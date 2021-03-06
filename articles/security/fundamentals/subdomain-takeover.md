---
title: Azure DNS エイリアス レコードと Azure App Service のカスタム ドメイン検証でサブドメインの乗っ取りを防ぐ
description: サブドメインの乗っ取りに関する重大度の高い一般的な脅威を回避する方法について説明します
services: security
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/23/2020
ms.author: memildin
ms.openlocfilehash: a7ff8a0cf23bf0701a7cc35cb137ec0965f295ec
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2020
ms.locfileid: "86223977"
---
# <a name="prevent-dangling-dns-entries-and-avoid-subdomain-takeover"></a>未解決の DNS エントリを防ぎ、サブドメインの乗っ取りを回避する

この記事では、サブドメインの乗っ取りに関する一般的なセキュリティの脅威と、その軽減手順について説明します。


## <a name="what-is-subdomain-takeover"></a>サブドメインの乗っ取りとは

サブドメインの乗っ取りは、多くのリソースを定期的に作成したり削除したりする組織にとって、重大度の高い一般的な脅威です。 サブドメインの乗っ取りは、プロビジョニング解除された Azure リソースを参照する DNS レコードがある場合に発生する可能性があります。 このような DNS レコードは、"未解決の DNS" エントリとも呼ばれます。 CNAME レコードは、この脅威に対して特に脆弱です。

サブドメインの乗っ取りの一般的なシナリオは次のとおりです。

1. Web サイトが作成されます。 

    この例では、 `app-contogreat-dev-001.azurewebsites.net`です。

1. この Web サイトを指す CNAME エントリが DNS に追加されます。 

    この例では、次のフレンドリ名が作成されました: `greatapp.contoso.com`。

1. 数か月後、サイトは不要になったため、対応する DNS エントリを削除**せずに** Web サイトが削除されました。 

    CNAME DNS エントリは、これで "未解決" になります。

1. サイトが削除されると、脅威アクターは即座に不足しているサイトを検出し、`app-contogreat-dev-001.azurewebsites.net` に独自の Web サイトを作成します。

    これで、`greatapp.contoso.com` に向けたトラフィックが脅威アクターの Azure サイトに送信され、脅威アクターが表示されるコンテンツを制御できるようになります。 

    未解決の DNS が悪用され、Contoso のサブドメイン "GreatApp" がサブドメインの乗っ取りの対象となりました。 

![プロビジョニング解除された Web サイトからのサブドメインの乗っ取り](./media/subdomain-takeover/subdomain-takeover.png)



## <a name="the-risks-of-subdomain-takeover"></a>サブドメインの乗っ取りのリスク

DNS レコードが使用できないリソースへポイントしている場合、レコード自体は DNS ゾーンから削除されている必要があります。 削除されていない場合、これは "未解決の DNS" レコードになり、サブドメインの乗っ取りが行われる可能性があります。

未解決の DNS エントリが存在していると、脅威アクターは、関連付けられている DNS 名を制御して、悪意のある Web サイトやサービスをホストできるようになります。 組織のサブドメインに悪意のあるページやサービスがあると、次のような結果になる可能性があります。

- **サブドメインのコンテンツに対する制御の喪失** - ブランドの毀損や信頼の損失だけでなく、組織のコンテンツをセキュリティで保護できないことに関する否定的な報道など。

- **疑いを持たないサイト閲覧者からの Cookie 収集** - Web アプリでは、セッション Cookie をサブドメイン (*.contoso.com) に公開するのが一般的です。そのため、どのサブドメインからでもアクセスできるようになっています。 脅威アクターは、サブドメインの乗っ取りを使用して、本物に似せた検索ページを作成し、疑いを持たないユーザーを騙してアクセスさせることで Cookie (セキュリティで保護されているものも含む) を収集することができます。 よくある誤解は、SSL 証明書を使用することで、サイトとユーザーの Cookie を乗っ取りから保護できるというものです。 しかし、脅威アクターはハイジャックしたサブドメインを使用して、有効な SSL 証明書を適用して受け取ることができます。 有効な SSL 証明書により、セキュリティで保護された Cookie へのアクセスが許可され、悪意のあるサイトの見かけの正当性をさらに向上させることになります。

- **フィッシング キャンペーン** - 本物に見せかけたサブドメインは、フィッシング キャンペーンで使用されることがあります。 これは、悪意のあるサイトや、脅威アクターが既知の安全なブランドの正当なサブドメイン宛ての電子メールを受信できるようにする MX レコードに対しても当てはまります。

- **さらなるリスク** - 悪意のあるサイトは、XSS、CSRF、CORS バイパスなどの他の古典的な攻撃にエスカレートさせるために使用できます。



## <a name="preventing-dangling-dns-entries"></a>未解決の DNS エントリの防止

未解決の DNS エントリと、その結果として生じるサブドメインの乗っ取りを防止するためのプロセスを組織で導入していることを確認することは、セキュリティ プログラムの重要な部分です。

現在使用できる予防策を以下に示します。


### <a name="use-azure-dns-alias-records"></a>Azure DNS エイリアス レコードの使用

Azure DNS の[エイリアス レコード](https://docs.microsoft.com/azure/dns/dns-alias#scenarios)では、DNS レコードと Azure リソースのライフサイクルを結合することで、未解決の参照が防止できるようになります。 たとえば、パブリック IP アドレスまたは Traffic Manager プロファイルをポイントするエイリアス レコードとして修飾されている DNS レコードについて考えます。 これらの基になるリソースを削除すると、DNS エイリアス レコードが空のレコード セットになります。 削除されたリソースは参照されなくなります。 エイリアス レコードを使用して保護できるものには制限があることに注意してください。 現在の、この一覧の制限は次のとおりです。

- Azure Front Door
- Traffic Manager プロファイル
- Azure Content Delivery Network (CDN) エンドポイント
- パブリック IP

現時点で提供されているサービス内容は限られていますが、サブドメインの乗っ取りを防ぐため、可能な限りエイリアス レコードを使用することをお勧めします。

Azure DNS のエイリアス レコードの機能に関する詳細については、[こちら](https://docs.microsoft.com/azure/dns/dns-alias#capabilities)を参照してください。



### <a name="use-azure-app-services-custom-domain-verification"></a>Azure App Service のカスタム ドメインの検証を使用する

Azure App Service の DNS エントリを作成する場合は、Domain Verification ID を持つ asuid.{subdomain} TXT レコードを作成します。 このような TXT レコードが存在する場合、他の Azure サブスクリプションによってカスタム ドメインの検証を行うこと、つまり乗っ取ることはできません。 

これらのレコードによって、他の誰かが CNAME エントリにある同じ名前の Azure App Service を作成することを阻止するわけではありません。 ドメイン名の所有権を証明することができない脅威アクターが、トラフィックを受信したりコンテンツを制御したりすることはできません。

既存のカスタム DNS 名を Azure App Service にマップする方法については、[こちら](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain)を参照してください。



### <a name="build-and-automate-processes-to-mitigate-the-threat"></a>脅威を軽減するためのプロセスを構築して自動化する

多くの場合、未解決の DNS による脅威を回避するためのクリーンアップ プロセスの実行は、開発者や運用チームの担当になっています。 以下のプラクティスは、組織がこの脅威による問題を回避するのに役立ちます。 

- **防止手順を作成する:**

    - リソースを削除するたびにアドレスを再ルーティングするようにアプリケーション開発者に指導します。

    - サービスを停止するときに、必要なチェック事項の一覧に "DNS エントリの削除" を含めます。

    - カスタム DNS エントリがあるすべてのリソースに対して[削除ロック](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources)をかけます。 削除ロックは、リソースがプロビジョニング解除される前にマッピングを削除する必要があることを示すインジケーターとして機能します。 このような対策は、社内の教育プログラムと組み合わせて初めて機能します。

- **検出手順を作成する:**

    - DNS レコードを定期的に確認して、サブドメインがすべて以下の Azure リソースにマップされていることを確認します。

        - 存在しているかどうか - DNS ゾーンに対して、*.azurewebsites.net や *.cloudapp.azure.com などの Azure サブドメインを指すリソースのクエリを実行します (詳しくは[このリファレンス リスト](azure-domains.md)を参照してください)。
        - 所有者が自分であるかどうか - DNS サブドメインが対象としているすべてのリソースを所有していることを確認します。

    - Azure の完全修飾ドメイン名 (FQDN) エンドポイントとアプリケーション所有者のサービス カタログを維持します。 サービス カタログを作成するには、次の表のパラメーターを使用して、次の Azure Resource Graph (ARG) クエリを実行します。
    
        >[!IMPORTANT]
        > **アクセス許可** - すべての Azure サブスクリプションへのアクセス権を持つユーザーとしてクエリを実行します。 
        >
        > **制限** - Azure Resource Graph には、大規模な Azure 環境を使用している場合に考慮する必要がある調整およびページングの制限があります。 大規模な Azure リソース データセットを処理する方法について[参照してください](https://docs.microsoft.com/azure/governance/resource-graph/concepts/work-with-data)。  

        ```powershell
        Search-AzGraph -Query "resources | where type == '<ResourceType>' | 
        project tenantId, subscriptionId, type, resourceGroup, name, 
        endpoint = <FQDNproperty>"
        ``` 

        ARG クエリのサービスごとのパラメーター:

        |リソース名  | `<ResourceType>`  | `<FQDNproperty>`  |
        |---------|---------|---------|
        |Azure Front Door|microsoft.network/frontdoors|properties.cName|
        |Azure Blob Storage|microsoft.storage/storageaccounts|properties.primaryEndpoints.blob|
        |Azure CDN|microsoft.cdn/profiles/endpoints|properties.hostName|
        |パブリック IP アドレス|microsoft.network/publicipaddresses|properties.dnsSettings.fqdn|
        |Azure の Traffic Manager|microsoft.network/trafficmanagerprofiles|properties.dnsConfig.fqdn|
        |Azure Container Instances|microsoft.containerinstance/containergroups|properties.ipAddress.fqdn|
        |Azure API Management|microsoft.apimanagement/service|properties.hostnameConfigurations.hostName|
        |Azure App Service|microsoft.web/sites|properties.defaultHostName|
        |Azure App Service - Slots|microsoft.web/sites/slots|properties.defaultHostName|

        
        **例 1** - このクエリでは Azure App Service からリソースが返されます。 

        ```powershell
        Search-AzGraph -Query "resources | where type == 'microsoft.web/sites' | 
        project tenantId, subscriptionId, type, resourceGroup, name, 
        endpoint = properties.defaultHostName"
        ```
        
        **例 2** - このクエリでは、Azure App Service **および** Azure App Service - Slot からリソースを返すために、複数のリソースの種類が結合されます。

        ```powershell
        Search-AzGraph -Query "resources | where type in ('microsoft.web/sites', 
        'microsoft.web/sites/slots') | project tenantId, subscriptionId, type, 
        resourceGroup, name, endpoint = properties.defaultHostName"
        ```


- **修復手順を作成する:**
    - 未解決の DNS エントリが見つかった場合、チームは侵害が発生したかどうかを調査する必要があります。
    - リソースが使用停止になったときにアドレスが再ルーティングされなかった理由を調査します。
    - 使用されなくなった DNS レコードは削除するか、組織が所有する正しい Azure リソース (FQDN) へポイントされるようにします。
 

## <a name="next-steps"></a>次のステップ

サブドメインの乗っ取りを防ぐために使用できる関連のサービスと Azure の機能の詳細については、以下のページを参照してください。

- [カスタム ドメインのエイリアス レコードを使用した Azure DNS のサポート](https://docs.microsoft.com/azure/dns/dns-alias#prevent-dangling-dns-records)

- [Azure App Service でカスタム ドメインを追加するときにドメイン検証 ID を使用する](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain#get-domain-verification-id) 

- [クイック スタート: Azure PowerShell を使用して最初の Resource Graph クエリを実行します](https://docs.microsoft.com/azure/governance/resource-graph/first-query-powershell)
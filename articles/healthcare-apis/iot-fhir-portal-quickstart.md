---
title: クイック スタート:Azure portal を使用して IoT コネクタ (プレビュー) をデプロイする
description: このクイックスタートでは、Azure portal を使用して、Azure API for FHIR の IoT コネクタ機能をデプロイ、構成、使用する方法を説明します。
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: quickstart
ms.date: 05/11/2020
ms.author: punagpal
ms.openlocfilehash: 95f5b5f13401c224ccf67c5f013deedf00379de7
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2020
ms.locfileid: "87446806"
---
# <a name="quickstart-deploy-iot-connector-preview-using-azure-portal"></a>クイック スタート:Azure portal を使用して IoT コネクタ (プレビュー) をデプロイする

IoT コネクタは、Azure API for FHIR のオプション機能の 1 つであり、Internet of Medical Things (IoMT) デバイスからデータを取り込むことができます。 さらに、プレビュー段階では、IoT コネクタ機能を無料で利用できます。 このクイック スタートでは、次の方法について説明します。
- Azure portal を使用して IoT コネクタをデプロイし、構成する
- シミュレートされたデバイスを使用してデータを IoT コネクタに送信する
- Azure API for FHIR で IoT コネクタによって作成されたリソースを表示する

## <a name="prerequisites"></a>前提条件

- アクティブな Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure API for FHIR リソース - [Azure portal を使用して Azure API for FHIR をデプロイする](fhir-paas-portal-quickstart.md)

## <a name="go-to-azure-api-for-fhir-resource"></a>Azure API for FHIR リソースにアクセスする

[Azure portal](https://portal.azure.com) を開いて、IoT コネクタ機能を作成する **Azure API for FHIR** リソースにアクセスします。

[![Azure API for FHIR リソース](media/quickstart-iot-fhir-portal/portal-azure-api-fhir.jpg)](media/quickstart-iot-fhir-portal/portal-azure-api-fhir.jpg#lightbox)

左側のナビゲーション メニューの **[アドイン]** セクションで、 **[IoT コネクタ (プレビュー)]** をクリックして、 **[IoT コネクタ]** ページを開きます。

[![IoT コネクタ機能](media/quickstart-iot-fhir-portal/portal-iot-connectors.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connectors.jpg#lightbox)

## <a name="create-new-iot-connector-preview"></a>新しい IoT コネクタ (プレビュー) を作成する

**[追加]** ボタンをクリックして、 **[Create IoT Connector]\(IoT コネクタの作成\)** ページを開きます。

[![IoT コネクタを追加する](media/quickstart-iot-fhir-portal/portal-iot-connectors-add.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connectors-add.jpg#lightbox)

新しい IoT コネクタの設定を入力します。 **[作成]** ボタンをクリックして、IoT コネクタのデプロイを待ちます。

> [!NOTE]
> このインストールの **[解決の種類]** の値として **[作成]** ドロップ ダウンを選択する必要があります。 

[![IoT コネクタを作成する](media/quickstart-iot-fhir-portal/portal-iot-connector-create.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-create.jpg#lightbox)

|設定|値|説明 |
|---|---|---|
|コネクタ名|一意の名前|IoT コネクタを識別する名前を入力します。 この名前は、Azure API for FHIR リソース内で一意である必要があります。 名前に含めることができるのは、英小文字、数字、ハイフン (-) のみです。 名前の先頭と末尾は文字または数字である必要があります。また、長さは 3 から 24 文字までにする必要があります。|
|解決の種類|[Lookup]\(検索\) または [作成]|Azure API for FHIR 内に [Device](https://www.hl7.org/fhir/device.html) および [Patient](https://www.hl7.org/fhir/patient.html) FHIR リソースを作成する帯域外プロセスがある場合、 **[Lookup]\(検索\)** を選択します。 IoT コネクタでは、デバイス データを表す [Observation](https://www.hl7.org/fhir/observation.html) FHIR リソースを作成する場合に、これらのリソースへの参照を使用します。 IoT コネクタにより、デバイス データ内にあるそれぞれの識別子の値を使用して、Azure API for FHIR 内に最小限の Device および Patient リソースを作成する場合は、 **[Create]\(作成\)** を選択します。|

インストールが完了すると、新しく作成された IoT コネクタが、 **[IoT コネクタ]** ページに表示されます。

[![作成された IoT コネクタ](media/quickstart-iot-fhir-portal/portal-iot-connector-created.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-created.jpg#lightbox)

## <a name="configure-iot-connector-preview"></a>IoT コネクタ (プレビュー) を構成する

IoT コネクタには、デバイス メッセージを FHIR ベースの Observation リソースに変換する 2 つのマッピング テンプレート (**デバイス マッピング** および **FHIR マッピング**) が必要です。 これらのマッピングがアップロードされるまで、IoT コネクタは完全に動作しません。

[![マッピングが見つからない IoT コネクタ](media/quickstart-iot-fhir-portal/portal-iot-connector-missing-mappings.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-missing-mappings.jpg#lightbox)

マッピング テンプレートをアップロードするには、新しくデプロイされた IoT コネクタをクリックして、 **[IoT コネクタ]** ページに移動します。

[![IoT コネクタをクリックする](media/quickstart-iot-fhir-portal/portal-iot-connector-click.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click.jpg#lightbox)

#### <a name="device-mapping"></a>デバイス マッピング

デバイス マッピング テンプレートでは、デバイス データを正規化スキーマに変換します。 **[IoT コネクタ]** ページで、 **[Configure device mapping]\(デバイス マッピングの構成\)** ボタンをクリックして、 **[デバイス マッピング]** ページに移動します。 

[![IoT コネクタで [Configure device mapping]\(デバイス マッピングの構成\) をクリックする](media/quickstart-iot-fhir-portal/portal-iot-connector-click-device-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click-device-mapping.jpg#lightbox)

**[デバイス マッピング]** ページで、次のスクリプトを JSON エディターに追加して、 **[保存]** をクリックします。

```json
{
  "templateType": "CollectionContent",
  "template": [
    {
      "templateType": "IotJsonPathContent",
      "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@Body.HeartRate)]",
        "patientIdExpression": "$.SystemProperties.iothub-connection-device-id",
        "values": [
          {
            "required": "true",
            "valueExpression": "$.Body.HeartRate",
            "valueName": "hr"
          }
        ]
      }
    }
  ]
}
```

[![IoT コネクタのデバイス マッピング](media/quickstart-iot-fhir-portal/portal-iot-device-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-device-mapping.jpg#lightbox)


#### <a name="fhir-mapping"></a>FHIR マッピング

FHIR マッピング テンプレートでは、正規化されたメッセージを FHIR ベースの Observation リソースに変換します。 **[IoT コネクタ]** ページで、 **[Configure FHIR mapping]\(FHIR マッピングの構成\)** ボタンをクリックして、 **[FHIR マッピング]** ページに移動します。  

[![IoT コネクタで [Configure FHIR mapping]\(FHIR マッピングの構成\) をクリックする](media/quickstart-iot-fhir-portal/portal-iot-connector-click-fhir-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click-fhir-mapping.jpg#lightbox)

**[FHIR マッピング]** ページで、次のスクリプトを JSON エディターに追加して、 **[保存]** をクリックします。

```json
{
  "templateType": "CollectionFhir",
  "template": [
    {
      "templateType": "CodeValueFhir",
      "template": {
        "codes": [
          {
            "code": "8867-4",
            "system": "http://loinc.org",
            "display": "Heart rate"
          }
        ],
        "periodInterval": 0,
        "typeName": "heartrate",
        "value": {
          "unit": "count/min",
          "valueName": "hr",
          "valueType": "Quantity"
        }
      }
    }
  ]
}
```

[![IoT コネクタの FHIR マッピング](media/quickstart-iot-fhir-portal/portal-iot-fhir-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-fhir-mapping.jpg#lightbox)

## <a name="generate-a-connection-string"></a>接続文字列を生成する

IoMT デバイスでは、メッセージを IoT コネクタに接続してメッセージを送信するための接続文字列が必要です。 新しくデプロイされた IoT コネクタの **[IoT コネクタ]** ページで、 **[Manage client connections]\(クライアント接続の管理\)** ボタンを選択します。 

[![IoT コネクタで [Manage client connections]\(クライアント接続の管理\) をクリックする](media/quickstart-iot-fhir-portal/portal-iot-connector-click-client-connections.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click-client-connections.jpg#lightbox)

**[接続]** ページで、 **[追加]** ボタンをクリックして、新しい接続を作成します。 

[![IoT コネクタの接続](media/quickstart-iot-fhir-portal/portal-iot-connections.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connections.jpg#lightbox)

オーバーレイ ウィンドウで、この接続の表示名を指定し、 **[作成]** ボタンを選択します。

[![IoT コネクタの新しい接続](media/quickstart-iot-fhir-portal/portal-iot-new-connection.jpg)](media/quickstart-iot-fhir-portal/portal-iot-new-connection.jpg#lightbox)

**[接続]** ページから新しく作成された接続を選択し、右側にあるオーバーレイ ウィンドウの **[プライマリ接続文字列]** フィールドの値をコピーします。

[![IoT コネクタの接続文字列](media/quickstart-iot-fhir-portal/portal-iot-connection-string.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connection-string.jpg#lightbox)

この接続文字列は、後の手順で使用できるように保存しておきます。 

## <a name="connect-your-devices-to-iot"></a>デバイスを IoT に接続する

Azure では、IoT デバイスを接続し、管理するための幅広い IoT 製品スイートを提供しています。 Azure IoT Hub を使用して PaaS に基づいた独自のソリューションを作成することも、Azure IoT Central を使用して IoT アプリ プラットフォームの管理から始めることもできます。 このチュートリアルでは、Azure IoT Central を活用します。これには、開始するのに役立つ、各業界にフォーカスしたソリューション テンプレートが用意されています。

[患者の継続的なモニタリング アプリ テンプレート](https://docs.microsoft.com/azure/iot-central/healthcare/tutorial-continuous-patient-monitoring#create-an-application-template)をデプロイします。 このテンプレートには、開始するのに役立つリアルタイム データを生成する 2 つのシミュレートされたデバイス (**Smart Vitals Patch** と **Smart Knee Brace**) が含まれています。

> [!NOTE]
> 実際のデバイスの準備ができたらいつでも、同じ IoT Central アプリケーションを使用して、[ご利用のデバイスをオンボード](https://docs.microsoft.com/azure/iot-central/core/howto-set-up-template)し、デバイス シミュレーターを交換することができます。 デバイス データの FHIR への送信も自動的に開始されます。 

## <a name="connect-your-iot-data-with-the-iot-connector-preview"></a>IoT コネクタ (プレビュー) を使用して IoT データを接続する
IoT Central アプリケーションをデプロイすると、すぐに使用できる 2 つのシミュレートされたデバイスでテレメトリの生成が開始されます。 このチュートリアルでは、IoT コネクタを介して、テレメトリを *Smart Vitals Patch* シミュレーターから FHIR に取り込みます。 IoT データを IoT コネクタにエクスポートするには、[IoT Central 内で継続的なデータ エクスポートを設定する](https://docs.microsoft.com/azure/iot-central/core/howto-export-data#set-up-data-export)必要があります。 [継続的データ エクスポート] ページで、次の設定を行います。
- エクスポート先として *[Azure Event Hubs]* を選択します。
- **[Event Hubs 名前空間]** フィールドでは、値 *[接続文字列を使用します]* を選択します。
- **[接続文字列]** フィールドでは、前の手順で取得した IoT コネクタの接続文字列を指定します。
- **[エクスポートするデータ]** フィールドでは、 **[テレメトリ]** オプションを *[オン]* のままにしておきます。

## <a name="view-device-data-in-azure-api-for-fhir"></a>Azure API for FHIR でデバイス データを表示する

IoT コネクタによって作成された FHIR ベースの Observation リソースを、Postman を使用して Azure API for FHIR で表示できます。 [Azure API for FHIR にアクセスするように Postman](access-fhir-postman-tutorial.md) を設定し、心拍の値を含む Observation FHIR リソースを表示するように `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` に対して `GET` 要求を行います。 

> [!TIP]
> Azure API for FHIR データ プレーンへの適切なアクセス権をユーザーに確実に付与します。 [Azure のロールベースのアクセス制御 (Azure RBAC)](configure-azure-rbac.md) を使用して、必要なデータ プレーン ロールを割り当てます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

IoT コネクタのインスタンスが必要なくなった場合、それを削除するには、関連付けられたリソース グループ、関連付けられた Azure API for FHIR サービス、または IoT コネクタ インスタンス自体を削除します。 

IoT コネクタ インスタンスを直接削除するには、 **[IoT コネクタ]** ページからインスタンスを選択して、 **[IoT コネクタ]** ページに移動し、 **[削除]** ボタンをクリックします。 確認を求められたら、 **[はい]** を選択します。 

[![IoT コネクタ インスタンスを削除する](media/quickstart-iot-fhir-portal/portal-iot-connector-delete.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-delete.jpg#lightbox)

## <a name="next-steps"></a>次のステップ

このクイックスタート ガイドでは、Azure API for FHIR リソースに IoT コネクタ機能をデプロイしました。 以下に示す、次のステップの中からいずれかを選択して、IoT コネクタの詳細を確認してください。

IoT コネクタ内のさまざまなデータ フロー ステージについて説明します。

>[!div class="nextstepaction"]
>[IoT コネクタのデータ フロー](iot-data-flow.md)

デバイスと FHIR マッピング テンプレートを使用して IoT コネクタを構成する方法について説明します。

>[!div class="nextstepaction"]
>[IoT コネクタのマッピング テンプレート](iot-mapping-templates.md)

FHIR は HL7 の登録商標であり、HL7 の許可を得て使用しています。

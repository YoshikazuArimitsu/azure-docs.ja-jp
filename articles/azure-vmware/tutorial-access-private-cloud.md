---
title: チュートリアル - プライベート クラウドにアクセスする方法を学習する
description: Azure VMware Solution (AVS) プライベート クラウドにアクセスする方法を学習する
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: 32dc5c173789996d2559eda1153b1509e10e5984
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86497966"
---
# <a name="tutorial-learn-how-to-access-an-azure-vmware-solution-avs-private-cloud"></a>チュートリアル:Azure VMware Solution (AVS) プライベート クラウドにアクセスする方法を学習する

プレビュー期間中、AVS では、お使いのオンプレミス vCenter でプライベート クラウドを管理することはできません。 ジャンプ ボックスを使用して、ローカル vCenter インスタンスへの追加の設定と接続を実行する必要があります。 

このチュートリアルでは、前のチュートリアル「[チュートリアル: Azure で VMware プライベート クラウド用のネットワークを構成する](tutorial-configure-networking.md)」で作成したリソース グループにジャンプ ボックス用の Windows 仮想マシンを作成し、vCenter にサインインします。 これは、前に作成した同じ仮想ネットワーク上の VM であり、vCenter および NSX Manager へのアクセスを提供します。 

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * vCenter への接続に使用する Windows 仮想マシンを作成する
> * 仮想マシンから vCenter にログインする

## <a name="create-a-new-windows-virtual-machine"></a>新しい Windows 仮想マシンを作成する

リソース グループで、 **[+ 追加]** を選択し、**Microsoft Windows 10** を検索して選択して、 **[作成]** をクリックします。

:::image type="content" source="./media/tutorial-access-private-cloud/ss8-azure-w10vm-create.png" alt-text="ジャンプボックス用に新しい Windows 10 VM を追加する" border="true":::

フィールドに必要な情報を入力し、 **[確認および作成]** を選択します。 フィールドの詳細については、次の表を参照してください。

| フィールド | 値 |
| --- | --- |
| **サブスクリプション** | この値には、リソース グループが属しているサブスクリプションが既に設定されています。 |
| **リソース グループ** | この値は、現在のリソース グループに対して既に設定されています。 これは、前のチュートリアルで作成したリソース グループである必要があります。 |
| **仮想マシン名** | VM の一意の名前を入力します。 |
| **リージョン** | VM の地理的な場所を選択します。 |
| **可用性オプション** | 既定値を選択したままにします。 |
| **Image** | VM イメージを選択します。 |
| **[サイズ]** | 既定のサイズ値をそのまま使用します。 |
| **認証の種類**  | **[パスワード]** を選択します。 |
| **ユーザー名** | VM にログオンするためのユーザー名を入力します。 |
| **パスワード** | VM にログオンするためのパスワードを入力します。 |
| **[パスワードの確認入力]** | VM にログオンするためのパスワードを入力します。 |
| **パブリック インバウンド ポート** | **[なし]** を選択します。 [なし] を選択した場合は、[JIT アクセス](../security-center/security-center-just-in-time.md#jit-configure)を使用して、VM にアクセスする必要があるときにのみ VM へのアクセスを制御できます。  |

適切な情報を入力したら、 **[確認および作成]** をクリックします。 検証に合格したら、 **[作成]** を選択して仮想マシンの作成プロセスを開始します。

:::image type="content" source="./media/tutorial-access-private-cloud/ss11-review-create-wjb01.png" alt-text="ジャンプボックス用に新しい Windows 10 VM を作成する" border="true":::

## <a name="connect-to-the-local-vcenter-of-your-private-cloud"></a>プライベート クラウドのローカル vCenter に接続する

ジャンプ ボックスから、VMware vCenter SSO を使用して vSphere クライアントにサインインします。 クラウド管理者のユーザー名を使用して vSphere クライアントにサインインします。潜在的なセキュリティ リスクに関する警告が表示されたら、セキュリティ リスクを受け入れて続行します。シングル サインオン資格情報を使用して VMware vCenter にサインインし、ユーザー インターフェイスが正常に表示されることを確認します。

Azure portal で、お使いのプライベート クラウドを選択し、 **[概要]** ビューで **[ID]> [既定値]** を選択します。 プライベート クラウドの vCenter および NSX-T Manager の URL とログイン資格情報が表示されます。

:::image type="content" source="./media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="プライベート クラウドの vCenter および NSX Manager の URL と資格情報を表示する" border="true":::

前の手順で作成した仮想マシンに移動し、仮想マシンに接続します。 仮想マシンに接続する方法の詳細な手順については、「[仮想マシンへの接続](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine)」を参照してください

Windows VM でブラウザーを開き、2 つのタブを使用して vCenter と NSX-T Manger の URL に移動します。 vCenter タブで、前の手順の `cloudadmin@vmcp.local` ユーザー資格情報を入力します。

:::image type="content" source="./media/tutorial-access-private-cloud/ss5-vcenter-login.png" alt-text="プライベート クラウドの vCenter にサインインする" border="true":::

:::image type="content" source="./media/tutorial-access-private-cloud/ss6-vsphere-client-home.png" alt-text="vCenter ポータル" border="true":::

ブラウザーの 2 番目のタブで、NSX-T Manager にサインインします。

:::image type="content" source="./media/tutorial-access-private-cloud/ss10-nsx-manager-home.png" alt-text="ローカルのプライベート クラウドの NSX Manger のホーム" border="true":::

## <a name="next-steps"></a>次のステップ

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * vCenter への接続に使用する Windows 仮想マシンを作成する
> * 仮想マシンから vCenter にログインする

次のチュートリアルに進み、AVS プライベート クラウドをスケーリングする方法を学習してください。

> [!div class="nextstepaction"]
> [AVS プライベート クラウドをスケーリングする](tutorial-scale-private-cloud.md)

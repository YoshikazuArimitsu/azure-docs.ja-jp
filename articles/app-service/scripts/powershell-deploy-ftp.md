---
title: PowerShell:FTP を使用してファイルをアップロードする
description: Azure PowerShell を使用して App Service のデプロイと管理を自動化する方法について説明します。 このサンプルでは、FTP を使用してアプリにファイルをアップロードする方法を紹介しています。
tags: azure-service-management
ms.assetid: b7d46d6f-44fd-454c-8008-87dab6eefbc1
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc
ms.openlocfilehash: fd01e42570a10b0b9f9be182690dbd1daca5b896
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87083261"
---
# <a name="upload-files-to-a-web-app-using-ftp"></a>FTP を使用して Web アプリにファイルをアップロードする

このサンプル スクリプトでは、App Service で Web アプリを関連リソースと合わせて作成し、FTP ([WebClient.UploadFile()](https://msdn.microsoft.com/library/ms144229.aspx) 経由) を使用して Web アプリのコードをデプロイします。

必要に応じて、[Azure PowerShell ガイド](/powershell/azure/)の手順に従って Azure PowerShell をインストールし、`Connect-AzAccount` を実行して、Azure との接続を作成します。

## <a name="sample-script"></a>サンプル スクリプト

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/deploy-ftp/deploy-ftp.ps1?highlight=1 "Upload files to a web app using FTP")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

サンプル スクリプトの実行後、次のコマンドを使用すると、リソース グループ、Web アプリ、およびすべての関連リソースを削除できます。

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | Notes |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | すべてのリソースを格納するリソース グループを作成します。 |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | App Service プランを作成します。 |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Web アプリを作成します。 |
| [Get-AzWebAppPublishingProfile](/powershell/module/az.websites/get-azwebapppublishingprofile) | Web アプリの発行プロファイルを取得します。 |

## <a name="next-steps"></a>次のステップ

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/)を参照してください。

その他の Azure App Service Web Apps 用 Azure PowerShell サンプル スクリプトは、[Azure PowerShell サンプル](../samples-powershell.md)のページにあります。

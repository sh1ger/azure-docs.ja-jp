---
title: クイック スタート:ロード バランサーを作成する - Azure テンプレート
titleSuffix: Azure Load Balancer
description: このクイックスタートでは、Azure Resource Manager テンプレートを使用してロード バランサーを作成する方法について説明します。
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create a load balancer by using an Azure Resource Manager template so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2020
ms.author: allensu
ms.custom: mvc,subject-armqs
ms.openlocfilehash: ebf2f926f5be86ffee5f3a3e30277962a6060762
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2020
ms.locfileid: "85479762"
---
# <a name="quickstart-create-a-load-balancer-to-load-balance-vms-by-using-an-arm-template"></a>クイック スタート:ARM テンプレートを使用して VM の負荷を分散するロード バランサーを作成する

負荷分散では、着信要求を複数の仮想マシン (VM) に分散させることで、より高いレベルの可用性とスケールを実現します。 このクイックスタートでは、VM を負荷分散する Standard ロード バランサーを作成する Azure Resource Manager テンプレート (ARM テンプレート) を展開する方法を説明します。 ARM テンプレートを使用すると、他のデプロイ方法より手順が減ります。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-load-balancer-standard-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="review-the-template"></a>テンプレートを確認する

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/101-load-balancer-standard-create/)からのものです。

ロード バランサーとパブリック IP の SKU は一致している必要があります。 Standard Load Balancer を作成するときに、Standard Load Balancer のフロントエンドとして構成されている新しい Standard パブリック IP アドレスも作成する必要があります。 Basic Load Balancer を作成したい場合は、[こちらのテンプレート](https://azure.microsoft.com/resources/templates/201-2-vms-loadbalancer-natrules/)を使用してください。 運用環境のワークロードには Standard SKU の使用をお勧めします。

:::code language="json" source="~/quickstart-templates/101-load-balancer-standard-create/azuredeploy.json" range="1-324" highlight="57-122":::

テンプレートでは、複数の Azure リソースが定義されています。

- [**Microsoft.Network/loadBalancers**](/azure/templates/microsoft.network/loadbalancers)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses): ロード バランサー用。3 つの仮想マシンそれぞれに使用されます。
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virutalMachines**](/azure/templates/microsoft.compute/virtualmachines) (そのうちの 3 つ)。
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) (そのうちの 3 つ)。
- [**Microsoft.Compute/virtualMachine/extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions) (そのうちの 3 つ): IIS と Web ページの構成に使用。

Azure Load Balancer に関連するテンプレートをさらに探すには、「[Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular)」を参照してください。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

1. 次のコード ブロックの **[試してみる]** を選択して Azure Cloud Shell を開き、指示に従って Azure にサインインします。

   ```azurepowershell-interactive
   $projectName = Read-Host -Prompt "Enter a project name with 12 or less letters or numbers that is used to generate Azure resource names"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
   $adminUserName = Read-Host -Prompt "Enter the virtual machine administrator account name"
   $adminPassword = Read-Host -Prompt "Enter the virtual machine administrator password" -AsSecureString

   $resourceGroupName = "${projectName}rg"
   $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-load-balancer-standard-create/azuredeploy.json"

   New-AzResourceGroup -Name $resourceGroupName -Location $location
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -location $location -adminUsername $adminUsername -adminPassword $adminPassword

   Write-Host "Press [ENTER] to continue."
   ```

   コンソールからプロンプトが表示されるまで待ちます。

1. 前のコード ブロックから **[コピー]** を選択して、PowerShell スクリプトをコピーします。

1. シェル コンソール ウィンドウを右クリックし、 **[貼り付け]** を選択します。

1. 値を入力します。

   テンプレートのデプロイによって、3 つの可用性ゾーンが作成されます。 可用性ゾーンは[特定のリージョン](../availability-zones/az-overview.md)でのみサポートされています。 サポートされているリージョンのいずれかを使用します。 よくわからない場合は、「**centralus**」と入力します。

   リソース グループの名前は、**rg** が付加されたプロジェクト名です。 次のセクションでリソース グループ名が必要になります。

テンプレートのデプロイには約 10 分かかります。 完了すると、次のように出力されます。

![Azure Standard ロード バランサーの Resource Manager テンプレートによる PowerShell でのデプロイの出力](./media/quickstart-load-balancer-standard-public-template/azure-standard-load-balancer-resource-manager-template-powershell-output.png)

テンプレートをデプロイするには Azure PowerShell を使用します。 Azure PowerShell だけでなく、Azure portal、Azure CLI、および REST API を使用することもできます。 他のデプロイ方法については、「[テンプレートのデプロイ](../azure-resource-manager/templates/deploy-portal.md)」を参照してください。

## <a name="review-deployed-resources"></a>デプロイされているリソースを確認する

1. [Azure portal](https://portal.azure.com) にサインインします。

1. 左側のウィンドウから **[リソース グループ]** を選択します。

1. 前のセクションで作成したリソース グループを選択します 既定のリソース グループ名は、プロジェクト名に **rg** が追加されたものです。

1. ロード バランサーを選択します。 既定の名前は、プロジェクト名に **-lb** が追加されたものです。

1. パブリック IP アドレスの IP アドレス部分のみをコピーして、ブラウザーのアドレス バーに貼り付けます。

   ![Azure Standard ロード バランサーの Resource Manager テンプレートのパブリック IP](./media/quickstart-load-balancer-standard-public-template/azure-standard-load-balancer-resource-manager-template-deployment-public-ip.png)

    ブラウザーにはインターネット インフォメーション サービス (IIS) Web サーバーの既定ページが表示されます。

   ![IIS Web サーバー](./media/quickstart-load-balancer-standard-public-template/load-balancer-test-web-page.png)

ロード バランサーが 3 つの VM すべての間でトラフィックを分散していることを確認するには、クライアント マシンから Web ブラウザーを強制的に最新の情報に更新します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったら、リソース グループ、ロード バランサー、およびすべての関連リソースを削除します。 これを行うには、Azure portal に移動し、ロード バランサーを含むリソース グループを選択し、 **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Standard Load Balancer を作成し、それに VM をアタッチして、ロード バランサー トラフィック規則と正常性プローブを構成してから、ロード バランサーをテストしました。

さらに学習するには、Load Balancer に関するチュートリアルに進みます。

> [!div class="nextstepaction"]
> [Azure Load Balancer のチュートリアル](tutorial-load-balancer-standard-public-zone-redundant-portal.md)

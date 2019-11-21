---
title: Create an Azure Data Lake Storage Gen2 storage account | Microsoft Docs
description: Quickly learn to create a new storage account with access to Data Lake Storage Gen2 using the Azure portal, Azure PowerShell, or the Azure CLI.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 57350bd00a33c36b5aef3a0ccd3034b4db3d2c55
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227953"
---
# <a name="create-an-azure-data-lake-storage-gen2-storage-account"></a>Create an Azure Data Lake Storage Gen2 storage account

Azure Data Lake Storage Gen2 [supports a hierarchical namespace](data-lake-storage-introduction.md) which provides a native directory-based container tailored to work with the Hadoop Distributed File System (HDFS). A 2. generációs Data Lake Storage-adatok a HDFS-ből az [ABFS illesztőprogramon](data-lake-storage-abfs-driver.md) keresztül érhetők el.

This article demonstrates how to create an account using the Azure portal, Azure PowerShell, or via the Azure CLI.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt. 

|           | Előfeltétel |
|-----------|--------------|
|Portál     | None         |
|PowerShell | This article requires the PowerShell module Az.Storage version **0.7** or later. To find your current version, run the `Get-Module -ListAvailable Az.Storage` command. If after you run this command, no results appear, or if a version lower than **0.7** appears, then you'll have to upgrade your powershell module. See the [Upgrade your powershell module](#upgrade-your-powershell-module) section of this guide.
|CLI        | You can sign in to Azure and run Azure CLI commands in one of two ways: <ul><li>A CLI-parancsok az Azure Portalról, az Azure Cloud Shell felületén futtathatók </li><li>Telepítheti a parancssori felületet, így helyben is futtathatja a CLI-parancsokat</li></ul>|

Ha a parancssori felületet használja, futtathatja az Azure Cloud Shellt vagy telepítheti a parancssori felületet helyileg.

### <a name="use-azure-cloud-shell"></a>Az Azure Cloud Shell használata

Az Azure Cloud Shell olyan ingyenes Bash-felület, amelyet közvetlenül futtathat az Azure Portalon. A fiókjával való használat érdekében az Azure CLI már előre telepítve és konfigurálva van rajta. Az Azure Portal jobb felső sarkában található menüben kattintson a **Cloud Shell** gombra:

[![Cloud Shell](./media/data-lake-storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

The button launches an interactive shell that you can use to run the steps in this article:

[![Képernyőkép a Portalon lévő Cloud Shell-ablakról](./media/data-lake-storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>A parancssori felület helyi telepítése

Az Azure CLI-t helyben is telepítheti és használhatja. This article requires that you are running the Azure CLI version 2.0.38 or later. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

## <a name="create-a-storage-account-with-azure-data-lake-storage-gen2-enabled"></a>Azure Data Lake Storage Gen2-kompatibilis tárfiók létrehozása

An Azure storage account contains all of your Azure Storage data objects: blobs, files, queues, tables, and disks. The storage account provides a unique namespace for your Azure Storage data that is accessible from anywhere in the world over HTTP or HTTPS. Data in your Azure storage account is durable and highly available, secure, and massively scalable.

> [!NOTE]
> A 2. generációs Data Lake Store funkcióinak használatához az új tárfiókokat **StorageV2 (általános célú V2)** típusúként kell létrehoznia.  

További információ a tárfiókokról: [Az Azure Storage-fiókok áttekintése](../common/storage-account-overview.md).

## <a name="create-an-account-using-the-azure-portal"></a>Fiókok létrehozása az Azure Portalon

Jelentkezzen be az [Azure portálra](https://portal.azure.com).

### <a name="create-a-storage-account"></a>Create a storage account

Minden tárfióknak egy Azure-erőforráscsoporthoz kell tartoznia. Az erőforráscsoport egy logikai tároló az Azure-szolgáltatások csoportosításához. A tárfiók létrehozásakor lehetősége van létrehozni egy új erőforráscsoportot, vagy választhat egy meglévő erőforráscsoportot. This article shows how to create a new resource group.

Kövesse az alábbi lépéseket egy általános célú v2-tárfiók létrehozásához az Azure Portalon:

> [!NOTE]
> A hierarchikus névtér jelenleg minden nyilvános régióban elérhető.

1. Válassza ki azt az előfizetést, amelyikben létre kívánja hozni a tárfiókot.
2. In the Azure portal, choose the **Create a resource** button, then choose **Storage account**.
3. Az **Erőforráscsoport** mező alatt válassza az **Új létrehozása** elemet. Enter a name for your new resource group.
   
   Az erőforráscsoport egy logikai tároló az Azure-szolgáltatások csoportosításához. A tárfiók létrehozásakor lehetősége van létrehozni egy új erőforráscsoportot, vagy választhat egy meglévő erőforráscsoportot.

4. Ezután adja meg a tárfiók nevét. A választott névnek az Azure-on belül egyedinek kell lennie. A név 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat.
5. Válassza ki a helyet.
6. Make sure that **StorageV2 (general purpose v2)** appears as selected in the **Account kind** drop-down list.
7. Optionally change the values in each of these fields: **Performance**, **Replication**, **Access tier**. To learn more about these options, see [Introduction to Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction#azure-storage-services).
8. Choose the **Advanced** tab.
10. In the **Data Lake Storage Gen2** section set **Hierarchical namespace** to **Enabled**.
11. Click **Review + Create** to create the storage account.

Ezzel a tárfiók létrejött a portálon.

### <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Erőforráscsoport eltávolítása az Azure Portallal:

1. Az Azure Portalon bontsa ki a bal oldalon a szolgáltatásmenüt, és válassza az **Erőforráscsoportok** lehetőséget az erőforráscsoportok listájának megjelenítéséhez.
2. Keresse meg a törölni kívánt erőforráscsoportot, és kattintson a jobb gombbal a lista jobb oldalán lévő **Továbbiak** gombra ( **...** ).
3. Válassza az **Erőforráscsoport törlése** elemet, és erősítse meg a választását.

## <a name="create-an-account-using-powershell"></a>Fiók létrehozása a PowerShell használatával

First, install the latest version of the [PowerShellGet](/powershell/scripting/gallery/installing-psget) module.

Then, upgrade your powershell module, sign in to your Azure subscription, create a resource group, and then create a storage account.

### <a name="upgrade-your-powershell-module"></a>A PowerShell-modul frissítése

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

To interact with Data Lake Storage Gen2 by using PowerShell, you'll need to install module Az.Storage version **0.7** or later.

Start by opening a PowerShell session with elevated permissions.

Install the Az.Storage module

```powershell
Install-Module Az.Storage -Repository PSGallery -AllowClobber -Force
```

### <a name="sign-in-to-your-azure-subscription"></a>Sign in to your Azure Subscription

Use the `Login-AzAccount` command and follow the on-screen directions to authenticate.

```powershell
Login-AzAccount
```

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

To create a new resource group with PowerShell, use the [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) command: 

> [!NOTE]
> A hierarchikus névtér jelenleg minden nyilvános régióban elérhető.

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hardcoding it repeatedly
$resourceGroup = "storage-quickstart-resource-group"
$location = "westus2"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

### <a name="create-a-general-purpose-v2-storage-account"></a>Általános célú 2-es verziójú tárfiók létrehozása

To create a general-purpose v2 storage account from PowerShell with locally-redundant storage (LRS), use the [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) command:

```powershell
$location = "westus2"

New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name "storagequickstart" `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind StorageV2 `
  -EnableHierarchicalNamespace $True
```

### <a name="clean-up-resources"></a>Az erőforrások eltávolítása

To remove the resource group and its associated resources, including the new storage account, use the [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) command: 

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="create-an-account-using-azure-cli"></a>Fiók létrehozása az Azure CLI használatával

To start Azure Cloud Shell, sign in to the [Azure portal](https://portal.azure.com).

If you want to sign in to your local installation of the CLI, run the login command:

```cli
az login
```

### <a name="add-the-cli-extension-for-azure-data-lake-gen-2"></a>Add the CLI extension for Azure Data Lake Gen 2

To interact with Data Lake Storage Gen2 by using the CLI, you'll have to add an extension to your shell.

To do that, enter the following command by using either the Cloud Shell or a local shell: `az extension add --name storage-preview`

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Ha az Azure CLI használatával kíván új erőforráscsoportot létrehozni, használja az [az group create](/cli/azure/group) parancsot.

```azurecli-interactive
az group create `
    --name storage-quickstart-resource-group `
    --location westus2
```

> [!NOTE]
> > A hierarchikus névtér jelenleg minden nyilvános régióban elérhető.

### <a name="create-a-general-purpose-v2-storage-account"></a>Általános célú 2-es verziójú tárfiók létrehozása

Egy helyileg redundáns tárolást (LRS) használó általános célú v2-tárfiók létrehozásához az Azure CLI-ben használja az [az storage account create](/cli/azure/storage/account) parancsot.

```azurecli-interactive
az storage account create `
    --name storagequickstart `
    --resource-group storage-quickstart-resource-group `
    --location westus2 `
    --sku Standard_LRS `
    --kind StorageV2 `
    --hierarchical-namespace true
```

### <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az [az group delete](/cli/azure/group) paranccsal eltávolítható az erőforráscsoport és az összes kapcsolódó erőforrás, beleértve az új tárfiókot is.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

In this article, you've created a storage account with Data Lake Storage Gen2 capabilities. To learn how to upload and download blobs to and from your storage account, see the following topic.

* [AzCopy V10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

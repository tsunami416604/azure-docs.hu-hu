---
title: "Azure gyors üzembe helyezés – hozzon létre egy tárfiókot, PowerShell-lel |} Microsoft Docs"
description: "Gyorsan megismerheti, hogyan kell új tárfiók létrehozása a PowerShell használatával"
services: storage
documentationcenter: 
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 06/29/2017
ms.author: robinsh
ms.openlocfilehash: c9175cce0cb93e73009fb8d751e54f631603d482
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-storage-account-using-powershell"></a>Hozzon létre egy tárfiókot, PowerShell használatával

Az Azure PowerShell-modul az Azure-erőforrások PowerShell-parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez az útmutató adatokat egy Azure Storage-fiók létrehozása a PowerShell használatával. 

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

A gyorsútmutatóhoz az Azure PowerShell-modul 3.6-os vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a `Login-AzureRmAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

```powershell
Login-AzureRmAccount
```

Ha nem tudja, melyik használni kívánt helyet, listázhatja a helyeket. Miután a lista megjelenik, megtalálja használni kívánt. Ez a példa használandó **eastus**. Ez tárolható egy változóban, és használja a változót, így módosíthatja egy helyen.

```powershell
Get-AzureRmLocation | select Location 
$location = "eastus"
```

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy Azure-erőforráscsoportot a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) parancsmaggal. Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

```powershell
# put resource group in a variable so you can use the same group name going forward
#   without hardcoding it repeatedly
$resourceGroup = "contoso-storage-accounts"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location 
```

## <a name="create-a-general-purpose-standard-storage-account"></a>Általános célú szabványos storage-fiók létrehozása

Többféle tárfiókok, attól függően, hogy hogyan fog használni, és melyik szolgáltatás (BLOB, fájlok, táblák vagy várólisták). Az alábbi táblázatban a lehetőségeket.

|**Tárfiók típusa**|**Általános célú standard**|**Általános célú prémium**|**Blob Storage, a gyakran és a ritkán használt adatok tárolási rétegei**|
|-----|-----|-----|-----|
|**Támogatott szolgáltatások**| A BLOB, a fájl, Table, Queue szolgáltatások | Blob service | Blob service|
|**Támogatott blobtípusok**|Blokkblobokat, lapblobokat, hozzáfűző blobokat | Lapblobok | Blokkblobok és hozzáfűző blobok|

Használjon [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount) minden négy szolgáltatáshoz használható általános célú standard szintű storage-fiók létrehozásához. A tárfiók neve *contosomvcstandard*, és konfigurálja úgy, hogy rendelkezik a helyileg redundáns tárolás és a blob-titkosítás engedélyezése.

```powershell
New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name "contosomvcstandard" `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage `
  -EnableEncryptionService Blob
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége, használhatja a [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) parancsot a távolítsa el az erőforráscsoportot és az összes kapcsolódó erőforrásokat. Ebben az esetben a létrehozott tárfiók eltávolítja.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Következő lépések

A gyors üzembe helyezési a létrehozott egy általános célú standard szintű tárfiókot. További információt a BLOB storage-fiók le- és feltöltése, továbbra is a Blob storage gyors üzembe helyezés.
> [!div class="nextstepaction"]
> [Átviteli objektumok az Azure Blob storage PowerShell használatával](../blobs/storage-quickstart-blobs-powershell.md)
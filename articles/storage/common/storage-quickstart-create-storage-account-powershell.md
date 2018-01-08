---
title: "Azure gyors üzembe helyezés – Tárfiók létrehozása a PowerShell-lel | Microsoft Docs"
description: "Gyorsan megismerheti az új tárfiókok a PowerShell-lel való létrehozásának módját."
services: storage
documentationcenter: 
author: tamram
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
ms.author: tamram
ms.openlocfilehash: 87f179d2cf3802af3eca58e0adf57b07069bfcc6
ms.sourcegitcommit: c87e036fe898318487ea8df31b13b328985ce0e1
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2017
---
# <a name="create-a-storage-account-using-powershell"></a>Storage-fiók létrehozása a PowerShell-lel

Az Azure PowerShell-modul az Azure-erőforrások PowerShell-parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez az útmutató az Azure Storage-fiókok a PowerShell-lel való létrehozását ismerteti. 

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

A gyorsútmutatóhoz az Azure PowerShell-modul 3.6-os vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a `Login-AzureRmAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

```powershell
Login-AzureRmAccount
```

Ha nem tudja, melyik helyet szeretné használni, kilistázhatja az elérhető helyeket. A megjelenő listában keresse meg a használni kívánt helyet. Ez a példa az **eastus** helyet használja. Tárolja el ezt egy változóban, és használja azt, így szükség esetén csak egy helyen kell módosítania az adatokat.

```powershell
Get-AzureRmLocation | select Location 
$location = "eastus"
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy Azure-erőforráscsoportot a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) parancsmaggal. Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

```powershell
# put resource group in a variable so you can use the same group name going forward
#   without hardcoding it repeatedly
$resourceGroup = "contoso-storage-accounts"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location 
```

## <a name="create-a-general-purpose-storage-account"></a>Általános célú tárfiók létrehozása

Több tárfióktípus is elérhető, a használat módjától és a kiválasztott szolgáltatástól (blob, fájl, tábla vagy sor) függően. Az alábbi táblázat a rendelkezésre álló lehetőségeket mutatja be.

|**Tárfiók típusa**|**Általános célú standard**|**Általános célú prémium**|**Blob Storage, a gyakran és a ritkán használt adatok tárolási rétegei**|
|-----|-----|-----|-----|
|**Támogatott szolgáltatások**| Blob, File, Table és Queue szolgáltatás | Blob szolgáltatás | Blob szolgáltatás|
|**Támogatott blobtípusok**|Blokkblobok, lapblobok és hozzáfűző blobok | Lapblobok | Blokkblobok és hozzáfűző blobok|

A [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount) használatával hozzon létre egy általános célú tárfiókot, amelyet mind a négy szolgáltatáshoz használhat. Adja a *contosomvcstandard* nevet a tárfióknak, és konfigurálja helyileg redundáns tárolásra, engedélyezett blobtitkosítással.

```powershell
New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name "contosomvcstandard" `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage `
  -EnableEncryptionService Blob
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, a [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) paranccsal eltávolítható az erőforráscsoport és az összes kapcsolódó erőforrás. Ebben az esetben a használatával eltávolítja a létrehozott tárfiókot.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy általános célú standard szintű tárfiókot hozott létre. Ha szeretne a blobok a tárfiókkal való fel- és letöltésével megismerkedni, folytassa a Blob Storage rövid útmutatójával.
> [!div class="nextstepaction"]
> [Objektumok továbbítása Azure Blob-tárolókra és -tárolókról a PowerShell-lel](../blobs/storage-quickstart-blobs-powershell.md)
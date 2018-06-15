---
title: Az Azure PowerShell tárolás kezelése az Azure független a felhők |} Microsoft Docs
description: Tárhelykezelés a kínai felhő, kormányzati felhőalapú és német felhőalapú Azure PowerShell használatával
services: storage
documentationcenter: na
author: roygara
manager: jeconnoc
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: rogarana
ms.openlocfilehash: 3eecf8918e6628071e44cc588b1826df68a00f10
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32186916"
---
# <a name="managing-storage-in-the-azure-independent-clouds-using-powershell"></a>Tárhelykezelés a felhőben az Azure független PowerShell használatával

A legtöbben Azure nyilvános Felhőjében használja a globális Azure telepítésekhez. Van még néhány független üzemelő Microsoft Azure a közös joghatóság alá okait, és így tovább. A független központi telepítéseket nevezzük "environments." Az alábbi lista részletesen felsorolja a független jelenleg elérhető felhő.

* [Az Azure Government felhő](https://azure.microsoft.com/features/gov/)
* [Azure Kínában a 21Vianet által működtetett Kína-felhő](http://www.windowsazure.cn/)
* [Az Azure német felhő](../../germany/germany-welcome.md)

## <a name="using-an-independent-cloud"></a>Egy független a felhővel 

A független felhők egyikében Azure Storage használatához csatlakozik Azure nyilvános helyett az adott felhő. Egy független felhők, hanem Azure nyilvános használata:

* Adja meg, hogy a *környezet* , amelyhez csatlakozni kíván.
* Állapítsa meg, és az elérhető régiók használja.
* Nem azonos a nyilvános Azure megfelelő végpont előtagot használja.

A példák szükség Azure PowerShell modul verziója 4.4.0 vagy újabb. A PowerShell-ablakban futtassa `Get-Module -ListAvailable AzureRM` verzió található. Ha semmi nem jelenik meg, vagy szeretné frissíteni, lásd: [telepítése Azure PowerShell modul](/powershell/azure/install-azurerm-ps). 

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Futtassa a [Get-AzureEnvironment](/powershell/module/azure/Get-AzureRmEnvironment) parancsmag használatával ellenőrizheti az elérhető az Azure-környezetek:
   
```powershell
Get-AzureRmEnvironment
```

Jelentkezzen be a fiók, amely hozzáfér ehhez a felhő kívánt csatlakozni, és a környezet beállításához. Ez a példa bemutatja, hogyan használja az Azure Government felhő fiókkal bejelentkezni.   

```powershell
Connect-AzureRmAccount –Environment AzureUSGovernment
```

A kínai felhő elérésére használja a környezet **AzureChinaCloud**. A német felhő elérésére, **AzureGermanCloud**.

Ezen a ponton, ha a tárfiók vagy egy másik erőforrás létrehozása helyek listáját, lekérheti a helyeken érhető el a kiválasztott felhőbe történő [Get-AzureRmLocation](/powershell/module/azurerm.resources/get-azurermlocation).

```powershell
Get-AzureRmLocation | select Location, DisplayName
```

Az alábbi táblázat a helyek, a német felhő adott vissza.

|Hely | displayName |
|----|----|
| germanycentral | Közép-Németország|
| germanynortheast | Északkelet-Németország | 


## <a name="endpoint-suffix"></a>Végpont utótag

Egyes ezekben a környezetekben a végpont utótagja eltér az Azure nyilvános végpontot. A blob végpontja utótagja Azure nyilvános van például **blob.core.windows.net**. A blob végpontja utótag van kormányzati felhő **blob.core.usgovcloudapi.net**. 

### <a name="get-endpoint-using-get-azurermenvironment"></a>Get-AzureRMEnvironment végpont beolvasása 

Le a végpont utótag [Get-AzureRMEnvironment](/powershell/module/azurerm.profile/get-azurermenvironment). A végpont a *StorageEndpointSuffix* tulajdonság a környezetben. Az alábbi kódtöredékek bemutatják, hogyan ehhez. Ezek a parancsok mindegyikét térjen vissza valami "core.cloudapp.net" vagy "core.cloudapi.de" stb. Ez hozzáfűzni a társzolgáltatás, hogy a szolgáltatás eléréséhez. Például "queue.core.cloudapi.de" érik el a queue szolgáltatás német felhőben.

A kódrészletet lekéri az összes és a végpont-utótagot, minden egyes a környezetben.

```powershell
Get-AzureRmEnvironment | select Name, StorageEndpointSuffix 
```

Ez a parancs a következő eredményt ad vissza.

| Name (Név)| StorageEndpointSuffix|
|----|----|
| AzureChinaCloud | core.chinacloudapi.cn|
| AzureCloud | core.windows.net |
| AzureGermanCloud | core.cloudapi.de|
| AzureUSGovernment | Core.usgovcloudapi.NET |

Összes tulajdonság a megadott környezet lekéréséhez hívja **Get-AzureRmEnvironment** , és adja meg a felhő neve. A kódrészletet tulajdonságok; listáját adja vissza. Keressen **StorageEndpointSuffix** a listában. A következő példa a német felhőben van.

```powershell
Get-AzureRmEnvironment -Name AzureGermanCloud 
```

A eredményei a következőhöz hasonló:

|Tulajdonság neve|Érték|
|----|----|
| Name (Név) | AzureGermanCloud |
| EnableAdfsAuthentication | False (Hamis) |
| ActiveDirectoryServiceEndpointResourceI | http://management.core.cloudapi.de/ |
| GalleryURL | https://gallery.cloudapi.de/ |
| ManagementPortalUrl | https://portal.microsoftazure.de/ | 
| ServiceManagementUrl | https://manage.core.cloudapi.de/ |
| PublishSettingsFileUrl| https://manage.microsoftazure.de/publishsettings/index |
| ResourceManagerUrl | http://management.microsoftazure.de/ |
| SqlDatabaseDnsSuffix | .database.cloudapi.de |
| **StorageEndpointSuffix** | core.cloudapi.de |
| ... | ... | 

Csak a tárolási végpont utótag tulajdonság beolvasása, az adott felhő beolvasása, és kérje meg, hogy egy tulajdonság.

```powershell
$environment = Get-AzureRmEnvironment -Name AzureGermanCloud
Write-Host "Storage EndPoint Suffix = " $environment.StorageEndpointSuffix 
```

Ez a következő információkat ad vissza.

```
Storage Endpoint Suffix = core.cloudapi.de
```

### <a name="get-endpoint-from-a-storage-account"></a>Végpont beolvasása a tárfiókból

A tárfiók végpontjait beolvasása tulajdonságait is ellenőrizheti. Ez akkor lehet hasznos, ha már használja egy tárfiókot a PowerShell parancsfájl; csak a végpontot kell kérheti le. 

```powershell
# Get a reference to the storage account.
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"
$storageAccount = Get-AzureRmStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageAccountName 
  # Output the endpoints.
Write-Host "blob endpoint = " $storageAccount.PrimaryEndPoints.Blob 
Write-Host "file endpoint = " $storageAccount.PrimaryEndPoints.File
Write-Host "queue endpoint = " $storageAccount.PrimaryEndPoints.Queue
Write-Host "table endpoint = " $storageAccount.PrimaryEndPoints.Table
```

A tárfiók a kormányzati felhőben ezt adja vissza a következő: 

```
blob endpoint = http://myexistingstorageaccount.blob.core.usgovcloudapi.net/
file endpoint = http://myexistingstorageaccount.file.core.usgovcloudapi.net/
queue endpoint = http://myexistingstorageaccount.queue.core.usgovcloudapi.net/
table endpoint = http://myexistingstorageaccount.table.core.usgovcloudapi.net/
```

## <a name="after-setting-the-environment"></a>A környezet beállítása után

Az itt továbbítja, használhatja a használja a storage-fiókok kezelése, és az adatok vezérlősík eléréséhez, a cikkben leírtak szerint ugyanazon PowerShell [Azure PowerShell használata az Azure Storage](storage-powershell-guide-full.md).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha létrehozott egy új erőforráscsoportot és az ebben a gyakorlatban egy tárfiókot, távolítsa el az eszközök összes az erőforráscsoport eltávolításával. Így törli a csoportban lévő összes erőforrást is. Ebben az esetben eltávolítja a létrehozott tárfiókban és az erőforráscsoport magát.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>További lépések

* [PowerShell-munkamenetek között tárolásakor felhasználói bejelentkezések](/powershell/azure/context-persistence)
* [Az Azure Government storage](../../azure-government/documentation-government-services-storage.md)
* [A Microsoft Azure Government fejlesztői útmutató](../../azure-government/documentation-government-developer-guide.md)
* [Az Azure Kína alkalmazások megjegyzések fejlesztők számára](https://msdn.microsoft.com/library/azure/dn578439.aspx)
* [A Németországi Azure-dokumentáció](../../germany/germany-welcome.md)

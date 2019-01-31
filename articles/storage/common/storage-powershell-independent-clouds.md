---
title: Az Azure PowerShell Storage kezelése az Azure használatától függetlenül a felhők |} A Microsoft Docs
description: Tárhelykezelés a kínai felhőben, a kormányzati felhő és a német felhőben az Azure PowerShell-lel
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 10/24/2017
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: 6a3fe8c7235a0cf475f188a3379e372cd777ce8c
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55477641"
---
# <a name="managing-storage-in-the-azure-independent-clouds-using-powershell"></a>Az Azure-PowerShell-lel független felhőkben tárolás kezelése

A legtöbb ember használata az Azure nyilvános felhő a globális Azure üzemelő példányhoz. Vannak bizonyos független üzembe helyezések, a Microsoft Azure az adatszuverenitási miatt és így tovább. Ezek független üzembe helyezések nevezik "környezetekben." Az alábbi lista részletesen felsorolja az aktuálisan elérhető független felhőkben.

* [Azure Government Cloud](https://azure.microsoft.com/features/gov/)
* [Kínában a 21Vianet által üzemeltetett Azure China Cloud](http://www.windowsazure.cn/)
* [Az Azure német felhőben](../../germany/germany-welcome.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="using-an-independent-cloud"></a>Egy független felhő használatával 

A független felhőkben egy Azure Storage szolgáltatást használja, csatlakoznia kell az adott helyett az Azure-beli nyilvános felhő. Független felhők helyett az Azure-beli nyilvános egyikének használatához:

* Adja meg, hogy a *környezet* , amelyhez csatlakozni kíván.
* Határozza meg, és használja az elérhető régiók.
* A megfelelő végpont utótagja, amely eltér a Azure-beli nyilvános használhatja.

Példák Azure PowerShell modul Az 0,7 vagy újabb verziójára lesz szükség. Egy PowerShell-ablakot a Futtatás `Get-Module -ListAvailable Az` a verzió megkereséséhez. Ha semmi nem jelenik meg, vagy szeretné frissíteni, lásd: [Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps). 

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Futtassa a [Get-AzEnvironment](/powershell/module/az.accounts/get-azenvironment) parancsmaggal ellenőrizheti az elérhető Azure-környezetek:
   
```powershell
Get-AzEnvironment
```

Jelentkezzen be a fiók, amely hozzáféréssel rendelkezik, amelyhez csatlakozhat, és állítsa be a környezetet szeretne a felhőbe. Ez a példa bemutatja, hogyan lehet bejelentkezni egy fiókba, amelyet az Azure Government Cloud használ.   

```powershell
Connect-AzAccount –Environment AzureUSGovernment
```

A kínai Felhőszolgáltatásban elérésére, használja a környezet **AzureChinaCloud**. A Németországi Felhőjének elérésére, **AzureGermanCloud**.

Ezen a ponton helyeihez hozhat létre egy storage-fiók vagy egy másik erőforrásra van szüksége, ha lekérdezheti, ha a kiválasztott felhő használatához elérhető helyek [Get-AzLocation](/powershell/module/az.resources/get-azlocation).

```powershell
Get-AzLocation | select Location, DisplayName
```

Az alábbi táblázat a helyek, a Németországi felhőhöz készült adja vissza.

|Hely | Megjelenítendő név |
|----|----|
| germanycentral | Közép-Németország|
| germanynortheast | Északkelet-Németország | 


## <a name="endpoint-suffix"></a>Végpont utótagja

Az egyes ezekben a környezetekben a végpont utótagja eltér az Azure-beli nyilvános végpontot. Például egy, az Azure-beli nyilvános a blob-végpont utótagja nem **blob.core.windows.net**. A kormányzati felhőhöz, a blob-végpont utótagja van **blob.core.usgovcloudapi.net**. 

### <a name="get-endpoint-using-get-azenvironment"></a>Használja a Get-AzEnvironment végpont 

Lekérdezni a végpont utótagja használatával [Get-AzEnvironment](/powershell/module/az.accounts/get-azenvironment). A végpont a *StorageEndpointSuffix* tulajdonság a környezet. Az alábbi kódtöredékek bemutatják, hogyan ehhez. Ezek a parancsok mindegyike adja vissza valami például a "core.cloudapp.net" vagy "core.cloudapi.de", stb. Ez hozzáfűzése a társzolgáltatás az adott szolgáltatás eléréséhez. Például "queue.core.cloudapi.de" hozzá fog férni a queue szolgáltatás német felhőben.

Ez a kódrészlet lekérdezi az összes környezet és a végpont utótagja minden egyes.

```powershell
Get-AzEnvironment | select Name, StorageEndpointSuffix 
```

Ez a parancs az alábbi eredményeket adja vissza.

| Name (Név)| StorageEndpointSuffix|
|----|----|
| AzureChinaCloud | core.chinacloudapi.cn|
| AzureCloud | core.windows.net |
| AzureGermanCloud | core.cloudapi.de|
| AzureUSGovernment | core.usgovcloudapi.net |

Összes tulajdonság a megadott környezet lekéréséhez hívja **Get-AzEnvironment** és adja meg a felhő nevét. Ez a kódrészlet tulajdonságok; listáját adja vissza. Keressen **StorageEndpointSuffix** a listában. Az alábbi példa a német felhőben van.

```powershell
Get-AzEnvironment -Name AzureGermanCloud 
```

Az eredmények a következőhöz hasonlóak:

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

Csak a tárolási végpont utótagja tulajdonság lekéréséhez lekérni az adott felhő, és kérje meg, hogy egy tulajdonság.

```powershell
$environment = Get-AzEnvironment -Name AzureGermanCloud
Write-Host "Storage EndPoint Suffix = " $environment.StorageEndpointSuffix 
```

Ez a következő információkat adja vissza.

```
Storage Endpoint Suffix = core.cloudapi.de
```

### <a name="get-endpoint-from-a-storage-account"></a>Végpont beolvasása a tárfiókból

A végpontok beolvasni a storage-fiók tulajdonságait is ellenőrizheti. Ez akkor lehet hasznos, ha már használja a PowerShell-parancsfájlok; a storage-fiók csak a végpontot kell kérheti le. 

```powershell
# Get a reference to the storage account.
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"
$storageAccount = Get-AzStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageAccountName 
  # Output the endpoints.
Write-Host "blob endpoint = " $storageAccount.PrimaryEndPoints.Blob 
Write-Host "file endpoint = " $storageAccount.PrimaryEndPoints.File
Write-Host "queue endpoint = " $storageAccount.PrimaryEndPoints.Queue
Write-Host "table endpoint = " $storageAccount.PrimaryEndPoints.Table
```

A Government Cloud storage-fiókok ezt adja vissza a következő: 

```
blob endpoint = http://myexistingstorageaccount.blob.core.usgovcloudapi.net/
file endpoint = http://myexistingstorageaccount.file.core.usgovcloudapi.net/
queue endpoint = http://myexistingstorageaccount.queue.core.usgovcloudapi.net/
table endpoint = http://myexistingstorageaccount.table.core.usgovcloudapi.net/
```

## <a name="after-setting-the-environment"></a>A környezet beállítása után

Itt módosítástól, az ugyanazon PowerShell, a storage-fiókok felügyeletéhez és az adatsík érhető el a cikkben leírtak szerint használhatja a [az Azure PowerShell az Azure Storage](storage-powershell-guide-full.md).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha létrehozott egy új erőforráscsoportot és a egy storage-fiókot ehhez a gyakorlathoz, az erőforráscsoport törlésével eltávolíthatja összes eszköz. Így törli a csoportban lévő összes erőforrást is. Ebben az esetben eltávolítja a létrehozott tárfiókot és magát az erőforráscsoportot.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>További lépések

* [Felhasználói bejelentkezések megőrzése a PowerShell-munkamenetek között](/powershell/azure/context-persistence)
* [Az Azure Government-tároló](../../azure-government/documentation-government-services-storage.md)
* [A Microsoft Azure Government fejlesztői útmutató](../../azure-government/documentation-government-developer-guide.md)
* [Az Azure China alkalmazások megjegyzések fejlesztők számára](https://msdn.microsoft.com/library/azure/dn578439.aspx)
* [Azure Germany – dokumentáció](../../germany/germany-welcome.md)

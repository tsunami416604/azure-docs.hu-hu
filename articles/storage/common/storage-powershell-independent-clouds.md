---
title: Adatok kezelése az Azure független felhőiben a PowerShell használatával
titleSuffix: Azure Storage
description: A tárolás kezelése a China Cloud, a Kormányzati felhő és a német felhőben az Azure PowerShell használatával.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 5fa515515c06466e121a5c0ee925fd4d14245363
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74895236"
---
# <a name="managing-storage-in-the-azure-independent-clouds-using-powershell"></a>A storage kezelése az Azure független felhőiben a PowerShell használatával

A legtöbb ember az Azure Public Cloud szolgáltatást használja a globális Azure-telepítéshez. A szuverenitás és így tovább a Microsoft Azure néhány független központi telepítése is létezik. Ezeket a független központi telepítéseket "környezetnek" nevezzük. Az alábbi lista részletezi a jelenleg rendelkezésre álló független felhőket.

* [Azure Government Cloud](https://azure.microsoft.com/features/gov/)
* [A 21Vianet által kínában üzemeltetett Azure China 21Vianet Cloud](http://www.windowsazure.cn/)
* [Azure német felhő](../../germany/germany-welcome.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="using-an-independent-cloud"></a>Független felhő használata

Ha az Azure Storage-t a független felhők egyikében szeretné használni, az Azure Public helyett a felhőhöz kell csatlakoznia. A független felhők egyikének használata az Azure Public helyett:

* Megadhatja azt a *környezetet,* amelyhez csatlakozni szeretne.
* Ön határozza meg és használja a rendelkezésre álló régiókat.
* A megfelelő végpontutótagot használja, amely eltér az Azure Publictól.

A példák az Azure PowerShell-modul Az 0.7-es vagy újabb verzióját igénylik. Egy PowerShell-ablakban `Get-Module -ListAvailable Az` futtassa a verzió megkereséséhez. Ha semmi sem szerepel a listában, vagy frissítenie kell, olvassa el [az Azure PowerShell-modul telepítése című témakört.](/powershell/azure/install-Az-ps)

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Futtassa a [Get-AzEnvironment](/powershell/module/az.accounts/get-azenvironment) parancsmast a rendelkezésre álló Azure-környezetek megtekintéséhez:

```powershell
Get-AzEnvironment
```

Jelentkezzen be olyan fiókjába, amely hozzáfér ahhoz a felhőhöz, amelyhez csatlakozni szeretne, és állítsa be a környezetet. Ez a példa bemutatja, hogyan jelentkezz be egy azure government cloudot használó fiókba.   

```powershell
Connect-AzAccount –Environment AzureUSGovernment
```

A China Cloud eléréséhez használja az **AzureChinaCloud környezetet.** A német felhő eléréséhez használja az **AzureGermanCloud**ot.

Ezen a ponton, ha szüksége van a helyek listájára, hogy hozzon létre egy tárfiókot, vagy egy másik erőforrás, lekérdezheti a rendelkezésre álló helyek a kiválasztott felhő segítségével [Get-AzLocation](/powershell/module/az.resources/get-azlocation).

```powershell
Get-AzLocation | select Location, DisplayName
```

Az alábbi táblázat a német felhőhöz visszaadott helyeket mutatja be.

|Hely | Megjelenítendő név |
|----|----|
| `germanycentral` | Közép-Németország|
| `germanynortheast` | Északkelet-Németország |


## <a name="endpoint-suffix"></a>Végpontutótag

A végpont-utótag az egyes ilyen környezetekben eltér az Azure nyilvános végpont. Például a blob végpont utótag az Azure Public **blob.core.windows.net.** A kormányzati felhő esetében a blob végpontutótag **blob.core.usgovcloudapi.net.**

### <a name="get-endpoint-using-get-azenvironment"></a>Végpont beszerezése a Get-AzEnvironment használatával

A végpontutótag beolvasása a [Get-AzEnvironment használatával.](/powershell/module/az.accounts/get-azenvironment) A végpont a *környezet StorageEndpointSutófix* tulajdonsága.

A következő kódrészletek bemutatják, hogyan lehet beolvasni a végpontutótagot. Mindezek a parancsok vissza valami ilyesmi : "core.cloudapp.net" vagy "core.cloudapi.de", stb. Fűzz hozzá az utótagot a tárolószolgáltatáshoz a szolgáltatás eléréséhez. Például a "queue.core.cloudapi.de" a német felhőben éri el a várólista-szolgáltatást.

Ez a kódrészlet lekéri az összes környezetet és a végpont utótagot mindegyikhez.

```powershell
Get-AzEnvironment | select Name, StorageEndpointSuffix 
```

Ez a parancs a következő eredményeket adja vissza.

| Név| StorageEndpointSutótag|
|----|----|
| AzureChinaCloud | core.chinacloudapi.cn|
| AzureCloud | core.windows.net |
| AzureGermanCloud | core.cloudapi.de|
| AzureUSGovernment | core.usgovcloudapi.net |

A megadott környezet összes tulajdonságának beolvasásához hívja meg a **Get-AzEnvironment szolgáltatást,** és adja meg a felhő nevét. Ez a kódrészlet a tulajdonságok listáját adja vissza; keresse meg a **StorageEndpointSuffix-et** a listában. A következő példa a német felhő.

```powershell
Get-AzEnvironment -Name AzureGermanCloud
```

Az eredmények hasonlóak a következő értékekhez:

|Tulajdonság neve|Érték|
|----|----|
| Név | `AzureGermanCloud` |
| EnableAdfsAuthentication | `False` |
| ActiveDirectoryEndpointResourceI | `http://management.core.cloudapi.de/` |
| GalériaURL | `https://gallery.cloudapi.de/` |
| ManagementPortalUrl | `https://portal.microsoftazure.de/` |
| ServiceManagementUrl | `https://manage.core.cloudapi.de/` |
| PublishSettingsFileUrl| `https://manage.microsoftazure.de/publishsettings/index` |
| ResourceManagerUrl | `http://management.microsoftazure.de/` |
| SqlDatabaseDnsS utótag | `.database.cloudapi.de` |
| **StorageEndpointSutótag** | `core.cloudapi.de` |
| ... | ... |
Csak a tárolási végpont utótag tulajdonság ának lekéréséhez kérje le az adott felhőt, és csak egy tulajdonságot kérjen.

```powershell
$environment = Get-AzEnvironment -Name AzureGermanCloud
Write-Host "Storage EndPoint Suffix = " $environment.StorageEndpointSuffix
```

Ez a parancs a következő információkat adja vissza:

`Storage Endpoint Suffix = core.cloudapi.de`

### <a name="get-endpoint-from-a-storage-account"></a>Végpont bekéselése tárfiókból

A végpontok lekéréséhez megvizsgálhatja a tárfiók tulajdonságait is:

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

A kormányzati felhőben lévő tárfiók esetén ez a parancs a következő kimenetet adja vissza:

```
blob endpoint = http://myexistingstorageaccount.blob.core.usgovcloudapi.net/
file endpoint = http://myexistingstorageaccount.file.core.usgovcloudapi.net/
queue endpoint = http://myexistingstorageaccount.queue.core.usgovcloudapi.net/
table endpoint = http://myexistingstorageaccount.table.core.usgovcloudapi.net/
```

## <a name="after-setting-the-environment"></a>A környezet beállítása után

Innen a továbbiakban használhatja ugyanazt a PowerShell-t, amely a tárfiókok kezeléséhez és az adatsík eléréséhez használt az Azure PowerShell használata az [Azure Storage szolgáltatással](storage-powershell-guide-full.md)című cikkben leírtak szerint.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha létrehozott egy új erőforráscsoportot és egy tárfiókot ehhez a gyakorlathoz, az erőforráscsoport törlésével mindkét eszközt eltávolíthatja. Az erőforráscsoport törlésével a csoportban található összes erőforrás törlődik.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>További lépések

* [Felhasználói bejelentkezések megőrzése a PowerShell-munkamenetek között](/powershell/azure/context-persistence)
* [Azure Government-tárhely](../../azure-government/documentation-government-services-storage.md)
* [A Microsoft Azure kormányzati fejlesztői útmutatója](../../azure-government/documentation-government-developer-guide.md)
* [Fejlesztői megjegyzések az Azure China 21Vianet alkalmazásokhoz](https://msdn.microsoft.com/library/azure/dn578439.aspx)
* [Az Azure Germany dokumentációja](../../germany/germany-welcome.md)

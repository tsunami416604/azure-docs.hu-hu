---
title: PowerShell – Azure Batch használatának első lépései |} A Microsoft Docs
description: Gyors bevezetés a Batch-erőforrások kezeléséhez használható Azure PowerShell-parancsmagok használatába.
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: powershell
ms.workload: big-compute
ms.date: 10/05/2018
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: bd8e5425a09f0faeaa573cec58def88f352b9a1d
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53548068"
---
# <a name="manage-batch-resources-with-powershell-cmdlets"></a>Batch-erőforrások kezelése PowerShell-parancsmagokkal

Az Azure Batch PowerShell-parancsmagokkal a Batch API-k, az Azure Portal és az Azure parancssori felülete (CLI) használatával végrehajtott műveletek közül számosat elvégezhet vagy szkriptekbe foglalhat. Ez a cikk egy rövid áttekintést nyújt a Batch-fiókok felügyeletéhez és a Batch-erőforrásokkal, például készletekkel, feladatokkal és tevékenységekkel használható parancsmagokról.

A Batch-parancsmagok teljes listájáért és a parancsmagok részletes szintaxisáért lásd: [Azure Batch-parancsmagok referenciája](/powershell/module/azurerm.batch/#batch).

Ez a cikk az Azure Batch modul 4.1.5-ös verziójának parancsmagjain alapul. Ajánlott gyakran frissíteni az Azure PowerShell-modulokat a szolgáltatásfrissítések és -fejlesztések kihasználása érdekében.

## <a name="prerequisites"></a>Előfeltételek

* [Telepítse és konfigurálja az Azure PowerShell modul legújabb verzióját](/powershell/azure/overview). Egy adott Azure Batch-modul, például egy kiadás előtti modul telepítésével kapcsolatban lásd a [PowerShell-galériát](https://www.powershellgallery.com/packages/AzureRM.Batch/5.0.0-preview) ismertető cikket. 

* A **Connect-AzureRmAccount** parancsmag futtatásával csatlakozzon az előfizetéséhez (az Azure Batch-parancsmagok az Azure Resource Manager modulban találhatók):

  ```PowerShell
  Connect-AzureRmAccount
  ```

* **Regisztráljon a Batch-szolgáltató névterével**. Ezt a műveletet **előfizetésenként csak egyszer** kell elvégezni.
  
  ```PowerShell
  Register-AzureRMResourceProvider -ProviderNamespace Microsoft.Batch`
  ```

## <a name="manage-batch-accounts-and-keys"></a>Batch-fiókok és kulcsok felügyelete

### <a name="create-a-batch-account"></a>Batch-fiók létrehozása

A **New-AzureRmBatchAccount** parancs egy Batch-fiókot hoz létre a meghatározott erőforráscsoportban. Ha még nem rendelkezik erőforráscsoporttal, hozzon létre egyet a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) parancsmag futtatásával. A **Hely** paraméternél adjon meg egy Azure régiót, például az „USA középső régiója”. Példa:

```PowerShell
New-AzureRmResourceGroup –Name MyBatchResourceGroup –location "Central US"
```

Ezután hozzon létre egy Batch-fiókot az erőforráscsoportban, adjon meg egy fióknevet az <*account_name*> helyőrző helyett, és adja meg az erőforráscsoport helyét és nevét. A Batch-fiókok létrehozása eltarthat egy ideig. Példa:

```PowerShell
New-AzureRmBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName <res_group_name>
```

> [!NOTE]
> A Batch-fiók nevének egyedinek kell lennie az erőforráscsoport Azure-régiójában, 3–24 karaktert kell tartalmaznia, és csak kisbetűkből és számokból állhat.
> 

### <a name="get-account-access-keys"></a>Fiók hívóbetűinek beszerzése

A **Get-AzureRmBatchAccountKeys** megjeleníti az Azure Batch-fiókkal társított hívóbetűket. Például a következő futtatásával lekérheti a létrehozott fiók elsődleges és másodlagos hívóbetűit.

 ```PowerShell
$Account = Get-AzureRmBatchAccountKeys –AccountName <account_name>

$Account.PrimaryAccountKey

$Account.SecondaryAccountKey
```

### <a name="generate-a-new-access-key"></a>Új hívóbetű létrehozása

A **New-AzureRmBatchAccountKey** új elsődleges vagy másodlagos hívóbetűt hoz létre az Azure Batch-fiókhoz. Ha például új elsődleges hívóbetűt szeretne létrehozni a Batch-fiókhoz, írja be a következőt:

```PowerShell
New-AzureRmBatchAccountKey -AccountName <account_name> -KeyType Primary
```

> [!NOTE]
> Új másodlagos hívóbetű létrehozásához adja meg a „Másodlagos” szöveget a **KeyType** paraméterhez. Az elsődleges és a másodlagos hívóbetűket külön kell újra létrehoznia.
> 

### <a name="delete-a-batch-account"></a>Batch-fiók törlése

A **Remove-AzureRmBatchAccount** törli a Batch-fiókot. Példa:

```PowerShell
Remove-AzureRmBatchAccount -AccountName <account_name>
```

A felugró kérdésre erősítse meg, hogy valóban el kívánja távolítani a fiókot. A fiókeltávolítás eltarthat egy ideig.

## <a name="create-a-batchaccountcontext-object"></a>BatchAccountContext objektum létrehozása

A Batch-erőforrások kezeléséhez szükséges hitelesítést elvégezheti megosztott kulcsos hitelesítéssel vagy Azure Active Directory-hitelesítéssel. A Batch PowerShell-parancsmagokkal végzett hitelesítéshez először hozzon létre egy BatchAccountContext objektumot, amely a fiók hitelesítő adatait vagy az identitást tárolja. A BatchAccountContext objektumot a **BatchContext** paramétert használó parancsmagokba adja át. 

### <a name="shared-key-authentication"></a>Megosztott kulcsos hitelesítés

```PowerShell
$context = Get-AzureRmBatchAccountKeys -AccountName <account_name>
```

> [!NOTE]
> Alapértelmezés szerint a fiók elsődleges hívóbetűjét használja a hitelesítéshez, de explicit módon kiválaszthatja a használni kívánt betűt, ha módosítja a BatchAccountContext objektum **KeyInUse** tulajdonságát: `$context.KeyInUse = "Secondary"`.
> 

### <a name="azure-active-directory-authentication"></a>Hitelesítés Azure Active Directory-fiókkal

```PowerShell
$context = Get-AzureRmBatchAccount -AccountName <account_name>
```

## <a name="create-and-modify-batch-resources"></a>Batch-erőforrások létrehozása és módosítása
Például a **New-AzureBatchPool**, **New-AzureBatchJob** és **New-AzureBatchTask** parancsmagokkal hozhat létre erőforrásokat a Batch-fiókokban. Megfelelő **Get-** és **Set-** parancsmagokkal frissítheti a meglévő erőforrások tulajdonságait, és a **Remove-** parancsmagokkal távolíthatja el az erőforrásokat a Batch-fiókokról.

Sok parancsmag használatakor egy BatchContext objektum átadása mellett részletes erőforrás-beállításokat tartalmazó objektumok létrehozására vagy átadására is szükség van, ahogyan az a következő példában látható. További példákat az egyes parancsmagok részletes súgójában talál.

### <a name="create-a-batch-pool"></a>Batch-készlet létrehozása

Batch-készlet létrehozásakor vagy frissítésekor kiválaszthatja a felhőszolgáltatás- vagy a virtuálisgép-konfigurációt az operációs rendszerhez a számítási csomópontokon (lásd a [Batch-funkciók áttekintésével](batch-api-basics.md#pool) kapcsolatos témakört). Ha a felhőszolgáltatás-konfigurációt adja meg, a számítási csomópontokról az egyik [Azure-beli vendég operációs rendszer kiadásával](../cloud-services/cloud-services-guestos-update-matrix.md#releases) készül rendszerkép. Ha a virtuálisgép-konfigurációt választja, megadhatja az [Azure Virtual Machines Marketplace-en][vm_marketplace] felsorolt támogatott Linux vagy Windows virtuálisgép-rendszerképek egyikét, vagy megadhat egy Ön által előkészített egyéni rendszerképet.

A **New-AzureBatchPool** parancs futtatásakor adja át a PSCloudServiceConfiguration vagy PSVirtualMachineConfiguration objektumban található operációsrendszer-beállításokat. A következő kódrészlet például egy új Batch-készletet hoz létre Standard_A1 méretű számítási csomópontokkal a virtuális gép konfigurációjában, amelyhez az Ubuntu Server 16.04-LTS rendszerképet használja. Itt a **VirtualMachineConfiguration** paraméter a *$configuration* változót a PSVirtualMachineConfiguration objektumként határozza meg. A **BatchContext** paraméter egy korábban meghatározott *$context* változót ad meg a BatchAccountContext objektumként.

```PowerShell
$imageRef = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSImageReference" -ArgumentList @("UbuntuServer","Canonical","16.04.0-LTS")

$configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSVirtualMachineConfiguration" -ArgumentList @($imageRef, "batch.node.ubuntu 16.04")

New-AzureBatchPool -Id "mypool" -VirtualMachineSize "Standard_a1" -VirtualMachineConfiguration $configuration -AutoScaleFormula '$TargetDedicated=4;' -BatchContext $context
```

Az új készletben a számítási csomópontok célszámát egy automatikus méretezési képlet határozza meg. Ebben az esetben a képlet egyszerűen a **$TargetDedicated=4**, ami jelzi, hogy a készletben lévő számítási csomópontok száma legfeljebb 4.

## <a name="query-for-pools-jobs-tasks-and-other-details"></a>Készletek, feladatok, tevékenységek és egyéb részletek lekérdezése

Például a **Get-AzureBatchPool**, **Get-AzureBatchJob** és **Get-AzureBatchTask** parancsmagokkal kérdezhet le Batch-fiókokon létrehozott entitásokat.

### <a name="query-for-data"></a>Adatok lekérdezése

Például a **Get-AzureBatchPools** használatával megkeresheti a készleteket. Ez alapértelmezés szerint a fiók összes készletét lekérdezi, ha a BatchAccountContext objektumot már tárolta a *$context* helyen:

```PowerShell
Get-AzureBatchPool -BatchContext $context
```

### <a name="use-an-odata-filter"></a>OData-szűrő használata

Megadhat egy OData-szűrőt a **Filter** paraméterrel, ha csak a kívánt objektumokat szeretné megkeresni. Megkeresheti például az összes olyan készletet, amelynek az azonosítója a „myPool” karakterlánccal kezdődik:

```PowerShell
$filter = "startswith(id,'myPool')"

Get-AzureBatchPool -Filter $filter -BatchContext $context
```

Ez a módszer nem olyan rugalmas, mint a „Where-Object” használata a helyi folyamatokban. A lekérdezést azonban közvetlenül a Batch szolgáltatásnak küldi a rendszer, hogy minden szűrés a kiszolgálói oldalon történjen, így internetes sávszélesség takarítható meg.

### <a name="use-the-id-parameter"></a>Az Id paraméter használata

Az OData-szűrő alternatívája az **Id** paraméter használata. Egy „myPool” azonosítójú adott készlet lekérdezése:

```PowerShell
Get-AzureBatchPool -Id "myPool" -BatchContext $context
```

Az **ID** paraméter csak a teljes azonosítót használó kereséseket támogatja, a helyettesítő karaktereket és az OData stílusú szűrőket nem.

### <a name="use-the-maxcount-parameter"></a>A MaxCount paraméter használata

Alapértelmezés szerint mindegyik parancsmag maximum 1000 objektumot ad vissza. Ha eléri ezt a korlátot, pontosítsa a szűrőt, hogy kevesebb objektumot adjon vissza, vagy explicit módon állítson be maximális értéket a **MaxCount** paraméterrel. Példa:

```PowerShell
Get-AzureBatchTask -MaxCount 2500 -BatchContext $context
```

A felső határ eltávolításához 0 vagy kisebb értéket adjon meg a **MaxCount** paraméternek.

### <a name="use-the-powershell-pipeline"></a>A PowerShell-folyamat használata

A Batch-parancsmagok a PowerShell-folyamattal adatokat tudnak küldeni a folyamatok között. Ennek ugyanaz a hatása, mint a paraméterek megadása, de megkönnyíti a több entitással való munkát.

Például keresse meg és jelenítse meg fiókjában az összes feladatot:

```PowerShell
Get-AzureBatchJob -BatchContext $context | Get-AzureBatchTask -BatchContext $context
```

Indítsa újra a készlet összes számítási csomópontját:

```PowerShell
Get-AzureBatchComputeNode -PoolId "myPool" -BatchContext $context | Restart-AzureBatchComputeNode -BatchContext $context
```

## <a name="application-package-management"></a>Alkalmazáscsomagok kezelése

Az alkalmazáscsomagok egyszerű módot kínálnak az alkalmazások üzembe helyezésére a készletek számítási csomópontjain. A Batch PowerShell-parancsmagjaival feltöltheti és kezelheti az alkalmazáscsomagokat a Batch-fiókban, valamint a számítási csomópontokban üzembe helyezheti a csomagverziókat.

Alkalmazás **létrehozása**:

```PowerShell
New-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"
```

Alkalmazáscsomag **hozzáadása**:

```PowerShell
New-AzureRmBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0" -Format zip -FilePath package001.zip
```

Állítsa be az alkalmazás **alapértelmezett verzióját** :

```PowerShell
Set-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -DefaultVersion "1.0"
```

Alkalmazás csomagjainak **listázása**

```PowerShell
$application = Get-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

$application.ApplicationPackages
```

Alkalmazáscsomag **törlése**

```PowerShell
Remove-AzureRmBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0"
```

Alkalmazás **törlése**

```PowerShell
Remove-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"
```

> [!NOTE]
> Az alkalmazás törlése előtt törölnie kell az alkalmazás összes alkalmazáscsomag-verzióját. Ha olyan alkalmazást próbál meg törölni, amely jelenleg rendelkezik alkalmazáscsomaggal, „Ütközés” hibaüzenetet fog kapni.
> 

### <a name="deploy-an-application-package"></a>Alkalmazáscsomag üzembe helyezése

Készlet létrehozásakor megadhat egy vagy több alkalmazáscsomagot az üzembe helyezéshez. Ha a készlet létrehozásakor megad egy csomagot, az a csomópont készlethez való csatlakoztatásakor minden csomóponton üzembe lesz helyezve. A csomagok akkor is üzembe lesznek helyezve, ha a csomópontot újraindítják vagy alaphelyzetbe állítják.

Adja meg az `-ApplicationPackageReference` kapcsolót, ha készletet hoz létre egy alkalmazáscsomag üzembe helyezéséhez a készlet csomópontjain, amikor azok csatlakoznak a készlethez. Először hozzon létre egy **PSApplicationPackageReference**-objektumot, és konfigurálja a készlet számítási csomópontjain üzembe helyezendő alkalmazás azonosítóval és a csomagverzióval:

```PowerShell
$appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

$appPackageReference.ApplicationId = "MyBatchApplication"

$appPackageReference.Version = "1.0"
```

Ezután hozza létre a készletet, majd a csomag-referenciaobjektumot az `ApplicationPackageReferences` beállítás argumentumaként adja meg:

```PowerShell
New-AzureBatchPool -Id "PoolWithAppPackage" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -BatchContext $context -ApplicationPackageReferences $appPackageReference
```

Az alkalmazáscsomagok használatával kapcsolatban további információkat a [Batch-alkalmazáscsomagokkal számítási csomópontokra végzett alkalmazástelepítést](batch-application-packages.md) ismertető cikkben talál.

> [!IMPORTANT]
> Az alkalmazáscsomagok használatához [hozzá kell kapcsolnia egy Azure Storage-fiókot](#linked-storage-account-autostorage) a Batch-fiókjához.
> 
> 

### <a name="update-a-pools-application-packages"></a>Készlet alkalmazáscsomagjainak frissítése

A már létező készlethez rendelt alkalmazás frissítéséhez előbb hozzon létre PSApplicationPackageReference-objektumot a kívánt tulajdonságokkal (alkalmazásazonosító és csomagverzió):

```PowerShell
$appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

$appPackageReference.ApplicationId = "MyBatchApplication"

$appPackageReference.Version = "2.0"

```

Ezután olvassa be a készletet a Batch-ből, tisztítsa meg a már létező csomagoktól, adja hozzá az új csomagreferenciát, majd az új készletbeállításokkal frissítse a Batch-szolgáltatást:

```PowerShell
$pool = Get-AzureBatchPool -BatchContext $context -Id "PoolWithAppPackage"

$pool.ApplicationPackageReferences.Clear()

$pool.ApplicationPackageReferences.Add($appPackageReference)

Set-AzureBatchPool -BatchContext $context -Pool $pool
```

A készlet tulajdonságainak frissítése megtörtént a Batch-szolgáltatásban. Ha azonban a készlet számítási csomópontjain szeretné üzembe helyezni az új alkalmazáscsomagot, újra kell indítania vagy alaphelyzetbe kell állítania az adott csomópontok rendszerképét. Az alábbi paranccsal újraindíthatja a készlet összes számítási csomópontját:

```PowerShell
Get-AzureBatchComputeNode -PoolId "PoolWithAppPackage" -BatchContext $context | Restart-AzureBatchComputeNode -BatchContext $context
```

> [!TIP]
> A készlet számítási csomópontjain több alkalmazáscsomagot is üzembe helyezhet. Ha a jelenleg telepített csomagok cseréje helyett inkább *hozzáadna* egy alkalmazáscsomagot, hagyja ki a felső `$pool.ApplicationPackageReferences.Clear()` sort.
> 
> 

## <a name="next-steps"></a>További lépések

* A parancsmag részletes szintaxisáért és példákért lásd: [Azure Batch-parancsmagok referenciája](/powershell/module/azurerm.batch/#batch).
* A Batchben elérhető alkalmazásokkal és alkalmazáscsomagokkal kapcsolatban további információkat a [Batch-alkalmazáscsomagokkal számítási csomópontokra végzett alkalmazástelepítést](batch-application-packages.md) ismertető cikkben talál.

[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/

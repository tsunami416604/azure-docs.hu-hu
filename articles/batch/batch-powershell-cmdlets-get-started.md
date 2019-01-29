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
ms.date: 01/15/2019
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 3ceb7585a26290985cd5a2c523ad8094b1d1a40a
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55194236"
---
# <a name="manage-batch-resources-with-powershell-cmdlets"></a>Batch-erőforrások kezelése PowerShell-parancsmagokkal

Az Azure Batch PowerShell-parancsmagokkal a Batch API-k, az Azure Portal és az Azure parancssori felülete (CLI) használatával végrehajtott műveletek közül számosat elvégezhet vagy szkriptekbe foglalhat. Ez a cikk gyors ismertetést nyújt a Batch-fiókok felügyeletéhez és a Batch-erőforrásokkal, például készletekkel, feladatokkal és tevékenységekkel használható parancsmagokról.

A Batch-parancsmagok teljes listájáért és a parancsmagok részletes szintaxisáért lásd: [Azure Batch-parancsmagok referenciája](/powershell/module/az.batch).

Ez a cikk Az kötegelt modul 1.0.0-s parancsmagjain alapul. Ajánlott gyakran frissíteni az Azure PowerShell-modulokat a szolgáltatásfrissítések és -fejlesztések kihasználása érdekében.

## <a name="prerequisites"></a>Előfeltételek

* [Telepítse és konfigurálja az Azure PowerShell modul legújabb verzióját](/powershell/azure/overview). Egy adott Azure Batch-modul, például egy kiadás előtti modul telepítésével kapcsolatban lásd a [PowerShell-galériát](https://www.powershellgallery.com/packages/Az.Batch/1.0.0) ismertető cikket.

* Futtassa a **Connect-AzAccount** parancsmag használatával csatlakozzon az előfizetéséhez (az Azure Batch parancsmagok modulban az Azure Resource Manager):

  ```PowerShell
  Connect-AzAccount
  ```

* **Regisztráljon a Batch-szolgáltató névterével**. Ezt a műveletet **előfizetésenként csak egyszer** kell elvégezni.
  
  ```PowerShell
  Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
  ```

## <a name="manage-batch-accounts-and-keys"></a>Batch-fiókok és kulcsok felügyelete

### <a name="create-a-batch-account"></a>Batch-fiók létrehozása

**Új AzBatchAccount** létrehoz egy Batch-fiókot a meghatározott erőforráscsoportban. Ha még nem rendelkezik egy erőforráscsoportot, hozzon létre egyet futtatásával a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancsmagot. A **Hely** paraméternél adjon meg egy Azure régiót, például az „USA középső régiója”. Példa:

```PowerShell
New-AzResourceGroup –Name MyBatchResourceGroup –Location "Central US"
```

Ezután hozzon létre egy Batch-fiókot az erőforráscsoportban. Adjon meg egy nevet a fiókjához <*account_name*>, a hely, az erőforráscsoport nevét. A Batch-fiókok létrehozása eltarthat egy ideig. Példa:

```PowerShell
New-AzBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName <res_group_name>
```

> [!NOTE]
> A Batch-fiók nevének egyedinek kell lennie az erőforráscsoport Azure-régiójában, 3–24 karaktert kell tartalmaznia, és csak kisbetűkből és számokból állhat.

### <a name="get-account-access-keys"></a>Fiók hívóbetűinek beszerzése

**Get-AzBatchAccountKeys** bemutatja egy Azure Batch-fiókhoz társított hozzáférési kulcsait. Például a következő futtatásával lekérheti a létrehozott fiók elsődleges és másodlagos hívóbetűit.

 ```PowerShell
$Account = Get-AzBatchAccountKeys –AccountName <account_name>

$Account.PrimaryAccountKey

$Account.SecondaryAccountKey
```

### <a name="generate-a-new-access-key"></a>Új hívóbetű létrehozása

**Új AzBatchAccountKey** új kulcsot generál egy elsődleges vagy másodlagos fiókot egy Azure Batch-fiókhoz. Ha például új elsődleges hívóbetűt szeretne létrehozni a Batch-fiókhoz, írja be a következőt:

```PowerShell
New-AzBatchAccountKey -AccountName <account_name> -KeyType Primary
```

> [!NOTE]
> Új másodlagos hívóbetű létrehozásához adja meg a „Másodlagos” szöveget a **KeyType** paraméterhez. Az elsődleges és a másodlagos hívóbetűket külön kell újra létrehoznia.

### <a name="delete-a-batch-account"></a>Batch-fiók törlése

**Remove-AzBatchAccount** törli a Batch-fiókot. Példa:

```PowerShell
Remove-AzBatchAccount -AccountName <account_name>
```

A felugró kérdésre erősítse meg, hogy valóban el kívánja távolítani a fiókot. A fiókeltávolítás eltarthat egy ideig.

## <a name="create-a-batchaccountcontext-object"></a>BatchAccountContext objektum létrehozása

A Batch-erőforrások kezeléséhez szükséges hitelesítést elvégezheti megosztott kulcsos hitelesítéssel vagy Azure Active Directory-hitelesítéssel. A Batch PowerShell-parancsmagokkal végzett hitelesítéshez először hozzon létre egy BatchAccountContext objektumot, amely a fiók hitelesítő adatait vagy az identitást tárolja. A BatchAccountContext objektumot a **BatchContext** paramétert használó parancsmagokba adja át.

### <a name="shared-key-authentication"></a>Megosztott kulcsos hitelesítés

```PowerShell
$context = Get-AzBatchAccountKeys -AccountName <account_name>
```

> [!NOTE]
> Alapértelmezés szerint a fiók elsődleges hívóbetűjét használja a hitelesítéshez, de explicit módon kiválaszthatja a használni kívánt betűt, ha módosítja a BatchAccountContext objektum **KeyInUse** tulajdonságát: `$context.KeyInUse = "Secondary"`.

### <a name="azure-active-directory-authentication"></a>Hitelesítés Azure Active Directory-fiókkal

```PowerShell
$context = Get-AzBatchAccount -AccountName <account_name>
```

## <a name="create-and-modify-batch-resources"></a>Batch-erőforrások létrehozása és módosítása

Használja például a parancsmagokat **New-AzBatchPool**, **New-AzBatchJob**, és **New-AzBatchTask** erőforrásokat a Batch-fiók létrehozásához. Megfelelő **Get-** és **Set-** parancsmagokkal frissítheti a meglévő erőforrások tulajdonságait, és a **Remove-** parancsmagokkal távolíthatja el az erőforrásokat a Batch-fiókokról.

Sok parancsmag használatakor egy BatchContext objektum átadása mellett részletes erőforrás-beállításokat tartalmazó objektumok létrehozására vagy átadására is szükség van, ahogyan az a következő példában látható. További példákat az egyes parancsmagok részletes súgójában talál.

### <a name="create-a-batch-pool"></a>Batch-készlet létrehozása

Batch-készlet létrehozásakor vagy frissítésekor kiválaszthatja a felhőszolgáltatás- vagy a virtuálisgép-konfigurációt az operációs rendszerhez a számítási csomópontokon (lásd a [Batch-funkciók áttekintésével](batch-api-basics.md#pool) kapcsolatos témakört). Ha a felhőszolgáltatás-konfigurációt adja meg, a számítási csomópontokról az egyik [Azure-beli vendég operációs rendszer kiadásával](../cloud-services/cloud-services-guestos-update-matrix.md#releases) készül rendszerkép. Ha a virtuálisgép-konfigurációt választja, megadhatja az [Azure Virtual Machines Marketplace-en][vm_marketplace] felsorolt támogatott Linux vagy Windows virtuálisgép-rendszerképek egyikét, vagy megadhat egy Ön által előkészített egyéni rendszerképet.

Futtatásakor **New-AzBatchPool**, adja át a PSCloudServiceConfiguration vagy PSVirtualMachineConfiguration objektumban található operációsrendszer-beállításokat. Például az alábbi kódrészlet létrehoz egy Batch Standard_A1 méretű készlet számítási csomópontjain a virtuális gép konfigurációja, az Ubuntu Server 18.04-LTS rendszerképe. Itt a **VirtualMachineConfiguration** paraméter a *$configuration* változót a PSVirtualMachineConfiguration objektumként határozza meg. A **BatchContext** paraméter egy korábban meghatározott *$context* változót ad meg a BatchAccountContext objektumként.

```PowerShell
$imageRef = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSImageReference" -ArgumentList @("UbuntuServer","Canonical","18.04.0-LTS")

$configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSVirtualMachineConfiguration" -ArgumentList @($imageRef, "batch.node.ubuntu 18.04")

New-AzBatchPool -Id "mypspool" -VirtualMachineSize "Standard_a1" -VirtualMachineConfiguration $configuration -AutoScaleFormula '$TargetDedicated=4;' -BatchContext $context
```

Az új készletben lévő számítási csomópontok célszámát egy automatikus skálázási képletet számolható ki. Ebben az esetben a képlet egyszerűen a **$TargetDedicated=4**, ami jelzi, hogy a készletben lévő számítási csomópontok száma legfeljebb 4.

## <a name="query-for-pools-jobs-tasks-and-other-details"></a>Készletek, feladatok, tevékenységek és egyéb részletek lekérdezése

Használja például a parancsmagokat **Get-AzBatchPool**, **Get-AzBatchJob**, és **Get-AzBatchTask** lekérdezéshez létrehozott Batch-fiók entitásokat.

### <a name="query-for-data"></a>Adatok lekérdezése

Tegyük fel, használja a **Get-AzBatchPools** használatával megkeresheti a készleteket. Ez alapértelmezés szerint a fiók összes készletét lekérdezi, ha a BatchAccountContext objektumot már tárolta a *$context* helyen:

```PowerShell
Get-AzBatchPool -BatchContext $context
```

### <a name="use-an-odata-filter"></a>OData-szűrő használata

Megadhat egy OData-szűrőt a **Filter** paraméterrel, ha csak a kívánt objektumokat szeretné megkeresni. Például található összes azonosítókkal rendelkező "myPool" kezdve:

```PowerShell
$filter = "startswith(id,'myPool')"

Get-AzBatchPool -Filter $filter -BatchContext $context
```

Ez a módszer nem olyan rugalmas, mint a „Where-Object” használata a helyi folyamatokban. A lekérdezést azonban közvetlenül a Batch szolgáltatásnak küldi a rendszer, hogy minden szűrés a kiszolgálói oldalon történjen, így internetes sávszélesség takarítható meg.

### <a name="use-the-id-parameter"></a>Az Id paraméter használata

Az OData-szűrő alternatívája az **Id** paraméter használata. Egy „myPool” azonosítójú adott készlet lekérdezése:

```PowerShell
Get-AzBatchPool -Id "myPool" -BatchContext $context
```

A **azonosító** paraméter csak a teljes Azonosítót keresés; nem helyettesítő karaktereket vagy OData stílusú szűrőket támogatja.

### <a name="use-the-maxcount-parameter"></a>A MaxCount paraméter használata

Alapértelmezés szerint mindegyik parancsmag maximum 1000 objektumot ad vissza. Ha eléri ezt a korlátot, pontosítsa a szűrőt, hogy kevesebb objektumot adjon vissza, vagy explicit módon állítson be maximális értéket a **MaxCount** paraméterrel. Példa:

```PowerShell
Get-AzBatchTask -MaxCount 2500 -BatchContext $context
```

A felső határ eltávolításához 0 vagy kisebb értéket adjon meg a **MaxCount** paraméternek.

### <a name="use-the-powershell-pipeline"></a>A PowerShell-folyamat használata

A Batch-parancsmagok a PowerShell-adatcsatorna használatával parancsmagok közötti adatküldéshez. Ennek ugyanaz a hatása, mint a paraméterek megadása, de megkönnyíti a több entitással való munkát.

Például keresse meg és jelenítse meg fiókjában az összes feladatot:

```PowerShell
Get-AzBatchJob -BatchContext $context | Get-AzBatchTask -BatchContext $context
```

Indítsa újra a készlet összes számítási csomópontját:

```PowerShell
Get-AzBatchComputeNode -PoolId "myPool" -BatchContext $context | Restart-AzBatchComputeNode -BatchContext $context
```

## <a name="application-package-management"></a>Alkalmazáscsomagok kezelése

Az alkalmazáscsomagok egyszerű módot kínálnak az alkalmazások üzembe helyezésére a készletek számítási csomópontjain. A Batch PowerShell-parancsmagjaival feltöltheti és kezelheti az alkalmazáscsomagokat a Batch-fiókban, valamint a számítási csomópontokban üzembe helyezheti a csomagverziókat.

Alkalmazás **létrehozása**:

```PowerShell
New-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"
```

Alkalmazáscsomag **hozzáadása**:

```PowerShell
New-AzBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0" -Format zip -FilePath package001.zip
```

Állítsa be az alkalmazás **alapértelmezett verzióját** :

```PowerShell
Set-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -DefaultVersion "1.0"
```

Alkalmazás csomagjainak **listázása**

```PowerShell
$application = Get-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

$application.ApplicationPackages
```

Alkalmazáscsomag **törlése**

```PowerShell
Remove-AzBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0"
```

Alkalmazás **törlése**

```PowerShell
Remove-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"
```

> [!NOTE]
> Az alkalmazás törlése előtt törölnie kell az alkalmazás összes alkalmazáscsomag-verzióját. Ha olyan alkalmazást próbál meg törölni, amely jelenleg rendelkezik alkalmazáscsomaggal, „Ütközés” hibaüzenetet fog kapni.

### <a name="deploy-an-application-package"></a>Alkalmazáscsomag üzembe helyezése

Készlet létrehozásakor megadhat egy vagy több alkalmazáscsomagot az üzembe helyezéshez. Ha a készlet létrehozásakor megad egy csomagot, az a csomópont készlethez való csatlakoztatásakor minden csomóponton üzembe lesz helyezve. A csomagok akkor is üzembe lesznek helyezve, ha a csomópontot újraindítják vagy alaphelyzetbe állítják.

Adja meg az `-ApplicationPackageReference` kapcsolót, ha készletet hoz létre egy alkalmazáscsomag üzembe helyezéséhez a készlet csomópontjain, amikor azok csatlakoznak a készlethez. Először hozzon létre egy **PSApplicationPackageReference** objektumot, és konfigurálja azt a, a készlet számítási csomópontjain telepítendő alkalmazás Alkalmazásazonosító és Csomagverzió verziója:

```PowerShell
$appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

$appPackageReference.ApplicationId = "MyBatchApplication"

$appPackageReference.Version = "1.0"
```

Ezután hozza létre a készletet, majd a csomag-referenciaobjektumot az `ApplicationPackageReferences` beállítás argumentumaként adja meg:

```PowerShell
New-AzBatchPool -Id "PoolWithAppPackage" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -BatchContext $context -ApplicationPackageReferences $appPackageReference
```

Az alkalmazáscsomagok használatával kapcsolatban további információkat a [Batch-alkalmazáscsomagokkal számítási csomópontokra végzett alkalmazástelepítést](batch-application-packages.md) ismertető cikkben talál.

> [!IMPORTANT]
> Az alkalmazáscsomagok használatához [hozzá kell kapcsolnia egy Azure Storage-fiókot](#linked-storage-account-autostorage) a Batch-fiókjához.

### <a name="update-a-pools-application-packages"></a>Készlet alkalmazáscsomagjainak frissítése

Egy meglévő készlethez rendelt alkalmazások frissítéséhez, először hozzon létre PSApplicationPackageReference-objektumot a kívánt tulajdonságokkal (Alkalmazásazonosító és Csomagverzió verziója):

```PowerShell
$appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

$appPackageReference.ApplicationId = "MyBatchApplication"

$appPackageReference.Version = "2.0"

```

Ezután olvassa be a készletet a Batch-ből, tisztítsa meg a már létező csomagoktól, adja hozzá az új csomagreferenciát, majd az új készletbeállításokkal frissítse a Batch-szolgáltatást:

```PowerShell
$pool = Get-AzBatchPool -BatchContext $context -Id "PoolWithAppPackage"

$pool.ApplicationPackageReferences.Clear()

$pool.ApplicationPackageReferences.Add($appPackageReference)

Set-AzBatchPool -BatchContext $context -Pool $pool
```

A készlet tulajdonságainak frissítése megtörtént a Batch-szolgáltatásban. Ha azonban a készlet számítási csomópontjain szeretné üzembe helyezni az új alkalmazáscsomagot, újra kell indítania vagy alaphelyzetbe kell állítania az adott csomópontok rendszerképét. Az alábbi paranccsal újraindíthatja a készlet összes számítási csomópontját:

```PowerShell
Get-AzBatchComputeNode -PoolId "PoolWithAppPackage" -BatchContext $context | Restart-AzBatchComputeNode -BatchContext $context
```

> [!TIP]
> A készlet számítási csomópontjain több alkalmazáscsomagot is üzembe helyezhet. Ha a jelenleg telepített csomagok cseréje helyett inkább *hozzáadna* egy alkalmazáscsomagot, hagyja ki a felső `$pool.ApplicationPackageReferences.Clear()` sort.

## <a name="next-steps"></a>További lépések

* A parancsmag részletes szintaxisáért és példákért lásd: [Azure Batch-parancsmagok referenciája](/powershell/module/az.batch).
* A Batchben elérhető alkalmazásokkal és alkalmazáscsomagokkal kapcsolatban további információkat a [Batch-alkalmazáscsomagokkal számítási csomópontokra végzett alkalmazástelepítést](batch-application-packages.md) ismertető cikkben talál.

[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
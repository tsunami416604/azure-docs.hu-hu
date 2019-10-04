---
title: Ismerkedés a PowerShell-Azure Batchokkal | Microsoft Docs
description: Gyors bevezetés a Batch-erőforrások kezeléséhez használható Azure PowerShell-parancsmagok használatába.
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: powershell
ms.workload: big-compute
ms.date: 01/15/2019
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 21930d5240225540159fa425d9d9fa518a1b19d5
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323083"
---
# <a name="manage-batch-resources-with-powershell-cmdlets"></a>Batch-erőforrások kezelése PowerShell-parancsmagokkal

Az Azure Batch PowerShell-parancsmagokkal a Batch API-k, az Azure Portal és az Azure parancssori felülete (CLI) használatával végrehajtott műveletek közül számosat elvégezhet vagy szkriptekbe foglalhat. Ez a cikk gyors ismertetést nyújt a Batch-fiókok felügyeletéhez és a Batch-erőforrásokkal, például készletekkel, feladatokkal és tevékenységekkel használható parancsmagokról.

A Batch-parancsmagok teljes listájáért és a parancsmagok részletes szintaxisáért lásd: [Azure Batch-parancsmagok referenciája](/powershell/module/az.batch).

Ez a cikk parancsmagokon alapul az az batch modul 1.0.0-ban. Ajánlott gyakran frissíteni az Azure PowerShell-modulokat a szolgáltatásfrissítések és -fejlesztések kihasználása érdekében.

## <a name="prerequisites"></a>Előfeltételek

* [Telepítse és konfigurálja az Azure PowerShell modul legújabb verzióját](/powershell/azure/overview). Egy adott Azure Batch-modul, például egy kiadás előtti modul telepítésével kapcsolatban lásd a [PowerShell-galériát](https://www.powershellgallery.com/packages/Az.Batch/1.0.0) ismertető cikket.

* Futtassa a **AzAccount** parancsmagot az előfizetéshez való kapcsolódáshoz (a Azure batch parancsmagok a Azure Resource Manager modulban):

  ```powershell
  Connect-AzAccount
  ```

* **Regisztráljon a Batch-szolgáltató névterével**. Ezt a műveletet **előfizetésenként csak egyszer** kell elvégezni.
  
  ```powershell
  Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
  ```

## <a name="manage-batch-accounts-and-keys"></a>Batch-fiókok és kulcsok felügyelete

### <a name="create-a-batch-account"></a>Batch-fiók létrehozása

A **New-AzBatchAccount** létrehoz egy batch-fiókot egy adott erőforráscsoporthoz. Ha még nem rendelkezik erőforráscsoporthoz, hozzon létre egyet a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancsmag futtatásával. A **Hely** paraméternél adjon meg egy Azure régiót, például az „USA középső régiója”. Példa:

```powershell
New-AzResourceGroup –Name MyBatchResourceGroup –Location "Central US"
```

Ezután hozzon létre egy batch-fiókot az erőforráscsoporthoz. Adja meg a fiók nevét <*account_name*>, valamint az erőforráscsoport helyét és nevét. A Batch-fiókok létrehozása eltarthat egy ideig. Példa:

```powershell
New-AzBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName <res_group_name>
```

> [!NOTE]
> A Batch-fiók nevének egyedinek kell lennie az erőforráscsoport Azure-régiójában, 3–24 karaktert kell tartalmaznia, és csak kisbetűkből és számokból állhat.

### <a name="get-account-access-keys"></a>Fiók hívóbetűinek beszerzése

A **Get-AzBatchAccountKeys** megjeleníti az Azure batch-fiókhoz társított hozzáférési kulcsokat. Például a következő futtatásával lekérheti a létrehozott fiók elsődleges és másodlagos hívóbetűit.

 ```powershell
$Account = Get-AzBatchAccountKeys –AccountName <account_name>

$Account.PrimaryAccountKey

$Account.SecondaryAccountKey
```

### <a name="generate-a-new-access-key"></a>Új hívóbetű létrehozása

A **New-AzBatchAccountKey** új elsődleges vagy másodlagos fiók kulcsot hoz létre egy Azure batch-fiókhoz. Ha például új elsődleges hívóbetűt szeretne létrehozni a Batch-fiókhoz, írja be a következőt:

```powershell
New-AzBatchAccountKey -AccountName <account_name> -KeyType Primary
```

> [!NOTE]
> Új másodlagos hívóbetű létrehozásához adja meg a „Másodlagos” szöveget a **KeyType** paraméterhez. Az elsődleges és a másodlagos hívóbetűket külön kell újra létrehoznia.

### <a name="delete-a-batch-account"></a>Batch-fiók törlése

A **Remove-AzBatchAccount** törli a Batch-fiókot. Példa:

```powershell
Remove-AzBatchAccount -AccountName <account_name>
```

A felugró kérdésre erősítse meg, hogy valóban el kívánja távolítani a fiókot. A fiókeltávolítás eltarthat egy ideig.

## <a name="create-a-batchaccountcontext-object"></a>BatchAccountContext objektum létrehozása

A Batch-erőforrások kezeléséhez szükséges hitelesítést elvégezheti megosztott kulcsos hitelesítéssel vagy Azure Active Directory-hitelesítéssel. A Batch PowerShell-parancsmagokkal végzett hitelesítéshez először hozzon létre egy BatchAccountContext objektumot, amely a fiók hitelesítő adatait vagy az identitást tárolja. A BatchAccountContext objektumot a **BatchContext** paramétert használó parancsmagokba adja át.

### <a name="shared-key-authentication"></a>Megosztott kulcsos hitelesítés

```powershell
$context = Get-AzBatchAccountKeys -AccountName <account_name>
```

> [!NOTE]
> Alapértelmezés szerint a fiók elsődleges hívóbetűjét használja a hitelesítéshez, de explicit módon kiválaszthatja a használni kívánt betűt, ha módosítja a BatchAccountContext objektum **KeyInUse** tulajdonságát: `$context.KeyInUse = "Secondary"`.

### <a name="azure-active-directory-authentication"></a>Hitelesítés Azure Active Directory-fiókkal

```powershell
$context = Get-AzBatchAccount -AccountName <account_name>
```

## <a name="create-and-modify-batch-resources"></a>Batch-erőforrások létrehozása és módosítása

Használjon olyan parancsmagokat, mint a **New-AzBatchPool**, a **New-AzBatchJob**és a **New-AzBatchTask** , és hozzon létre erőforrásokat a Batch-fiókokban. Megfelelő **Get-** és **Set-** parancsmagokkal frissítheti a meglévő erőforrások tulajdonságait, és a **Remove-** parancsmagokkal távolíthatja el az erőforrásokat a Batch-fiókokról.

Sok parancsmag használatakor egy BatchContext objektum átadása mellett részletes erőforrás-beállításokat tartalmazó objektumok létrehozására vagy átadására is szükség van, ahogyan az a következő példában látható. További példákat az egyes parancsmagok részletes súgójában talál.

### <a name="create-a-batch-pool"></a>Batch-készlet létrehozása

Batch-készlet létrehozásakor vagy frissítésekor kiválaszthatja a felhőszolgáltatás- vagy a virtuálisgép-konfigurációt az operációs rendszerhez a számítási csomópontokon (lásd a [Batch-funkciók áttekintésével](batch-api-basics.md#pool) kapcsolatos témakört). Ha a felhőszolgáltatás-konfigurációt adja meg, a számítási csomópontokról az egyik [Azure-beli vendég operációs rendszer kiadásával](../cloud-services/cloud-services-guestos-update-matrix.md#releases) készül rendszerkép. Ha megadja a virtuális gép konfigurációját, megadhatja az [Azure Virtual Machines piactéren][vm_marketplace]felsorolt támogatott Linux vagy Windows rendszerű virtuálisgép-rendszerképek egyikét, vagy megadhat egy előkészített egyéni rendszerképet.

A **New-AzBatchPool**futtatásakor adja meg az operációs rendszer beállításait egy PSCloudServiceConfiguration vagy PSVirtualMachineConfiguration objektumban. Az alábbi kódrészlet például egy Standard_A1 számítási csomópontokkal rendelkező batch-készletet hoz létre a virtuálisgép-konfigurációban, amely az Ubuntu Server 18,04-LTS formátummal van ellátva. Itt a **VirtualMachineConfiguration** paraméter a *$configuration* változót a PSVirtualMachineConfiguration objektumként határozza meg. A **BatchContext** paraméter egy korábban meghatározott *$context* változót ad meg a BatchAccountContext objektumként.

```powershell
$imageRef = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSImageReference" -ArgumentList @("UbuntuServer","Canonical","18.04-LTS")

$configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSVirtualMachineConfiguration" -ArgumentList @($imageRef, "batch.node.ubuntu 18.04")

New-AzBatchPool -Id "mypspool" -VirtualMachineSize "Standard_a1" -VirtualMachineConfiguration $configuration -AutoScaleFormula '$TargetDedicated=4;' -BatchContext $context
```

Az új készletben lévő számítási csomópontok megcélzott számát egy automatikus skálázási képlet számítja ki. Ebben az esetben a képlet egyszerűen a **$TargetDedicated=4**, ami jelzi, hogy a készletben lévő számítási csomópontok száma legfeljebb 4.

## <a name="query-for-pools-jobs-tasks-and-other-details"></a>Készletek, feladatok, tevékenységek és egyéb részletek lekérdezése

Használjon olyan parancsmagokat, mint például a **Get-AzBatchPool**, a **Get-AzBatchJob**és a **Get-AzBatchTask** a Batch-fiókban létrehozott entitások lekérdezéséhez.

### <a name="query-for-data"></a>Adatok lekérdezése

Például a **Get-AzBatchPools** használatával megkeresheti a készleteket. Ez alapértelmezés szerint a fiók összes készletét lekérdezi, ha a BatchAccountContext objektumot már tárolta a *$context* helyen:

```powershell
Get-AzBatchPool -BatchContext $context
```

### <a name="use-an-odata-filter"></a>OData-szűrő használata

Megadhat egy OData-szűrőt a **Filter** paraméterrel, ha csak a kívánt objektumokat szeretné megkeresni. Megkeresheti például az összes olyan készletet, amelynek azonosítói a "myPool" értékkel kezdődnek:

```powershell
$filter = "startswith(id,'myPool')"

Get-AzBatchPool -Filter $filter -BatchContext $context
```

Ez a módszer nem olyan rugalmas, mint a „Where-Object” használata a helyi folyamatokban. A lekérdezést azonban közvetlenül a Batch szolgáltatásnak küldi a rendszer, hogy minden szűrés a kiszolgálói oldalon történjen, így internetes sávszélesség takarítható meg.

### <a name="use-the-id-parameter"></a>Az Id paraméter használata

Az OData-szűrő alternatívája az **Id** paraméter használata. Egy „myPool” azonosítójú adott készlet lekérdezése:

```powershell
Get-AzBatchPool -Id "myPool" -BatchContext $context
```

Az **ID** paraméter csak a teljes azonosítót támogató kereséseket támogatja. nem helyettesítő karakter vagy OData típusú szűrő.

### <a name="use-the-maxcount-parameter"></a>A MaxCount paraméter használata

Alapértelmezés szerint mindegyik parancsmag maximum 1000 objektumot ad vissza. Ha eléri ezt a korlátot, pontosítsa a szűrőt, hogy kevesebb objektumot adjon vissza, vagy explicit módon állítson be maximális értéket a **MaxCount** paraméterrel. Példa:

```powershell
Get-AzBatchTask -MaxCount 2500 -BatchContext $context
```

A felső határ eltávolításához 0 vagy kisebb értéket adjon meg a **MaxCount** paraméternek.

### <a name="use-the-powershell-pipeline"></a>A PowerShell-folyamat használata

A Batch-parancsmagok a PowerShell-folyamat használatával küldik el az adatparancsmagok közötti adatküldést. Ennek ugyanaz a hatása, mint a paraméterek megadása, de megkönnyíti a több entitással való munkát.

Például keresse meg és jelenítse meg fiókjában az összes feladatot:

```powershell
Get-AzBatchJob -BatchContext $context | Get-AzBatchTask -BatchContext $context
```

Indítsa újra a készlet összes számítási csomópontját:

```powershell
Get-AzBatchComputeNode -PoolId "myPool" -BatchContext $context | Restart-AzBatchComputeNode -BatchContext $context
```

## <a name="application-package-management"></a>Alkalmazáscsomagok kezelése

Az alkalmazáscsomagok egyszerű módot kínálnak az alkalmazások üzembe helyezésére a készletek számítási csomópontjain. A Batch PowerShell-parancsmagjaival feltöltheti és kezelheti az alkalmazáscsomagokat a Batch-fiókban, valamint a számítási csomópontokban üzembe helyezheti a csomagverziókat.

Alkalmazás **létrehozása**:

```powershell
New-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"
```

Alkalmazáscsomag **hozzáadása**:

```powershell
New-AzBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0" -Format zip -FilePath package001.zip
```

Állítsa be az alkalmazás **alapértelmezett verzióját** :

```powershell
Set-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -DefaultVersion "1.0"
```

Alkalmazás csomagjainak **listázása**

```powershell
$application = Get-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

$application.ApplicationPackages
```

Alkalmazáscsomag **törlése**

```powershell
Remove-AzBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0"
```

Alkalmazás **törlése**

```powershell
Remove-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"
```

> [!NOTE]
> Az alkalmazás törlése előtt törölnie kell az alkalmazás összes alkalmazáscsomag-verzióját. Ha olyan alkalmazást próbál meg törölni, amely jelenleg rendelkezik alkalmazáscsomaggal, „Ütközés” hibaüzenetet fog kapni.

### <a name="deploy-an-application-package"></a>Alkalmazáscsomag üzembe helyezése

Készlet létrehozásakor megadhat egy vagy több alkalmazáscsomagot az üzembe helyezéshez. Ha a készlet létrehozásakor megad egy csomagot, az a csomópont készlethez való csatlakoztatásakor minden csomóponton üzembe lesz helyezve. A csomagok akkor is üzembe lesznek helyezve, ha a csomópontot újraindítják vagy alaphelyzetbe állítják.

Adja meg az `-ApplicationPackageReference` kapcsolót, ha készletet hoz létre egy alkalmazáscsomag üzembe helyezéséhez a készlet csomópontjain, amikor azok csatlakoznak a készlethez. Először hozzon létre egy **PSApplicationPackageReference** objektumot, és konfigurálja a készlet számítási csomópontjain telepíteni kívánt alkalmazás-azonosítóval és csomag-verzióval:

```powershell
$appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

$appPackageReference.ApplicationId = "MyBatchApplication"

$appPackageReference.Version = "1.0"
```

Ezután hozza létre a készletet, majd a csomag-referenciaobjektumot az `ApplicationPackageReferences` beállítás argumentumaként adja meg:

```powershell
New-AzBatchPool -Id "PoolWithAppPackage" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -BatchContext $context -ApplicationPackageReferences $appPackageReference
```

Az alkalmazáscsomagok használatával kapcsolatban további információkat a [Batch-alkalmazáscsomagokkal számítási csomópontokra végzett alkalmazástelepítést](batch-application-packages.md) ismertető cikkben talál.

> [!IMPORTANT]
> Alkalmazáscsomag használatához egy Azure Storage-fiókot kell összekapcsolnia a Batch-fiókkal.

### <a name="update-a-pools-application-packages"></a>Készlet alkalmazáscsomagjainak frissítése

Egy meglévő készlethez rendelt alkalmazások frissítéséhez először hozzon létre egy PSApplicationPackageReference-objektumot a kívánt tulajdonságokkal (alkalmazás-azonosító és csomag verziója):

```powershell
$appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

$appPackageReference.ApplicationId = "MyBatchApplication"

$appPackageReference.Version = "2.0"

```

Ezután olvassa be a készletet a Batch-ből, tisztítsa meg a már létező csomagoktól, adja hozzá az új csomagreferenciát, majd az új készletbeállításokkal frissítse a Batch-szolgáltatást:

```powershell
$pool = Get-AzBatchPool -BatchContext $context -Id "PoolWithAppPackage"

$pool.ApplicationPackageReferences.Clear()

$pool.ApplicationPackageReferences.Add($appPackageReference)

Set-AzBatchPool -BatchContext $context -Pool $pool
```

A készlet tulajdonságainak frissítése megtörtént a Batch-szolgáltatásban. Ha azonban a készlet számítási csomópontjain szeretné üzembe helyezni az új alkalmazáscsomagot, újra kell indítania vagy alaphelyzetbe kell állítania az adott csomópontok rendszerképét. Az alábbi paranccsal újraindíthatja a készlet összes számítási csomópontját:

```powershell
Get-AzBatchComputeNode -PoolId "PoolWithAppPackage" -BatchContext $context | Restart-AzBatchComputeNode -BatchContext $context
```

> [!TIP]
> A készlet számítási csomópontjain több alkalmazáscsomagot is üzembe helyezhet. Ha a jelenleg telepített csomagok cseréje helyett inkább *hozzáadna* egy alkalmazáscsomagot, hagyja ki a felső `$pool.ApplicationPackageReferences.Clear()` sort.

## <a name="next-steps"></a>További lépések

* A parancsmag részletes szintaxisáért és példákért lásd: [Azure Batch-parancsmagok referenciája](/powershell/module/az.batch).
* A Batchben elérhető alkalmazásokkal és alkalmazáscsomagokkal kapcsolatban további információkat a [Batch-alkalmazáscsomagokkal számítási csomópontokra végzett alkalmazástelepítést](batch-application-packages.md) ismertető cikkben talál.

[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/

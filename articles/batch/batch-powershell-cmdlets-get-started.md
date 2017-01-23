---
title: "Az Azure Batch PowerShell használatának első lépései | Microsoft Docs"
description: "Gyors bevezetést olvashat az Azure Batch szolgáltatáshoz használható Azure PowerShell-parancsmagokról"
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: 
ms.assetid: f9ad62c5-27bf-4e6b-a5bf-c5f5914e6199
ms.service: batch
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: powershell
ms.workload: big-compute
ms.date: 01/20/2017
ms.author: tamram
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 1ea51f4053ba0d4e08628d29eca7cb7c82a26d61


---
# <a name="get-started-with-azure-batch-powershell-cmdlets"></a>Az Azure Batch PowerShell-parancsmagok használatának első lépései
Az Azure Batch PowerShell-parancsmagokkal a Batch API-k, az Azure Portal és az Azure parancssori felület (CLI) használatával végrehajtott műveletek közül sokat elvégezhet. Ez a cikk gyors ismertetést nyújt a Batch-fiókok felügyeletéhez és a Batch-erőforrásokkal, például készletekkel, feladatokkal és tevékenységekkel használható parancsmagokról.

A Batch-parancsmagok teljes listájáért és a parancsmagok részletes szintaxisáért lásd: [Azure Batch-parancsmagok referenciája](https://msdn.microsoft.com/library/azure/mt125957.aspx).

Ez a cikk az Azure PowerShell 3.0.0-s verziójának parancsmagjain alapul. Ajánlott gyakran frissíteni az Azure PowerShellt a szolgáltatásfrissítések és -fejlesztések kihasználása érdekében.

## <a name="prerequisites"></a>Előfeltételek
Az alábbi műveletek segítségével felügyelheti az Azure PowerShell-lel a Batch-erőforrásokat.

* [Telepítse és konfigurálja az Azure PowerShellt](/powershell/azureps-cmdlets-docs)
* Futtassa a **Login-AzureRmAccount** parancsmagot, hogy csatlakozzon az előfizetéséhez (az Azure Batch-parancsmagok az Azure Resource Manager modulban találhatók):
  
    `Login-AzureRmAccount`
* **Regisztráljon a Batch-szolgáltató névterével**. Ezt a műveletet **előfizetésenként csak egyszer** kell elvégezni.
  
    `Register-AzureRMResourceProvider -ProviderNamespace Microsoft.Batch`

## <a name="manage-batch-accounts-and-keys"></a>Batch-fiókok és kulcsok felügyelete
### <a name="create-a-batch-account"></a>Batch-fiók létrehozása
A **New-AzureRmBatchAccount** parancs egy Batch-fiókot hoz létre a meghatározott erőforráscsoportban. Ha még nem rendelkezik erőforráscsoporttal, hozzon létre egyet a [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/azure/mt603739.aspx) parancsmag futtatásával. A **Hely** paraméternél adjon meg egy Azure régiót, például az „USA középső régiója”. Példa:

    New-AzureRmResourceGroup –Name MyBatchResourceGroup –location "Central US"

Ezután hozzon létre egy Batch-fiókot az erőforráscsoportban, adjon meg egy fióknevet az <*account_name*> helyőrző helyett, és adja meg az erőforráscsoport helyét és nevét. A Batch-fiókok létrehozása eltarthat egy ideig. Példa:

    New-AzureRmBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName <res_group_name>

> [!NOTE]
> A Batch-fiók nevének egyedinek kell lennie az erőforráscsoport Azure-régiójában, 3–24 karaktert kell tartalmaznia, és csak kisbetűkből és számokból állhat.
> 
> 

### <a name="get-account-access-keys"></a>Fiók hívóbetűinek beszerzése
A **Get-AzureRmBatchAccountKeys** megjeleníti az Azure Batch-fiókkal társított hívóbetűket. Például a következő futtatásával lekérheti a létrehozott fiók elsődleges és másodlagos hívóbetűit.

    $Account = Get-AzureRmBatchAccountKeys –AccountName <account_name>

    $Account.PrimaryAccountKey

    $Account.SecondaryAccountKey

### <a name="generate-a-new-access-key"></a>Új hívóbetű létrehozása
A **New-AzureRmBatchAccountKey** új elsődleges vagy másodlagos hívóbetűt hoz létre az Azure Batch-fiókhoz. Ha például új elsődleges hívóbetűt szeretne létrehozni a Batch-fiókhoz, írja be a következőt:

    New-AzureRmBatchAccountKey -AccountName <account_name> -KeyType Primary

> [!NOTE]
> Új másodlagos hívóbetű létrehozásához adja meg a „Másodlagos” szöveget a **KeyType** paraméterhez. Az elsődleges és a másodlagos hívóbetűket külön kell újra létrehoznia.
> 
> 

### <a name="delete-a-batch-account"></a>Batch-fiók törlése
A **Remove-AzureRmBatchAccount** törli a Batch-fiókot. Példa:

    Remove-AzureRmBatchAccount -AccountName <account_name>

A felugró kérdésre erősítse meg, hogy valóban el kívánja távolítani a fiókot. A fiókeltávolítás eltarthat egy ideig.

## <a name="create-a-batchaccountcontext-object"></a>BatchAccountContext objektum létrehozása
Ha a Batch PowerShell-parancsmagokkal szeretne végrehajtani a hitelesítést Batch-készletek, -feladatok, -tevékenységek és más erőforrások létrehozásakor és felügyeletekor, először hozzon létre egy BatchAccountContext objektumot a fióknév és a kulcsok tárolásához:

    $context = Get-AzureRmBatchAccountKeys -AccountName <account_name>

A BatchAccountContext objektumot a **BatchContext** paramétert használó parancsmagokba adja át.

> [!NOTE]
> Alapértelmezés szerint a fiók elsődleges hívóbetűjét használja a hitelesítéshez, de explicit módon kiválaszthatja a használni kívánt betűt, ha módosítja a BatchAccountContext objektum **KeyInUse** tulajdonságát: `$context.KeyInUse = "Secondary"`.
> 
> 

## <a name="create-and-modify-batch-resources"></a>Batch-erőforrások létrehozása és módosítása
Például a **New-AzureBatchPool**, **New-AzureBatchJob** és **New-AzureBatchTask** parancsmagokkal hozhat létre erőforrásokat a Batch-fiókokban. Megfelelő **Get-** és **Set-** parancsmagokkal frissítheti a meglévő erőforrások tulajdonságait, és a **Remove-** parancsmagokkal távolíthatja el az erőforrásokat a Batch-fiókokról.

Sok parancsmag használatakor egy BatchContext objektum átadása mellett részletes erőforrás-beállításokat tartalmazó objektumok létrehozására vagy átadására is szükség van, ahogyan az a következő példában látható. További példákat az egyes parancsmagok részletes súgójában talál.

### <a name="create-a-batch-pool"></a>Batch-készlet létrehozása
Batch-készlet létrehozásakor vagy frissítésekor kiválaszt egy felhőszolgáltatás- vagy virtuálisgép-konfigurációt az operációs rendszerhez a számítási csomópontokon (lásd: [Batch-funkciók áttekintése](batch-api-basics.md#pool)). A választása határozza meg, hogy a számítási csomópontok az egyik [Azure vendég operációs rendszer kiadással](../cloud-services/cloud-services-guestos-update-matrix.md#releases), vagy az Azure Marketplace egyik támogatott Linux vagy Windows virtuálisgép-lemezképével képeződnek-e le.

A **New-AzureBatchPool** parancs futtatásakor adja át a PSCloudServiceConfiguration vagy PSVirtualMachineConfiguration objektumban található operációsrendszer-beállításokat. A következő parancsmag például egy új Batch-készletet hoz létre kis méretű számítási csomópontokkal a felhőszolgáltatás konfigurációjában, amely a 3-as család (Windows Server 2012) legújabb operációsrendszer-verziójával van leképezve. Itt a **CloudServiceConfiguration** paraméter a *$configuration* változót a PSCloudServiceConfiguration objektumként határozza meg. A **BatchContext** paraméter egy korábban meghatározott *$context* változót ad meg a BatchAccountContext objektumként.

    $configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSCloudServiceConfiguration" -ArgumentList @(4,"*")

    New-AzureBatchPool -Id "AutoScalePool" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -AutoScaleFormula '$TargetDedicated=4;' -BatchContext $context

Az új készletben a számítási csomópontok célszámát egy automatikus méretezési képlet határozza meg. Ebben az esetben a képlet egyszerűen a **$TargetDedicated=4**, ami jelzi, hogy a készletben lévő számítási csomópontok száma legfeljebb 4.

## <a name="query-for-pools-jobs-tasks-and-other-details"></a>Készletek, feladatok, tevékenységek és egyéb részletek lekérdezése
Például a **Get-AzureBatchPool**, **Get-AzureBatchJob** és **Get-AzureBatchTask** parancsmagokkal kérdezhet le Batch-fiókokon létrehozott entitásokat.

### <a name="query-for-data"></a>Adatok lekérdezése
Például a **Get-AzureBatchPools** használatával megkeresheti a készleteket. Ez alapértelmezés szerint a fiók összes készletét lekérdezi, ha a BatchAccountContext objektumot már tárolta a *$context* helyen:

    Get-AzureBatchPool -BatchContext $context

### <a name="use-an-odata-filter"></a>OData-szűrő használata
Megadhat egy OData-szűrőt a **Filter** paraméterrel, ha csak a kívánt objektumokat szeretné megkeresni. Megkeresheti például az összes olyan készletet, amelynek az azonosítója a „myPool” karakterlánccal kezdődik:

    $filter = "startswith(id,'myPool')"

    Get-AzureBatchPool -Filter $filter -BatchContext $context

Ez a módszer nem olyan rugalmas, mint a „Where-Object” használata a helyi folyamatokban. A lekérdezést azonban közvetlenül a Batch szolgáltatásnak küldi a rendszer, hogy minden szűrés a kiszolgálói oldalon történjen, így internetes sávszélesség takarítható meg.

### <a name="use-the-id-parameter"></a>Az Id paraméter használata
Az OData-szűrő alternatívája az **Id** paraméter használata. Egy „myPool” azonosítójú adott készlet lekérdezése:

    Get-AzureBatchPool -Id "myPool" -BatchContext $context

Az **ID** paraméter csak a teljes azonosítót használó kereséseket támogatja, a helyettesítő karaktereket és az OData stílusú szűrőket nem.

### <a name="use-the-maxcount-parameter"></a>A MaxCount paraméter használata
Alapértelmezés szerint mindegyik parancsmag maximum 1000 objektumot ad vissza. Ha eléri ezt a korlátot, pontosítsa a szűrőt, hogy kevesebb objektumot adjon vissza, vagy explicit módon állítson be maximális értéket a **MaxCount** paraméterrel. Példa:

    Get-AzureBatchTask -MaxCount 2500 -BatchContext $context

A felső határ eltávolításához 0 vagy kisebb értéket adjon meg a **MaxCount** paraméternek.

### <a name="use-the-powershell-pipeline"></a>A PowerShell-folyamat használata
A Batch-parancsmagok a PowerShell-folyamattal adatokat tudnak küldeni a folyamatok között. Ennek ugyanaz a hatása, mint a paraméterek megadása, de megkönnyíti a több entitással való munkát.

Például keresse meg és jelenítse meg fiókjában az összes feladatot:

    Get-AzureBatchJob -BatchContext $context | Get-AzureBatchTask -BatchContext $context

Indítsa újra a készlet összes számítási csomópontját:

    Get-AzureBatchComputeNode -PoolId "myPool" -BatchContext $context | Restart-AzureBatchComputeNode -BatchContext $context

## <a name="application-package-management"></a>Alkalmazáscsomagok kezelése
Az alkalmazáscsomagok egyszerű módot kínálnak az alkalmazások üzembe helyezésére a készletek számítási csomópontjain. A Batch PowerShell-parancsmagjaival feltöltheti és kezelheti az alkalmazáscsomagokat a Batch-fiókban, valamint a számítási csomópontokban üzembe helyezheti a csomagverziókat.

Alkalmazás **létrehozása**:

    New-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

Alkalmazáscsomag **hozzáadása**:

    New-AzureRmBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0" -Format zip -FilePath package001.zip

Állítsa be az alkalmazás **alapértelmezett verzióját** :

    Set-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -DefaultVersion "1.0"

Alkalmazás csomagjainak **listázása**

    $application = Get-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

    $application.ApplicationPackages

Alkalmazáscsomag **törlése**

    Remove-AzureRmBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0"

Alkalmazás **törlése**

    Remove-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

> [!NOTE]
> Az alkalmazás törlése előtt törölnie kell az alkalmazás összes alkalmazáscsomag-verzióját. Ha olyan alkalmazást próbál meg törölni, amely jelenleg rendelkezik alkalmazáscsomaggal, „Ütközés” hibaüzenetet fog kapni.
> 
> 

### <a name="deploy-an-application-package"></a>Alkalmazáscsomag üzembe helyezése
Készlet létrehozásakor megadhat egy vagy több alkalmazáscsomagot az üzembe helyezéshez. Ha a készlet létrehozásakor megad egy csomagot, az a csomópont készlethez való csatlakoztatásakor minden csomóponton üzembe lesz helyezve. A csomagok akkor is üzembe lesznek helyezve, ha a csomópontot újraindítják vagy alaphelyzetbe állítják.

Adja meg az `-ApplicationPackageReference` kapcsolót, ha készletet hoz létre egy alkalmazáscsomag üzembe helyezéséhez a készlet csomópontjain, amikor azok csatlakoznak a készlethez. Először hozzon létre egy **PSApplicationPackageReference**-objektumot, és konfigurálja a készlet számítási csomópontjain üzembe helyezendő alkalmazás azonosítóval és a csomagverzióval:

    $appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

    $appPackageReference.ApplicationId = "MyBatchApplication"

    $appPackageReference.Version = "1.0"

Ezután hozza létre a készletet, majd a csomag-referenciaobjektumot az `ApplicationPackageReferences` beállítás argumentumaként adja meg:

    New-AzureBatchPool -Id "PoolWithAppPackage" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -BatchContext $context -ApplicationPackageReferences $appPackageReference

Az alkalmazáscsomagok használatával kapcsolatban további információkat az [Application deployment with Azure Batch application packages](batch-application-packages.md) (Alkalmazások üzembe helyezése az Azure Batch-alkalmazáscsomagokkal) című cikk tartalmazza.

> [!IMPORTANT]
> Az alkalmazáscsomagok használatához [hozzá kell kapcsolnia egy Azure Storage-fiókot](#linked-storage-account-autostorage) a Batch-fiókjához.
> 
> 

### <a name="update-a-pools-application-packages"></a>Készlet alkalmazáscsomagjainak frissítése
A már létező készlethez rendelt alkalmazás frissítéséhez előbb hozzon létre PSApplicationPackageReference-objektumot a kívánt tulajdonságokkal (alkalmazásazonosító és csomagverzió):

    $appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

    $appPackageReference.ApplicationId = "MyBatchApplication"

    $appPackageReference.Version = "2.0"

Ezután olvassa be a készletet a Batch-ből, tisztítsa meg a már létező csomagoktól, adja hozzá az új csomagreferenciát, majd az új készletbeállításokkal frissítse a Batch-szolgáltatást:

    $pool = Get-AzureBatchPool -BatchContext $context -Id "PoolWithAppPackage"

    $pool.ApplicationPackageReferences.Clear()

    $pool.ApplicationPackageReferences.Add($appPackageReference)

    Set-AzureBatchPool -BatchContext $context -Pool $pool

A készlet tulajdonságainak frissítése megtörtént a Batch-szolgáltatásban. Ha azonban a készlet számítási csomópontjain szeretné üzembe helyezni az új alkalmazáscsomagot, újra kell indítania vagy alaphelyzetbe kell állítania az adott csomópontok rendszerképét. Az alábbi paranccsal újraindíthatja a készlet összes számítási csomópontját:

    Get-AzureBatchComputeNode -PoolId "PoolWithAppPackage" -BatchContext $context | Restart-AzureBatchComputeNode -BatchContext $context

> [!TIP]
> A készlet számítási csomópontjain több alkalmazáscsomagot is üzembe helyezhet. Ha a jelenleg telepített csomagok cseréje helyett inkább *hozzáadna* egy alkalmazáscsomagot, hagyja ki a felső `$pool.ApplicationPackageReferences.Clear()` sort.
> 
> 

## <a name="next-steps"></a>Következő lépések
* A parancsmag részletes szintaxisáért és példákért lásd: [Azure Batch-parancsmagok referenciája](https://msdn.microsoft.com/library/azure/mt125957.aspx).
* Az alkalmazások és alkalmazáscsomagok Batch-ben történő használatával kapcsolatban további információt az [Application deployment with Azure Batch application packages](batch-application-packages.md) (Alkalmazások üzembe helyezése az Azure Batch-alkalmazáscsomagokkal) című cikkben talál.




<!--HONumber=Dec16_HO1-->



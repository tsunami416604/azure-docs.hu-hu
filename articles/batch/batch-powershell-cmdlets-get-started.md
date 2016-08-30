<properties
   pageTitle="Az Azure Batch PowerShell használatának első lépései | Microsoft Azure"
   description="Gyors bevezetést olvashat az Azure Batch szolgáltatáshoz használható Azure PowerShell-parancsmagokról"
   services="batch"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""/>

<tags
   ms.service="batch"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="powershell"
   ms.workload="big-compute"
   ms.date="04/21/2016"
   ms.author="danlep"/>

# Az Azure Batch PowerShell-parancsmagok használatának első lépései
Ez a cikk gyors ismertetést nyújt a Batch-fiókok felügyeletéhez és a Batch-erőforrásokkal, például készletekkel, feladatokkal és tevékenységekkel használható Azure PowerShell-parancsmagokról. A Batch API-k, az Azure Portal és az Azure parancssori felület (CLI) használatával végrehajtott műveletek közül sokat elvégezhet a Batch-parancsmagokkal is. Ez a cikk az Azure PowerShell 1.3.2-es vagy újabb verziójának parancsmagjain alapul.

A Batch-parancsmagok teljes listájáért és a parancsmagok részletes szintaxisáért lásd: [Azure Batch-parancsmagok referenciája](https://msdn.microsoft.com/library/azure/mt125957.aspx). 


## Előfeltételek

* **Azure PowerShell** – Az Azure PowerShell letöltéséről és telepítéséről útmutatásért lásd: [How to install and configure Azure PowerShell](../powershell-install-configure.md) (Az Azure PowerShell telepítése és konfigurálása). 
   
    * Mivel az Azure Batch-parancsmagok az Azure Resource Manager modulban találhatók, futtatnia kell a **Login-AzureRmAccount** parancsmagot, hogy az előfizetéshez csatlakozzon. 
    
    * Ajánlott gyakran frissíteni az Azure PowerShellt a szolgáltatásfrissítések és -fejlesztések kihasználása érdekében. 
    
* **Regisztráció a Batch-szolgáltató névterével (egyszeri művelet)** – A Batch-fiókok használata előtt regisztrálnia kell a Batch-szolgáltató névterével. Ezt a műveletet előfizetésenként csak egyszer kell elvégezni. Futtassa a következő parancsmagot:

        Register-AzureRMResourceProvider -ProviderNamespace Microsoft.Batch


## Batch-fiókok és kulcsok felügyelete

### Batch-fiók létrehozása

A **New-AzureRmBatchAccount** parancs új Batch-fiókot hoz létre a meghatározott erőforráscsoportban. Ha még nem rendelkezik erőforráscsoporttal, hozzon létre egyet az [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/azure/mt603739.aspx) parancsmag futtatásával, és a **Hely** paraméterben adjon meg egy Azure-régiót (például az USA középső régióját). Példa:


    New-AzureRmResourceGroup –Name MyBatchResourceGroup –location "Central US"


Ezután hozzon létre egy új Batch-fiókot az erőforráscsoportban, majd adjon meg egy fióknevet az <*account_name*> helyőrző helyett, valamint egy helyet, ahol a Batch-szolgáltatás elérhető. A fiók létrehozásának befejezése több percig is eltarthat. Példa:


    New-AzureRmBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName MyBatchResourceGroup

> [AZURE.NOTE] A Batch-fiók nevének egyedinek kell lennie az erőforráscsoport Azure-régiójában, 3–24 karaktert kell tartalmaznia, és csak kisbetűkből és számokból állhat.

### Fiók hívóbetűinek beszerzése
A **Get-AzureRmBatchAccountKeys** megjeleníti az Azure Batch-fiókkal társított hívóbetűket. Például a következő futtatásával lekérheti a létrehozott fiók elsődleges és másodlagos hívóbetűit.

    $Account = Get-AzureRmBatchAccountKeys –AccountName <accountname>

    $Account.PrimaryAccountKey

    $Account.SecondaryAccountKey


### Új hívóbetű létrehozása
A **New-AzureRmBatchAccountKey** új elsődleges vagy másodlagos hívóbetűt hoz létre az Azure Batch-fiókhoz. Ha például új elsődleges hívóbetűt szeretne létrehozni a Batch-fiókhoz, írja be a következőt:


    New-AzureRmBatchAccountKey -AccountName <account_name> -KeyType Primary


> [AZURE.NOTE] Új másodlagos hívóbetű létrehozásához adja meg a „Másodlagos” szöveget a **KeyType** paraméterhez. Az elsődleges és a másodlagos hívóbetűket külön kell újra létrehoznia.

### Batch-fiók törlése
A **Remove-AzureRmBatchAccount** törli a Batch-fiókot. Példa:


    Remove-AzureRmBatchAccount -AccountName <account_name>

A felugró kérdésre erősítse meg, hogy valóban el kívánja távolítani a fiókot. A fiókeltávolítás eltarthat egy ideig.

## BatchAccountContext objektum létrehozása

Ha a Batch PowerShell-parancsmagokkal szeretne végrehajtani a hitelesítést Batch-készletek, -feladatok, -tevékenységek és más erőforrások létrehozásakor és felügyeletekor, először hozzon létre egy BatchAccountContext objektumot a fióknév és a kulcsok tárolásához:

    $context = Get-AzureRmBatchAccountKeys -AccountName <account_name>

A BatchAccountContext objektumot a **BatchContext** paramétert használó parancsmagokba adja át.

> [AZURE.NOTE] Alapértelmezés szerint a fiók elsődleges hívóbetűjét használja a hitelesítéshez, de explicit módon kiválaszthatja a használni kívánt betűt, ha módosítja a BatchAccountContext objektum **KeyInUse** tulajdonságát: `$context.KeyInUse = "Secondary"`.



## Batch-erőforrások létrehozása és módosítása
Például a **New-AzureBatchPool**, **New-AzureBatchJob** és **New-AzureBatchTask** parancsmagokkal hozhat létre erőforrásokat a Batch-fiókokban. Megfelelő **Get-** és **Set-** parancsmagokkal frissítheti a meglévő erőforrások tulajdonságait, és a **Remove-** parancsmagokkal távolíthatja el az erőforrásokat a Batch-fiókokról. 

### Batch-készlet létrehozása

A következő parancsmag például egy új Batch-készletet hoz létre, amely úgy van konfigurálva, hogy kis méretű virtuális gépeket használjon a 3-as család (Windows Server 2012) legújabb operációsrendszer-verziójával. A számítási csomópontok célszámát egy automatikus méretezési képlet határozza meg. Ebben az esetben a képlet egyszerűen a **$TargetDedicated=3**, ami jelzi, hogy a készletben lévő számítási csomópontok száma legfeljebb 3. A **BatchContext** paraméter egy korábban meghatározott *$context* változót ad meg a BatchAccountContext objektumként.


    New-AzureBatchPool -Id "MyAutoScalePool" -VirtualMachineSize "Small" -OSFamily "3" -TargetOSVersion "*" -AutoScaleFormula '$TargetDedicated=3;' -BatchContext $Context

>[AZURE.NOTE]A Batch PowerShell-parancsmagok jelenleg csak a számítási csomópontok Cloud Services-konfigurációját támogatják. Így a Windows Server operációs rendszer Azure vendég operációs rendszer verzióit futtathatja a számítási csomópontokon. A Batch-készletek egyéb számításicsomópont-konfigurációihoz használja a Batch SDK-kat vagy az Azure parancssori felületet.

## Készletek, feladatok, tevékenységek és egyéb részletek lekérdezése

Például a **Get-AzureBatchPool**, **Get-AzureBatchJob** és **Get-AzureBatchTask** parancsmagokkal kérdezhet le Batch-fiókokon létrehozott entitásokat.


### Adatok lekérdezése

Például a **Get-AzureBatchPools** használatával megkeresheti a készleteket. Ez alapértelmezés szerint a fiók összes készletét lekérdezi, ha a BatchAccountContext objektumot már tárolta a *$context* helyen:


    Get-AzureBatchPool -BatchContext $context

### OData-szűrő használata

Megadhat egy OData-szűrőt a **Filter** paraméterrel, ha csak a kívánt objektumokat szeretné megkeresni. Megkeresheti például az összes olyan készletet, amelynek az azonosítója a „myPool” karakterlánccal kezdődik:


    $filter = "startswith(id,'myPool')"

    Get-AzureBatchPool -Filter $filter -BatchContext $context


Ez a módszer nem olyan rugalmas, mint a „Where-Object” használata a helyi folyamatokban. A lekérdezést azonban közvetlenül a Batch szolgáltatásnak küldi a rendszer, hogy minden szűrés a kiszolgálói oldalon történjen, így internetes sávszélesség takarítható meg.

### Az Id paraméter használata

Az OData-szűrő alternatívája az **Id** paraméter használata. Egy „myPool” azonosítójú adott készlet lekérdezése:


    Get-AzureBatchPool -Id "myPool" -BatchContext $context


Az **ID** paraméter csak a teljes azonosítót használó kereséseket támogatja, a helyettesítő karaktereket és az OData stílusú szűrőket nem.



### A MaxCount paraméter használata

Alapértelmezés szerint mindegyik parancsmag maximum 1000 objektumot ad vissza. Ha eléri ezt a korlátot, pontosítsa a szűrőt, hogy kevesebb objektumot adjon vissza, vagy explicit módon állítson be maximális értéket a **MaxCount** paraméterrel. Példa:


    Get-AzureBatchTask -MaxCount 2500 -BatchContext $context

A felső határ eltávolításához 0 vagy kisebb értéket adjon meg a **MaxCount** paraméternek.

### A folyamat használata

A Batch-parancsmagok a PowerShell-folyamattal adatokat tudnak küldeni a folyamatok között. Ennek ugyanaz a hatása, mint a paraméterek megadása, de megkönnyíti több entitás listázását. A következő például megkeresi a fiók összes feladatát:


    Get-AzureBatchJob -BatchContext $context | Get-AzureBatchTask -BatchContext $context


## Következő lépések
* A parancsmag részletes szintaxisáért és példákért lásd: [Azure Batch-parancsmagok referenciája](https://msdn.microsoft.com/library/azure/mt125957.aspx).

* További információ az elemek számának csökkentéséről és a Batch lekérdezésiről visszaadott információk típusáról: [A Batch szolgáltatás hatékony lekérdezése](batch-efficient-list-queries.md). 



<!--HONumber=jun16_HO2-->



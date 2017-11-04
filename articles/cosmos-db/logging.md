---
title: "Az Azure Cosmos DB diagnosztikai naplózás |} Microsoft Docs"
description: "Ez az oktatóanyag használatával alakítsa ki Ismerkedés az Azure Cosmos DB-naplózás."
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
tags: azure-resource-manager
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2017
ms.author: mimig
ms.openlocfilehash: 407a9a3be4ae8a9b00a953914e6b4414d8dac8b6
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/14/2017
---
# <a name="azure-cosmos-db-diagnostic-logging"></a>Az Azure Cosmos DB diagnosztikai naplózás

Miután lépéseket egy vagy több Azure Cosmos DB adatbázist használ, érdemes lehet figyelni hogyan és mikor érhetők el az adatbázisok. Azure Cosmos DB diagnosztikai naplózás lehetővé téve a figyelést. Diagnosztikai naplózás engedélyezésével, elküldheti a naplófájlokat [Azure Storage](https://azure.microsoft.com/services/storage/), adatfolyamként küldje el azokat [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), és/vagy exportálja őket [Naplóelemzési](https://azure.microsoft.com/services/log-analytics/), amely része[ Az Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite).

![Diagnosztikai naplózás tárolási, az Event Hubs vagy az Operations Management Suite Naplóelemzési keresztül](./media/logging/azure-cosmos-db-logging-overview.png)

Ez az oktatóanyag segítségével Ismerkedés az Azure Cosmos DB az Azure-portálon, a parancssori felület vagy a PowerShell-naplózás.

## <a name="what-is-logged"></a>Mi kerül?

* Minden hitelesített REST DocumentDB (SQL) API-kérések naplózása történik, mely tartalmazza a hozzáférési engedélyeket, rendszerhibákból vagy hibás kérésekből adótó sikertelen kérelmeket. MongoDB, a Graph és a tábla API-k támogatása jelenleg nem áll rendelkezésre.
* Maga az adatbázis, amely magában foglalja a CRUD-műveleteknek a dokumentumok, tárolók és adatbázisok műveleteket.
* Műveletek a kulcsait, többek között létrehozása, módosítása vagy törlése a kulcsokat.
* A 401-es választ eredményező, nem hitelesített kérelmek. Ilyenek például azok a kérelmek, amelyek nem rendelkeznek tulajdonosi jogkivonattal, helytelen formátumúak vagy lejártak, vagy érvénytelen a jogkivonatuk.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elvégzéséhez a következőket kell rendelkeznie:

* Egy meglévő Azure Cosmos DB fiók, adatbázis, és a tároló. Ezek az erőforrások létrehozásának, lásd: [az Azure portál használatával adatbázisfiók létrehozása](create-documentdb-dotnet.md#create-a-database-account), [CLI minták](cli-samples.md), vagy [PowerShell-példák](powershell-samples.md).

<a id="#turn-on"></a>
## <a name="turn-on-logging-in-the-azure-portal"></a>Kapcsolja be a naplózás az Azure-portálon

1. Az a [Azure-portálon](https://portal.azure.com), az Azure Cosmos DB a fiókot, kattintson a **diagnosztikai naplók** a bal oldali navigációs, majd **a diagnosztika bekapcsolásához**.

    ![Az Azure-portálon az Azure Cosmos DB diagnosztikai naplózás bekapcsolása](./media/logging/turn-on-portal-logging.png)

2. Az a **diagnosztikai beállítások** lapján tegye a következőket: 

    * **Név**. Adjon meg egy nevet, a naplók létrehozásához.

    * **Archív tárfiókba**. Használja ezt a beállítást, meglévő tárfiókot csatlakozni kell. Új tárfiók létrehozása a portálon: [hozzon létre egy tárfiókot](../storage/common/storage-create-storage-account.md) és kövesse az utasításokat az erőforrás-kezelő, általános célú fiók létrehozásához. Ezután térjen vissza erre a lapra, jelölje be a tárfiók a portálon. Az újonnan létrehozott tárfiók megjelenik a legördülő menü néhány percig is eltarthat.
    * **Az eseményközpontok felé adatfolyam**. Használja ezt a beállítást, egy meglévő Eseményközpont névtér és esemény-központot csatlakozni kell. Az Event Hubs névtér létrehozásához lásd: [hozzon létre egy Event Hubs névtér és egy eseményközpontot, az Azure portál használatával](../event-hubs/event-hubs-create.md). Térjen vissza erre az oldalra a portálon, és válassza ki az Event Hubs-névteret és a házirend nevét.
    * **Naplóelemzési küldése**.     Használja ezt a beállítást, vagy használjon meglévő munkaterülethez vagy hozzon létre egy új Naplóelemzési munkaterület lépéseit követve [hozzon létre egy új munkaterületet](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace) a portálon. A naplók megtekintése a Naplóelemzési további információkért lásd: [Naplóelemzési a naplók megtekintése](#view-in-loganalytics).
    * **Naplófájl DataPlaneRequests**. Válassza ezt a beállítást, a DocumentDB, diagram és táblázat API fiókok diagnosztika bejelentkezni. Ha archiválni egy tárfiókot, válassza a diagnosztikai naplók megőrzési időtartama. Nincsenek autodeleted naplói a megőrzési időszak lejárta után.
    * **Naplófájl MongoRequests**. Válassza ezt a beállítást, a MongoDB API fiókok diagnosztika bejelentkezni. Ha archiválni egy tárfiókot, válassza a diagnosztikai naplók megőrzési időtartama. Nincsenek autodeleted naplói a megőrzési időszak lejárta után.
    * **Metrika kérelmek**. Válassza ezt a beállítást, a részletes adatok tárolására [Azure metrikák](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftdocumentdbdatabaseaccounts-cosmosdb). Ha archiválni egy tárfiókot, válassza a diagnosztikai naplók megőrzési időtartama. Nincsenek autodeleted naplói a megőrzési időszak lejárta után.

3. Kattintson a **Save** (Mentés) gombra.

    Ha hibaüzenet jelenik meg, amely szerint "nem sikerült frissíteni a diagnosztikai \<munkaterület neve >. Az előfizetés \<előfizetés-azonosító > microsoft.insights használandó nincs regisztrálva. " Kövesse a [hibaelhárítása Azure Diagnostics](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-storage) a fiók regisztrálására vonatkozó utasításokat ismételje meg ezt az eljárást.

    Ha szeretné módosítani a diagnosztikai naplók mentési módjának bármikor a jövőben, térhet vissza ezt a lapot, bármikor a fiókja a diagnosztikai beállításainak módosítása.

## <a name="turn-on-logging-using-cli"></a>Kapcsolja be a naplózás a parancssori felület használatával

Metrikák és az Azure parancssori felület használatával diagnosztikai naplózás engedélyezéséhez használja a következő parancsokat:

- Ahhoz, hogy a Storage-fiókok a diagnosztikai naplók tárolására, az alábbi parancsot használja:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   A `resourceId` a Azure Cosmos DB-fiók neve. A `storageId` , amelyre szeretné elküldeni a naplókat a tárfiók neve.

- Adatfolyamként való küldése a diagnosztikai naplók Eseményközpontokba való engedélyezéséhez az alábbi parancsot használja:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   A `resourceId` a Azure Cosmos DB-fiók neve. A `serviceBusRuleId` egy karakterlánc, ezt a formátumot:

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- A Naplóelemzési munkaterület elküldését a diagnosztikai naplók engedélyezéséhez az alábbi parancsot használja:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

Ezek a paraméterek ahhoz, hogy több kimenet beállításai kombinálhatja.

## <a name="turn-on-logging-using-powershell"></a>Kapcsolja be a naplózás a PowerShell használatával

Kapcsolja be a naplózás a PowerShell használatával, van szüksége az Azure Powershell legalább 1.0.1-es verziója.

Az Azure PowerShell telepítésérről és az Azure-előfizetéssel való társításáról további információt [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása) című cikkben találhat.

Ha már telepítette az Azure PowerShell és a verziója nem ismeri a PowerShell-konzolon, írja be a `(Get-Module azure -ListAvailable).Version`.  

### <a id="connect"></a>Csatlakozás az előfizetésekhez
Indítson el egy Azure PowerShell-munkamenetet, és jelentkezzen be az Azure-fiókjába az alábbi paranccsal:  

```powershell
Login-AzureRmAccount
```

Az előugró böngészőablakban adja meg az Azure-fiókja felhasználónevét és jelszavát. Az Azure PowerShell megkeresi az összes olyan előfizetést, amely ehhez a fiókhoz van rendelve, és alapértelmezés szerint kiválasztja az elsőt.

Ha több előfizetése van, előfordulhat, hogy meg kell adnia azt, amelyikkel az Azure Key Vault tárolóját létrehozta. Írja be az alábbi parancsot a fiókhoz tartozó előfizetések megjelenítéséhez:

```powershell
Get-AzureRmSubscription
```

Ezt követően a jelentkezik Azure Cosmos DB fiókhoz tartozó előfizetés megadásához írja be:

```powershell
Set-AzureRmContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Ha a fiókjához, fontos, hogy adja meg az előfizetés több előfizetéssel rendelkezik.
>
>

További információ az Azure PowerShell konfigurálásáról: [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása).

### <a id="storage"></a>Új tárfiók létrehozása a naplóknak
Bár meglévő tárfiókot a naplókhoz, ebben az oktatóanyagban azt hozzon létre egy új tárfiókot dedikált Azure Cosmos DB naplókba. Kényelmi célokat szolgál, azt tárolja a tárfiókadatok egy változóba nevű **sa**.

Az egyszerű, az ebben az oktatóanyagban vesszük ugyanahhoz az erőforráscsoporthoz tartozik, amely tartalmazza az Azure Cosmos DB adatbázishoz. Értékek helyettesítéséhez ContosoResourceGroup és contosocosmosdblogs "Északi középső Régiójában" saját értékek kulcsnevekkel:

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName ContosoResourceGroup `
-Name contosocosmosdblogs -Type Standard_LRS -Location 'North Central US'
```

> [!NOTE]
> Ha meglévő tárfiók használata mellett dönt, azt kell használjon ugyanazt az előfizetést Azure Cosmos DB előfizetését, majd azt a Resource Manager üzembe helyezési modellel, nem pedig a klasszikus telepítési modellt kell használnia.
>
>

### <a id="identify"></a>A naplók Azure Cosmos DB-fiók azonosítása
A Azure Cosmos DB-fiók neve nevű változó beállítása **fiók**, ahol a ResourceName az Azure Cosmos DB fiók nevét.

```powershell
$account = Get-AzureRmResource -ResourceGroupName ContosoResourceGroup `
-ResourceName contosocosmosdb -ResourceType "Microsoft.DocumentDb/databaseAccounts"
```

### <a id="enable"></a>Naplózás engedélyezése
Azure Cosmos adatbázis naplózásának engedélyezéséhez használja a Set-AzureRmDiagnosticSetting parancsmagot, az új tárfiókot, Azure Cosmos DB fiók és a kategóriát, amelynek szeretné engedélyezni a naplók változói együtt. Futtassa a következő parancsot, a beállítás a **-engedélyezve** jelzőt **$true**:

```powershell
Set-AzureRmDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests
```

A parancs kimenete a következő kell hasonlítania:

```powershell
    StorageAccountId            : /subscriptions/<subscription-ID>/resourceGroups/ContosoResourceGroup/providers`
    /Microsoft.Storage/storageAccounts/contosocosmosdblogs
    ServiceBusRuleId            :
    EventHubAuthorizationRuleId :
    Metrics
        TimeGrain       : PT1M
        Enabled         : False
        RetentionPolicy
        Enabled : False
        Days    : 0
    
    Logs
        Category        : DataPlaneRequests
        Enabled         : True
        RetentionPolicy
        Enabled : False
        Days    : 0
    
    WorkspaceId                 :
    Id                          : /subscriptions/<subscription-ID>/resourcegroups/ContosoResourceGroup/providers`
    /microsoft.documentdb/databaseaccounts/contosocosmosdb/providers/microsoft.insights/diagnosticSettings/service
    Name                        : service
    Type                        :
    Location                    :
    Tags                        :
```

Ez megerősíti, hogy naplózás engedélyezve van az adatbázis, az adatok mentése a tárfiókhoz.

Opcionálisan beállíthat egy megtartási házirendet a naplóihoz, így a régebbi naplófájlok automatikusan törlődni fognak. Például állítsa be a megtartási házirendet a **-RetentionEnabled** jelző használatával a **$true** értékre, majd állítsa a **-RetentionInDays** paramétert **90** értékre, így a 90 napnál régebbi naplófájlok automatikusan törlődnek.

```powershell
Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests`
  -RetentionEnabled $true -RetentionInDays 90
```

### <a id="access"></a>A naplók elérése
Azure Cosmos-adatbázis, ha a naplókban **DataPlaneRequests** kategória tárolódnak a **insights-logs--vezérlősík-kérelmek** megadott tárfiók tárolóhoz. 

Először hozzon létre egy változót a tároló nevéhez. Ez az útmutató többi teljes fogja használni.

```powershell
    $container = 'insights-logs-dataplanerequests'
```

A tároló összes blobjának megjelenítéséhez írja be az alábbi parancsot:

```powershell
Get-AzureStorageBlob -Container $container -Context $sa.Context
```

A kimenet ehhez hasonló lesz:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 10510193
ContentType       : application/octet-stream
LastModified      : 9/28/2017 7:49:04 PM +00:00
SnapshotTime      :
ContinuationToken:
Context           : Microsoft.WindowsAzure.Commands.Common.Storage.`
                    LazyAzureStorageContext
Name              : resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS`
/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/CONTOSOCOSMOSDB/y=2017/m=09/d=28/h=19/m=00/PT1H.json
```

Mivel a kimenetből látható, a blobok az alábbi elnevezési:`resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<Database Account Name>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

A dátum- és időértékek az UTC hivatkozási időzónát használják.

Ugyanazt a tárfiókot használható gyűjtött naplók tárolására több erőforrást, mert az a blob nevének teljes erőforrás-azonosító nagyon hasznos eléréséhez, vagy csak a szükséges blobokat töltse le. Előtte azonban nézzük meg, hogyan tölthető le az összes blob.

Elsőként hozzon létre egy mappát, amelybe letölti a blobokat. Példa:

```powershell
New-Item -Path 'C:\Users\username\ContosoCosmosDBLogs'`
 -ItemType Directory -Force
```

Majd kérje le az összes blob listáját:  

```powershell
$blobs = Get-AzureStorageBlob -Container $container -Context $sa.Context
```

A listát a „Get-AzureStorageBlobContent” paranccsal töltse le a mappába:

```powershell
$blobs | Get-AzureStorageBlobContent `
 -Destination 'C:\Users\username\ContosoCosmosDBLogs'
```

A második parancs futtatásakor a  **/**  a blob nevének elválasztó hoz létre egy teljes mapparendszert a rendeltetési mappára. A gyökérmappa-szerkezetében töltse le és fájlként a blobok tárolására szolgál.

A blobok egyenkénti letöltéséhez használjon helyettesítő elemeket. Példa:

* Ha több adatbázist, és szeretné, hogy csak egy adatbázishoz tartozó naplók letöltéséhez, nevű CONTOSOCOSMOSDB3:

    ```powershell
    Get-AzureStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/DATABASEACCOUNTS/CONTOSOCOSMOSDB3
    ```

* Ha több erőforráscsoporttal rendelkezik, de csak egyhez szeretne naplókat letölteni, használja a `-Blob '*/RESOURCEGROUPS/<resource group name>/*'` parancsot:

    ```powershell
    Get-AzureStorageBlob -Container $container `
    -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
    ```
* Ha azt szeretné, töltse le a naplófájlokat. július 2017 hónapja, `-Blob '*/year=2017/m=07/*'`:

    ```powershell
    Get-AzureStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/year=2017/m=07/*'
    ```

Továbbá:

* Az adatbázis erőforrás diagnosztikai beállítások állapotának lekérdezése:`Get-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`
* A naplózás letiltása **DataPlaneRequests** az adatbázis-fiók erőforrás kategória:`Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories DataPlaneRequests`


A BLOB visszaadott minden ezeket a lekérdezéseket tárolni, formázott JSON blob, az alábbi kódban látható módon. 

```json
{
    "records":
    [
        {
           "time": "Fri, 23 Jun 2017 19:29:50.266 GMT",
           "resourceId": "contosocosmosdb",
           "category": "DataPlaneRequests",
           "operationName": "Query",
           "resourceType": "Database",
           "properties": {"activityId": "05fcf607-6f64-48fe-81a5-f13ac13dd1eb",`
           "userAgent": "documentdb-dotnet-sdk/1.12.0 Host/64-bit MicrosoftWindowsNT/6.2.9200.0 AzureSearchIndexer/1.0.0",`
           "resourceType": "Database","statusCode": "200","documentResourceId": "",`
           "clientIpAddress": "13.92.241.0","requestCharge": "2.260","collectionRid": "",`
           "duration": "9250","requestLength": "72","responseLength": "209", "resourceTokenUserRid": ""}
        }
    ]
}
```

Minden JSON BLOB adatokkal kapcsolatos további tudnivalókért lásd: [az Azure Cosmos DB naplók értelmezése](#interpret).

## <a name="managing-your-logs"></a>A naplók kezelése

Naplók elérhetővé válnak a fiókjában két órát a óta, az Azure Cosmos DB művelet történt. A tárfiók naplófájljait Önnek kell kezelnie:

* Az Azure szabványos hozzáférés-vezérlési módszereivel korlátozhatja a naplókhoz való hozzáférést, így megvédheti azokat.
* Törölje azokat a naplókat, amelyeket nem kíván megőrizni a tárfiókban.
* A megőrzési idő tárfiókba archivált vezérlősík kérelmek van konfigurálva, a portál amikor **napló DataPlaneRequests** van kiválasztva. Ez a beállítás módosításához lásd [kapcsolja be a naplózás az Azure portálon](#turn-on-logging-in-the-azure-portal).


<a id="#view-in-loganalytics"></a>
## <a name="view-logs-in-log-analytics"></a>A Naplóelemzési naplók megtekintése

Ha bejelölte a **küldeni a Naplóelemzési** beállítást, ha bekapcsolta a naplózást, a gyűjtemény diagnosztikai adatokat továbbíthatja a rendszer Naplóelemzési két órán belül. Ez azt jelenti, hogy ha Naplóelemzési naplózás bekapcsolása után tekinti meg, akkor nem látható adatokat. Csak két óra várakozás után próbálja újra. 

A naplók megtekintése, előtt érdemes, ha a munkaterületet frissítve lett az új Naplóelemzési lekérdezési nyelv használatával ellenőrizheti, hogy. Ennek ellenőrzéséhez nyissa meg a [Azure-portálon](https://portal.azure.com), kattintson a **Naplóelemzési** bal oldalán, majd válassza ki a munkaterület neve a következő ábrán látható módon. A **OMS-munkaterület** lap is megjelenik, a következő ábrán látható módon.

![Naplóelemzési az Azure-portálon](./media/logging/azure-portal.png)

Ha a következő üzenet jelenik meg a a **OMS-munkaterület** lap, a munkaterület nincs frissítve az új nyelv használatával. Új lekérdezés nyelvének frissítésével kapcsolatos további információkért lásd: [Azure Naplóelemzési munkaterület frissítése új naplófájl-keresési](../log-analytics/log-analytics-log-search-upgrade.md). 

![Log analytics frissítési értesítést](./media/logging/upgrade-notification.png)

A Naplóelemzési a diagnosztikai adatok megtekintéséhez nyissa meg a napló lapon a bal oldali menüből vagy a felügyeleti területen az oldal a következő ábrán látható módon.

![Keresési beállítások jelentkezzen be az Azure-portálon](./media/logging/log-analytics-open-log-search.png)

Most, hogy engedélyezte az adatgyűjtést, futtassa a következő naplófájl-keresési példa, az új lekérdezési nyelv használatával tekintse meg a 10 legutóbbi naplókat `AzureDiagnostics | take 10`.

![A minta 10 naplófájl-keresési igénybe vehet.](./media/logging/log-analytics-query.png)

<a id="#queries"></a>
### <a name="queries"></a>Lekérdezések

Az alábbiakban néhány további lekérdezések adhatja meg azokat a **naplófájl-keresési** jelölőnégyzetet, hogy az Azure Cosmos DB tárolók figyeléséhez nyújt segítséget. Ezeket a lekérdezéseket dolgozni a [új nyelvi](../log-analytics/log-analytics-log-search-upgrade.md). 

Az egyes napló keresés által visszaadott adatok szerinti kapcsolatos további tudnivalókért lásd: [az Azure Cosmos DB naplók értelmezése](#interpret).

* Minden diagnosztikai naplókat a Azure Cosmos-Adatbázisból az adott időszakra vonatkozóan.

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
    ```

* Tíz legutóbb naplózott események.

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | take 10
    ```

* Minden művelet, a művelet típusa szerint csoportosítva.

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName
    ```

* Összes művelet erőforrás szerint csoportosítva.

    ```
    AzureActivity | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```

* Összes felhasználói tevékenységet, erőforrás szerint csoportosítva. Vegye figyelembe, hogy ez egy tevékenység naplója nem diagnosztikai naplófájl.

    ```
    AzureActivity | where Caller == "test@company.com" and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```

* Milyen műveletek tovább tart, mint 3 ezredmásodperc.

    ```
    AzureDiagnostics | where toint(duration_s) > 3000 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by clientIpAddress_s, TimeGenerated
    ```

* Melyik ügynök fut a műveleteket.

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName, userAgent_s
    ```

* Ha volt hosszú ideig futó műveletek végre.

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | project TimeGenerated , toint(duration_s)/1000 | render timechart
    ```

Az új naplófájl-keresési nyelv használatával kapcsolatos további információkért lásd: [ismertetése napló keres a Naplóelemzési](../log-analytics/log-analytics-log-search-new.md). 

## <a id="interpret"></a>A naplók értelmezése

Diagnosztikai adatok Azure Storage és Naplóelemzési tárolt nagyon hasonló a séma használja. 

Az alábbi táblázatban minden naplóbejegyzés tartalmát.

| Az Azure Storage mező vagy tulajdonság | Napló Analytics tulajdonság | Leírás |
| --- | --- | --- |
| time | TimeGenerated | A dátum és idő (UTC), ha a művelet történt. |
| resourceId | Erőforrás | A Azure Cosmos DB fiók, amelynek a naplók engedélyezve vannak.|
| category | Kategória | Az Azure Cosmos DB naplók DataPlaneRequests az érték csak érhető el. |
| operationName | OperationName | A művelet neve. Ez az érték a következő műveletek valamelyike lehet: frissítés, Olvasás, ReadFeed, törlése, a név felülírandó, végrehajtás, SqlQuery, lekérdezés, JSQuery, Head, HeadFeed vagy Upsert létrehozása.   |
| properties | n/a | Ez a mező tartalmát a következő sorokat ismerteti. |
| Tevékenységazonosító | activityId_g | A naplózott műveletnek egyedi GUID azonosítója. |
| Felhasználói ügynök | userAgent_s | A kérelem végrehajtása felhasználói ügynök meghatározó karakterlánc. A formátum: {felhasználói ügynök neve} / {version}.|
| a resourceType | ResourceType | Elért erőforrás típusa. Ez az érték a következő típusok egyike lehet: adatbázis, gyűjtemény, a dokumentum, melléklet, felhasználó, engedéllyel, tárolt eljárás, eseményindító, UserDefinedFunction vagy ajánlat. |
| statusCode |statusCode_s | A válasz a művelet állapotát. |
| requestResourceId | ResourceId | Az erőforrás-azonosítója a kérést, vethetők databaseRid, collectionRid vagy documentRid végrehajtott művelettől függően előfordulhat, hogy mutasson.|
| clientIpAddress | clientIpAddress_s | Az ügyfél IP-címét. |
| requestCharge | requestCharge_s | A művelet által használt RUs száma |
| collectionRid | collectionId_s | A gyűjtemény egyedi azonosítója.|
| Időtartam | duration_s | A művelet, a ticks időtartama. |
| requestLength | requestLength_s | A kérést, bájtban hosszát. |
| responseLength | responseLength_s | A válasz bájtban hosszát.|
| resourceTokenUserRid | resourceTokenUserRid_s | Ami nem üres mikor [erőforrás jogkivonatok](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens) használ a hitelesítési és erőforrás-azonosító a felhasználó mutat. |

## <a name="next-steps"></a>Következő lépések

- Felmérheti, nem csak hogyan engedélyezze a naplózást, hanem a metrikák és a napló kategóriák a különböző Azure által támogatott szolgáltatások olvassa el is a [áttekintése a Microsoft Azure-ban mérőszámok](../monitoring-and-diagnostics/monitoring-overview-metrics.md) és [áttekintés az Azure Diagnosztikai naplók](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) cikkeket.
- Ezek a cikkek az event hubs megismeréséhez olvassa el:
   - [Mik az Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
   - [Bevezetés az Event Hubs használatába](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
- Olvasási [metrikák és diagnosztikai naplók letöltése Azure Storage-ból](../storage/blobs/storage-dotnet-how-to-use-blobs.md#download-blobs)
- Olvasási [ismertetése napló Naplóelemzési keresi](../log-analytics/log-analytics-log-search-new.md)

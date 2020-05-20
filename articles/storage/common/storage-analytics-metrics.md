---
title: Azure Storage Analytics metrikák (klasszikus)
description: Ismerje meg, hogyan használhatja Storage Analytics metrikákat az Azure Storage-ban.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: 5613453667e3bb278f4da22ebed4502def70235b
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83675900"
---
# <a name="azure-storage-analytics-metrics-classic"></a>Azure Storage Analytics metrikák (klasszikus)

Az Azure Storage a Storage Analytics megoldást használja az összesített tranzakciós statisztikát és a tárolási szolgáltatásra irányuló kérelmekre vonatkozó kapacitási adatokat tartalmazó mérőszámok tárolására. A tranzakciókat az API műveleti szintjén és a tárolási szolgáltatás szintjén kell jelenteni. A kapacitás a tárolási szolgáltatás szintjén szerepel. A metrikák adatai a következőket használhatják:
- A tárolási szolgáltatás használatának elemzése.
- A tárolási szolgáltatáshoz intézett kérelmekkel kapcsolatos problémák diagnosztizálása.
- Javítsa a szolgáltatást használó alkalmazások teljesítményét.

 Storage Analytics metrikák alapértelmezés szerint engedélyezve vannak az új Storage-fiókok esetében. A metrikákat a [Azure Portal](https://portal.azure.com/)is konfigurálhatja. További információ: [Storage-fiók figyelése a Azure Portalban](/azure/storage/storage-monitor-storage-account). Storage Analytics programozott módon is engedélyezheti a REST API vagy az ügyféloldali kódtár használatával. A szolgáltatás tulajdonságainak beállítása művelettel engedélyezheti a Storage Analytics az egyes szolgáltatásokhoz.  

> [!NOTE]
> Storage Analytics metrikák érhetők el az Azure Blob Storage-hoz, az Azure üzenetsor-tároláshoz, az Azure Table Storage-hoz és a Azure Fileshoz.
> Storage Analytics metrikák mostantól klasszikus metrikák. Azt javasoljuk, hogy Storage Analytics mérőszámok helyett [Azure monitor használjon tárolási metrikákat](monitor-storage.md) .

## <a name="transaction-metrics"></a>Tranzakciómetrikák  
 Az egyes tárolási szolgáltatások és a kért API-művelet óránkénti vagy percenkénti időközönként rögzíti az adatmennyiséget, amely magában foglalja a bejövő és a kimenő, a rendelkezésre állási, a hibák és a kategorizált kérelmek százalékos arányát. A tranzakció részleteinek teljes listáját itt tekintheti meg: [Storage Analytics mérőszámok tábla sémája](/rest/api/storageservices/storage-analytics-metrics-table-schema).  

 A tranzakciós adatai a szolgáltatási szinten és az API-műveleti szinten vannak rögzítve. A szolgáltatási szinten az összes igényelt API-műveletet összefoglaló statisztikát óránként egy táblázatos entitásba írja a rendszer, még akkor is, ha nem történtek kérelmek a szolgáltatáshoz. Az API működési szintjén a statisztikák csak akkor íródnak az entitásba, ha a műveletet az adott órán belül kérték.  

 Ha például **GetBlob** műveletet hajt végre a blob szolgáltatásban, Storage Analytics metrikák naplózzák a kérést, és belefoglalják a blob szolgáltatás összesített adataiba és a **GetBlob** műveletbe. Ha az óra során nem kér **GetBlob** műveletet, akkor az entitás nem íródik a művelet *$MetricsTransactionsBlob* .  

 A tranzakciós metrikák a Storage Analytics saját maga által küldött felhasználói kérésekhez és kérésekhez vannak rögzítve. A naplók és a tábla entitások írásához Storage Analytics kérelmeket például rögzíti a rendszer.

## <a name="capacity-metrics"></a>Kapacitásmetrikák  

> [!NOTE]
>  A kapacitási metrikák jelenleg csak a blob szolgáltatáshoz érhetők el.

 A rendszer naponta rögzíti a kapacitási adatkészletet a Storage-fiók blob-szolgáltatásához, és két tábla entitást ír. Az egyik entitás statisztikai adatokat biztosít a felhasználói adatokhoz, a másik pedig a `$logs` Storage Analytics által használt BLOB-tároló statisztikáit tartalmazza. A *$MetricsCapacityBlob* tábla a következő statisztikákat tartalmazza:  

- **Kapacitás**: a Storage-fiók blob szolgáltatása által felhasznált tárterület mérete bájtban megadva.  
- **ContainerCount**: a Storage-fiók blob szolgáltatásában lévő blob-tárolók száma.  
- **ObjectCount**: a Storage-fiók blob szolgáltatásában lévő véglegesített és nem véglegesített blokk-vagy oldal-Blobok száma.  

  További információ a kapacitási metrikákkal kapcsolatban: [Storage Analytics mérőszámok tábla sémája](/rest/api/storageservices/storage-analytics-metrics-table-schema).  

## <a name="how-metrics-are-stored"></a>A metrikák tárolása  

 Az egyes tárolási szolgáltatásokhoz tartozó összes metrikai adat a szolgáltatás számára fenntartott három táblában tárolódik. Az egyik tábla a tranzakciós adatokra szolgál, az egyik tábla a tranzakciós adatokhoz, a másik pedig a kapacitásra vonatkozó információkat tartalmazza. A tranzakciós és a perces tranzakciós adatok kérelmekből és válaszokból állnak. A kapacitás adatai tárolási használati adatokból állnak. A Storage-fiók blob szolgáltatásának óra mérőszámai, perc mérőszámai és kapacitása a következő táblázatban leírtaknak megfelelően elnevezett táblákban érhető el.  

|Metrikák szintje|Táblák nevei|Verziók esetében támogatott|  
|-------------------|-----------------|----------------------------|  
|Óránkénti mérőszámok, elsődleges hely|– $MetricsTransactionsBlob<br />– $MetricsTransactionsTable<br />– $MetricsTransactionsQueue|Csak 2013. augusztus 15. előtti verziók. Habár ezek a nevek továbbra is támogatottak, javasoljuk, hogy váltson az alábbi táblázatok használatára.|  
|Óránkénti mérőszámok, elsődleges hely|– $MetricsHourPrimaryTransactionsBlob<br />– $MetricsHourPrimaryTransactionsTable<br />– $MetricsHourPrimaryTransactionsQueue<br />– $MetricsHourPrimaryTransactionsFile|Minden verzió. A file Service-metrikák támogatása csak a 2015-es és újabb verziókban érhető el.|  
|Perc mérőszámok, elsődleges hely|– $MetricsMinutePrimaryTransactionsBlob<br />– $MetricsMinutePrimaryTransactionsTable<br />– $MetricsMinutePrimaryTransactionsQueue<br />– $MetricsMinutePrimaryTransactionsFile|Minden verzió. A file Service-metrikák támogatása csak a 2015-es és újabb verziókban érhető el.|  
|Óránkénti metrika, másodlagos hely|– $MetricsHourSecondaryTransactionsBlob<br />– $MetricsHourSecondaryTransactionsTable<br />– $MetricsHourSecondaryTransactionsQueue|Minden verzió. Az olvasási hozzáférés geo-redundáns replikációját engedélyezni kell.|  
|Perc mérőszámok, másodlagos hely|– $MetricsMinuteSecondaryTransactionsBlob<br />– $MetricsMinuteSecondaryTransactionsTable<br />– $MetricsMinuteSecondaryTransactionsQueue|Minden verzió. Az olvasási hozzáférés geo-redundáns replikációját engedélyezni kell.|  
|Kapacitás (csak blob-szolgáltatás)|$MetricsCapacityBlob|Minden verzió.|  

 Ezek a táblák automatikusan létrejönnek, ha Storage Analytics van engedélyezve a tárolási szolgáltatás végpontja számára. Ezek a Storage-fiók névterén keresztül érhetők el, például: `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")` . A metrikák táblázat nem jelenik meg a listázási műveletben, és közvetlenül a tábla nevén keresztül kell elérni.

## <a name="enable-metrics-by-using-the-azure-portal"></a>Metrikák engedélyezése a Azure Portal használatával
Az alábbi lépéseket követve engedélyezheti a metrikákat a [Azure Portalban](https://portal.azure.com):

1. Nyissa meg a Storage-fiókját.
1. Válassza a **diagnosztikai beállítások (klasszikus)** lehetőséget a menü ablaktáblán.
1. Győződjön **meg**arról, hogy az **állapot** beállítása be értékre van állítva.
1. Válassza ki a figyelni kívánt szolgáltatások mérőszámait.
1. Adja meg az adatmegőrzési szabályzatot, amely azt jelzi, hogy meddig kell megőrizni a metrikákat és a naplózási adatokat.
1. Kattintson a **Mentés** gombra.

A [Azure Portal](https://portal.azure.com) jelenleg nem teszi lehetővé perc típusú metrikák konfigurálását a Storage-fiókban. A perc típusú metrikákat a PowerShell vagy programozott módon kell engedélyeznie.

## <a name="enable-storage-metrics-by-using-powershell"></a>Tárolási mérőszámok engedélyezése a PowerShell használatával  
A helyi gépen található PowerShell használatával konfigurálhatja a Storage-fiók tárolási metrikáit az aktuális beállítások lekéréséhez a **Get-AzStorageServiceMetricsProperty Azure PowerShell-** parancsmag használatával. Az aktuális beállítások módosításához használja a **set-AzStorageServiceMetricsProperty** parancsmagot.  

A tárolási metrikákat vezérlő parancsmagok a következő paramétereket használják:  

* **ServiceType**: a lehetséges értékek a **blob**, **üzenetsor**, **tábla**és **fájl**.
* **MetricsType**: a lehetséges értékek **óra** és **perc**.  
* **MetricsLevel**: a lehetséges értékek a következők:
   * **Nincs**: a figyelés kikapcsolása.
   * **Szolgáltatás**: olyan mérőszámokat gyűjt, mint a bejövő és a kimenő forgalom, a rendelkezésre állás, a késés és a sikerességi arány, amely a blob, a várólista, a tábla és a Fájlszolgáltatások esetében összesítve van.
   * **ServiceAndApi**: a szolgáltatási metrikák mellett az Azure Storage szolgáltatás API-ban minden tárolási művelethez ugyanazt a metrikát gyűjti.

Például a következő parancs a Storage-fiókban lévő blob-szolgáltatás perc mérőszámait kapcsolja be, a megőrzési időtartam pedig öt napra van beállítva: 

> [!NOTE]
> Ez a parancs feltételezi, hogy az paranccsal bejelentkezett az Azure-előfizetésbe `Connect-AzAccount` .

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"

Set-AzStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5 -Context $storageAccount.Context
```  

* Cserélje le a `<resource-group-name>` helyőrző értékét az erőforráscsoport nevére.      
* Cserélje le a `<storage-account-name>` helyőrző értékét a Storage-fiók nevére.



A következő parancs lekérdezi a blob szolgáltatás aktuális óránkénti metrikáit és megőrzési napjait az alapértelmezett Storage-fiókban:  

```powershell
Get-AzStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob -Context $storagecontext.Context
```  

Az Azure PowerShell-parancsmagoknak az Azure-előfizetéssel való használatához való konfigurálásával és a használni kívánt alapértelmezett Storage-fiók kiválasztásával kapcsolatos információkért lásd: [Azure PowerShell telepítése és konfigurálása](https://azure.microsoft.com/documentation/articles/install-configure-powershell/).  

## <a name="enable-storage-metrics-programmatically"></a>A tárolási mérőszámok programozott módon történő engedélyezése  
Amellett, hogy a Azure Portal vagy a Azure PowerShell parancsmagokat használja a tárolási metrikák szabályozására, használhatja az egyik Azure Storage API-t is. Ha például .NET nyelvet használ, használhatja az Azure Storage ügyféloldali kódtárat.  

A **CloudBlobClient**, a **CloudQueueClient**, a **CloudTableClient**és a **CloudFileClient** osztályok minden olyan metódussal rendelkeznek, mint például a **SetServiceProperties** **és a SetServicePropertiesAsync,** amelyek paraméterként egy **ServiceProperties** objektumot készítenek. A **ServiceProperties** objektum használatával konfigurálhatja a tárolási metrikákat. Az alábbi C#-kódrészlet például azt mutatja be, hogyan módosíthatja a metrikák szintjét és a megőrzési napokat az óránkénti üzenetsor-metrikák esetében:  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.HourMetrics.MetricsLevel = MetricsLevel.Service;  
serviceProperties.HourMetrics.RetentionDays = 10;  

queueClient.SetServiceProperties(serviceProperties);  
```  

További információ a tárolási metrikák konfigurálásáról .NET-nyelv használatával: [Azure Storage ügyféloldali kódtárak a .net-hez](https://msdn.microsoft.com/library/azure/mt347887.aspx).  

A tárolási metrikák REST API használatával történő konfigurálásával kapcsolatos általános információkért lásd: [Storage Analytics engedélyezése és konfigurálása](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics).  

##  <a name="view-storage-metrics"></a>Tárolási metrikák megtekintése  
Miután konfigurálta Storage Analytics mérőszámokat a Storage-fiók figyeléséhez, Storage Analytics rögzíti a metrikákat a Storage-fiók jól ismert tábláiban. A diagramokat konfigurálhatja a [Azure Portal](https://portal.azure.com)óránkénti metrikáinak megtekintéséhez:

1. Nyissa meg a Storage-fiókját a [Azure Portal](https://portal.azure.com).
1. Válassza a **metrikák (klasszikus)** lehetőséget azon szolgáltatás menüjében, amelynek a metrikáit meg szeretné tekinteni.
1. Válassza ki a konfigurálni kívánt diagramot.
1. A **diagram szerkesztése** ablaktáblán válassza ki az **időtartományt**, a **diagram típusát**és a diagramon megjeleníteni kívánt metrikákat.

A Storage-fiókja Azure Portal menüjének **figyelés (klasszikus)** szakaszában beállíthatja a [riasztási szabályokat](#metrics-alerts). Küldhet például e-mailes riasztásokat, hogy értesítést kapjon, ha egy adott metrika elér egy bizonyos értéket.

Ha le szeretné tölteni a mérőszámokat a hosszú távú tároláshoz, vagy helyileg kívánja elemezni őket, egy eszközt kell használnia, vagy írnia kell egy kódot a táblák olvasásához. Az elemzéshez le kell töltenie a perc mérőszámait. A táblák nem jelennek meg, ha a Storage-fiókban lévő összes táblát listázza, de közvetlenül a név alapján érheti el őket. Számos Storage-szemöldök eszköz ismeri ezeket a táblázatokat, és lehetővé teszi, hogy közvetlenül megtekintse őket. Az elérhető eszközök listáját az [Azure Storage-ügyféleszközök](/azure/storage/storage-explorers)című témakörben tekintheti meg.

||||  
|-|-|-|  
|**Metrikák**|**Táblák nevei**|**Megjegyzések**|  
|Óránkénti mérőszámok|$MetricsHourPrimaryTransactionsBlob<br /><br /> $MetricsHourPrimaryTransactionsTable<br /><br /> $MetricsHourPrimaryTransactionsQueue<br /><br /> $MetricsHourPrimaryTransactionsFile|A 2013. augusztus 15. előtti verziókban ezek a táblázatok a következőképpen ismertek:<br /><br /> $MetricsTransactionsBlob<br /><br /> $MetricsTransactionsTable<br /><br /> $MetricsTransactionsQueue<br /><br /> A file Service metrikái a 2015-as verziótól kezdődően érhetők el.|  
|Perc mérőszámok|$MetricsMinutePrimaryTransactionsBlob<br /><br /> $MetricsMinutePrimaryTransactionsTable<br /><br /> $MetricsMinutePrimaryTransactionsQueue<br /><br /> $MetricsMinutePrimaryTransactionsFile|Csak PowerShell használatával vagy programozott módon engedélyezhető.<br /><br /> A file Service metrikái a 2015-as verziótól kezdődően érhetők el.|  
|Kapacitás|$MetricsCapacityBlob|Csak Blob service.|  

A táblázatok sémáinak részletes ismertetését lásd: [Storage Analytics mérőszámok tábla sémája](/rest/api/storageservices/storage-analytics-metrics-table-schema). A következő minta sorok csak az elérhető oszlopok egy részhalmazát jelenítik meg, de a tárolási metrikák egyes fontos funkcióit illusztrálják a metrikák mentéséhez:  

||||||||||||  
|-|-|-|-|-|-|-|-|-|-|-|  
|**PartitionKey**|**RowKey**|**Időbélyeg**|**TotalRequests**|**TotalBillableRequests**|**TotalIngress**|**TotalEgress**|**Rendelkezésre állás**|**AverageE2ELatency**|**AverageServerLatency**|**PercentSuccess**|  
|20140522T1100|felhasználói Összes|2014-05-22T11:01:16.7650250 Z|7|7|4003|46801|100|104,4286|6,857143|100|  
|20140522T1100|felhasználói QueryEntities|2014-05-22T11:01:16.7640250 Z|5|5|2694|45951|100|143,8|7.8|100|  
|20140522T1100|felhasználói QueryEntity|2014-05-22T11:01:16.7650250 Z|1|1|538|633|100|3|3|100|  
|20140522T1100|felhasználói UpdateEntity|2014-05-22T11:01:16.7650250 Z|1|1|771|217|100|9|6|100|  

Ebben a példában a perc mérőszámok adatainál a partíciós kulcs a percenkénti felbontásban használja az időt. A sor kulcsa azonosítja a sorban tárolt információ típusát. Az információ a hozzáférési típustól és a kérelem típusától tevődik össze:  

-   A hozzáférési típus vagy **felhasználó** vagy **rendszer**, ahol a **felhasználó** a tárolási szolgáltatáshoz tartozó összes felhasználói kérésre hivatkozik, és a **rendszer** a Storage Analytics által benyújtott kérelmekre hivatkozik.  
-   A kérelem típusa vagy **mind**, ebben az esetben ez egy összesítő sor, vagy azonosítja az adott API-t (például **QueryEntity** vagy **UpdateEntity**).  

Ez a mintaadatok egy percen belüli összes rekordot jeleníti meg (a 11-es naptól számítva), így a **QueryEntities** -kérések száma, valamint a **QueryEntity** -kérések száma és a **UpdateEntity** -kérelmek száma akár hét is felveszi. Ez az összeg a **felhasználó: minden** sorban látható. Hasonlóképpen, az átlagos végpontok közötti késés 104,4286 a **felhasználónál: az összes** sor kiszámításával ((143,8 * 5) + 3 + 9)/7.  

## <a name="metrics-alerts"></a>Metrikák riasztásai
Érdemes lehet riasztásokat beállítani a [Azure Portalban](https://portal.azure.com) , hogy automatikusan értesüljön a tárolási szolgáltatások működésével kapcsolatos fontos változásokról. Ha Storage Explorer eszközzel tölti le ezeket a metrikákat, a Microsoft Excel segítségével elemezheti az adatokat. Az elérhető Storage Explorer eszközök listáját az [Azure Storage-ügyféleszközök](/azure/storage/storage-explorers)című témakörben tekintheti meg. Riasztásokat konfigurálhat a **riasztás (klasszikus)** ablaktáblán, amely a **figyelés (klasszikus)** területen érhető el a Storage-fiók menü paneljén.

> [!IMPORTANT]
> Előfordulhat, hogy a tárolási esemény és a megfelelő óránkénti vagy perces metrikai adatok rögzítése között késés történik. Percenkénti metrika esetén több percnyi adat is írható egyszerre. Ez a probléma a korábbi percekben lebonyolított tranzakciókat eredményezheti az aktuális percek tranzakciójában. Ha ez a probléma történik, előfordulhat, hogy a riasztási szolgáltatás nem rendelkezik az összes rendelkezésre álló metrikai adattal a beállított riasztási intervallumhoz, ami a riasztások váratlan elégetését eredményezheti.
>

## <a name="access-metrics-data-programmatically"></a>A metrikák adatai programozott módon érhetők el  
Az alábbi lista a C#-kódot mutatja be, amely egy percen belül fér hozzá a perc mérőszámokhoz, és megjeleníti az eredményeket egy konzolablak-ablakban. A kód minta az Azure Storage ügyféloldali kódtára 4. x vagy újabb verzióját használja, amely a **CloudAnalyticsClient** osztályt is tartalmazza, amely leegyszerűsíti a tárolóban lévő metrikai táblák elérését.  

```csharp
private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)  
{  
 // Convert the dates to the format used in the PartitionKey.  
 var start = startDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");  
 var end = endDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");  

 var services = Enum.GetValues(typeof(StorageService));  
 foreach (StorageService service in services)  
 {  
     Console.WriteLine("Minute Metrics for Service {0} from {1} to {2} UTC", service, start, end);  
     var metricsQuery = analyticsClient.CreateMinuteMetricsQuery(service, StorageLocation.Primary);  
     var t = analyticsClient.GetMinuteMetricsTable(service);  
     var opContext = new OperationContext();  
     var query =  
             from entity in metricsQuery  
             // Note, you can't filter using the entity properties Time, AccessType, or TransactionType  
             // because they are calculated fields in the MetricsEntity class.  
             // The PartitionKey identifies the DataTime of the metrics.  
             where entity.PartitionKey.CompareTo(start) >= 0 && entity.PartitionKey.CompareTo(end) <= 0   
             select entity;  

     // Filter on "user" transactions after fetching the metrics from Azure Table storage.  
     // (StartsWith is not supported using LINQ with Azure Table storage.)  
     var results = query.ToList().Where(m => m.RowKey.StartsWith("user"));  
     var resultString = results.Aggregate(new StringBuilder(), (builder, metrics) => builder.AppendLine(MetricsString(metrics, opContext))).ToString();  
     Console.WriteLine(resultString);  
 }  
}  

private static string MetricsString(MetricsEntity entity, OperationContext opContext)  
{  
 var entityProperties = entity.WriteEntity(opContext);  
 var entityString =  
         string.Format("Time: {0}, ", entity.Time) +  
         string.Format("AccessType: {0}, ", entity.AccessType) +  
         string.Format("TransactionType: {0}, ", entity.TransactionType) +  
         string.Join(",", entityProperties.Select(e => new KeyValuePair<string, string>(e.Key.ToString(), e.Value.PropertyAsObject.ToString())));  
 return entityString;  
}  
```  

## <a name="billing-on-storage-metrics"></a>Számlázás a tárolási mérőszámokon
A táblázatos entitások metrikák létrehozására vonatkozó írási kéréseket az összes Azure Storage-műveletre érvényes standard díjszabás szerint számítjuk fel.  

A metrikai adatoknak az ügyfelek által küldött olvasási és törlési kérelmei a standard díjszabás szerint is számlázva vannak. Ha adatmegőrzési szabályzatot állított be, akkor nem számítunk fel díjat, ha az Azure Storage törli a régi metrikák adatait. Ha törli az elemzési adatok mennyiségét, a fiók a törlési műveletekért kell fizetnie.  

A metrikák táblái által használt kapacitás is számlázható. A következő információk segítségével becsülheti meg a metrikák adatainak tárolására használt kapacitás mennyiségét:  

-   Ha minden órában a szolgáltatás minden API-t használ minden szolgáltatásban, körülbelül 148 KB adat tárolódik óránként a metrikák tranzakciós tábláiban, ha engedélyezte a szolgáltatási szintet és az API-szintű összegzést.  
-   Ha minden órában a szolgáltatás minden API-t használ, a metrikák tranzakciós tábláiban körülbelül 12 KB adat tárolódik, ha csak egy szolgáltatási szintű összegzést engedélyez.  
-   A Blobok kapacitási táblája két sorral bővült, amelyeket a naplókhoz választott ki. Ez a forgatókönyv azt feltételezi, hogy a táblázat mérete minden nap körülbelül 300 bájttal nő.

## <a name="next-steps"></a>További lépések
* [Tárfiók figyelése](https://www.windowsazure.com/manage/services/storage/how-to-monitor-a-storage-account/)   
* [Storage Analytics mérőszámok táblázatának sémája](/rest/api/storageservices/storage-analytics-metrics-table-schema)   
* [Naplózott műveletek és állapotüzenetek Storage Analytics](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)   
* [Storage Analytics naplózás](storage-analytics-logging.md)

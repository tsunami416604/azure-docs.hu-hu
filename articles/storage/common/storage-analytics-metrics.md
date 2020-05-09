---
title: Azure Storage Analytics-metrikák (klasszikus)
description: Ismerje meg, hogyan használhatók a metrikák az Azure Storage-ban.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 03e5f1e888680f6020b45f51103e7b5cb6dc86ab
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82692731"
---
# <a name="azure-storage-analytics-metrics-classic"></a>Azure Storage Analytics-metrikák (klasszikus)

A Storage Analytics olyan metrikákat tárolhat, amelyek összesített tranzakciós statisztikát és kapacitási adatokat tartalmaznak a tárolási szolgáltatásokra irányuló kérések esetén. A tranzakciókat az API működési szintjén, valamint a tárolási szolgáltatás szintjén is jelenteni kell, és a kapacitást a tárolási szolgáltatás szintjén kell jelenteni. A metrikák adatai a tárolási szolgáltatások használatának elemzésére, a tárolási szolgáltatással kapcsolatos kérések diagnosztizálására, valamint a szolgáltatást használó alkalmazások teljesítményének javítására használhatók.  

 Storage Analytics metrikák alapértelmezés szerint engedélyezve vannak az új Storage-fiókok esetében. A metrikákat a [Azure Portalban](https://portal.azure.com/)is konfigurálhatja; További információ: [Storage-fiók figyelése a Azure Portalban](/azure/storage/storage-monitor-storage-account). Storage Analytics programozott módon is engedélyezheti a REST API vagy az ügyféloldali kódtár használatával. A szolgáltatás tulajdonságainak beállítása művelettel engedélyezheti a Storage Analytics az egyes szolgáltatásokhoz.  

> [!NOTE]
> A blob-, üzenetsor-, tábla-és Fájlszolgáltatások Storage Analytics metrikák érhetők el.
> Storage Analytics metrikák mostantól klasszikus metrikák. A Microsoft azt javasolja, hogy Storage Analytics mérőszámok helyett [Azure monitor tárolási metrikákat](monitor-storage.md) használjon.

## <a name="transaction-metrics"></a>Tranzakciómetrikák  
 Az egyes tárolási szolgáltatások és a kért API-művelet (beleértve a bejövő/kimenő forgalmat, a rendelkezésre állást, a hibákat és a kategorizált kérelmek százalékos arányát) óránként vagy percenként rögzíti a robusztus adatkészletet. A tranzakció részleteinek teljes listáját a [Storage Analytics mérőszámok tábla sémája](/rest/api/storageservices/storage-analytics-metrics-table-schema) című témakörben tekintheti meg.  

 A tranzakciós adatai két szinten vannak rögzítve – a szolgáltatási szint és az API-művelet szintjén. A szolgáltatási szinten az összes kért API-művelet összefoglalása óránként történik, még akkor is, ha a szolgáltatáshoz nem érkezett kérelem. Az API működési szintjén a statisztikák csak akkor íródnak az entitásba, ha a műveletet az adott órán belül kérték.  

 Ha például **GetBlob** műveletet hajt végre a blob serviceon, Storage Analytics metrikák naplózzák a kérést, és belefoglalják a blob Service és a **GetBlob** művelet összesített adataiba is. Ha azonban a rendszer nem kér **GetBlob** műveletet az óra során, az entitás nem kerül a művelet *$MetricsTransactionsBlob* .  

 A tranzakciós metrikák a felhasználói kérések és a Storage Analytics saját maga által benyújtott kérelmek esetében is rögzítésre kerülnek. A naplók és a tábla entitások írásához Storage Analytics kérelmeket például rögzíti a rendszer.

## <a name="capacity-metrics"></a>Kapacitásmetrikák  

> [!NOTE]
>  A kapacitás-metrikák jelenleg csak a Blob service számára érhetők el.

 A rendszer naponta rögzíti a kapacitási adatkészleteket a Storage-fiókok Blob serviceéhez, és két tábla entitást ír. Az egyik entitás statisztikai adatokat biztosít a felhasználói adatokhoz, a másik pedig a `$logs` Storage Analytics által használt BLOB-tároló statisztikáit tartalmazza. A *$MetricsCapacityBlob* tábla a következő statisztikákat tartalmazza:  

- **Kapacitás**: a Storage-fiók blob Service által felhasznált tárterület mérete bájtban kifejezve.  
- **ContainerCount**: a Storage-fiók blob Service található blob-tárolók száma.  
- **ObjectCount**: a Storage-fiók blob serviceban lévő véglegesített és nem véglegesített blokk-vagy Blobok száma.  

  További információ a kapacitási metrikákkal kapcsolatban: [Storage Analytics mérőszámok tábla sémája](/rest/api/storageservices/storage-analytics-metrics-table-schema).  

## <a name="how-metrics-are-stored"></a>A metrikák tárolása  

 Az egyes tárolási szolgáltatásokhoz tartozó összes metrikai adatot az adott szolgáltatás számára fenntartott három táblában tároljuk: egy tábla a tranzakciós adatokhoz, egy táblázat a percenkénti tranzakciós adatokhoz, valamint egy másik tábla a kapacitással kapcsolatos információkhoz. A tranzakciós és a perces tranzakciós adatok kérelemből és válaszokból állnak, és a Kapacitási adatok tárolási használati adatokból állnak. Az óra mérőszámai, a perc mérőszámai és a Storage-fiók Blob serviceának kapacitása a következő táblázatban leírtaknak megfelelően elérhető táblákban érhető el.  

|Metrikák szintje|Táblák nevei|Verziók esetében támogatott|  
|-------------------|-----------------|----------------------------|  
|Óránkénti mérőszámok, elsődleges hely|– $MetricsTransactionsBlob<br />– $MetricsTransactionsTable<br />– $MetricsTransactionsQueue|Csak 2013-08-15-nél korábbi verziók. Habár ezek a nevek továbbra is támogatottak, javasoljuk, hogy váltson az alább felsorolt táblázatok használatára.|  
|Óránkénti mérőszámok, elsődleges hely|– $MetricsHourPrimaryTransactionsBlob<br />– $MetricsHourPrimaryTransactionsTable<br />– $MetricsHourPrimaryTransactionsQueue<br />– $MetricsHourPrimaryTransactionsFile|Minden verzió. A file Service-metrikák támogatása csak a 2015-04-05-es és újabb verziókban érhető el.|  
|Perc mérőszámok, elsődleges hely|– $MetricsMinutePrimaryTransactionsBlob<br />– $MetricsMinutePrimaryTransactionsTable<br />– $MetricsMinutePrimaryTransactionsQueue<br />– $MetricsMinutePrimaryTransactionsFile|Minden verzió. A file Service-metrikák támogatása csak a 2015-04-05-es és újabb verziókban érhető el.|  
|Óránkénti metrika, másodlagos hely|– $MetricsHourSecondaryTransactionsBlob<br />– $MetricsHourSecondaryTransactionsTable<br />– $MetricsHourSecondaryTransactionsQueue|Minden verzió. Az olvasási hozzáférés geo-redundáns replikációját engedélyezni kell.|  
|Perc mérőszámok, másodlagos hely|– $MetricsMinuteSecondaryTransactionsBlob<br />– $MetricsMinuteSecondaryTransactionsTable<br />– $MetricsMinuteSecondaryTransactionsQueue|Minden verzió. Az olvasási hozzáférés geo-redundáns replikációját engedélyezni kell.|  
|Kapacitás (csak Blob service)|$MetricsCapacityBlob|Minden verzió.|  

 Ezek a táblák automatikusan létrejönnek, ha Storage Analytics van engedélyezve a tárolási szolgáltatás végpontja számára. Ezek a Storage-fiók névterén keresztül érhetők el, például: `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`. A metrikák táblázat nem jelenik meg egy listaelem-műveletben, és közvetlenül a táblanév használatával kell elérni.  

## <a name="enable-metrics-using-the-azure-portal"></a>Metrikák engedélyezése a Azure Portal használatával
Az alábbi lépéseket követve engedélyezheti a metrikákat a [Azure Portalban](https://portal.azure.com):

1. Nyissa meg a tárfiókot.
1. Válassza a **diagnosztikai beállítások (klasszikus)** lehetőséget a **menü** ablaktáblán.
1. Győződjön **meg**arról, hogy az **állapot** beállítása be értékre van állítva.
1. Válassza ki a figyelni kívánt szolgáltatások mérőszámait.
1. Adja meg az adatmegőrzési szabályzatot, amely azt jelzi, hogy meddig kell megőrizni a metrikákat és a naplózási adatokat.
1. Kattintson a **Mentés** gombra.

A [Azure Portal](https://portal.azure.com) jelenleg nem teszi lehetővé a percenkénti mérőszámok konfigurálását a Storage-fiókban; a perc típusú metrikákat a PowerShell vagy programozott módon kell engedélyeznie.

## <a name="enable-storage-metrics-using-powershell"></a>Tárolási metrikák engedélyezése a PowerShell használatával  
A helyi gépen található PowerShell használatával konfigurálhatja a Storage-fiók tárolási metrikáit a **Get-AzStorageServiceMetricsProperty Azure PowerShell-** parancsmag használatával a jelenlegi beállítások lekéréséhez, valamint a **set-AzStorageServiceMetricsProperty** parancsmagot az aktuális beállítások módosításához.  

A tárolási metrikákat vezérlő parancsmagok a következő paramétereket használják:  

* A **ServiceType**, a lehetséges érték a **blob**, a **várólista**, a **tábla**és a **fájl**.
* A **MetricsType**a lehetséges értékek: **óra** és **perc**.  
* **MetricsLevel**a lehetséges értékek a következők:
* **Nincs**: a figyelés kikapcsolása.
* **Szolgáltatás**: olyan mérőszámokat gyűjt, mint a bejövő/kimenő forgalom, a rendelkezésre állás, a késés és a sikerességi arányok, amelyek a blob, a várólista, a tábla és a Fájlszolgáltatások esetében összesítve vannak.
* **ServiceAndApi**: a szolgáltatási metrikák mellett az Azure Storage szolgáltatás API-ban minden tárolási művelethez ugyanazt a metrikát gyűjti.

Például a következő parancs a Storage-fiókban lévő blob-szolgáltatás perc mérőszámait kapcsolja be, a megőrzési időtartam pedig öt napra van beállítva: 

> [!NOTE]
> Ez a parancs feltételezi, hogy az `Connect-AzAccount` paranccsal bejelentkezett az Azure-előfizetésbe.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"

Set-AzStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5 -Context $storageAccount.Context
```  

* Cserélje le `<resource-group-name>` a helyőrző értékét az erőforráscsoport nevére.
        
* Cserélje le `<storage-account-name>` a helyőrző értékét a Storage-fiók nevére.



A következő parancs lekérdezi a blob szolgáltatás aktuális óránkénti metrikáit és megőrzési napjait az alapértelmezett Storage-fiókban:  

```powershell
Get-AzStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob -Context $storagecontext.Context
```  

Az Azure PowerShell-parancsmagoknak az Azure-előfizetéssel való használatához való konfigurálásával és a használni kívánt alapértelmezett Storage-fiók kiválasztásával kapcsolatos információkért lásd: [Azure PowerShell telepítése és konfigurálása](https://azure.microsoft.com/documentation/articles/install-configure-powershell/).  

## <a name="enable-storage-metrics-programmatically"></a>A tárolási mérőszámok programozott módon történő engedélyezése  
Amellett, hogy a Azure Portal vagy a Azure PowerShell parancsmagokat használja a tárolási metrikák szabályozására, használhatja az egyik Azure Storage API-t is. Ha például .NET nyelvet használ, használhatja a Storage ügyféloldali kódtárat.  

A **CloudBlobClient**, a **CloudQueueClient**, a **CloudTableClient**és a **CloudFileClient** osztályok minden olyan metódussal rendelkeznek, mint például a **SetServiceProperties** **és a SetServicePropertiesAsync,** amelyek paraméterként egy **ServiceProperties** objektumot készítenek. A **ServiceProperties** objektum használatával konfigurálhatja a tárolási metrikákat. Az alábbi C#-kódrészlet például azt mutatja be, hogyan módosíthatja a metrikák szintjét és a megőrzési napokat az óránkénti üzenetsor-metrikák esetében:  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.HourMetrics.MetricsLevel = MetricsLevel.Service;  
serviceProperties.HourMetrics.RetentionDays = 10;  

queueClient.SetServiceProperties(serviceProperties);  
```  

További információ a tárolási metrikák konfigurálásáról .NET-nyelv használatával: [Storage ügyféloldali kódtára a .net-hez](https://msdn.microsoft.com/library/azure/mt347887.aspx).  

A tárolási metrikák REST API használatával történő konfigurálásával kapcsolatos általános információkért lásd: [Storage Analytics engedélyezése és konfigurálása](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics).  

##  <a name="viewing-storage-metrics"></a>Tárolási metrikák megtekintése  
Miután konfigurálta Storage Analytics mérőszámokat a Storage-fiók figyeléséhez, Storage Analytics rögzíti a metrikákat a Storage-fiók jól ismert tábláiban. A diagramokat konfigurálhatja a [Azure Portal](https://portal.azure.com)óránkénti metrikáinak megtekintéséhez:

1. Navigáljon a Storage-fiókjához a [Azure Portal](https://portal.azure.com).
1. Válassza ki a **metrikák (klasszikus)** elemet azon szolgáltatás **menüjében** , amelynek a metrikáit meg szeretné tekinteni.
1. Kattintson a konfigurálni kívánt diagramra.
1. A **diagram szerkesztése** panelen válassza ki az **időtartományt**, a **diagram típusát**és a diagramon megjeleníteni kívánt metrikákat.

A Storage-fiók menüjének **monitorozás (klasszikus)** szakaszában a Azure Portalban beállíthatja a [riasztási szabályokat](#metrics-alerts), például az e-mailes riasztások küldését, hogy értesítést kapjon, ha egy adott metrika elér egy bizonyos értéket.

Ha le szeretné tölteni a mérőszámokat a hosszú távú tároláshoz, vagy helyileg kívánja elemezni őket, egy eszközt kell használnia, vagy írnia kell egy kódot a táblák olvasásához. Az elemzéshez le kell töltenie a perc mérőszámait. A táblák nem jelennek meg, ha a Storage-fiókban lévő összes táblát listázza, de közvetlenül a név alapján érheti el őket. Számos tárolási és böngészési eszköz ismeri ezeket a táblázatokat, és lehetővé teszi a közvetlen megtekintését (lásd az [Azure Storage-ügyféleszközök](/azure/storage/storage-explorers) listáját az elérhető eszközök listájához).

||||  
|-|-|-|  
|**Mérőszámok**|**Táblák nevei**|**Megjegyzések**|  
|Óránkénti mérőszámok|$MetricsHourPrimaryTransactionsBlob<br /><br /> $MetricsHourPrimaryTransactionsTable<br /><br /> $MetricsHourPrimaryTransactionsQueue<br /><br /> $MetricsHourPrimaryTransactionsFile|A 2013-08-15 előtti verziókban ezek a táblák a következőképpen ismertek:<br /><br /> $MetricsTransactionsBlob<br /><br /> $MetricsTransactionsTable<br /><br /> $MetricsTransactionsQueue<br /><br /> A Fájlszolgáltatások metrikái az 2015-04-05-es verziótól kezdve érhetők el.|  
|Perc mérőszámok|$MetricsMinutePrimaryTransactionsBlob<br /><br /> $MetricsMinutePrimaryTransactionsTable<br /><br /> $MetricsMinutePrimaryTransactionsQueue<br /><br /> $MetricsMinutePrimaryTransactionsFile|Csak a PowerShell vagy programozott módon engedélyezhető.<br /><br /> A Fájlszolgáltatások metrikái az 2015-04-05-es verziótól kezdve érhetők el.|  
|Kapacitás|$MetricsCapacityBlob|Csak Blob service.|  

A táblázatok sémáinak részletes ismertetését [Storage Analytics mérőszámok tábla sémájában](/rest/api/storageservices/storage-analytics-metrics-table-schema)találja. Az alábbi minta sorok csak a rendelkezésre álló oszlopok egy részhalmazát mutatják be, de a tárolási metrikák egyes fontos funkcióit a következő metrikák mentik:  

||||||||||||  
|-|-|-|-|-|-|-|-|-|-|-|  
|**PartitionKey**|**RowKey**|**Időbélyeg**|**TotalRequests**|**TotalBillableRequests**|**TotalIngress**|**TotalEgress**|**Rendelkezésre állás**|**AverageE2ELatency**|**AverageServerLatency**|**PercentSuccess**|  
|20140522T1100|felhasználói Összes|2014-05-22T11:01:16.7650250 Z|7|7|4003|46801|100|104,4286|6,857143|100|  
|20140522T1100|felhasználói QueryEntities|2014-05-22T11:01:16.7640250 Z|5|5|2694|45951|100|143,8|7.8|100|  
|20140522T1100|felhasználói QueryEntity|2014-05-22T11:01:16.7650250 Z|1|1|538|633|100|3|3|100|  
|20140522T1100|felhasználói UpdateEntity|2014-05-22T11:01:16.7650250 Z|1|1|771|217|100|9|6|100|  

Ebben a példában a perc mérőszámok adataiban a partíciós kulcs az időt percenkénti felbontásban használja. A sor kulcsa azonosítja a sorban tárolt információ típusát, és ez két adatból, a hozzáférési típusból és a kérelem típusától tevődik össze:  

-   A hozzáférési típus vagy **felhasználó** vagy **rendszer**, ahol a **felhasználó** a tárolási szolgáltatás összes felhasználói kérelmére hivatkozik, és a **rendszer** a Storage Analytics által benyújtott kérelmekre hivatkozik.  

-   A kérelem típusa vagy **minden** , amely esetben ez egy összegző sor, vagy azonosítja az adott API-t (például **QueryEntity** vagy **UpdateEntity**).  

A fenti mintaadatok egy percen belüli összes rekordot jelenítik meg (a 11. naptól kezdődően), így a **QueryEntities** -kérések száma, valamint a **QueryEntity** -kérések száma, valamint a **UpdateEntity** -kérések száma legfeljebb hét, amely a felhasználó által megjelenített teljes összeg **: az összes** sor. Hasonlóképpen, az átlagos végpontok közötti késés 104,4286 a **felhasználónál: az összes** sor kiszámításával ((143,8 * 5) + 3 + 9)/7.  

## <a name="metrics-alerts"></a>Metrikák riasztásai
Érdemes megfontolnia a riasztások beállítását a [Azure Portalban](https://portal.azure.com) , hogy automatikusan értesüljön a tárolási szolgáltatások működésével kapcsolatos fontos változásokról. Ha Storage Explorer eszközzel tölti le ezt a metrikát, akkor a Microsoft Excel használatával elemezheti az adatokat. Az elérhető Storage Explorer-eszközök listáját az [Azure Storage ügyféleszközök](/azure/storage/storage-explorers) című témakörben tekintheti meg. A riasztásokat a **(klasszikus)** panelen is konfigurálhatja, amely elérhető a **figyelés (klasszikus)** elemnél a Storage-fiók menü paneljén.

> [!IMPORTANT]
> Előfordulhat, hogy a tárolási esemény és a megfelelő óránkénti vagy perces metrikai adatok rögzítése között késés van. Percenkénti metrika esetén több percnyi adat is írható egyszerre. Ez olyan tranzakciókat eredményezhet, amelyeknek az előző percben való összesítése zajlik a tranzakcióban az aktuális percben. Ha ez történik, előfordulhat, hogy a riasztási szolgáltatás nem rendelkezik az összes rendelkezésre álló metrikai adattal a beállított riasztási intervallumhoz, ami a riasztások váratlan elégetését eredményezheti.
>

## <a name="accessing-metrics-data-programmatically"></a>A metrikák adatai programozott módon való elérése  
Az alábbi lista a C#-kódot mutatja be, amely egy percen belül fér hozzá a perc mérőszámokhoz, és megjeleníti az eredményeket egy konzolablak-ablakban. A kód minta az Azure Storage ügyféloldali kódtára 4. x vagy újabb verzióját használja, amely a **CloudAnalyticsClient** osztályt is tartalmazza, amely leegyszerűsíti a tárolóban lévő metrikai táblák elérését.  

```csharp
private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)  
{  
 // Convert the dates to the format used in the PartitionKey  
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

     // Filter on "user" transactions after fetching the metrics from Table Storage.  
     // (StartsWith is not supported using LINQ with Azure Table storage)  
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

A metrikai adatoknak az ügyfelek által küldött olvasási és törlési kérelmei a standard díjszabás szerint is számlázva vannak. Ha adatmegőrzési szabályzatot állított be, akkor nem számítunk fel díjat, ha az Azure Storage törli a régi metrikák adatait. Ha azonban törli az elemzési adatok mennyiségét, a fiók a törlési műveletekért kell fizetnie.  

A metrikák táblái által használt kapacitás is számlázható. A metrikai adatok tárolásához használt kapacitás mértékének becsléséhez a következőket használhatja:  

-   Ha minden órában a szolgáltatás minden egyes API-t használ minden szolgáltatásban, akkor a metrikák tranzakciós tábláiban körülbelül 148KB tárolja a rendszer, ha engedélyezte a szolgáltatás-és az API-szintű összegzést is.  
-   Ha minden órában a szolgáltatás minden API-t használ, akkor a metrikák tranzakciós tábláiban körülbelül 12KB tárolja a rendszer, ha engedélyezte a csak a szolgáltatási szint összegzését.  
-   A Blobok kapacitási táblája naponta két sorral bővült, amennyiben a naplókhoz való bejelentkezés is megtörtént. Ez azt jelenti, hogy a táblázat mérete minden nap körülbelül 300 bájttal nő.

## <a name="next-steps"></a>További lépések
* [Storage-fiók figyelése](https://www.windowsazure.com/manage/services/storage/how-to-monitor-a-storage-account/)   
* [Storage Analytics mérőszámok táblázatának sémája](/rest/api/storageservices/storage-analytics-metrics-table-schema)   
* [Naplózott műveletek és állapotüzenetek Storage Analytics](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)   
* [Storage Analytics-naplózás](storage-analytics-logging.md)

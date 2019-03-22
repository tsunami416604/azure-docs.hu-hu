---
title: Az Azure Storage analytics metrics (klasszikus)
description: Ismerje meg, hogyan használja a metrikák az Azure Storage-ban.
services: storage
author: fhryo-msft
ms.service: storage
ms.topic: article
ms.date: 03/11/2019
ms.author: fryu
ms.subservice: common
ms.openlocfilehash: 9b4bceba53658cb8ac3c73e75e0d19faf3fe3f0b
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259754"
---
# <a name="azure-storage-analytics-metrics-classic"></a>Az Azure Storage analytics metrics (klasszikus)

A Storage Analytics olyan mérőszámokat, összesített és kapacitási adatait tartalmazzák a tárolási szolgáltatáshoz érkező kérések kapcsolatban tárol. Tranzakciók jelenti az API művelet szinten, valamint a storage szolgáltatás szintjén, és a storage szolgáltatás szintjén jelentett kapacitás. Metrikaadatok is használható, elemezheti a storage szolgáltatás használatát, a storage szolgáltatás kérelmekre kapcsolatos problémák diagnosztizálása és a egy szolgáltatást használó alkalmazások teljesítményének javítása érdekében.  

 Új storage-tárfiókok alapértelmezés szerint engedélyezve vannak a Storage Analytics mérőszámainak áttekintését. Beállíthatja, hogy a metrikák a [az Azure portal](https://portal.azure.com/); további információkért lásd: [monitorozása az Azure Portal tárfiók](/azure/storage/storage-monitor-storage-account). A Storage Analytics automatizáltan a REST API-t vagy az ügyféloldali kódtár is engedélyezheti. A szolgáltatás tulajdonságainak beállítása műveletek használata a Storage Analytics engedélyezése az egyes szolgáltatások.  

> [!NOTE]
> Storage Analytics mérőszámainak áttekintését a Blob, üzenetsor, tábla és fájl szolgáltatások érhetők el.
> Storage Analytics mérőszámainak áttekintését, mostantól klasszikus metrikákat. A Microsoft használatát javasolja [Storage-mérőszámok az Azure Monitor](storage-metrics-in-azure-monitor.md) helyett a Storage Analytics mérőszámainak áttekintését.

## <a name="transaction-metrics"></a>Tranzakció-mérőszámot  
 Adatok robusztus készletét minden egyes tárolási szolgáltatás óránként vagy perces időközönként rögzíti, és a kért API-művelet, beleértve a bejövő/kimenő forgalom, a rendelkezésre állás, a hibákat, és kérelmek százalékos kategorizált. A tranzakció részleteit a teljes listáját megtekintheti a [Storage Analytics mérőszámainak Táblasémáját](/rest/api/storageservices/storage-analytics-metrics-table-schema) témakör.  

 Tranzakciós adatok két szinten – a szolgáltatási szint és az API-műveletek szintjén rögzíti. A szolgáltatási szintű összefoglalójához az összes statisztika kért API-műveleteket írja a program egy Táblaentitás óránként akkor is, ha nincsenek kérelmek történtek a szolgáltatás. Az API-művelet szintjén statisztikák csak írt entitásokhoz, ha a művelet adott órán belül a kért.  

 Például, ha hajt végre egy **GetBlob** műveletet a Blob service, a Storage Analytics mérőszámainak áttekintését a fog naplózni a kérelmet, és adja hozzá az összesített adatokat a Blob szolgáltatás, valamint a **GetBlob** a művelet. Azonban ha nem **GetBlob** művelet az adott órában van szükség, egy entitás nem ír a *$MetricsTransactionsBlob* az adott műveletnél.  

 Tranzakció-mérőszámot rögzíti a felhasználói kérések és a kérést hoz létre a Storage Analytics magát. Például a Storage Analytics naplók és táblaentitások írása kérések rögzíti.

## <a name="capacity-metrics"></a>A kapacitás-metrikák  

> [!NOTE]
>  Kapacitási jelenleg csak a Blob szolgáltatás érhető el.

 Kapacitás adatokat a rendszer naponta rögzíti a tárfiók Blob service, és két táblaentitások készültek. Egy entitás statisztika biztosít a felhasználói adatok és statisztikák kapcsolatban nyújt a másik a `$logs` Storage Analytics által használt blob-tárolóba. A *$MetricsCapacityBlob* táblázat foglalja össze a következő adatokat:  

- **Kapacitás**: A tárfiók Blob service, a memória által felhasznált tárterület mennyisége.  
- **ContainerCount**: A tárfiók Blob service-ben a blobtárolók száma.  
- **ObjectCount**: A tárfiók Blob service-ben vállalt és a nem véglegesített blokk vagy lap blobok száma.  

  A kapacitási mérőszámot kapcsolatos további információkért lásd: [Storage Analytics mérőszámainak Táblasémáját](/rest/api/storageservices/storage-analytics-metrics-table-schema).  

## <a name="how-metrics-are-stored"></a>Metrikák módjára  

 Fenntartva, hogy a szolgáltatás három táblában tárolt összes metrikák adatait a tárolási szolgáltatások mindegyike esetében: tranzakció adatainak egy táblát, egy tábla a percenkénti tranzakciós adatokat és kapacitás egy másik táblázatból. Tranzakció, és a perc tranzakcióinformációi áll kérelmek és válaszok adatait, és kapacitási adatainak a storage használati adatok áll. Óra típusú metrikák, a perc típusú metrikák és a egy Storage-fiók Blob szolgáltatás, amely az alábbi táblázatban leírtak szerint nevesített táblákat elérhető kapacitás.  

|Metrikák szint|Táblanevek|Támogatott verziók|  
|-------------------|-----------------|----------------------------|  
|Óránkénti mérőszámokat, elsődleges hely|-$MetricsTransactionsBlob<br />-$MetricsTransactionsTable<br />-   $MetricsTransactionsQueue|2013-08-15 csak előtti verziók. Miközben továbbra is támogatja ezeket a neveket, váltson át az alábbi táblázatok használata ajánlott.|  
|Óránkénti mérőszámokat, elsődleges hely|-$MetricsHourPrimaryTransactionsBlob<br />-$MetricsHourPrimaryTransactionsTable<br />-   $MetricsHourPrimaryTransactionsQueue<br />-$MetricsHourPrimaryTransactionsFile|Minden verzió. Szolgáltatási metrikák fájl támogatása csak a 2015-04-05-verzió vagy újabb.|  
|Perc típusú metrikák, elsődleges hely|-$MetricsMinutePrimaryTransactionsBlob<br />-$MetricsMinutePrimaryTransactionsTable<br />-   $MetricsMinutePrimaryTransactionsQueue<br />-   $MetricsMinutePrimaryTransactionsFile|Minden verzió. Szolgáltatási metrikák fájl támogatása csak a 2015-04-05-verzió vagy újabb.|  
|Óránkénti mérőszámot, a másodlagos helyre|-$MetricsHourSecondaryTransactionsBlob<br />-$MetricsHourSecondaryTransactionsTable<br />-   $MetricsHourSecondaryTransactionsQueue|Minden verzió. Írásvédett georedundáns replikáció engedélyezve kell lennie.|  
|Perc típusú metrikák, a másodlagos helyre|-$MetricsMinuteSecondaryTransactionsBlob<br />-   $MetricsMinuteSecondaryTransactionsTable<br />-   $MetricsMinuteSecondaryTransactionsQueue|Minden verzió. Írásvédett georedundáns replikáció engedélyezve kell lennie.|  
|Kapacitás (csak Blob szolgáltatás)|$MetricsCapacityBlob|Minden verzió.|  

 Ezek a táblák egy storage-szolgáltatásvégpont engedélyezve van a Storage Analytics automatikusan létrejönnek. Akkor érhetők el a névtér a tárfiók, például: `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`. A metrikák táblák nem jelennek meg a listázási művelet, és közvetlenül a tábla neve keresztül érhető el.  

## <a name="enable-metrics-using-the-azure-portal"></a>Engedélyezze a mérőszámok az Azure portal használatával
Kövesse az alábbi lépéseket, hogy engedélyezze a mérőszámok a a [az Azure portal](https://portal.azure.com):

1. Nyissa meg a tárfiókot.
1. Válassza ki **diagnosztikai beállítások (klasszikus)** származó a **menü** ablaktáblán.
1. Ügyeljen arra, hogy **állapot** értékre van állítva **a**.
1. Válassza ki a figyelni kívánt szolgáltatások metrikáit.
1. Adja meg, amelyek jelzik, hogy mennyi ideig mérőszámok és az adatok adatmegőrzési.
1. Kattintson a **Mentés** gombra.

A [az Azure portal](https://portal.azure.com) nem jelenleg engedélyezi a tárfiók; perc típusú metrikák beállításához engedélyeznie kell a PowerShell-lel perc típusú metrikák vagy programozott módon.

> [!NOTE]
>  Vegye figyelembe, hogy az Azure Portalon nem jelenleg teszi lehetővé a tárfiókban lévő perc típusú metrikák konfigurálása. Engedélyeznie kell a PowerShell-lel perc típusú metrikák vagy programozott módon.  

## <a name="enable-storage-metrics-using-powershell"></a>PowerShell-lel Storage mérőszámainak engedélyezése  
Ön PowerShell a helyi gépen való konfigurálásához használható Storage-mérőszámok a tárfiókban lévő Azure PowerShell-parancsmag használatával **Get-AzureStorageServiceMetricsProperty** lekérni az aktuális beállítások és a parancsmag  **Set-AzureStorageServiceMetricsProperty** az aktuális beállítások módosításához.  

A parancsmagok, amelyek vezérlik a Storage Metrics használja a következő paramétereket:  

* **ServiceType**, lehetséges értéke van **Blob**, **várólista**, **tábla**, és **fájl**.
* **MetricsType**, a lehetséges értékek: **óra** és **perc**.  
* **MetricsLevel**, lehetséges értékek a következők:
* **Nincs**: Kikapcsolja a figyelést.
* **Szolgáltatás**: Gyűjti a mérőszámokat, például a bejövő/kimenő forgalom, elérhetőség, késés és sikeres százalékok, amely a blob, queue, table és Fájlszolgáltatások összesítve szerepelnek.
* **ServiceAndApi**: A szolgáltatások mérőszámait mellett ugyanazokat az egyes tárolási műveletek az Azure Storage szolgáltatás API-ban mérőszámok gyűjti.

Az alábbi parancs például a perc típusú metrikák a alapértelmezett tárfiókban található blob szolgáltatás vált, együtt a megőrzési időszak beállítása pedig öt nappal:  

```  
Set-AzureStorageServiceMetricsProperty -MetricsType Minute   
-ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5  
```  

Az alábbi parancs lekéri a jelenlegi óránkénti metrikák szintje és megőrzési nap a blobszolgáltatás alapértelmezett tárfiókban található:  

```  
Get-AzureStorageServiceMetricsProperty -MetricsType Hour   
-ServiceType Blob  
```  

Működik az Azure-előfizetésében az Azure PowerShell-parancsmagjainak konfigurálása és használata az alapértelmezett tárfiók kiválasztása kapcsolatos információkért lásd: [Azure PowerShell telepítése és konfigurálása annak](https://azure.microsoft.com/documentation/articles/install-configure-powershell/).  

## <a name="enable-storage-metrics-programmatically"></a>Programozott módon engedélyezze a Storage-mérőszámok  
Használja az Azure portal vagy az Azure PowerShell-parancsmagok a Storage Metrics-vezérlőbe, mellett is használhatja az Azure Storage API-k közül. Ha például egy .NET-nyelv használata a Storage ügyféloldali kódtára használhatja.  

Az osztályok **CloudBlobClient**, **CloudQueueClient**, **CloudTableClient**, és **CloudFileClient** minden rendelkezik-e módszerekkel például  **SetServiceProperties** és **SetServicePropertiesAsync** hosszabb időt igénylő egy **ServiceProperties** paraméterként objektum. Használhatja a **ServiceProperties** objektum Storage Metrics konfigurálásához. Ha például a következő C# kódrészlet bemutatja, hogyan módosíthatja az óránkénti várólista mérőszámot metrikák szint és a megőrzési napok:  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.HourMetrics.MetricsLevel = MetricsLevel.Service;  
serviceProperties.HourMetrics.RetentionDays = 10;  

queueClient.SetServiceProperties(serviceProperties);  
```  

Storage-mérőszámok konfigurálása egy .NET-nyelv használatával kapcsolatos további információkért lásd: [a Storage ügyféloldali kódtára a .NET-hez](https://msdn.microsoft.com/library/azure/mt347887.aspx).  

A REST API-val Storage Metrics konfigurálásával kapcsolatos általános információkért lásd: [engedélyezése és konfigurálása a Storage Analytics](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics).  

##  <a name="viewing-storage-metrics"></a>Storage-metrikák megtekintése  
Miután konfigurálta a Storage Analytics metrikákat kíván monitorozni a storage-fiókot, a Storage Analytics rögzíti a metrikákat a tárfiókban lévő jól ismert táblák egy készlete. Konfigurálhatja a diagramot óránkénti mérőszámok megtekintéséhez a [az Azure portal](https://portal.azure.com):

1. Keresse meg a tárfiók a [az Azure portal](https://portal.azure.com).
1. Válassza ki **metrikák (klasszikus)** a a **menü** a szolgáltatás, amelynek meg szeretné tekinteni metrikák paneljén.
1. Kattintson a konfigurálni kívánt diagramot.
1. Az a **diagram szerkesztése** panelen válassza ki a **időtartomány**, **diagramtípus**, és a metrikák, amelyeket meg szeretne a diagram.

Az a **figyelés (klasszikus)** szakaszban konfigurálhatja a tárfiók menüjében panel az Azure Portalon [riasztási szabályok](#metrics-alerts), például az e-mailt küld riasztásokat, amelyek figyelmeztetik, ha az adott metrika eléri egy bizonyos értéket.

Ha azt szeretné, töltse le a metrikákat, hosszú távú tárolás céljából, vagy az elemzésük helyileg, vagy egy eszközzel kell, írjon egy kódrészletet, olvassa el a táblákat. Le kell töltenie a perc típusú metrikák, elemzés céljából. A táblák jelenik meg, ha a tárfiókban lévő összes táblát listázza, de a név alapján közvetlenül elérheti azokat. Számos tárolási böngészési eszközök ismeri ezeket a táblákat, és megtekintheti őket közvetlenül (lásd: [Azure Storage-ügyféleszközök](/azure/storage/storage-explorers) elérhető eszközök listáját).

||||  
|-|-|-|  
|**Metrikák**|**Táblanevek**|**Megjegyzések**|  
|Óránkénti metrikák|$MetricsHourPrimaryTransactionsBlob<br /><br /> $MetricsHourPrimaryTransactionsTable<br /><br /> $MetricsHourPrimaryTransactionsQueue<br /><br /> $MetricsHourPrimaryTransactionsFile|A 2013-08-15 előtti verziókban a táblák néven is ismert:<br /><br /> $MetricsTransactionsBlob<br /><br /> $MetricsTransactionsTable<br /><br /> $MetricsTransactionsQueue<br /><br /> A szolgáltatás metrikáinak elérhető elején 2015-04-05-verzióval.|  
|Perc típusú metrikák|$MetricsMinutePrimaryTransactionsBlob<br /><br /> $MetricsMinutePrimaryTransactionsTable<br /><br /> $MetricsMinutePrimaryTransactionsQueue<br /><br /> $MetricsMinutePrimaryTransactionsFile|Csak akkor engedélyezhető, PowerShell-lel vagy programozott módon.<br /><br /> A szolgáltatás metrikáinak elérhető elején 2015-04-05-verzióval.|  
|Kapacitás|$MetricsCapacityBlob|Csak a BLOB szolgáltatás.|  

Részletes információk a séma találhat meg ezen táblák [Storage Analytics mérőszámainak Táblasémáját](/rest/api/storageservices/storage-analytics-metrics-table-schema). A minta az alábbi sorokat megjelenítése csak az elérhető oszlopok egy része, de néhány fontosabb funkciói a Storage Metrics menti ezeket a metrikákat módon mutatják be:  

||||||||||||  
|-|-|-|-|-|-|-|-|-|-|-|  
|**PartitionKey**|**Rowkey tulajdonságok esetén**|**Időbélyeg**|**TotalRequests**|**TotalBillableRequests**|**TotalIngress**|**TotalEgress**|**Rendelkezésre állás**|**AverageE2ELatency**|**AverageServerLatency**|**PercentSuccess**|  
|20140522T1100|user;All|2014-05-22T11:01:16.7650250Z|7|7|4003|46801|100|104.4286|6.857143|100|  
|20140522T1100|felhasználói; QueryEntities|2014-05-22T11:01:16.7640250Z|5|5|2694|45951|100|143.8|7.8|100|  
|20140522T1100|user;QueryEntity|2014-05-22T11:01:16.7650250Z|1|1|538|633|100|3|3|100|  
|20140522T1100|user;UpdateEntity|2014-05-22T11:01:16.7650250Z|1|1|771|217|100|9|6|100|  

A példaadatokat perc típusú metrikák a partíciókulcsot használja az idő perces felbontásban. A sorkulcs azonosítja a sorban tárolt információ típusát, és ez két darab, a hozzáférés típusa, és a kérelemtípus épül fel:  

-   A hozzáférés típusa, mint **felhasználói** vagy **rendszer**, ahol **felhasználói** minden felhasználói kérések a storage szolgáltatásra hivatkozik, és **rendszer** hivatkozik a Storage Analytics által végrehajtott kéréseket.  

-   A kérelem típusa, vagy **összes** ebben az esetben összegző sort, vagy például azonosítja az adott API **QueryEntity** vagy **UpdateEntity**.  

A fenti mintaadatok minden a rekordokat jeleníti meg egy perc (11:00 órakor kezdődik), így száma **QueryEntities** száma plus-kérelmek **QueryEntity** száma plus-kérelmek  **UpdateEntity** kérések hozzáadása legfeljebb hét, amely az összes látható a **felhasználói: All** sor. Ehhez hasonlóan származtatni a átlagos végpontok közötti késése 104.4286 a a **felhasználói: All** sor szerint kiszámításának ((143.8 * 5) + 3 + 9) / 7.  

## <a name="metrics-alerts"></a>Metrikák riasztások
Vegye figyelembe a riasztások beállítását a [az Azure portal](https://portal.azure.com) , automatikusan értesítést kap, a tárolási szolgáltatások viselkedését a fontos változásokat. A storage explorer eszköz használatával töltse le a metrikákat tagolt formátumú adatokat, ha a Microsoft Excel segítségével elemezheti az adatokat. Lásd: [Azure Storage-ügyféleszközök](/azure/storage/storage-explorers) rendelkezésre álló tár explorer eszközök listáját. A riasztásokat konfigurálhat a **riasztás (klasszikus)** panelen elérhető **figyelés (klasszikus)** a Tárfiók menüjében panelén található.

> [!IMPORTANT]
> Előfordulhat, hogy egy tárolási esemény, és ha a megfelelő óránkénti vagy percenkénti metrikákat adatok rögzített közötti késleltetést jelzi. Perc típusú metrikák, esetén az adatok több percig egyszerre írhatók. Ez a tranzakcióban, a jelenlegi percen összesített korábbi perctől tranzakciók vezethet. Ha ez történik, a riasztási szolgáltatás nem lehet az összes rendelkezésre álló metrikák adatait a konfigurált, a riasztások kiváltó váratlanul vezethet riasztási időköz.
>

## <a name="accessing-metrics-data-programmatically"></a>Metrikai adatok programozott elérése  
A következő listában látható a minta C#-kódot, amely hozzáfér a különböző perc, a perc típusú metrikák, és megjeleníti az eredményeket a konzol ablakot. A kódminta használ az Azure Storage ügyféloldali kódtár verzió 4.x-es vagy újabb, amely tartalmazza a **CloudAnalyticsClient** osztály, amely leegyszerűsíti a storage-ban tabulky metrik eléréséhez.  

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

## <a name="billing-on-storage-metrics"></a>A számlázás a storage-mérőszámok  
Az írási kérések létrehozása metrikákhoz táblaentitások számoljuk el a standard díjszabás vonatkozik minden Azure Storage-műveletek.  

Olvasási és törlési kérelmek az ügyfelek számára a metrikák adatait is számlázhatók normál díjszabásnak megfelelően felszámítjuk. Ha az adatmegőrzési házirend van beállítva, nem terheli Azure Storage metrikák régi adatok törlését. Azonban ha törli az analytics-adatait, a fiók díjszabásának a törlési műveletek.  

A metrikák táblák által használt kapacitása is számlázható. A következő segítségével megbecsülheti a metrikák adatainak tárolására szolgáló kapacitását:  

-   Ha minden egyes óráért szolgáltatás már minden szolgáltatás minden API-t használja, majd 148KB adatot tárolja a tranzakciós tabulky metrik óránként Ha engedélyezte, hogy mindkét és API-szolgáltatásiszint - összefoglaló.  
-   Belül minden egyes óráért, ha egy szolgáltatás a szolgáltatás minden API-t használja, akkor körülbelül 12KB méretű adatot tárolódik a tranzakció tabulky metrik óránként, ha engedélyezte, hogy csak a szolgáltatási szintekkel összegzése.  
-   A kapacitás táblázat blobok soraiban két hozzáadott minden nap, feltéve, rendelkezik kilépteti a naplók. Ez azt jelenti, hogy minden nap, ez a tábla méretét nő legfeljebb körülbelül 300 bájt.

## <a name="next-steps"></a>További lépések
* [Tárfiók figyelése](https://www.windowsazure.com/manage/services/storage/how-to-monitor-a-storage-account/)   
* [Storage Analytics mérőszámainak Táblasémáját](/rest/api/storageservices/storage-analytics-metrics-table-schema)   
* [A Storage Analytics naplózott műveletek és a hibaállapot-üzenetek](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)   
* [A Storage Analytics naplózási](storage-analytics-logging.md)

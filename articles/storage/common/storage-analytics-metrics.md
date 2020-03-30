---
title: Azure Storage-elemzési metrikák (klasszikus)
description: Ismerje meg, hogyan használhatja a metrikákat az Azure Storage-ban.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 897ae1fa474de8726ed0caa1def162a00e142dbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268404"
---
# <a name="azure-storage-analytics-metrics-classic"></a>Azure Storage-elemzési metrikák (klasszikus)

A Storage Analytics olyan metrikákat tárolhat, amelyek összesített tranzakcióstatisztikákat és a tárolási szolgáltatásnak küldött kérelmekkapacitási adatait tartalmazzák. A tranzakciók mind az API-művelet szintjén, mind a tárolási szolgáltatás szintjén, a kapacitás pedig a tárolási szolgáltatás szintjén vannak jelentve. Metrikák adatok segítségével elemezheti a tárolási szolgáltatás használatát, diagnosztizálhatja a rendszerkérésekkel kapcsolatos problémákat a tárolási szolgáltatás, és a szolgáltatás thasználó alkalmazások teljesítményének javítása.  

 A Storage Analytics-metrikák alapértelmezés szerint engedélyezve vannak az új tárfiókokhoz. A metrikákat az [Azure Portalon](https://portal.azure.com/)állíthatja be; További információt a [Tárfiók figyelése az Azure Portalon című témakörben talál.](/azure/storage/storage-monitor-storage-account) A Storage Analytics programozott módon is engedélyezhető a REST API-n vagy az ügyfélkódtáron keresztül. A Szolgáltatás tulajdonságainak beállítása művelettel engedélyezheti a Storage Analytics szolgáltatást az egyes szolgáltatásokhoz.  

> [!NOTE]
> A Storage Analytics-metrikák a Blob, a Várólista, a Táblázat és a Fájl szolgáltatásokhoz érhetők el.
> A Storage Analytics-mérőszámok mostantól klasszikus mérőszámok. A Microsoft azt javasolja, hogy a Storage Analytics-metrikák helyett [az Azure Monitor storage-metrikákat](storage-metrics-in-azure-monitor.md) használjon.

## <a name="transaction-metrics"></a>Tranzakciómetrikák  
 A megbízható adathalmazt óránkénti vagy percidőközönként rögzíti az egyes tárolási szolgáltatások és a kért API-művelet, beleértve a be-és kimenő forgalom, rendelkezésre állás, hibák és kategorizált kérelem százalékok. A tranzakció részleteinek teljes listáját a [Storage Analytics Metrics táblaséma](/rest/api/storageservices/storage-analytics-metrics-table-schema) témakörben láthatja.  

 A tranzakciós adatok at két szinten rögzítik : a szolgáltatási szint és az API működési szintje. A szolgáltatás szintjén az összes kért API-műveletet összegző statisztikák óránként egy táblaentitásba kerülnek, még akkor is, ha a szolgáltatásra nem érkezett kérés. Az API-művelet szintjén a statisztikák csak akkor kerülnek írásra egy entitásnak, ha a műveletet egy órán belül kérték.  

 Ha például egy **GetBlob-műveletet** hajt végre a Blob szolgáltatáson, a Storage Analytics-metrikák naplózzák a kérelmet, és tartalmazzák azt a Blob szolgáltatás és a **GetBlob** művelet összesített adatai között. Azonban ha nem **GetBlob** művelet et kér az óra alatt, egy entitás nem lesz írva a *$MetricsTransactionsBlob* az adott művelethez.  

 A tranzakciós metrikákat a rendszer rögzíti a felhasználói kérelmekhez és a Storage Analytics által benyújtott kérelmekhez is. Például a Storage Analytics által a naplók és a táblaentitások írására vonatkozó kéréseket rögzíti a rendszer.

## <a name="capacity-metrics"></a>Kapacitásmetrikák  

> [!NOTE]
>  Jelenleg a kapacitás metrikák csak a Blob szolgáltatás érhető el.

 A kapacitásadatok naponta rögzítésre kerülnek egy tárfiók Blob szolgáltatásához, és két táblaentitás van megírva. Az egyik entitás a felhasználói adatok statisztikáit, a `$logs` másik pedig a Storage Analytics által használt blobtároló statisztikáit tartalmazza. A *$MetricsCapacityBlob* táblázat a következő statisztikákat tartalmazza:  

- **Kapacitás:** A tárfiók Blob szolgáltatása által használt tárterület mennyisége bájtban.  
- **ContainerCount:** A blobtárolók száma a tárfiók Blob szolgáltatásában.  
- **ObjectCount**: A tárfiók Blob szolgáltatásában lévő véglegesített és nem véglegesített blokk- vagy lapblobok száma.  

  A kapacitásmérőkről további információt a [Storage Analytics Metrics táblázatsémája című témakörben talál.](/rest/api/storageservices/storage-analytics-metrics-table-schema)  

## <a name="how-metrics-are-stored"></a>A mérőszámok tárolásának megóra  

 Az egyes tárolási szolgáltatások összes metrika-adata három, az adott szolgáltatás számára fenntartott táblában tárolódik: egy tábla a tranzakcióadatokhoz, egy tábla a perctranzakció-információkhoz, és egy másik tábla a kapacitásadatokhoz. A tranzakciós és a perctranzakciós adatok kérés- és válaszadatokból, a kapacitásadatok pedig a tárolási használati adatokból állnak. Az órametrikák, a percmetrikák és a tárfiók Blob-szolgáltatásának kapacitása az alábbi táblázatban leírt táblákban érhető el.  

|Metrikák szintje|Táblanevek|Verziók hoz támogatott|  
|-------------------|-----------------|----------------------------|  
|Óránkénti mutatók, elsődleges hely|- $MetricsTransactionsBlob<br />- $MetricsTransactionsTable<br />- $MetricsTransactionsQueue|Csak a 2013-08-15 előtti verziókban. Bár ezek a nevek továbbra is támogatottak, ajánlott az alább felsorolt táblázatok használatára váltani.|  
|Óránkénti mutatók, elsődleges hely|- $MetricsHourPrimaryTransactionsBlob<br />- $MetricsHourPrimaryTransactionsTable<br />- $MetricsHourPrimaryTransactionsQueue<br />- $MetricsHourPrimaryTransactionsFile|Minden verzió. A fájlszolgáltatás-metrikák támogatása csak a 2015-04-05-ös és újabb verziókban érhető el.|  
|Percmérők, elsődleges hely|- $MetricsMinutePrimaryTransactionsBlob<br />- $MetricsMinutePrimaryTransactionsTable<br />- $MetricsMinutePrimaryTransactionsQueue<br />- $MetricsMinutePrimaryTransactionsFile|Minden verzió. A fájlszolgáltatás-metrikák támogatása csak a 2015-04-05-ös és újabb verziókban érhető el.|  
|Óránkénti mutatók, másodlagos hely|- $MetricsHourSecondaryTransactionsBlob<br />- $MetricsHourSecondaryTransactionsTable<br />- $MetricsHourSecondaryTransactionsQueue|Minden verzió. Az olvasási hozzáférésű georedundáns replikációt engedélyezni kell.|  
|Percmetrikák, másodlagos hely|- $MetricsMinuteSecondaryTransactionsBlob<br />- $MetricsMinuteSecondaryTransactionsTable<br />- $MetricsMinuteSecondaryTransactionsQueue|Minden verzió. Az olvasási hozzáférésű georedundáns replikációt engedélyezni kell.|  
|Kapacitás (csak Blob-szolgáltatás)|$MetricsCapacityBlob|Minden verzió.|  

 Ezek a táblák automatikusan létrejönnek, ha a Storage Analytics engedélyezve van egy tárolási szolgáltatás végponthoz. Ezek a tárfiók névterén keresztül érhetők el, például: `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`. A metrikák táblák nem jelennek meg a listaelem-műveletben, és közvetlenül a táblanéven keresztül kell hozzáférniük.  

## <a name="enable-metrics-using-the-azure-portal"></a>Metrikák engedélyezése az Azure Portal használatával
Kövesse az alábbi lépéseket a metrikák engedélyezéséhez az [Azure Portalon:](https://portal.azure.com)

1. Nyissa meg a tárfiókot.
1. Válassza **a Diagnosztika beállításai (klasszikus)** lehetőséget a **Menü** ablaktáblán.
1. Győződjön meg arról, hogy az **Állapot** beállítás **Be**állásra van állítva.
1. Válassza ki a monitorozni kívánt szolgáltatások metrikákat.
1. Adja meg az adatmegőrzési szabályt, amely jelzi, hogy mennyi ideig őrizze meg a metrikákat és a naplóadatokat.
1. Kattintson a **Mentés** gombra.

Az [Azure Portal](https://portal.azure.com) jelenleg nem teszi lehetővé a tárfiók ban a percmetrikák konfigurálását; engedélyeznie kell a percmetrikákat a PowerShell vagy programozott használatával.

## <a name="enable-storage-metrics-using-powershell"></a>Storage-metrikák engedélyezése a PowerShell használatával  
A PowerShell a helyi gépen konfigurálhatja a storage metrikák a tárfiókban az Azure PowerShell-parancsmag **Get-AzStorageServiceMetricsProperty** az aktuális beállítások lekéréséhez, és a **parancsmag Set-AzStorageServiceMetricsProperty** az aktuális beállítások módosításához.  

A storage metrikákat vezérlő parancsmagok a következő paramétereket használják:  

* **ServiceType**, lehetséges érték: **Blob**, **Várólista**, **Tábla**és **Fájl**.
* **MetricsType**, lehetséges értékek **óra** és **perc**.  
* **MetricsLevel**, lehetséges értékek a következők:
* **Nincs**: Kikapcsolja a figyelést.
* **Szolgáltatás:** olyan metrikákat gyűjt, mint például a be- és kimenő forgalom, a rendelkezésre állás, a késés és a sikerességi százalékok, amelyek a blob, a várólista, a tábla és a fájlszolgáltatások összesítve vannak.
* **ServiceAndApi:** A szolgáltatás metrikák mellett az Azure Storage-szolgáltatás API-jában minden egyes tárolási művelethez ugyanazokat a metrikákat gyűjti.

Például a következő parancs átkapcsolja a blobszolgáltatás percmetrikáját a tárfiókban, a megőrzési idő öt napra van állítva: 

> [!NOTE]
> Ez a parancs feltételezi, hogy a parancs használatával `Connect-AzAccount` bejelentkezett az Azure-előfizetésbe.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"

Set-AzStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5 -Context $storageAccount.Context
```  

* Cserélje `<resource-group-name>` le a helyőrző értéket az erőforráscsoport nevére.
        
* Cserélje `<storage-account-name>` le a helyőrző értéket a tárfiók nevére.



A következő parancs lekéri az aktuális óránkénti metrikák szintjét és megőrzési napok a blob szolgáltatás az alapértelmezett tárfiókban:  

```powershell
Get-AzStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob -Context $storagecontext.Context
```  

Az Azure PowerShell-parancsmagok Azure-előfizetéssel való együttműködésre történő konfigurálásáról, valamint a használandó alapértelmezett tárfiók kiválasztásáról a következő témakörben olvashat: [Az Azure PowerShell telepítése és konfigurálása.](https://azure.microsoft.com/documentation/articles/install-configure-powershell/)  

## <a name="enable-storage-metrics-programmatically"></a>Storage-mérőszámok engedélyezése programozott módon  
Az Azure Portalon vagy az Azure PowerShell-parancsmagok használata mellett a storage-metrikák vezérléséhez is használhatja az Azure Storage API-k egyikét. Ha például .NET nyelvet használ, használhatja a Storage Client Library könyvtárat.  

A **CloudBlobClient**, **CloudQueueClient**, **CloudTableClient**és **CloudFileClient** osztályok mindegyike rendelkezik olyan metódusokkal, mint például **a SetServiceProperties** és **a SetServicePropertiesAsync,** amelyek paraméterként a **ServiceProperties** objektumot veszik. A **ServiceProperties** objektum használatával konfigurálhatja a tárolási metrikákat. A következő C# kódrészlet például bemutatja, hogyan módosíthatja a metrikaszintet és az óránkénti várólista-metrikák megőrzési napjait:  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.HourMetrics.MetricsLevel = MetricsLevel.Service;  
serviceProperties.HourMetrics.RetentionDays = 10;  

queueClient.SetServiceProperties(serviceProperties);  
```  

A .NET nyelv tárolási metrikák konfigurálásáról a [Storage Client Library for .NET](https://msdn.microsoft.com/library/azure/mt347887.aspx)című témakörben talál további információt.  

A Storage Metrics REST API használatával történő konfigurálásáról a [Storage Analytics engedélyezése és konfigurálása](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics)című témakörben talál általános tudnivalókat.  

##  <a name="viewing-storage-metrics"></a>A tárolási mutatók megtekintése  
Miután konfigurálta a Storage Analytics-metrikákat a tárfiók figyelésére, a Storage Analytics a tárfiók jól ismert tábláinak készletében rögzíti a metrikákat. A diagramokat beállíthatja úgy, hogy óránkénti metrikákat tekintsenek meg az [Azure Portalon:](https://portal.azure.com)

1. Nyissa meg a tárfiókot az [Azure Portalon.](https://portal.azure.com)
1. Válassza **ki a Metrikák (klasszikus)** a **Menü** panelen a szolgáltatás, amelynek metrikák meg szeretné tekinteni.
1. Kattintson a konfigurálni kívánt diagramra.
1. A **Diagram szerkesztése** panelen jelölje ki az **Időtartományt**, a **Diagram típusát**és a diagramon megjeleníteni kívánt mutatókat.

A Storage-fiók menüpaneljének **figyelési (klasszikus)** szakaszában konfigurálhatja a [riasztási szabályokat,](#metrics-alerts)például e-mail riasztásokküldését, hogy értesítse, ha egy adott metrika elér egy bizonyos értéket.

Ha hosszú távú tároláshoz vagy helyi elemzéshez szeretné letölteni a metrikákat, használjon eszközt, vagy írjon valamilyen kódot a táblák olvasásához. Az elemzéshez le kell töltenie a percmérőket. A táblák nem jelennek meg, ha a tárfiókban lévő összes táblát felsorolja, de közvetlenül név szerint is elérheti őket. Számos tárolóböngészési eszköz tisztában van ezekkel a táblákkal, és lehetővé teszi azok közvetlen megtekintését (az elérhető eszközök listáját lásd az [Azure Storage-ügyféleszközökben).](/azure/storage/storage-explorers)

||||  
|-|-|-|  
|**Mutatókat**|**Táblanevek**|**Megjegyzések**|  
|Óránkénti mutatók|$MetricsHourPrimaryTransactionsBlob<br /><br /> $MetricsHourPrimaryTransactionsTable<br /><br /> $MetricsHourPrimaryTransactionsQueue<br /><br /> $MetricsHourPrimaryTransactionsFile|A 2013-08-15 előtti verziókban ezek a táblázatok a következők voltak:<br /><br /> $MetricsTransactionsBlob<br /><br /> $MetricsTransactionsTable<br /><br /> $MetricsTransactionsQueue<br /><br /> A Fájlszolgáltatás metrikák a 2015-04-05-ös verziótól kezdve érhetők el.|  
|Percmérők|$MetricsMinutePrimaryTransactionsBlob<br /><br /> $MetricsMinutePrimaryTransactionsTable<br /><br /> $MetricsMinutePrimaryTransactionsQueue<br /><br /> $MetricsMinutePrimaryTransactionsFile|Csak a PowerShell használatával vagy programozott módon engedélyezhető.<br /><br /> A Fájlszolgáltatás metrikák a 2015-04-05-ös verziótól kezdve érhetők el.|  
|Kapacitás|$MetricsCapacityBlob|Csak blob szolgáltatás.|  

Ezeknek a tábláknak a sémáinak részletes adatait a [Storage Analytics Metrics táblaséma](/rest/api/storageservices/storage-analytics-metrics-table-schema)tartalmazza. Az alábbi mintasorok csak a rendelkezésre álló oszlopok egy részét mutatják, de szemléltetik a Storage Metrics alábbi mérőszámok mentési módjának néhány fontos jellemzőjét:  

||||||||||||  
|-|-|-|-|-|-|-|-|-|-|-|  
|**PartitionKey**|**RowKey**|**Időbélyeg**|**TotalRequests**|**Összesszámlázható kérések**|**TotalIngress**|**TotalEgress**|**Rendelkezésre állás**|**AverageE2ELatency**|**AverageServerLatency**|**Százalékos siker**|  
|20140522T1100|felhasználó; Minden|2014-05-22T11:01:16.7650250z|7|7|4003|46801|100|104.4286|6.857143|100|  
|20140522T1100|felhasználó; QueryEntitások|2014-05-22T11:01:16.7640250z|5|5|2694|45951|100|143.8|7.8|100|  
|20140522T1100|felhasználó; QueryEntity (Lekérdezésentitás)|2014-05-22T11:01:16.7650250z|1|1|538|633|100|3|3|100|  
|20140522T1100|felhasználó; UpdateEntity (UpdateEntity)|2014-05-22T11:01:16.7650250z|1|1|771|217|100|9|6|100|  

Ebben a példában a metrikák adatai, a partíciókulcs a perc felbontási időt használja. A sorkulcs azonosítja a sorban tárolt információ típusát, és ez két információból, a hozzáférési típusból és a kérelem típusából áll:  

-   A hozzáférési típus **felhasználó** vagy **rendszer,** ahol a **felhasználó** a storage szolgáltatáshoz intézett összes felhasználói kérelemre hivatkozik, a **rendszer** pedig a Storage Analytics által benyújtott kérelmekre hivatkozik.  

-   A kérelem típusa vagy **az összes,** ebben az esetben egy összegző sor, vagy azonosítja az adott API-t, például **QueryEntity** vagy **UpdateEntity**.  

A fenti mintaadatok egy perc összes rekordját jelenítik meg (11:00 am-től kezdődően), így a **QueryEntityes** kérelmek száma, valamint a **QueryEntity** kérelmek száma, valamint az **UpdateEntity** kérelmek száma legfeljebb hét, amely a **felhasználó:All** sorban látható összeg. Hasonlóképpen a felhasználó 104.4286 átlagos késése a felhasználón(((143.8 * 5) + 3 + 9)/7. **user:All**  

## <a name="metrics-alerts"></a>Metrikák riasztások
Fontolja meg a riasztások beállítását az [Azure Portalon,](https://portal.azure.com) hogy automatikusan értesítést kapjon a tárolási szolgáltatások viselkedésében bekövetkező fontos változásokról. Ha tárolókezelő eszközzel tölti le ezeket a mérőszámok adatait tagolt formátumban, a Microsoft Excel segítségével elemezheti az adatokat. Az elérhető tárkezelő eszközök listáját az [Azure Storage-ügyféleszközök](/azure/storage/storage-explorers) című témakörben található. Riasztások konfigurálása a **riasztási (klasszikus)** panel, elérhető **a figyelés (klasszikus)** a Tárfiók menüpanelen.

> [!IMPORTANT]
> Előfordulhat, hogy egy tárolási esemény között késés van, és amikor a megfelelő óránkénti vagy perces metrikák adatai rögzítésre kerülnek. Percmérők esetén egyszerre több percnyi adat is írható. Ez azt eredményezheti, hogy a korábbi percekből származó tranzakciók az aktuális perc tranzakciójába kerülnek. Ha ez történik, előfordulhat, hogy a riasztási szolgáltatás nem rendelkezik az összes rendelkezésre álló metrika adatok a beállított riasztási időköz, ami a riasztások váratlanul tüzelési vezethet.
>

## <a name="accessing-metrics-data-programmatically"></a>Metrikák adatainak programozott elérése  
A következő lista bemutatja a C# mintakódot, amely percekig hozzáfér a percmetrikákhoz, és megjeleníti az eredményeket egy konzolablakban. A kódminta az Azure Storage Client Library 4.x-es vagy újabb verzióját használja, amely tartalmazza a **CloudAnalyticsClient** osztályt, amely leegyszerűsíti a metrikatáblák elérését a storage-ban.  

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

## <a name="billing-on-storage-metrics"></a>A tárolási mutatók számlázása  
A metrikák táblaentitások létrehozására vonatkozó írási kérelmeket az összes Azure Storage-műveletre vonatkozó általános díjszabás szerint számítjuk fel.  

Olvasási és törlési kérelmek metrikák adatok egy ügyfél is számlázható a szokásos díjakat. Ha konfigurált egy adatmegőrzési szabályzatot, nem számítunk fel díjat, amikor az Azure Storage törli a régi metrikák adatait. Ha azonban törli az elemzési adatokat, a fióknak díjat kell fizetnie a törlési műveletekért.  

A metrikák táblák által használt kapacitás is számlázható. Az alábbiak segítségével megbecsülheti a metrikák adatainak tárolására használt kapacitás mennyiségét:  

-   Ha egy szolgáltatás minden egyes alkalommal minden api-t használ minden szolgáltatásban, akkor körülbelül 148 KB-nyi adatot tárol óránként a metrikák tranzakciós tábláiban, ha engedélyezte a szolgáltatás- és API-szintű összegzést.  
-   Ha minden órán belül egy szolgáltatás a szolgáltatás minden API-ját használja, majd óránként körülbelül 12 KB-nyi adatot tárol a metrikák tranzakciós táblái, ha engedélyezte a csak szolgáltatásszintű összegzést.  
-   A blobok kapacitástáblája naponta két sort tartalmaz, feltéve, hogy engedélyezte a naplókat. Ez azt jelenti, hogy a tábla mérete naponta körülbelül 300 bájttal nőhet.

## <a name="next-steps"></a>További lépések
* [Tárfiók figyelése](https://www.windowsazure.com/manage/services/storage/how-to-monitor-a-storage-account/)   
* [Storage Analytics Metrics táblaséma](/rest/api/storageservices/storage-analytics-metrics-table-schema)   
* [Storage Analytics naplózott műveletek és állapotüzenetek](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)   
* [Storage Analytics-naplózás](storage-analytics-logging.md)

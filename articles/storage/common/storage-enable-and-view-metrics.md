---
title: Az Azure Portalon storage mérőszámainak engedélyezése |} A Microsoft Docs
description: A Blob, üzenetsor, tábla és fájl szolgáltatások storage mérőszámainak engedélyezése
services: storage
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 02/14/2017
ms.author: rogarana
ms.component: common
ms.openlocfilehash: 3e6a11a8c225afb220f290cee2db39c36750b401
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53632144"
---
# <a name="enabling-azure-storage-metrics-and-viewing-metrics-data"></a>Azure Storage mérőszámainak engedélyezése és a mérőszámadatok megtekintése
[!INCLUDE [storage-selector-portal-enable-and-view-metrics](../../../includes/storage-selector-portal-enable-and-view-metrics.md)]

## <a name="overview"></a>Áttekintés
Storage-mérőszámok alapértelmezés szerint engedélyezve van, amikor létrehoz egy új tárfiókot. -N keresztül a figyelés konfigurálásához a [az Azure portal](https://portal.azure.com) vagy a Windows Powershellt, vagy automatizáltan a tároló ügyfélkódtárai egyikét.

Konfigurálhatja a mérőszámadatokat olyan megőrzési időszakot: Ez az időszak meghatározza, hogy mennyi ideig a Storage szolgáltatás követi a metrikák és a költségek, terület szükséges tárolja őket. Általában használjon rövidebb adatmegőrzési idő perc típusú metrikák óránkénti metrikákat, mint a miatt szükséges perc típusú metrikák jelentős további helyet. Olyan megőrzési időszakot válasszon, hogy elegendő idő áll az adatok elemzését, és bármely végezze a rögzítést elemzési vagy jelentéskészítési célból a megtartani kívánt metrikák letöltéséhez. Ne feledje, hogy is díjköteles metrikai adatok letöltése a storage-fiókból.

## <a name="how-to-enable-metrics-using-the-azure-portal"></a>Az Azure portal használatával metrikák engedélyezése
Kövesse az alábbi lépéseket, hogy engedélyezze a mérőszámok a a [az Azure portal](https://portal.azure.com):

1. Nyissa meg a tárfiókot.
1. Válassza ki **diagnosztikai** származó a **menü** ablaktáblán.
1. Ügyeljen arra, hogy **állapot** értékre van állítva **a**.
1. Válassza ki a figyelni kívánt szolgáltatások metrikáit.
1. Adja meg, amelyek jelzik, hogy mennyi ideig mérőszámok és az adatok adatmegőrzési.
1. Kattintson a **Mentés** gombra.

A [az Azure portal](https://portal.azure.com) nem jelenleg engedélyezi a tárfiók; perc típusú metrikák beállításához engedélyeznie kell a PowerShell-lel perc típusú metrikák vagy programozott módon.

## <a name="how-to-enable-metrics-using-powershell"></a>PowerShell-lel metrikák engedélyezése

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ön PowerShell a helyi gépen való konfigurálásához használható Storage Metrics a tárfiókban található az Azure PowerShell-parancsmag Get-AzStorageServiceMetricsProperty lekérni az aktuális beállítások és a Set-AzStorageServiceMetricsProperty parancsmag használatával az aktuális beállítások módosításához.

A parancsmagok, amelyek vezérlik a Storage Metrics használja a következő paramétereket:

* MetricsType: lehetséges értékek: az órában és percben.
* Szolgáltatástípus: lehetséges értékei a Blob, Queue és Table.
* MetricsLevel: a lehetséges értékek: None, a szolgáltatás és a ServiceAndApi.

Az alábbi parancs például a perc típusú metrikák a alapértelmezett tárfiókban található Blob szolgáltatás vált, együtt a megőrzési időszak beállítása pedig öt nappal:

```powershell
Set-AzStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5`
```

Az alábbi parancs lekéri a jelenlegi óránkénti metrikák szintje és megőrzési nap a blobszolgáltatás alapértelmezett tárfiókban található:

```powershell
Get-AzStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob
```

Működik az Azure-előfizetésében az Azure PowerShell-parancsmagjainak konfigurálása és használata az alapértelmezett tárfiók kiválasztása kapcsolatos információkért lásd: [Azure PowerShell telepítése és konfigurálása annak](/powershell/azure/overview).

## <a name="how-to-enable-storage-metrics-programmatically"></a>Programozott módon a Storage mérőszámainak engedélyezése
Az alábbi C#-kódrészlet bemutatja, hogyan engedélyezze a mérőszámok és a naplózást a Blob service használata a storage ügyféloldali kódtára a .NET-hez:

```csharp
//Parse the connection string for the storage account.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

// Create service client for credentialed access to the Blob service.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Enable Storage Analytics logging and set retention policy to 10 days.
ServiceProperties properties = new ServiceProperties();
properties.Logging.LoggingOperations = LoggingOperations.All;
properties.Logging.RetentionDays = 10;
properties.Logging.Version = "1.0";

// Configure service properties for metrics. Both metrics and logging must be set at the same time.
properties.HourMetrics.MetricsLevel = MetricsLevel.ServiceAndApi;
properties.HourMetrics.RetentionDays = 10;
properties.HourMetrics.Version = "1.0";

properties.MinuteMetrics.MetricsLevel = MetricsLevel.ServiceAndApi;
properties.MinuteMetrics.RetentionDays = 10;
properties.MinuteMetrics.Version = "1.0";

// Set the default service version to be used for anonymous requests.
properties.DefaultServiceVersion = "2015-04-05";

// Set the service properties.
blobClient.SetServiceProperties(properties);
```

## <a name="viewing-storage-metrics"></a>Storage-metrikák megtekintése
Miután konfigurálta a Storage Analytics metrikákat kíván monitorozni a storage-fiókot, a Storage Analytics rögzíti a metrikákat a tárfiókban lévő jól ismert táblák egy készlete. Konfigurálhatja a diagramot óránkénti mérőszámok megtekintéséhez a [az Azure portal](https://portal.azure.com):

1. Keresse meg a tárfiók a [az Azure portal](https://portal.azure.com).
1. Válassza ki **metrikák** a a **menü** a szolgáltatás, amelynek meg szeretné tekinteni metrikák paneljén.
1. Válassza ki **szerkesztése** konfigurálni szeretné a diagramon.
1. Az a **diagram szerkesztése** panelen válassza a **időtartomány**, **diagramtípus**, és a metrikák, amelyeket meg szeretne a diagram.
1. Kattintson az **OK** gombra.

Ha azt szeretné, töltse le a metrikákat, hosszú távú tárolás céljából, vagy az elemzésük helyileg, kell tennie:

* Olyan eszköz, amely észleli ezeket a táblákat, és lehetővé teszi, hogy megtekintheti és letöltheti a fájlokat használja.
* Írjon egy egyéni alkalmazást vagy szkriptet olvassa el, és tárolja a.

Számos harmadik fél tárolási böngészési eszközök ismeri ezeket a táblákat, és megtekintheti őket közvetlenül.
Lásd: [Azure Storage-ügyféleszközök](storage-explorers.md) elérhető eszközök listáját.

> [!NOTE]
> 0.8.0 verziójával kezdődően a [Microsoft Azure Storage Explorer](http://storageexplorer.com/), tekintheti meg és töltse le az analytics tabulky metrik.
>
>

Annak érdekében, hogy programozott módon hozzáférni az analytics-táblák, vegye figyelembe, hogy az analytics táblák nem jelennek meg ha a tárfiókban lévő összes tábla. Név szerint közvetlenül érheti el őket, vagy a [CloudAnalyticsClient API](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.analytics.cloudanalyticsclient.aspx) .NET ügyféloldali kódtár lekérdezése a tábla neve.

### <a name="hourly-metrics"></a>Óránkénti metrikák
* $MetricsHourPrimaryTransactionsBlob
* $MetricsHourPrimaryTransactionsTable
* $MetricsHourPrimaryTransactionsQueue

### <a name="minute-metrics"></a>Perc típusú metrikák
* $MetricsMinutePrimaryTransactionsBlob
* $MetricsMinutePrimaryTransactionsTable
* $MetricsMinutePrimaryTransactionsQueue

### <a name="capacity"></a>Kapacitás
* $MetricsCapacityBlob

Részletes információk a séma találhat meg ezen táblák [Storage Analytics mérőszámainak Táblasémáját](https://msdn.microsoft.com/library/azure/hh343264.aspx). A minta az alábbi sorokat megjelenítése csak az elérhető oszlopok egy része, de néhány fontosabb funkciói a Storage Metrics menti ezeket a metrikákat módon mutatják be:

| PartitionKey | RowKey | Időbélyeg | TotalRequests | TotalBillableRequests | TotalIngress | TotalEgress | Rendelkezésre állás | AverageE2ELatency | AverageServerLatency | PercentSuccess |
| --- |:---:| ---:| --- | --- | --- | --- | --- | --- | --- | --- |
| 20140522T1100 |user;All |2014-05-22T11:01:16.7650250Z |7 |7 |4003 |46801 |100 |104.4286 |6.857143 |100 |
| 20140522T1100 |felhasználói; QueryEntities |2014-05-22T11:01:16.7640250Z |5 |5 |2694 |45951 |100 |143.8 |7.8 |100 |
| 20140522T1100 |user;QueryEntity |2014-05-22T11:01:16.7650250Z |1 |1 |538 |633 |100 |3 |3 |100 |
| 20140522T1100 |user;UpdateEntity |2014-05-22T11:01:16.7650250Z |1 |1 |771 |217 |100 |9 |6 |100 |

A példaadatokat perc típusú metrikák a partíciókulcsot használja az idő perces felbontásban. A sorkulcs azonosítja a sorban tárolt információ típusát. A sorkulcs két darab, a hozzáférés típusa, és a kérelemtípus tevődik össze:

* A hozzáférési típus vagy a felhasználó, vagy a rendszer, ahol felhasználó az összes felhasználói kérések a storage szolgáltatásra hivatkozik, és a rendszer a kérést hoz létre a Storage Analytics hivatkozik.
* Kérés típusa, amelyben minden esetben azt összegző sort, vagy az adott API például QueryEntity vagy UpdateEntity azonosítja.

A fenti mintaadatok minden a rekordokat jeleníti meg egy perc (11:00 órakor kezdődik), így QueryEntities kérések száma, valamint QueryEntity száma kérések száma plusz UpdateEntity kérelmek hozzáadása legfeljebb hét, amely az összes felhasználó: minden a sorban jelenik meg. Ehhez hasonlóan nyer a felhasználói: minden a sorban az átlagos végpontok közötti késés 104.4286 szerint kiszámításának ((143.8 * 5) + 3 + 9) / 7.

Vegye figyelembe, hogy **óránkénti metrika beállítások Blob** egyaránt érvényesek **Blob kapacitás metrika** ($MetricsCapacityBlob) és **óránkénti Blob tranzakciók mérőszáma** ($ MetricsHourPrimaryTransactionsBlob). Mind engedélyezve vannak vagy együtt le van tiltva, és ugyanazt a megőrzési házirend.

## <a name="metrics-alerts"></a>Metrikák riasztások
Vegye figyelembe a riasztások beállítását a [az Azure portal](https://portal.azure.com) , a Storage-mérőszámok automatikusan értesíti a felhasználót, a tárolási szolgáltatások viselkedését a fontos változásokról. A storage explorer eszköz használatával töltse le a metrikákat tagolt formátumú adatokat, ha a Microsoft Excel segítségével elemezheti az adatokat. Lásd: [Azure Storage-ügyféleszközök](storage-explorers.md) rendelkezésre álló tár explorer eszközök listáját. A riasztásokat konfigurálhat a **riasztási szabályok** panelen elérhető **figyelés** a tárolási fiók menü ablaktáblán.

> [!IMPORTANT]
> Előfordulhat, hogy egy tárolási esemény, és ha a megfelelő óránkénti vagy percenkénti metrikákat adatok rögzített közötti késleltetést jelzi. Perc típusú metrikák naplózása, amikor az adatok több percig egyszerre írhatók. Korábbi perctől tranzakciók majd összesíti a jelenlegi percen a tranzakciót. Ha ez történik, a riasztási szolgáltatás nem lehet az összes rendelkezésre álló metrikák adatait a konfigurált, a riasztások kiváltó váratlanul vezethet riasztási időköz.
>

## <a name="accessing-metrics-data-programmatically"></a>Metrikai adatok programozott elérése
A következő listában látható a minta C#-kódot, amely hozzáfér a különböző perc, a perc típusú metrikák, és megjeleníti az eredményeket a konzol ablakot. Az Azure Storage-kódtárat 4-es verzió, amely tartalmazza a CloudAnalyticsClient osztály, amely egyszerűbbé teszi az elérése a storage-ban tabulky metrik használ.

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
        // (StartsWith is not supported using LINQ with Azure table storage)
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

## <a name="what-charges-do-you-incur-when-you-enable-storage-metrics"></a>Milyen díjakat tegye díjak storage mérőszámainak engedélyezése esetén?
Az írási kérések létrehozása metrikákhoz táblaentitások számoljuk el a standard díjszabás vonatkozik minden Azure Storage-műveletek.

Olvassa el és a delete kérések egy ügyfél a metrikák adatait is számlázhatók normál díjszabásnak megfelelően felszámítjuk. Ha az adatmegőrzési házirend van beállítva, nem terheli Azure Storage metrikák régi adatok törlését. Azonban ha törli az analytics-adatait, a fiók díjszabásának a törlési műveletek.

A kapacitás felhasználása a tabulky metrik egyben számlázható: metrikák adatainak tárolására szolgáló kapacitását becslése érdekében használhatja a következőt:

* Ha minden egyes óráért szolgáltatás már minden szolgáltatás minden API-t használja, majd 148KB adatot tárolja a tranzakciós tabulky metrik óránként Ha engedélyezte a szolgáltatás és az API-szintet összegzése.
* Ha minden egyes óráért szolgáltatás már minden szolgáltatás minden API-t használja, majd körülbelül 12KB méretű adatot tárolja a tranzakciós tabulky metrik óránként Ha engedélyezte, hogy csak szolgáltatási szint összegzése.
* A blobok kapacitás táblához hozzáadott minden nap (feltéve, hogy felhasználói naplók feliratkozott) két sor: Ez azt jelenti, hogy minden nap a tábla méretét nő legfeljebb körülbelül 300 bájt.

## <a name="next-steps"></a>További lépések
[Tárolási Teljesítménynapló-adatok elérése és naplózásának engedélyezése](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data)

---
title: Az Azure portálon storage mérőszámainak engedélyezése |} Microsoft Docs
description: A Blob, a várólista, a tábla és a fájl szolgáltatások storage mérőszámainak engedélyezése
services: storage
documentationcenter: ''
author: roygara
manager: jeconnoc
editor: tysonn
ms.assetid: 0407adfc-2a41-4126-922d-b76e90b74563
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/14/2017
ms.author: rogarana
ms.openlocfilehash: 0caa4eff80877ad4bf8d501a276e82922b1a84c7
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="enabling-azure-storage-metrics-and-viewing-metrics-data"></a>Azure Storage mérőszámainak engedélyezése és metrikai adatok megtekintése
[!INCLUDE [storage-selector-portal-enable-and-view-metrics](../../../includes/storage-selector-portal-enable-and-view-metrics.md)]

## <a name="overview"></a>Áttekintés
Storage mérőszámainak alapértelmezés szerint engedélyezve van, amikor létrehoz egy új tárfiókot. Keresztül figyelését be tudja állítani a [Azure-portálon](https://portal.azure.com) vagy a Windows PowerShell vagy a storage ügyfélkódtáraival egyik programozott módon keresztül.

A metrikák adatok megőrzési időtartamot is beállíthat: Ez az időszak meghatározza, hogy mennyi ideig a tárolási szolgáltatás tartja a metrikák és a költségek, akkor a tárhely szükséges tárolja őket. Általában használjon rövidebb adatmegőrzési idő percben metrikáihoz mint óránkénti metrikák percenkénti metrikákat szükséges jelentős területnek miatt. Válassza ki a megőrzési időtartam, úgy, hogy elegendő idő áll elemezheti az adatokat, és töltse le a rögzítést elemzési vagy jelentéskészítési célból a megtartani kívánt metrikák. Ne feledje, hogy akkor is számlázása metrikai adatok letölthető a tárfiók.

## <a name="how-to-enable-metrics-using-the-azure-portal"></a>Az Azure portál használatával metrikák engedélyezése
Kövesse az alábbi lépéseket ahhoz, hogy a metrikák a [Azure-portálon](https://portal.azure.com):

1. Lépjen a tárfiókhoz.
1. Válassza ki **diagnosztika** a a **menü** ablaktáblán.
1. Győződjön meg arról, hogy **állapot** értéke **a**.
1. Válassza ki a figyelni kívánt szolgáltatás a használatmérés.
1. Adjon meg egy megőrzési házirend megjelenítésével jelzi mennyi ideig metrikákat, és az adatok naplózása.
1. Kattintson a **Mentés** gombra.

A [Azure-portálon](https://portal.azure.com) jelenleg teszi lehetővé az percenkénti metrikákat konfigurálhatja a tárfiók; engedélyeznie kell a PowerShell használatával percenkénti metrikákat vagy programon keresztül.

## <a name="how-to-enable-metrics-using-powershell"></a>PowerShell-lel metrikák engedélyezése
A helyi gépen PowerShell használatával Storage Metrics konfigurálása a tárfiókban lévő aktuális beállításainak módosítása az Azure PowerShell-parancsmag Get-AzureStorageServiceMetricsProperty a jelenlegi beállítások és a Set-AzureStorageServiceMetricsProperty parancsmag használatával.

A parancsmagok Storage Metrics szabályozó használja a következő paramétereket:

* MetricsType: lehetséges értékei órában és percben.
* ServiceType: lehetséges értékei a Blob, Queue és Table.
* MetricsLevel: a lehetséges értékek: None, szolgáltatás és ServiceAndApi.

A következő parancs például az alapértelmezett tárfiók a Blob szolgáltatás percenkénti metrikákat a vált, a megőrzési időszak beállítása öt nap az:

```powershell
Set-AzureStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5`
```

A következő parancs segítségével lekérdezhető az aktuális óránkénti metrikák szint és a megőrzési nap az alapértelmezett tárfiók a blob szolgáltatás:

```powershell
Get-AzureStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob
```

Konfigurálásával kapcsolatos információkat az Azure PowerShell-parancsmagok az Azure-előfizetéshez és kiválasztása az alapértelmezett tárfiókot használni, lásd: [telepítése és konfigurálása az Azure PowerShell](/powershell/azure/overview).

## <a name="how-to-enable-storage-metrics-programmatically"></a>Programozott módon a Storage mérőszámainak engedélyezése
Az alábbi C# kódrészletben láthatja a metrikák és a Blob szolgáltatás használata a storage ügyféloldali kódtára a .NET-hez naplózásának engedélyezése:

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

## <a name="viewing-storage-metrics"></a>Storage mérőszámainak megtekintése
Miután konfigurálta a figyelheti a tárfiók tárolási analitika metrikákat, tárolási analitika rögzít a metrikák a tárfiókban lévő jól ismert táblák egy készlete. Konfigurálhatja a diagramot óránkénti metrikát a megtekintéséhez a [Azure-portálon](https://portal.azure.com):

1. Keresse meg a storage-fiókot a [Azure-portálon](https://portal.azure.com).
1. Válassza ki **metrikák** a a **menü** a szolgáltatás, amelynek meg szeretné tekinteni metrikák ablaktáblán.
1. Válassza ki **szerkesztése** konfigurálni szeretné a diagramon.
1. Az a **diagram szerkesztése lehetőséget** ablaktáblán válassza előbb a **időtartomány**, **diagramtípus**, és a metrikák, amelyeket meg szeretne jeleníteni a diagramon.
1. Kattintson az **OK** gombra.

Ha szeretné letölteni a metrikák a hosszú távú tároláshoz, vagy helyileg elemzi azokat, kell:

* Egy eszközzel, hogy ismeri a következő táblák, és lehetővé teszi a megtekintése, és letöltheti a fájlokat.
* Egy egyéni alkalmazást vagy parancsfájl beolvassa és tárolja a táblák írni.

Sok külső tároló tallózása eszközök észlelik az egyes táblák, és közvetlenül megtekintheti őket.
Lásd: [Azure Storage ügyféleszközök elől](storage-explorers.md) elérhető eszközök listáját.

> [!NOTE]
> 0.8.0 verziójával kezdődően a [Microsoft Azure Tártallózó](http://storageexplorer.com/), tekintheti meg és töltse le a analytics metrikák táblákat.
> 
> 

Ahhoz, hogy hozzáférhessen a analytics táblák programozott módon, vegye figyelembe, hogy az analytics táblák jelenik meg, ha a tárfiókban lévő összes tábla felsorolja. Közvetlenül a név alapján elérhet, vagy a [CloudAnalyticsClient API](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.analytics.cloudanalyticsclient.aspx) a a .NET ügyféloldali kódtár lekérdezni a tábla neve.

### <a name="hourly-metrics"></a>Óránkénti metrikák
* $MetricsHourPrimaryTransactionsBlob
* $MetricsHourPrimaryTransactionsTable
* $MetricsHourPrimaryTransactionsQueue

### <a name="minute-metrics"></a>Percenkénti metrikákat
* $MetricsMinutePrimaryTransactionsBlob
* $MetricsMinutePrimaryTransactionsTable
* $MetricsMinutePrimaryTransactionsQueue

### <a name="capacity"></a>Kapacitás
* $MetricsCapacityBlob

A sémák teljes körű információkat találhat meg ezek a táblázatok, [Storage Analytics metrikák táblaséma](https://msdn.microsoft.com/library/azure/hh343264.aspx). A minta sorokat megjelenítése csak az elérhető oszlopok egy részét, de néhány fontosabb funkciói a Storage Metrics menti ezeket a mérési módját mutatja be:

| PartitionKey | RowKey | Időbélyeg | TotalRequests | TotalBillableRequests | TotalIngress | TotalEgress | Rendelkezésre állás | AverageE2ELatency | AverageServerLatency | PercentSuccess |
| --- |:---:| ---:| --- | --- | --- | --- | --- | --- | --- | --- |
| 20140522T1100 |user;All |2014-05-22T11:01:16.7650250Z |7 |7 |4003 |46801 |100 |104.4286 |6.857143 |100 |
| 20140522T1100 |user;QueryEntities |2014-05-22T11:01:16.7640250Z |5 |5 |2694 |45951 |100 |143.8 |7.8 |100 |
| 20140522T1100 |user;QueryEntity |2014-05-22T11:01:16.7650250Z |1 |1 |538 |633 |100 |3 |3 |100 |
| 20140522T1100 |user;UpdateEntity |2014-05-22T11:01:16.7650250Z |1 |1 |771 |217 |100 |9 |6 |100 |

A példaadatokat percenkénti metrikákat a partíciós kulcs használja az idő percben felbontásban. A sorkulcs azonosítja a sor tárolt információ típusát. A sorkulcs kétféle információt, a hozzáférés típusa, és a kérelem áll:

* A hozzáférési típus vagy a felhasználó, vagy a rendszer, ahol felhasználói a társzolgáltatás-az összes felhasználói kérelmek hivatkozik, és a rendszer tárolási analitika kérelmeire hivatkozik.
* A kérelem típus összes ekkor az összegző sort, vagy az adott API-t például QueryEntity vagy UpdateEntity azonosítja.

A fenti mintaadatok jelennek meg a rekordok egy perc alatt (11:00 órakor kezdődően), így QueryEntities kérések száma és az QueryEntity száma lekér plusz a száma UpdateEntity kérelmek legfeljebb 7, amely az összes hozzáadása a felhasználói: minden sor látható. Ehhez hasonlóan származtatni a felhasználó: minden a sorban a átlagos végpontok közötti késés 104.4286 kiszámításával ((143.8 * 5) + 3 + 9) / 7.

## <a name="metrics-alerts"></a>Metrikák riasztások
Vegye figyelembe a riasztások beállítása a [Azure-portálon](https://portal.azure.com) , Storage Metrics automatikusan értesítheti, a tárolási szolgáltatások működésének fontos változások. Ha egy tárolási explorer eszköz használatával töltse le a metrikák tagolt formátumú adatokat, a Microsoft Excel segítségével elemezheti az adatokat. Lásd: [Azure Storage ügyféleszközök elől](storage-explorers.md) rendelkezésre álló tár explorer eszközök listáját. A riasztásokat lehet beállítani a **riasztási szabályok** ablaktábláján elérhető **figyelés** a tárolási fiók menü ablaktáblán.

> [!IMPORTANT]
> Előfordulhat, hogy egy tárolási esemény, és ha a megfelelő óránként vagy percenkénti metrikákat adatok rögzítése késleltetés. Percenkénti metrikákat bejelentkezéskor az adatok több percig egyszerre írhatók. Korábbi perces majd összevonhatók a jelenlegi percen tranzakcióban. Ez akkor fordul elő, amikor az értesítési szolgáltatás nem rendelkezhet összes elérhető adat a beállított riasztási időköz, ami azt eredményezheti, hogy a riasztást kiváltó váratlanul.
>

## <a name="accessing-metrics-data-programmatically"></a>Metrikai adatok szoftveres elérése
Az alábbi lista tartalmazza a C# mintakód perc számos percben metrikáját hozzáfér, és megjeleníti az eredményeket a konzol ablakot jeleníti meg. Az Azure Storage kódtár 4-es verzió, amely tartalmazza a CloudAnalyticsClient osztály, amely egyszerűbbé teszi a tárolási metrikák tábláit eléréséhez használ.

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

## <a name="what-charges-do-you-incur-when-you-enable-storage-metrics"></a>Milyen költségekkel tegye Ön tudomásával storage mérőszámainak engedélyezésével?
Írás a táblaentitásokat a metrikák létrehozására irányuló kéréseket van szó, minden Azure tárolási műveletek alkalmazandó szabványos ütemben.

Olvasási és törlési kérelmek metrikai adatok ügyfelek által a normál díjszabás számlázható egyaránt. Az adatmegőrzési házirend van beállítva, ha van nem szó, ha az Azure Storage törli a régi mérőszámok-adatokat. Azonban ha analitikai adatok törléséhez a fiók fel van töltve a delete művelet esetén.

A metrikák tábla által használt kapacitás egyben számlázható: a következő metrikák adatok tárolására használt kapacitás becslésére használható:

* Minden órában egy szolgáltatás minden API-nak minden szolgáltatást használja, ha majd 148KB adatot tárolja a metrikák tranzakció táblázatokban óránként Ha engedélyezte a szolgáltatás és az összefoglaló API-szintet.
* Ha minden órában egy szolgáltatás minden API-nak minden szolgáltatást használja, majd körülbelül 12KB adatot tárolják metrikák tranzakció táblázatokban óránként Ha engedélyezte az imént szolgáltatási szint összefoglaló.
* Blobok kapacitás táblához két sort hozzáadott minden nap (feltéve, hogy felhasználói naplók feliratkozott): Ez azt jelenti, hogy minden nap a táblázat mérete nő körülbelül 300 bájt.

## <a name="next-steps"></a>További lépések
[Naplózás és naplózási adatok elérése tárolási engedélyezése](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data)

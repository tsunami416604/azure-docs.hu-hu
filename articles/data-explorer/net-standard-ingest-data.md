---
title: Adatok betöltése az Azure Data Explorer .NET Standard SDK (előzetes verzió) segítségével
description: Ebben a cikkben megtudhatja, hogyan töltheti be (töltheti be) az adatokat az Azure Data Explorerbe a .NET Standard SDK használatával.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 9b6eda60f0b0cb1b697560cccc2cffe719d58536
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251777"
---
# <a name="ingest-data-using-the-azure-data-explorer-net-standard-sdk-preview"></a>Adatok betöltése az Azure Data Explorer .NET Standard SDK (előzetes verzió) használatával

Az Azure Data Explorer (ADX) egy gyors és jól méretezhető adatfeltárási szolgáltatás napló- és telemetriai adatok. Az ADX két ügyféltárat biztosít a .NET Standard számára: egy [betöltési könyvtárat](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Ingest.NETStandard) és [egy adattárat.](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Data.NETStandard) Ezekkel a kódtárakkal adatokat tölthet be egy fürtbe, illetve adatokat kérdezhet le a kódból. Ebben a cikkben először hozzon létre egy táblát és adatleképezést egy tesztfürtben. Ezután várólistára kell állnia egy betöltést a fürtbe, és érvényesítenie kell az eredményeket.

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

* [Egy tesztfürt és egy adatbázis](create-cluster-database-portal.md)

## <a name="install-the-ingest-library"></a>A betöltési tár telepítése

```
Install-Package Microsoft.Azure.Kusto.Ingest.NETStandard
```

## <a name="authentication"></a>Hitelesítés

Az alkalmazás hitelesítéséhez az Azure Data Explorer az AAD-bérlő azonosítóját használja. A bérlőazonosító megkereséséhez használja a következő URL-címet úgy, hogy a *YourDomain* kifejezés helyére a saját tartományát írja be.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Ha például a tartomány a *contoso.com*, az URL-cím a következő: [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Kattintson erre az URL-címre az eredmények megtekintéséhez; az első sor a következő. 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

A bérlőazonosító ebben az esetben a következő: `6babcaad-604b-40ac-a9d7-9fd97c0b779f`.

Ez a példa egy AAD-felhasználót és jelszót használ a fürt eléréséhez. Az AAD alkalmazástanúsítvány és az AAD alkalmazáskulcs is használható. A kód futtatása `user`előtt `password` állítsa be a, `tenantId`a értékértékét.

```csharp
var tenantId = "<TenantId>";
var user = "<User>";
var password = "<Password>";
```

## <a name="construct-the-connection-string"></a>A kapcsolati karakterlánc létrehozása
Most hozza létre a kapcsolati sztringet. Egy későbbi lépésben fogja létrehozni a céltáblát és a leképezést.

```csharp
var kustoUri = "https://<ClusterName>.<Region>.kusto.windows.net:443/";
var database = "<DatabaseName>";

var kustoConnectionStringBuilder =
    new KustoConnectionStringBuilder(kustoUri)
    {
        FederatedSecurity = true,
        InitialCatalog = database,
        UserID = user,
        Password = password,
        Authority = tenantId
    };
```

## <a name="set-source-file-information"></a>A forrásfájl adatainak beállítása

A forrásfájl elérési útjának beállítása. Ez a példa egy Azure Blob Storage-ban üzemeltetett mintafájlt használ. A **StormEvents** mintaadatkészlet a [környezeti adatok nemzeti központjaiból](https://www.ncdc.noaa.gov/stormevents/) származó, időjárással kapcsolatos adatokat tartalmaz.

```csharp
var blobPath = "https://kustosamplefiles.blob.core.windows.net/samplefiles/StormEvents.csv?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
```

## <a name="create-a-table-on-your-test-cluster"></a>Tábla létrehozása a tesztfürtön
Hozzon létre `StormEvents` egy olyan nevű táblát, amely `StormEvents.csv` megfelel a fájlban lévő adatok sémájának.

```csharp
var table = "StormEvents";
using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    var command =
        CslCommandGenerator.GenerateTableCreateCommand(
            table,
            new[]
            {
                Tuple.Create("StartTime", "System.DateTime"),
                Tuple.Create("EndTime", "System.DateTime"),
                Tuple.Create("EpisodeId", "System.Int32"),
                Tuple.Create("EventId", "System.Int32"),
                Tuple.Create("State", "System.String"),
                Tuple.Create("EventType", "System.String"),
                Tuple.Create("InjuriesDirect", "System.Int32"),
                Tuple.Create("InjuriesIndirect", "System.Int32"),
                Tuple.Create("DeathsDirect", "System.Int32"),
                Tuple.Create("DeathsIndirect", "System.Int32"),
                Tuple.Create("DamageProperty", "System.Int32"),
                Tuple.Create("DamageCrops", "System.Int32"),
                Tuple.Create("Source", "System.String"),
                Tuple.Create("BeginLocation", "System.String"),
                Tuple.Create("EndLocation", "System.String"),
                Tuple.Create("BeginLat", "System.Double"),
                Tuple.Create("BeginLon", "System.Double"),
                Tuple.Create("EndLat", "System.Double"),
                Tuple.Create("EndLon", "System.Double"),
                Tuple.Create("EpisodeNarrative", "System.String"),
                Tuple.Create("EventNarrative", "System.String"),
                Tuple.Create("StormSummary", "System.Object"),
            });

    kustoClient.ExecuteControlCommand(command);
}
```

## <a name="define-ingestion-mapping"></a>Adatbetöltési leképezés meghatározása

Rendelje hozzá a bejövő CSV-adatokat a tábla létrehozásakor használt oszlopnevekhez.
[CSV-oszlop-hozzárendelési objektum](/azure/kusto/management/create-ingestion-mapping-command) kiépítése a táblában

```csharp
var tableMapping = "StormEvents_CSV_Mapping";
using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    var command =
        CslCommandGenerator.GenerateTableCsvMappingCreateCommand(
            table,
            tableMapping,
            new[]
            {
                new CsvColumnMapping { ColumnName = "StartTime", Ordinal = 0 },
                new CsvColumnMapping { ColumnName = "EndTime", Ordinal = 1 },
                new CsvColumnMapping { ColumnName = "EpisodeId", Ordinal = 2 },
                new CsvColumnMapping { ColumnName = "EventId", Ordinal = 3 },
                new CsvColumnMapping { ColumnName = "State", Ordinal = 4 },
                new CsvColumnMapping { ColumnName = "EventType", Ordinal = 5 },
                new CsvColumnMapping { ColumnName = "InjuriesDirect", Ordinal = 6 },
                new CsvColumnMapping { ColumnName = "InjuriesIndirect", Ordinal = 7 },
                new CsvColumnMapping { ColumnName = "DeathsDirect", Ordinal = 8 },
                new CsvColumnMapping { ColumnName = "DeathsIndirect", Ordinal = 9 },
                new CsvColumnMapping { ColumnName = "DamageProperty", Ordinal = 10 },
                new CsvColumnMapping { ColumnName = "DamageCrops", Ordinal = 11 },
                new CsvColumnMapping { ColumnName = "Source", Ordinal = 12 },
                new CsvColumnMapping { ColumnName = "BeginLocation", Ordinal = 13 },
                new CsvColumnMapping { ColumnName = "EndLocation", Ordinal = 14 },
                new CsvColumnMapping { ColumnName = "BeginLat", Ordinal = 15 },
                new CsvColumnMapping { ColumnName = "BeginLon", Ordinal = 16 },
                new CsvColumnMapping { ColumnName = "EndLat", Ordinal = 17 },
                new CsvColumnMapping { ColumnName = "EndLon", Ordinal = 18 },
                new CsvColumnMapping { ColumnName = "EpisodeNarrative", Ordinal = 19 },
                new CsvColumnMapping { ColumnName = "EventNarrative", Ordinal = 20 },
                new CsvColumnMapping { ColumnName = "StormSummary", Ordinal = 21 },
            });

    kustoClient.ExecuteControlCommand(command);
}
```

## <a name="queue-a-message-for-ingestion"></a>Üzenet várólistába helyezése a betöltéshez

Üzenet várólistára állítása a blobtárolóból származó adatok lekérése és az adatok ADX-be való betöltése.

```csharp
var ingestUri = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/";
var ingestConnectionStringBuilder =
    new KustoConnectionStringBuilder(ingestUri)
    {
        FederatedSecurity = true,
        InitialCatalog = database,
        UserID = user,
        Password = password,
        Authority = tenantId
    };

using (var ingestClient = KustoIngestFactory.CreateQueuedIngestClient(ingestConnectionStringBuilder))
{
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.csv,
            CSVMappingReference = tableMapping,
            IgnoreFirstRecord = true
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
}
```

## <a name="validate-data-was-ingested-into-the-table"></a>Adatok ellenőrzése a táblába

Várjon 5-10 percet, amíg a várólistára helyezett betöltés ütemezi a betöltést, és betölti az adatokat az ADX-be. Ezután futtassa a következő kódot a `StormEvents`-táblában lévő rekordok számának lekérdezéséhez.

```csharp
using (var cslQueryProvider = KustoClientFactory.CreateCslQueryProvider(kustoConnectionStringBuilder))
{
    var query = $"{table} | count";

    var results = cslQueryProvider.ExecuteQuery<long>(query);
    Console.WriteLine(results.Single());
}
```

## <a name="run-troubleshooting-queries"></a>Hibaelhárítási lekérdezések futtatása

Jelentkezzen be, [https://dataexplorer.azure.com](https://dataexplorer.azure.com) és csatlakozzon a fürthöz. Futtassa a következő parancsot az adatbázisban annak ellenőrzéséhez, hogy voltak-e betöltési hibák az elmúlt négy órában. A futtatás előtt cserélje le az adatbázis nevét.

```Kusto
.show ingestion failures
| where FailedOn > ago(4h) and Database == "<DatabaseName>"
```

Futtassa a következő parancsot az elmúlt négy órában végzett összes betöltési művelet állapotának megtekintéséhez. A futtatás előtt cserélje le az adatbázis nevét.

```Kusto
.show operations
| where StartedOn > ago(4h) and Database == "<DatabaseName>" and Operation == "DataIngestPull"
| summarize arg_max(LastUpdatedOn, *) by OperationId
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy követi a többi cikket, tartsa meg a létrehozott erőforrásokat. Ha nem szeretné, futtassa a következő parancsot az adatbázisban a `StormEvents`-tábla felesleges elemeinek eltávolításához.

```Kusto
.drop table StormEvents
```

## <a name="next-steps"></a>További lépések

* [Lekérdezések írása](write-queries.md)

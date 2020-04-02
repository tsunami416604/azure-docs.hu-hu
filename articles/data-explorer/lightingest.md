---
title: A LightIngest egy parancssori segédprogram az Azure Data Explorer betöltéséhez.
description: Ismerje meg a LightIngest parancssori segédprogramot az Azure Data Explorerbe történő ad hoc adatbetöltéshez.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 95d943685cf511acb88f9e48d36a9dd43b0a27d2
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548016"
---
# <a name="install-and-use-lightingest"></a>A LightIngest telepítése és használata

A LightIngest egy parancssori segédprogram az Azure Data Explorerbe történő ad-hoc adatbetöltéshez.
A segédprogram lehívhatja a forrásadatokat egy helyi mappából vagy egy Azure blob storage tárolóból.

## <a name="prerequisites"></a>Előfeltételek

* LightIngest – töltse le a [Microsoft.Azure.Kusto.Tools NuGet csomag](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/) részeként

    ![Legmegvilágításos letöltés](media/lightingest/lightingest-download-area.png)

* WinRAR - töltse le [www.win-rar.com/download.html](http://www.win-rar.com/download.html)

## <a name="install-lightingest"></a>Lightingest telepítése

1. Nyissa meg azt a helyet a számítógépen, ahol a LightIngest-et töltötte. 
1. A WinRAR használatával bontsa ki az *eszközkönyvtárat* a számítógépre.

## <a name="run-lightingest"></a>Lightingest futtatása

1. Nyissa meg a kinyert *eszközök* könyvtárát a számítógépen.
1. Törölje a meglévő helyadatokat a helysávról.
    
      ![Helyadatok törlése](media/lightingest/lightingest-location-bar.png)

1. Írja `cmd` be és nyomja **le az Enter billentyűt.**
1. A parancssorba `LightIngest.exe` írja be a megfelelő parancssori argumentumot.

    > [!Tip]
    > A támogatott parancssori argumentumok `LightIngest.exe /help`listájához írja be a be a című beírást.
    >
    >![Parancssorsúgó súgója](media/lightingest/lightingest-cmd-line-help.png)

1. Adja `ingest-` meg a kapcsolati karakterláncot az Azure Data Explorer-fürthöz, amely kezeli a betöltést.
    A kapcsolati karakterláncot idézőjelek közé kell tenni, és kövesse a [Kusto kapcsolati karakterláncok specifikációját.](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto)

    Példa:
    ```
    ingest-{Cluster name and region}.kusto.windows.net;AAD Federated Security=True -db:{Database} -table:Trips -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}" -pattern:"*.csv.gz" -format:csv -limit:2 -ignoreFirst:true -cr:10.0 -dontWait:true
    ```

* Az ajánlott módszer a LightIngest a betöltési `https://ingest-{yourClusterNameAndRegion}.kusto.windows.net`végponttal való munkához. Ily módon az Azure Data Explorer szolgáltatás kezelheti a betöltési terhelést, és könnyen helyreállítható az átmeneti hibák. A LightIngest azonban beállítható úgy is, hogy`https://{yourClusterNameAndRegion}.kusto.windows.net`közvetlenül a motorvégponttal ( működjön.

> [!Note]
> Ha közvetlenül a motor végpontjával megy be, `ingest-`nem kell megadnia, de nem lesz DM funkció a motor védelmére és a betöltési sikerességi arány javítására.

* Az optimális betöltési teljesítmény érdekében fontos, hogy a LightIngest ismerje a nyers adatok méretét, és így a LightIngest megbecsüli a helyi fájlok tömörítetlen méretét. Előfordulhat azonban, hogy a LightIngest nem tudja megfelelően megbecsülni a tömörített blobok nyers méretét anélkül, hogy először le kellene töltenie őket. Ezért tömörített blobok betöltésekor `rawSizeBytes` állítsa be a tulajdonságot a blob metaadatok tömörítetlen adatméret bájtban.

## <a name="general-command-line-arguments"></a>Általános parancssori argumentumok

|Argumentum neve         |Rövid név   |Típus    |Kötelező |Leírás                                |
|----------------------|-------------|--------|----------|-------------------------------------------|
|                      |             |sztring  |Kötelező |[Az Azure Data Explorer kapcsolati karakterlánca](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto) megadja a Kusto-végpontot, amely kezeli a betöltést. Idézőjelek közé kell tenni |
|-adatbázis             |-db          |sztring  |Optional  |Az Azure Data Explorer adatbázisának neve |
|-asztal                |             |sztring  |Kötelező |Az Azure Data Explorer tábla neve |
|-sourcePath           |-forrás      |sztring  |Kötelező |A forrásfájlok elérési útja vagy a blobtároló gyökér URI-ja. Ha az adatok blobokban vannak, tárfiókkulcsot vagy SAS-t kell tartalmazniuk. Dupla idézőjelek közé van takarva |
|-előtag               |             |sztring  |Optional  |Amikor a betöltési forrásadatok blob storage-ban található, ezt az URL-előtagot az összes blob oka megosztja, kivéve a tároló nevét. <br>Ha például az adatok `MyContainer/Dir1/Dir2`a ban vannak, `Dir1/Dir2`akkor az előtagnak kell lennie. Dupla idézőjelek közé ágyazás ajánlott |
|-minta              |             |sztring  |Optional  |Minta, amely forrásfájlok/blobok szedett. Támogatja a helyettesítő karaktereket. Például: `"*.csv"`. Dupla idézőjelek közé van takarva |
|-zipPattern           |             |sztring  |Optional  |A zip-archívumban a betöltéshez használt fájlok kiválasztásakor használandó reguláris kifejezés.<br>Az archívumban lévő összes többi fájlt a rendszer figyelmen kívül hagyja. `"*.csv"`Például. Javasoljuk, hogy dupla idézőjelben vegye körül |
|-formátum               |-f           |sztring  |Optional  |Forrásadat-formátum. A [támogatott formátumok](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats) egyikének kell lennie |
|-inestionMappingPath |-mappingPath |sztring  |Optional  |A betöltési oszlopleképezési fájl elérési útja (json- és Avro-formátumok esetén kötelező). [Adatleképezések megtekintése](https://docs.microsoft.com/azure/kusto/management/mappings) |
|-inestionMappingRef  |-mappingRef  |sztring  |Optional  |Előre létrehozott betöltési oszlopleképezés neve (json- és Avro-formátumok esetén kötelező). [Adatleképezések megtekintése](https://docs.microsoft.com/azure/kusto/management/mappings) |
|-creationTimePattern  |             |sztring  |Optional  |Ha be van állítva, a creationtime tulajdonság kibontására szolgál a fájlból vagy a blob elérési útjából. Lásd: [CreationTimePattern argumentum használata](#using-creationtimepattern-argument) |
|-ignoreFirstRow       |-ignoreFirst |Bool    |Optional  |Ha be van állítva, a rendszer figyelmen kívül hagyja az egyes fájlok/blobok első rekordját (például ha a forrásadatok fejléceket tartalmaznak) |
|-tag                  |             |sztring  |Optional  |A bevitt adatokhoz társítani való [címkék.](https://docs.microsoft.com/azure/kusto/management/extents-overview#extent-tagging) Több előfordulás megengedett |
|-dontVárjon             |             |Bool    |Optional  |Ha értéke "true", nem várja meg a betöltés befejezését. Hasznos nagy mennyiségű fájl/blob betöltésekén |

### <a name="using-creationtimepattern-argument"></a>CreationTimePattern argumentum használata

Az `-creationTimePattern` argumentum kinyeri a CreationTime tulajdonságot a fájlból vagy a blob elérési útjából. A mintának nem kell tükröznie a teljes elemelérési utat, csak a használni kívánt időbélyeget körülvevő szakaszt.

Az argumentumértékeknek a következőket kell tartalmazniuk:
* Az időbélyeget közvetlenül megelőző állandó vizsgálat, egyszeres idézőjelek között
* Az időbélyeg formátuma szabványos [.NET DateTime jelölésben](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)
* Az időbélyeget közvetlenül követő állandó szöveg. Ha például a blobnevek végződésével `historicalvalues19840101.parquet` végződik (az időbélyeg az év négy számjegye, a hónap két számjegye és a hónap napjának két számje), az argumentum megfelelő értéke a `-creationTimePattern` következő:

```
ingest-{Cluster name and region}.kusto.windows.net;AAD Federated Security=True -db:{Database} -table:Trips -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}" -creationTimePattern:"'historicalvalues'yyyyMMdd'.parquet'"
 -pattern:"*.csv.gz" -format:csv -limit:2 -ignoreFirst:true -cr:10.0 -dontWait:true
```

### <a name="command-line-arguments-for-advanced-scenarios"></a>Parancssori argumentumok speciális forgatókönyvekhez

|Argumentum neve         |Rövid név   |Típus    |Kötelező |Leírás                                |
|----------------------|-------------|--------|----------|-------------------------------------------|
|-tömörítés          |-cr          |double  |Optional  |A kompressziós arány tipp. Akkor hasznos, ha tömörített fájlokat/blobokat szeretne beadni, hogy az Azure Data Explorer felmérhesse a nyers adatok méretét. Az eredeti méret és a tömörített méret hányadosaként számítva |
|-limit                |-l           |egész szám |Optional  |Ha be van állítva, korlátozza a betöltést az első N-fájlokra |
|-listOnly             |-lista        |Bool    |Optional  |Ha be van állítva, csak azokat az elemeket jeleníti meg, amelyeket lekellett volna választani a lenyeléshez.| 
|-betöltésTimeout        |             |egész szám |Optional  |Időmeghosszabbítás percben az összes betöltési művelet befejezéséhez. Alapértelmezés szerint`60`|
|-forceSync            |             |Bool    |Optional  |Ha be van állítva, a szinkron betöltést kényszeríti. Alapértelmezés szerint`false` |
|-dataBatchSize        |             |egész szám |Optional  |Az egyes betöltési műveletek teljes méretkorlátjának (MB, tömörítetlen) beállítása |
|-filesInBatch         |             |egész szám |Optional  |Beállítja az egyes betöltési műveletek fájl-/blobszámlálási korlátját |
|-devTracing           |-nyomkövetés       |sztring  |Optional  |Ha be van állítva, a diagnosztikai naplók `RollingLogs` a helyi könyvtárba kerülnek (alapértelmezés szerint az aktuális könyvtárban, vagy a kapcsolóérték beállításával módosíthatók) |

## <a name="blob-metadata-properties"></a>Blob metaadatok tulajdonságai
Az Azure-blobok használata esetén a LightIngest bizonyos blobmetaadat-tulajdonságokat fog használni a betöltési folyamat bővítéséhez.

|Metaadat-tulajdonság                            | Használat                                                                           |
|---------------------------------------------|---------------------------------------------------------------------------------|
|`rawSizeBytes`, `kustoUncompressedSizeBytes` | Ha be van állítva, a rendszer a tömörítetlen adatméretként fogja értelmezni                       |
|`kustoCreationTime`, `kustoCreationTimeUtc`  | UTC időbélyegként értelmezve. Ha be van állítva, a kusto-beli létrehozási idő felülbírálására lesz használva. Hasznos a feltöltési forgatókönyvekhez |

## <a name="usage-examples"></a>Használati példák

<!-- Waiting for Tzvia or Vladik to rewrite the instructions for this example before publishing it

### Ingesting a specific number of blobs in JSON format

* Ingest two blobs under a specified storage account {Account}, in `JSON` format matching the pattern `.json`
* Destination is the database {Database}, the table `SampleData`
* Indicate that your data is compressed with the approximate ratio of 10.0
* LightIngest won't wait for the ingestion to be completed

To use the LightIngest command below:
1. Create a table command and enter the table name into the LightIngest command, replacing `SampleData`.
1. Create a mapping command and enter the IngestionMappingRef command, replacing `SampleData_mapping`.
1. Copy your cluster name and enter it into the LightIngest command, replacing `{ClusterandRegion}`.
1. Enter the database name into the LightIngest command, replacing `{Database name}`.
1. Replace `{Account}` with your account name and replace `{ROOT_CONTAINER}?{SAS token}` with the appropriate information.

    ```
    LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"  
        -db:{Database name} 
        -table:SampleData 
        -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER}?{SAS token}" 
        -IngestionMappingRef:SampleData_mapping 
        -pattern:"*.json" 
        -format:JSON 
        -limit:2 
        -cr:10.0 
        -dontWait:true
    ```
     
1. In Azure Data Explorer, open query count.

    ![Injestion result in Azure Data Explorer](media/lightingest/lightingest-show-failure-count.png)
-->

### <a name="ingesting-blobs-using-a-storage-account-key-or-a-sas-token"></a>Blobok betöltése tárfiók-kulccsal vagy SAS-jogkivonatokkal

* 10 blob betöltése a `ACCOUNT`megadott tárfiók alatt , a mappában `DIR`, a tároló `CONT`alatt , és a mintának megfelelő`*.csv.gz`
* A cél `DB`az `TABLE`adatbázis , tábla `MAPPING` , és a betöltési hozzárendelés elővan hozva a célhelyen
* Az eszköz megvárja, amíg a betöltési műveletek befejeződnek
* Jegyezze fel a céladatbázis és a tárfiók kulcsának és a SAS-jogkivonatnak a különböző beállításait.

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"https://ACCOUNT.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}"
  -prefix:"DIR"
  -pattern:*.csv.gz
  -format:csv
  -mappingRef:MAPPING
  -limit:10

LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True;Initial Catalog=DB"
  -table:TABLE
  -source:"https://ACCOUNT.blob.core.windows.net/{ROOT_CONTAINER}?{SAS token}"
  -prefix:"DIR"
  -pattern:*.csv.gz
  -format:csv
  -mappingRef:MAPPING
  -limit:10
```

### <a name="ingesting-all-blobs-in-a-container-not-including-header-rows"></a>Az összes blob betöltése egy tárolóban, a fejlécsorok nem beleértve

* A megadott tárfiók `ACCOUNT`alatt az összes `DIR1/DIR2`blob betöltése a mappában , a tároló `CONT`alatt, és a mintának megfelelő`*.csv.gz`
* A cél `DB`az `TABLE`adatbázis , tábla `MAPPING` , és a betöltési hozzárendelés elővan hozva a célhelyen
* A forrásblobok fejlécvonalat tartalmaznak, ezért az eszköz utasítást kap arra, hogy dobja el az egyes blobok első rekordját
* Az eszköz könyveli az adatokat a betöltéshez, és nem várja meg, amíg a betöltési műveletek befejeződnek

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"https://ACCOUNT.blob.core.windows.net/{ROOT_CONTAINER}?{SAS token}"
  -prefix:"DIR1/DIR2"
  -pattern:*.csv.gz
  -format:csv
  -mappingRef:MAPPING
  -ignoreFirstRow:true
```

### <a name="ingesting-all-json-files-from-a-path"></a>Az összes JSON-fájl betöltése egy elérési útról

* Az összes fájl `PATH`betöltése a görbe alá , amely megfelel a mintának`*.json`
* A cél `DB`az `TABLE`adatbázis , tábla , és a betöltési leképezés a helyi fájlban van definiálva.`MAPPING_FILE_PATH`
* Az eszköz könyveli az adatokat a betöltéshez, és nem várja meg, amíg a betöltési műveletek befejeződnek

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"PATH"
  -pattern:*.json
  -format:json
  -mappingPath:"MAPPING_FILE_PATH"
```

### <a name="ingesting-files-and-writing-diagnostic-trace-files"></a>Fájlok betöltése és diagnosztikai nyomkövetési fájlok írása

* Az összes fájl `PATH`betöltése a görbe alá , amely megfelel a mintának`*.json`
* A cél `DB`az `TABLE`adatbázis , tábla , és a betöltési leképezés a helyi fájlban van definiálva.`MAPPING_FILE_PATH`
* Az eszköz könyveli az adatokat a betöltéshez, és nem várja meg, amíg a betöltési műveletek befejeződnek
* A diagnosztika nyomkövetési fájljai helyileg lesznek írva a mappa alá`LOGS_PATH`

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"PATH"
  -pattern:*.json
  -format:json
  -mappingPath:"MAPPING_FILE_PATH"
  -trace:"LOGS_PATH"
```
## <a name="changelog"></a>Változatlan
|Verzió        |Változások                                                                             |
|---------------|------------------------------------------------------------------------------------|
|4.0.9.0        |<ul><li>Hozzáadott `-zipPattern` argumentum</li><li>Hozzáadott `-listOnly` argumentum</li><li>Az argumentumok összegzése a futtatás megkezdése előtt jelenik meg</li><li>CreationTime a blob metaadat-tulajdonságaiból, illetve a blob `-creationTimePattern` vagy a fájlnévből olvasható az argumentum nak megfelelően</li></ul>|

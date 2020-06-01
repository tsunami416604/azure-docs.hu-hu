---
title: T-SQL streaming-feladatok létrehozása az Azure SQL Edge-ben (előzetes verzió)
description: Tudnivalók Stream Analytics feladatok létrehozásáról az Azure SQL Edge-ben (előzetes verzió)
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 323ec00667350917e6b16827f908ac1abeee77d6
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/31/2020
ms.locfileid: "84233305"
---
# <a name="create-stream-analytics-job-in-azure-sql-edge-preview"></a>Stream Analytics-feladatok létrehozása az Azure SQL Edge-ben (előzetes verzió) 

Ez a cikk azt ismerteti, hogyan lehet T-SQL streaming-feladatot létrehozni az Azure SQL Edge-ben (előzetes verzió). A folyamatos átviteli feladatok SQL Edge-ben való létrehozásához a következő lépések szükségesek

1. Külső stream bemeneti és kimeneti objektumainak létrehozása
2. A folyamatos átviteli feladatok létrehozásának részeként adja meg a folyamatos átviteli feladatok lekérdezését.

> [!NOTE]
> Az Azure SQL Edge-ben a T-SQL streaming funkció engedélyezéséhez engedélyezze a TF 11515-as indítási lehetőséget, vagy használja a [DBCC TRACEON]( https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql) parancsot. További információ a nyomkövetési jelzők MSSQL. conf fájl használatával történő engedélyezéséről: [Konfigurálás az MSSQL. conf fájllal](configure.md#configure-using-mssqlconf-file). Ez a követelmény el lesz távolítva az Azure SQL Edge (előzetes verzió) jövőbeli frissítéseiben.

## <a name="configure-an-external-stream-input-and-output-object"></a>Külső stream bemeneti és kimeneti objektumának konfigurálása

A T-SQL streaming a SQL Server külső adatforrás-funkciójának használatával határozza meg a folyamatos átviteli feladatok külső stream-bemenetekhez és kimenetekhez társított adatforrásokat. A következő T-SQL-parancsok szükségesek külső adatfolyam-bemeneti vagy kimeneti objektum létrehozásához.

[CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql)

[CREATE EXTERNAL DATA SOURCE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql)

[KÜLSŐ STREAM létrehozása (Transact-SQL)](#example-create-an-external-stream-object-sql-database)

Emellett az SQL Edge (vagy SQL Server, Azure SQL) kimeneti adatfolyamként való használata esetén az [adatbázis-hatókörű hitelesítő adatok létrehozása (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-database-scoped-credential-transact-sql) T-SQL parancs szükséges a hitelesítő adatok az SQL Database-hez való hozzáféréshez való definiálásához.

### <a name="supported-input-and-output-stream-data-sources"></a>Támogatott bemeneti és kimeneti adatfolyam-adatforrások

Az Azure SQL Edge jelenleg csak a következő adatforrásokat támogatja stream-bemenetként és-kimenetként.

| Adatforrás típusa | Input (Bemenet) | Kimenet | Leírás |
|------------------|-------|--------|------------------|
| Azure IoT Edge hub | Y | Y | Az adatforrást az adatfolyamok Azure IoT Edge hubhoz való olvasására és írására. Azure IoT Edge hubhoz vonatkozó további információkért lásd: [IoT Edge hub](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub)|
| SQL Database | N | Y | Adatforrás-kapcsolódás az adatfolyam-adatSQL Databaseba való íráshoz. A SQL Database lehet helyi SQL Edge-adatbázis vagy távoli SQL Server vagy Azure SQL Database|
| Azure Blob Storage | N | Y | Adatforrás, amely az Azure Storage-fiókban lévő blobba írja az adatforrást. |
| Kafka | Y | N | Adatforrást egy Kafka-témakörben lévő adatfolyam-adatok olvasásához. Ez az adapter jelenleg csak az Azure SQL Edge Intel/AMD verziójához érhető el, és nem érhető el az SQL Edge ARM64 verziójához.|

### <a name="example-create-an-external-stream-inputoutput-object-for-azure-iot-edge-hub"></a>Példa: külső stream bemeneti/kimeneti objektum létrehozása Azure IoT Edge hubhoz

Az alábbi példa egy külső stream objektumot hoz létre a peremhálózati hubhoz. Ha Azure IoT Edge hub külső stream bemeneti/kimeneti adatforrását szeretné létrehozni, először létre kell hoznia egy külső fájlformátumot az SQL-hez, hogy megértse az adatok elrendezését.

1. Hozzon létre egy külső fájlformátumot a JSON formátum típusával.

    ```sql
    Create External file format InputFileFormat
    WITH (  
       format_type = JSON,
    )
    go
    ```

2. Hozzon létre egy külső adatforrást az IoT Edge hub számára. Az alábbi T-SQL-szkript létrehoz egy adatforrás-kapcsolódást egy olyan peremhálózati hubhoz, amely ugyanazon a Docker-gazdagépen fut, mint az SQL Edge.

    ```sql
    CREATE EXTERNAL DATA SOURCE EdgeHubInput WITH (
    LOCATION = 'edgehub://'
    )
    go
    ```

3. Hozza létre a külső stream objektumot az IoT Edge hub számára. Az alábbi T-SQL-szkript létrehoz egy stream objektumot a peremhálózati hubhoz. Edge hub Stream-objektum esetén a LOCATION paraméter a (z) a (z) és a (z) rendszerhez tartozó Edge hub-témakör vagy-csatorna neve.

    ```sql
    CREATE EXTERNAL STREAM MyTempSensors WITH (
    DATA_SOURCE = EdgeHubInput,
    FILE_FORMAT = InputFileFormat,
    LOCATION = N'TemperatureSensors',
    INPUT_OPTIONS = N'',
    OUTPUT_OPTIONS = N''
    )
    go
    ```

### <a name="example-create-an-external-stream-object-sql-database"></a>Példa: külső Stream objektum létrehozása SQL Database

Az alábbi példa egy külső stream objektumot hoz létre a helyi SQL Edge-adatbázishoz. 

1. Hozzon létre egy főkulcsot az adatbázisban. Ez a hitelesítő adatok titkos kulcsának titkosításához szükséges.

    ```sql
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<<Strong_Password_For_Master_Key_Encryption>>';
    ```

2. Hozzon létre egy adatbázis-hatókörrel rendelkező hitelesítő adatot a SQL Server forrás eléréséhez. A következő példa hitelesítő adatokat hoz létre a külső adatforráshoz az IDENTITY = "username" és a SECRET = "password" névvel.

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL SQLCredential
    WITH IDENTITY = '<SQL_Login>', SECRET = '<SQL_Login_PASSWORD>'
    go
    ```

3. Hozzon létre egy külső adatforrást külső ADATFORRÁS LÉTREHOZÁSával. A következő példa:

    * Létrehoz egy LocalSQLOutput nevű külső adatforrást.
    * A külső adatforrás (LOCATION = ' <vendor> :// <server> [: <port> ] ') azonosítása. A példában az SQL Edge helyi példányára mutat.
    * Végül a példa a korábban létrehozott hitelesítő adatokat használja.

    ```sql
    CREATE EXTERNAL DATA SOURCE LocalSQLOutput WITH (
    LOCATION = 'sqlserver://tcp:.,1433'
    ,CREDENTIAL = SQLCredential
    )
    go
    ```

4. Hozza létre a külső stream objektumot. Az alábbi példa egy külső stream objektumot hoz létre, amely egy tábla *dbo mutat. TemperatureMeasurements* az adatbázis *MySQLDatabase*.

    ```sql
    CREATE EXTERNAL STREAM TemperatureMeasurements WITH (
    DATA_SOURCE = LocalSQLOutput,
    LOCATION = N'MySQLDatabase.dbo.TemperatureMeasurements',
    INPUT_OPTIONS = N'',
    OUTPUT_OPTIONS = N''
    )
    ```

## <a name="create-the-streaming-job-and-the-streaming-queries"></a>A folyamatos átviteli feladatok és a folyamatos átviteli lekérdezések létrehozása

Az adatfolyam-lekérdezések definiálásához és a folyamatos átviteli feladat létrehozásához használja a **sys. sp_create_streaming_job** rendszer tárolt eljárását. A **sp_create_streaming_job** tárolt eljárás két paramétert vesz igénybe

- job_name – a folyamatos átviteli feladatok neve. A folyamatos átviteli feladatok nevei egyediek a példányok között.
- utasítás – [stream Analytics lekérdezési nyelven](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?) alapuló streaming Query-utasítások.

Az alábbi példa egy egyszerű folyamatos átviteli feladatot hoz létre egy adatfolyam-lekérdezéssel. Ez a lekérdezés beolvassa a bemeneteket a peremhálózati hubhoz, és írást ír a *dbo. TemperatureMeasurements* az adatbázisban.

```sql
EXEC sys.sp_create_streaming_job @name=N'StreamingJob1',
@statement= N'Select * INTO TemperatureMeasurements from MyEdgeHubInput'
```

Az alábbi példa egy összetettebb folyamatos átviteli feladatot hoz létre több különböző lekérdezéssel, beleértve egy olyan lekérdezést, amely a beépített AnomalyDetection_ChangePoint függvényt használja a hőmérsékleti adatokban észlelt rendellenességek azonosítására.

```sql
EXEC sys.sp_create_streaming_job @name=N'StreamingJob2', @statement=
N' Select * INTO TemperatureMeasurements1 from MyEdgeHubInput1

Select * Into TemperatureMeasurements2 from MyEdgeHubInput2

Select * Into TemperatureMeasurements3 from MyEdgeHubInput3

SELECT
Timestamp as [Time],
[Temperature] As [Temperature],
GetRecordPropertyValue(AnomalyDetection_ChangePoint(Temperature, 80, 1200) OVER(LIMIT DURATION(minute, 20)), ''Score'') as ChangePointScore,
GetRecordPropertyValue(AnomalyDetection_ChangePoint(Temperature, 80, 1200) OVER(LIMIT DURATION(minute, 20)), ''IsAnomaly'') as IsChangePointAnomaly,
INTO TemperatureAnomalies FROM MyEdgeHubInput2;
'
go
```

## <a name="start-stop-drop-and-monitor-streaming-jobs"></a>Streaming-feladatok elindítása, leállítása, eldobása és figyelése

Ha egy folyamatos átviteli feladatot szeretne elindítani az SQL Edge-ben, hajtsa végre a **sys. sp_start_streaming_job** tárolt eljárást. A tárolt eljárás működéséhez az szükséges, hogy a folyamatos átviteli feladat elinduljon bemenetként.

```sql
exec sys.sp_start_streaming_job @name=N'StreamingJob1'
go
```

Ha le szeretne állítani egy folyamatos átviteli feladatot az SQL Edge-ben, hajtsa végre a **sys. sp_stop_streaming_job** tárolt eljárást. A tárolt eljárásnak meg kell egyeznie a folyamatos átviteli feladattal, amely bemenetként van megszakítva.

```sql
exec sys.sp_stop_streaming_job @name=N'StreamingJob1'
go
```

Ha az SQL Edge-ben szeretné eldobni (vagy törölni) egy folyamatos átviteli feladatot, hajtsa végre a **sys. sp_drop_streaming_job** tárolt eljárást. A tárolt eljárásnak meg kell egyeznie a folyamatos átviteli feladatokkal, mint a bemenet.

```sql
exec sys.sp_drop_streaming_job @name=N'StreamingJob1'
go
```

Egy folyamatos átviteli feladat aktuális állapotának lekéréséhez az SQL Edge-ben hajtsa végre a **sys. sp_get_streaming_job** tárolt eljárást. A tárolt eljárásnak meg kell egyeznie a folyamatos átviteli feladattal, hogy beírja a bemenetet, és kiírja a folyamatos átviteli feladat nevét és aktuális állapotát.

```sql
exec sys.sp_get_streaming_job @name=N'StreamingJob1'
        WITH RESULT SETS
(
       (
       name nvarchar(256),
       status nvarchar(256)
       )
)
```

A folyamatos átviteli feladatot a következő állapotok bármelyike lehet:

| Állapot | Leírás |
|--------| ------------|
| Létrehozva | A folyamatos átviteli feladatot létrehozták, de még nincs elindítva |
| Indítás | A folyamatos átviteli feladatok a kezdeti fázisban vannak |
| Üresjárati | A folyamatos átviteli feladatot futtatja, de nincs feldolgozható bemenet. |
| Feldolgozás | A folyamatos átviteli feladatot futtatja, és feldolgozza a bemeneteket. Ez az állapot a folyamatos átviteli feladatokhoz tartozó kifogástalan állapotot jelzi. |
| Csökkentett teljesítményű | A folyamatos átviteli feladatok futtatása folyamatban van, de néhány nem végzetes bemeneti/kimeneti szerializálási/deszerializálási hiba történt a bemeneti feldolgozás során. A bemeneti feladatot a rendszer továbbra is futtatja, de a hibákkal kapcsolatos bemenetek eldobása is megtörténik. |
| Leállítva | A folyamatos átviteli feladatot leállították |
| Sikertelen | A folyamatos átviteli feladatot nem sikerült végrehajtani. Ez általában egy végzetes hibát jelez a feldolgozás során. |

## <a name="next-steps"></a>Következő lépések

- [A folyamatos átviteli feladatokhoz tartozó metaadatok megtekintése az Azure SQL Edge-ben (előzetes verzió)](streaming-catalog-views.md) 
- [Külső stream létrehozása](create-external-stream-transact-sql.md)

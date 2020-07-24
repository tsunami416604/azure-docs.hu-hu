---
title: KÜLSŐ STREAM létrehozása (Transact-SQL) – Azure SQL Edge (előzetes verzió)
description: További tudnivalók a külső STREAM-utasítás létrehozása az Azure SQL Edge-ben (előzetes verzió)
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: efc58a15dbd2c42060d0ebb4e75a1a20d4b3d06f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87067387"
---
# <a name="create-external-stream-transact-sql"></a>KÜLSŐ STREAM létrehozása (Transact-SQL)

A külső STREAM objektumnak kettős célja van a bemenet és a kimenet is. A szolgáltatás bemenetként használható az esemény-betöltési szolgáltatások, például az Azure Event vagy a IoT hubok adatfolyam-adatainak lekérdezéséhez, vagy kimenetként használható, hogy megadja, hol és hogyan kell tárolni az eredményeket egy streaming-lekérdezésből.

Egy külső STREAM is megadható és létrehozható kimenetként és bemenetként olyan szolgáltatásokhoz, mint az Event hub vagy a blob Storage. Ez olyan láncolási forgatókönyvek esetében fordul elő, amikor a folyamatos átviteli lekérdezés eredményként a külső stream kimenetként és egy másik adatfolyam-lekérdezésként való olvasását is megőrzi a bemenet alapján. 

Az Azure SQL Edge jelenleg csak a következő stream-bemeneteket és-kimeneteket támogatja.

**Stream bemenete**: Ez határozza meg az adatforrással létesített kapcsolatokat, hogy beolvassa az adatfolyamot
- Peremhálózati hub
- Kafka (a Kafka-bemenetek támogatása jelenleg csak az Azure SQL Edge Intel/AMD64 verziójában érhető el.)

**Stream kimenete**: Ez határozza meg az adatforráshoz való kapcsolódást az adatfolyamnak a szolgáltatásba való írásához. 
- Peremhálózati hub
- SQL (az SQL-kimenet lehet az Azure SQL Edge példányán belüli helyi adatbázis, illetve távoli SQL Server vagy Azure SQL Database.) 
- Azure Blob Storage


## <a name="syntax"></a>Szintaxis

```sql
CREATE EXTERNAL STREAM {external_stream_name}  
(column definition [,column definitions ] * ) --Used for input - optional 
WITH  
(  
  DATA_SOURCE = <data_source_name>, 
  LOCATION = <location_name>, 
  EXTERNAL_FILE_FORMAT = <external_file_format_name>, --Used for input - optional 
   
  INPUT_OPTIONS =  
    ‘CONSUMER_GROUP=<consumer_group_name>; 
    ‘TIME_POLICY=<time_policy>; 
    LATE_EVENT_TOLERANCE=<late_event_tolerance_value>; 
    OUT_OF_ORDER_EVENT_TOLERANCE=<out_of_order_tolerance_value> 
     
    /* Edge options */ 
  , 
  OUTPUT_OPTIONS =  
    ‘REJECT_POLICY=<reject_policy>; 
    MINIMUM_ROWS=<row_value>; 
    MAXIMUM_TIME=<time_value_minutes>; 
    PARTITION_KEY_COLUMN=<partition_key_column_name>; 
    PROPERTY_COLUMNS=(); 
    SYSTEM_PROPERTY_COLUMNS=(); 
    PARTITION_KEY=<partition_key_name>; 
    ROW_KEY=<row_key_name>; 
    BATCH_SIZE=<batch_size_value>; 
    MAXIMUM_BATCH_COUNT=<batch_value>;  
    STAGING_AREA=<blob_data_source>’ 
     
    /* Edge options */ TAGS=<tag_column_value> 
); 
```


## <a name="arguments"></a>Argumentumok


- [KÜLSŐ ADATFORRÁS](/sql/t-sql/statements/create-external-data-source-transact-sql/)
- [KÜLSŐ FÁJLFORMÁTUM](/sql/t-sql/statements/create-external-file-format-transact-sql/)
- **Hely**: az adatforrásban szereplő tényleges adatok vagy helyek neve. Peremhálózati vagy Kafka Stream-objektum esetén a Location (hely) megadja a peremhálózati központ vagy a Kafka-témakör nevét, amelyből olvasni vagy írni kíván.
- **INPUT_OPTIONS**: beállítások megadása kulcs-érték párokként olyan szolgáltatások esetében, mint például az Event és a IOT hubok, amelyek bemeneti adatfolyam-lekérdezések
    - CONSUMER_GROUP: az Event és a IoT hubok az egyik fogyasztói csoporton belül korlátozzák az olvasók számát (5). Ha ezt a mezőt üresen hagyja, a "$Default" fogyasztói csoportot fogja használni.
      - Esemény-és IOT-hubokra vonatkozik
    - TIME_POLICY: azt ismerteti, hogy el kell-e dobni az eseményeket, vagy módosítania kell az esemény időpontját, amikor a késői események, illetve az események nem felelnek meg
      - Esemény-és IOT-hubokra vonatkozik
    - LATE_EVENT_TOLERANCE: a maximális elfogadható késleltetési idő. A késleltetés az esemény időbélyegzője és a rendszeróra közötti különbséget jelöli.
      - Esemény-és IOT-hubokra vonatkozik
    - OUT_OF_ORDER_EVENT_TOLERANCE: az események addig nem érkeznek meg, amíg a bemenetről az adatfolyam-lekérdezésbe elvégezték az utat. Ezek az események elfogadhatók a-ben, vagy dönthet úgy is, hogy egy meghatározott időszakra szünetelteti az átrendezést.
      - Esemény-és IOT-hubokra vonatkozik
- **OUTPUT_OPTIONS**: adja meg a beállításokat kulcs-érték párokként a streaming lekérdezésekhez kimenetként támogatott szolgáltatások esetében 
  - REJECT_POLICY: DROP | Próbálkozzon újra a fajokkal az Adatátalakítási hibák esetén. 
    - Az összes támogatott kimenetre vonatkozik 
  - MINIMUM_ROWS:  
    Egy kimenetre írt kötegekben szükséges minimális sorok száma. A parketta esetében minden köteg létrehoz egy új fájlt. 
    - Az összes támogatott kimenetre vonatkozik 
  - MAXIMUM_TIME:  
    Maximális várakozási idő/köteg. Ezt követően a köteg a kimenetbe kerül, még akkor is, ha a minimális sorokra vonatkozó követelmény nem teljesül. 
    - Az összes támogatott kimenetre vonatkozik 
  - PARTITION_KEY_COLUMN:  
    A partíciós kulcshoz használt oszlop. 
    - Az Event hub és a Service Bus témakörre vonatkozik 
  - PROPERTY_COLUMNS:  
    Az üzenetekhez egyéni tulajdonságokként csatolt kimeneti oszlopok neveinek vesszővel tagolt listája, ha van ilyen.  
    - Az Event hub és a Service Bus témakörre és a várólistára vonatkozik 
  - SYSTEM_PROPERTY_COLUMNS:  
    A Rendszertulajdonságok és a kimeneti oszlopok neve/érték párokból álló, Service Bus üzenetekbe való feltöltésére szolgáló, JSON formátumú gyűjtemény. például {"MessageId": "Column1", "PartitionKey": "Oszlop2"} 
    - A Service Bus témakörre és a várólistára vonatkozik 
  - PARTITION_KEY:  
    A partíciós kulcsot tartalmazó kimeneti oszlop neve. A partíciós kulcs a partíció egyedi azonosítója egy adott táblán belül, amely az entitás elsődleges kulcsának első részét képezi. Ez egy olyan karakterlánc-érték, amely legfeljebb 1 KB méretű lehet. 
    - A Table Storagera és az Azure-függvényre vonatkozik 
  - ROW_KEY:  
    A sor kulcsot tartalmazó kimeneti oszlop neve. A sor kulcsa egy adott partíción belüli entitás egyedi azonosítója. Az entitás elsődleges kulcsának második részét képezi. A sor kulcsa egy olyan karakterlánc-érték, amely legfeljebb 1 KB méretű lehet. 
    - A Table Storagera és az Azure-függvényre vonatkozik 
  - BATCH_SIZE:  
    Ez a tábla tárterületének azon tranzakcióinak számát jelöli, amelyeknél a maximális érték 100 rekord lehet. A Azure Functions esetében ez a függvény hívási alapértelmezett értéke bájtban, a 256 kB értéket jelenti. 
    - A Table Storagera és az Azure-függvényre vonatkozik 
  - MAXIMUM_BATCH_COUNT:  
    Az Azure Function függvénynek másodpercenként eljuttatott események maximális száma – az alapértelmezett érték a 100. SQL Database esetében ez az összes tömeges beszúrási tranzakcióval ellátott rekordok maximális számát jelöli – az alapértelmezett érték a 10 000. 
    - A SQL Databasera és az Azure-függvényre vonatkozik 
  - STAGING_AREA: külső ADATFORRÁS-objektum Blob Storage az átmeneti terület a nagy átviteli sebességű adatok betöltésének SQL Data Warehouse 
    - A SQL Data Warehousera vonatkozik


## <a name="examples"></a>Példák

### <a name="example-1---edgehub"></a>1. példa – EdgeHub

Típus: bemenet vagy kimenet<br>
Paraméterek:
- Bemenet vagy kimenet
  - Alias 
  - Eseményszerializációs formátum 
  - Encoding 
- Csak bemenet: 
  - Esemény tömörítési típusa 

Szintaxis:

```sql
CREATE EXTERNAL DATA SOURCE MyEdgeHub 
WITH  
(      
  LOCATION = 'edgehub://'       
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat  
WITH (  
   FORMAT_TYPE = 'JSON', 
); 
 
CREATE EXTERNAL STREAM Stream_A  
WITH    
(   
   DATA_SOURCE = MyEdgeHub, 
   EXTERNAL_FILE_FORMAT = myFileFormat, 
   LOCATION = ‘<mytopicname>’, 
   OUTPUT_OPTIONS =   
     ‘REJECT_TYPE: Drop’ 
);
```


### <a name="example-2---azure-sql-database-azure-sql-edge-sql-server"></a>2. példa – Azure SQL Database, Azure SQL Edge, SQL Server

Típus: kimenet<br>
Paraméterek:
- Kimeneti alias  
- Adatbázis (SQL Databasehoz szükséges) 
- Kiszolgáló (SQL Databasehoz szükséges) 
- Username (a SQL Databasehoz szükséges) 
- Jelszó (SQL Databasehoz szükséges) 
- Táblázat 
- Egyesítse az összes bemeneti partíciót egyetlen írási vagy öröklési sémával az előző lekérdezési lépés vagy bemenet számára (SQL Database szükséges) 
- Kötegek maximális száma 

Szintaxis:

```sql
CREATE DATABASE SCOPED CREDENTIAL SQLCredName 
WITH IDENTITY = '<user>', 
SECRET = '<password>'; 
 
-- Azure SQL Database 
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl 
WITH 
(     
  LOCATION = ' <my_server_name>.database.windows.net', 
  CREDENTIAL = SQLCredName 
); 
 
--SQL Server or Azure SQL Edge
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl 
WITH 
(     
  LOCATION = ' <sqlserver://<ipaddress>,<port>', 
  CREDENTIAL = SQLCredName 
); 

CREATE EXTERNAL STREAM Stream_A 
WITH   
(  
    DATA_SOURCE = MyTargetSQLTable, 
    LOCATION = '<DatabaseName>.<SchemaName>.<TableName>' 
   --Note: If table is container in the database, <TableName> should be sufficient 
   --Note: Do not need external file format in this case 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    OUTPUT_OPTIONS =  
      ‘REJECT_TYPE: Drop 
); 
```

### <a name="example-3---kafka"></a>3. példa – Kafka

Típus: bemenet<br>
Paraméterek:

- Kafka rendszertöltő kiszolgáló 
- Kafka-témakör neve 
- Forrás partícióinak száma 

Szintaxis:

```sql
CREATE EXTERNAL DATA SOURCE MyKafka_tweets 
WITH 
( 
  --The location maps to KafkaBootstrapServer 
  LOCATION = 'kafka://<kafkaserver>:<ipaddress>', 
  CREDENTIAL = kafkaCredName 
 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = 'UTF-8', 
    DELIMITER = '|' 
); 
 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyKafka_tweets, 
    LOCATION = '<KafkaTopicName>', 
   --JSON: Format, CSV: Delimiter and Encoding, AVRO: None 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    INPUT_OPTIONS =  
      ‘PARTITIONS: 5’ 
); 
```

### <a name="example-4---blob-storage"></a>4. példa – blob Storage

Típus: bemenet vagy kimenet<br>
Paraméterek:
- Bemenet vagy kimenet:
  - Alias 
  - Tárfiók 
  - Storage-fiók kulcsa 
  - Tároló 
  - Elérésiút-minta 
  - Dátumformátum 
  - Időformátum 
  - Eseményszerializációs formátum 
  - Encoding 
- Csak bemenet: 
  - Partíciók (bemenet) 
  - Esemény tömörítési típusa (bemenet) 
- Csak kimenet: 
  - Minimális sorok (kimenet) 
  - Maximális idő (kimenet) 
  - Hitelesítési mód (kimenet) 

Szintaxis:

```sql
CREATE DATABASE SCOPED CREDENTIAL StorageAcctCredName 
WITH IDENTITY = '<my_account>', -- <my_account> can be any string. This string is not used during authentication
SECRET = 'AccountKey'; 
 
CREATE EXTERNAL DATA SOURCE MyBlobStorage_tweets 
WITH 
(     
  LOCATION = 'wasbs://<container_name>@<storage_account_name>.blob.core.windows.net/', 
  CREDENTIAL = StorageAcctCredName 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', --Event serialization format 
    DATE_FORMAT = 'YYYY/MM/DD HH', --Both Date and Time format 
    ENCODING = 'UTF-8'
); 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyBlobStorage_tweets, 
    LOCATION = '<path_pattern>', 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    INPUT_OPTIONS =  
      'PARTITIONS: 1', 
  
    OUTPUT_OPTIONS =  
      'REJECT_TYPE: Drop, 
      PARTITION_KEY_COLUMN: , 
      PROPERTY_COLUMNS: (), 
      MINUMUM_ROWS: 100000, 
      MAXIMUM_TIME: 60'
); 
```

### <a name="example-5---event-hub"></a>5. példa – Event hub

Típus: bemenet vagy kimenet<br>
Paraméterek:
- Bemenet vagy kimenet:
  - Alias 
  - Service Bus névtér 
  - Eseményközpont neve 
  - Eseményközpont szabályzatának neve 
  - Event hub-házirend kulcsa 
  - Eseményszerializációs formátum 
  - Encoding 
- Csak bemenet: 
  - Eseményközpont fogyasztói csoportja 
  - Esemény tömörítési típusa 
- Csak kimenet: 
  - Partíciós kulcs oszlopa 
  - Tulajdonságok oszlopai 

Szintaxis:

```sql
CREATE DATABASE SCOPED CREDENTIAL eventHubCredName 
WITH IDENTITY = 'Shared Access Signature', 
SECRET = '<policyName>'; 
 
CREATE EXTERNAL DATA SOURCE MyEventHub_tweets 
WITH 
(     
  LOCATION = 'sb://my-sb-namespace.servicebus.windows.net', 
  CREDENTIAL = eventHubCredName 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = 'UTF-8, 
    DELIMITER = '|' 
); 
 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyEventHub_tweets, 
    LOCATION = '<topicname>', 
   --JSON: Format, CSV: Delimiter and Encoding, AVRO: None 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
 
    INPUT_OPTIONS =  
      'CONSUMER_GROUP: FirstConsumerGroup', 
          
    OUTPUT_OPTIONS =  
      'REJECT_TYPE: Drop, 
      PARTITION_KEY_COLUMN: , 
      PROPERTY_COLUMNS: ()' 
);
```

### <a name="example-6---iot-hub"></a>6. példa – IOT hub

Típus: bemenet<br>
Paraméterek:

- Bemeneti alias 
- IoT Hub 
- Végpont 
- Megosztott hozzáférési szabályzat neve 
- Megosztott elérési házirend kulcsa 
- Fogyasztói csoport 
- Eseményszerializációs formátum 
- Encoding 
- Esemény tömörítési típusa 

Szintaxis:

```sql
CREATE DATABASE SCOPED CREDENTIAL IoTHubCredName 
WITH IDENTITY = 'Shared Access Signature', 
SECRET = '<policyName>'; 
 
CREATE EXTERNAL DATA SOURCE MyIoTHub_tweets 
WITH 
(     
  LOCATION = ' iot://iot_hub_name.azure-devices.net', 
  CREDENTIAL = IoTHubCredName 
);  

CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', --Event serialization format 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = 'UTF-8'
); 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyIoTHub_tweets, 
    LOCATION = ‘<name>', 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    INPUT_OPTIONS =  
      'ENDPOINT: Messaging, 
      CONSUMER_GROUP: ''FirstConsumerGroup'''
); 
```

### <a name="example-7---azure-synapse-analytics-formerly-sql-data-warehouse"></a>7. példa – Azure szinapszis Analytics (korábban SQL Data Warehouse)

Típus: kimenet<br>
Paraméterek:
- Kimeneti alias 
- Adatbázis 
- Kiszolgáló 
- Felhasználónév 
- Jelszó 
- Táblázat 
- Átmeneti környezet (MÁSOLÁShoz) 

Szintaxis:

```sql
CREATE DATABASE SCOPED CREDENTIAL SQLCredName 
WITH IDENTITY = '<user>', 
SECRET = '<password>'; 
 
CREATE EXTERNAL DATA SOURCE MyTargetSQLTable 
WITH 
(     
  LOCATION = ' <my_server_name>.database.windows.net', 
  CREDENTIAL = SQLCredName 
); 
 
CREATE EXTERNAL STREAM MySQLTableOutput 
WITH ( 
   DATA_SOURCE = MyTargetSQLTable, 
   LOCATION = '<TableName>' 
   --Note: Do not need external file format in this case 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop, 
     STAGING_AREA: staging_area_data_source', 
); 
```


### <a name="example-8---table-storage"></a>8. példa – Table Storage

Típus: kimenet<br>
Paraméterek:
- Kimeneti alias 
- Tárfiók 
- Storage-fiók kulcsa 
- Tábla neve 
- Partíciókulcs 
- Sor kulcsa 
- Köteg mérete 

Szintaxis:

```sql
CREATE DATABASE SCOPED CREDENTIAL TableStorageCredName 
WITH IDENTITY = 'Storage account Key', 
SECRET = '<storage_account_key>'; 
 
CREATE EXTERNAL DATA SOURCE MyTargetTableStorage 
WITH 
(     
  LOCATION = 'abfss://<storage_account>.dfs.core.windows.net', 
  CREDENTIAL = TableStorageCredName 
); 
 
CREATE EXTERNAL STREAM MyTargetTableStorageOutput 
WITH ( 
   DATA_SOURCE = MyTargetTableStorage, 
   LOCATION = '<TableName>', 
   OUTPUT_OPTIONS =  
     'REJECT_TYPE: Drop, 
     PARTITION_KEY: <column_partition_key>, 
     ROW_KEY: <column_row_key>, 
     BATCH_SIZE: 100'
); 
```


### <a name="example-9---service-bus-topic-same-properties-as-queue"></a>9. példa – Service Bus témakör (ugyanazokat a tulajdonságokat, mint a várólista)

Típus: kimenet<br>
Paraméterek:
- Kimeneti alias 
- Service Bus névtér 
- Témakör neve 
- Témakör-házirend neve 
- Témakör-házirend kulcsa 
- Tulajdonságok oszlopai 
- Rendszertulajdonság-oszlopok 
- Eseményszerializációs formátum 
- Encoding 

Szintaxis:

```sql
CREATE DATABASE SCOPED CREDENTIAL serviceBusCredName 
WITH IDENTITY = 'Shared Access Signature', 
SECRET = '<policyName>'; 
 
CREATE EXTERNAL DATA SOURCE MyServiceBus_tweets 
WITH 
(     
  LOCATION = 'sb://my-sb-namespace.servicebus.windows.net', 
  CREDENTIAL = serviceBusCredName 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', --Event serialization format 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = 'UTF-8'
); 
 
CREATE EXTERNAL STREAM MyServiceBusOutput 
WITH ( 
   DATA_SOURCE = MyServiceBus_tweets, 
   LOCATION = '<topic_name>', 
   EXTERNAL_FILE_FORMAT = myFileFormat 
       OUTPUT_OPTIONS =  
     'REJECT_TYPE: Drop, 
     PARTITION_KEY_COLUMN: , 
     PROPERTY_COLUMNS: (), 
     SYSTEM_PROPERTY_COLUMNS: ()'
   --JSON: Format, CSV: Delimiter and Encoding, AVRO: None 
           
); 
```


### <a name="example-10---cosmos-db"></a>10. példa – Cosmos DB

Típus: kimenet<br>
Paraméterek:
- Kimeneti alias 
- Fiókazonosító 
- Fiókkulcs 
- Adatbázis 
- Tárolónév 
- Dokumentum azonosítója 


Szintaxis:

```sql
CREATE DATABASE SCOPED CREDENTIAL cosmosDBCredName 
WITH IDENTITY = 'Storage Account Key', 
SECRET = '<accountKey>'; 
 
CREATE EXTERNAL DATA SOURCE MyCosmosDB_tweets 
WITH 
(     
  LOCATION = 'cosmosdb://accountid.documents.azure.com:443/ database', 
  CREDENTIAL = cosmosDBCredName 
); 
 
CREATE EXTERNAL STREAM MyCosmosDBOutput 
WITH ( 
   DATA_SOURCE = MyCosmosDB_tweets, 
   LOCATION = '<container/documentID>'
   OUTPUT_OPTIONS =  
     'REJECT_TYPE: Drop', 
     --Note: Do not need external file format in this case 
          
);
```



### <a name="example-11---power-bi"></a>11. példa – Power BI

Típus: kimenet<br>
Paraméterek:
- Kimeneti alias 
- Adatkészlet neve 
- Tábla neve 


Szintaxis:

```sql
CREATE DATABASE SCOPED CREDENTIAL PBIDBCredName 
WITH IDENTITY = 'Managed Identity'; 
 
CREATE EXTERNAL DATA SOURCE MyPbi_tweets 
WITH 
( 
  LOCATION = 'pbi://dataset/', 
  CREDENTIAL = PBIDBCredName 
 
); 
 
CREATE EXTERNAL STREAM MyPbiOutput 
WITH ( 
   DATA_SOURCE = MyPbi_tweets, 
   LOCATION = 'tableName', 
   OUTPUT_OPTIONS =  
     'REJECT_TYPE: Drop' 
        
);
```

### <a name="example-12---azure-function"></a>12. példa – Azure-függvény

Típus: kimenet<br>
Paraméterek:
- Függvényalkalmazás 
- Függvény 
- Kulcs 
- Köteg maximális mérete 
- Kötegek maximális száma 

Szintaxis:

```sql
CREATE DATABASE SCOPED CREDENTIAL AzureFunctionCredName 
WITH IDENTITY = 'Function Key', 
SECRET = '<function_key>'; 
 
CREATE EXTERNAL DATA SOURCE MyTargetTableStorage 
WITH 
(     
  LOCATION = 'abfss://<storage_account>.dfs.core.windows.net', 
  CREDENTIAL = TableStorageCredName 
); 
 
CREATE EXTERNAL STREAM MyTargetTableStorageOutput 
WITH ( 
   DATA_SOURCE = MyTargetTableStorage, 
   LOCATION = '<TableName>', 
   OUTPUT_OPTIONS =  
     'REJECT_TYPE: 'Drop'      
     PARTITION_KEY: '<column_partition_key>, 
     ROW_KEY: <column_row_key>, 
     BATCH_SIZE: 100'
); 
```


## <a name="see-also"></a>Lásd még

- [KÜLSŐ STREAM módosítása (Transact-SQL)](alter-external-stream-transact-sql.md) 
- [KÜLSŐ STREAM eldobása (Transact-SQL)](drop-external-stream-transact-sql.md) 


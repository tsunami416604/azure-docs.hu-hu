---
title: Adatok másolása és Oracle az Azure Data Factory használatával |} A Microsoft Docs
description: Ismerje meg, hogyan másolhat adatokat egy Oracle Database-adatbázishoz a támogatott forrás áruházakból származó, vagy támogatott fogadó-áruházak Oracle Data Factory használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: jingwang
ms.openlocfilehash: 04f623a889a87c325b1f53e3b39656ca4b703961
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509228"
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory"></a>Adatok másolása az és Oracle az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki a Data Factory szolgáltatás használ:"]
> * [1-es verzió](v1/data-factory-onprem-oracle-connector.md)
> * [Aktuális verzió](connector-oracle.md)

Ez a cikk ismerteti, hogyan használja a másolási tevékenység az Azure Data Factoryban másolhat adatokat a kezdő és a egy Oracle-adatbázishoz. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek

Oracle-adatbázisból adatokat másolhatja bármely támogatott fogadó adattárba. Is másolhatja adatokat bármely támogatott forrásadattárból Oracle-adatbázishoz. A másolási tevékenység által források vagy fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban az Oracle-összekötő támogatja:

- Oracle-adatbázis következő verziói:
  - Oracle 12c R1 (12.1)
  - Oracle 11g R1 vagy R2 (11.1, 11.2)
  - Oracle 10g R1 vagy R2 (10.1, 10,2)
  - Oracle 9i R1 vagy R2 (9.0.1, 9.2)
  - Oracle-8i R3 (8.1.7-es)
- Másolja az adatokat az **alapszintű** vagy **OID** üzenetküldéshez használ.
- Párhuzamos másolási Oracle forrásból. Lásd: [Oracle másolási párhuzamos](#parallel-copy-from-oracle) részletei szakaszban.

> [!Note]
> Oracle-proxy kiszolgáló nem támogatott.

## <a name="prerequisites"></a>Előfeltételek

Adatok másolása a kezdő és a egy Oracle-adatbázis, amely nem a nyilvánosan elérhető-e, meg kell egy helyi Integration Runtime beállítása. Az integration runtime kapcsolatos további információkért lásd: [helyi Integration Runtime](create-self-hosted-integration-runtime.md). Az integrációs modul egy beépített Oracle-illesztőprogram biztosít. Így nem kell manuálisan a Oracle és az adatok másolása az illesztő telepítése.

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok segítségével határozhatók meg a Data Factory-entitások adott és az Oracle-összekötő-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Az Oracle-beli társított szolgáltatás a következő tulajdonságok támogatottak.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot állítsa **Oracle**. | Igen |
| connectionString | Itt adható meg az Oracle Database-példányhoz való kapcsolódáshoz szükséges információkat. <br/>Ez a mező jelölhetnek egy SecureString tárolja biztonságos helyen a Data Factoryban. Jelszó az Azure Key Vault és lekéréses is helyezheti a `password` konfigurációs ki a kapcsolati karakterláncot. Tekintse meg a következő minták és [Store hitelesítő adatokat az Azure Key Vaultban](store-credentials-in-key-vault.md) további részleteket a cikkben. <br><br>**Kapcsolat típusa támogatott**: Használhat **Oracle biztonsági azonosító** vagy **Oracle-szolgáltatás neve** az adatbázis azonosításához:<br>-Ha biztonsági azonosító: `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>-Ha a szolgáltatás a nevet használja: `Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;` | Igen |
| connectVia | A [integrációs modul](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. (Ha az adattár nyilvánosan hozzáférhető) használhatja a helyi Integration Runtime vagy az Azure integrációs modul. Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

>[!TIP]
>Ha eléri hiba üzenettel "ORA-01025: UPI paraméter engedélyezett tartományon kívül esik"és az Oracle-verzió 8i, adjon hozzá `WireProtocolMode=1` a kapcsolati karakterláncot, és próbálkozzon újra.

**Oracle kapcsolati titkosításának**, két lehetősége van:

1.  Használandó **háromszoros DES-titkosítás (3DES) és Advanced Encryption Standard (AES)** , Oracle-kiszolgáló oldalán, nyissa meg az Oracle speciális biztonsági (OAS), és a titkosítási beállításainak konfigurálása, tekintse meg a részleteket [Itt](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759). Az ADF Oracle-összekötő automatikusan egyezteti OAS Oracle-kapcsolat létesítéséhez konfigurálja egy használandó titkosítási módszer.

2.  Használandó **SSL**, kövesse az alábbi lépéseket:

    1.  SSL-tanúsítvány adatainak beolvasása. A DER kódolású tanúsítvány adatainak az SSL-tanúsítványt, és mentse a kimeneti (---tanúsítvány megkezdése Tanúsítvány---End) szöveges fájlból.

        ```
        openssl x509 -inform DER -in [Full Path to the DER Certificate including the name of the DER Certificate] -text
        ```

        **Példa:** kinyerheti az információkat cert DERcert.cer; ezt követően mentse a kimeneti cert.txt

        ```
        openssl x509 -inform DER -in DERcert.cer -text
        Output:
        -----BEGIN CERTIFICATE-----
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXX
        -----END CERTIFICATE-----
        ```
    
    2.  A keystore vagy truststore hozhat létre. A következő parancsot a truststore fájlt hoz létre, vagy a PKCS-12 formátumú jelszó nélkül.

        ```
        openssl pkcs12 -in [Path to the file created in the previous step] -out [Path and name of TrustStore] -passout pass:[Keystore PWD] -nokeys -export
        ```

        **Példa:** MyTrustStoreFile nevű jelszóval PKCS12 truststore fájl létrehozása

        ```
        openssl pkcs12 -in cert.txt -out MyTrustStoreFile -passout pass:ThePWD -nokeys -export  
        ```

    3.  A helyi IR a gép, például a C:\MyTrustStoreFile truststore fájlt a hely.
    4.  Az ADF, az Oracle kapcsolati karakterlánc konfigurálása `EncryptionMethod=1` és a megfelelő `TrustStore` / `TrustStorePassword`értéket, pl. `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;EncryptionMethod=1;TrustStore=C:\\MyTrustStoreFile;TrustStorePassword=<trust_store_password>`.

**Példa**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Példa: a jelszó tárolásához az Azure Key Vaultban**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;"
            },
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```
## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a szakasz az Oracle-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Adatok másolása az és az Oracle, állítsa be a type tulajdonság, az adatkészlet **OracleTable**. A következő tulajdonságok támogatottak.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adatkészlet értékre kell állítani **OracleTable**. | Igen |
| tableName |Az Oracle-adatbázis, amelyre a társított szolgáltatás hivatkozik a tábla neve. | Igen |

**Példa**

```json
{
    "name": "OracleDataset",
    "properties":
    {
        "type": "OracleTable",
        "linkedServiceName": {
            "referenceName": "<Oracle linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz az Oracle-forrás és fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="oracle-as-a-source-type"></a>Oracle-forrás típusa

> [!TIP]
>
> További információkat talál a [Oracle másolási párhuzamos](#parallel-copy-from-oracle) hogyan lehet adatokat betölteni az Oracle hatékonyan navigálhat az adatparticionálás szakaszán.

Adatok másolása az Oracle, állítsa be a forrás típusaként a másolási tevékenység **OracleSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrása típusa tulajdonságát állítsa **OracleSource**. | Igen |
| oracleReaderQuery | Az egyéni SQL-lekérdezés segítségével olvassa el az adatokat. Például: `"SELECT * FROM MyTable"`.<br>Ha engedélyezi a particionált terhelés, kell megfelelő beépített partíció történt a lekérdezés környezet igénybe vételét. Példák a [Oracle másolási párhuzamos](#parallel-copy-from-oracle) szakaszban. | Nem |
| partitionOptions | Az adatparticionálási adatokat betölteni az Oracle használt beállításokat megadja. <br>Lehetővé teszi az értékek a következők: **Nincs** (alapértelmezett), **PhysicalPartitionsOfTable** és **DynamicRange**.<br>Ha engedélyezve van a partíció lehetőséget (nem "nincs"), állítsa is be **[ `parallelCopies` ](copy-activity-performance.md#parallel-copy)** beállítása 4, például a másolási tevékenység, amely meghatározza, hogy a párhuzamos párhuzamossági egy időben betölteni az adatokat az Oracle az adatbázis. | Nem |
| partitionSettings | Adja meg a csoport, az adatparticionálás beállításait. <br>Alkalmazza, ha partíció lehetőséget nem `None`. | Nem |
| partitionNames | A lista a fizikai partíciók, amely kell másolni. <br>Alkalmazza, ha partíció lehetőséget `PhysicalPartitionsOfTable`. Ha a lekérdezések használatával lekérheti a forrásadatokat, a környezet igénybe vételét `?AdfTabularPartitionName` WHERE záradékban. A példa [Oracle másolási párhuzamos](#parallel-copy-from-oracle) szakaszban. | Nem |
| partitionColumnName | Adja meg a forrásoszlop neve **az egész típusú** particionálásával tartomány párhuzamos másolásához használni kívánt. Ha nincs megadva, a tábla elsődleges kulcsa nem észlelt, és szerepel partícióoszlop automatikus. <br>Alkalmazza, ha partíció lehetőséget `DynamicRange`. Ha a lekérdezések használatával lekérheti a forrásadatokat, a környezet igénybe vételét `?AdfRangePartitionColumnName` WHERE záradékban. A példa [Oracle másolási párhuzamos](#parallel-copy-from-oracle) szakaszban. | Nem |
| partitionUpperBound | Az adatokat másolja a partíciós oszlopának legnagyobb értékét. <br>Alkalmazza, ha partíció lehetőséget `DynamicRange`. Ha a lekérdezések használatával lekérheti a forrásadatokat, a környezet igénybe vételét `?AdfRangePartitionUpbound` WHERE záradékban. A példa [Oracle másolási párhuzamos](#parallel-copy-from-oracle) szakaszban. | Nem |
| partitionLowerBound | Minimális érték a partícióoszlop adatmásolás céljából. <br>Alkalmazza, ha partíció lehetőséget `DynamicRange`. Ha a lekérdezések használatával lekérheti a forrásadatokat, a környezet igénybe vételét `?AdfRangePartitionLowbound` WHERE záradékban. A példa [Oracle másolási párhuzamos](#parallel-copy-from-oracle) szakaszban. | Nem |

**. Példa: alapszintű lekérdezési partíció nélkül használatával adatok másolása**

```json
"activities":[
    {
        "name": "CopyFromOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Oracle input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "OracleSource",
                "oracleReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

További példák a [Oracle másolási párhuzamos](#parallel-copy-from-oracle) szakaszban.

### <a name="oracle-as-a-sink-type"></a>Oracle, a fogadó típusa

Adatok másolása az Oracle, állítsa a fogadó típusa a másolási tevékenység **Oraclesinkben**. A következő tulajdonságok támogatottak a másolási tevékenység **fogadó** szakaszban.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadó típusa tulajdonságát állítsa **Oraclesinkben**. | Igen |
| writeBatchSize | Amikor a puffer mérete eléri a writeBatchSize adatok beszúrása SQL-táblát.<br/>Megengedett értékek: egész szám (sorok száma). |Nem (az alapértelmezett érték 10 000) |
| writeBatchTimeout | Várjon, amíg a kötegelt insert művelet befejezését, mielőtt azt az időkorlátot.<br/>Megengedett értékek: időtartam. Ilyen például, 00:30:00 (30 perc). | Nem |
| preCopyScript | Adjon meg egy SQL-lekérdezést az adatok írása az Oracle-be való minden egyes futtatás előtt hajtsa végre a másolási tevékenység. Ez a tulajdonság segítségével törölje az előre betöltött adatokat. | Nem |

**Példa**

```json
"activities":[
    {
        "name": "CopyToOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Oracle output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OracleSink"
            }
        }
    }
]
```

## <a name="parallel-copy-from-oracle"></a>Az Oracle párhuzamos másolása

Data factory Oracle-összekötő használatával adatait átmásolhatja az Oracle, nagy teljesítményű párhuzamos particionálás beépített adatokat biztosít. A másolási tevékenység adatok particionálási beállításai -> Oracle-forrás található:

![Partíciós beállítások](./media/connector-oracle/connector-oracle-partition-options.png)

Ha engedélyezi a particionált másolási, adat-előállító párhuzamos lekérdezések fut az Oracle forrás partíció betölteni az adatokat. A párhuzamos párhuzamossági van beállítva, és keresztül **[ `parallelCopies` ](copy-activity-performance.md#parallel-copy)** másolási tevékenység beállítást. Például, ha a beállított `parallelCopies` négy, egyidejűleg állít elő, a data factory és a megadott partíció lehetőséget és a beállításokat, az Oracle-adatbázis adatait minden lekérése során részének alapján négy lekérdezések futtatása.

Használata javasolt, párhuzamos másolás engedélyezéséhez az adatparticionálás, különösen akkor, ha nagy mennyiségű adat tölthető be, Oracle-adatbázisból. A különböző helyzetekhez javasolt konfigurációk a következők:

| Forgatókönyv                                                     | Ajánlott beállítások                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| A teljes terhelés fizikai partícióval rendelkező nagy táblából          | **Beállítás particionálása**: Fizikai partíciók tábla. <br><br/>A futtatás során a data Factory automatikusan észleli a fizikai partíciók, és adatok másolása-partíciókat. |
| A teljes terhelés nagy táblából, az adatparticionálás egészszám-oszloppal rendelkező fizikai partíciók nélkül | **Beállítások particionálása**: Dinamikus tartományának partíció.<br>**Partícióoszlop**: Adja meg az adatok particionálása használt oszlop. Ha nem a megadott, elsődleges kulcsának oszlopa szolgál. |
| Nagy mennyiségű adat használatával Ez alatt a egyéni lekérdezés fizikai partícióval rendelkező betöltése | **Beállítás particionálása**: Fizikai partíciók tábla.<br>**Lekérdezés**: `SELECT * FROM <TABLENAME> PARTITION("?AdfTabularPartitionName") WHERE <your_additional_where_clause>`.<br>**Partíció neve**: Adja meg az adatokat másolni a partíció neve. Ha nincs megadva, az ADF automatikusan észleli a fizikai partíciók az Oracle-adatkészletben megadott táblába.<br><br>Data factory cserélje le a futtatás során `?AdfTabularPartitionName` a partíciók tényleges nevét és a küldési és Oracle-ig. |
| Nagy mennyiségű adatot, egyéni lekérdezés használata, ez alatt egészszám-oszloppal közben fizikai partíciók nélkül az adatok particionálása betöltése | **Beállítások particionálása**: Dinamikus tartományának partíció.<br>**Lekérdezés**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Partícióoszlop**: Adja meg az adatok particionálása használt oszlop. Egész szám adattípusú particionáló oszlop ellen.<br>**Partíció felső határérték** és **partíció alsó határérték**: Adja meg, hogy szeretné-e be csak alsó és felső tartományba eső adatokat partícióoszlop szűrést.<br><br>Data factory cserélje le a futtatás során `?AdfRangePartitionColumnName`, `?AdfRangePartitionUpbound`, és `?AdfRangePartitionLowbound` a tényleges oszlop nevét és értékét az egyes tartományok particionálja, és Oracle küldje. <br>Például, ha a partíció oszlop "ID" csoportot az alsó határ értékének 1 és 80-as, a párhuzamos készletet, 4, mint a felső határérték ADF adatbeolvasás 4 partíció azonosítójú között [1,20], [21, 40], [41, 60] és [61, 80]. |

**Példa: lekérdezés fizikai partíciónként**

```json
"source": {
    "type": "OracleSource",
    "query": "SELECT * FROM <TABLENAME> PARTITION(\"?AdfTabularPartitionName\") WHERE <your_additional_where_clause>",
    "partitionOption": "PhysicalPartitionsOfTable",
    "partitionSettings": {
        "partitionNames": [
            "<partitionA_name>",
            "<partitionB_name>"
        ]
    }
}
```

**Példa: lekérdezés dinamikus tartományának partíció**

```json
"source": {
    "type": "OracleSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="data-type-mapping-for-oracle"></a>Adattípus-leképezés Oracle

Oracle és a másolt adatok, Data Factory-közbenső adattípusok a következő hozzárendeléseket használtak Oracle adattípusokat. Hogyan a másolási tevékenység leképezi a forrás séma és adatok típusa a fogadó kapcsolatos további információkért lásd: [séma és adatok írja be a hozzárendelések](copy-activity-schema-and-type-mapping.md).

| Oracle-adattípus | Data Factory közbenső adattípus |
|:--- |:--- |
| BFILE |Byte[] |
| BLOB |Byte[]<br/>(csak támogatott Oracle 10g vagy újabb) |
| CHAR |String |
| CLOB |String |
| DATE |DateTime |
| FLOAT |Decimal, String (Ha a pontosság > 28) |
| INTEGER |Decimal, String (Ha a pontosság > 28) |
| LONG |String |
| LONG RAW |Byte[] |
| NCHAR |String |
| NCLOB |String |
| NUMBER |Decimal, String (Ha a pontosság > 28) |
| NVARCHAR2 |String |
| RAW |Byte[] |
| ROWID |String |
| TIMESTAMP |DateTime |
| TIMESTAMP WITH LOCAL TIME ZONE |String |
| TIMESTAMP WITH TIME ZONE |String |
| UNSIGNED INTEGER |Number |
| VARCHAR2 |String |
| XML |String |

> [!NOTE]
> Az adattípusok IDŐKÖZ év TO hónap és nap TO IDŐKÖZ második nem támogatottak.


## <a name="next-steps"></a>További lépések
A másolási tevékenység, Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md##supported-data-stores-and-formats).

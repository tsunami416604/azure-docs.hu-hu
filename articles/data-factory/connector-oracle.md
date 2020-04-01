---
title: Adatok másolása az Oracle-be és onnan az Azure Data Factory használatával
description: Ismerje meg, hogyan másolhat adatokat a támogatott forrástárolókból egy Oracle adatbázisba, vagy az Oracle-től a támogatott fogadótárolókba a Data Factory használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: jingwang
ms.openlocfilehash: 68e234b9db269c30dc9f24106ae1942c01304da7
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422504"
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory"></a>Adatok másolása az Oracle-től és az Oracle-nek az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-onprem-oracle-connector.md)
> * [Aktuális verzió](connector-oracle.md)

Ez a cikk bemutatja, hogyan használhatja a másolási tevékenységet az Azure Data Factory-ban adatok másolásához és oracle-adatbázisba. A [másolási tevékenység áttekintésére](copy-activity-overview.md)épül.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az Oracle-összekötő a következő tevékenységek esetén támogatott:

- [Tevékenység másolása](copy-activity-overview.md) [támogatott forrás/fogadó mátrixcal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

Az Oracle-adatbázisból adatokat másolhat bármely támogatott fogadóadattárba. Bármely támogatott forrásadattárból adatokat másolhat egy Oracle-adatbázisba. A másolási tevékenység által forrásként vagy fogadóként támogatott adattárak listáját a [Támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblában láthatja.

Pontosabban, ez az Oracle csatlakozó támogatja:

- Az Oracle adatbázis következő verziói:
    - Oracle 18c R1 (18.1) és magasabb
    - Oracle 12c R1 (12.1) és magasabb
    - Oracle 11g R1 (11.1) és magasabb
    - Oracle 10g R1 (10.1) és magasabb
    - Oracle 9i R2 (9.2) és magasabb
    - Oracle 8i R3 (8.1.7) és magasabb
    - Oracle Database Cloud Exadata szolgáltatás
- Párhuzamos másolás Oracle forrásból. A [részleteket](#parallel-copy-from-oracle) lásd az Oracle párhuzamos másolata című részében.

> [!Note]
> Az Oracle proxykiszolgáló nem támogatott.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)] 

Az integrációs futásidejű beépített Oracle-illesztőprogramot biztosít. Ezért nem kell manuálisan telepítenie egy illesztőprogramot, amikor adatokat másol az Oracle-ből és az Oracle-be.

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok az Oracle-összekötőre jellemző Data Factory-entitások definiálásához használt tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

Az Oracle hivatkozott szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A típustulajdonságot **Oracle**tulajdonságra kell állítani. | Igen |
| connectionString (kapcsolati karakterlánc) | Az Oracle Database-példányhoz való csatlakozáshoz szükséges adatokat adja meg. <br/>Az Azure Key Vaultban is elhelyezhet `password` egy jelszót, és kihúzhatja a konfigurációt a kapcsolati karakterláncból. További részleteket az alábbi mintákés Áruházhitelesítő adatok az [Azure Key Vaultban](store-credentials-in-key-vault.md) tekintse meg. <br><br>**Támogatott kapcsolattípus**: **Az Adatbázis** azonosítására Oracle SID vagy **Oracle service Name** használatával azonosíthatja az adatbázist:<br>- Sid használata esetén:`Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>- Ha a szolgáltatás nevét használja:`Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;`<br>Az Oracle natív kapcsolatbeállításaihoz hozzáadhat egy bejegyzést a [TNSNAMES-ben. OrA](http://www.orafaq.com/wiki/Tnsnames.ora) fájl az Oracle szerveren, és az ADF Oracle csatolt szolgáltatás, úgy dönt, hogy használja az Oracle Service Name kapcsolat típusát, és konfigurálja a megfelelő szolgáltatás nevét. | Igen |
| connectVia | Az adattárhoz való csatlakozáshoz használandó [integrációs futásidő.](concepts-integration-runtime.md) További információ az [Előfeltételek](#prerequisites) szakaszból. Ha nincs megadva, a rendszer az alapértelmezett Azure-integrációs futásidőt használja. |Nem |

>[!TIP]
>Ha hibaüzenetet kap, "ORA-01025: UPI paraméter hatótávolságon kívül", és `WireProtocolMode=1` az Oracle verziója 8i, adja hozzá a kapcsolati karakterlánchoz. Akkor próbálja újra.

További kapcsolati tulajdonságok, amelyeket a kapcsolati karakterláncban állíthat be esetenként:

| Tulajdonság | Leírás | Megengedett értékek |
|:--- |:--- |:--- |
| Tömbméret |Az összekötő által egyetlen hálózati oda-vissza utazás során lehívható bájtok száma. Pl. `ArraySize=‭10485760‬`.<br/><br/>A nagyobb értékek növelik az átviteli forgalmat azáltal, hogy csökkentik az adatok hálózaton keresztüli letöltésének számát. A kisebb értékek növelik a válaszidőt, mivel kevesebb késés vár a kiszolgáló raaz adatok továbbítására. | Egész szám 1 és 4294967296 (4 GB) között. Az alapértelmezett `60000`érték a . Az 1 érték nem határozza meg a bájtok számát, de pontosan egy adatsor helyének lefoglalását jelzi. |

Az Oracle-kapcsolat titkosításának engedélyezéséhez két lehetőség közül választhat:

-   A **Triple-DES Encryption (3DES) és az Advanced Encryption Standard (AES)** használatához az Oracle kiszolgálóoldalán nyissa meg az Oracle Advanced Security (OAS) webhelyet, és konfigurálja a titkosítási beállításokat. További részletekért tekintse meg ezt az [Oracle dokumentációt.](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759) Az Oracle Application Development Framework (ADF) összekötő automatikusan egyezteti a titkosítási módszert, hogy az Oracle-rel való kapcsolat létrehozásakor az OAS-ban konfigurált titkosítási módszert használja.

-   Az **SSL**használata:

    1.  Az SSL-tanúsítvány adatainak beszerezése. Az SSL-tanúsítvány megkülönböztető kódolási szabályainak (DER) kódolású tanúsítványadatainak beszereznie, és menteni a kimenetet (----- A tanúsítvány kezdete ... Tanúsítvány befejezése -----) szövegfájlként.

        ```
        openssl x509 -inform DER -in [Full Path to the DER Certificate including the name of the DER Certificate] -text
        ```

        **Példa:** Kivonat cert info DERcert.cer, majd mentse a kimenetet cert.txt.

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
    
    2.  A `keystore` vagy `truststore`a . A következő parancs `truststore` létrehozza a fájlt jelszóval vagy anélkül, PKCS-12 formátumban.

        ```
        openssl pkcs12 -in [Path to the file created in the previous step] -out [Path and name of TrustStore] -passout pass:[Keystore PWD] -nokeys -export
        ```

        **Példa:** Hozzon létre egy `truststore` MyTrustStoreFile nevű PKCS12-fájlt jelszóval.

        ```
        openssl pkcs12 -in cert.txt -out MyTrustStoreFile -passout pass:ThePWD -nokeys -export  
        ```

    3.  Helyezze `truststore` a fájlt a saját üzemeltetésű infravörös gépre. Helyezze például a fájlt a C:\MyTrustStoreFile mappába.
    4.  Az Azure Data Factory konfigurálja `EncryptionMethod=1` az Oracle `TrustStore` / `TrustStorePassword`kapcsolati karakterláncot és a megfelelő értéket. Például: `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;EncryptionMethod=1;TrustStore=C:\\MyTrustStoreFile;TrustStorePassword=<trust_store_password>`.

**Példa:**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Példa: jelszó tárolása az Azure Key Vaultban**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;",
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

Ez a szakasz az Oracle adatkészlet által támogatott tulajdonságok listáját tartalmazza. Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az Adatkészletek című [témakörben található.](concepts-datasets-linked-services.md) 

Ha adatokat szeretne másolni az Oracle programból `OracleTable`és az Oracle programba, állítsa az adatkészlet típustulajdonságát a beállításra. A következő tulajdonságok támogatottak.

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet típustulajdonságát a ikonra `OracleTable`kell állítani. | Igen |
| Séma | A séma neve. |Nem a forráshoz, Igen a mosogatóhoz  |
| tábla | A tábla/nézet neve. |Nem a forráshoz, Igen a mosogatóhoz  |
| tableName | A sémával rendelkező tábla/nézet neve. Ez a tulajdonság a visszamenőleges kompatibilitás érdekében támogatott. Az új számítási `schema` `table`feladatokhoz használja és használja. | Nem a forráshoz, Igen a mosogatóhoz |

**Példa:**

```json
{
    "name": "OracleDataset",
    "properties":
    {
        "type": "OracleTable",
        "schema": [],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        },
        "linkedServiceName": {
            "referenceName": "<Oracle linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Ez a szakasz az Oracle forrás és fogadó által támogatott tulajdonságok listáját tartalmazza. A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok című témakörben található.](concepts-pipelines-activities.md) 

### <a name="oracle-as-source"></a>Oracle mint forrás

>[!TIP]
>Ha az adatokat hatékonyan szeretné betölteni az Oracle-től az adatparticionálás használatával, tudjon meg többet az [Oracle párhuzamos másolásából.](#parallel-copy-from-oracle)

Ha adatokat szeretne másolni az Oracle programból, állítsa a másolási tevékenység forrástípusát a-ra. `OracleSource` A következő tulajdonságokat a másolási tevékenység **forrása** szakasz támogatja.

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának típustulajdonságát a ikonra `OracleSource`kell állítani. | Igen |
| oracleReaderQuery | Az adatok olvasásához használja az egyéni SQL-lekérdezést. Például: `"SELECT * FROM MyTable"`.<br>Ha engedélyezi a particionált terhelést, a lekérdezésben a megfelelő beépített partícióparamétereket kell csatlakoztatnia. Példák: Párhuzamos másolat az [Oracle-ből](#parallel-copy-from-oracle) szakaszban. | Nem |
| partitionOptions | Megadja az Oracle-től az adatok betöltéséhez használt adatparticionálási beállításokat. <br>Az engedélyezett értékek a következők: **Nincs** (alapértelmezett), **PhysicalPartitionsOfTable** ,és **DynamicRange**.<br>Ha egy partícióbeállítás engedélyezve van `None`(azaz nem), az Oracle-adatbázisból származó adatok egyidejű [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) betöltéséhez szükséges párhuzamosság mértékét a másolási tevékenység beállítása szabályozza. | Nem |
| partitionSettings (partícióbeállításai) | Adja meg az adatparticionálás beállításainak csoportját. <br>Alkalmazza, ha a partíciós beállítás nem `None`. | Nem |
| partícióNames | A másolandó fizikai partíciók listája. <br>Alkalmazza, ha a `PhysicalPartitionsOfTable`partíciós beállítás . Ha lekérdezéssel olvassa be a forrásadatokat, a HOOK a `?AdfTabularPartitionName` WHERE záradékban. Például tekintse meg a Párhuzamos másolat az [Oracle szakaszt.](#parallel-copy-from-oracle) | Nem |
| partitionColumnName | Adja meg a forrásoszlop nevét **egész típusban,** amelyet a tartománypartidatás a párhuzamos másoláshoz használ. Ha nincs megadva, a tábla elsődleges kulcsa automatikusan észlelhető, és partícióoszlopként használatos. <br>Alkalmazza, ha a `DynamicRange`partíciós beállítás . Ha lekérdezéssel olvassa be a forrásadatokat, a HOOK a `?AdfRangePartitionColumnName` WHERE záradékban. Például tekintse meg a Párhuzamos másolat az [Oracle szakaszt.](#parallel-copy-from-oracle) | Nem |
| partitionUpperBound között | Az adatok másolásához a partícióoszlop maximális értéke. <br>Alkalmazza, ha a `DynamicRange`partíciós beállítás . Ha lekérdezéssel olvassa be a forrásadatokat, a HOOK a `?AdfRangePartitionUpbound` WHERE záradékban. Például tekintse meg a Párhuzamos másolat az [Oracle szakaszt.](#parallel-copy-from-oracle) | Nem |
| partitionLowerBound | Az adatok másolásához szükséges partícióoszlop minimális értéke. <br>Alkalmazza, ha a `DynamicRange`partíciós beállítás . Ha lekérdezéssel olvassa be a forrásadatokat, a HOOK a `?AdfRangePartitionLowbound` WHERE záradékban. Például tekintse meg a Párhuzamos másolat az [Oracle szakaszt.](#parallel-copy-from-oracle) | Nem |

**Példa: adatok másolása egyszerű lekérdezéssel partíció nélkül**

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

### <a name="oracle-as-sink"></a>Oracle mint mosogató

Ha adatokat szeretne másolni az Oracle programba, állítsa a fogadó típusát a másolási tevékenységben a-ra. `OracleSink` A következő tulajdonságokat a másolási tevékenység **fogadó** szakasza támogatja.

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadójának típustulajdonságát a ikonra `OracleSink`kell állítani. | Igen |
| writeBatchSize | Adatokat szúr be az SQL táblába, amikor a puffermérete eléri a mezőt. `writeBatchSize`<br/>Az engedélyezett értékek egész szám (sorok száma). |Nem (az alapértelmezett érték 10 000) |
| writeBatchTimeout | A kötegbehelyezési művelet befejezéséhez szükséges várakozási idő, mielőtt az időtúljárna.<br/>Az engedélyezett értékek timespan. Ilyen például a 00:30:00 (30 perc). | Nem |
| preCopyScript | Adja meg a másolási tevékenység futtatásához megadott SQL-lekérdezést, mielőtt minden futtatáskor adatokat írna az Oracle-be. Ezzel a tulajdonsággal megtisztíthatja az előre betöltött adatokat. | Nem |

**Példa:**

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

## <a name="parallel-copy-from-oracle"></a>Párhuzamos másolat az Oracle-től

A Data Factory Oracle összekötő beépített adatparticionálást biztosít az Oracle adatainak párhuzamos másolásához. Az adatparticionálási beállításokat a másolási tevékenység **Forrás** lapján találja.

![A partícióbeállításainak képernyőképe](./media/connector-oracle/connector-oracle-partition-options.png)

Ha engedélyezi a particionált másolást, a Data Factory párhuzamos lekérdezéseket futtat az Oracle-forráson az adatok partíciók általi betöltéséhez. A párhuzamos mértéket a [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) másolási tevékenység beállítása szabályozza. Ha például négyre állítja a beállítást, `parallelCopies` a Data Factory egyidejűleg négy lekérdezést hoz létre és futtat a megadott partícióbeállítás és -beállítások alapján, és minden lekérdezés lekéri az adatok egy részét az Oracle-adatbázisból.

Javasoljuk, hogy engedélyezze a párhuzamos másolást az adatparticionálással, különösen akkor, ha nagy mennyiségű adatot tölt be az Oracle adatbázisból. Az alábbiakban a különböző forgatókönyvekhez javasolt konfigurációkat javasoljuk. Amikor adatokat másol fájlalapú adattárba, a rendszer azt a parancsot adja meg, hogy egy mappába több fájlként írjon (csak adja meg a mappa nevét), ebben az esetben a teljesítmény jobb, mint egyetlen fájlba írni.

| Forgatókönyv                                                     | Javasolt beállítások                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Teljes terhelés nagy táblából, fizikai partíciókkal.          | **Partícióopció**: A tábla fizikai partíciói. <br><br/>A végrehajtás során a Data Factory automatikusan észleli a fizikai partíciókat, és partíciók szerint másolja az adatokat. |
| Teljes terhelés nagy táblából, fizikai partíciók nélkül, míg egy egész oszlop adatparticionálás. | **Partíció beállításai**: Dinamikus tartományú partíció.<br>**Partícióoszlop:** Adja meg az adatok particionálásához használt oszlopot. Ha nincs megadva, a program az elsődleges kulcs oszlopot használja. |
| Nagy mennyiségű adatot tölthet be egyéni lekérdezéssel, fizikai partíciókkal. | **Partícióopció**: A tábla fizikai partíciói.<br>**Lekérdezés** `SELECT * FROM <TABLENAME> PARTITION("?AdfTabularPartitionName") WHERE <your_additional_where_clause>`: .<br>**Partíció neve**: Adja meg a partíció nevét(oka)t, amelyből adatokat szeretne másolni. Ha nincs megadva, a Data Factory automatikusan észleli az Oracle adatkészletben megadott táblában megadott fizikai partíciókat.<br><br>A végrehajtás során a `?AdfTabularPartitionName` Data Factory lecseréli a partíció tényleges nevét, és elküldi az Oracle-nek. |
| Nagy mennyiségű adatot tölthet be egyéni lekérdezéssel, fizikai partíciók nélkül, míg egy egész oszlopot az adatok particionálásához. | **Partíció beállításai**: Dinamikus tartományú partíció.<br>**Lekérdezés** `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`: .<br>**Partícióoszlop:** Adja meg az adatok particionálásához használt oszlopot. Az egész adattípussal rendelkező oszlopra particionálhat.<br>**Partíció felső és** **alsó határa**: Adja meg, hogy a partícióoszlophoz képest csak az alsó és a felső tartomány közötti adatokat szeretné-e beolvasni.<br><br>A végrehajtás során a `?AdfRangePartitionColumnName` `?AdfRangePartitionUpbound`Data `?AdfRangePartitionLowbound` Factory lecseréli a , és az egyes partíciók tényleges oszlopnevét és értéktartományait, és elküldi az Oracle-nek. <br>Ha például az "ID" partícióoszlop az alsó határ 1, a felső határ pedig 80, a párhuzamos másolat pedig 4, a Data Factory 4 partícióval olvassa be az adatokat. Azonosítóik [1,20], [21, 40], [41, 60] és [61, 80] között vannak. |

**Példa: lekérdezés fizikai partícióval**

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

**Példa: lekérdezés dinamikus tartománypartícióval**

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

## <a name="data-type-mapping-for-oracle"></a>Adattípus-leképezés az Oracle számára

Amikor adatokat másol az Oracle-ből és az Oracle-be, a következő leképezések lesznek érvényesek. Ha többet szeretne tudni arról, hogy a másolási tevékenység hogyan rendeli le a forrássémát és az adattípust a fogadóhoz, olvassa el a [Séma- és adattípus-hozzárendelések című témakört.](copy-activity-schema-and-type-mapping.md)

| Oracle adattípus | Adatgyár köztes adattípusa |
|:--- |:--- |
| BFILE |Bájt[] |
| Blob |Bájt[]<br/>(csak Oracle 10g vagy újabb verziókesetén támogatott) |
| Char |Sztring |
| CLOB |Sztring |
| DATE |DateTime |
| Úszó |Decimális, Karakterlánc (ha a pontosság 28 >) |
| EGÉSZ SZÁM |Decimális, Karakterlánc (ha a pontosság 28 >) |
| Hosszú |Sztring |
| HOSSZÚ NYERS |Bájt[] |
| Nchar |Sztring |
| NCLOB között |Sztring |
| Szám |Decimális, Karakterlánc (ha a pontosság 28 >) |
| NVARCHAR2 között |Sztring |
| Nyers |Bájt[] |
| SORAZONOSÍTÓ |Sztring |
| Időbélyeg |DateTime |
| IDŐBÉLYEG HELYI IDŐZÓNÁVAL |Sztring |
| IDŐBÉLYEG IDŐZÓNÁVAL |Sztring |
| Aláíratlan egész szám |Szám |
| VARCHAR2 között |Sztring |
| XML |Sztring |

> [!NOTE]
> Az intervallum-hónap és a napközis második adattípusok nem támogatottak.

## <a name="lookup-activity-properties"></a>A keresgaszíntevékenység tulajdonságai

A tulajdonságokrészleteinek megismeréséhez ellenőrizze a [Kereskövetési tevékenységet.](control-flow-lookup-activity.md)

## <a name="next-steps"></a>További lépések
A Data Factory másolási tevékenysége által forrásként és fogadóként támogatott adattárak listáját a Támogatott adattárak című témakörben [tetszését.](copy-activity-overview.md#supported-data-stores-and-formats)

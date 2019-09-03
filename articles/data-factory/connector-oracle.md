---
title: Adatok másolása az Oracle-ből és-ból az Azure Data Factory használatával | Microsoft Docs
description: Megtudhatja, hogyan másolhat át a támogatott forrásokból származó adatok egy Oracle-adatbázisba vagy Oracle-ből a támogatott fogadó áruházakba Data Factory használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/23/2019
ms.author: jingwang
ms.openlocfilehash: 90adacffd947be38b447117bfe64242bed3a90af
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/03/2019
ms.locfileid: "70231359"
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory"></a>Adatok másolása az Oracle-ből és a rendszerből a Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-onprem-oracle-connector.md)
> * [Aktuális verzió](connector-oracle.md)

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok egy Oracle-adatbázisba való másolásához. A [másolási tevékenység áttekintésére](copy-activity-overview.md)épül.

## <a name="supported-capabilities"></a>Támogatott képességek

Az Oracle-adatbázisok adatait bármely támogatott fogadó adattárba másolhatja. Az adatok bármely támogatott forrásból származó adattárból is átmásolhatók egy Oracle-adatbázisba. A másolási tevékenység által források vagy fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban, ez az Oracle-összekötő a következőket támogatja:

- Egy Oracle-adatbázis következő verziói:
    - Oracle 18c R1 (18,1) és újabb verziók
    - Oracle 12c R1 (12,1) és újabb verziók
    - Oracle 11g R1 (11,1) és újabb verziók
    - Oracle 10g R1 (10,1) és újabb
    - Oracle 9i R2 (9,2) és újabb
    - Oracle 8i R3 (8.1.7) és újabb verziók
    - Oracle Database Cloud Exadata szolgáltatás
- Adatok másolása alapszintű vagy OID hitelesítés használatával.
- Párhuzamos másolás Oracle-forrásból. Részletekért tekintse meg az [Oracle párhuzamos másolási](#parallel-copy-from-oracle) szakaszát.

> [!Note]
> Az Oracle-proxykiszolgáló nem támogatott.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)] 

Az Integration Runtime egy beépített Oracle-illesztőprogramot biztosít. Ezért nem kell manuálisan telepítenie az illesztőprogramot, amikor a és az Oracle rendszerbe másol adatokból.

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük az Oracle-összekötőhöz tartozó Data Factory-entitások definiálásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Az Oracle társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A Type tulajdonságot az **Oracle**értékre kell beállítani. | Igen |
| connectionString | Megadja az Oracle Database-példányhoz való kapcsolódáshoz szükséges adatokat. <br/>A mező megjelölése `SecureString` úgy, hogy biztonságosan tárolja Data Factoryban. A jelszót Azure Key Vaultba is helyezheti, és `password` lekérheti a konfigurációt a kapcsolatok sztringből. További részletekért tekintse meg a következő mintákat, és [tárolja Azure Key Vault a hitelesítő adatokat](store-credentials-in-key-vault.md) . <br><br>**Támogatott kapcsolattípus**: Az adatbázis azonosításához használhatja az **Oracle SID** vagy az **Oracle szolgáltatás nevét** :<br>– Ha SID-t használ:`Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>-Ha a szolgáltatás nevét használja:`Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;` | Igen |
| connectVia | A [integrációs modul](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. További tudnivalók az [Előfeltételek](#prerequisites) szakaszban olvashatók. Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

>[!TIP]
>Ha hibaüzenet jelenik meg, "ORA-01025: A UPI paraméter a megengedett tartományon kívül esik, és az Oracle-verziója `WireProtocolMode=1` 8i, adja hozzá a kapcsolódási karakterlánchoz. Ezután próbálkozzon újra.

Az Oracle-kapcsolatok titkosításának engedélyezéséhez két lehetőség közül választhat:

-   A **Triple-des encryption (3DES) és a Advanced Encryption Standard (AES)** használatához az Oracle Server oldalán nyissa meg az Oracle Advanced Security (OAS) oldalt, és konfigurálja a titkosítási beállításokat. További részletekért tekintse meg ezt az [Oracle](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759)-dokumentációt. Az Oracle Application Development Framework (ADF) összekötő automatikusan egyezteti a titkosítási módszert, hogy a OAS-ben konfigurált konfigurációt használja az Oracle-hez való kapcsolódáskor.

-   Az **SSL**használata:

    1.  Az SSL-tanúsítvány adatainak beolvasása. Szerezze be az SSL-tanúsítvány DER-szabályok (DER) kódolású tanúsítványának adatait, és mentse a kimenetet (-----BEGIN Certificate... Záró tanúsítvány-----) szövegfájlként.

        ```
        openssl x509 -inform DER -in [Full Path to the DER Certificate including the name of the DER Certificate] -text
        ```

        **Példa:** Bontsa ki a CERT-adatokat a DERcert. cer fájlból, majd mentse a kimenetet a CERT. txt fájlba.

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
    
    2.  Hozza létre `keystore` a `truststore`vagy a-t. A következő parancs jelszóval `truststore` vagy anélkül hozza létre a fájlt PKCS-12 formátumban.

        ```
        openssl pkcs12 -in [Path to the file created in the previous step] -out [Path and name of TrustStore] -passout pass:[Keystore PWD] -nokeys -export
        ```

        **Példa:** Hozzon létre `truststore` egy MyTrustStoreFile nevű PKCS12/pfx-profil-fájlt jelszóval.

        ```
        openssl pkcs12 -in cert.txt -out MyTrustStoreFile -passout pass:ThePWD -nokeys -export  
        ```

    3.  Helyezze a `truststore` fájlt a saját üzemeltetésű IR-gépre. Tegyük fel például, hogy a fájl a következő helyen található: C:\MyTrustStoreFile.
    4.  A Azure Data Factoryban konfigurálja `EncryptionMethod=1` az Oracle-kapcsolatok karakterláncát a és a megfelelő `TrustStore` / `TrustStorePassword`értékkel. Például: `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;EncryptionMethod=1;TrustStore=C:\\MyTrustStoreFile;TrustStorePassword=<trust_store_password>`.

**Példa:**

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

**Példa: a Jelszó tárolása Azure Key Vault**

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

Ez a szakasz az Oracle-adatkészlet által támogatott tulajdonságok listáját tartalmazza. Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját lásd: [](concepts-datasets-linked-services.md)adatkészletek. 

Az adatok és az Oracle közötti másoláshoz állítsa az adatkészlet `OracleTable`Type (típus) tulajdonságát a következőre:. A következő tulajdonságok támogatottak.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | Az adatkészlet Type tulajdonságát be kell állítani `OracleTable`. | Igen |
| tableName |Annak az Oracle-adatbázisnak a neve, amelyre a társított szolgáltatás hivatkozik. | Igen |

**Példa:**

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

Ez a szakasz az Oracle-forrás és a fogadó által támogatott tulajdonságok listáját tartalmazza. A tevékenységek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját lásd: [folyamatok](concepts-pipelines-activities.md). 

### <a name="oracle-as-source"></a>Oracle forrásként

>[!TIP]
>Az Oracle-adatoknak az adatparticionálással való hatékony betöltéséhez az [Oracle](#parallel-copy-from-oracle)-ből származó párhuzamos másolásról többet is megtudhat.

Az Oracle-adatok másolásához állítsa a forrás típusát a másolási tevékenységbe `OracleSource`. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának Type tulajdonságát a következőre `OracleSource`kell beállítani:. | Igen |
| oracleReaderQuery | Az egyéni SQL-lekérdezés segítségével olvassa el az adatokat. Például: `"SELECT * FROM MyTable"`.<br>Ha engedélyezi a particionált terhelést, össze kell kapcsolnia a lekérdezéshez tartozó beépített partíciós paramétereket. Példaként tekintse meg az [Oracle párhuzamos másolási](#parallel-copy-from-oracle) szakaszát. | Nem |
| partitionOptions | Az Oracle-adatok betöltéséhez használt adatparticionálási beállításokat adja meg. <br>Engedélyezett értékek a következők: **Nincs** (alapértelmezett), **PhysicalPartitionsOfTable** és **DynamicRange**.<br>Ha engedélyezve van egy partíciós beállítás (azaz nem `None`), az Oracle-adatbázisok adatainak párhuzamos betöltésének foka a másolási tevékenység [`parallelCopies`](copy-activity-performance.md#parallel-copy) beállításai vezérlik. | Nem |
| partitionSettings | Határozza meg az adatparticionálási beállítások csoportját. <br>Akkor alkalmazza, ha a partíció `None`lehetőség nem. | Nem |
| partitionNames | A másolandó fizikai partíciók listája. <br>Akkor alkalmazza, ha a partíciós beállítás van `PhysicalPartitionsOfTable`. Ha lekérdezést használ a forrásadatok beolvasásához, a WHERE záradékban lévő hookot `?AdfTabularPartitionName` . Példaként tekintse meg az [Oracle párhuzamos másolási](#parallel-copy-from-oracle) szakaszát. | Nem |
| partitionColumnName | Adja meg a forrás oszlop **olyan egész típusú** nevét, amelyet a párhuzamos másolási tartomány particionálásakor fog használni. Ha nincs megadva, a rendszer automatikusan észleli a tábla elsődleges kulcsát, és a partíció oszlopként használja. <br>Akkor alkalmazza, ha a partíciós beállítás van `DynamicRange`. Ha lekérdezést használ a forrásadatok beolvasásához, a WHERE záradékban lévő hookot `?AdfRangePartitionColumnName` . Példaként tekintse meg az [Oracle párhuzamos másolási](#parallel-copy-from-oracle) szakaszát. | Nem |
| partitionUpperBound | Az adatmásolásra szolgáló partíciós oszlop maximális értéke. <br>Akkor alkalmazza, ha a partíciós beállítás van `DynamicRange`. Ha lekérdezést használ a forrásadatok beolvasásához, a WHERE záradékban lévő hookot `?AdfRangePartitionUpbound` . Példaként tekintse meg az [Oracle párhuzamos másolási](#parallel-copy-from-oracle) szakaszát. | Nem |
| partitionLowerBound | Az adatmásolásra szolgáló partíciós oszlop minimális értéke. <br>Akkor alkalmazza, ha a partíciós beállítás van `DynamicRange`. Ha lekérdezést használ a forrásadatok beolvasásához, a WHERE záradékban lévő hookot `?AdfRangePartitionLowbound` . Példaként tekintse meg az [Oracle párhuzamos másolási](#parallel-copy-from-oracle) szakaszát. | Nem |

**Példa: adatok másolása egy alapszintű, partíció nélküli lekérdezés használatával**

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

### <a name="oracle-as-sink"></a>Oracle as mosogató

Az Oracle-be történő másoláshoz állítsa a fogadó típusát a másolási tevékenységbe `OracleSink`. A másolási tevékenység fogadója szakaszban a következő tulajdonságok támogatottak.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadójának Type tulajdonságát be kell állítani `OracleSink`. | Igen |
| writeBatchSize | Beilleszti az adatmennyiséget az SQL-táblába, amikor eléri `writeBatchSize`a puffer méretét.<br/>Az engedélyezett értékek egész szám (sorok száma). |Nem (az alapértelmezett érték 10 000) |
| writeBatchTimeout | Az a várakozási idő, ameddig a Batch INSERT művelet befejeződik, mielőtt időtúllépés történt.<br/>Az engedélyezett értékek a TimeSpan. Például 00:30:00 (30 perc). | Nem |
| preCopyScript | Adja meg a másolási tevékenység futtatásához szükséges SQL-lekérdezést, mielőtt az összes futtatáskor beírja az Oracle-ba. Ennek a tulajdonságnak a használatával törölheti az előre feltöltött adatkészleteket. | Nem |

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

## <a name="parallel-copy-from-oracle"></a>Párhuzamos másolás Oracle-ből

A Data Factory Oracle Connector beépített adatparticionálást biztosít az adatok Oracle-ből párhuzamos másolásához. Az adatparticionálási beállítások a másolási tevékenység **forrás** lapján találhatók.

![Képernyőfelvétel a partíciós beállításokról](./media/connector-oracle/connector-oracle-partition-options.png)

A particionált másolás engedélyezésekor a Data Factory párhuzamos lekérdezéseket futtat az Oracle-forráson az adatpartíciók alapján történő betöltéshez. A párhuzamos mértéket a másolási [`parallelCopies`](copy-activity-performance.md#parallel-copy) tevékenység beállításai vezérlik. Ha például négyre van állítva `parallelCopies` , Data Factory egyidejűleg létrehoz és futtat négy lekérdezést a megadott partíciós beállítás és beállítások alapján, és mindegyik lekérdezés az Oracle-adatbázisból származó adatok egy részét kéri le.

Javasoljuk, hogy engedélyezze a párhuzamos másolást az adatok particionálásával, különösen akkor, ha nagy mennyiségű adatmennyiséget tölt be az Oracle-adatbázisból. Az alábbiakban a különböző forgatókönyvekhez javasolt konfigurációk szerepelnek. Az adatok file-alapú adattárba való másolása során a rendszer úgy helyezi át, hogy több fájlként írjon egy mappába (csak a mappa nevét adja meg), amely esetben a teljesítmény jobb, mint egyetlen fájl írásakor.

| Forgatókönyv                                                     | Javasolt beállítások                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Teljes terhelés nagyméretű táblából, fizikai partíciókkal.          | **Partíciós beállítás**: A tábla fizikai partíciói. <br><br/>A végrehajtás során a Data Factory automatikusan észleli a fizikai partíciókat, és az Adatmásolást partíciók szerint. |
| Teljes terhelés a nagyméretű táblából fizikai partíciók nélkül, míg az adatparticionálás egész oszlopa. | **Partíciós beállítások**: Dinamikus tartomány partíciója.<br>**Partíciós oszlop**: Az adatparticionáláshoz használt oszlop meghatározása. Ha nincs megadva, a rendszer az elsődleges kulcs oszlopot használja. |
| Nagy mennyiségű adattal tölthet be egy egyéni lekérdezést, fizikai partíciókkal. | **Partíciós beállítás**: A tábla fizikai partíciói.<br>**Lekérdezés**: `SELECT * FROM <TABLENAME> PARTITION("?AdfTabularPartitionName") WHERE <your_additional_where_clause>`.<br>**Partíció neve**: Adja meg az adatok másolásához szükséges partíciós nevet (ka) t. Ha nincs megadva, a Data Factory automatikusan észleli az Oracle-adatkészletben megadott tábla fizikai partícióit.<br><br>A végrehajtás során Data Factory lecseréli `?AdfTabularPartitionName` a tényleges partíció nevét, és elküldi az Oracle-nek. |
| Nagy mennyiségű adatmennyiséget tölthet be egy egyéni lekérdezéssel, fizikai partíciók nélkül, valamint az adatparticionálás egész oszlopával. | **Partíciós beállítások**: Dinamikus tartomány partíciója.<br>**Lekérdezés**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Partíciós oszlop**: Az adatparticionáláshoz használt oszlop meghatározása. Az oszlop egész adattípussal is particionálható.<br>**Partíció felső határa** és a **partíció alsó határa**: Adja meg, hogy szeretné-e szűrni a partíciós oszlop alapján, hogy csak az alsó és a felső tartomány között lehessen beolvasni az adatgyűjtést.<br><br>A végrehajtás során a Data Factory `?AdfRangePartitionColumnName`lecseréli `?AdfRangePartitionLowbound` `?AdfRangePartitionUpbound`, és az egyes partíciók tényleges oszlopának nevét és értékeit, majd elküldi az Oracle-nek. <br>Ha például az "ID" partíciós oszlop értéke az 1 értékre van állítva, a felső határ pedig 80, a párhuzamos másolási beállítás értéke 4, Data Factory 4 partíció alapján kérdezi le az adatmennyiséget. Az azonosítóik [1, 20], [21, 40], [41, 60] és [61, 80] között vannak. |

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

**Példa: lekérdezés dinamikus tartományú partícióval**

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

## <a name="data-type-mapping-for-oracle"></a>Az Oracle adattípusának leképezése

Amikor a és az Oracle rendszerbe másol adatokból, a következő leképezések érvényesek. Ha szeretné megtudni, hogyan képezi le a másolási tevékenység a forrás sémát és az adattípust a fogadónak, tekintse meg a [séma-és adattípus](copy-activity-schema-and-type-mapping.md)-leképezéseket.

| Oracle-adattípus | Data Factory közbenső adattípus |
|:--- |:--- |
| BFILE |Byte[] |
| BLOB |Byte[]<br/>(csak Oracle 10g és újabb verziók esetén támogatott) |
| CHAR |Sztring |
| CLOB |String |
| DATE |DateTime |
| FLOAT |Decimal, String (Ha a pontosság > 28) |
| INTEGER |Decimal, String (Ha a pontosság > 28) |
| LONG |Sztring |
| LONG RAW |Byte[] |
| NCHAR |Sztring |
| NCLOB |Sztring |
| NUMBER |Decimal, String (Ha a pontosság > 28) |
| NVARCHAR2 |Sztring |
| RAW |Byte[] |
| ROWID |Sztring |
| TIMESTAMP |DateTime |
| TIMESTAMP WITH LOCAL TIME ZONE |Sztring |
| TIMESTAMP WITH TIME ZONE |Sztring |
| UNSIGNED INTEGER |Number |
| VARCHAR2 |Sztring |
| XML |Sztring |

> [!NOTE]
> Az adattípusok INTERVALLUMa év – hónap, és a nap és a második közötti intervallum nem támogatott.


## <a name="next-steps"></a>További lépések
A másolási tevékenység, Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md##supported-data-stores-and-formats).

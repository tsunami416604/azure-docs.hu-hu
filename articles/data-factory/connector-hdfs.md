---
title: Adatok másolása a HDFS a Azure Data Factory használatával
description: Megtudhatja, hogyan másolhat adatok egy felhőből vagy helyszíni HDFS-forrásból a fogadó adattárakba egy Azure Data Factory folyamat másolási tevékenységének használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/15/2020
ms.author: jingwang
ms.openlocfilehash: f39fb50c5044cacb04b3b147a0160dd23c9eb2d0
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83657096"
---
# <a name="copy-data-from-hdfs-using-azure-data-factory"></a>Adatok másolása a HDFS a Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-hdfs-connector.md)
> * [Aktuális verzió](connector-hdfs.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk az adatok HDFS-kiszolgálóról történő másolását ismerteti. A Azure Data Factoryről a [bevezető cikkben](introduction.md)olvashat bővebben.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a HDFS-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

Ez a HDFS-összekötő a következőket támogatja:

- Fájlok másolása **Windows** (Kerberos) vagy **Névtelen** hitelesítés használatával.
- Fájlok másolása a **webhdfs** protokollal vagy **a beépített DistCp-** támogatással.
- Fájlok másolása a-ként vagy a fájlok elemzése/létrehozása a [támogatott fájlformátumokkal és tömörítési kodekekkel](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

> [!NOTE]
> Győződjön meg arról, hogy a Integration Runtime hozzáférhet az **összes** [név csomópont-kiszolgálóhoz]: [name Node port] és [adatcsomópont-kiszolgálók]: [adatcsomópont-port] a Hadoop-fürthöz. Az alapértelmezett [name Node port] a 50070, és az alapértelmezett [adatcsomópont-port] a 50075.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyek a HDFS-re vonatkozó Data Factory-entitások definiálására szolgálnak.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A HDFS társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Description | Kötelező |
|:--- |:--- |:--- |
| típus | A Type tulajdonságot a következőre kell beállítani: **Hdfs**. | Igen |
| url |A HDFS URL-címe |Igen |
| authenticationType | Az engedélyezett értékek: **Anonymous**vagy **Windows**. <br><br> Ha **Kerberos-hitelesítést** kíván használni a HDFS-összekötőhöz, tekintse meg [ezt a szakaszt](#use-kerberos-authentication-for-hdfs-connector) , és ennek megfelelően állítsa be a helyszíni környezetet. |Igen |
| userName (Felhasználónév) |Felhasználónév a Windows-hitelesítéshez. Kerberos-hitelesítés esetén válassza a következőt: `<username>@<domain>.com` . |Igen (Windows-hitelesítéshez) |
| jelszó |Jelszó a Windows-hitelesítéshez. Megjelöli ezt a mezőt SecureString, hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). |Igen (Windows-hitelesítéshez) |
| Connectvia tulajdonsággal | Az adattárhoz való kapcsolódáshoz használt [Integration Runtime](concepts-integration-runtime.md) . További tudnivalók az [Előfeltételek](#prerequisites) szakaszban olvashatók. Ha nincs megadva, az alapértelmezett Azure Integration Runtime használja. |Nem |

**Példa: Névtelen hitelesítés használata**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Anonymous",
            "userName": "hadoop"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Példa: Windows-hitelesítés használata**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Windows",
            "userName": "<username>@<domain>.com (for Kerberos auth)",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

A HDFS a következő tulajdonságokat támogatja a `location` Format-alapú adatkészlet beállításai alatt:

| Tulajdonság   | Description                                                  | Kötelező |
| ---------- | ------------------------------------------------------------ | -------- |
| típus       | Az `location` adatkészletben található Type tulajdonságot **HdfsLocation**értékre kell állítani. | Igen      |
| folderPath | A mappa elérési útja. Ha a mappa szűréséhez helyettesítő karaktert szeretne használni, hagyja ki ezt a beállítást, és a tevékenység forrásának beállításai között válassza a lehetőséget. | Nem       |
| fileName   | A fájlnév a megadott folderPath alatt. Ha helyettesítő karaktereket szeretne használni a fájlok szűréséhez, hagyja ki ezt a beállítást, és a tevékenység forrásának beállításai között válassza a lehetőséget. | Nem       |

**Például**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<HDFS linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "HdfsLocation",
                "folderPath": "root/folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz a HDFS forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="hdfs-as-source"></a>HDFS forrásként

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

A következő tulajdonságok támogatottak a HDFS a `storeSettings` Format-alapú másolási forrás beállításai alatt:

| Tulajdonság                 | Description                                                  | Kötelező                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| típus                     | A Type tulajdonságot a `storeSettings` **HdfsReadSettings**értékre kell állítani. | Igen                                           |
| ***Keresse meg a másolandó fájlokat:*** |  |  |
| 1. lehetőség: statikus elérési út<br> | Másolja az adatkészletben megadott mappa vagy fájl elérési útját. Ha az összes fájlt egy mappából szeretné másolni, azt is meg kell adnia `wildcardFileName` `*` . |  |
| 2. lehetőség: helyettesítő karakter<br>- wildcardFolderPath | A mappa elérési útja helyettesítő karakterekkel a forrás mappák szűréséhez. <br>Az engedélyezett helyettesítő karakterek a következők: `*` (nulla vagy több karakternek felel meg) és `?` (a nulla vagy egy karakter egyezése) `^` <br>További példákat a [mappák és a fájlok szűrésére szolgáló példákban](#folder-and-file-filter-examples)talál. | Nem                                            |
| 2. lehetőség: helyettesítő karakter<br>- wildcardFileName | A forrásfájl szűréséhez a megadott folderPath/wildcardFolderPath helyettesítő karaktereket tartalmazó fájlnév. <br>Az engedélyezett helyettesítő karakterek a következők: `*` (nulla vagy több karakternek felel meg) és `?` (a nulla vagy egy karakter egyezése) `^`  További példákat a [mappák és a fájlok szűrésére szolgáló példákban](#folder-and-file-filter-examples)talál. | Igen |
| 3. lehetőség: a fájlok listája<br>- fileListPath | Egy adott fájl másolását jelzi. Mutasson egy szövegfájlra, amely tartalmazza a másolni kívánt fájlok listáját, soronként egy fájlt, amely az adatkészletben konfigurált útvonal relatív elérési útja.<br/>Ha ezt a beállítást használja, ne adja meg a fájl nevét az adatkészletben. További példákat a [fájllista példákban](#file-list-examples)talál. |Nem |
| ***További beállítások:*** |  | |
| rekurzív | Azt jelzi, hogy az adatok rekurzív módon olvashatók-e az almappákból, vagy csak a megadott mappából. Vegye figyelembe, hogy ha a rekurzív értéke TRUE (igaz), a fogadó pedig egy fájl alapú tároló, a fogadó nem másolja vagy hozza létre az üres mappát vagy almappát. <br>Az engedélyezett értékek: **true** (alapértelmezett) és **false (hamis**).<br>Ez a tulajdonság nem érvényes a konfiguráláskor `fileListPath` . |Nem |
| modifiedDatetimeStart    | A fájlok szűrése a következő attribútum alapján: utoljára módosítva. <br>A fájlok akkor lesznek kiválasztva, ha az utolsó módosítás időpontja a és a közötti időtartományon belül van `modifiedDatetimeStart` `modifiedDatetimeEnd` . Az idő az UTC-időzónára vonatkozik "2018-12-01T05:00:00Z" formátumban. <br> A tulajdonságok értéke lehet NULL, ami azt jelenti, hogy nem lesz alkalmazva a file Attribute szűrő az adatkészletre.  Ha `modifiedDatetimeStart` a dátum datetime értékkel rendelkezik `modifiedDatetimeEnd` , de null értékű, az azt jelenti, hogy azok a fájlok lesznek kiválasztva, amelyek utolsó módosított attribútuma nagyobb vagy egyenlő, mint a DateTime érték.  Ha `modifiedDatetimeEnd` a dátum datetime értékkel rendelkezik `modifiedDatetimeStart` , de null értékű, az azt jelenti, hogy azok a fájlok, amelyek utolsó módosítási attribútuma kisebb, mint a DateTime érték, ki lesz választva.<br/>Ez a tulajdonság nem érvényes a konfiguráláskor `fileListPath` . | Nem                                            |
| maxConcurrentConnections | A tárolási tárolóhoz való kapcsolódáshoz szükséges kapcsolatok száma egyidejűleg. Csak akkor kell megadni, ha az egyidejű kapcsolódást szeretné korlátozni az adattárral. | Nem                                            |
| ***DistCp beállításai:*** |  | |
| distcpSettings | Tulajdonság a HDFS DistCp használatakor. | Nem |
| resourceManagerEndpoint | A fonál Resource Manager-végpont | Igen, ha DistCp használ |
| tempScriptPath | A temp DistCp parancs parancsfájljának tárolására szolgáló mappa elérési útja. A parancsfájlt Data Factory hozza létre, és a másolási feladatok befejezése után el lesz távolítva. | Igen, ha DistCp használ |
| distcpOptions | A DistCp parancshoz megadott további beállítások. | Nem |

**Például**

```json
"activities":[
    {
        "name": "CopyFromHDFS",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
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
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "HdfsReadSettings",
                    "recursive": true,
                    "distcpSettings": {
                        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
                        "tempScriptPath": "/usr/hadoop/tempscript",
                        "distcpOptions": "-m 100"
                    }
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Példák a mappák és a fájlok szűrésére

Ez a szakasz a mappa elérési útjának és fájlnevének a helyettesítő karakteres szűrőkkel való viselkedését írja le.

| folderPath | fileName             | rekurzív | A forrás mappa szerkezete és a szűrő eredménye (a **félkövérrel szedett** fájlok beolvasása) |
| :--------- | :------------------- | :-------- | :----------------------------------------------------------- |
| `Folder*`  | (üres, alapértelmezett használata) | hamis     | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Fájl2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*`  | (üres, alapértelmezett használata) | igaz      | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Fájl2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*`  | `*.csv`              | hamis     | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*`  | `*.csv`              | igaz      | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |

### <a name="file-list-examples"></a>Példák a fájllista

Ez a szakasz a fájllista elérési útjának a másolási tevékenység forrásában való használatának eredményét írja le.

Feltételezve, hogy rendelkezik a következő forrás-mappa struktúrájával, és félkövéren szeretné átmásolni a fájlokat:

| Példa a forrás struktúrájára                                      | Tartalom a FileListToCopy. txt fájlban                             | ADF-konfiguráció                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| legfelső szintű<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metaadatok<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy. txt | File1. csv<br>Subfolder1/fájl3. csv<br>Subfolder1/File5. csv | **Az adatkészletben:**<br>-Mappa elérési útja:`root/FolderA`<br><br>**A másolási tevékenység forrása:**<br>-Fájllista elérési útja:`root/Metadata/FileListToCopy.txt` <br><br>A fájl-lista elérési útja ugyanazon az adattárban található szövegfájlra mutat, amely tartalmazza a másolni kívánt fájlok listáját, és soronként egy fájlt az adatkészletben konfigurált útvonal relatív elérési útjával. |

## <a name="use-distcp-to-copy-data-from-hdfs"></a>Adatok másolása a HDFS-ből a DistCp használatával

A [DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) egy Hadoop natív parancssori eszköz, amellyel elosztott másolatot készíthet egy Hadoop-fürtön. Distcp parancs futtatásakor először az összes másolandó fájlt listázza, több leképezési feladatot hoz létre a Hadoop-fürtben, és minden térképi feladat binárisan másolja a forrásról a fogadóba.

A másolási tevékenység támogatása a DistCp használatával a fájlok másolása az Azure Blobba (beleértve a [szakaszos másolást](copy-activity-performance.md)) vagy a Azure Data Lake Store, amely esetben teljes mértékben kihasználhatja a fürt erejét a saját üzemeltetésű Integration Runtime futtatása helyett. Jobb másolási sebességet biztosít, különösen akkor, ha a fürt nagyon hatékony. A Azure Data Factory konfigurációján alapuló másolási tevékenység automatikusan létrehoz egy distcp-parancsot, elküldi a Hadoop-fürtnek, és figyeli a másolási állapotot.

### <a name="prerequisites"></a>Előfeltételek

Ha a DistCp-et szeretné használni a HDFS-ből az Azure-Blobba (beleértve a szakaszos másolást) vagy a Azure Data Lake Store, akkor győződjön meg arról, hogy a Hadoop-fürt megfelel az alábbi követelményeknek:

1. A MapReduce és a fonalas szolgáltatások engedélyezve vannak.
2. A fonal verziója 2,5 vagy újabb.
3. A HDFS-kiszolgáló integrálva van a célként megadott adattárral – Azure Blob vagy Azure Data Lake Store:

    - Az Azure Blob-fájlrendszer natív módon támogatott a Hadoop 2,7 óta. Csak a jar elérési utat kell megadnia a Hadoop env-konfigurációban.
    - Azure Data Lake Store fájlrendszer a Hadoop 3.0.0-alfa1-től kezdve van csomagolva. Ha a Hadoop-fürt alacsonyabb ennél a verziónál, manuálisan kell importálnia a ADLS kapcsolódó jar-csomagokat (Azure-datalake-Store. jar) a fürtbe [innen, és](https://hadoop.apache.org/releases.html)a jar elérési utat kell megadnia a Hadoop env konfigurációban.

4. Készítse elő a Temp mappát a HDFS-ben. Ez a temp mappa a DistCp-rendszerhéj parancsfájljának tárolására szolgál, így KB szintű helyet foglal le.
5. Győződjön meg arról, hogy a HDFS társított szolgáltatásban megadott felhasználói fiók rendelkezik engedéllyel a () alkalmazás beküldéséhez a Fonalban; b) engedéllyel rendelkezik az almappa létrehozásához, a fájlok olvasásához/írásához a fenti Temp mappában.

### <a name="configurations"></a>Konfigurációk

Lásd: DistCp kapcsolódó konfigurációk és példák a [HDFS forrás](#hdfs-as-source) szakasza.

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Kerberos-hitelesítés használata a HDFS-összekötőhöz

Két lehetőség áll rendelkezésre a helyszíni környezet beállítására úgy, hogy a Kerberos-hitelesítést használják a HDFS-összekötőben. Kiválaszthatja, hogy melyik illik jobban az esethez.
* 1. lehetőség: saját üzemeltetésű [Integration Runtime gép csatlakoztatása Kerberos-tartományban](#kerberos-join-realm)
* 2. lehetőség: [kölcsönös megbízhatóság engedélyezése a Windows és a Kerberos tartomány között](#kerberos-mutual-trust)

### <a name="option-1-join-self-hosted-integration-runtime-machine-in-kerberos-realm"></a><a name="kerberos-join-realm"></a>1. lehetőség: saját üzemeltetésű Integration Runtime gép csatlakoztatása Kerberos-tartományban

#### <a name="requirements"></a>Követelmények

* A saját üzemeltetésű Integration Runtime számítógépnek csatlakoznia kell a Kerberos-tartományhoz, és nem csatlakozhat Windows-tartományhoz.

#### <a name="how-to-configure"></a>Konfigurálás

**Önkiszolgáló Integration Runtime gépen:**

1.  Futtassa a **Ksetup** segédprogramot a Kerberos KDC-kiszolgáló és-tartomány konfigurálásához.

    A gépet egy munkacsoport tagjaként kell konfigurálni, mert egy Kerberos-tartomány eltér a Windows-tartománytól. Ezt a Kerberos-tartomány beállításával és a KDC-kiszolgáló a következőképpen való hozzáadásával lehet megvalósítani. Szükség szerint cserélje le a *REALM.com* -t a saját megfelelő tartománynevére.

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    **Indítsa újra** a gépet a 2 parancs végrehajtása után.

2.  Ellenőrizze a konfigurációt a **Ksetup** paranccsal. A kimenetnek az alábbihoz hasonlónak kell lennie:

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**Azure Data Factory:**

* Konfigurálja a HDFS-összekötőt a **Windows-hitelesítéssel** együtt a Kerberos egyszerű felhasználónevével és jelszavával a HDFS-adatforráshoz való csatlakozáshoz. A konfiguráció részleteinél keresse meg a [HDFS társított szolgáltatás tulajdonságai](#linked-service-properties) szakaszt.

### <a name="option-2-enable-mutual-trust-between-windows-domain-and-kerberos-realm"></a><a name="kerberos-mutual-trust"></a>2. lehetőség: kölcsönös megbízhatóság engedélyezése a Windows és a Kerberos tartomány között

#### <a name="requirements"></a>Követelmények

*   A saját üzemeltetésű Integration Runtime számítógépnek csatlakoznia kell egy Windows-tartományhoz.
*   Engedéllyel kell rendelkeznie a tartományvezérlő beállításainak frissítéséhez.

#### <a name="how-to-configure"></a>Konfigurálás

> [!NOTE]
> Cserélje le a REALM.COM és a AD.COM-t a következő oktatóanyagban a saját megfelelő tartományára és tartományvezérlőre szükség szerint.

**KDC-kiszolgálón:**

1. Szerkessze a KDC konfigurációját a **krb5. conf** fájlban, hogy a KDC megbízzon a Windows-tartományon a következő konfigurációs sablonra hivatkozva. Alapértelmezés szerint a konfiguráció a következő helyen található: **/etc/krb5.conf állományt**.

           [logging]
            default = FILE:/var/log/krb5libs.log
            kdc = FILE:/var/log/krb5kdc.log
            admin_server = FILE:/var/log/kadmind.log
            
           [libdefaults]
            default_realm = REALM.COM
            dns_lookup_realm = false
            dns_lookup_kdc = false
            ticket_lifetime = 24h
            renew_lifetime = 7d
            forwardable = true
            
           [realms]
            REALM.COM = {
             kdc = node.REALM.COM
             admin_server = node.REALM.COM
            }
           AD.COM = {
            kdc = windc.ad.com
            admin_server = windc.ad.com
           }
            
           [domain_realm]
            .REALM.COM = REALM.COM
            REALM.COM = REALM.COM
            .ad.com = AD.COM
            ad.com = AD.COM
            
           [capaths]
            AD.COM = {
             REALM.COM = .
            }

   A konfiguráció után **indítsa újra** a KDC szolgáltatást.

2. Készítse elő a **krbtgt/REALM. COM \@ ad.com** nevű rendszerbiztonsági tag a KDC-kiszolgálón a következő paranccsal:

           Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3. A **Hadoop. Security. auth_to_local** HDFS szolgáltatás konfigurációs fájljában adja hozzá a t `RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//` .

**Tartományvezérlőn:**

1.  A következő **Ksetup** parancsok futtatásával vegyen fel egy tartományi bejegyzést:

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  Megbízhatósági kapcsolat létrehozása a Windows-tartományból a Kerberos tartományba. a [password] a fő **krbtgt/REALM. COM \@ ad.com**tartozó jelszó.

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  Válassza ki a Kerberosban használt titkosítási algoritmust.

    1. Lépjen a Kiszolgálókezelő > Csoportházirend felügyeleti > tartomány > Csoportházirend objektumok > alapértelmezett vagy aktív tartományi házirend elemre, és szerkessze a következőt:.

    2. A **csoportházirend-felügyeleti szerkesztő** előugró ablakban válassza a számítógép konfigurációja > házirendek > Windows beállítások > biztonsági beállítások > helyi házirendek > biztonsági beállítások, majd **a hálózati biztonság konfigurálása: a Kerberos számára engedélyezett titkosítási típusok konfigurálása**című részt.

    3. Válassza ki a KDC-hoz való csatlakozáskor használni kívánt titkosítási algoritmust. Általában egyszerűen kiválaszthatja az összes beállítást.

        ![A Kerberos konfigurációjának titkosítási típusai](media/connector-hdfs/config-encryption-types-for-kerberos.png)

    4. A **Ksetup** parancs használatával megadhatja az adott tartományban használandó titkosítási algoritmust.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Hozzon létre egy leképezést a tartományi fiók és a Kerberos-tag között a Kerberos-rendszerbiztonsági tag Windows-tartományban való használatához.

    1. Indítsa el a felügyeleti eszközöket > **Active Directory felhasználókat és számítógépeket**.

    2. A speciális szolgáltatások konfigurálásához **View**kattintson a  >  **speciális szolgáltatások**megtekintése elemre.

    3. Keresse meg azt a fiókot, amelyhez leképezéseket szeretne létrehozni, majd kattintson a jobb gombbal a **név-hozzárendelések** megtekintéséhez > kattintson a **Kerberos-nevek** fülre.

    4. Adjon hozzá egy rendszerbiztonsági tag a tartományhoz.

        ![Térkép biztonsági identitása](media/connector-hdfs/map-security-identity.png)

**Önkiszolgáló Integration Runtime gépen:**

* A következő **Ksetup** parancsok futtatásával vegyen fel egy tartományi bejegyzést.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**Azure Data Factory:**

* Konfigurálja a HDFS-összekötőt a **Windows-hitelesítéssel** együtt a tartományi fiókkal vagy a Kerberos-rendszerbiztonsági tag használatával a HDFS-adatforráshoz való csatlakozáshoz. A konfiguráció részleteinél keresse meg a [HDFS társított szolgáltatás tulajdonságai](#linked-service-properties) szakaszt.

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).

## <a name="legacy-models"></a>Örökölt modellek

>[!NOTE]
>A következő modellek továbbra is támogatottak a visszamenőleges kompatibilitás érdekében. Azt javasoljuk, hogy használja a fenti szakaszban említett új modellt, és az ADF authoring felhasználói felülete átvált az új modell generálására.

### <a name="legacy-dataset-model"></a>Örökölt adatkészlet-modell

| Tulajdonság | Description | Kötelező |
|:--- |:--- |:--- |
| típus | Az adatkészlet Type tulajdonságát a következőre kell beállítani: **fájlmegosztás** |Igen |
| folderPath | A mappa elérési útja. A helyettesítő karakteres szűrő támogatott, az engedélyezett helyettesítő karakterek a következők: `*` (nulla vagy több karakternek felel meg) és `?` (a nulla vagy az egyetlen karakternek felel meg); `^` Ha a tényleges fájlnév helyettesítő karakter vagy a escape-karakter szerepel a rendszerben, használja a Escape karaktert. <br/><br/>Példák: gyökérmappa/almappa/, további példák a [mappák és a fájlok szűrése példákban](#folder-and-file-filter-examples). |Igen |
| fileName |  A fájl (ok) **neve vagy helyettesítő szűrője** a megadott "folderPath". Ha nem ad meg értéket ehhez a tulajdonsághoz, az adatkészlet a mappában található összes fájlra mutat. <br/><br/>A Filter (szűrő) esetében az engedélyezett helyettesítő karakterek a következők: `*` (nulla vagy több karakternek felel meg) és `?` (nulla vagy egyetlen karakternek felel meg).<br/>– 1. példa:`"fileName": "*.csv"`<br/>– 2. példa:`"fileName": "???20180427.txt"`<br/>A (z `^` ) használatával elkerülheti a mappát, ha az aktuális mappanév helyettesítő karakterrel rendelkezik, vagy a menekülési karakter szerepel a |Nem |
| modifiedDatetimeStart | A fájlok szűrése a következő attribútum alapján: utoljára módosítva. A fájlok akkor lesznek kiválasztva, ha az utolsó módosítás időpontja a és a közötti időtartományon belül van `modifiedDatetimeStart` `modifiedDatetimeEnd` . Az idő az UTC-időzónára vonatkozik "2018-12-01T05:00:00Z" formátumban. <br/><br/> Ügyeljen arra, hogy az adatáthelyezés általános teljesítményét a beállítás engedélyezésével befolyásolja, ha nagy mennyiségű fájlból szeretne szűrni a fájlmegosztást. <br/><br/> A tulajdonságok értéke NULL lehet, ami azt jelenti, hogy a rendszer nem alkalmazza a file Attribute szűrőt az adatkészletre.  Ha `modifiedDatetimeStart` a dátum datetime értékkel rendelkezik `modifiedDatetimeEnd` , de null értékű, az azt jelenti, hogy azok a fájlok lesznek kiválasztva, amelyek utolsó módosított attribútuma nagyobb vagy egyenlő, mint a DateTime érték.  Ha `modifiedDatetimeEnd` a dátum datetime értékkel rendelkezik `modifiedDatetimeStart` , de null értékű, az azt jelenti, hogy azok a fájlok, amelyek utolsó módosítási attribútuma kisebb, mint a DateTime érték, ki lesz választva.| Nem |
| modifiedDatetimeEnd | A fájlok szűrése a következő attribútum alapján: utoljára módosítva. A fájlok akkor lesznek kiválasztva, ha az utolsó módosítás időpontja a és a közötti időtartományon belül van `modifiedDatetimeStart` `modifiedDatetimeEnd` . Az idő az UTC-időzónára vonatkozik "2018-12-01T05:00:00Z" formátumban. <br/><br/> Ügyeljen arra, hogy az adatáthelyezés általános teljesítményét a beállítás engedélyezésével befolyásolja, ha nagy mennyiségű fájlból szeretne szűrni a fájlmegosztást. <br/><br/> A tulajdonságok értéke NULL lehet, ami azt jelenti, hogy a rendszer nem alkalmazza a file Attribute szűrőt az adatkészletre.  Ha `modifiedDatetimeStart` a dátum datetime értékkel rendelkezik `modifiedDatetimeEnd` , de null értékű, az azt jelenti, hogy azok a fájlok lesznek kiválasztva, amelyek utolsó módosított attribútuma nagyobb vagy egyenlő, mint a DateTime érték.  Ha `modifiedDatetimeEnd` a dátum datetime értékkel rendelkezik `modifiedDatetimeStart` , de null értékű, az azt jelenti, hogy azok a fájlok, amelyek utolsó módosítási attribútuma kisebb, mint a DateTime érték, ki lesz választva.| Nem |
| formátumban | Ha **fájlokat szeretne másolni** a fájl alapú tárolók között (bináris másolás), ugorja át a formátum szakaszt mind a bemeneti, mind a kimeneti adatkészlet-definíciókban.<br/><br/>Ha a fájlokat egy adott formátummal szeretné elemezni, a következő fájlformátum-típusok támogatottak: **Szövegformátum**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. A **Type (típus** ) tulajdonságot állítsa a Format értékre a következő értékek egyikére. További információkért lásd: [Szövegformátum](supported-file-formats-and-compression-codecs-legacy.md#text-format), JSON- [Formátum](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro formátum](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [ork-formátum](supported-file-formats-and-compression-codecs-legacy.md#orc-format)és a [parketta formátuma](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) című rész. |Nem (csak bináris másolási forgatókönyv esetén) |
| tömörítés | Adja meg az adattömörítés típusát és szintjét. További információ: [támogatott fájlformátumok és tömörítési kodekek](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>A támogatott típusok a következők: **gzip**, **deflate**, **BZip2**és **ZipDeflate**.<br/>A támogatott szintek a következők: **optimális** és **leggyorsabb**. |Nem |

>[!TIP]
>Egy mappa összes fájljának másolásához csak a **folderPath** kell megadni.<br>Egy adott névvel rendelkező egyetlen fájl másolásához adja meg a **folderPath** és a fájlnév nevű **fájlnevet** .<br>Ha egy mappában lévő fájlok egy részhalmazát szeretné másolni, akkor a **folderPath** és a **filename** paramétert a helyettesítő karakteres szűrővel.

**Például**

```json
{
    "name": "HDFSDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<HDFS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

### <a name="legacy-copy-activity-source-model"></a>Örökölt másolási tevékenység forrásának modellje

| Tulajdonság | Description | Kötelező |
|:--- |:--- |:--- |
| típus | A másolási tevékenység forrásának Type tulajdonságát a következőre kell beállítani: **HdfsSource** |Igen |
| rekurzív | Azt jelzi, hogy az adatok rekurzív módon olvashatók-e az alárendelt mappákból, vagy csak a megadott mappából. Vegye figyelembe, hogy ha a rekurzív értéke TRUE (igaz), a fogadó pedig a fájl alapú tároló, akkor a rendszer nem másolja/hozza létre az üres mappát/almappát a fogadóban.<br/>Az engedélyezett értékek: **true** (alapértelmezett), **false** | Nem |
| distcpSettings | Tulajdonság a HDFS DistCp használatakor. | Nem |
| resourceManagerEndpoint | A fonál Resource Manager-végpont | Igen, ha DistCp használ |
| tempScriptPath | A temp DistCp parancs parancsfájljának tárolására szolgáló mappa elérési útja. A parancsfájlt Data Factory hozza létre, és a másolási feladatok befejezése után el lesz távolítva. | Igen, ha DistCp használ |
| distcpOptions | A DistCp parancshoz megadott további beállítások. | Nem |
| maxConcurrentConnections | A tárolási tárolóhoz való kapcsolódáshoz szükséges kapcsolatok száma egyidejűleg. Csak akkor kell megadni, ha az egyidejű kapcsolódást szeretné korlátozni az adattárral. | Nem |

**Példa: HDFS forrás a másolási tevékenységben a DistCp használatával**

```json
"source": {
    "type": "HdfsSource",
    "distcpSettings": {
        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
        "tempScriptPath": "/usr/hadoop/tempscript",
        "distcpOptions": "-m 100"
    }
}
```

## <a name="next-steps"></a>További lépések
A Azure Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).

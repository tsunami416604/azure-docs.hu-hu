---
title: Adatok másolása fájlrendszerből/fájlrendszerbe az Azure Data Factory használatával
description: Megtudhatja, hogyan másolhatja az adatokat a fájlrendszerből a támogatott fogadó adattárakba (vagy) a támogatott forrásadat-tárolókból a fájlrendszerbe az Azure Data Factory használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: 90bab7b49f82c3c9c8954c603e8d3bda6e2dcc89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77082927"
---
# <a name="copy-data-to-or-from-a-file-system-by-using-azure-data-factory"></a>Adatok másolása fájlrendszerbe vagy fájlrendszerből az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-onprem-file-system-connector.md)
> * [Aktuális verzió](connector-file-system.md)

Ez a cikk az adatok fájlrendszerbe és fájlrendszerből történő másolását ismerteti. Az Azure Data Factory ról a [bevezető cikkben](introduction.md)olvashat.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a fájlrendszer-összekötő a következő tevékenységek esetén támogatott:

- [Tevékenység másolása](copy-activity-overview.md) [támogatott forrás/fogadó mátrixcal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)
- [Tevékenység törlése](delete-activity.md)

Pontosabban ez a fájlrendszer-összekötő a következőket támogatja:

- Fájlok másolása a helyi számítógépről vagy hálózati fájlmegosztásra. Linux os fájlmegosztás használatához telepítse a [Samba-t](https://www.samba.org/) a Linux szerverre.
- Fájlok másolása **Windows** Windows-hitelesítéssel.
- Fájlok másolása a támogatott fájlformátumok és [tömörítési kodekek](supported-file-formats-and-compression-codecs.md)segítségével.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok a Data Factory entitások fájlrendszerre jellemző meghatározásához használt tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

A fájlrendszerhez csatolt szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A típustulajdonságnak a következőre kell állítania: **FileServer**. | Igen |
| gazda | A másolni kívánt mappa gyökérelérési útját adja meg. Használja a escape\" karakter " a különleges karaktereket a karakterláncban. [Példákat lásd: Minta csatolt szolgáltatás és adatkészlet-definíciók.](#sample-linked-service-and-dataset-definitions) | Igen |
| Userid | Adja meg annak a felhasználónak az azonosítóját, aki hozzáfér a kiszolgálóhoz. | Igen |
| jelszó | Adja meg a felhasználó jelszavát (userid). Jelölje meg ezt a mezőt SecureStringként a Data Factory biztonságos tárolásához, vagy [hivatkozzon az Azure Key Vaultban tárolt titkos fájlokra.](store-credentials-in-key-vault.md) | Igen |
| connectVia | Az adattárhoz való csatlakozáshoz használandó [integrációs futásidő.](concepts-integration-runtime.md) További információ az [Előfeltételek](#prerequisites) szakaszból. Ha nincs megadva, az alapértelmezett Azure-integrációs runtime-ot használja. |Nem |

### <a name="sample-linked-service-and-dataset-definitions"></a>Mintacsatolt szolgáltatás- és adatkészlet-definíciók

| Forgatókönyv | "állomás" az összekapcsolt szolgáltatás definíciójában | "folderPath" az adatkészlet-definícióban |
|:--- |:--- |:--- |
| Helyi mappa az integrációs futásidejű gépen: <br/><br/>Példák:\\ \* D: vagy D:\folder\almappa\\* |A JSON-ban:`D:\\`<br/>Felhasználói felületen:`D:\` |A JSON-ban: `.\\` vagy`folder\\subfolder`<br>Felhasználói felületen: `.\` vagy`folder\subfolder` |
| Távoli megosztott mappa: <br/><br/>\\ \\Példák:\\myserver \\ \\megosztás\\\\ \* \\vagy\\myserver megosztási mappa almappája\\* |A JSON-ban:`\\\\myserver\\share`<br/>Felhasználói felületen:`\\myserver\share` |A JSON-ban: `.\\` vagy`folder\\subfolder`<br/>Felhasználói felületen: `.\` vagy`folder\subfolder` |

>[!NOTE]
>A felhasználói felületen keresztültörténő szerkesztéskor nem kell`\\`dupla fordított perjelet ( ) megadnia ahhoz, hogy a JSON-on keresztül ily értsen, adja meg az egyetlen fordított perjelet.

**Példa:**

```json
{
    "name": "FileLinkedService",
    "properties": {
        "type": "FileServer",
        "typeProperties": {
            "host": "<host>",
            "userid": "<domain>\\<user>",
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

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [Adatkészletek](concepts-datasets-linked-services.md) című cikkben olvashatja. 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

A következő tulajdonságok támogatottak `location` a fájlrendszerhez a formátumalapú adatkészlet beállításai között:

| Tulajdonság   | Leírás                                                  | Kötelező |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Az adatkészlet `location` ben található típustulajdonságot **FileServerLocation**beállításra kell állítani. | Igen      |
| folderPath | A mappa elérési útja. Ha helyettesítő karaktert szeretne használni a mappa szűréséhez, hagyja ki ezt a beállítást, és adja meg a tevékenységforrás beállításaiban. | Nem       |
| fileName   | A fájlnév a megadott mappaPath. Ha helyettesítő karaktert szeretne használni a fájlok szűréséhez, hagyja ki ezt a beállítást, és adja meg a tevékenységforrás beállításaiban. | Nem       |

**Példa:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<File system linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "FileServerLocation",
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

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok](concepts-pipelines-activities.md) című cikkben olvashat. Ez a szakasz a fájlrendszer forrása és fogadója által támogatott tulajdonságok listáját tartalmazza.

### <a name="file-system-as-source"></a>Fájlrendszer forrásként

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

A következő tulajdonságok támogatottak `storeSettings` a fájlrendszerhez a formátumalapú másolási forrás beállításai között:

| Tulajdonság                 | Leírás                                                  | Kötelező                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | A típustulajdonságot `storeSettings` **FileServerReadSettings (FájlServerReadSettings )** tulajdonságra kell állítani. | Igen                                           |
| Rekurzív                | Azt jelzi, hogy az adatok olvasása rekurzív anameddig az almappákból vagy csak a megadott mappából történik. Ne feledje, hogy ha a rekurzív érték igaz, és a fogadó fájlalapú tároló, a program nem másolja vagy hozza létre az üres mappát vagy almappát a fogadóban. Az engedélyezett értékek **igazak** (alapértelmezettek) és **hamisak.** | Nem                                            |
| helyettesítő mappaelérési út       | A forrásmappák szűréséhez helyettesítő karakterekkel ellátott mappaelérési út. <br>Az engedélyezett helyettesítő `*` karakterek a következők: `?` (nulla vagy több karakternek felel meg) és (nulla vagy egy karakternek felel meg); akkor `^` menekülhet, ha a mappa tényleges neve helyettesítő karaktert használ, vagy ha ez az escape karakter van benne. <br>További példák a [Mappa- és fájlszűrő-példákban](#folder-and-file-filter-examples)találhatók. | Nem                                            |
| helyettesítő fájlneve         | A forrásfájlok szűréséhez helyettesítő karakterekkel rendelkező fájlnév a kívánt/helyettesítő folderFolderPath mappában. <br>Az engedélyezett helyettesítő `*` karakterek a következők: `?` (nulla vagy több karakternek felel meg) és (nulla vagy egy karakternek felel meg); akkor `^` menekülhet, ha a mappa tényleges neve helyettesítő karaktert használ, vagy ha ez az escape karakter van benne.  További példák a [Mappa- és fájlszűrő-példákban](#folder-and-file-filter-examples)találhatók. [Kérjük, vegye figyelembe, hogy ez felülbírálja a fájlnév beállítást] | Igen, `fileName` ha nincs megadva az adatkészletben |
| modifiedDatetimeStart    | A fájlok szűrője az attribútum alapján: Utolsó módosítás. A fájlok akkor lesznek kiválasztva, ha az `modifiedDatetimeStart` `modifiedDatetimeEnd`utolsó módosítási idejük a és a közötti időtartományon belül van. Az idő az UTC időzónára a "2018-12-01T05:00:00Z" formátumban kerül alkalmazásra. <br> A tulajdonságok null értékűek lehetnek, ami azt jelenti, hogy az adatkészletre nem lesz fájlattribútum-szűrő alkalmazva.  Ha `modifiedDatetimeStart` datetime értékkel rendelkezik, de `modifiedDatetimeEnd` NULL, az azt jelenti, hogy azokat a fájlokat, amelyek utolsó módosított attribútuma nagyobb vagy egyenlő, mint a datetime érték, ki lesznek jelölve.  Ha `modifiedDatetimeEnd` datetime értékkel rendelkezik, de `modifiedDatetimeStart` NULL, az azt jelenti, hogy azutolsó módosított attribútum kisebb, mint a datetime érték, ki lesznek jelölve. | Nem                                            |
| modifiedDatetimeEnd      | Ugyanaz, mint fent.                                               | Nem                                            |
| maxConcurrentConnections | A tárolóhoz egyidejűleg csatlakozó kapcsolatok száma. Csak akkor adja meg, ha korlátozni szeretné az egyidejű kapcsolatot az adattárhoz. | Nem                                            |

**Példa:**

```json
"activities":[
    {
        "name": "CopyFromFileSystem",
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
                    "type": "FileServerReadSettings",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="file-system-as-sink"></a>Fájlrendszer mint fogadó

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

A következő tulajdonságok támogatottak `storeSettings` a fájlrendszerhez a formátumalapú másolási gyűjtő beállításai között:

| Tulajdonság                 | Leírás                                                  | Kötelező |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | A típustulajdonságot `storeSettings` **FileServerWriteSettings (FájlServerWriteSettings**) tulajdonságra kell állítani. | Igen      |
| copyBehavior (Másként)             | Azt a másolási viselkedést határozza meg, amikor a forrás fájlalapú adattárból származó fájlok.<br/><br/>Az engedélyezett értékek a következők:<br/><b>- MegőrzéseHierarchy (alapértelmezett)</b>: Megőrzi a fájlhierarchiát a célmappában. A forrásfájl forrásmappához viszonyított elérési útja megegyezik a célfájl célmappához való relatív elérési útjával.<br/><b>- Ahierarcha összeolvasztása</b>: A forrásmappából származó összes fájl a célmappa első szintjén található. A célfájlok nak automatikusan generált neve van. <br/><b>- MergeFiles</b>: Egyesíti az összes fájlt a forrás mappát egy fájlt. Ha a fájlnév meg van adva, az egyesített fájlnév a megadott név. Ellenkező esetben ez egy automatikusan létrehozott fájlnév. | Nem       |
| maxConcurrentConnections | Az adattárhoz egyidejűleg csatlakozó kapcsolatok száma. Csak akkor adja meg, ha korlátozni szeretné az egyidejű kapcsolatot az adattárhoz. | Nem       |

**Példa:**

```json
"activities":[
    {
        "name": "CopyToFileSystem",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Parquet output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "ParquetSink",
                "storeSettings":{
                    "type": "FileServerWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Példák mappa- és fájlszűrőre

Ez a szakasz a mappa elérési útjának és a helyettesítő szűrőkkel rendelkező fájlnévnek a viselkedését ismerteti.

| folderPath | fileName | Rekurzív | A forrásmappa szerkezete és a szűrő eredménye (a **félkövérrel szedett** fájlok beolvasása)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (üres, használja az alapértelmezettet) | hamis | MappaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Fájl1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Fájl2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5.csv<br/>Egy másikFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl6.csv |
| `Folder*` | (üres, használja az alapértelmezettet) | igaz | MappaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Fájl1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Fájl2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl5.csv**<br/>Egy másikFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl6.csv |
| `Folder*` | `*.csv` | hamis | MappaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Fájl1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5.csv<br/>Egy másikFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl6.csv |
| `Folder*` | `*.csv` | igaz | MappaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Fájl1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl5.csv**<br/>Egy másikFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl6.csv |

### <a name="recursive-and-copybehavior-examples"></a>rekurzív és copyBehavior példák

Ez a szakasz a Copy művelet viselkedését ismerteti a rekurzív és copyBehavior értékek különböző kombinációihoz.

| Rekurzív | copyBehavior (Másként) | Forrásmappa szerkezete | Eredményként kapott cél |
|:--- |:--- |:--- |:--- |
| igaz |megőrzési hierarchia | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5 | A Folder1 célmappa ugyanazzal a szerkezettel jön létre, mint a forrás:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5. |
| igaz |összeolvasztási hierarchia | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5 | A célmappa1 a következő struktúrával jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;fájl automatikusan létrehozott neve1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott név a File2 fájlhoz<br/>&nbsp;&nbsp;&nbsp;&nbsp;fájl 3 automatikusan létrehozott neve<br/>&nbsp;&nbsp;&nbsp;&nbsp;fájl4 automatikusan létrehozott neve<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott név a File5 fájlhoz |
| igaz |mergeFiles | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5 | A célmappa1 a következő struktúrával jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File 5 tartalmát egyesítik egy fájlautomatikusan generált fájl neve |
| hamis |megőrzési hierarchia | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5 | A Mappa1 mappa mappa a következő struktúrával jön létre:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/><br/>A File3, File4 és File5 almappát a rendszer nem veszi fel. |
| hamis |összeolvasztási hierarchia | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5 | A Mappa1 mappa mappa a következő struktúrával jön létre:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;fájl automatikusan létrehozott neve1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott név a File2 fájlhoz<br/><br/>A File3, File4 és File5 almappát a rendszer nem veszi fel. |
| hamis |mergeFiles | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5 | A Mappa1 mappa mappa a következő struktúrával jön létre:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;A Fájl1 + File2 tartalom egyetlen, automatikusan generált fájlnévvel rendelkező fájlba egyesül. fájl automatikusan létrehozott neve1<br/><br/>A File3, File4 és File5 almappát a rendszer nem veszi fel. |

## <a name="lookup-activity-properties"></a>A keresgaszíntevékenység tulajdonságai

A tulajdonságokrészleteinek megismeréséhez ellenőrizze a [Kereskövetési tevékenységet.](control-flow-lookup-activity.md)

## <a name="getmetadata-activity-properties"></a>GetMetadata tevékenység tulajdonságai

A tulajdonságokrészleteinek megismeréséhez jelölje be a [GetMetadata tevékenység](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Tevékenységtulajdonságok törlése

A tulajdonságok részleteinek megismeréséhez jelölje be a [Tevékenység törlése jelölőnégyzetet.](delete-activity.md)

## <a name="legacy-models"></a>Örökölt modellek

>[!NOTE]
>A következő modellek továbbra is támogatottak, mint a visszamenőleges kompatibilitás. Javasoljuk, hogy a fenti szakaszokban említett új modellt használja, és az ADF szerzői felhasználói felülete áttért az új modell létrehozására.

### <a name="legacy-dataset-model"></a>Örökölt adatkészletmodell

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet típustulajdonságát a következőre kell állítani: **FileShare** |Igen |
| folderPath | A mappa elérési útja. A helyettesítő karakterek szűrője támogatott, `*` az engedélyezett helyettesítő karakterek `?` a következők: (nulla vagy több karakternek felel meg) és (nulla vagy egy karakternek felel meg); akkor `^` menekülhet, ha a mappa tényleges neve helyettesítő karaktert használ, vagy ha ez az escape karakter van benne. <br/><br/>Példák: rootfolder/subfolder/, további példák a [Minta csatolt szolgáltatás és adatkészlet definíciók](#sample-linked-service-and-dataset-definitions) és [mappa- és fájlszűrő példák](#folder-and-file-filter-examples). |Nem |
| fileName | A megadott "folderPath" alatt található fájl(ok) **neve vagy helyettesítő karaktere.** Ha nem ad meg értéket ehhez a tulajdonsághoz, az adatkészlet a mappában lévő összes fájlra mutat. <br/><br/>Szűrő esetén az engedélyezett `*` helyettesítő karakterek a következők: `?` (nulla vagy több karakternek felel meg) és (nulla vagy egy karakternek felel meg).<br/>- 1. példa:`"fileName": "*.csv"`<br/>- 2. példa:`"fileName": "???20180427.txt"`<br/>Menekülésre használható, `^` ha a tényleges fájlnévben helyettesítő karakter szerepel, vagy ez az escape karakter van benne.<br/><br/>Ha a fájlnév nincs megadva egy kimeneti adatkészlethez, és a **preserveHierarchy** nincs megadva a tevékenységfogadóban, a másolási tevékenység automatikusan létrehozza a fájlnevet a következő mintával: "*Data.[ tevékenység futtatási azonosító azonosítója GUID]. [GUID, ha összeolvasztási hierarchia]. [formátum, ha konfigurálva van]. [tömörítés, ha be van állítva]*", például "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz"; ha táblázatos forrásból a táblanevet használja a lekérdezés helyett, a névminta a "*[táblanév].[ formátum]. [tömörítés, ha be van állítva]*", pl. "MyTable.csv". |Nem |
| modifiedDatetimeStart | A fájlok szűrője az attribútum alapján: Utolsó módosítás. A fájlok akkor lesznek kiválasztva, ha az `modifiedDatetimeStart` `modifiedDatetimeEnd`utolsó módosítási idejük a és a közötti időtartományon belül van. Az idő az UTC időzónára a "2018-12-01T05:00:00Z" formátumban kerül alkalmazásra. <br/><br/> Ne feledje, hogy az adatok mozgásának általános teljesítményét befolyásolja, ha engedélyezi ezt a beállítást, ha nagy mennyiségű fájlból szeretne fájlszűrőt végezni. <br/><br/> A tulajdonságok null értékűek lehetnek, ami azt jelenti, hogy az adatkészletre nem lesz fájlattribútum-szűrő alkalmazva.  Ha `modifiedDatetimeStart` datetime értékkel rendelkezik, de `modifiedDatetimeEnd` NULL, az azt jelenti, hogy azokat a fájlokat, amelyek utolsó módosított attribútuma nagyobb vagy egyenlő, mint a datetime érték, ki lesznek jelölve.  Ha `modifiedDatetimeEnd` datetime értékkel rendelkezik, de `modifiedDatetimeStart` NULL, az azt jelenti, hogy azutolsó módosított attribútum kisebb, mint a datetime érték, ki lesznek jelölve.| Nem |
| modifiedDatetimeEnd | A fájlok szűrője az attribútum alapján: Utolsó módosítás. A fájlok akkor lesznek kiválasztva, ha az `modifiedDatetimeStart` `modifiedDatetimeEnd`utolsó módosítási idejük a és a közötti időtartományon belül van. Az idő az UTC időzónára a "2018-12-01T05:00:00Z" formátumban kerül alkalmazásra. <br/><br/> Ne feledje, hogy az adatok mozgásának általános teljesítményét befolyásolja, ha engedélyezi ezt a beállítást, ha nagy mennyiségű fájlból szeretne fájlszűrőt végezni. <br/><br/> A tulajdonságok null értékűek lehetnek, ami azt jelenti, hogy az adatkészletre nem lesz fájlattribútum-szűrő alkalmazva.  Ha `modifiedDatetimeStart` datetime értékkel rendelkezik, de `modifiedDatetimeEnd` NULL, az azt jelenti, hogy azokat a fájlokat, amelyek utolsó módosított attribútuma nagyobb vagy egyenlő, mint a datetime érték, ki lesznek jelölve.  Ha `modifiedDatetimeEnd` datetime értékkel rendelkezik, de `modifiedDatetimeStart` NULL, az azt jelenti, hogy azutolsó módosított attribútum kisebb, mint a datetime érték, ki lesznek jelölve.| Nem |
| Formátum | Ha a fájlokat fájlalapú tárolók között (bináris másolat) szeretné **másolni,** hagyja ki a formátum szakaszt a bemeneti és a kimeneti adatkészlet-definíciókban is.<br/><br/>Ha meghatározott formátumú fájlokat szeretne elemezni vagy létrehozni, a következő fájlformátum-típusok támogatottak: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Állítsa a formátum alatti **típustulajdonságot** ezen értékek egyikére. További információt a [Szövegformátum,](supported-file-formats-and-compression-codecs-legacy.md#text-format) [a Json formátum,](supported-file-formats-and-compression-codecs-legacy.md#json-format) [az Avro formátum,](supported-file-formats-and-compression-codecs-legacy.md#avro-format) [az Orc Formátum](supported-file-formats-and-compression-codecs-legacy.md#orc-format)és a [Parkettaformátum](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) című szakaszban talál. |Nem (csak bináris másolási forgatókönyv esetén) |
| tömörítés | Adja meg az adatok tömörítésének típusát és szintjét. További információt a [Támogatott fájlformátumok és tömörítési kodekek című témakörben talál.](supported-file-formats-and-compression-codecs-legacy.md#compression-support)<br/>A támogatott típusok a következők: **GZip**, **Deflate**, **BZip2**és **ZipDeflate**.<br/>Támogatott szintek: **Optimális** és **leggyorsabb**. |Nem |

>[!TIP]
>A mappa összes fájljának másolásához adja meg a csak **mappaPath** parancsot.<br>Egyetlen fájl adott nevű másolásához adja meg a **folderPath** mappát tartalmazó részét, **a fájlnév** pedig a fájlnevet.<br>Ha egy mappába szeretné másolni a fájlok egy részét, adja meg a **folderPath** mapparészlel és **a fileName** helyettesítő karakterrel című elemét.

>[!NOTE]
>Ha a "fileFilter" tulajdonságot használta a fájlszűrőhöz, akkor is támogatott, miközben a "fileName" új szűrőképességének használatát javasolja.

**Példa:**

```json
{
    "name": "FileSystemDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<file system linked service name>",
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

### <a name="legacy-copy-activity-source-model"></a>Örökölt másolási tevékenység forrásmodellje

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának típustulajdonságát a következőre kell állítani: **FileSystemSource** |Igen |
| Rekurzív | Azt jelzi, hogy az adatok olvasása rekurzív anameddig az almappákból vagy csak a megadott mappából történik. Megjegyzés: Ha a rekurzív érték igaz, a fogadó pedig fájlalapú tároló, az üres mappa/almappa nem lesz másolva/hozva a fogadóban.<br/>Az engedélyezett értékek a következők: **true** (alapértelmezett), **hamis** | Nem |
| maxConcurrentConnections | A tárolóhoz egyidejűleg csatlakozó kapcsolatok száma. Csak akkor adja meg, ha korlátozni szeretné az egyidejű kapcsolatot az adattárhoz. | Nem |

**Példa:**

```json
"activities":[
    {
        "name": "CopyFromFileSystem",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<file system input dataset name>",
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
                "type": "FileSystemSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="legacy-copy-activity-sink-model"></a>Örökölt másolási tevékenység fogadójának modellje

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadójának típustulajdonságát a következőre kell állítani: **FileSystemSink** |Igen |
| copyBehavior (Másként) | Azt a másolási viselkedést határozza meg, amikor a forrás fájlalapú adattárból származó fájlok.<br/><br/>Az engedélyezett értékek a következők:<br/><b>- MegőrzéseHierarchy (alapértelmezett)</b>: megőrzi a fájlhierarchiát a célmappában. A forrásfájl forrásmappához viszonyított elérési útja megegyezik a célfájl célmappához való relatív elérési útjával.<br/><b>- Ahierarcha összeolvasztása</b>: a forrásmappából származó összes fájl a célmappa első szintjén található. A célfájlok automatikusan létrehozott névvel rendelkeznek. <br/><b>- MergeFiles</b>: egyesíti az összes fájlt a forrás mappát egy fájlt. Az egyesítés során nem történik rekorddeduplikáció. Ha a fájlnév meg van adva, az egyesített fájlnév lesz a megadott név; ellenkező esetben lenne automatikusan generált fájlnevét. | Nem |
| maxConcurrentConnections | A tárolóhoz egyidejűleg csatlakozó kapcsolatok száma. Csak akkor adja meg, ha korlátozni szeretné az egyidejű kapcsolatot az adattárhoz. | Nem |

**Példa:**

```json
"activities":[
    {
        "name": "CopyToFileSystem",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<file system output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "FileSystemSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

## <a name="next-steps"></a>További lépések
A forrásként támogatott és fogadóként az Azure Data Factory másolási tevékenysége által támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats)című témakörben tetszhet.

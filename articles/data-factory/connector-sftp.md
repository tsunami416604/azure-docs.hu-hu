---
title: Adatok másolása SFTP-kiszolgálóról és -ba
description: Ismerje meg, hogyan másolhat adatokat az SFTP-kiszolgálóról és az SFTP-kiszolgálóra az Azure Data Factory használatával.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/02/2020
ms.openlocfilehash: e6d29f73716b04699e0cd250396df7f7d744d4c4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415248"
---
# <a name="copy-data-from-and-to-sftp-server-using-azure-data-factory"></a>Adatok másolása SFTP-kiszolgálóról és sftp-kiszolgálóra az Azure Data Factory használatával

> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-sftp-connector.md)
> * [Jelenlegi verzió](connector-sftp.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk az SFTP-kiszolgálóról és az SFTP-kiszolgálóra történő másolást ismerteti. Az Azure Data Factory ról a [bevezető cikkben](introduction.md)olvashat.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az SFTP-összekötő a következő tevékenységek esetén támogatott:

- [Tevékenység másolása](copy-activity-overview.md) [támogatott forrás/fogadó mátrixcal](copy-activity-overview.md)
- [Keress tevékenységet](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)
- [Tevékenység törlése](delete-activity.md)

Pontosabban ez az SFTP-összekötő a következőket támogatja:

- Fájlok másolása az SFTP-ből/-be **basic** vagy **SshPublicKey** hitelesítéssel.
- Fájlok másolása a támogatott fájlformátumok és [tömörítési kodekek](supported-file-formats-and-compression-codecs.md)segítségével.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok az SFTP-re jellemző Data Factory-entitások definiálására használt tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

Az SFTP-csatolt szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A típustulajdonságnak **sftp**. |Igen |
| gazda | Az SFTP-kiszolgáló neve vagy IP-címe. |Igen |
| port | Az a port, amelyen az SFTP-kiszolgáló figyel.<br/>Az engedélyezett értékek a következők: egész szám, az alapértelmezett érték **22**. |Nem |
| skipHostKeyValidation | Adja meg, hogy kihagyja-e az állomáskulcs-érvényesítést.<br/>Az engedélyezett értékek a következők: **true**, **false** (default).  | Nem |
| hostKeyFingerprint | Adja meg az állomáskulcs ujjlenyomatát. | Igen, ha a "skipHostKeyValidation" értéke hamis.  |
| authenticationType | Adja meg a hitelesítés típusát.<br/>Az engedélyezett értékek a következők: **Alapszintű**, **SshPublicKey**. Lásd: [Az alapszintű hitelesítés és](#using-basic-authentication) az [SSH nyilvános kulcsú hitelesítésszakaszainak használata](#using-ssh-public-key-authentication) további tulajdonságokon, illetve JSON-mintákon. |Igen |
| connectVia | Az adattárhoz való csatlakozáshoz használandó [integrációs futásidő.](concepts-integration-runtime.md) További információ az [Előfeltételek](#prerequisites) szakaszból. Ha nincs megadva, az alapértelmezett Azure-integrációs runtime-ot használja. |Nem |

### <a name="using-basic-authentication"></a>Egyszerű hitelesítés használata

Az alapfokú hitelesítés használatához állítsa a "authenticationType" tulajdonságot **Alapszintűre,** és adja meg a következő tulajdonságokat az utolsó szakaszban bevezetett SFTP-összekötő általános tulajdonságai mellett:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| userName (Felhasználónév) | Az SFTP-kiszolgálóhoz hozzáféréssel rendelkező felhasználó. |Igen |
| jelszó | A felhasználó jelszava (felhasználónév). Jelölje meg ezt a mezőt SecureStringként a Data Factory biztonságos tárolásához, vagy [hivatkozzon az Azure Key Vaultban tárolt titkos fájlokra.](store-credentials-in-key-vault.md) | Igen |

**Példa:**

```json
{
    "name": "SftpLinkedService",
    "type": "linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "authenticationType": "Basic",
            "userName": "<username>",
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

### <a name="using-ssh-public-key-authentication"></a>Az SSH nyilvános kulcsos hitelesítésének használata

Az SSH nyilvános kulcsú hitelesítésének használatához állítsa be a "authenticationType" tulajdonságot **SshPublicKey**néven, és adja meg a következő tulajdonságokat az utolsó szakaszban bevezetett SFTP-összekötő általános tulajdonságai mellett:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| userName (Felhasználónév) | Az SFTP-kiszolgálóhoz hozzáféréssel rendelkező felhasználó |Igen |
| privateKeyPath | Adja meg az integrációs futásidejű személyes kulcsfájl abszolút elérési útját. Csak akkor érvényes, ha az integrációs futásidejű saját üzemeltetésű típusa a "connectVia" mezőben van megadva. | Adja meg `privateKeyPath` `privateKeyContent`a vagy a .  |
| privateKeyContent | Base64 kódolt SSH titkos kulcs tartalma. Az SSH személyes kulcsának OpenSSH formátumúnak kell lennie. Jelölje meg ezt a mezőt SecureStringként a Data Factory biztonságos tárolásához, vagy [hivatkozzon az Azure Key Vaultban tárolt titkos fájlokra.](store-credentials-in-key-vault.md) | Adja meg `privateKeyPath` `privateKeyContent`a vagy a . |
| Jelszót | Adja meg a személyes kulcs visszafejtéséhez használt jelszót/jelszót, ha a kulcsfájlt jelmondat védi. Jelölje meg ezt a mezőt SecureStringként a Data Factory biztonságos tárolásához, vagy [hivatkozzon az Azure Key Vaultban tárolt titkos fájlokra.](store-credentials-in-key-vault.md) | Igen, ha a személyes kulcsfájlt jelmondat védi. |

> [!NOTE]
> Az SFTP csatlakozó támogatja az RSA/DSA OpenSSH kulcsot. Győződjön meg arról, hogy a kulcsfájl tartalma a következővel kezdődik: "-----BEGIN [RSA/DSA] PRIVATE KEY-----". Ha a személyes kulcsfájl ppk formátumú fájl, kérjük, a Putty eszközzel konvertáljon .ppk formátumból OpenSSH formátumba. 

**1. példa: SshPublicKey hitelesítés személyes kulcsfájlhasználatávalPath**

```json
{
    "name": "SftpLinkedService",
    "type": "Linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": true,
            "authenticationType": "SshPublicKey",
            "userName": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": {
                "type": "SecureString",
                "value": "<pass phrase>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**2. példa: SshPublicKey hitelesítés titkoskulcs-tartalom használatával**

```json
{
    "name": "SftpLinkedService",
    "type": "Linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": true,
            "authenticationType": "SshPublicKey",
            "userName": "<username>",
            "privateKeyContent": {
                "type": "SecureString",
                "value": "<base64 string of the private key content>"
            },
            "passPhrase": {
                "type": "SecureString",
                "value": "<pass phrase>"
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

A következő tulajdonságok támogatottak `location` az SFTP-hez a formátumalapú adatkészlet beállításai között:

| Tulajdonság   | Leírás                                                  | Kötelező |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Az adatkészlet `location` típustulajdonságát **SftpLocation (SftpLocation )** tulajdonságra kell állítani. | Igen      |
| folderPath | A mappa elérési útja. Ha helyettesítő karaktert szeretne használni a mappa szűréséhez, hagyja ki ezt a beállítást, és adja meg a tevékenységforrás beállításaiban. | Nem       |
| fileName   | A fájlnév a megadott mappaPath. Ha helyettesítő karaktert szeretne használni a fájlok szűréséhez, hagyja ki ezt a beállítást, és adja meg a tevékenységforrás beállításaiban. | Nem       |

**Példa:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<SFTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "SftpLocation",
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

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok](concepts-pipelines-activities.md) című cikkben olvashat. Ez a szakasz az SFTP-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="sftp-as-source"></a>SFTP mint forrás

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

A következő tulajdonságok támogatottak `storeSettings` az SFTP-hez a formátumalapú másolási forrás beállításai között:

| Tulajdonság                 | Leírás                                                  | Kötelező                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | A típustulajdonságot `storeSettings` **SftpReadSettings (SftpReadSettings )** tulajdonságnak kell beállítani. | Igen                                           |
| Rekurzív                | Azt jelzi, hogy az adatok olvasása rekurzív anameddig az almappákból vagy csak a megadott mappából történik. Ne feledje, hogy ha a rekurzív érték igaz, és a fogadó fájlalapú tároló, a program nem másolja vagy hozza létre az üres mappát vagy almappát a fogadóban. Az engedélyezett értékek **igazak** (alapértelmezettek) és **hamisak.** | Nem                                            |
| helyettesítő mappaelérési út       | A forrásmappák szűréséhez helyettesítő karakterekkel ellátott mappaelérési út. <br>Az engedélyezett helyettesítő `*` karakterek a következők: `?` (nulla vagy több karakternek felel meg) és (nulla vagy egy karakternek felel meg); akkor `^` menekülhet, ha a mappa tényleges neve helyettesítő karaktert használ, vagy ha ez az escape karakter van benne. <br>További példák a [Mappa- és fájlszűrő-példákban](#folder-and-file-filter-examples)találhatók. | Nem                                            |
| helyettesítő fájlneve         | A forrásfájlok szűréséhez helyettesítő karakterekkel rendelkező fájlnév a kívánt/helyettesítő folderFolderPath mappában. <br>Az engedélyezett helyettesítő `*` karakterek a következők: `?` (nulla vagy több karakternek felel meg) és (nulla vagy egy karakternek felel meg); akkor `^` menekülhet, ha a mappa tényleges neve helyettesítő karaktert használ, vagy ha ez az escape karakter van benne.  További példák a [Mappa- és fájlszűrő-példákban](#folder-and-file-filter-examples)találhatók. | Igen, `fileName` ha nincs megadva az adatkészletben |
| modifiedDatetimeStart    | A fájlok szűrője az attribútum alapján: Utolsó módosítás. A fájlok akkor lesznek kiválasztva, ha az `modifiedDatetimeStart` `modifiedDatetimeEnd`utolsó módosítási idejük a és a közötti időtartományon belül van. Az idő az UTC időzónára a "2018-12-01T05:00:00Z" formátumban kerül alkalmazásra. <br> A tulajdonságok null értékűek lehetnek, ami azt jelenti, hogy az adatkészletre nem lesz fájlattribútum-szűrő alkalmazva.  Ha `modifiedDatetimeStart` datetime értékkel rendelkezik, de `modifiedDatetimeEnd` NULL, az azt jelenti, hogy azokat a fájlokat, amelyek utolsó módosított attribútuma nagyobb vagy egyenlő, mint a datetime érték, ki lesznek jelölve.  Ha `modifiedDatetimeEnd` datetime értékkel rendelkezik, de `modifiedDatetimeStart` NULL, az azt jelenti, hogy azutolsó módosított attribútum kisebb, mint a datetime érték, ki lesznek jelölve. | Nem                                            |
| modifiedDatetimeEnd      | Ugyanaz, mint fent.                                               | Nem                                            |
| maxConcurrentConnections | A tárolóhoz egyidejűleg csatlakozó kapcsolatok száma. Csak akkor adja meg, ha korlátozni szeretné az egyidejű kapcsolatot az adattárhoz. | Nem                                            |

**Példa:**

```json
"activities":[
    {
        "name": "CopyFromSFTP",
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
                    "type": "SftpReadSettings",
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

### <a name="sftp-as-sink"></a>SFTP mint fogadó

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Az SFTP a következő `storeSettings` tulajdonságokat támogatja a formátumalapú másolási fogadó beállításai között:

| Tulajdonság                 | Leírás                                                  | Kötelező |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | A típustulajdonságot `storeSettings` **SftpWriteSettings (SftpWriteSettings )** tulajdonságra kell állítani. | Igen      |
| copyBehavior (Másként)             | Azt a másolási viselkedést határozza meg, amikor a forrás fájlalapú adattárból származó fájlok.<br/><br/>Az engedélyezett értékek a következők:<br/><b>- MegőrzéseHierarchy (alapértelmezett)</b>: Megőrzi a fájlhierarchiát a célmappában. A forrásfájl forrásmappához viszonyított elérési útja megegyezik a célfájl nak a célmappához viszonyított elérési útvonalával.<br/><b>- Ahierarcha összeolvasztása</b>: A forrásmappából származó összes fájl a célmappa első szintjén található. A célfájlok nak automatikusan generált neve van. <br/><b>- MergeFiles</b>: Egyesíti az összes fájlt a forrás mappát egy fájlt. Ha a fájlnév meg van adva, az egyesített fájlnév a megadott név. Ellenkező esetben ez egy automatikusan létrehozott fájlnév. | Nem       |
| maxConcurrentConnections | Az adattárhoz egyidejűleg csatlakozó kapcsolatok száma. Csak akkor adja meg, ha korlátozni szeretné az egyidejű kapcsolatot az adattárhoz. | Nem       |
| useTempFileRename | Adja meg, hogy ideiglenes fájlba (fájlokba) szeretne-e feltölteni és átnevezni, vagy közvetlenül írni a célmappába/fájlhelyére. Alapértelmezés szerint az ADF először ír az ideiglenes fájl(ok)ba, majd a feltöltés befejezésekor átnevezi a fájlt, hogy 1) elkerülje az ütközéses írást, ami sérült fájlt eredményez, ha más folyamat írja ugyanazt a fájlt, és 2) biztosítja, hogy a fájl eredeti verziója létezik a teljes átvitel során. Ha az SFTP-kiszolgáló nem támogatja az átnevezési műveletet, tiltsa le ezt a beállítást, és győződjön meg arról, hogy nincs egyidejű írás a célfájlba. Lásd a hibaelhárítási tippet a táblázat alatt. | Nem. Az alapértelmezett érték igaz. |
| operationTimeout | Az SFTP-kiszolgálóra küldött minden írási kérelem előtti várakozási idő idő. Az alapértelmezett érték 60 min (01:00:00).|Nem |

>[!TIP]
>Ha bejön hiba "UserErrorSftpPathNotFound", "UserErrorSftpPermissionDenied" vagy "SftpOperationFail" amikor adatokat ír az SFTP, és az SFTP felhasználó használata nem rendelkezik a megfelelő engedéllyel,`useTempFileRename`ellenőrizze, hogy az SFTP szerver támogatja a fájl átnevezési művelet - ha nem, tiltsa le a "Feltöltés ideiglenes fájllal" ( ) opciót, és próbálja újra. Tudjon meg többet erről a tulajdonságról a fenti táblázatból. Ha önkiszolgáló integrációs futásidejű másolást használ, győződjön meg arról, hogy a 4.6-os vagy újabb verziót használja.

**Példa:**

```json
"activities":[
    {
        "name": "CopyToSFTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
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
                "type": "<source type>"
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings":{
                    "type": "SftpWriteSettings",
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
| folderPath | A mappa elérési útja. A helyettesítő karakterek szűrője támogatott, `*` az engedélyezett helyettesítő karakterek `?` a következők: (nulla vagy több karakternek felel meg) és (nulla vagy egy karakternek felel meg); akkor `^` menekülhet, ha a tényleges fájlnévben helyettesítő karakter szerepel, vagy ez az escape karakter van benne. <br/><br/>Példák: rootfolder/subfolder/, további példák a [Mappa- és fájlszűrőben.](#folder-and-file-filter-examples) |Igen |
| fileName |  A megadott "folderPath" alatt található fájl(ok) **neve vagy helyettesítő karaktere.** Ha nem ad meg értéket ehhez a tulajdonsághoz, az adatkészlet a mappában lévő összes fájlra mutat. <br/><br/>Szűrő esetén az engedélyezett `*` helyettesítő karakterek a következők: `?` (nulla vagy több karakternek felel meg) és (nulla vagy egy karakternek felel meg).<br/>- 1. példa:`"fileName": "*.csv"`<br/>- 2. példa:`"fileName": "???20180427.txt"`<br/>Menekülésre használható, `^` ha a tényleges mappanévben helyettesítő karakter szerepel, vagy ez az escape karakter van benne. |Nem |
| modifiedDatetimeStart | A fájlok szűrője az attribútum alapján: Utolsó módosítás. A fájlok akkor lesznek kiválasztva, ha az `modifiedDatetimeStart` `modifiedDatetimeEnd`utolsó módosítási idejük a és a közötti időtartományon belül van. Az idő az UTC időzónára a "2018-12-01T05:00:00Z" formátumban kerül alkalmazásra. <br/><br/> Ne feledje, hogy az adatok mozgásának általános teljesítményét befolyásolja, ha engedélyezi ezt a beállítást, ha nagy mennyiségű fájlból szeretne fájlszűrőt végezni. <br/><br/> A tulajdonságok null értékűek lehetnek, ami azt jelenti, hogy az adatkészletre nem lesz fájlattribútum-szűrő alkalmazva.  Ha `modifiedDatetimeStart` datetime értékkel rendelkezik, de `modifiedDatetimeEnd` NULL, az azt jelenti, hogy azokat a fájlokat, amelyek utolsó módosított attribútuma nagyobb vagy egyenlő, mint a datetime érték, ki lesznek jelölve.  Ha `modifiedDatetimeEnd` datetime értékkel rendelkezik, de `modifiedDatetimeStart` NULL, az azt jelenti, hogy azutolsó módosított attribútum kisebb, mint a datetime érték, ki lesznek jelölve.| Nem |
| modifiedDatetimeEnd | A fájlok szűrője az attribútum alapján: Utolsó módosítás. A fájlok akkor lesznek kiválasztva, ha az `modifiedDatetimeStart` `modifiedDatetimeEnd`utolsó módosítási idejük a és a közötti időtartományon belül van. Az idő az UTC időzónára a "2018-12-01T05:00:00Z" formátumban kerül alkalmazásra. <br/><br/> Ne feledje, hogy az adatok mozgásának általános teljesítményét befolyásolja, ha engedélyezi ezt a beállítást, ha nagy mennyiségű fájlból szeretne fájlszűrőt végezni. <br/><br/> A tulajdonságok null értékűek lehetnek, ami azt jelenti, hogy az adatkészletre nem lesz fájlattribútum-szűrő alkalmazva.  Ha `modifiedDatetimeStart` datetime értékkel rendelkezik, de `modifiedDatetimeEnd` NULL, az azt jelenti, hogy azokat a fájlokat, amelyek utolsó módosított attribútuma nagyobb vagy egyenlő, mint a datetime érték, ki lesznek jelölve.  Ha `modifiedDatetimeEnd` datetime értékkel rendelkezik, de `modifiedDatetimeStart` NULL, az azt jelenti, hogy azutolsó módosított attribútum kisebb, mint a datetime érték, ki lesznek jelölve.| Nem |
| Formátum | Ha a fájlokat fájlalapú tárolók között (bináris másolat) szeretné **másolni,** hagyja ki a formátum szakaszt a bemeneti és a kimeneti adatkészlet-definíciókban is.<br/><br/>Ha adott formátumú fájlokat szeretne elemezni, a következő fájlformátum-típusok támogatottak: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Állítsa a formátum alatti **típustulajdonságot** ezen értékek egyikére. További információt a [Szövegformátum,](supported-file-formats-and-compression-codecs-legacy.md#text-format) [a Json formátum,](supported-file-formats-and-compression-codecs-legacy.md#json-format) [az Avro formátum,](supported-file-formats-and-compression-codecs-legacy.md#avro-format) [az Orc Formátum](supported-file-formats-and-compression-codecs-legacy.md#orc-format)és a [Parkettaformátum](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) című szakaszban talál. |Nem (csak bináris másolási forgatókönyv esetén) |
| tömörítés | Adja meg az adatok tömörítésének típusát és szintjét. További információt a [Támogatott fájlformátumok és tömörítési kodekek című témakörben talál.](supported-file-formats-and-compression-codecs-legacy.md#compression-support)<br/>A támogatott típusok a következők: **GZip**, **Deflate**, **BZip2**és **ZipDeflate**.<br/>Támogatott szintek: **Optimális** és **leggyorsabb**. |Nem |

>[!TIP]
>A mappa összes fájljának másolásához adja meg a csak **mappaPath** parancsot.<br>Egyetlen fájl adott nevű másolásához adja meg a **folderPath** mappát tartalmazó részét, **a fájlnév** pedig a fájlnevet.<br>Ha egy mappába szeretné másolni a fájlok egy részét, adja meg a **folderPath** mapparészlel és **a fileName** helyettesítő karakterrel című elemét.

>[!NOTE]
>Ha a "fileFilter" tulajdonságot használta a fájlszűrőhöz, akkor is támogatott, miközben a "fileName" új szűrőképességének használatát javasolja.

**Példa:**

```json
{
    "name": "SFTPDataset",
    "type": "Datasets",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<SFTP linked service name>",
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
| Rekurzív | Azt jelzi, hogy az adatok olvasása rekurzív an- vagy csak a megadott mappából történik. Megjegyzés: Ha a rekurzív érték igaz, a fogadó pedig fájlalapú tároló, az üres mappa/almappa nem lesz másolva/hozva a fogadóban.<br/>Az engedélyezett értékek a következők: **true** (alapértelmezett), **hamis** | Nem |
| maxConcurrentConnections | A tárolóhoz egyidejűleg csatlakozó kapcsolatok száma. Csak akkor adja meg, ha korlátozni szeretné az egyidejű kapcsolatot az adattárhoz. | Nem |

**Példa:**

```json
"activities":[
    {
        "name": "CopyFromSFTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SFTP input dataset name>",
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

## <a name="next-steps"></a>További lépések
A forrásként támogatott és fogadóként az Azure Data Factory másolási tevékenysége által támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats)című témakörben tetszhet.

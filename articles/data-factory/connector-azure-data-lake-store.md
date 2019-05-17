---
title: Adatok másolása, illetve az Azure Data Lake Storage Gen1 a Data Factory használatával |} A Microsoft Docs
description: Ismerje meg, hogyan másolhat adatokat az Azure Data Lake Store támogatott forrásadattárakból, vagy a Data Lake Store támogatott fogadó-áruházak, Data Factory használatával.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: jingwang
ms.openlocfilehash: 94fb3fbe9def034dc36467d219cb3e8b45c40dad
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/13/2019
ms.locfileid: "65560611"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-by-using-azure-data-factory"></a>Adatok másolása, vagy az Azure Data Lake Storage Gen1 Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](v1/data-factory-azure-datalake-connector.md)
> * [Aktuális verzió](connector-azure-data-lake-store.md)

Ez a cikk ismerteti, hogyan másolhat adatokat, és az Azure Data Lake Storage Gen1 (ADLS Gen1). Azure Data Factory szolgáltatásról, olvassa el a [bevezető cikk](introduction.md).

## <a name="supported-capabilities"></a>Támogatott képességek

Az Azure Data Lake Storage Gen1 összekötő támogatott a következő tevékenységeket:

- [Másolási tevékenység](copy-activity-overview.md) a [támogatott forrás/fogadó mátrix](copy-activity-overview.md)
- [Az adatfolyam-leképezés](concepts-data-flow-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)

Pontosabban az összekötő támogatja:

- Fájlok másolása az alábbi hitelesítési módszerek egyikének használatával: **szolgáltatásnév** vagy **felügyelt identitások az Azure-erőforrások**.
- Másolja a fájlokat,-van, vagy elemzés vagy -fájlok létrehozása a [támogatott fájlformátumok és tömörítési kodek](supported-file-formats-and-compression-codecs.md).

> [!IMPORTANT]
> Ha a saját üzemeltetésű integrációs modul adatokat másol, állítsa be a vállalati tűzfalon a kimenő forgalom engedélyezésére `<ADLS account name>.azuredatalakestore.net` és `login.microsoftonline.com/<tenant>/oauth2/token` 443-as porton. Az utóbbi az Azure biztonsági jogkivonat-szolgáltatás, amely az integrációs modul és a hozzáférési jogkivonat beszerzése az közötti kommunikációhoz szükséges.

## <a name="get-started"></a>Bevezetés

> [!TIP]
> Az Azure Data Lake Store-összekötő használatával, olvassa [adatok betöltése az Azure Data Lake Store](load-azure-data-lake-store.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Az alábbi szakaszok nyújtanak, amelyek meghatározzák az adott Data Factory-entitások Azure Data Lake Store-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A következő tulajdonságok támogatottak a az Azure Data Lake Store-beli társított szolgáltatást:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A `type` tulajdonságot állítsa **AzureDataLakeStore**. | Igen |
| dataLakeStoreUri | Az Azure Data Lake Store-fiókkal kapcsolatos információk. Ezt az információt a következő formátumok egyikét veheti fel: `https://[accountname].azuredatalakestore.net/webhdfs/v1` vagy `adl://[accountname].azuredatalakestore.net/`. | Igen |
| subscriptionId | Az Azure-előfizetés azonosítója, amelyhez a Data Lake Store-fiókhoz tartozik. | A fogadó megadása kötelező |
| resourceGroupName | A Azure erőforráscsoport-név, amely a Data Lake Store-fiókhoz tartozik. | A fogadó megadása kötelező |
| connectVia | A [integrációs modul](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. Azure integrációs modul használatára, de a saját üzemeltetésű integrációs modult, (ha az adattár egy magánhálózaton található). Ha ez a tulajdonság nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

### <a name="use-service-principal-authentication"></a>Használja az egyszerű szolgáltatásnév hitelesítése

Egyszerű szolgáltatásnév hitelesítése használandó entitás alkalmazás regisztrálása az Azure Active Directoryban, és hozzáférést biztosítani, Data Lake Store. Részletes lépéseiért lásd: [szolgáltatások közötti hitelesítés](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Jegyezze fel a következő értékeket, mert a társított szolgáltatás definiálásához használja:

- Alkalmazásazonosító
- Alkalmazáskulcs
- Bérlőazonosító

>[!IMPORTANT]
> Győződjön meg arról, hogy engedélyeket a szolgáltatás egyszerű megfelelő a Data Lake Store:
>- **Forrásként**: A **adatkezelő** > **hozzáférés**, adjon meg legalább **Olvasás + végrehajtás** listázása, és másolja a fájlokat a mappákban és almappáiban engedéllyel. Vagy biztosíthat **olvasási** engedéllyel egy fájl másolása. Dönthet úgy, hogy hozzá **ezt a mappát, és az összes gyermekre** a rekurzív, és adja hozzá **egy hozzáférési és alapértelmezett engedély bejegyzés**. Esetében nem követelmény a fiók fájlszintű hozzáférés-vezérlés (IAM).
>- **Fogadóként**: A **adatkezelő** > **hozzáférés**, adjon meg legalább **írás + végrehajtás** engedély szükséges gyermekelemek létrehozásához a mappában. Dönthet úgy, hogy hozzá **ezt a mappát, és az összes gyermekre** a rekurzív, és adja hozzá **egy hozzáférési és alapértelmezett engedély bejegyzés**. Ha Azure integrációs modul használatával másolása (forrás- és fogadóadattárként is vannak a felhőben), az IAM, biztosítson legalább a **olvasó** szerepkör annak érdekében, hogy észlelni az Data Lake Store-régiót, a Data Factory. Ha szeretné-e elkerülése érdekében explicit módon a IAM szerepkör [Azure integrációs modul létrehozása](create-azure-integration-runtime.md#create-azure-ir) helyét, a Data Lake Store. Például ha a Data Lake Store Nyugat-Európában, hozzon létre egy Azure-beli integrációs modul helyét "Nyugat-Európa" értékre. A Data Lake Store társított szolgáltatásban a következő példához hasonlóan rendelheti őket.

>[!NOTE]
>A lista kezdve a legfelső szintű mappák, be kell az egyszerű szolgáltatás nyújtott engedélye **gyökér szintű "Execute" engedéllyel rendelkező**. Ez igaz, ha használja a:
>- **Adatok másolása eszköz** a szerző másolási folyamat.
>- **Data Factory felhasználói felülete** tesztelheti a kapcsolatot, és lépjen a mappák létrehozása alatt.
>Ha rendelkezik a legfelső szintű engedély megadására szempont, kihagyhatja kapcsolat tesztelése és a bemeneti elérési út manuális létrehozása alatt. A másolási tevékenység továbbra is működni fog, amíg a egyszerű szolgáltatást kell másolni a fájlokat a megfelelő engedéllyel rendelkező kapnak.

A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| servicePrincipalId | Adja meg az alkalmazás ügyfél-azonosítót. | Igen |
| servicePrincipalKey | Adja meg az alkalmazáskulcsot. Jelölje meg a mező egy `SecureString` tárolja biztonságos helyen a Data Factory áttekintése, vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Igen |
| tenant | Adja meg a bérlő információkat (tartomány neve vagy a bérlő azonosítója) alatt az alkalmazás található. Kérheti a viszi az egérmutatót az Azure portal jobb felső sarkában. | Igen |

**Példa**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a> Felügyelt identitások Azure-erőforrások hitelesítéshez használandó

Adat-előállító társítható egy [-identitás az Azure-erőforrások](data-factory-service-identity.md), amely az adott adat-előállító jelöli. Használhatja a felügyelt identitást közvetlenül a Data Lake Store-hitelesítéshez, hasonlóan a saját egyszerű szolgáltatás használatával. A kijelölt factory eléréséhez és másolásához adatokhoz, illetve a Data Lake Store lehetővé teszi.

Azure-erőforrások hitelesítéshez használandó felügyelt identitások:

1. [A data factory által felügyelt azonosító adatok lekéréséhez](data-factory-service-identity.md#retrieve-managed-identity) másolása a "Szolgáltatásidentitás Alkalmazásazonosítója" az előállító együtt létrehozott értékét.
2. Hozzáférést biztosít a felügyelt identitás a Data Lake Store, végezhet el, az egyszerű szolgáltatást, ezek a Megjegyzések a következő azonos módon.

>[!IMPORTANT]
> Győződjön meg arról, hogy engedélyeket a data factory által felügyelt identitás megfelelő a Data Lake Store:
>- **Forrásként**: A **adatkezelő** > **hozzáférés**, adjon meg legalább **Olvasás + végrehajtás** listázása, és másolja a fájlokat a mappákban és almappáiban engedéllyel. Vagy biztosíthat **olvasási** engedéllyel egy fájl másolása. Dönthet úgy, hogy hozzá **ezt a mappát, és az összes gyermekre** a rekurzív, és adja hozzá **egy hozzáférési és alapértelmezett engedély bejegyzés**. Esetében nem követelmény a fiók fájlszintű hozzáférés-vezérlés (IAM).
>- **Fogadóként**: A **adatkezelő** > **hozzáférés**, adjon meg legalább **írás + végrehajtás** engedély szükséges gyermekelemek létrehozásához a mappában. Dönthet úgy, hogy hozzá **ezt a mappát, és az összes gyermekre** a rekurzív, és adja hozzá **egy hozzáférési és alapértelmezett engedély bejegyzés**. Ha Azure integrációs modul használatával másolása (forrás- és fogadóadattárként is vannak a felhőben), az IAM, biztosítson legalább a **olvasó** szerepkör annak érdekében, hogy észlelni az Data Lake Store-régiót, a Data Factory. Ha szeretné-e elkerülése érdekében explicit módon a IAM szerepkör [Azure integrációs modul létrehozása](create-azure-integration-runtime.md#create-azure-ir) helyét, a Data Lake Store. Rendelje azokat a Data Lake Store társított szolgáltatásban az alábbi példa szerint.

>[!NOTE]
>A lista kezdve a legfelső szintű mappák, be kell az engedély az engedély megadása a felügyelt identitás **gyökér szintű "Execute" engedéllyel rendelkező**. Ez igaz, ha használja a:
>- **Adatok másolása eszköz** a szerző másolási folyamat.
>- **Data Factory felhasználói felülete** tesztelheti a kapcsolatot, és lépjen a mappák létrehozása alatt.
>Ha rendelkezik a legfelső szintű engedély megadására szempont, kihagyhatja kapcsolat tesztelése és a bemeneti elérési út manuális létrehozása alatt. A másolási tevékenység továbbra is működni fog, amíg a felügyelt identitást kapnak a átmásolni a fájlokat a megfelelő engedéllyel rendelkező.

Az Azure Data Factoryben nem kell meghatároznia semmilyen tulajdonságot a Data Lake Store általános adatok mellett a társított szolgáltatás.

**Példa**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. 

- A **Parquet és tagolt szövegformátum**, tekintse meg [Parquet és elválasztójellel tagolt szöveges formátumban adatkészlet](#parquet-and-delimited-text-format-dataset) szakaszban.
- Más formátumú hasonló **ORC/Avro/JSON/bináris formátum**, tekintse meg [más formátumú adatkészlet](#other-format-dataset) szakaszban.

### <a name="parquet-and-delimited-text-format-dataset"></a>Parquet és elválasztójellel tagolt szöveges formátum adatkészlet

Az adatok másolásához, és onnan az ADLS-Gen1 **Parquet vagy tagolt szövegformátum**, tekintse meg [Parquet formátum](format-parquet.md) és [tagolt szövegformátum](format-delimited-text.md) cikk adatkészlet formátumú-alapú és támogatott beállítások. A következő tulajdonságok támogatottak ADLS Gen1 alatt `location` formátum-alapú adatkészlet beállításai:

| Tulajdonság   | Leírás                                                  | Szükséges |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | A type tulajdonság alatt `location` adatkészlet értékre kell állítani **AzureDataLakeStoreLocation**. | Igen      |
| folderPath | Mappa elérési útját. Ha azt szeretné, helyettesítő karaktert tartalmazó szűrő mappába használandó, hagyja ki ezt a beállítást, és adja meg a tevékenység – forrásbeállítások. | Nem       |
| fileName   | A fájlnév a megadott folderPath alatt. Ha szeretné használni a helyettesítő karaktert tartalmazó fájlok szűrésére, hagyja ki ezt a beállítást, és adja meg a tevékenység – forrásbeállítások. | Nem       |

> [!NOTE]
>
> **AzureDataLakeStoreFile** továbbra is támogatja a Parquet vagy szöveges formátum a következő szakaszban említett adatkészlet típusa – a Másolás/keresési/GetMetadata tevékenység az előző verziókkal való kompatibilitás, de nem működik az adatfolyam-leképezés. A jövőben az új modell használata javasolt, és ezek a típusok létrehozása a felhasználói felület szerzői ADF változott.

**Példa**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<ADLS Gen1 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureDataLakeStoreLocation",
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

### <a name="other-format-dataset"></a>Más formátumú adatkészlet

Az adatok másolásához, és onnan az ADLS-Gen1 **ORC/Avro/JSON/bináris formátum**, a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adatkészlet értékre kell állítani: **AzureDataLakeStoreFile** |Igen |
| folderPath | A Data Lake Store a mappa elérési útját. Ha nincs megadva, a legfelső szintű mutat. <br/><br/>Helyettesítő karaktert tartalmazó szűrő támogatott, a helyettesítő karakterek engedélyezve vannak: `*` (nulla vagy több olyan karakterre illeszkedik) és `?` (megegyezik a nulla vagy önálló karakter); használata `^` elkerülésére, ha a tényleges mappanevet helyettesítő elemet vagy a escape karaktere belül. <br/><br/>Példák: a gyökérmappa/almappa /, tekintse meg a további példákat [példák a mappához és fájlhoz szűrők](#folder-and-file-filter-examples). |Nem |
| fileName | **Név vagy helyettesítő karaktert tartalmazó szűrő** az fájl(ok) a megadott "folderPath" alatt. Ez a tulajdonság értékét nem adja meg, ha az adatkészlet mutat a mappában lévő összes fájlt. <br/><br/>Szűrő esetén engedélyezett a helyettesítő karaktereket: `*` (nulla vagy több olyan karakterre illeszkedik) és `?` (megegyezik a nulla vagy önálló karakter).<br/>-1. példa: `"fileName": "*.csv"`<br/>– 2. példa: `"fileName": "???20180427.txt"`<br/>Használat `^` elkerülésére, ha a fájl tényleges nevét helyettesítő elemet vagy a escape karaktere belül.<br/><br/>Ha nincs megadva fájlnév egy kimeneti adatkészletet és **preserveHierarchy** nincs megadva a tevékenység fogadó, a másolási tevékenység létrehozza a fájl neve a következő mintának: "*Adatokat. [a tevékenység futtatása azonosító GUID]. [GUID Ha FlattenHierarchy]. [Ha a konfigurált formátum]. [Ha konfigurálta a tömörítés]* ". Ha például "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz". Tábla neve helyett lekérdezési táblázatos forrásból másol, ha van-e a névminta "*[táblanév]. [ formátum]. [Ha konfigurálta a tömörítés]* ". Például "MyTable.csv." |Nem |
| modifiedDatetimeStart | Az attribútum alapján fájlok szűrés: Utolsó módosítás. A fájlok lesz kiválasztva, ha az utolsó módosítás időpontja közötti időtartományban `modifiedDatetimeStart` és `modifiedDatetimeEnd`. Az idő UTC időzóna szerint formátumban alkalmazott "2018-12-01T05:00:00Z". <br/><br/> Ügyeljen arra, hogy általános teljesítményét adatáthelyezési érint ez a beállítás engedélyezésével, ha meg szeretné szűrő fájlok hatalmas mennyiségű fájlt. <br/><br/> A Tulajdonságok lehet olyan nem fájlszűrő attribútum alkalmazandó az adatkészletet, amely a NULL.  Amikor `modifiedDatetimeStart` dátum és idő értékkel rendelkezik, de `modifiedDatetimeEnd` má hodnotu NULL, azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútum értéke nagyobb, mint vagy egyenlő a dátum és idő értékkel lesz kiválasztva.  Amikor `modifiedDatetimeEnd` dátum és idő értékkel rendelkezik, de `modifiedDatetimeStart` má hodnotu NULL, azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútum értéke kisebb, mint a dátum/idő értéket fog jelölni.| Nem |
| modifiedDatetimeEnd | Az attribútum alapján fájlok szűrés: Utolsó módosítás. A fájlok lesz kiválasztva, ha az utolsó módosítás időpontja közötti időtartományban `modifiedDatetimeStart` és `modifiedDatetimeEnd`. Az idő UTC időzóna szerint formátumban alkalmazott "2018-12-01T05:00:00Z". <br/><br/> Ügyeljen arra, hogy általános teljesítményét adatáthelyezési érint ez a beállítás engedélyezésével, ha meg szeretné szűrő fájlok hatalmas mennyiségű fájlt. <br/><br/> A Tulajdonságok lehet olyan nem fájlszűrő attribútum alkalmazandó az adatkészletet, amely a NULL.  Amikor `modifiedDatetimeStart` dátum és idő értékkel rendelkezik, de `modifiedDatetimeEnd` má hodnotu NULL, azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútum értéke nagyobb, mint vagy egyenlő a dátum és idő értékkel lesz kiválasztva.  Amikor `modifiedDatetimeEnd` dátum és idő értékkel rendelkezik, de `modifiedDatetimeStart` má hodnotu NULL, azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútum értéke kisebb, mint a dátum/idő értéket fog jelölni.| Nem |
| format | Ha azt szeretné, hogy **, a fájlok másolása a-rendszer** közötti fájlalapú tárolók (bináris másolat), hagyja ki a format szakaszban mindkét bemeneti és kimeneti adatkészlet-definíciókban.<br/><br/>Ha szeretné elemezni, vagy hozzon létre egy adott formátumú fájlok formátuma a következő fájltípusokat támogatja: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Állítsa be a **típus** tulajdonság alatt formátumot az alábbi értékek egyikére. További információkért lásd: [szövegformátum](supported-file-formats-and-compression-codecs.md#text-format), [Json formátumban](supported-file-formats-and-compression-codecs.md#json-format), [Avro formátum](supported-file-formats-and-compression-codecs.md#avro-format), [Orc formátum](supported-file-formats-and-compression-codecs.md#orc-format), és [Parquetformátum](supported-file-formats-and-compression-codecs.md#parquet-format) szakaszokat. |Nem (csak a bináris másolás esetén) |
| compression | Adja meg a típus és az adatok tömörítési szintje. További információkért lásd: [támogatott fájlformátumok és tömörítési kodek](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Támogatott típusok a következők: **A GZip**, **Deflate**, **BZip2**, és **ZipDeflate**.<br/>Támogatott szintek a következők: **Optimális** és **leggyorsabb**. |Nem |


>[!TIP]
>Másolja egy mappában található összes fájlt, adja meg a **folderPath** csak.<br>Adjon meg egy adott nevű egyetlen fájl másolásához **folderPath** a mappára vonatkozó részt és **fileName** nevére.<br>Másolja a fájlokat egy mappában egy részét, adja meg a **folderPath** mappára vonatkozó részt a és **fileName** helyettesítő szűrővel. 

**Példa**

```json
{
    "name": "ADLSDataset",
    "properties": {
        "type": "AzureDataLakeStoreFile",
        "linkedServiceName":{
            "referenceName": "<ADLS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "datalake/myfolder/",
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

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: [folyamatok](concepts-pipelines-activities.md). Ez a szakasz az Azure Data Lake Store-adatforrások és adatfogadók által támogatott tulajdonságok listáját tartalmazza.

### <a name="azure-data-lake-store-as-source"></a>Az Azure Data Lake Store mint forrás

- A Másolás **Parquet és tagolt szövegformátum**, tekintse meg [elválasztójellel tagolt szöveges formátum forrás- és Parquet](#parquet-and-delimited-text-format-source) szakaszban.
- A más formátumú, például a Másolás **ORC/Avro/JSON/bináris formátum**, tekintse meg [más formátumú forrás](#other-format-source) szakaszban.

#### <a name="parquet-and-delimited-text-format-source"></a>Parquet és elválasztójellel tagolt szöveges formátum forrása

Adatok másolása az ADLS-Gen1 **Parquet vagy tagolt szövegformátum**, tekintse meg [Parquet formátum](format-parquet.md) és [tagolt szövegformátum](format-delimited-text.md) formátum-alapú másolási tevékenység forrása a cikk és támogatott beállítások. A következő tulajdonságok támogatottak ADLS Gen1 alatt `storeSettings` formátum-alapú másolási forrásaként beállításait:

| Tulajdonság                 | Leírás                                                  | Szükséges                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | A type tulajdonság alatt `storeSettings` értékre kell állítani **AzureDataLakeStoreReadSetting**. | Igen                                           |
| recursive                | Azt jelzi, hogy az adatok olvasható rekurzív módon az almappák vagy csak a megadott mappába. Vegye figyelembe, hogy ha a rekurzív értéke igaz, és a fogadó a fájlalapú tároló, egy üres mappát vagy almappát nem másolja vagy létrehozott, a fogadó. Engedélyezett értékek a következők **igaz** (alapértelmezett), és **hamis**. | Nem                                            |
| wildcardFolderPath       | Mappa elérési útja a forrás mappák helyettesítő karakter. <br>Helyettesítő karakterek engedélyezettek: `*` (nulla vagy több olyan karakterre illeszkedik) és `?` (megegyezik a nulla vagy önálló karakter); használata `^` elkerülésére, ha a tényleges mappanevet helyettesítő elemet vagy a escape karaktere belül. <br>További példák a [példák a mappához és fájlhoz szűrők](#folder-and-file-filter-examples). | Nem                                            |
| wildcardFileName         | A szűrő forrásfájljaihoz megadott folderPath/wildcardFolderPath mellett helyettesítő karaktereket is tartalmazó fájl neve. <br>Helyettesítő karakterek engedélyezettek: `*` (nulla vagy több olyan karakterre illeszkedik) és `?` (megegyezik a nulla vagy önálló karakter); használata `^` elkerülésére, ha a tényleges mappanevet helyettesítő elemet vagy a escape karaktere belül.  További példák a [példák a mappához és fájlhoz szűrők](#folder-and-file-filter-examples). | Igen, ha `fileName` nincs megadva a következő adatkészlet |
| modifiedDatetimeStart    | Az attribútum alapján fájlok szűrés: Utolsó módosítás. A fájlok lesz kiválasztva, ha az utolsó módosítás időpontja közötti időtartományban `modifiedDatetimeStart` és `modifiedDatetimeEnd`. Az idő UTC időzóna szerint formátumban alkalmazott "2018-12-01T05:00:00Z". <br> A Tulajdonságok lehet null értékű, ami jelenti azt, hogy nincs fájlszűrő attribútum alkalmazandó az adatkészletet.  Amikor `modifiedDatetimeStart` dátum és idő értékkel rendelkezik, de `modifiedDatetimeEnd` má hodnotu NULL, azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútum értéke nagyobb, mint vagy egyenlő a dátum és idő értékkel lesz kiválasztva.  Amikor `modifiedDatetimeEnd` dátum és idő értékkel rendelkezik, de `modifiedDatetimeStart` má hodnotu NULL, azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútum értéke kisebb, mint a dátum/idő értéket fog jelölni. | Nem                                            |
| modifiedDatetimeEnd      | Ugyanaz, mint a fenti.                                               | Nem                                            |
| maxConcurrentConnections | A szeretne csatlakozni a storage-tároló egyidejű kapcsolatok száma. Adja meg, csak akkor, ha szeretné korlátozni a egyidejű kapcsolat az adattárba. | Nem                                            |

> [!NOTE]
> A Parquet vagy tagolt szövegformátum **AzureDataLakeStoreSource** továbbra is támogatott a következő szakaszban említett típusa másolási tevékenység forrása – az előző verziókkal való kompatibilitás. A jövőben az új modell használata javasolt, és ezek a típusok létrehozása a felhasználói felület szerzői ADF változott.

**Példa**

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
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
                    "type": "DelimitedTextReadSetting",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AzureDataLakeStoreReadSetting",
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

#### <a name="other-format-source"></a>Más formátumú forrás

Adatok másolása az ADLS-Gen1 **ORC/Avro/JSON/bináris formátum**, a következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A `type` értékre kell állítani a másolási tevékenység forrás tulajdonságát: **AzureDataLakeStoreSource**. |Igen |
| recursive | Azt jelzi, hogy az adatok olvasható rekurzív módon az almappák vagy csak a megadott mappába. Vegye figyelembe, hogy amikor `recursive` értéke igaz, és a fogadó egy fájlalapú tároló, egy üres mappát vagy almappát nem másolta, vagy hozza létre a fogadó. Engedélyezett értékek a következők: **igaz** (alapértelmezett), és **hamis**. | Nem |
| maxConcurrentConnections | Az adattár egyidejűleg csatlakozni a kapcsolatok száma. Adja meg, csak akkor, ha szeretné korlátozni a egyidejű kapcsolat az adattárba. | Nem |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS Gen1 input dataset name>",
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
                "type": "AzureDataLakeStoreSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-data-lake-store-as-sink"></a>Az Azure Data Lake Store mint fogadó

- A Másolás **Parquet és tagolt szövegformátum**, tekintse meg [Parquet és elválasztójellel tagolt szöveges formátum fogadó](#parquet-and-delimited-text-format-sink) szakaszban.
- A más formátumú, például a Másolás **ORC/Avro/JSON/bináris formátum**, tekintse meg [más formátumú fogadó](#other-format-sink) szakaszban.

#### <a name="parquet-and-delimited-text-format-sink"></a>Parquet és elválasztójellel tagolt szöveges formátum fogadó

Adatok másolása az ADLS-Gen1 **Parquet vagy tagolt szövegformátum**, tekintse meg [Parquet formátum](format-parquet.md) és [tagolt szövegformátum](format-delimited-text.md) formátum-alapú másolási tevékenység fogadó cikket, és támogatott beállítások. A következő tulajdonságok támogatottak ADLS Gen1 alatt `storeSettings` formátum-alapú másolási fogadó beállításait:

| Tulajdonság                 | Leírás                                                  | Szükséges |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | A type tulajdonság alatt `storeSettings` értékre kell állítani **AzureDataLakeStoreWriteSetting**. | Igen      |
| copyBehavior             | A másolási viselkedés határozza meg, ha a forrás fájlok fájlalapú adattárból.<br/><br/>Engedélyezett értékek a következők:<br/><b>-PreserveHierarchy (alapértelmezett)</b>: Megőrzi a hierarchiája a célmappában. A forrásmappa forrás-fájl elérési útja megegyezik a célmappában a célfájl elérési útja.<br/><b>-FlattenHierarchy</b>: Minden fájl a forrásmappából szerepelnek, az első szintjét a célmappában. A cél fájlok automatikusan létrehozott névvel rendelkeznek. <br/><b>-MergeFiles</b>: Egy fájl összes fájlt a forrásmappából egyesíti. A fájl neve meg van adva, az egyesített fájlnév-e a megadott néven. Ellenkező esetben egy automatikusan létrehozott nevét. | Nem       |
| maxConcurrentConnections | Az adattár egyidejűleg csatlakozni a kapcsolatok száma. Adja meg, csak akkor, ha szeretné korlátozni a egyidejű kapcsolat az adattárba. | Nem       |

> [!NOTE]
> A Parquet vagy tagolt szövegformátum **AzureDataLakeStoreSink** továbbra is támogatott a következő szakaszban említett típusa másolási tevékenység fogadó-visszamenőleges kompatibilitásra. A jövőben az új modell használata javasolt, és ezek a típusok létrehozása a felhasználói felület szerzői ADF változott.

**Példa**

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
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
                    "type": "AzureDataLakeStoreWriteSetting",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

#### <a name="other-format-sink"></a>Más formátumú fogadó

Adatok másolása az ADLS-Gen1 **ORC/Avro/JSON/bináris formátum**, a következő tulajdonságok támogatottak a **fogadó** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A `type` értékre kell állítani a másolási tevékenység fogadó tulajdonságát: **AzureDataLakeStoreSink**. |Igen |
| copyBehavior | A másolási viselkedés határozza meg, ha a forrás fájlok fájlalapú adattárból.<br/><br/>Engedélyezett értékek a következők:<br/><b>-PreserveHierarchy (alapértelmezett)</b>: megőrzi a hierarchiája a célmappában. A relatív elérési útját a forrásfájl, a megadott forrásmappához megegyezik a célmappában cél fájl relatív elérési útját.<br/><b>-FlattenHierarchy</b>: a forrásmappából a fájlok az első szintjét a célmappában található is. A cél fájlok automatikusan létrehozott névvel rendelkeznek. <br/><b>-MergeFiles</b>: egyesíti a forrásmappából egy fájl összes fájlt. A fájl neve meg van adva, az egyesített fájlnév-e a megadott néven. Ellenkező esetben a fájlnév formátuma automatikusan létrehozott. | Nem |
| maxConcurrentConnections | Az adattár egyidejűleg csatlakozni a kapcsolatok száma. Adja meg, csak akkor, ha szeretné korlátozni a egyidejű kapcsolat az adattárba. | Nem |

**Példa**

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS Gen1 output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataLakeStoreSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Mappa és fájl szűrő példák

Ez a szakasz ismerteti a mappa elérési útját és nevét, az eredményül kapott viselkedéstől helyettesítő szűrőket.

| folderPath | fileName | recursive | Forrás-mappa szerkezete és szűrő eredmény (a fájlok **félkövér** adatok lekérése)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (üres, használhatja az alapértelmezettet) | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (üres, használhatja az alapértelmezettet) | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="examples-of-behavior-of-the-copy-operation"></a>Példák a másolási művelet viselkedése

Ez a szakasz ismerteti a másolási művelet különböző kombinációihoz az eredményül kapott viselkedéstől `recursive` és `copyBehavior` értékeket.

| recursive | copyBehavior | Forrás mappastruktúra | Eredményül kapott cél |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A cél szerkezete ugyanaz, mint a forrás mappa1 hozzon létre:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A cél mappa1 jön létre az alábbi struktúra használatával: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatikusan létrehozott nevet a file1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File5 |
| true |mergeFiles | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A cél mappa1 jön létre az alábbi struktúra használatával: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + fájl3 + File4 + 5 fájl tartalmát egy fájlba, egy automatikusan létrehozott fájlnévvel rendelkező egyesítésekor. |
| false |preserveHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A cél mappa1 jön létre az alábbi struktúra használatával:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Fájl3, File4 és File5 Subfolder1 a rendszer nem követi. |
| false |flattenHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A cél mappa1 jön létre az alábbi struktúra használatával:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatikusan létrehozott nevet a file1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File2<br/><br/>Fájl3, File4 és File5 Subfolder1 a rendszer nem követi. |
| false |mergeFiles | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A cél mappa1 jön létre az alábbi struktúra használatával:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + 2. fájl tartalmát egy fájlba a fájl automatikusan létrehozott névvel összefésülése megtörténjen. Automatikusan létrehozott nevet a file1 kiszolgálón<br/><br/>Fájl3, File4 és File5 Subfolder1 a rendszer nem követi. |

## <a name="preserve-acls-to-data-lake-storage-gen2"></a>Hozzáférés-vezérlési listák Data Lake Storage Gen2-re megőrzése

Ha meg szeretné replikálni a hozzáférés-vezérlési listák data fájlokkal együtt, a Data Lake Storage Gen1 Gen2-re történő frissítés során, olvassa el [megőrzése hozzáférés-vezérlési listák a Data Lake Storage Gen1](connector-azure-data-lake-storage.md#preserve-acls-from-data-lake-storage-gen1).

## <a name="mapping-data-flow-properties"></a>Adatfolyam-tulajdonságok hozzárendelése

A részletek [átalakítási forrás](data-flow-source.md) és [átalakítási fogadó](data-flow-sink.md) az adatfolyam-leképezés.

## <a name="next-steps"></a>További lépések

Az Azure Data Factory másolási tevékenység által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md##supported-data-stores-and-formats).

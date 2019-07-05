---
title: Másolja az adatokat, vagy az Azure Data Lake Storage Gen1 Data Factory használatával |} A Microsoft Docs
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
ms.date: 07/02/2019
ms.author: jingwang
ms.openlocfilehash: df88c3e2e07165182c917eaf30a5f37451fbd073
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509582"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-using-azure-data-factory"></a>Másolja az adatokat, vagy az Azure Data Lake Storage Gen1 Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki az Azure Data Factory használt verzióját:"]
> * [1-es verzió](v1/data-factory-azure-datalake-connector.md)
> * [Aktuális verzió](connector-azure-data-lake-store.md)

Ez a cikk ismerteti, hogyan másolhat adatokat, és az Azure Data Lake Storage Gen1. Azure Data Factory szolgáltatásról, olvassa el a [bevezető cikk](introduction.md).

## <a name="supported-capabilities"></a>Támogatott képességek

Az Azure Data Lake Storage Gen1 összekötő támogatott a következő tevékenységeket:

- [Másolási tevékenység](copy-activity-overview.md) a [támogatott forrás és fogadó mátrix](copy-activity-overview.md)
- [Az adatfolyam-leképezés](concepts-data-flow-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)

Pontosabban az összekötő a következőket teheti:

- Másolja a fájlokat az alábbi hitelesítési módszerek egyikének használatával: Azure-erőforrások egyszerű vagy felügyelt szolgáltatásidentitásokat.
- Másolja a fájlokat, vagy elemezni, vagy hozzon létre fájlokat a [támogatott fájlformátumok és tömörítési kodek](supported-file-formats-and-compression-codecs.md).

> [!IMPORTANT]
> Ha adatokat másolja a saját üzemeltetésű integrációs modul használatával, konfigurálja a vállalati tűzfalon a kimenő forgalom engedélyezésére `<ADLS account name>.azuredatalakestore.net` és `login.microsoftonline.com/<tenant>/oauth2/token` 443-as porton. Az utóbbi az Azure biztonsági jogkivonat-szolgáltatás, amely az integrációs modul és a hozzáférési jogkivonat beszerzése az közötti kommunikációhoz szükséges.

## <a name="get-started"></a>Bevezetés

> [!TIP]
> Bemutatja, hogyan használhatja az Azure Data Lake Store-összekötő a lépésenkénti útmutatóért lásd: [adatok betöltése az Azure Data Lake Store](load-azure-data-lake-store.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Az alábbi szakaszok ismertetik az Azure Data Lake Store adott Data Factory-entitások definiálásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A következő tulajdonságok támogatottak a az Azure Data Lake Store-beli társított szolgáltatást:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A `type` tulajdonságot állítsa **AzureDataLakeStore**. | Igen |
| dataLakeStoreUri | Az Azure Data Lake Store-fiókkal kapcsolatos információk. Ezt az információt a következő formátumok egyikét veheti fel: `https://[accountname].azuredatalakestore.net/webhdfs/v1` vagy `adl://[accountname].azuredatalakestore.net/`. | Igen |
| subscriptionId | Az Azure-előfizetés azonosítója, amelyhez a Data Lake Store-fiókhoz tartozik. | A fogadó megadása kötelező |
| resourceGroupName | A Azure erőforráscsoport-név, amely a Data Lake Store-fiókhoz tartozik. | A fogadó megadása kötelező |
| connectVia | A [integrációs modul](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. Az Azure-beli integrációs modul és a egy saját üzemeltetésű integrációs modult is használhatja, ha az adattár egy magánhálózaton található. Ha ez a tulajdonság nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

### <a name="use-service-principal-authentication"></a>Használja az egyszerű szolgáltatásnév hitelesítése

Egyszerű szolgáltatásnév hitelesítése használandó entitás alkalmazás regisztrálása az Azure Active Directoryban, és hozzáférést biztosítani, Data Lake Store. Részletes lépéseiért lásd: [szolgáltatások közötti hitelesítés](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Jegyezze fel a következő értékeket, mert a társított szolgáltatás definiálásához használja:

- Alkalmazásazonosító
- Alkalmazáskulcs
- Bérlőazonosító

>[!IMPORTANT]
> A szolgáltatás egyszerű megfelelő engedély a Data Lake Store megadása:
>- **Forrásként**: A **adatkezelő** > **hozzáférés**, adjon meg legalább **Olvasás + végrehajtás** listázása, és másolja a fájlokat a mappákban és almappáiban engedéllyel. Vagy biztosíthat **olvasási** engedéllyel egy fájl másolása. Dönthet úgy, hogy hozzá **ezt a mappát, és az összes gyermekre** a rekurzív, és adja hozzá **egy hozzáférési és alapértelmezett engedély bejegyzés**. Esetében nem követelmény a fiókszintű hozzáférés-vezérlés (IAM).
>- **Fogadóként**: A **adatkezelő** > **hozzáférés**, adjon meg legalább **írás + végrehajtás** engedély szükséges gyermekelemek létrehozásához a mappában. Dönthet úgy, hogy hozzá **ezt a mappát, és az összes gyermekre** a rekurzív, és adja hozzá **egy hozzáférési és alapértelmezett engedély bejegyzés**. Ha egy Azure-beli integrációs modul használatával másolása (forrás- és fogadóadattárként is vannak a felhőben), az IAM, biztosítson legalább a **olvasó** szerepkör annak érdekében, hogy észlelni az Data Lake Store-régiót, a Data Factory. Ha szeretné-e elkerülése érdekében explicit módon a IAM szerepkör [Azure integrációs modul létrehozása](create-azure-integration-runtime.md#create-azure-ir) helyét, a Data Lake Store. Például ha a Data Lake Store Nyugat-Európában, hozzon létre egy Azure-beli integrációs modul helyét beállítása "Nyugat-Európa." A Data Lake Store társított szolgáltatásban, az alábbi példában látható módon rendelheti őket.

>[!NOTE]
>A lista kezdve a legfelső szintű mappák, be kell az egyszerű szolgáltatás nyújtott engedélye **gyökér szintű "Execute" engedéllyel rendelkező**. Ez igaz, ha használja a:
>- **Másolás eszköz** a szerző másolási folyamat.
>- **Data Factory felhasználói felülete** tesztelheti a kapcsolatot, és lépjen a mappák létrehozása alatt.
>Ha kapcsolatos engedély gyökér szintű során szerzői, hagyja ki a kapcsolat és a bemeneti engedély paraent útvonala majd válassza a Tallózás gombra a megadott elérési út. Másolja a tevékenység működése, mindaddig, amíg az egyszerű szolgáltatás átmásolni a fájlokat a megfelelő engedéllyel rendelkező kapnak.

A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| servicePrincipalId | Adja meg az alkalmazás ügyfél-azonosítót. | Igen |
| servicePrincipalKey | Adja meg az alkalmazáskulcsot. Jelölje meg a mező egy `SecureString` tárolja biztonságos helyen a Data Factory áttekintése, vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Igen |
| tenant | Adja meg a bérlő információkat, például a tartománynév, vagy a bérlő Azonosítóját, amelyben az alkalmazás található. Kérheti a viszi az egérmutatót az Azure portal jobb felső sarkában. | Igen |

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
>- **Forrásként**: A **adatkezelő** > **hozzáférés**, adjon meg legalább **Olvasás + végrehajtás** listázása, és másolja a fájlokat a mappákban és almappáiban engedéllyel. Vagy biztosíthat **olvasási** engedéllyel egy fájl másolása. Dönthet úgy, hogy hozzá **ezt a mappát, és az összes gyermekre** a rekurzív, és adja hozzá **egy hozzáférési és alapértelmezett engedély bejegyzés**. Esetében nem követelmény a fiókszintű hozzáférés-vezérlés (IAM).
>- **Fogadóként**: A **adatkezelő** > **hozzáférés**, adjon meg legalább **írás + végrehajtás** engedély szükséges gyermekelemek létrehozásához a mappában. Dönthet úgy, hogy hozzá **ezt a mappát, és az összes gyermekre** a rekurzív, és adja hozzá **egy hozzáférési és alapértelmezett engedély bejegyzés**. Ha egy Azure-beli integrációs modul használatával másolása (forrás- és fogadóadattárként is vannak a felhőben), az IAM, biztosítson legalább a **olvasó** szerepkör annak érdekében, hogy észlelni az Data Lake Store-régiót, a Data Factory. Ha szeretné-e elkerülése érdekében explicit módon a IAM szerepkör [Azure integrációs modul létrehozása](create-azure-integration-runtime.md#create-azure-ir) helyét, a Data Lake Store. A Data Lake Store társított szolgáltatásban, az alábbi példában látható módon rendelheti őket.

>[!NOTE]
>A lista kezdve a legfelső szintű mappák, be kell az engedély az engedély megadása a felügyelt identitás **gyökér szintű "Execute" engedéllyel rendelkező**. Ez igaz, ha használja a:
>- **Másolás eszköz** a szerző másolási folyamat.
>- **Data Factory felhasználói felülete** tesztelheti a kapcsolatot, és lépjen a mappák létrehozása alatt.
>Ha kapcsolatos engedély gyökér szintű során szerzői, hagyja ki a kapcsolat és a bemeneti engedély megadása a szülő elérési utat a Tallózás gombra, majd válassza a megadott elérési út. Másolja a tevékenység működése, mindaddig, amíg az egyszerű szolgáltatás átmásolni a fájlokat a megfelelő engedéllyel rendelkező kapnak.

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

- A parquet eszközökben és tagolt szövegformátum, tekintse meg a [Parquet és elválasztójellel tagolt szöveges formátumban adatkészlet](#parquet-and-delimited-text-format-dataset) szakaszban.
- Más formátumokhoz, például ORC, Avro, JSON vagy bináris formátum, tekintse meg a [más formátumú adatkészlet](#other-format-dataset) szakaszban.

### <a name="parquet-and-delimited-text-format-dataset"></a>Parquet és elválasztójellel tagolt szöveges formátum adatkészlet

Adatok másolása és az Azure Data Lake Store Gen1 a parquet vagy tagolt szövegformátum, tekintse meg a [Parquet formátum](format-parquet.md) és [tagolt szövegformátum](format-delimited-text.md) cikkek formátum-alapú adatkészlet és a támogatott beállítások. A következő tulajdonságok támogatottak az Azure Data Lake Store Gen1 alatt `location` formátum-alapú adatkészlet beállításai:

| Tulajdonság   | Leírás                                                  | Szükséges |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | A type tulajdonság alatt `location` az adatkészletben lévő értékre kell állítani **AzureDataLakeStoreLocation**. | Igen      |
| folderPath | A mappa elérési útja. Ha szeretne egy helyettesítő karakter segítségével szűrő mappákat, hagyja ki ezt a beállítást, és adja meg a tevékenység – forrásbeállítások. | Nem       |
| fileName   | A fájlnév a megadott folderPath alatt. Ha szeretne egy helyettesítő karakter használatával szűrhetők a fájlok, hagyja ki ezt a beállítást, és adja meg a tevékenység – forrásbeállítások. | Nem       |

> [!NOTE]
>
> A **AzureDataLakeStoreFile** parquet vagy szöveges formátum a következő szakaszban említett adatkészlet típusa, a másolás, a keresési és a GetMetadata tevékenység a visszamenőleges kompatibilitás érdekében továbbra is támogatott. De a leképezés data flow szolgáltatással nem működik. Azt javasoljuk, hogy használja-e az új modell, a jövőben. A Data Factory felhasználói felületén szerzői állít elő, hogy ezek a típusok.

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

Az adatok másolása és az Azure Data Lake Store Gen1 ORC, Avro, JSON vagy bináris formátum, a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adatkészlet értékre kell állítani **AzureDataLakeStoreFile**. |Igen |
| folderPath | A Data Lake Store a mappa elérési útját. Ha nincs megadva, a legfelső szintű mutat. <br/><br/>Helyettesítő karaktert tartalmazó szűrő használata támogatott. A helyettesítő karakterek engedélyezettek `*` (nulla vagy több olyan karakterre illeszkedik) és `?` (megegyezik a nulla vagy önálló karakter). Használat `^` elkerülésére, ha a tényleges mappanevet helyettesítő karakter, vagy a escape karaktere belül. <br/><br/>Például: a gyökérmappa/almappa /. További példák a [példák a mappához és fájlhoz szűrők](#folder-and-file-filter-examples). |Nem |
| fileName | Név vagy helyettesítő karaktert tartalmazó szűrő a fájlok a megadott "folderPath" alatt. Ez a tulajdonság értékét nem adja meg, ha az adatkészlet mutat a mappában lévő összes fájlt. <br/><br/>A szűrő a helyettesítő karakterek engedélyezve vannak `*` (nulla vagy több olyan karakterre illeszkedik) és `?` (megegyezik a nulla vagy önálló karakter).<br/>-1. példa: `"fileName": "*.csv"`<br/>– 2. példa: `"fileName": "???20180427.txt"`<br/>Használat `^` elkerülésére, ha a fájl tényleges nevét egy helyettesítő karakter, vagy ez escape karaktere belül.<br/><br/>Ha nincs megadva fájlnév egy kimeneti adatkészletet és **preserveHierarchy** nincs megadva a tevékenység fogadó, a másolási tevékenység létrehozza a fájl neve a következő mintának: "*Adatokat. [a tevékenység futtatása azonosító GUID]. [GUID Ha FlattenHierarchy]. [Ha a konfigurált formátum]. [Ha konfigurálta a tömörítés]* ", például"Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz". Ha egy lekérdezést a tábla neve helyett másolja a táblázatos forrásból, van-e a névminta " *[táblanév]. [ formátum]. [Ha konfigurálta a tömörítés]* ", például"MyTable.csv". |Nem |
| modifiedDatetimeStart | Fájlok szűrése az utolsó módosítás attribútum alapján. A fájlok ki van jelölve, ha az utolsó módosítás időpontja közötti időtartományban `modifiedDatetimeStart` és `modifiedDatetimeEnd`. Az idő UTC időzóna szerint formátumban alkalmazott "2018-12-01T05:00:00Z". <br/><br/> Adatáthelyezés általános teljesítményét befolyásolja a beállítás engedélyezése, ha meg szeretné szűrő fájlok hatalmas mennyiségű fájlt. <br/><br/> A Tulajdonságok lehet null értékű, ami azt jelenti, hogy nincs fájl attribútum szűrő alkalmazása az adatkészlethez. Amikor `modifiedDatetimeStart` egy datetime értéket tartalmaz, de `modifiedDatetimeEnd` má hodnotu NULL, azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútum értéke nagyobb, mint vagy egyenlő a DateTime típusú érték van kijelölve. Amikor `modifiedDatetimeEnd` egy datetime értéket tartalmaz, de `modifiedDatetimeStart` má hodnotu NULL, ez azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútuma kisebb, mint a dátum/idő érték kiválasztva.| Nem |
| modifiedDatetimeEnd | Fájlok szűrése az utolsó módosítás attribútum alapján. A fájlok ki van jelölve, ha az utolsó módosítás időpontja közötti időtartományban `modifiedDatetimeStart` és `modifiedDatetimeEnd`. Az idő UTC időzóna szerint formátumban alkalmazott "2018-12-01T05:00:00Z". <br/><br/> Adatáthelyezés általános teljesítményét befolyásolja a beállítás engedélyezése, ha meg szeretné szűrő fájlok hatalmas mennyiségű fájlt. <br/><br/> A Tulajdonságok lehet null értékű, ami azt jelenti, hogy nincs fájl attribútum szűrő alkalmazása az adatkészlethez. Amikor `modifiedDatetimeStart` egy datetime értéket tartalmaz, de `modifiedDatetimeEnd` má hodnotu NULL, azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútum értéke nagyobb, mint vagy egyenlő a DateTime típusú érték van kijelölve. Amikor `modifiedDatetimeEnd` egy datetime értéket tartalmaz, de `modifiedDatetimeStart` má hodnotu NULL, ez azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútuma kisebb, mint a dátum/idő érték kiválasztva.| Nem |
| format | Ha szeretné másolni a fájlokat, a fájlalapú tárolók (bináris másolat) közötti, hagyja ki a format szakaszban mindkét bemeneti és kimeneti adatkészlet-definíciókban.<br/><br/>Ha szeretné elemezni, vagy hozzon létre egy adott formátumú fájlok formátuma a következő fájltípusokat támogatja: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, és **ParquetFormat**. Állítsa be a **típus** tulajdonság alatt **formátum** az alábbi értékek egyikére. További információkért lásd: a [szövegformátum](supported-file-formats-and-compression-codecs.md#text-format), [JSON formátumban](supported-file-formats-and-compression-codecs.md#json-format), [Avro formátum](supported-file-formats-and-compression-codecs.md#avro-format), [Orc formátum](supported-file-formats-and-compression-codecs.md#orc-format), és [Parquet formátum ](supported-file-formats-and-compression-codecs.md#parquet-format) szakaszokat. |Nem (csak a bináris másolás esetén) |
| compression | Adja meg a típus és az adatok tömörítési szintje. További információkért lásd: [támogatott fájlformátumok és tömörítési kodek](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Támogatott típusok a következők **GZip**, **Deflate**, **BZip2**, és **ZipDeflate**.<br/>Támogatott szintek a következők **Optimal** és **leggyorsabb**. |Nem |


>[!TIP]
>Másolja egy mappában található összes fájlt, adja meg a **folderPath** csak.<br>Adjon meg egy adott nevű egyetlen fájl másolásához **folderPath** egy mappára vonatkozó részt a és **fileName** a fájl nevét.<br>Másolja a fájlokat egy mappában egy részét, adja meg a **folderPath** egy mappára vonatkozó részt a és **fileName** egy helyettesítő szűrővel. 

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

- Másol parquet vagy tagolt szövegformátum, tekintse meg a [elválasztójellel tagolt szöveges formátum forrás- és Parquet](#parquet-and-delimited-text-format-source) szakaszban.
- Más tartalomfolyamokat ORC, az avro-hoz, a JSON és a bináris formátum másolása, tekintse meg a [más formátumú forrás](#other-format-source) szakaszban.

#### <a name="parquet-and-delimited-text-format-source"></a>Parquet és elválasztójellel tagolt szöveges formátum forrása

Adatok másolása az Azure Data Lake Store Gen1 parquet vagy elválasztójellel tagolt szöveges formátumban, tekintse meg a [Parquet formátum](format-parquet.md) és [tagolt szövegformátum](format-delimited-text.md) formátum-alapú másolási tevékenység forrás- és a támogatott beállítások cikkeket. A következő tulajdonságok támogatottak az Azure Data Lake Store Gen1 alatt `storeSettings` a formátum-alapú másolási forrás beállításai:

| Tulajdonság                 | Leírás                                                  | Szükséges                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | A type tulajdonság alatt `storeSettings` értékre kell állítani **AzureDataLakeStoreReadSetting**. | Igen                                           |
| recursive                | Azt jelzi, hogy az adatok olvasható rekurzív módon az almappák vagy csak a megadott mappába. Ha rekurzív értéke igaz, és a fogadó egy fájlalapú tároló, egy üres mappát vagy almappát nem másolja vagy létrehozott, a fogadó. Engedélyezett értékek a következők **igaz** (alapértelmezett), és **hamis**. | Nem                                            |
| wildcardFolderPath       | Mappa elérési útja a forrás mappák helyettesítő karakter. <br>A helyettesítő karakterek engedélyezettek `*` (nulla vagy több olyan karakterre illeszkedik) és `?` (megegyezik a nulla vagy önálló karakter). Használat `^` elkerülésére, ha a tényleges mappanevet helyettesítő karakter, vagy a escape karaktere belül. <br>További példák a [példák a mappához és fájlhoz szűrők](#folder-and-file-filter-examples). | Nem                                            |
| wildcardFileName         | A szűrő forrásfájljaihoz megadott folderPath/wildcardFolderPath mellett helyettesítő karaktereket is tartalmazó fájl neve. <br>A helyettesítő karakterek engedélyezettek `*` (nulla vagy több olyan karakterre illeszkedik) és `?` (megegyezik a nulla vagy önálló karakter). Használat `^` elkerülésére, ha a tényleges mappanevet helyettesítő karakter, vagy a escape karaktere belül. További példák a [példák a mappához és fájlhoz szűrők](#folder-and-file-filter-examples). | Igen, ha `fileName` nincs megadva a adatkészlet |
| modifiedDatetimeStart    | Fájlok szűrése az utolsó módosítás attribútum alapján. A fájlok ki van jelölve, ha az utolsó módosítás időpontja közötti időtartományban `modifiedDatetimeStart` és `modifiedDatetimeEnd`. Az idő UTC időzóna szerint formátumban alkalmazott "2018-12-01T05:00:00Z". <br> A Tulajdonságok lehet null értékű, ami azt jelenti, hogy nincs fájl attribútum szűrő alkalmazása az adatkészlethez. Amikor `modifiedDatetimeStart` egy datetime értéket tartalmaz, de `modifiedDatetimeEnd` má hodnotu NULL, azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútum értéke nagyobb, mint vagy egyenlő a DateTime típusú érték van kijelölve. Amikor `modifiedDatetimeEnd` egy datetime értéket tartalmaz, de `modifiedDatetimeStart` má hodnotu NULL, ez azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútuma kisebb, mint a dátum/idő érték kiválasztva. | Nem                                            |
| modifiedDatetimeEnd      | Ugyanaz, mint a fenti.                                               | Nem                                            |
| maxConcurrentConnections | Szeretne csatlakozni a storage-tároló egyidejű kapcsolatok száma. Adja meg, csak akkor, ha szeretné korlátozni a egyidejű kapcsolat az adattárba. | Nem                                            |

> [!NOTE]
> A parquet vagy a elválasztójellel tagolt szöveges formátum a **AzureDataLakeStoreSource** típusa másolási tevékenység forrása az alábbi szakaszban ismertetett továbbra is támogatott, mert a visszamenőleges kompatibilitás érdekében. Azt javasoljuk, hogy használja-e az új modell, a jövőben. A Data Factory felhasználói felületén szerzői állít elő, hogy ezek a típusok.

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

Adatok másolása az Azure Data Lake Store Gen1 ORC, Avro, JSON vagy bináris formátum, a következő tulajdonságokat a másolási tevékenység által támogatott **forrás** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A `type` értékre kell állítani a másolási tevékenység forrása tulajdonságát **AzureDataLakeStoreSource**. |Igen |
| recursive | Azt jelzi, hogy az adatok olvasható rekurzív módon az almappák vagy csak a megadott mappába. Amikor `recursive` értéke igaz, és a fogadó egy fájlalapú tároló, egy üres mappát vagy almappát nem másolta, vagy hozza létre a fogadó. Engedélyezett értékek a következők **igaz** (alapértelmezett), és **hamis**. | Nem |
| maxConcurrentConnections | Az adattár egyidejűleg csatlakozhatnak száma. Adja meg, csak akkor, ha szeretné korlátozni a egyidejű kapcsolat az adattárba. | Nem |

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

- Másol parquet és tagolt szövegformátum, tekintse meg a [Parquet és elválasztójellel tagolt szöveges formátum fogadó](#parquet-and-delimited-text-format-sink) szakaszban.
- Más tartalomfolyamokat ORC, az avro-hoz, a JSON és a bináris formátum másolása, tekintse meg a [más formátumú fogadó](#other-format-sink) szakaszban.

#### <a name="parquet-and-delimited-text-format-sink"></a>Parquet és elválasztójellel tagolt szöveges formátum fogadó

Adatok másolása az Azure Data Lake Store Gen1 parquet vagy elválasztójellel tagolt szöveges formátumban, tekintse meg a [Parquet formátum](format-parquet.md) és [tagolt szövegformátum](format-delimited-text.md) formátum-alapú másolási tevékenység fogadó és a támogatott beállítások cikkeket. A következő tulajdonságok támogatottak az Azure Data Lake Store Gen1 alatt `storeSettings` a formátum-alapú másolási fogadó beállításait:

| Tulajdonság                 | Leírás                                                  | Szükséges |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | A type tulajdonság alatt `storeSettings` értékre kell állítani **AzureDataLakeStoreWriteSetting**. | Igen      |
| copyBehavior             | A másolási viselkedés határozza meg, ha a forrás fájlok fájlalapú adattárból.<br/><br/>Engedélyezett értékek a következők:<br/><b>-PreserveHierarchy (alapértelmezett)</b>: Megőrzi a hierarchiája a célmappában. A relatív elérési útját a forrásfájl, a megadott forrásmappához megegyezik a célmappában cél fájl relatív elérési útját.<br/><b>-FlattenHierarchy</b>: Minden fájl a forrásmappából szerepelnek, az első szintjét a célmappában. A cél fájlok automatikusan létrehozott névvel rendelkeznek. <br/><b>-MergeFiles</b>: Egy fájl összes fájlt a forrásmappából egyesíti. A fájl neve meg van adva, az egyesített fájlnév-e a megadott néven. Ellenkező esetben egy automatikusan létrehozott nevét. | Nem       |
| maxConcurrentConnections | Az adattár egyidejűleg csatlakozhatnak száma. Adja meg, csak akkor, ha szeretné korlátozni a egyidejű kapcsolat az adattárba. | Nem       |

> [!NOTE]
> A parquet vagy a elválasztójellel tagolt szöveges formátum a **AzureDataLakeStoreSink** típusa másolási tevékenység fogadó az alábbi szakaszban ismertetett továbbra is támogatott, mert a visszamenőleges kompatibilitás érdekében. Azt javasoljuk, hogy használja-e az új modell, a jövőben. A Data Factory felhasználói felületén szerzői állít elő, hogy ezek a típusok.

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

Adatok másolása az Azure Data Lake Store Gen1 ORC, Avro, JSON vagy bináris formátum, a következő tulajdonságok támogatottak a **fogadó** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A `type` értékre kell állítani a másolási tevékenység fogadó tulajdonságát **AzureDataLakeStoreSink**. |Igen |
| copyBehavior | A másolási viselkedés határozza meg, ha a forrás fájlok fájlalapú adattárból.<br/><br/>Engedélyezett értékek a következők:<br/><b>-PreserveHierarchy (alapértelmezett)</b>: Megőrzi a hierarchiája a célmappában. A relatív elérési útját a forrásfájl, a megadott forrásmappához megegyezik a célmappában cél fájl relatív elérési útját.<br/><b>-FlattenHierarchy</b>: Minden fájl a forrásmappából szerepelnek, az első szintjét a célmappában. A cél fájlok automatikusan létrehozott névvel rendelkeznek. <br/><b>-MergeFiles</b>: Egy fájl összes fájlt a forrásmappából egyesíti. A fájl neve meg van adva, az egyesített fájlnév-e a megadott néven. Ellenkező esetben a fájlnév formátuma automatikusan létrehozott. | Nem |
| maxConcurrentConnections | Az adattár egyidejűleg csatlakozhatnak száma. Adja meg, csak akkor, ha szeretné korlátozni a egyidejű kapcsolat az adattárba. | Nem |

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
| `Folder*` | (Üres, használhatja az alapértelmezettet) | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (Üres, használhatja az alapértelmezettet) | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="examples-of-behavior-of-the-copy-operation"></a>Példák a másolási művelet viselkedése

Ez a szakasz ismerteti a másolási művelet különböző kombinációihoz az eredményül kapott viselkedéstől `recursive` és `copyBehavior` értékeket.

| recursive | copyBehavior | Forrás mappastruktúra | Eredményül kapott cél |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A cél szerkezete ugyanaz, mint a forrás mappa1 hozzon létre:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A cél mappa1 jön létre az alábbi struktúra használatával: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet, a file1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File5 |
| true |mergeFiles | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A cél mappa1 jön létre az alábbi struktúra használatával: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + fájl3 + File4 + File5 tartalmát egy fájlba, egy automatikusan létrehozott fájl nevű egyesítésekor. |
| false |preserveHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A cél mappa1 jön létre az alábbi struktúra használatával:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Fájl3, File4 és File5 Subfolder1 mértékének nem. |
| false |flattenHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A cél mappa1 jön létre az alábbi struktúra használatával:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet, a file1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File2<br/><br/>Fájl3, File4 és File5 Subfolder1 mértékének nem. |
| false |mergeFiles | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A cél mappa1 jön létre az alábbi struktúra használatával:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + 2. fájl tartalmát egy fájlba az automatikusan létrehozott fájl neve összefésülése megtörténjen. automatikusan létrehozott nevet, a file1 kiszolgálón<br/><br/>Fájl3, File4 és File5 Subfolder1 mértékének nem. |

## <a name="preserve-acls-to-data-lake-storage-gen2"></a>Hozzáférés-vezérlési listák Data Lake Storage Gen2-re megőrzése

Ha szeretné replikálni a hozzáférés-vezérlési listák (ACL) adatok fájlokkal együtt, amikor Data Lake Storage Gen2-re frissíti a Data Lake Storage Gen1, lásd: [megőrzése hozzáférés-vezérlési listák a Data Lake Storage Gen1](connector-azure-data-lake-storage.md#preserve-acls-from-data-lake-storage-gen1).

## <a name="mapping-data-flow-properties"></a>Data flow tulajdonságai

Tudjon meg többet [átalakítási forrás](data-flow-source.md) és [átalakítási fogadó](data-flow-sink.md) a leképezési adatok flow szolgáltatásban.

## <a name="next-steps"></a>További lépések

A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md##supported-data-stores-and-formats).

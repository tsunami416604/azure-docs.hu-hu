---
title: Adatok másolása Azure Data Lake Storage Gen1 a Data Factory használatával | Microsoft Docs
description: Megtudhatja, hogyan másolhat adatok a támogatott forrás-adattárakból a Azure Data Lake Storeba, vagy Data Lake Storeról a támogatott fogadó áruházakba Data Factory használatával.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: jingwang
ms.openlocfilehash: 4bb57190a310e1ea4b8e5c511f1acd90f53b8f09
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/09/2019
ms.locfileid: "70813462"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-using-azure-data-factory"></a>Adatok másolása Azure Data Lake Storage Gen1 a Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki a használt Azure Data Factory verzióját:"]
> * [1-es verzió](v1/data-factory-azure-datalake-connector.md)
> * [Aktuális verzió](connector-azure-data-lake-store.md)

Ez a cikk az adatok Azure Data Lake Storage Gen1ba való másolásának módját ismerteti. Azure Data Factory szolgáltatásról, olvassa el a [bevezető cikk](introduction.md).

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az Azure Data Lake Storage Gen1-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás-vagy fogadó mátrixtal](copy-activity-overview.md)
- [Adatfolyam hozzárendelése](concepts-data-flow-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)

Pontosabban, ezzel az összekötővel a következőket teheti:

- Másolja a fájlokat az alábbi hitelesítési módszerek egyikével: egyszerű szolgáltatásnév vagy felügyelt identitások az Azure-erőforrásokhoz.
- Fájlok másolása másként, vagy fájlok elemzése vagy készítése a [támogatott fájlformátumokkal és tömörítési kodekekkel](supported-file-formats-and-compression-codecs.md).

> [!IMPORTANT]
> Ha az adatokat a saját üzemeltetésű integrációs modul használatával másolja, konfigurálja a vállalati tűzfalat úgy, hogy engedélyezze `<ADLS account name>.azuredatalakestore.net` a `login.microsoftonline.com/<tenant>/oauth2/token` kimenő forgalmat az 443-es porton. Az utóbbi az Azure biztonsági jogkivonat szolgáltatás, amelyhez az integrációs modulnak kommunikálnia kell a hozzáférési token beszerzéséhez.

## <a name="get-started"></a>Bevezetés

> [!TIP]
> Az Azure Data Lake Store-összekötő használatáról a következő témakörben olvashat: az [adatAzure Data Lake Storeba való betöltés](load-azure-data-lake-store.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban információt talál a Azure Data Lake Storera vonatkozó Data Factory entitások definiálásához használt tulajdonságokról.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A Azure Data Lake Store társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A `type` tulajdonságot **AzureDataLakeStore**értékre kell beállítani. | Igen |
| dataLakeStoreUri | Az Azure Data Lake Store-fiókkal kapcsolatos információk. Ezt az információt a következő formátumok egyikét veheti fel: `https://[accountname].azuredatalakestore.net/webhdfs/v1` vagy `adl://[accountname].azuredatalakestore.net/`. | Igen |
| subscriptionId | Az Azure-előfizetés azonosítója, amelyhez a Data Lake Store fiók tartozik. | A fogadó megadása kötelező |
| resourceGroupName | Az Azure-erőforráscsoport neve, amelyhez a Data Lake Store fiók tartozik. | A fogadó megadása kötelező |
| connectVia | A [integrációs modul](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. Az Azure Integration Runtime vagy egy saját üzemeltetésű integrációs modult is használhat, ha az adattár egy magánhálózaton található. Ha ez a tulajdonság nincs megadva, a rendszer az alapértelmezett Azure Integration Runtime-t használja. |Nem |

### <a name="use-service-principal-authentication"></a>Egyszerű szolgáltatás hitelesítésének használata

Az egyszerű szolgáltatás hitelesítésének használatához regisztráljon egy alkalmazás-entitást Azure Active Directory, és adja meg Data Lake Store számára a hozzáférést. Részletes lépéseiért lásd: [szolgáltatások közötti hitelesítés](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Jegyezze fel a következő értékeket, mert a társított szolgáltatás definiálásához használja:

- Alkalmazásazonosító
- Alkalmazáskulcs
- Bérlőazonosító

>[!IMPORTANT]
> Adja meg az egyszerű szolgáltatás megfelelő engedélyét Data Lake Storeban:
>- **Forrásként**: Az **adatkezelő** > **eléréséhez**adjon meg legalább **olvasási és végrehajtási** engedélyt a mappák és almappákban található fájlok listázásához és másolásához. Emellett **olvasási** engedélyt is biztosíthat egyetlen fájl másolásához. Dönthet úgy, hogy felveszi **ezt a mappát és minden gyermeket** a rekurzív értékre, és hozzá kívánja adni **hozzáférési engedélyként és alapértelmezett engedély-bejegyzést**. A fiók szintű hozzáférés-vezérlés (IAM) nem követelmény.
>- **Fogadóként:** Az **adatkezelő** > **eléréséhez**adjon meg legalább **írási + végrehajtási** engedélyt a gyermekobjektumok létrehozásához a mappában. Dönthet úgy, hogy felveszi **ezt a mappát és minden gyermeket** a rekurzív értékre, és hozzá kívánja adni **hozzáférési engedélyként és alapértelmezett engedély-bejegyzést**. Ha Azure Integration Runtime-t használ a másoláshoz (a forrás és a fogadó is a felhőben van), a IAM-ban legalább az **olvasói** szerepkört kell biztosítania ahhoz, hogy Data Factory felderítse a Data Lake Store régióját. Ha el szeretné kerülni ezt a IAM-szerepkört, explicit módon [hozzon létre egy Azure Integration Runtime](create-azure-integration-runtime.md#create-azure-ir) -t Data Lake Store helyével. Ha például a Data Lake Store Nyugat-Európában található, hozzon létre egy "Nyugat-európai" nevű Azure Integration Runtime-t a helyhez. Társítsa őket a Data Lake Store társított szolgáltatáshoz az alábbi példában látható módon.

>[!NOTE]
>Ahhoz, hogy a mappák a gyökerétől kezdődően legyenek listázva, be kell állítania az egyszerű szolgáltatásnév engedélyét a **"végrehajtás" engedéllyel rendelkező gyökérszintű szinten**. Ez akkor igaz, ha a következőt használja:
>- **Adatmásolási eszköz** másolása másolási folyamat létrehozásához.
>- **Data Factory felhasználói felület** a kapcsolatok teszteléséhez és a mappák navigálásához a szerzői műveletek során.
>Ha a root szintű engedélyek megadásával kapcsolatos problémák merülnek fel, a szerzői műveletek, a tesztelési kapcsolatok kihagyása és a paraent elérési út megadása a megadott elérési úton lehetőséggel, majd válassza a megadott útvonalon való Tallózás lehetőséget A másolási tevékenység akkor működik, ha az egyszerű szolgáltatásnév megfelelő engedélyekkel rendelkezik a másolandó fájlok számára.

A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| servicePrincipalId | Adja meg az alkalmazás ügyfél-azonosítót. | Igen |
| servicePrincipalKey | Adja meg az alkalmazáskulcsot. Ezt a mezőt `SecureString` megjelölve tárolja biztonságosan a Data Factoryban, vagy [a Azure Key Vault tárolt titkos kulcsra hivatkozva](store-credentials-in-key-vault.md). | Igen |
| tenant | Adja meg a bérlői adatokat, például a tartománynevet vagy a bérlő AZONOSÍTÓját, amely alatt az alkalmazás található. Kérheti a viszi az egérmutatót az Azure portal jobb felső sarkában. | Igen |

**Példa:**

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

### <a name="managed-identity"></a>Felügyelt identitások használata az Azure-erőforrások hitelesítéséhez

Adat-előállító társítható egy [-identitás az Azure-erőforrások](data-factory-service-identity.md), amely az adott adat-előállító jelöli. Ezt a felügyelt identitást közvetlenül használhatja Data Lake Store hitelesítéshez, hasonlóan a saját egyszerű szolgáltatásnév használatához is. Ez lehetővé teszi, hogy ez a kijelölt gyár hozzáférjen az adatokhoz, és másolja azokat Data Lake Storeba.

Azure-erőforrások hitelesítéshez használandó felügyelt identitások:

1. [Kérje le az adat-előállító által felügyelt személyazonossági adatokat](data-factory-service-identity.md#retrieve-managed-identity) úgy, hogy átmásolja a "szolgáltatás Identity Application id" értékét a gyári környezettel együtt.
2. Adja meg a felügyelt identitás hozzáférését Data Lake Storehoz, ugyanúgy, mint az egyszerű szolgáltatásnév esetében, ezeket a megjegyzéseket követve.

>[!IMPORTANT]
> Győződjön meg arról, hogy az adat-előállító felügyelt identitásának megfelelő engedélyt ad a Data Lake Storeban:
>- **Forrásként**: Az **adatkezelő** > **eléréséhez**adjon meg legalább **olvasási és végrehajtási** engedélyt a mappák és almappákban található fájlok listázásához és másolásához. Emellett **olvasási** engedélyt is biztosíthat egyetlen fájl másolásához. Dönthet úgy, hogy felveszi **ezt a mappát és minden gyermeket** a rekurzív értékre, és hozzá kívánja adni **hozzáférési engedélyként és alapértelmezett engedély-bejegyzést**. A fiók szintű hozzáférés-vezérlés (IAM) nem követelmény.
>- **Fogadóként:** Az **adatkezelő** > **eléréséhez**adjon meg legalább **írási + végrehajtási** engedélyt a gyermekobjektumok létrehozásához a mappában. Dönthet úgy, hogy felveszi **ezt a mappát és minden gyermeket** a rekurzív értékre, és hozzá kívánja adni **hozzáférési engedélyként és alapértelmezett engedély-bejegyzést**. Ha Azure Integration Runtime-t használ a másoláshoz (a forrás és a fogadó is a felhőben van), a IAM-ban legalább az **olvasói** szerepkört kell biztosítania ahhoz, hogy Data Factory felderítse a Data Lake Store régióját. Ha el szeretné kerülni ezt a IAM-szerepkört, explicit módon [hozzon létre egy Azure Integration Runtime](create-azure-integration-runtime.md#create-azure-ir) -t Data Lake Store helyével. Társítsa őket a Data Lake Store társított szolgáltatáshoz az alábbi példában látható módon.

>[!NOTE]
>Ahhoz, hogy a mappák a gyökerétől kezdődően legyenek listázva, a **"végrehajtás" engedéllyel**kell megadnia a felügyelt identitásnak a legfelső szintű jogosultságát. Ez akkor igaz, ha a következőt használja:
>- **Adatmásolási eszköz** másolása másolási folyamat létrehozásához.
>- **Data Factory felhasználói felület** a kapcsolatok teszteléséhez és a mappák navigálásához a szerzői műveletek során.
>Ha a root szintű engedélyek megadásával kapcsolatos problémák merülnek fel, a szerzői műveletek, a tesztelési kapcsolatok kihagyása és a megadott engedéllyel rendelkező szülő elérési út megadása után válassza a megadott elérési útról való Tallózás lehetőséget. A másolási tevékenység akkor működik, ha az egyszerű szolgáltatásnév megfelelő engedélyekkel rendelkezik a másolandó fájlok számára.

Azure Data Factory a társított szolgáltatás általános Data Lake Store információi mellett nem kell tulajdonságokat megadnia.

**Példa:**

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

- A **Parquet, a tagolt szöveget, a JSON-t, a Avro és a bináris formátumot**a [parketta, a tagolt szöveg, a JSON, a Avro és a bináris formátum adatkészlet](#format-based-dataset) szakaszban találja.
- Más formátumok, például az **ork formátum**esetében tekintse meg a [más formátumú adatkészlet](#other-format-dataset) szakaszt.

### <a name="format-based-dataset"></a>Parketta, tagolt szöveg, JSON, Avro és bináris formátumú adatkészlet

Ha a **parketta, a tagolt szöveg, a JSON, a Avro és a bináris formátum**adatait szeretné másolni, tekintse meg a [parketta formátumát](format-parquet.md), a [tagolt szöveg formátumát](format-delimited-text.md), a [Avro formátumát](format-avro.md) és a [bináris formátumú](format-binary.md) cikket a Format-alapú adatkészlet és a támogatott beállítások alapján. .
A következő tulajdonságok támogatottak Azure Data Lake Store Gen1 `location` a Format-alapú adatkészlet beállítások területén:

| Tulajdonság   | Leírás                                                  | Szükséges |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Az adatkészletben `location` található Type tulajdonságot **AzureDataLakeStoreLocation**értékre kell állítani. | Igen      |
| folderPath | Egy mappa elérési útja. Ha a mappák szűréséhez helyettesítő karaktert szeretne használni, hagyja ki ezt a beállítást, és határozza meg a tevékenység forrásának beállításai között. | Nem       |
| fileName   | A fájlnév a megadott folderPath alatt. Ha helyettesítő karaktert szeretne használni a fájlok szűréséhez, hagyja ki ezt a beállítást, és határozza meg a tevékenység forrásának beállításai között. | Nem       |

> [!NOTE]
>
> A következő szakaszban említett, a Parquet vagy szöveges formátumot tartalmazó **AzureDataLakeStoreFile** -típus adatkészlete továbbra is támogatott a visszamenőleges kompatibilitás érdekében a másolási, a keresési és a GetMetaData tevékenység esetében. Ez azonban nem működik a leképezési adatfolyam-szolgáltatással. Javasoljuk, hogy ezt az új modellt használja tovább. Az Data Factory szerzői felhasználói felület ezeket az új típusokat hozza létre.

**Példa:**

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

### <a name="other-format-dataset"></a>Egyéb formátumú adatkészlet

Az adatok Azure Data Lake Store Gen1 az **ork formátumba**való másolásához a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | Az adatkészlet Type tulajdonságát **AzureDataLakeStoreFile**értékre kell állítani. |Igen |
| folderPath | A Data Lake Store mappájának elérési útja. Ha nincs megadva, a legfelső szintű mutat. <br/><br/>A helyettesítő karakteres szűrő használata támogatott. Az `*` engedélyezett helyettesítő karakterek (nulla vagy több karakternek felelnek `?` meg) és (nulla vagy egyetlen karakternek felelnek meg). Akkor `^` használja a-et, ha a tényleges mappanév helyettesítő karakterrel rendelkezik, vagy a menekülési karakter szerepel a belsejében. <br/><br/>Például: gyökérmappa/almappa/. További példákat a [mappák és a fájlok szűrésére szolgáló példákban](#folder-and-file-filter-examples)talál. |Nem |
| fileName | A megadott "folderPath" alatt lévő fájlok neve vagy helyettesítő karakteres szűrője. Ez a tulajdonság értékét nem adja meg, ha az adatkészlet mutat a mappában lévő összes fájlt. <br/><br/>A Filter `*` (szűrő) esetében a megengedett helyettesítő karakterek (nulla vagy több karakternek `?` felelnek meg) és (a nulla vagy egyetlen karakternek felel meg).<br/>-1. példa: `"fileName": "*.csv"`<br/>– 2. példa: `"fileName": "???20180427.txt"`<br/>Ha `^` a tényleges fájl neve helyettesítő karakterrel rendelkezik, vagy a menekülési karakter szerepel a következőben, használja a Escape karaktert.<br/><br/>Ha nincs megadva a fájlnév egy kimeneti adatkészlethez, és a **preserveHierarchy** nincs megadva a tevékenység fogadójában, a másolási tevékenység automatikusan létrehozza a fájlnevet a következő mintával: "*Az adatgyűjtés. [tevékenység futtatásának azonosítója GUID]. [GUID if FlattenHierarchy]. [formátum, ha konfigurálva]. [tömörítés, ha be van állítva]* ", például" az "adat. 0a405f8a-93ff-4c6f-B3BE-f69616f1df7a. txt. gz". Ha egy táblázatos forrásból másol egy tábla nevét egy lekérdezés helyett, akkor a név minta a következő: " *[Table Name]. [ Format]. [tömörítés, ha konfigurálva]* ", például" sajáttábla. csv ". |Nem |
| modifiedDatetimeStart | A fájlok szűrése a legutóbb módosított attribútum alapján. A fájlok akkor vannak kiválasztva, ha az utolsó módosítás időpontja a és `modifiedDatetimeStart` `modifiedDatetimeEnd`a közötti időtartományon belül van. Az időpontot az UTC időzónára alkalmazza a "2018-12-01T05:00:00Z" formátumban. <br/><br/> Ha ezt a beállítást szeretné elvégezni, nagy mennyiségű fájl használata esetén a rendszer az adatáthelyezés általános teljesítményét érinti. <br/><br/> A tulajdonságok lehetnek NULL értékűek, ami azt jelenti, hogy a rendszer nem alkalmazza a file Attribute szűrőt az adatkészletre. Ha `modifiedDatetimeStart` a dátum datetime értékkel `modifiedDatetimeEnd` rendelkezik, de null értékű, az azt jelenti, hogy azok a fájlok vannak kiválasztva, amelyek utolsó módosítási attribútuma nagyobb vagy egyenlő, mint a DateTime érték. Ha `modifiedDatetimeEnd` a dátum datetime értékkel `modifiedDatetimeStart` rendelkezik, de null értékű, az azt jelenti, hogy azok a fájlok vannak kiválasztva, amelyek utolsó módosítási attribútuma kisebb a DateTime értéknél.| Nem |
| modifiedDatetimeEnd | A fájlok szűrése a legutóbb módosított attribútum alapján. A fájlok akkor vannak kiválasztva, ha az utolsó módosítás időpontja a és `modifiedDatetimeStart` `modifiedDatetimeEnd`a közötti időtartományon belül van. Az időpontot az UTC időzónára alkalmazza a "2018-12-01T05:00:00Z" formátumban. <br/><br/> Ha ezt a beállítást szeretné elvégezni, nagy mennyiségű fájl használata esetén a rendszer az adatáthelyezés általános teljesítményét érinti. <br/><br/> A tulajdonságok lehetnek NULL értékűek, ami azt jelenti, hogy a rendszer nem alkalmazza a file Attribute szűrőt az adatkészletre. Ha `modifiedDatetimeStart` a dátum datetime értékkel `modifiedDatetimeEnd` rendelkezik, de null értékű, az azt jelenti, hogy azok a fájlok vannak kiválasztva, amelyek utolsó módosítási attribútuma nagyobb vagy egyenlő, mint a DateTime érték. Ha `modifiedDatetimeEnd` a dátum datetime értékkel `modifiedDatetimeStart` rendelkezik, de null értékű, az azt jelenti, hogy azok a fájlok vannak kiválasztva, amelyek utolsó módosítási attribútuma kisebb a DateTime értéknél.| Nem |
| format | Ha fájlokat szeretne másolni a fájl alapú tárolók között (bináris másolás), ugorja át a formátum szakaszt mind a bemeneti, mind a kimeneti adatkészlet-definíciókban.<br/><br/>Ha a fájlokat egy adott formátummal szeretné elemezni vagy előállítani, a következő fájlformátum-típusok támogatottak: **Szövegformátum**, **JsonFormat**, **AvroFormat**, **OrcFormat**és **ParquetFormat**. Állítsa be a **típus** tulajdonság alatt **formátum** az alábbi értékek egyikére. További információkért lásd: a [szövegformátum](supported-file-formats-and-compression-codecs.md#text-format), [JSON formátumban](supported-file-formats-and-compression-codecs.md#json-format), [Avro formátum](supported-file-formats-and-compression-codecs.md#avro-format), [Orc formátum](supported-file-formats-and-compression-codecs.md#orc-format), és [Parquet formátum ](supported-file-formats-and-compression-codecs.md#parquet-format) szakaszokat. |Nem (csak a bináris másolás esetén) |
| compression | Adja meg a típus és az adatok tömörítési szintje. További információkért lásd: [támogatott fájlformátumok és tömörítési kodek](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Támogatott típusok a következők **GZip**, **Deflate**, **BZip2**, és **ZipDeflate**.<br/>Támogatott szintek a következők **Optimal** és **leggyorsabb**. |Nem |


>[!TIP]
>Másolja egy mappában található összes fájlt, adja meg a **folderPath** csak.<br>Egy adott névvel rendelkező egyetlen fájl másolásához adja meg a **folderPath** mappát, és nevezze el a **fájlnevet** .<br>Ha egy mappában lévő fájlok egy részhalmazát szeretné másolni, akkor a **folderPath** és a **fájlnevet** egy helyettesítő szűrővel kell megadnia. 

**Példa:**

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

A tevékenységek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját lásd: [folyamatok](concepts-pipelines-activities.md). Ez a szakasz a Azure Data Lake Store forrás és a fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="azure-data-lake-store-as-source"></a>Az Azure Data Lake Store mint forrás

- A parketta, a **tagolt szöveg, a JSON, a Avro és a bináris formátum**másolásához tekintse meg a [parketta, a tagolt szöveg, a JSON, a Avro és a bináris formátum forrás](#format-based-source) szakaszát.
- Más formátumokból, például az **ork formátumból**való másoláshoz tekintse meg a [más formátumú forrás](#other-format-source) szakaszt.

#### <a name="format-based-source"></a>Parketta, tagolt szöveg, JSON, Avro és bináris formátum forrása

Ha a **parketta, a tagolt szöveg, a JSON, a Avro és a bináris formátum**adatait szeretné átmásolni, tekintse meg a [parketta formátumát](format-parquet.md), a [tagolt szöveg formátumát](format-delimited-text.md), a [Avro formátumát](format-avro.md) és a [bináris formátumú](format-binary.md) cikket a Format-alapú másolási tevékenység forrásáról beállítások.  A következő tulajdonságok támogatottak Azure Data Lake Store Gen1 a Format `storeSettings` -alapú másolási forrás beállításai alatt:

| Tulajdonság                 | Leírás                                                  | Szükséges                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | A Type tulajdonságot `storeSettings` a **AzureDataLakeStoreReadSetting**értékre kell állítani. | Igen                                           |
| recursive                | Azt jelzi, hogy az adatok olvasható rekurzív módon az almappák vagy csak a megadott mappába. Ha a rekurzív értéke TRUE (igaz), a fogadó pedig egy fájl alapú tároló, akkor a fogadó nem másol vagy hoz létre üres mappát vagy almappát. Engedélyezett értékek a következők **igaz** (alapértelmezett), és **hamis**. | Nem                                            |
| wildcardFolderPath       | A mappa elérési útja helyettesítő karakterekkel a forrás mappák szűréséhez. <br>Az `*` engedélyezett helyettesítő karakterek (nulla vagy több karakternek felelnek `?` meg) és (nulla vagy egyetlen karakternek felelnek meg). Akkor `^` használja a-et, ha a tényleges mappanév helyettesítő karakterrel rendelkezik, vagy a menekülési karakter szerepel a belsejében. <br>További példákat a [mappák és a fájlok szűrésére szolgáló példákban](#folder-and-file-filter-examples)talál. | Nem                                            |
| wildcardFileName         | A forrásfájl szűréséhez a megadott folderPath/wildcardFolderPath helyettesítő karaktereket tartalmazó fájlnév. <br>Az `*` engedélyezett helyettesítő karakterek (nulla vagy több karakternek felelnek `?` meg) és (nulla vagy egyetlen karakternek felelnek meg). Akkor `^` használja a-et, ha a tényleges mappanév helyettesítő karakterrel rendelkezik, vagy a menekülési karakter szerepel a belsejében. További példákat a [mappák és a fájlok szűrésére szolgáló példákban](#folder-and-file-filter-examples)talál. | Igen, `fileName` ha nincs megadva az adatkészletben |
| modifiedDatetimeStart    | A fájlok szűrése a legutóbb módosított attribútum alapján. A fájlok akkor vannak kiválasztva, ha az utolsó módosítás időpontja a és `modifiedDatetimeStart` `modifiedDatetimeEnd`a közötti időtartományon belül van. Az időpontot az UTC időzónára alkalmazza a "2018-12-01T05:00:00Z" formátumban. <br> A tulajdonságok lehetnek NULL értékűek, ami azt jelenti, hogy a rendszer nem alkalmazza a file Attribute szűrőt az adatkészletre. Ha `modifiedDatetimeStart` a dátum datetime értékkel `modifiedDatetimeEnd` rendelkezik, de null értékű, az azt jelenti, hogy azok a fájlok vannak kiválasztva, amelyek utolsó módosítási attribútuma nagyobb vagy egyenlő, mint a DateTime érték. Ha `modifiedDatetimeEnd` a dátum datetime értékkel `modifiedDatetimeStart` rendelkezik, de null értékű, az azt jelenti, hogy azok a fájlok vannak kiválasztva, amelyek utolsó módosítási attribútuma kisebb a DateTime értéknél. | Nem                                            |
| modifiedDatetimeEnd      | Ugyanaz, mint a fenti.                                               | Nem                                            |
| maxConcurrentConnections | A tárolási tárolóhoz való kapcsolódáshoz szükséges kapcsolatok száma egyidejűleg. Csak akkor kell megadni, ha az egyidejű kapcsolódást szeretné korlátozni az adattárral. | Nem                                            |

> [!NOTE]
> A Parquet és a tagolt szöveges formátum esetében a következő szakaszban említett **AzureDataLakeStoreSource** -típus másolási tevékenységének támogatása továbbra is támogatott a visszamenőleges kompatibilitás érdekében. Javasoljuk, hogy ezt az új modellt használja tovább. Az Data Factory szerzői felhasználói felület ezeket az új típusokat hozza létre.

**Példa:**

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

#### <a name="other-format-source"></a>Egyéb formátum forrása

Az adatok Azure Data Lake Store Gen1 az **ork formátumban**való másolásához a következő tulajdonságok támogatottak a másolási tevékenység **forrása** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának tulajdonságátAzureDataLakeStoreSourceértékrekell`type` állítani. |Igen |
| recursive | Azt jelzi, hogy az adatok olvasható rekurzív módon az almappák vagy csak a megadott mappába. Ha `recursive` a értéke TRUE (igaz), a fogadó pedig egy fájl alapú tároló, akkor a fogadó nem másol vagy hoz létre üres mappát vagy almappát. Engedélyezett értékek a következők **igaz** (alapértelmezett), és **hamis**. | Nem |
| maxConcurrentConnections | Az adattárhoz való kapcsolódáshoz szükséges kapcsolatok száma egyszerre. Csak akkor kell megadni, ha az egyidejű kapcsolódást szeretné korlátozni az adattárral. | Nem |

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

- A parketta, a **tagolt szöveg, a JSON, a Avro és a bináris formátum**másolásához tekintse meg a [Parquet, a tagolt szöveget, a JSON-t, a Avro és a bináris formátumú mosogató](#format-based-sink) szakaszt.
- Más formátumokba, például az **ork/JSON formátumba**való másoláshoz tekintse meg a [más Format mosogató](#other-format-sink) szakaszt.

#### <a name="format-based-sink"></a>Parketta, tagolt szöveg, JSON, Avro és bináris formátumú fogadó

Az adatgyűjtés a **parketta, a tagolt szöveg, a JSON, a Avro és a bináris formátum**között, a [parketta formátuma](format-parquet.md), a [tagolt szöveg formátuma](format-delimited-text.md), a [Avro formátum](format-avro.md) és a [bináris formátum](format-binary.md) cikk a Format-alapú másolási tevékenység fogadóján és a támogatott beállítások.  A következő tulajdonságok támogatottak Azure Data Lake Store Gen1 a Format `storeSettings` -alapú másolási fogadó beállításai alatt:

| Tulajdonság                 | Leírás                                                  | Szükséges |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | A Type tulajdonságot `storeSettings` a **AzureDataLakeStoreWriteSetting**értékre kell állítani. | Igen      |
| copyBehavior             | A másolási viselkedés határozza meg, ha a forrás fájlok fájlalapú adattárból.<br/><br/>Engedélyezett értékek a következők:<br/><b>-PreserveHierarchy (alapértelmezett)</b>: Megőrzi a fájl hierarchiáját a célmappában. A forrásfájl relatív elérési útja a forrás mappájához azonos a célfájl relatív elérési útjával.<br/><b>-FlattenHierarchy</b>: A forrás mappából származó összes fájl a célmappa első szintjén található. A cél fájlok automatikusan létrehozott névvel rendelkeznek. <br/><b>-MergeFiles</b>: Egyesít minden fájlt a forrás mappájából egy fájlba. A fájl neve meg van adva, az egyesített fájlnév-e a megadott néven. Ellenkező esetben egy automatikusan létrehozott nevét. | Nem       |
| maxConcurrentConnections | Az adattárhoz való kapcsolódáshoz szükséges kapcsolatok száma egyszerre. Csak akkor kell megadni, ha az egyidejű kapcsolódást szeretné korlátozni az adattárral. | Nem       |

> [!NOTE]
> A Parquet és a tagolt szöveges formátum esetében a következő szakaszban említett **AzureDataLakeStoreSink** -típus másolási tevékenység fogadója továbbra is támogatott a visszamenőleges kompatibilitás érdekében. Javasoljuk, hogy ezt az új modellt használja tovább. Az Data Factory szerzői felhasználói felület ezeket az új típusokat hozza létre.

**Példa:**

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

#### <a name="other-format-sink"></a>Egyéb formátumú fogadó

Ha az Adatmásolást az **ork formátumba**Azure Data Lake Store Gen1 szeretné másolni, a **fogadó szakasz a** következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadójának tulajdonságátAzureDataLakeStoreSinkértékrekell`type` állítani. |Igen |
| copyBehavior | A másolási viselkedés határozza meg, ha a forrás fájlok fájlalapú adattárból.<br/><br/>Engedélyezett értékek a következők:<br/><b>-PreserveHierarchy (alapértelmezett)</b>: Megőrzi a fájl hierarchiáját a célmappában. A forrásfájl relatív elérési útja a forrás mappájához azonos a célfájl relatív elérési útjával.<br/><b>-FlattenHierarchy</b>: A forrás mappából származó összes fájl a célmappa első szintjén található. A cél fájlok automatikusan létrehozott névvel rendelkeznek. <br/><b>-MergeFiles</b>: Egyesít minden fájlt a forrás mappájából egy fájlba. A fájl neve meg van adva, az egyesített fájlnév-e a megadott néven. Ellenkező esetben a fájl neve automatikusan létrejön. | Nem |
| maxConcurrentConnections | Az adattárhoz való kapcsolódáshoz szükséges kapcsolatok száma egyszerre. Csak akkor kell megadni, ha az egyidejű kapcsolódást szeretné korlátozni az adattárral. | Nem |

**Példa:**

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

### <a name="folder-and-file-filter-examples"></a>Példák a mappák és a fájlok szűrésére

Ez a szakasz a mappa elérési útjának és fájlnevének a helyettesítő karakteres szűrőkkel való viselkedését írja le.

| folderPath | fileName | recursive | A forrás mappa szerkezete és a szűrő eredménye (a **félkövérrel szedett** fájlok beolvasása)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (Üres, alapértelmezett használata) | false | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (Üres, alapértelmezett használata) | true | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="examples-of-behavior-of-the-copy-operation"></a>Példák a másolási művelet viselkedésére

Ez a szakasz ismerteti a másolási művelet eredményét a különböző kombinációk `recursive` és `copyBehavior` értékek esetében.

| recursive | copyBehavior | Forrás mappastruktúra | Eredményül kapott cél |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A célként megadott Mappa1 ugyanazzal a struktúrával jön létre, mint a forrás:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A cél mappa1 jön létre az alábbi struktúra használatával: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet, a file1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File5 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A cél mappa1 jön létre az alábbi struktúra használatával: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;A file1 + Fájl2 + fájl3 + File4 + File5 tartalma egyetlen fájlba van egyesítve, és automatikusan generált fájlnévvel. |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A cél mappa1 jön létre az alábbi struktúra használatával:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>A fájl3, a File4 és a File5 Subfolder1 nem vesznek fel. |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A cél mappa1 jön létre az alábbi struktúra használatával:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet, a file1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File2<br/><br/>A fájl3, a File4 és a File5 Subfolder1 nem vesznek fel. |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A cél mappa1 jön létre az alábbi struktúra használatával:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;A file1 + Fájl2 tartalma egy fájlba van egyesítve, automatikusan létrehozott fájlnévvel. automatikusan létrehozott nevet, a file1 kiszolgálón<br/><br/>A fájl3, a File4 és a File5 Subfolder1 nem vesznek fel. |

## <a name="preserve-acls-to-data-lake-storage-gen2"></a>ACL-ek megőrzése Data Lake Storage Gen2

Ha a hozzáférés-vezérlési listákat (ACL-eket) az adatfájlokkal együtt szeretné replikálni Data Lake Storage Gen1ról Data Lake Storage Gen2ra, tekintse meg az [ACL-ek megőrzése Data Lake Storage Gen1ról](connector-azure-data-lake-storage.md#preserve-acls-from-data-lake-storage-gen1)című témakört.

## <a name="mapping-data-flow-properties"></a>Adatfolyam-tulajdonságok leképezése

További információ a [forrás-átalakításról](data-flow-source.md) és a fogadó [transzformációról](data-flow-sink.md) a leképezési adatfolyam funkcióban.

## <a name="next-steps"></a>További lépések

A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md##supported-data-stores-and-formats).

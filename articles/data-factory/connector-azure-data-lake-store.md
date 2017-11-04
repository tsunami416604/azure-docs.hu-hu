---
title: "Adatok másolása az Azure Data Lake Store használatának a Data Factory |} Microsoft Docs"
description: "Ismerje meg az adatok másolása az Azure Data Lake Store áruházakból a támogatott forráshierarchiából adatok (vagy) a Data Lake Store támogatott fogadó áruházak Data Factory használatával."
services: data-factory
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 11/01/2017
ms.author: jingwang
ms.openlocfilehash: 5e6e56dd7ce1a16cadf35f9efe959ac490a65071
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
---
# <a name="copy-data-to-or-from-azure-data-lake-store-by-using-azure-data-factory"></a>Másolja a adatok vagy az Azure Data Lake Store az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-azure-datalake-connector.md)
> * [2. verzió – Előzetes verzió](connector-azure-data-lake-store.md)

Ez a cikk ismerteti, hogyan használható a másolási tevékenység során az Azure Data Factory másolja az adatokat, és az Azure Data Lake Store. Buildekről nyújtanak a [másolása tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [Azure Data Lake Store-összekötőt a V1](v1/data-factory-azure-datalake-connector.md).

## <a name="supported-scenarios"></a>Támogatott esetek

Adatok másolása az egyetlen támogatott forrás adattár az Azure Data Lake Store, vagy adatok másolása az Azure Data Lake Store az egyetlen támogatott fogadó adattár. A másolási tevékenység által támogatott adatforrások vagy mosdók adattárolókhoz listájáért lásd: a [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban Ez az Azure Data Lake Store az összekötő támogatja:

- Fájlokat másol használatával **egyszerű** hitelesítés.
- Mint a fájlok másolása-, vagy a fájlok elemzése/létrehozása a [támogatott formátumok és a tömörítési kodek](supported-file-formats-and-compression-codecs.md).

## <a name="get-started"></a>Bevezetés
A másolási tevékenység során .NET SDK, a Python SDK, az Azure PowerShell, a REST API-t vagy a Azure Resource Manager-sablon használatával hozhat létre egy folyamatot. Lásd: [másolási tevékenység oktatóanyag](quickstart-create-data-factory-dot-net.md) hozzon létre egy folyamatot a másolási tevékenység részletes útmutatóját. 

A következő szakaszok részletesen bemutatják adat-előállító tartozó entitások meghatározásához az Azure Data lake Store használt tulajdonságokat.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok

Létrehozhat egy Azure Data Lake Store kapcsolódó szolgáltatás szolgáltatás egyszerű hitelesítéssel.

Szolgáltatás egyszerű hitelesítést használ, egy alkalmazás entitás regisztrálni kell az Azure Active Directory (Azure AD), és hozzáférést biztosíthat, Data Lake Store-bA. Részletes útmutató: [szolgáltatások közötti hitelesítési](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Jegyezze fel a következő érték, melynek segítségével határozza meg a társított szolgáltatás:

- Alkalmazásazonosító
- Alkalmazás kulcs
- Bérlőazonosító

[!TIP]
> Győződjön meg arról, hogy biztosítsa a szolgáltatás egyszerű megfelelő engedélyeket az Azure Data Lake Store:
>- Forrásaként, adja meg legalább **olvasási + Execute** adat-hozzáférési listán, és másolja át egy mappa tartalmát vagy **olvasási** engedéllyel egy fájl másolása. Nem követelmény a fióknak szintű hozzáférés-vezérlés.
>- Mint fogadó, adja meg legalább **írási + hajtható végre** adat-hozzáférési engedélye a mappában található elemek gyermek létrehozására. Azure IR használatakor építve a másolás és (a forrás- és fogadó vannak felhő), lehetővé teszik a Data Factory Data Lake Store régió észleli, hogy adja meg legalább **olvasó** fiók hozzáférés-vezérlés (IAM) szerepkörhöz. Ha el szeretné kerülni ezt a IAM-szerepkört [hozzon létre egy Azure-IR](create-azure-integration-runtime.md#create-azure-ir) a helyét, valamint a Data Lake Store, a Data Lake Store-ban társítása a társított szolgáltatás a következő példa.

A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot meg kell **AzureDataLakeStore**. | Igen |
| dataLakeStoreUri | Az Azure Data Lake Store-fiókja adatait. Ez az információ időt vesz igénybe, a következő formátumok egyikének: `https://[accountname].azuredatalakestore.net/webhdfs/v1` vagy `adl://[accountname].azuredatalakestore.net/`. | Igen |
| servicePrincipalId | Adja meg az alkalmazás ügyfél-azonosítót. | Igen |
| servicePrincipalKey | Adja meg az alkalmazás kulcsot. Ez a mező megjelölése a SecureString. | Igen |
| Bérlői | Adja meg a bérlői adatokat (tartomány nevét vagy a bérlő azonosító) alatt az alkalmazás található. Azt az Azure-portál jobb felső sarkában az egér rámutató által kérheti le. | Igen |
| subscriptionId | Azure-előfizetése Azonosítóját, amelyhez a Data Lake Store-fiók tartozik. | A fogadó szükséges |
| erőforráscsoport-név | Azure erőforráscsoport-név, amely a Data Lake Store-fiók tartozik. | A fogadó szükséges |
| connectVia | A [integrációs futásidejű](concepts-integration-runtime.md) csatlakozni az adattárolóhoz használandó. Használhat Azure integrációs futásidejű vagy Self-hosted integrációs futásidejű (amennyiben az adattároló magánhálózaton található). Ha nincs megadva, akkor használja az alapértelmezett Azure integrációs futásidejű. |Nem |

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

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Szakaszok és meghatározása adatkészletek esetében elérhető tulajdonságok teljes listájáért tekintse meg az adatkészletek cikket. Ez a témakör az Azure Data Lake Store dataset által támogatott tulajdonságokról.

Adatok másolása az Azure Data Lake Store, állítsa be a type tulajdonságot az adathalmaz **AzureDataLakeStoreFile**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adathalmaz értékre kell állítani: **AzureDataLakeStoreFile** |Igen |
| folderPath | A tároló és a file storage mappa elérési útja. Példa: rootfolder/almappa / |Igen |
| fileName | Adja meg a fájl nevét a **folderPath** Ha át kívánja másolni az egy adott fájlt. Ha nem ad meg ehhez a tulajdonsághoz értéket, a DataSet adatkészlet mutat a mappában lévő összes fájlt.<br/><br/>Ha nincs megadva fájlnév egy kimeneti adatkészlet és **preserveHierarchy** nincs megadva tevékenység fogadó, a másolási tevékenység során automatikusan létrehozza a fájl nevét a következő formátumban: `Data.[activity run id GUID].[GUID if FlattenHierarchy].[format if configured].[compression if configured]`. Például: `Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz`. |Nem |
| Formátumban | Ha azt szeretné, hogy **másolja a fájlokat-van** közötti fájlalapú tárolók (bináris másolhatja azokat), hagyja ki a Formátum szakasz mindkét bemeneti és kimeneti adatkészlet-definíciókban.<br/><br/>Szeretne elemezni, vagy egy adott formátumú fájlok létrehozása, ha a következő fájl formátuma típusok támogatottak: **szöveges**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Állítsa be a **típus** tulajdonság a formátuma a következő értékek egyikét. További információkért lásd: [szövegformátum](supported-file-formats-and-compression-codecs.md#text-format), [Json formátumban](supported-file-formats-and-compression-codecs.md#json-format), [az Avro formátum](supported-file-formats-and-compression-codecs.md#avro-format), [Orc formátum](supported-file-formats-and-compression-codecs.md#orc-format), és [Parquet formátum](supported-file-formats-and-compression-codecs.md#parquet-format) szakaszok. |Nem (csak a bináris másolásának esetéhez) |
| Tömörítés | Adja meg a típus és az adatok tömörítése szintjét. További információkért lásd: [támogatott formátumok és a tömörítési kodek](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Támogatott típusok a következők: **GZip**, **Deflate**, **BZip2**, és **ZipDeflate**.<br/>Támogatott szintek a következők: **Optimal** és **leggyorsabb**. |Nem |

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
            "fileName": "myfile.csv.gz",
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

Szakaszok és a rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a témakör az Azure Data Lake-forrás és a fogadó által támogatott tulajdonságokról.

### <a name="azure-data-lake-store-as-source"></a>Azure Data Lake Store forrásaként

Adatok másolása az Azure Data Lake Store, állítsa be a forrás típusa a másolási tevékenység **AzureDataLakeStoreSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot a másolási tevékenység forrás értékre kell állítani: **AzureDataLakeStoreSource** |Igen |
| Rekurzív | Azt jelzi, hogy az adatok olvasható rekurzív módon az almappák vagy csak a megadott mappát.<br/>Két érték engedélyezett: **igaz** (alapértelmezett), **hamis** | Nem |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromADLS",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS input dataset name>",
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

### <a name="azure-data-lake-store-as-sink"></a>Azure Data Lake Store mint fogadó

Adatok másolása az Azure Blob, állítsa be a fogadó típusa a másolási tevékenység **AzureDataLakeStoreSink**. A következő tulajdonságok támogatottak a **fogadó** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadó type tulajdonsága értékre kell állítani: **AzureDataLakeStoreSink** |Igen |
| copyBehavior | Meghatározza a másolási viselkedését, ha az adatforrás fájlok fájlalapú adattárolóból.<br/><br/>Engedélyezett értékek a következők:<br/><b>-PreserveHierarchy (alapértelmezett)</b>: őrzi meg a fájl hierarchia a célmappában. A következő forrásfájl forrásmappához relatív elérési a relatív elérési út a cél-fájlját és a célmappa megegyezik.<br/><b>-FlattenHierarchy</b>: a forrásmappából a fájlok a célmappában első szintjét is. A fájlok céljaként automatikusan létrehozott nevet adni. <br/><b>-Mergefiles típusú</b>: egy fájl összes fájlt a forrásmappából egyesíti. Ha a fájl/Blob neve meg van adva, az egyesített neve legyen a megadott név; Ellenkező esetben lenne automatikusan létrehozott fájl nevét. | Nem |

**Példa**

```json
"activities":[
    {
        "name": "CopyToADLS",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS output dataset name>",
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

### <a name="recursive-and-copybehavior-examples"></a>rekurzív és copyBehavior példák

Ez a szakasz ismerteti az eredményül kapott viselkedéstől rekurzív és copyBehavior kombinációk a másolási művelet.

| Rekurzív | copyBehavior | Forrás mappaszerkezet | Eredményül kapott cél |
|:--- |:--- |:--- |:--- |
| Igaz |preserveHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | a célmappa mappa1 forrásaként azonos struktúrájú jön létre:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| Igaz |flattenHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | a cél az alábbi szerkezettel mappa1 jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet a file1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File5 |
| Igaz |mergefiles típusú | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | a cél az alábbi szerkezettel mappa1 jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + fájl3 + File4 + 5 fájl tartalmát egy fájl automatikusan létrehozott fájlnévvel egyesülnek |
| hamis |preserveHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | a célmappa mappa1 jön létre a következő struktúra<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Fájl3, File4 és File5 Subfolder1 nem átveszik. |
| hamis |flattenHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | a célmappa mappa1 jön létre a következő struktúra<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet a file1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File2<br/><br/>Fájl3, File4 és File5 Subfolder1 nem átveszik. |
| hamis |mergefiles típusú | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | a célmappa mappa1 jön létre a következő struktúra<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Egy fájl automatikusan létrehozott fájlnévvel egyesített file1 + File2 tartalma. automatikusan létrehozott nevet a file1 kiszolgálón<br/><br/>Fájl3, File4 és File5 Subfolder1 nem átveszik. |

## <a name="next-steps"></a>Következő lépések
Támogatott források és mosdók által a másolási tevékenység során az Azure Data Factory adattárolókhoz listájáért lásd: [adattárolókhoz támogatott](copy-activity-overview.md##supported-data-stores-and-formats).
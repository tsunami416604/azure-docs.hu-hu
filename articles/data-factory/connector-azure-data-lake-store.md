---
title: Adatok másolása az Azure Data Lake Store használatának a Data Factory |} Microsoft Docs
description: Ismerje meg az adatok másolása az Azure Data Lake Store áruházakból a támogatott forráshierarchiából adatok (vagy) a Data Lake Store támogatott fogadó áruházak Data Factory használatával.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: f346accd318689bbaa10080111d07e3fedd84556
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-to-or-from-azure-data-lake-store-by-using-azure-data-factory"></a>Másolja a adatok vagy az Azure Data Lake Store az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-azure-datalake-connector.md)
> * [2. verzió – Előzetes verzió](connector-azure-data-lake-store.md)

Ez a cikk ismerteti, hogyan használható a másolási tevékenység során az Azure Data Factory másolja az adatokat, és az Azure Data Lake Store. Buildekről nyújtanak a [másolása tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [Azure Data Lake Store-összekötőt a V1](v1/data-factory-azure-datalake-connector.md).

## <a name="supported-capabilities"></a>Támogatott képességei

Adatok másolása az egyetlen támogatott forrás adattár az Azure Data Lake Store, vagy adatok másolása az Azure Data Lake Store az egyetlen támogatott fogadó adattár. A másolási tevékenység által támogatott adatforrások vagy mosdók adattárolókhoz listájáért lásd: a [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban Ez az Azure Data Lake Store az összekötő támogatja:

- Fájlokat másol használatával **egyszerű** vagy **-identitás (MSI)** hitelesítés.
- Mint a fájlok másolása-, vagy a fájlok elemzése/létrehozása a [támogatott formátumok és a tömörítési kodek](supported-file-formats-and-compression-codecs.md).

> [!IMPORTANT]
> Ha a Másolás Self-hosted integrációs futásidejű használatával konfigurálása a vállalati tűzfal engedélyezze a kimenő forgalmat `<ADLS account name>.azuredatalakestore.net` és `login.microsoftonline.com/<tenant>/oauth2/token` a 443-as porton. Azure biztonsági jogkivonat szolgáltatás (STS) IR megszerezni a hozzáférési jogkivonatot kell kommunikációt nem.

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok részletesen bemutatják adat-előállító tartozó entitások meghatározásához az Azure Data lake Store használt tulajdonságokat.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok

A következő tulajdonságok esetén a társított szolgáltatásnak Azure Data Lake Store támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot meg kell **AzureDataLakeStore**. | Igen |
| dataLakeStoreUri | Az Azure Data Lake Store-fiókja adatait. Ez az információ időt vesz igénybe, a következő formátumok egyikének: `https://[accountname].azuredatalakestore.net/webhdfs/v1` vagy `adl://[accountname].azuredatalakestore.net/`. | Igen |
| subscriptionId | Azure-előfizetése Azonosítóját, amelyhez a Data Lake Store-fiók tartozik. | A fogadó szükséges |
| resourceGroupName | Azure erőforráscsoport-név, amely a Data Lake Store-fiók tartozik. | A fogadó szükséges |
| connectVia | A [integrációs futásidejű](concepts-integration-runtime.md) csatlakozni az adattárolóhoz használandó. Használhat Azure integrációs futásidejű vagy Self-hosted integrációs futásidejű (amennyiben az adattároló magánhálózaton található). Ha nincs megadva, akkor használja az alapértelmezett Azure integrációs futásidejű. |Nem |

Tekintse meg a következő szakaszok további tulajdonságokat és más hitelesítési típusok JSON-példák osztályban:

- [Szolgáltatás egyszerű hitelesítést használó](#using-service-principal-authentication)
- [Felügyelt identitás-hitelesítéssel](#using-managed-service-identity-authentication)

### <a name="using-service-principal-authentication"></a>Szolgáltatás egyszerű hitelesítést használó

Szolgáltatás egyszerű hitelesítést használ, egy alkalmazás entitás regisztrálni kell az Azure Active Directory (Azure AD), és hozzáférést biztosíthat, Data Lake Store-bA. Részletes útmutató: [szolgáltatások közötti hitelesítési](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Jegyezze fel a következő érték, melynek segítségével határozza meg a társított szolgáltatás:

- Alkalmazásazonosító
- Alkalmazás kulcs
- Bérlőazonosító

>[!IMPORTANT]
> Győződjön meg arról, hogy biztosítsa a szolgáltatás egyszerű megfelelő engedélyeket az Azure Data Lake Store:
>- **Forrásként**, az adatok explorer -> hozzáférés, adja meg legalább **olvasási + Execute** listában, és másolja a fájlokat a mappa/almappa, vagy **olvasási** engedéllyel egy fájl; másolja, majd válassza ki a Adja hozzá **egy hozzáférési és egy alapértelmezett engedélybejegyzés**. Nem követelmény a fióknak szintű hozzáférés-vezérlés (IAM).
>- **Mint fogadó**, az adatok explorer -> hozzáférés, adja meg legalább **írási + hajtható végre** hozhatók létre gyermek elemek a mappában, és szeretné hozzáadni, mint a **egy hozzáférési és egy alapértelmezett engedélybejegyzés**. Ha Azure-IR segítségével másolása (a forrás- és fogadó vannak felhő), a hozzáférés-vezérlés (IAM), adja meg legalább **olvasó** ahhoz, hogy a Data Factory észleli a Data Lake Store régió lehetővé szerepkör. Ha szeretné-e a IAM szerepkör elkerüléséhez explicit módon [hozzon létre egy Azure-IR](create-azure-integration-runtime.md#create-azure-ir) a helyét, valamint a Data Lake Store, a Data Lake Store-ban társítása a társított szolgáltatás a következő példa.

A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| servicePrincipalId | Adja meg az alkalmazás ügyfél-azonosítót. | Igen |
| servicePrincipalKey | Adja meg az alkalmazás kulcsot. Ez a mező megjelölése a SecureString tárolja biztonságos helyen, a Data factoryban vagy [hivatkozik az Azure Key Vault tárolt titkos kulcs](store-credentials-in-key-vault.md). | Igen |
| bérlő | Adja meg a bérlői adatokat (tartomány nevét vagy a bérlő azonosító) alatt az alkalmazás található. Azt az Azure-portál jobb felső sarkában az egér rámutató által kérheti le. | Igen |

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

### <a name="using-managed-service-identity-authentication"></a>Felügyelt identitás-hitelesítéssel

Egy adat-előállító társítható egy [identitás](data-factory-service-identity.md), amely jelenti, hogy az adott adat-előállítóban. A szolgáltatásidentitás közvetlenül hasonló saját egyszerű szolgáltatásnév a Data Lake Store-hitelesítés használható. A Data Lake Store a/való lehetővé teszi az adatokhoz hozzáférést, és másolja a kijelölt gyári.

Felügyelt szolgáltatás identitásának (MSI) hitelesítés használatára:

1. [Beolvasása a data factory szolgáltatásidentitás](data-factory-service-identity.md#retrieve-service-identity) a "SERVICE IDENTITÁS alkalmazás Azonosítójának értéke" jön létre, valamint a gyári másolásával.
2. A szolgáltatás identitás hozzáférési jogot Data Lake Store szolgáltatás egyszerű következő alábbi megjegyzések az azonos módon.

>[!IMPORTANT]
> Győződjön meg arról, hogy az engedélyeket a data factory szolgáltatás identitás megfelelő az Azure Data Lake Store:
>- **Forrásként**, az adatok explorer -> hozzáférés, adja meg legalább **olvasási + Execute** listában, és másolja a fájlokat a mappa/almappa, vagy **olvasási** engedéllyel egy fájl; másolja, majd válassza ki a Adja hozzá **egy hozzáférési és egy alapértelmezett engedélybejegyzés**. Nem követelmény a fióknak szintű hozzáférés-vezérlés (IAM).
>- **Mint fogadó**, az adatok explorer -> hozzáférés, adja meg legalább **írási + hajtható végre** hozhatók létre gyermek elemek a mappában, és szeretné hozzáadni, mint a **egy hozzáférési és egy alapértelmezett engedélybejegyzés**. Ha Azure-IR segítségével másolása (a forrás- és fogadó vannak felhő), a hozzáférés-vezérlés (IAM), adja meg legalább **olvasó** ahhoz, hogy a Data Factory észleli a Data Lake Store régió lehetővé szerepkör. Ha szeretné-e a IAM szerepkör elkerüléséhez explicit módon [hozzon létre egy Azure-IR](create-azure-integration-runtime.md#create-azure-ir) a helyét, valamint a Data Lake Store, a Data Lake Store-ban társítása a társított szolgáltatás a következő példa.

Az Azure Data Factoryben nem kell határozhat meg semmilyen mellett az általános Data Lake Store-információkat a szolgáltatásnak.

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
| Rekurzív | Azt jelzi, hogy az adatok olvasható rekurzív módon az almappák vagy csak a megadott mappát. Megjegyzés: Ha a rekurzív értéke true, és a fogadó fájlalapú tároló, üres mappa/alterület-folder nem lesz másolva vagy hozható létre a fogadó.<br/>Két érték engedélyezett: **igaz** (alapértelmezett), **hamis** | Nem |

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

Adatok másolása az Azure Data Lake Store, állítsa be a fogadó típusa másolási tevékenységre **AzureDataLakeStoreSink**. A következő tulajdonságok támogatottak a **fogadó** szakasz:

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
| true |preserveHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | a célmappa mappa1 forrásaként azonos struktúrájú jön létre:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | a cél az alábbi szerkezettel mappa1 jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet a file1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File5 |
| true |mergeFiles | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | a cél az alábbi szerkezettel mappa1 jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + fájl3 + File4 + 5 fájl tartalmát egy fájl automatikusan létrehozott fájlnévvel egyesülnek |
| false |preserveHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | a célmappa mappa1 jön létre a következő struktúra<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Fájl3, File4 és File5 Subfolder1 nem átveszik. |
| false |flattenHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | a célmappa mappa1 jön létre a következő struktúra<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet a file1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File2<br/><br/>Fájl3, File4 és File5 Subfolder1 nem átveszik. |
| false |mergeFiles | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | a célmappa mappa1 jön létre a következő struktúra<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Egy fájl automatikusan létrehozott fájlnévvel egyesített file1 + File2 tartalma. automatikusan létrehozott nevet a file1 kiszolgálón<br/><br/>Fájl3, File4 és File5 Subfolder1 nem átveszik. |

## <a name="next-steps"></a>További lépések
Támogatott források és mosdók által a másolási tevékenység során az Azure Data Factory adattárolókhoz listájáért lásd: [adattárolókhoz támogatott](copy-activity-overview.md##supported-data-stores-and-formats).

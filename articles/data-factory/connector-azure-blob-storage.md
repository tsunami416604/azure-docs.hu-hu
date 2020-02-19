---
title: Az Azure Blob Storage-ban tárolt adatmásolási és-átalakítási funkciók
description: Megtudhatja, hogyan másolhat adatok a blob Storage-ba és a-ból, és hogyan alakíthatja át a blob Storage-ban lévő adatok Data Factory használatával.
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/17/2020
ms.openlocfilehash: f21c99b18102adc9d43c21964e51b7c7b769771e
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2020
ms.locfileid: "77431213"
---
# <a name="copy-and-transform-data-in-azure-blob-storage-by-using-azure-data-factory"></a>Az Azure Blob Storage-ban lévő Adatmásolás és-átalakítás Azure Data Factory használatával

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-azure-blob-connector.md)
> * [Aktuális verzió](connector-azure-blob-storage.md)

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok és az Azure Blob Storage-ba történő másolásához, valamint az adatok az Azure Blob Storage-ban való átalakításához. A Azure Data Factoryről a [bevezető cikkben](introduction.md)olvashat bővebben.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az Azure Blob-összekötő a következő tevékenységek esetében támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Adatfolyam hozzárendelése](concepts-data-flow-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)
- [Tevékenység törlése](delete-activity.md)

Másolási tevékenység esetén a blob Storage-összekötő a következőket támogatja:

- Blobok másolása, és az általános célú Azure storage-fiókok és a gyakori és ritka elérésű blob storage-bA. 
- Blobok másolása az Azure-erőforrások hitelesítések fiókkulcs, a szolgáltatás közös hozzáférésű jogosultságkódot, a fő- vagy felügyelt szolgáltatásidentitások használatával.
- Hozzáfűző BLOB másolása letiltása, vagy a lapblobok és az adatok másolása csak blokkblobok.
- A Blobok másolása vagy elemzése vagy a Blobok generálása [támogatott fájlformátumokkal és tömörítési kodekekkel](supported-file-formats-and-compression-codecs.md)történik.
- A [fájl metaadatainak megőrzése a másolás során](#preserve-metadata-during-copy).

>[!IMPORTANT]
>Ha engedélyezi a **megbízható Microsoft-szolgáltatások számára a Storage-fiók elérésének engedélyezése** beállítást az Azure Storage-tűzfal beállításainál, és az Azure Integration Runtime használatával szeretne csatlakozni a blob Storagehoz, a [felügyelt identitások hitelesítését](#managed-identity)kell használnia.

## <a name="get-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok segítségével határozhatók meg a Data Factory-entitások adott Blob Storage-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Az Azure Blob-összekötő a következő hitelesítési típusok támogatása, tekintse meg a megfelelő szakaszban talál:

- [Fiók kulcsának hitelesítése](#account-key-authentication)
- [Közös hozzáférésű aláírások hitelesítése](#shared-access-signature-authentication)
- [Egyszerű szolgáltatás hitelesítése](#service-principal-authentication)
- [Felügyelt identitások az Azure-erőforrások hitelesítéséhez](#managed-identity)

>[!NOTE]
>Ha az adatok SQL Data Warehouseba való betöltéséhez a forrás-vagy előkészítési blob Storage-t Virtual Network-végponttal konfigurálja, akkor a rendszer a következőt használja a felügyelt identitások hitelesítéséhez Integration Runtime: 3,18 vagy újabb. Tekintse meg a [felügyelt identitás hitelesítése](#managed-identity) szakaszt további konfigurálási előfeltételekkel.

>[!NOTE]
>A Hdinsight és a Azure Machine Learning tevékenységek csak az Azure Blob Storage-fiók kulcsának hitelesítését támogatják.

### <a name="account-key-authentication"></a>Fiók kulcsos hitelesítés

A storage-fiók kulcsos hitelesítést használ, a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A Type tulajdonságot **AzureBlobStorage** (javasolt) vagy **AzureStorage** értékre kell beállítani (lásd az alábbi megjegyzéseket). |Igen |
| connectionString | Adja meg a connectionString tulajdonság tárolási való csatlakozáshoz szükséges információk. <br/> A Azure Key Vault is elhelyezheti a fiók kulcsát, és lekérheti a `accountKey` konfigurációt a kapcsolatok sztringből. További részletekért tekintse meg a következő mintákat, és [tárolja a hitelesítő adatokat Azure Key Vault](store-credentials-in-key-vault.md) cikkben. |Igen |
| connectVia | Az adattárhoz való csatlakozáshoz használt [integrációs](concepts-integration-runtime.md) modul. Használhatja az Azure integrációs modul vagy a helyi integrációs modul (ha az adattár egy magánhálózaton található). Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

>[!NOTE]
>A másodlagos blob Service-végpont nem támogatott a fiók kulcsos hitelesítésének használatakor. Más hitelesítési típusokat is használhat.

>[!NOTE]
>Ha "AzureStorage" típusú társított szolgáltatás használja, továbbra is támogatott-van, amíg használja az új "Azure BLOB Storage szolgáltatásról" társított szolgáltatás típusa, a jövőben használata javasolt.

**Példa**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Példa: fiók kulcsának tárolása Azure Key Vault**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;",
            "accountKey": { 
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

### <a name="shared-access-signature-authentication"></a>Közös hozzáférésű jogosultságkódos hitelesítés

Közös hozzáférésű jogosultságkód a tárfiókban található erőforrások delegált hozzáférést biztosít. Közös hozzáférésű jogosultságkód használatával az ügyfeleknek a tárfiókban lévő objektumokra vonatkozó egy megadott ideig. Nem kell megosztani a fiók hozzáférési kulcsait. A közös hozzáférésű jogosultságkód URI, amely a lekérdezési paraméterek magában foglalja a tárolási erőforrásokhoz való hitelesített hozzáférés szükséges összes információt. A közös hozzáférésű jogosultságkód-adattároló erőforrások elérésére, az ügyfél csak kell a megfelelő konstruktor vagy a metódus a közös hozzáférésű jogosultságkód adja át. A közös hozzáférésű aláírásokkal kapcsolatos további információkért lásd: közös hozzáférésű aláírások [: a közös hozzáférésű aláírás modelljének megismerése](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
>- A Data Factory mostantól a **szolgáltatás közös hozzáférési aláírásait** és a **fiók közös hozzáférési aláírásait**is támogatja. A közös hozzáférésű aláírásokkal kapcsolatos további információkért lásd: [korlátozott hozzáférés engedélyezése az Azure Storage-erőforrásokhoz közös hozzáférésű aláírások (SAS) használatával](../storage/common/storage-sas-overview.md).
>- A későbbi adatkészlet konfigurációja a mappa elérési útja az abszolút elérési út tároló szint kezdve. Kell egy vonalba az elérési utat a SAS URI-t a konfigurálása.

> [!TIP]
> A tárfiók szolgáltatás közös hozzáférési aláírásokat létrehozni, hajtsa végre a következő PowerShell-parancsokat. Cserélje le a zárójelben, és a szükséges engedélyeket.
> `$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Közös hozzáférésű jogosultságkódos hitelesítés használatához a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A Type tulajdonságot **AzureBlobStorage** (javasolt) vagy **AzureStorage** értékre kell beállítani (lásd az alábbi megjegyzéseket). |Igen |
| sasUri | A Storage-erőforrásokhoz, például a blobhoz vagy a tárolóhoz tartozó megosztott hozzáférési aláírás URI azonosítójának megadása. <br/>A mező megjelölése SecureString, hogy biztonságosan tárolja Data Factoryban. A Azure Key Vault SAS-tokent is helyezhet el az automatikus elforgatás kihasználása és a jogkivonat-rész eltávolításához. További részletekért tekintse meg a következő mintákat, és [tárolja a hitelesítő adatokat Azure Key Vault](store-credentials-in-key-vault.md) cikkben. |Igen |
| connectVia | Az adattárhoz való csatlakozáshoz használt [integrációs](concepts-integration-runtime.md) modul. Használhatja az Azure integrációs modul vagy a helyi integrációs modul (ha az adattár egy magánhálózaton található). Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

>[!NOTE]
>Ha "AzureStorage" típusú társított szolgáltatás használja, továbbra is támogatott-van, amíg használja az új "Azure BLOB Storage szolgáltatásról" társított szolgáltatás típusa, a jövőben használata javasolt.

**Példa**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource e.g. https://<container>.blob.core.windows.net/?sv=<storage version>&amp;st=<start time>&amp;se=<expire time>&amp;sr=<resource>&amp;sp=<permissions>&amp;sip=<ip range>&amp;spr=<protocol>&amp;sig=<signature>>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Példa: fiók kulcsának tárolása Azure Key Vault**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource without token e.g. https://<container>.blob.core.windows.net/>"
            },
            "sasToken": { 
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

Amikor létrehoz egy közös hozzáférésű jogosultságkód URI Azonosítójának, vegye figyelembe a következőket:

- A társított szolgáltatás (olvasási, írási, olvasási/írási) használatának módja a következőben az adat-előállító alapján objektumok megfelelő írási/olvasási engedélyeket.
- Megfelelő **lejárati idő** beállítása. Győződjön meg arról, hogy a tároló objektumok elérése le nem jár, a folyamat aktív időszakon belül.
- Az URI-t a megfelelő tárolóban vagy blobban kell létrehozni a szükséglet alapján. A blob közös hozzáférésű jogosultságkód URI lehetővé teszi, hogy a Data Factory, hogy a blob eléréséhez. A blob Storage-tárolók közös hozzáférésű aláírási URI-ja lehetővé teszi, hogy Data Factory megismételje a tárolóban lévő blobokat. Később több vagy kevesebb objektumok eléréséhez, vagy a közös hozzáférésű jogosultságkód URI Azonosítójának frissítése, ne felejtse el frissíteni a társított szolgáltatás az új URI-t.

### <a name="service-principal-authentication"></a>Egyszerű szolgáltatásnév hitelesítése

Az Azure Storage szolgáltatás egyszerű hitelesítéséhez lásd: az [Azure Storage hozzáférésének hitelesítése Azure Active Directory használatával](../storage/common/storage-auth-aad.md).

Egyszerű szolgáltatásnév hitelesítése használatához kövesse az alábbi lépéseket:

1. Az alkalmazás [Azure ad-Bérlővel való regisztrálását](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)követően regisztráljon egy Azure Active Directory (Azure ad) alkalmazásbeli entitást. Jegyezze fel a következő értékeket, mert a társított szolgáltatás definiálásához használja:

    - Alkalmazásazonosító
    - Alkalmazáskulcs
    - Bérlőazonosító

2. Adja meg a szolgáltatás egyszerű megfelelő engedéllyel az Azure Blob storage-ban. A szerepkörökkel kapcsolatos további részletekért tekintse meg az [Azure Storage-adatokhoz való hozzáférési jogosultságok kezelése a RBAC](../storage/common/storage-auth-aad-rbac.md) című témakört.

    - **Forrásként**a hozzáférés-vezérlés (iam) mezőben adjon meg legalább **Storage blob adatolvasói** szerepkört.
    - Fogadóként a **hozzáférés-vezérlés**(iam) mezőben adja meg legalább a **Storage blob adatközreműködői** szerepkört.

Ezek a Tulajdonságok támogatottak az Azure Blob storage társított szolgáltatása:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A Type tulajdonságot **AzureBlobStorage**értékre kell beállítani. |Igen |
| serviceEndpoint | Határozza meg az Azure Blob Storage szolgáltatás végpontját `https://<accountName>.blob.core.windows.net/`mintázatával. |Igen |
| servicePrincipalId | Adja meg az alkalmazás ügyfél-azonosítót. | Igen |
| servicePrincipalKey | Adja meg az alkalmazáskulcsot. Megjelöli ezt a mezőt **SecureString** , hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). | Igen |
| tenant | Adja meg a bérlő információkat (tartomány neve vagy a bérlő azonosítója) alatt az alkalmazás található. Az Azure portal jobb felső sarkában az egér viszi, lekéréséhez. | Igen |
| connectVia | Az adattárhoz való csatlakozáshoz használt [integrációs](concepts-integration-runtime.md) modul. Használhatja az Azure integrációs modul vagy a helyi integrációs modul (ha az adattár egy magánhálózaton található). Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

>[!NOTE]
>Egyszerű hitelesítés csak támogatott "Azure BLOB Storage szolgáltatásról" típus szerint Service-beli társított szolgáltatást, de nem korábbi "AzureStorage" írja be a társított szolgáltatás.

**Példa**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>" 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a>Felügyelt identitások az Azure-erőforrások hitelesítéséhez

Az adatok előállítója [felügyelt identitással társítható az Azure-erőforrásokhoz](data-factory-service-identity.md), amely az adott adatelőállítót képviseli. Ezt a felügyelt identitást közvetlenül használhatja a blob Storage-hitelesítéshez, hasonlóan a saját egyszerű szolgáltatásnév használatához. Lehetővé teszi a kijelölt factory eléréséhez és másolásához adatokhoz, és a Blob storage-bA.

Tekintse át az Azure [Storage-hoz való hozzáférés hitelesítése](../storage/common/storage-auth-aad.md) az Azure Storage-hitelesítéshez Azure Active Directory az általános lehetőséget. Felügyelt identitások Azure-erőforrások hitelesítés használatához kövesse az alábbi lépéseket:

1. Az [adat-előállító által felügyelt személyazonossági adatok beolvasása](data-factory-service-identity.md#retrieve-managed-identity) a gyári **azonosítóval együtt generált felügyelt identitási objektum** értékének másolásával.

2. Adja meg az Azure Blob storage-felügyelt identitásnak megfelelő jogosultságot. A szerepkörökkel kapcsolatos további részletekért tekintse meg az [Azure Storage-adatokhoz való hozzáférési jogosultságok kezelése a RBAC](../storage/common/storage-auth-aad-rbac.md) című témakört.

    - **Forrásként**a hozzáférés-vezérlés (iam) mezőben adjon meg legalább **Storage blob adatolvasói** szerepkört.
    - Fogadóként a **hozzáférés-vezérlés**(iam) mezőben adja meg legalább a **Storage blob adatközreműködői** szerepkört.

>[!IMPORTANT]
>Ha az adatok Blobból (forrásként vagy előkészítésként) való betöltéséhez a-t használja a SQL Data Warehouseba, akkor a Blobok felügyelt identitás-hitelesítésének használatakor az [útmutató](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) 1. és 2. lépését is végrehajtja 1.) a SQL Database-kiszolgáló regisztrálása a Azure Active Directory (Azure ad) és 2) a Storage blob adatközreműködői szerepkört hozzárendelheti a SQL Database kiszolgálóhoz; a REST-et a Data Factory kezeli. Ha a blob Storage egy Azure Virtual Network-végponttal van konfigurálva, akkor az adatoknak a használatával történő betöltéséhez használja a felügyelt identitások hitelesítését.

Ezek a Tulajdonságok támogatottak az Azure Blob storage társított szolgáltatása:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A Type tulajdonságot **AzureBlobStorage**értékre kell beállítani. |Igen |
| serviceEndpoint | Határozza meg az Azure Blob Storage szolgáltatás végpontját `https://<accountName>.blob.core.windows.net/`mintázatával. |Igen |
| connectVia | Az adattárhoz való csatlakozáshoz használt [integrációs](concepts-integration-runtime.md) modul. Használhatja az Azure integrációs modul vagy a helyi integrációs modul (ha az adattár egy magánhálózaton található). Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

> [!NOTE]
> Felügyelt identitások az Azure-erőforrások "Azure BLOB Storage szolgáltatásról" csak támogatja a hitelesítést, írja be társított szolgáltatást, de nem korábbi "AzureStorage" írja be a társított szolgáltatás. 

**Példa**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/"
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

A következő tulajdonságok támogatottak az Azure Blob számára a `location` beállítások a Format-alapú adatkészletben:

| Tulajdonság   | Leírás                                                  | Kötelező |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Az adatkészlet helyének Type tulajdonságát **AzureBlobStorageLocation**értékre kell állítani. | Igen      |
| tároló  | A blob-tároló.                                          | Igen      |
| folderPath | Az adott tárolóban lévő mappa elérési útja. Ha a mappa szűréséhez helyettesítő karaktert szeretne használni, hagyja ki ezt a beállítást, és a tevékenység forrásának beállításai között válassza a lehetőséget. | Nem       |
| fileName   | A fájlnév a megadott tároló + folderPath alatt. Ha helyettesítő karaktereket szeretne használni a fájlok szűréséhez, hagyja ki ezt a beállítást, és a tevékenység forrásának beállításai között válassza a lehetőséget. | Nem       |

**Példa**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder"
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

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz a Blob storage-forrás és fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="blob-storage-as-a-source-type"></a>BLOB storage-ot egy adatforrás típusa

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

A következő tulajdonságok támogatottak az Azure Blobhoz a formátum-alapú másolási forrás `storeSettings` beállításainál:

| Tulajdonság                 | Leírás                                                  | Kötelező                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | A `storeSettings` alatti Type tulajdonságot **AzureBlobStorageReadSettings**értékre kell állítani. | Igen                                           |
| recursive                | Azt jelzi, hogy az adatok olvasható rekurzív módon az almappák vagy csak a megadott mappába. Vegye figyelembe, hogy ha a rekurzív értéke igaz, és a fogadó a fájlalapú tároló, egy üres mappát vagy almappát nem másolja vagy létrehozott, a fogadó. Az engedélyezett értékek: **true** (alapértelmezett) és **false (hamis**). | Nem                                            |
| prefix                   | Az adatkészletben a forrás Blobok szűréséhez konfigurált megadott tárolóban lévő blob nevének előtagja. Azok a Blobok vannak kiválasztva, amelyek neve ezzel az előtaggal kezdődik. <br>Csak akkor érvényes, ha a `wildcardFolderPath` és a `wildcardFileName` tulajdonságok nincsenek megadva. | Nem                                                          |
| wildcardFolderPath       | A mappa elérési útja a forrás mappák szűréséhez az adatkészletben konfigurált megadott tárolóban helyettesítő karakterekkel. <br>Az engedélyezett helyettesítő karakterek a következők: `*` (nulla vagy több karakternek felel meg) és `?` (a nulla vagy egyetlen karakternek felel meg); a `^` használatával elkerülheti, hogy a tényleges mappanév helyettesítő karakterrel vagy a menekülési karakterrel rendelkezik-e a belsejében. <br>További példákat a [mappák és a fájlok szűrésére szolgáló példákban](#folder-and-file-filter-examples)talál. | Nem                                            |
| wildcardFileName         | A forrásfájl szűréséhez a megadott tároló + folderPath/wildcardFolderPath helyettesítő karakterekkel rendelkező fájlnév. <br>Az engedélyezett helyettesítő karakterek a következők: `*` (nulla vagy több karakternek felel meg) és `?` (a nulla vagy egyetlen karakternek felel meg); a `^` használatával elkerülheti, hogy a tényleges mappanév helyettesítő karakterrel vagy a menekülési karakterrel rendelkezik-e a belsejében.  További példákat a [mappák és a fájlok szűrésére szolgáló példákban](#folder-and-file-filter-examples)talál. | Igen, ha `fileName` nincs megadva az adatkészletben |
| modifiedDatetimeStart    | Az attribútum alapján fájlok szűrés: utolsó módosítás. A fájlok akkor lesznek kiválasztva, ha az utolsó módosítás időpontja a `modifiedDatetimeStart` és `modifiedDatetimeEnd`közötti időtartományon belül van. Az idő UTC időzóna szerint formátumban alkalmazott "2018-12-01T05:00:00Z". <br> A Tulajdonságok lehet null értékű, ami jelenti azt, hogy nincs fájlszűrő attribútum alkalmazandó az adatkészletet.  Ha `modifiedDatetimeStart` rendelkezik datetime értékkel, de `modifiedDatetimeEnd` NULL értékű, az azt jelenti, hogy azok a fájlok lesznek kiválasztva, amelyek utolsó módosítási attribútuma nagyobb vagy egyenlő, mint a DateTime érték.  Ha `modifiedDatetimeEnd` rendelkezik datetime értékkel, de `modifiedDatetimeStart` NULL értékű, az azt jelenti, hogy azok a fájlok lesznek kiválasztva, amelyek utolsó módosítási attribútuma kisebb, mint a DateTime érték. | Nem                                            |
| modifiedDatetimeEnd      | Ugyanaz, mint a fenti.                                               | Nem                                            |
| maxConcurrentConnections | A tárolási tárolóhoz való kapcsolódáshoz szükséges kapcsolatok száma egyidejűleg. Csak akkor kell megadni, ha az egyidejű kapcsolódást szeretné korlátozni az adattárral. | Nem                                            |

> [!NOTE]
> A Parquet/tagolt szöveg formátuma esetén a következő szakaszban említett, **BlobSource** típusú másolási tevékenység továbbra is támogatott a visszafelé kompatibilitás érdekében. Azt javasoljuk, hogy ezt az új modellt fogja használni, és az ADF szerzői felhasználói felülete átváltott az új típusok létrehozásához.

**Példa**

```json
"activities":[
    {
        "name": "CopyFromBlob",
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
                    "type": "AzureBlobStorageReadSettings",
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

### <a name="blob-storage-as-a-sink-type"></a>BLOB storage-ot a fogadó típusa

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

A következő tulajdonságok támogatottak az Azure Blobhoz a Format-alapú másolási fogadó `storeSettings` beállításaiban:

| Tulajdonság                 | Leírás                                                  | Kötelező |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | A `storeSettings` alatti Type tulajdonságot **AzureBlobStorageWriteSettings**értékre kell állítani. | Igen      |
| copyBehavior             | A másolási viselkedés határozza meg, ha a forrás fájlok fájlalapú adattárból.<br/><br/>Engedélyezett értékek a következők:<br/><b>-PreserveHierarchy (alapértelmezett)</b>: megőrzi a fájl-hierarchiát a célmappában. A forrásmappa forrás-fájl elérési útja megegyezik a célmappában a célfájl elérési útja.<br/><b>-FlattenHierarchy</b>: a forrás mappából származó összes fájl a célmappa első szintjén van. A cél fájlok automatikusan létrehozott névvel rendelkeznek. <br/><b>-MergeFiles</b>: az összes fájlt egyesíti a forrás mappájából egy fájlba. A fájl- vagy blob neve meg van adva, az egyesített fájlnév-e a megadott néven. Ellenkező esetben egy automatikusan létrehozott nevét. | Nem       |
| blockSizeInMB | Határozza meg a blokk méretét MB-ban, amely a Blobok blokkolására szolgál. További [információ a blokkos blobokról](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs). <br/>Az engedélyezett érték **4 és 100 MB között**van. <br/>Alapértelmezés szerint az ADF automatikusan meghatározza a blokk méretét a forrás-áruház típusa és adatai alapján. A nem bináris másolás Blobba való másolása esetén az alapértelmezett blokk mérete 100 MB, hogy a legtöbb 4,95 TB-os adathoz illeszkedjen. Nem lehet optimális, ha az adatai nem nagyméretűak, különösen akkor, ha olyan helyi Integration Runtime használ, amely gyenge hálózattal működik, ami működési időtúllépést vagy teljesítménnyel kapcsolatos problémát okoz. Explicit módon megadhatja a blokk méretét, miközben biztosítja, hogy a blockSizeInMB * 50000 elég nagy legyen az adattároláshoz, máskülönben a másolási tevékenység futtatása sikertelen lesz. | Nem |
| maxConcurrentConnections | A tárolási tárolóhoz való kapcsolódáshoz szükséges kapcsolatok száma egyidejűleg. Csak akkor kell megadni, ha az egyidejű kapcsolódást szeretné korlátozni az adattárral. | Nem       |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromBlob",
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
                    "type": "AzureBlobStorageWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Példák a mappák és a fájlok szűrésére

Ez a szakasz a mappa elérési útjának és fájlnevének a helyettesítő karakteres szűrőkkel való viselkedését írja le.

| folderPath | fileName | recursive | A forrás mappa szerkezete és a szűrő eredménye (a **félkövérrel szedett** fájlok beolvasása)|
|:--- |:--- |:--- |:--- |
| `container/Folder*` | (üres, alapértelmezett használata) | false | tároló<br/>&nbsp;&nbsp;&nbsp;&nbsp;mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**file1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**fájl2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fájl3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `container/Folder*` | (üres, alapértelmezett használata) | true | tároló<br/>&nbsp;&nbsp;&nbsp;&nbsp;mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**file1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**fájl2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**fájl3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `container/Folder*` | `*.csv` | false | tároló<br/>&nbsp;&nbsp;&nbsp;&nbsp;mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**file1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fájl3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `container/Folder*` | `*.csv` | true | tároló<br/>&nbsp;&nbsp;&nbsp;&nbsp;mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**file1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**fájl3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |

### <a name="some-recursive-and-copybehavior-examples"></a>A rekurzív és copyBehavior példákat

Ez a szakasz ismerteti az eredményül kapott viselkedéstől a másolási művelet rekurzív és copyBehavior értékek különböző kombinációihoz.

| recursive | copyBehavior | Forrás mappastruktúra | Eredményül kapott cél |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | a célmappában mappa1 szerkezete ugyanaz, mint a forrás hozzon létre:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A cél mappa1 jön létre az alábbi struktúra használatával: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan generált neve a file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan generált neve a Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan generált neve a fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan generált neve a File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan generált neve a File5 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A cél mappa1 jön létre az alábbi struktúra használatával: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1 + Fájl2 + fájl3 + File4 + File5 tartalma egyetlen fájlba van egyesítve egy automatikusan létrehozott fájlnévvel. |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | a célmappában mappa1 az alábbi struktúra használatával jön létre: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/><br/>Fájl3, File4 és File5 Subfolder1 nem mértékének. |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | a célmappában mappa1 az alábbi struktúra használatával jön létre: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan generált neve a file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan generált neve a Fájl2<br/><br/>Fájl3, File4 és File5 Subfolder1 nem mértékének. |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Az alábbi struktúra használatával jön létre a célmappában mappa1<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1 + Fájl2 tartalom egyetlen fájlba van egyesítve, és automatikusan létrehozott fájlnevet tartalmaz. automatikusan létrehozott nevet, a file1 kiszolgálón<br/><br/>Fájl3, File4 és File5 Subfolder1 nem mértékének. |

## <a name="preserve-metadata-during-copy"></a>Metaadatok megőrzése a másolás során

Ha fájlokat másol az Amazon S3/Azure Blob/Azure Data Lake Storage Gen2ról Azure Data Lake Storage Gen2/Azure Blobba, megtarthatja a fájl metaadatainak megőrzését az adatokkal együtt. További információ: a [metaadatok megőrzése](copy-activity-preserve-metadata.md#preserve-metadata).

## <a name="mapping-data-flow-properties"></a>Adatfolyam-tulajdonságok leképezése

Az adatoknak a leképezési folyamatba való átalakításakor a fájlok olvashatók és írhatók az Azure Blob Storage JSON-, Avro-, tagolt szöveg-vagy parketta-formátumban. További információ: a [forrás-átalakítás](data-flow-source.md) és a fogadó [transzformációja](data-flow-sink.md) a leképezési adatfolyam funkcióban.

### <a name="source-transformation"></a>Forrás-átalakítás

A forrás-átalakításban egy tárolóból, mappából vagy egyedi fájlból is beolvashatja az Azure Blob Storage. A **forrás beállításai** lapon kezelheti a fájlok olvasásának módját. 

![Forrás beállításai](media/data-flow/sourceOptions1.png "Forrás beállításai")

**Helyettesítő karakter elérési útja:** A helyettesítő karakterek használatakor a rendszer az ADF-et az egyes egyező mappákon és fájlokon keresztül, egyetlen forrás-átalakításon keresztüli hurokra utasítja. Ez egy hatékony módszer több fájl feldolgozására egyetlen folyamaton belül. Több helyettesítő karakterből álló mintázatot is hozzáadhat, ha a meglévő helyettesítő karakter fölé viszi a kurzort.

A forrás tárolóban válasszon egy olyan fájlt, amely megfelel a mintának. Az adatkészletben csak tároló adható meg. A helyettesítő karakteres elérési útnak ezért a gyökérmappa elérési útját is tartalmaznia kell.

Helyettesítő karakteres példák:

* ```*``` a karakterek tetszőleges halmazát jelöli
* a ```**``` rekurzív címtár-beágyazást jelöl
* ```?``` egy karaktert cserél le
* ```[]``` a zárójelben szereplő további karakterek egyikének felel meg

* ```/data/sales/**/*.csv``` lekéri az összes CSV-fájlt a/Data/Sales alatt
* ```/data/sales/20??/**``` beolvassa az összes fájlt a 20. században
* ```/data/sales/2004/*/12/[XY]1?.csv``` beolvassa az összes CSV-fájlt a 2004-as verzióban, amely egy kétjegyű számú X vagy Y előtaggal kezdődik.

**Partíció gyökerének elérési útja:** Ha a forrásban particionált mappák vannak ```key=value``` formátumban (például Year = 2019), akkor a partíciós mappa legfelső szintjét hozzárendelheti az adatfolyami adatfolyamban lévő oszlop neveként.

Először állítson be egy helyettesítő karaktert, amely tartalmazza az összes olyan elérési utat, amely a particionált mappák és az elolvasni kívánt levél fájlok.

![Partíciós forrásfájl beállításai](media/data-flow/partfile2.png "Partíciós fájl beállítása")

A partíció gyökerének elérési útja beállítással határozhatja meg, hogy a mappa szerkezete milyen felső szintű legyen. Amikor az adatelőnézet használatával tekinti meg az adatai tartalmát, látni fogja, hogy az ADF hozzáadja az egyes mappák szintjein talált feloldott partíciókat.

![Partíció gyökerének elérési útja](media/data-flow/partfile1.png "Partíciós gyökér elérési útjának előnézete")

**Fájlok listája:** Ez egy beállított fájl. Hozzon létre egy szövegfájlt, amely tartalmazza a feldolgozandó relatív elérési út fájljainak listáját. Mutasson erre a szövegfájlra.

**A fájl nevét tároló oszlop:** Tárolja a forrásfájl nevét az adataiban található oszlopban. Itt adjon meg egy új oszlopnevet a fájlnév karakterláncának tárolásához.

**Befejezés után:** Ha az adatfolyam futtatása után semmit nem kíván végrehajtani a forrásfájlban, törölje a forrásfájlt, vagy helyezze át a forrásfájlt. Az áthelyezés elérési útjai relatívak.

Ha a forrásfájlokat másik helyre szeretné áthelyezni a feldolgozás után, először válassza a "áthelyezés" lehetőséget a fájl művelethez. Ezután állítsa be a "from" könyvtárat. Ha nem használ helyettesítő karaktereket az elérési úthoz, akkor a "from" beállítás lesz a forrás mappájával megegyező mappa.

Ha a forrás elérési útja helyettesítő karakterrel rendelkezik, a szintaxis az alábbihoz hasonlóan fog kinézni:

```/data/sales/20??/**/*.csv```

A "from" lehetőséget adhatja meg

```/data/sales```

És "to"

```/backup/priorSales```

Ebben az esetben a rendszer a/Data/Sales alatt forrásozott összes fájlt áthelyezi a/backup/priorSales.

> [!NOTE]
> A fájl műveletei csak akkor futnak, amikor egy folyamaton belül indítja el az adatfolyamatot (a folyamat hibakeresési vagy végrehajtási futtatása), amely az adatfolyamok végrehajtása tevékenységet használja egy folyamaton belül. A fájl műveletei *nem* futnak adatfolyam-hibakeresési módban.

**Szűrés legutóbb módosítva:** A feldolgozás alatt álló fájlokat szűrheti, ha az utolsó módosítás dátumát megadja. Az összes dátum-idő UTC. 

### <a name="sink-properties"></a>Fogadó tulajdonságai

A fogadó átalakításban az Azure Blob Storageban található tárolóba vagy mappába írhat. a **Beállítások** lapon kezelheti a fájlok írásának módját.

![fogadó beállításai](media/data-flow/file-sink-settings.png "fogadó beállításai")

**Törölje a mappát:** Meghatározza, hogy a célmappa törlődik-e az adatírás előtt.

**Fájlnév beállítás:** Meghatározza, hogy a célfájl hogyan legyen elnevezve a célmappában. A fájlnév beállításai a következők:
   * **Default (alapértelmezett**): a Spark használatával a fájlok a részek alapértelmezett értékei alapján megtalálhatók.
   * **Minta**: adjon meg egy mintát, amely egy partíción lévő kimeneti fájlokat sorolja fel. Például a **hitelek [n]. csv** hozza létre a loans1. csv, a loans2. csv fájlt stb.
   * **/Partíció**: adjon meg egy fájlnevet partícióként.
   * **Oszlopbeli adatként**: állítsa a kimeneti fájlt egy oszlop értékére. Az elérési út az adatkészlet-tárolóhoz viszonyítva nem a célmappához képest.
   * **Kimenet egyetlen fájlba**: a particionált kimeneti fájlok egyetlen elnevezett fájlba kombinálhatók. Az elérési út az adatkészlet mappájához képest relatív. Vegye figyelembe, hogy a saját egyesítési művelete valószínűleg meghiúsulhat a csomópont méretétől függően. Ez a beállítás nagyméretű adatkészletek esetén nem ajánlott.

**Összes idézet:** Meghatározza, hogy az idézőjelek közé kell-e foglalni az összes értéket

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>GetMetadata tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [GetMetaData tevékenységet](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Tevékenység tulajdonságainak törlése

A tulajdonságok részleteinek megismeréséhez tekintse meg a [tevékenység törlése](delete-activity.md) lehetőséget.

## <a name="legacy-models"></a>Örökölt modellek

>[!NOTE]
>A következő modellek továbbra is támogatottak a visszamenőleges kompatibilitás érdekében. Azt javasoljuk, hogy használja a fenti szakaszban említett új modellt, és az ADF authoring felhasználói felülete átvált az új modell generálására.

### <a name="legacy-dataset-model"></a>Örökölt adatkészlet-modell

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet Type tulajdonságát **AzureBlob**értékre kell állítani. |Igen |
| folderPath | A tároló és a blob Storage-mappa elérési útja. <br/><br/>A helyettesítő karakteres szűrő a tároló neve nélkül használható. Az engedélyezett helyettesítő karakterek a következők: `*` (nulla vagy több karakternek felel meg) és `?` (a nulla vagy egyetlen karakternek felel meg); a `^` használatával elkerülheti, hogy a tényleges mappanév helyettesítő karakterrel vagy a menekülési karakterrel rendelkezik-e a belsejében. <br/><br/>Példák: myblobcontainer/myblobfolder/, további példák a [mappák és a fájlok szűrése példákban](#folder-and-file-filter-examples). |Igen, a Másolás/keresési tevékenység, GetMetadata tevékenység esetében: nem |
| fileName | A blob (ok) **neve vagy helyettesítő szűrője** a megadott "folderPath" elemnél. Ez a tulajdonság értékét nem adja meg, ha az adatkészlet a mappában lévő összes BLOB mutat. <br/><br/>A Filter (szűrő) esetében az engedélyezett helyettesítő karakterek a következők: `*` (nulla vagy több karakternek felel meg) és `?` (nulla vagy egyetlen karakternek felel meg).<br/>– 1. példa: `"fileName": "*.csv"`<br/>– 2. példa: `"fileName": "???20180427.txt"`<br/>A `^` használatával elkerülheti, ha a tényleges fájlnév helyettesítő karakterrel vagy a menekülési karakterrel rendelkezik.<br/><br/>Ha nincs megadva a fájlnév egy kimeneti adatkészlethez, és a **preserveHierarchy** nincs megadva a tevékenység fogadójában, a másolási tevékenység automatikusan létrehozza a blob nevét a következő mintával: "*adat. [ tevékenység futtatási azonosítója GUID]. [GUID if FlattenHierarchy]. [formátum, ha konfigurálva]. [tömörítés, ha be van állítva]* ", például" adatfájl. 0a405f8a-93ff-4c6f-B3BE-f69616f1df7a. txt. gz "; Ha táblázatos forrásból másol a lekérdezés helyett táblanév használatával, a név minta a következő: " *[Table Name]. [ Format]. [tömörítés, ha konfigurálva]* ", például" sajáttábla. csv ". |Nem |
| modifiedDatetimeStart | Az attribútum alapján fájlok szűrés: utolsó módosítás. A fájlok akkor lesznek kiválasztva, ha az utolsó módosítás időpontja a `modifiedDatetimeStart` és `modifiedDatetimeEnd`közötti időtartományon belül van. Az idő UTC időzóna szerint formátumban alkalmazott "2018-12-01T05:00:00Z". <br/><br/> Ügyeljen arra, hogy az adatáthelyezés általános teljesítményét a beállítás engedélyezésével befolyásolja, ha nagy mennyiségű fájlból szeretne szűrni a fájlmegosztást. <br/><br/> A tulajdonságok értéke NULL lehet, ami azt jelenti, hogy a rendszer nem alkalmazza a file Attribute szűrőt az adatkészletre.  Ha `modifiedDatetimeStart` rendelkezik datetime értékkel, de `modifiedDatetimeEnd` NULL értékű, az azt jelenti, hogy azok a fájlok lesznek kiválasztva, amelyek utolsó módosítási attribútuma nagyobb vagy egyenlő, mint a DateTime érték.  Ha `modifiedDatetimeEnd` rendelkezik datetime értékkel, de `modifiedDatetimeStart` NULL értékű, az azt jelenti, hogy azok a fájlok lesznek kiválasztva, amelyek utolsó módosítási attribútuma kisebb, mint a DateTime érték.| Nem |
| modifiedDatetimeEnd | Az attribútum alapján fájlok szűrés: utolsó módosítás. A fájlok akkor lesznek kiválasztva, ha az utolsó módosítás időpontja a `modifiedDatetimeStart` és `modifiedDatetimeEnd`közötti időtartományon belül van. Az idő UTC időzóna szerint formátumban alkalmazott "2018-12-01T05:00:00Z". <br/><br/> Ügyeljen arra, hogy az adatáthelyezés általános teljesítményét a beállítás engedélyezésével befolyásolja, ha nagy mennyiségű fájlból szeretne szűrni a fájlmegosztást. <br/><br/> A tulajdonságok értéke NULL lehet, ami azt jelenti, hogy a rendszer nem alkalmazza a file Attribute szűrőt az adatkészletre.  Ha `modifiedDatetimeStart` rendelkezik datetime értékkel, de `modifiedDatetimeEnd` NULL értékű, az azt jelenti, hogy azok a fájlok lesznek kiválasztva, amelyek utolsó módosítási attribútuma nagyobb vagy egyenlő, mint a DateTime érték.  Ha `modifiedDatetimeEnd` rendelkezik datetime értékkel, de `modifiedDatetimeStart` NULL értékű, az azt jelenti, hogy azok a fájlok lesznek kiválasztva, amelyek utolsó módosítási attribútuma kisebb, mint a DateTime érték.| Nem |
| format | Ha szeretné másolni a fájlokat, a fájlalapú tárolók (bináris másolat) közötti, hagyja ki a format szakaszban, mind a bemeneti és kimeneti adatkészlet-definíciókban.<br/><br/>Ha a fájlokat egy adott formátummal szeretné elemezni vagy előállítani, a következő fájlformátum-típusok támogatottak: **Szövegformátum**, **JsonFormat**, **AvroFormat**, **OrcFormat**és **ParquetFormat**. A **Type (típus** ) tulajdonságot állítsa a **Format** értékre a következő értékek egyikére. További információ: [szöveg formátuma](supported-file-formats-and-compression-codecs-legacy.md#text-format), JSON- [Formátum](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro formátum](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [ork-formátum](supported-file-formats-and-compression-codecs-legacy.md#orc-format)és [parketta formátuma](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) . |Nem (csak a bináris másolás esetén) |
| compression | Adja meg a típus és az adatok tömörítési szintje. További információ: [támogatott fájlformátumok és tömörítési kodekek](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>A támogatott típusok a **gzip**, a **deflate**, a **BZip2**és a **ZipDeflate**.<br/>A támogatott szintek **optimálisak** és **leggyorsabbak**. |Nem |

>[!TIP]
>A mappában található összes blob másolásához csak a **folderPath** kell megadni.<br>Ha egyetlen blobot szeretne másolni egy adott névvel, adja meg a **folderPath** és a **fájlnevet** tartalmazó mappa nevét.<br>Ha egy mappában lévő Blobok egy részhalmazát szeretné átmásolni, akkor a **folderPath** és a filename (helyettesítő karakter) nevű **fájlnevet** kell megadnia. 

**Példa**

```json
{
    "name": "AzureBlobDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
            "referenceName": "<Azure Blob storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
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

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának Type tulajdonságát **BlobSource**értékre kell állítani. |Igen |
| recursive | Azt jelzi, hogy az adatok olvasható rekurzív módon az almappák vagy csak a megadott mappába. Vegye figyelembe, hogy ha a rekurzív értéke igaz, és a fogadó a fájlalapú tároló, egy üres mappát vagy almappát nem másolja vagy létrehozott, a fogadó.<br/>Az engedélyezett értékek: **true** (alapértelmezett) és **false (hamis**). | Nem |
| maxConcurrentConnections | A tárolási tárolóhoz való kapcsolódáshoz szükséges kapcsolatok száma egyidejűleg. Csak akkor kell megadni, ha az egyidejű kapcsolódást szeretné korlátozni az adattárral. | Nem |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Blob input dataset name>",
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
                "type": "BlobSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="legacy-copy-activity-sink-model"></a>Örökölt másolási tevékenység fogadó modellje

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadójának Type tulajdonságát **BlobSink**értékre kell állítani. |Igen |
| copyBehavior | A másolási viselkedés határozza meg, ha a forrás fájlok fájlalapú adattárból.<br/><br/>Engedélyezett értékek a következők:<br/><b>-PreserveHierarchy (alapértelmezett)</b>: megőrzi a fájl-hierarchiát a célmappában. A forrásmappa forrás-fájl elérési útja megegyezik a célmappában a célfájl elérési útja.<br/><b>-FlattenHierarchy</b>: a forrás mappából származó összes fájl a célmappa első szintjén van. A cél fájlok automatikusan létrehozott névvel rendelkeznek. <br/><b>-MergeFiles</b>: az összes fájlt egyesíti a forrás mappájából egy fájlba. A fájl- vagy blob neve meg van adva, az egyesített fájlnév-e a megadott néven. Ellenkező esetben egy automatikusan létrehozott nevét. | Nem |
| maxConcurrentConnections | A tárolási tárolóhoz való kapcsolódáshoz szükséges kapcsolatok száma egyidejűleg. Csak akkor kell megadni, ha az egyidejű kapcsolódást szeretné korlátozni az adattárral. | Nem |

**Példa**

```json
"activities":[
    {
        "name": "CopyToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Blob output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "BlobSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

## <a name="next-steps"></a>Következő lépések

A Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).

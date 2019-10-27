---
title: Adatok másolása az Azure Blob Storage-ba vagy onnan az Data Factory használatával | Microsoft Docs
description: Megtudhatja, hogyan másolhat át az adatok a támogatott forrásból származó adattárakból az Azure Blob Storage-ba vagy a blob Storage-ból a Data Factory használatával támogatott fogadó adattárakba.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: a68549622972bfa031bc2934473dc65f0a656231
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935699"
---
# <a name="copy-data-to-or-from-azure-blob-storage-by-using-azure-data-factory"></a>Adatok másolása az Azure Blob Storage-ba vagy onnan az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-azure-blob-connector.md)
> * [Aktuális verzió](connector-azure-blob-storage.md)

Ez a cikk az adatok Azure Blob Storage-ba és onnan történő másolását ismerteti. A Azure Data Factoryről a [bevezető cikkben](introduction.md)olvashat bővebben.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az Azure Blob-összekötő a következő tevékenységek esetében támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Adatfolyam hozzárendelése](concepts-data-flow-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)
- [Tevékenység törlése](delete-activity.md)

Pontosabban a blob Storage-összekötő a következőket támogatja:

- Blobok másolása általános célú Azure Storage-fiókokba és gyors/lassú blob Storage-tárolóba. 
- Blobok másolása a fiók kulcsa, a szolgáltatás megosztott hozzáférésének aláírása, az egyszerű szolgáltatásnév vagy a felügyelt identitások használatával az Azure-erőforrások hitelesítéséhez.
- Blobok másolása blokkból, hozzáfűzésből vagy blobokból, és az adatok másolása csak a Blobok blokkolására.
- A Blobok másolása vagy elemzése vagy a Blobok generálása [támogatott fájlformátumokkal és tömörítési kodekekkel](supported-file-formats-and-compression-codecs.md)történik.

>[!IMPORTANT]
>Ha engedélyezi a **megbízható Microsoft-szolgáltatások számára a Storage-fiók elérésének engedélyezése** beállítást az Azure Storage-tűzfal beállításainál, és az Azure Integration Runtime használatával szeretne csatlakozni a blob Storagehoz, a [felügyelt identitások hitelesítését](#managed-identity)kell használnia.

## <a name="get-started"></a>Az első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük a blob Storage-hoz kapcsolódó Data Factory definiálásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Az Azure Blob-összekötő a következő hitelesítési típusokat támogatja, a részletekért tekintse meg a megfelelő szakaszt:

- [Fiók kulcsának hitelesítése](#account-key-authentication)
- [Közös hozzáférésű aláírások hitelesítése](#shared-access-signature-authentication)
- [Egyszerű szolgáltatás hitelesítése](#service-principal-authentication)
- [Felügyelt identitások az Azure-erőforrások hitelesítéséhez](#managed-identity)

>[!NOTE]
>Ha az adatok SQL Data Warehouseba való betöltéséhez a forrás-vagy előkészítési blob Storage-t Virtual Network-végponttal konfigurálja, akkor a rendszer a következőt használja a felügyelt identitások hitelesítéséhez Integration Runtime: 3,18 vagy újabb. Tekintse meg a [felügyelt identitás hitelesítése](#managed-identity) szakaszt további konfigurálási előfeltételekkel.

>[!NOTE]
>A Hdinsight és a Azure Machine Learning tevékenységek csak az Azure Blob Storage-fiók kulcsának hitelesítését támogatják.

### <a name="account-key-authentication"></a>Fiók kulcsának hitelesítése

A Storage-fiók kulcsos hitelesítésének használatához a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A Type tulajdonságot **AzureBlobStorage** (javasolt) vagy **AzureStorage** értékre kell beállítani (lásd az alábbi megjegyzéseket). |Igen |
| connectionString | Határozza meg a connectionString tulajdonsághoz való kapcsolódáshoz szükséges adatokat. <br/>A mező megjelölése SecureString, hogy biztonságosan tárolja Data Factoryban. A Azure Key Vault is elhelyezheti a fiók kulcsát, és lekérheti a `accountKey` konfigurációt a kapcsolatok sztringből. További részletekért tekintse meg a következő mintákat, és [tárolja a hitelesítő adatokat Azure Key Vault](store-credentials-in-key-vault.md) cikkben. |Igen |
| Connectvia tulajdonsággal | Az adattárhoz való csatlakozáshoz használt [integrációs](concepts-integration-runtime.md) modul. Használhat Azure Integration Runtime vagy saját üzemeltetésű Integration Runtime (ha az adattár egy magánhálózaton található). Ha nincs megadva, az alapértelmezett Azure Integration Runtime használja. |Nem |

>[!NOTE]
>A másodlagos blob Service-végpont nem támogatott a fiók kulcsos hitelesítésének használatakor. Más hitelesítési típusokat is használhat.

>[!NOTE]
>Ha a "AzureStorage" típusú társított szolgáltatást használta, akkor továbbra is támogatott, míg a rendszer ezt az új "AzureBlobStorage" társított szolgáltatástípus használatát javasolja.

**Példa**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
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
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;"
            },
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

### <a name="shared-access-signature-authentication"></a>Közös hozzáférésű aláírások hitelesítése

A közös hozzáférésű aláírások delegált hozzáférést biztosítanak a Storage-fiók erőforrásaihoz. A megosztott elérési aláírás használatával a megadott időpontra korlátozhatja az ügyfél számára a Storage-fiókban lévő objektumokra vonatkozó engedélyek megadását. Nem kell megosztania a fiók hozzáférési kulcsait. A közös hozzáférésű aláírás egy URI, amely magában foglalja a lekérdezési paramétereit. a tárolási erőforrásokhoz való hitelesített hozzáféréshez szükséges összes információ. A megosztott hozzáférési aláírással rendelkező tárolási erőforrások eléréséhez az ügyfélnek csak a közös hozzáférési aláírásban kell átadnia a megfelelő konstruktort vagy metódust. A közös hozzáférésű aláírásokkal kapcsolatos további információkért lásd: közös hozzáférésű aláírások [: a közös hozzáférésű aláírás modelljének megismerése](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
>- A Data Factory mostantól a **szolgáltatás közös hozzáférési aláírásait** és a **fiók közös hozzáférési aláírásait**is támogatja. A közös hozzáférésű aláírásokkal kapcsolatos további információkért lásd: [korlátozott hozzáférés engedélyezése az Azure Storage-erőforrásokhoz közös hozzáférésű aláírások (SAS) használatával](../storage/common/storage-sas-overview.md).
>- A későbbi adatkészlet-konfigurációban a mappa elérési útja a tároló szintjétől kezdődő abszolút elérési út. Konfigurálnia kell egy igazítást a SAS URI elérési útjával.

> [!TIP]
> A következő PowerShell-parancsok végrehajtásával hozhatja végre a szolgáltatás megosztott elérési aláírását a Storage-fiókhoz. Cserélje le a helyőrzőket, és adja meg a szükséges engedélyt.
> `$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

A közös hozzáférésű aláírás-hitelesítés használatához a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A Type tulajdonságot **AzureBlobStorage** (javasolt) vagy **AzureStorage** értékre kell beállítani (lásd az alábbi megjegyzéseket). |Igen |
| sasUri | A Storage-erőforrásokhoz, például a blobhoz vagy a tárolóhoz tartozó megosztott hozzáférési aláírás URI azonosítójának megadása. <br/>A mező megjelölése SecureString, hogy biztonságosan tárolja Data Factoryban. A Azure Key Vault SAS-tokent is helyezhet el az automatikus elforgatás kihasználása és a jogkivonat-rész eltávolításához. További részletekért tekintse meg a következő mintákat, és [tárolja a hitelesítő adatokat Azure Key Vault](store-credentials-in-key-vault.md) cikkben. |Igen |
| Connectvia tulajdonsággal | Az adattárhoz való csatlakozáshoz használt [integrációs](concepts-integration-runtime.md) modul. Használhatja a Azure Integration Runtime vagy a saját üzemeltetésű Integration Runtime (ha az adattár egy magánhálózaton található). Ha nincs megadva, az alapértelmezett Azure Integration Runtime használja. |Nem |

>[!NOTE]
>Ha a "AzureStorage" típusú társított szolgáltatást használta, akkor továbbra is támogatott, míg a rendszer ezt az új "AzureBlobStorage" társított szolgáltatástípus használatát javasolja.

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

Közös hozzáférésű aláírási URI létrehozásakor vegye figyelembe a következő szempontokat:

- Állítsa be a megfelelő olvasási/írási engedélyeket az objektumokon attól függően, hogy a társított szolgáltatás (olvasás, írás, olvasás/írás) hogyan legyen használatban az adatelőállítóban.
- Megfelelő **lejárati idő** beállítása. Győződjön meg arról, hogy a tárolási objektumokhoz való hozzáférés a folyamat aktív időszakán belül nem jár le.
- Az URI-t a megfelelő tárolóban vagy blobban kell létrehozni a szükséglet alapján. A Blobok közös hozzáférésű aláírási URI-ja lehetővé teszi az Data Factory számára az adott blob elérését. A blob Storage-tárolók közös hozzáférésű aláírási URI-ja lehetővé teszi, hogy Data Factory megismételje a tárolóban lévő blobokat. Ha később több vagy kevesebb objektumhoz szeretne hozzáférést biztosítani, vagy a közös hozzáférésű aláírás URI-JÁT szeretné frissíteni, ne felejtse el frissíteni a társított szolgáltatást az új URI-val.

### <a name="service-principal-authentication"></a>Egyszerű szolgáltatásnév hitelesítése

Az Azure Storage szolgáltatás egyszerű hitelesítéséhez lásd: az [Azure Storage hozzáférésének hitelesítése Azure Active Directory használatával](../storage/common/storage-auth-aad.md).

Az egyszerű szolgáltatás hitelesítésének használatához kövesse az alábbi lépéseket:

1. Az alkalmazás [Azure ad-Bérlővel való regisztrálását](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)követően regisztráljon egy Azure Active Directory (Azure ad) alkalmazásbeli entitást. Jegyezze fel a következő értékeket, amelyeket a társított szolgáltatás definiálásához használ:

    - Alkalmazásazonosító
    - Alkalmazás kulcsa
    - Bérlőazonosító

2. Adja meg az egyszerű szolgáltatás megfelelő engedélyét az Azure Blob Storage-ban. A szerepkörökkel kapcsolatos további részletekért tekintse meg az [Azure Storage-adatokhoz való hozzáférési jogosultságok kezelése a RBAC](../storage/common/storage-auth-aad-rbac.md) című témakört.

    - **Forrásként**a hozzáférés-vezérlés (iam) mezőben adjon meg legalább **Storage blob adatolvasói** szerepkört.
    - Fogadóként a **hozzáférés-vezérlés**(iam) mezőben adja meg legalább a **Storage blob adatközreműködői** szerepkört.

Ezek a tulajdonságok egy Azure Blob Storage-beli társított szolgáltatás esetén támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A Type tulajdonságot **AzureBlobStorage**értékre kell beállítani. |Igen |
| serviceEndpoint | Az Azure Blob Storage szolgáltatás végpontját a `https://<accountName>.blob.core.windows.net/` mintázattal adhatja meg. |Igen |
| servicePrincipalId | Határozza meg az alkalmazás ügyfél-AZONOSÍTÓját. | Igen |
| servicePrincipalKey | Az alkalmazás kulcsának meghatározása. Megjelöli ezt a mezőt **SecureString** , hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). | Igen |
| Bérlő | Adja meg a bérlői adatokat (tartománynevet vagy bérlői azonosítót), amely alatt az alkalmazás található. A Azure Portal jobb felső sarkában lévő egér fölé helyezve lekéri. | Igen |
| Connectvia tulajdonsággal | Az adattárhoz való csatlakozáshoz használt [integrációs](concepts-integration-runtime.md) modul. Használhat Azure Integration Runtime vagy saját üzemeltetésű Integration Runtime (ha az adattár egy magánhálózaton található). Ha nincs megadva, az alapértelmezett Azure Integration Runtime használja. |Nem |

>[!NOTE]
>Az egyszerű szolgáltatás hitelesítését csak "AzureBlobStorage" típusú társított szolgáltatás támogatja, de nem korábbi "AzureStorage" típusú társított szolgáltatás.

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

Az adatok előállítója [felügyelt identitással társítható az Azure-erőforrásokhoz](data-factory-service-identity.md), amely az adott adatelőállítót képviseli. Ezt a felügyelt identitást közvetlenül használhatja a blob Storage-hitelesítéshez, hasonlóan a saját egyszerű szolgáltatásnév használatához. Ez lehetővé teszi, hogy ez a kijelölt gyár hozzáférjen az adatokhoz, és onnan másolja a blob Storage-ba.

Tekintse át az Azure [Storage-hoz való hozzáférés hitelesítése](../storage/common/storage-auth-aad.md) az Azure Storage-hitelesítéshez Azure Active Directory az általános lehetőséget. Ha felügyelt identitásokat szeretne használni az Azure-erőforrások hitelesítéséhez, kövesse az alábbi lépéseket:

1. Az [adat-előállító által felügyelt személyazonossági adatok beolvasása](data-factory-service-identity.md#retrieve-managed-identity) a gyári szolgáltatással együtt GENERÁLT "Service Identity Application id" értékének másolásával.

2. A felügyelt identitás megfelelő engedélyének megadása az Azure Blob Storage-ban. A szerepkörökkel kapcsolatos további részletekért tekintse meg az [Azure Storage-adatokhoz való hozzáférési jogosultságok kezelése a RBAC](../storage/common/storage-auth-aad-rbac.md) című témakört.

    - **Forrásként**a hozzáférés-vezérlés (iam) mezőben adjon meg legalább **Storage blob adatolvasói** szerepkört.
    - Fogadóként a **hozzáférés-vezérlés**(iam) mezőben adja meg legalább a **Storage blob adatközreműködői** szerepkört.

>[!IMPORTANT]
>Ha a blobból (forrásként vagy átmenetiként) lévő adatok betöltését végzi a SQL Data Warehouseba, ha felügyelt identitásos hitelesítést használ a Blobhoz, akkor az [útmutató](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) 1. és 2. lépését is kövesse az Azure Active SQL Database-kiszolgáló regisztrálásához. A könyvtár (Azure AD) és a 2) rendelje hozzá a Storage blob adatközreműködői szerepkört a SQL Database-kiszolgálóhoz; a REST-et a Data Factory kezeli. Ha a blob Storage egy Azure Virtual Network-végponttal van konfigurálva, akkor az adatoknak a használatával történő betöltéséhez használja a felügyelt identitások hitelesítését.

Ezek a tulajdonságok egy Azure Blob Storage-beli társított szolgáltatás esetén támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A Type tulajdonságot **AzureBlobStorage**értékre kell beállítani. |Igen |
| serviceEndpoint | Az Azure Blob Storage szolgáltatás végpontját a `https://<accountName>.blob.core.windows.net/` mintázattal adhatja meg. |Igen |
| Connectvia tulajdonsággal | Az adattárhoz való csatlakozáshoz használt [integrációs](concepts-integration-runtime.md) modul. Használhat Azure Integration Runtime vagy saját üzemeltetésű Integration Runtime (ha az adattár egy magánhálózaton található). Ha nincs megadva, az alapértelmezett Azure Integration Runtime használja. |Nem |

> [!NOTE]
> Az Azure-erőforrások hitelesítésének felügyelt identitásait csak "AzureBlobStorage" típusú társított szolgáltatás támogatja, de nem korábbi "AzureStorage" típusú társított szolgáltatás. 

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

A következő tulajdonságokat támogatja az Azure Blob `location` beállításokban a Format-alapú adatkészletben:

| Tulajdonság   | Leírás                                                  | Szükséges |
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

### <a name="legacy-dataset-model"></a>Örökölt adatkészlet-modell

>[!NOTE]
>A következő adatkészlet-modell továbbra is támogatott, ha visszafelé kompatibilis. Javasoljuk, hogy használja a fenti szakaszban említett új modellt, és az ADF authoring felhasználói felülete átvált az új modell generálására.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | Az adatkészlet Type tulajdonságát **AzureBlob**értékre kell állítani. |Igen |
| folderPath | A tároló és a mappa elérési útja a blob Storage-ban. <br/><br/>A helyettesítő karakteres szűrő a tároló neve nélkül használható. Az engedélyezett helyettesítő karakterek a következők: `*` (nulla vagy több karakternek felel meg) és `?` (a nulla vagy egyetlen karakternek felel meg); a `^` használatával elkerülheti, hogy a tényleges mappanév helyettesítő karakterrel vagy a menekülési karakterrel rendelkezik-e a belsejében. <br/><br/>Példák: myblobcontainer/myblobfolder/, további példák a [mappák és a fájlok szűrése példákban](#folder-and-file-filter-examples). |Igen a másolási/keresési tevékenységhez, nem a GetMetadata tevékenységhez |
| fileName | A blob (ok) **neve vagy helyettesítő szűrője** a megadott "folderPath" elemnél. Ha nem ad meg értéket ehhez a tulajdonsághoz, az adatkészlet a mappában található összes blobra mutat. <br/><br/>A Filter (szűrő) esetében az engedélyezett helyettesítő karakterek a következők: `*` (nulla vagy több karakternek felel meg) és `?` (a nulla vagy egy karakternek felel meg).<br/>-1. példa: `"fileName": "*.csv"`<br/>-2. példa: `"fileName": "???20180427.txt"`<br/>A `^` használatával elkerülheti, ha a tényleges fájlnév helyettesítő karakterrel vagy a menekülési karakterrel rendelkezik.<br/><br/>Ha nincs megadva a fájlnév egy kimeneti adatkészlethez, és a **preserveHierarchy** nincs megadva a tevékenység fogadójában, a másolási tevékenység automatikusan létrehozza a blob nevét a következő mintával: "*adat. [ tevékenység futtatási azonosítója GUID]. [GUID if FlattenHierarchy]. [formátum, ha konfigurálva]. [tömörítés, ha be van állítva]* ", például" adatfájl. 0a405f8a-93ff-4c6f-B3BE-f69616f1df7a. txt. gz "; Ha táblázatos forrásból másol a lekérdezés helyett táblanév használatával, a név minta a következő: " *[Table Name]. [ Format]. [tömörítés, ha konfigurálva]* ", például" sajáttábla. csv ". |Nem |
| modifiedDatetimeStart | A fájlok szűrése a következő attribútum alapján: utoljára módosítva. A fájlok akkor lesznek kiválasztva, ha az utolsó módosítás időpontja a `modifiedDatetimeStart` és `modifiedDatetimeEnd`közötti időtartományon belül van. Az idő az UTC-időzónára vonatkozik "2018-12-01T05:00:00Z" formátumban. <br/><br/> Ügyeljen arra, hogy az adatáthelyezés általános teljesítményét a beállítás engedélyezésével befolyásolja, ha nagy mennyiségű fájlból szeretne szűrni a fájlmegosztást. <br/><br/> A tulajdonságok értéke NULL lehet, ami azt jelenti, hogy a rendszer nem alkalmazza a file Attribute szűrőt az adatkészletre.  Ha `modifiedDatetimeStart` dátum datetime értékkel rendelkezik, de a `modifiedDatetimeEnd` NULL értékű, akkor azok a fájlok lesznek kiválasztva, amelyek utolsó módosított attribútuma nagyobb vagy egyenlő, mint a DateTime érték.  Ha `modifiedDatetimeEnd` dátum datetime értékkel rendelkezik, de a `modifiedDatetimeStart` NULL értékű, akkor azokat a fájlokat jelenti, amelyek utolsó módosítási attribútuma kisebb, mint a DateTime érték.| Nem |
| modifiedDatetimeEnd | A fájlok szűrése a következő attribútum alapján: utoljára módosítva. A fájlok akkor lesznek kiválasztva, ha az utolsó módosítás időpontja a `modifiedDatetimeStart` és `modifiedDatetimeEnd`közötti időtartományon belül van. Az idő az UTC-időzónára vonatkozik "2018-12-01T05:00:00Z" formátumban. <br/><br/> Ügyeljen arra, hogy az adatáthelyezés általános teljesítményét a beállítás engedélyezésével befolyásolja, ha nagy mennyiségű fájlból szeretne szűrni a fájlmegosztást. <br/><br/> A tulajdonságok értéke NULL lehet, ami azt jelenti, hogy a rendszer nem alkalmazza a file Attribute szűrőt az adatkészletre.  Ha `modifiedDatetimeStart` dátum datetime értékkel rendelkezik, de a `modifiedDatetimeEnd` NULL értékű, akkor azok a fájlok lesznek kiválasztva, amelyek utolsó módosított attribútuma nagyobb vagy egyenlő, mint a DateTime érték.  Ha `modifiedDatetimeEnd` dátum datetime értékkel rendelkezik, de a `modifiedDatetimeStart` NULL értékű, akkor azokat a fájlokat jelenti, amelyek utolsó módosítási attribútuma kisebb, mint a DateTime érték.| Nem |
| formátumban | Ha fájlokat szeretne másolni a fájl alapú tárolók között (bináris másolás), ugorja át a formátum szakaszt a bemeneti és a kimeneti adatkészlet-definíciókban is.<br/><br/>Ha a fájlokat egy adott formátummal szeretné elemezni vagy előállítani, a következő fájlformátum-típusok támogatottak: **Szövegformátum**, **JsonFormat**, **AvroFormat**, **OrcFormat**és **ParquetFormat**. A **Type (típus** ) tulajdonságot állítsa a **Format** értékre a következő értékek egyikére. További információ: [szöveg formátuma](supported-file-formats-and-compression-codecs.md#text-format), JSON- [Formátum](supported-file-formats-and-compression-codecs.md#json-format), [Avro formátum](supported-file-formats-and-compression-codecs.md#avro-format), [ork-formátum](supported-file-formats-and-compression-codecs.md#orc-format)és [parketta formátuma](supported-file-formats-and-compression-codecs.md#parquet-format) . |Nem (csak bináris másolási forgatókönyv esetén) |
| tömörítés | Adja meg az adattömörítés típusát és szintjét. További információ: [támogatott fájlformátumok és tömörítési kodekek](supported-file-formats-and-compression-codecs.md#compression-support).<br/>A támogatott típusok a **gzip**, a **deflate**, a **BZip2**és a **ZipDeflate**.<br/>A támogatott szintek **optimálisak** és **leggyorsabbak**. |Nem |

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

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz a blob Storage forrás és a fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="blob-storage-as-a-source-type"></a>BLOB Storage forrás típusaként

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

A következő tulajdonságokat támogatja az Azure Blob `storeSettings` beállításokban a Format-alapú másolási forrásban:

| Tulajdonság                 | Leírás                                                  | Szükséges                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | A `storeSettings` alatti Type tulajdonságot **AzureBlobStorageReadSetting**értékre kell beállítani. | Igen                                           |
| rekurzív                | Azt jelzi, hogy az adatok rekurzív módon olvashatók-e az almappákból, vagy csak a megadott mappából. Vegye figyelembe, hogy ha a rekurzív értéke TRUE (igaz), a fogadó pedig egy fájl alapú tároló, a fogadó nem másolja vagy hozza létre az üres mappát vagy almappát. Az engedélyezett értékek: **true** (alapértelmezett) és **false (hamis**). | Nem                                            |
| wildcardFolderPath       | A mappa elérési útja a forrás mappák szűréséhez az adatkészletben konfigurált megadott tárolóban helyettesítő karakterekkel. <br>Az engedélyezett helyettesítő karakterek a következők: `*` (nulla vagy több karakternek felel meg) és `?` (a nulla vagy egyetlen karakternek felel meg); a `^` használatával elkerülheti, hogy a tényleges mappanév helyettesítő karakterrel vagy a menekülési karakterrel rendelkezik-e a belsejében. <br>További példákat a [mappák és a fájlok szűrésére szolgáló példákban](#folder-and-file-filter-examples)talál. | Nem                                            |
| wildcardFileName         | A forrásfájl szűréséhez a megadott tároló + folderPath/wildcardFolderPath helyettesítő karakterekkel rendelkező fájlnév. <br>Az engedélyezett helyettesítő karakterek a következők: `*` (nulla vagy több karakternek felel meg) és `?` (a nulla vagy egyetlen karakternek felel meg); a `^` használatával elkerülheti, hogy a tényleges mappanév helyettesítő karakterrel vagy a menekülési karakterrel rendelkezik-e a belsejében.  További példákat a [mappák és a fájlok szűrésére szolgáló példákban](#folder-and-file-filter-examples)talál. | Igen, ha a `fileName` nincs megadva az adatkészletben |
| modifiedDatetimeStart    | A fájlok szűrése a következő attribútum alapján: utoljára módosítva. A fájlok akkor lesznek kiválasztva, ha az utolsó módosítás időpontja a `modifiedDatetimeStart` és `modifiedDatetimeEnd`közötti időtartományon belül van. Az idő az UTC-időzónára vonatkozik "2018-12-01T05:00:00Z" formátumban. <br> A tulajdonságok értéke lehet NULL, ami azt jelenti, hogy nem lesz alkalmazva a file Attribute szűrő az adatkészletre.  Ha `modifiedDatetimeStart` dátum datetime értékkel rendelkezik, de a `modifiedDatetimeEnd` NULL értékű, akkor azok a fájlok lesznek kiválasztva, amelyek utolsó módosított attribútuma nagyobb vagy egyenlő, mint a DateTime érték.  Ha `modifiedDatetimeEnd` dátum datetime értékkel rendelkezik, de a `modifiedDatetimeStart` NULL értékű, akkor azokat a fájlokat jelenti, amelyek utolsó módosítási attribútuma kisebb, mint a DateTime érték. | Nem                                            |
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
                    "type": "DelimitedTextReadSetting",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AzureBlobStorageReadSetting",
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

#### <a name="legacy-source-model"></a>Örökölt forrás modell

>[!NOTE]
>A következő másolási forrás modell továbbra is támogatott, ha visszafelé kompatibilis. Azt javasoljuk, hogy használja a fentiekben említett új modellt, és az ADF authoring felhasználói felülete átvált az új modell generálására.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának Type tulajdonságát **BlobSource**értékre kell állítani. |Igen |
| rekurzív | Azt jelzi, hogy az adatok rekurzív módon olvashatók-e az almappákból, vagy csak a megadott mappából. Vegye figyelembe, hogy ha a rekurzív értéke TRUE (igaz), a fogadó pedig egy fájl alapú tároló, a fogadó nem másolja vagy hozza létre az üres mappát vagy almappát.<br/>Az engedélyezett értékek: **true** (alapértelmezett) és **false (hamis**). | Nem |
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

### <a name="blob-storage-as-a-sink-type"></a>BLOB Storage fogadó típusként

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

A következő tulajdonságokat támogatja az Azure Blob `storeSettings` beállításokban a Format-alapú másolási fogadóban:

| Tulajdonság                 | Leírás                                                  | Szükséges |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | A `storeSettings` alatti Type tulajdonságot **AzureBlobStorageWriteSetting**értékre kell beállítani. | Igen      |
| copyBehavior             | Meghatározza a másolási viselkedést, ha a forrás fájl-alapú adattárból származó fájlok.<br/><br/>Az engedélyezett értékek a következők:<br/><b>-PreserveHierarchy (alapértelmezett)</b>: megőrzi a fájl-hierarchiát a célmappában. A forrásfájl a forrás mappájához relatív elérési útja megegyezik a célfájl relatív elérési útjával.<br/><b>-FlattenHierarchy</b>: a forrás mappából származó összes fájl a célmappa első szintjén van. A célként megadott fájlok automatikusan generált névvel rendelkeznek. <br/><b>-MergeFiles</b>: az összes fájlt egyesíti a forrás mappájából egy fájlba. Ha meg van adva a fájl vagy a blob neve, az egyesített fájl neve a megadott név. Ellenkező esetben ez egy automatikusan létrehozott fájl neve. | Nem       |
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
                    "type": "AzureBlobStorageWriteSetting",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

#### <a name="legacy-sink-model"></a>Örökölt fogadó modell

>[!NOTE]
>A következő másolási fogadó modell továbbra is támogatott a visszamenőleges kompatibilitás érdekében. Azt javasoljuk, hogy használja a fentiekben említett új modellt, és az ADF authoring felhasználói felülete átvált az új modell generálására.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadójának Type tulajdonságát **BlobSink**értékre kell állítani. |Igen |
| copyBehavior | Meghatározza a másolási viselkedést, ha a forrás fájl-alapú adattárból származó fájlok.<br/><br/>Az engedélyezett értékek a következők:<br/><b>-PreserveHierarchy (alapértelmezett)</b>: megőrzi a fájl-hierarchiát a célmappában. A forrásfájl a forrás mappájához relatív elérési útja megegyezik a célfájl relatív elérési útjával.<br/><b>-FlattenHierarchy</b>: a forrás mappából származó összes fájl a célmappa első szintjén van. A célként megadott fájlok automatikusan generált névvel rendelkeznek. <br/><b>-MergeFiles</b>: az összes fájlt egyesíti a forrás mappájából egy fájlba. Ha meg van adva a fájl vagy a blob neve, az egyesített fájl neve a megadott név. Ellenkező esetben ez egy automatikusan létrehozott fájl neve. | Nem |
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

### <a name="folder-and-file-filter-examples"></a>Példák a mappák és a fájlok szűrésére

Ez a szakasz a mappa elérési útjának és fájlnevének a helyettesítő karakteres szűrőkkel való viselkedését írja le.

| folderPath | fileName | rekurzív | A forrás mappa szerkezete és a szűrő eredménye (a **félkövérrel szedett** fájlok beolvasása)|
|:--- |:--- |:--- |:--- |
| `container/Folder*` | (üres, alapértelmezett használata) | hamis | tároló<br/>&nbsp;&nbsp;&nbsp;&nbsp;mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**file1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**fájl2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fájl3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `container/Folder*` | (üres, alapértelmezett használata) | igaz | tároló<br/>&nbsp;&nbsp;&nbsp;&nbsp;mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**file1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**fájl2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**fájl3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `container/Folder*` | `*.csv` | hamis | tároló<br/>&nbsp;&nbsp;&nbsp;&nbsp;mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**file1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fájl3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `container/Folder*` | `*.csv` | igaz | tároló<br/>&nbsp;&nbsp;&nbsp;&nbsp;mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**file1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**fájl3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |

### <a name="some-recursive-and-copybehavior-examples"></a>Néhány rekurzív és copyBehavior példa

Ez a szakasz a rekurzív és copyBehavior értékek különböző kombinációinak másolási műveletének eredményét írja le.

| rekurzív | copyBehavior | Forrás mappa szerkezete | Eredményül kapott cél |
|:--- |:--- |:--- |:--- |
| igaz |preserveHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A célmappa Mappa1 ugyanazzal a struktúrával jön létre, mint a forrás:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| igaz |flattenHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A célként megadott Mappa1 a következő szerkezettel jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan generált neve a file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan generált neve a Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan generált neve a fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan generált neve a File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan generált neve a File5 |
| igaz |mergeFiles | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A célként megadott Mappa1 a következő szerkezettel jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1 + Fájl2 + fájl3 + File4 + File5 tartalma egyetlen fájlba van egyesítve egy automatikusan létrehozott fájlnévvel. |
| hamis |preserveHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A célmappa Mappa1 az alábbi struktúrával jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/><br/>A fájl3, a File4 és a File5 Subfolder1 nem kerül felvételre. |
| hamis |flattenHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A célmappa Mappa1 az alábbi struktúrával jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan generált neve a file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan generált neve a Fájl2<br/><br/>A fájl3, a File4 és a File5 Subfolder1 nem kerül felvételre. |
| hamis |mergeFiles | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A célmappa Mappa1 a következő szerkezettel jön létre<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;file1 + Fájl2 tartalom egyetlen fájlba van egyesítve, és automatikusan létrehozott fájlnevet tartalmaz. a file1 automatikusan generált neve<br/><br/>A fájl3, a File4 és a File5 Subfolder1 nem kerül felvételre. |

## <a name="mapping-data-flow-properties"></a>Adatfolyam-tulajdonságok leképezése

A forrás- [átalakítás](data-flow-source.md) és a fogadó [átalakítás](data-flow-sink.md) részleteinek megismerése a leképezési folyamatokban.

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>GetMetadata tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [GetMetaData tevékenységet](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Tevékenység tulajdonságainak törlése

A tulajdonságok részleteinek megismeréséhez tekintse meg a [tevékenység törlése](delete-activity.md) lehetőséget.

## <a name="next-steps"></a>Következő lépések

A Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md##supported-data-stores-and-formats).

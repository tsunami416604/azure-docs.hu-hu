---
title: Adatok másolása és átalakítása az Azure Blob storage-ban
description: Megtudhatja, hogyan másolhat adatokat a Blob storage-ba és onnan, és hogyan alakíthatja át az adatokat a Blob storage-ban a Data Factory használatával.
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/09/2020
ms.openlocfilehash: dd13a08b3c2f63baf509efbb730032edd4eba61a
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011548"
---
# <a name="copy-and-transform-data-in-azure-blob-storage-by-using-azure-data-factory"></a>Adatok másolása és átalakítása az Azure Blob storage-ban az Azure Data Factory használatával

> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-azure-blob-connector.md)
> * [Aktuális verzió](connector-azure-blob-storage.md)

Ez a cikk bemutatja, hogyan használhatja a másolási tevékenység et az Azure Data Factory adatok másolására és az Azure Blob storage-ba, és az Adatfolyam használatával alakítja át az adatokat az Azure Blob storage-ban. Az Azure Data Factory ról a [bevezető cikkben](introduction.md)olvashat.

>[!TIP]
>A Data Lake vagy az adattárház áttelepítési forgatókönyv, további információ [az Azure Data Factory segítségével áttelepíteni az adatokat a data lake vagy adattárház az Azure-ba.](data-migration-guidance-overview.md)

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az Azure Blob-összekötő a következő tevékenységek esetén támogatott:

- [Tevékenység másolása](copy-activity-overview.md) [támogatott forrás/fogadó mátrixcal](copy-activity-overview.md)
- [Adatfolyam leképezése](concepts-data-flow-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)
- [Tevékenység törlése](delete-activity.md)

Másolási tevékenység esetén ez a Blob storage-összekötő a következőket támogatja:

- Blobok másolása általános célú Azure storage-fiókokba és a gyors/gyors blob tárolóba. 
- Blobok másolása fiókkulcs, szolgáltatás megosztott hozzáférés-aláírás, egyszerű szolgáltatás vagy felügyelt identitások azure-erőforrások hitelesítései használatával.
- Blobok másolása blokkból, hozzáfűző vagy lapblobokból, és adatok másolása csak blokkblobok.
- Blobok másolása a megfelelő módon, illetve a [támogatott fájlformátumokkal és tömörítési kodekekkel](supported-file-formats-and-compression-codecs.md)rendelkező blobok analizálása vagy létrehozása.
- [A fájl metaadatainak megőrzése másolás közben](#preserve-metadata-during-copy).

>[!IMPORTANT]
>Ha engedélyezi a **Megbízható Microsoft-szolgáltatások hozzáférését ehhez a tárfiókhoz az** Azure Storage tűzfalbeállításaiban, és az Azure-integrációs futásidejű használatával szeretne csatlakozni a Blob Storage-hoz, felügyelt [identitás-hitelesítést kell használnia.](#managed-identity)

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok a Blob storage-ra jellemző Data Factory-entitások definiálására használt tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

Az Azure Blob-összekötő a következő hitelesítési típusokat támogatja, tekintse meg a megfelelő szakaszt a részletekről:

- [Fiókkulcs hitelesítése](#account-key-authentication)
- [Megosztott hozzáférésű aláírás hitelesítése](#shared-access-signature-authentication)
- [Egyszerű szolgáltatás hitelesítése](#service-principal-authentication)
- [Felügyelt identitások az Azure-erőforrások hitelesítéséhez](#managed-identity)

>[!NOTE]
>Ha a PolyBase használatával adatokat tölt be az SQL Data Warehouse-ba, ha a forrás- vagy átmeneti Blob-tároló virtuális hálózati végponttal van konfigurálva, a PolyBase által előírt felügyelt identitás-hitelesítést kell használnia, és saját üzemeltetésű integrációs futásidőt kell használnia a 3.18-as vagy újabb verzióval. Tekintse meg a [felügyelt identitás-hitelesítésszakaszt](#managed-identity) további konfigurációs előfeltételekkel.

>[!NOTE]
>A HDInsights és az Azure Machine Learning-tevékenységek csak az Azure Blob storage-fiók kulcsának hitelesítését támogatják.

### <a name="account-key-authentication"></a>Fiókkulcs hitelesítése

A tárfiók kulcsának hitelesítéséhez a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A típus tulajdonságot **az AzureBlobStorage** (javasolt) vagy **az AzureStorage (lásd** az alábbi megjegyzéseket) értékre kell állítani. |Igen |
| connectionString (kapcsolati karakterlánc) | Adja meg a connectionString tulajdonság storage-hoz való csatlakozásához szükséges adatokat. <br/> Fiókkulcsot is helyezhet el az Azure `accountKey` Key Vaultban, és kihúzhatja a konfigurációt a kapcsolati karakterláncból. További részleteket az [Azure Key Vault-cikkben](store-credentials-in-key-vault.md) a következő minták és áruházi hitelesítő adatok című cikkben talál. |Igen |
| connectVia | Az adattárhoz való csatlakozáshoz használandó [integrációs futásidő.](concepts-integration-runtime.md) Használhatja az Azure-integrációs runtime vagy saját üzemeltetésű integrációs runtime (ha az adattár egy magánhálózat). Ha nincs megadva, az alapértelmezett Azure-integrációs runtime-ot használja. |Nem |

>[!NOTE]
>Másodlagos Blob Service Endpoint nem támogatott fiókkulcs-hitelesítés használatakor. Más hitelesítési típusokat is használhat.

>[!NOTE]
>Ha az "AzureStorage" típusú kapcsolt szolgáltatás használata, továbbra is támogatott, ahogy van, miközben azt javasoljuk, hogy használja ezt az új "AzureBlobStorage" csatolt szolgáltatástípus megy előre.

**Példa:**

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

**Példa: fiókkulcs tárolása az Azure Key Vaultban**

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

### <a name="shared-access-signature-authentication"></a>Megosztott hozzáférésű aláírás hitelesítése

A megosztott hozzáférésű aláírás delegált hozzáférést biztosít a tárfiókban lévő erőforrásokhoz. A megosztott hozzáférésű aláírás használatával korlátozott engedélyeket adhat az ügyfélnek a tárfiókban lévő objektumokhoz egy adott ideig. Nem kell megosztania a fiókhozzáférési kulcsokat. A megosztott hozzáférésű aláírás olyan URI, amely a lekérdezési paraméterekben a tárolási erőforrás hitelesített eléréséhez szükséges összes információt magában foglalja. A megosztott hozzáférésű aláírással rendelkező tárolóerőforrások eléréséhez az ügyfélnek csak a megosztott hozzáférésű aláírást kell átadnia a megfelelő konstruktornak vagy metódusnak. A megosztott hozzáférésű aláírásokról a [Megosztott hozzáférésű aláírások: A közös hozzáférésű aláírásmodell ismertetése](../storage/common/storage-dotnet-shared-access-signature-part-1.md)című témakörben talál további információt.

> [!NOTE]
>- A Data Factory mostantól támogatja a **szolgáltatás megosztott hozzáférési aláírásait** és **a fiók megosztott hozzáférési aláírásait is.** A megosztott hozzáférésű aláírásokról további információt az [Azure Storage-erőforrásokhoz való korlátozott hozzáférés megadása megosztott hozzáférésű aláírások (SAS) használatával](../storage/common/storage-sas-overview.md)című témakörben talál.
>- A későbbi adatkészlet-konfigurációban a mappa elérési útja a tároló szintjétől kezdődő abszolút elérési út. A SAS URI-ban lévő elérési úthoz igazítva kell konfigurálnia.

A megosztott hozzáférésű aláírás-hitelesítés használatához a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A típus tulajdonságot **az AzureBlobStorage** (javasolt) vagy **az AzureStorage (lásd** az alábbi megjegyzéseket) értékre kell állítani. |Igen |
| sasUri között | Adja meg a megosztott hozzáférésű aláírás URI-hoz a storage-erőforrások, például blob/tároló. <br/>Jelölje meg ezt a mezőt SecureStringként, hogy biztonságosan tárolhatja a Data Factory-ban. SAS-jogkivonatot is helyezhet az Azure Key Vaultban az automatikus rotáció kihasználásához és a jogkivonat-rész eltávolításához. További részleteket az [Azure Key Vault-cikkben](store-credentials-in-key-vault.md) a következő minták és áruházi hitelesítő adatok című cikkben talál. |Igen |
| connectVia | Az adattárhoz való csatlakozáshoz használandó [integrációs futásidő.](concepts-integration-runtime.md) Használhatja az Azure-integrációs runtime vagy a saját üzemeltetésű integrációs runtime (ha az adattár található egy magánhálózat). Ha nincs megadva, az alapértelmezett Azure-integrációs runtime-ot használja. |Nem |

>[!NOTE]
>Ha az "AzureStorage" típusú kapcsolt szolgáltatás használata, továbbra is támogatott, ahogy van, miközben azt javasoljuk, hogy használja ezt az új "AzureBlobStorage" csatolt szolgáltatástípus megy előre.

**Példa:**

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

**Példa: fiókkulcs tárolása az Azure Key Vaultban**

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

Amikor közös hozzáférésű hozzáférési kód URI-t hoz létre, vegye figyelembe a következő pontokat:

- Állítsa be a megfelelő olvasási/írási engedélyeket az objektumokhoz a csatolt szolgáltatás (olvasás, írás, írás/írás) adat-előállítóban való használatával.
- Megfelelően állítsa be **a lejárati időt.** Győződjön meg arról, hogy a storage-objektumokhoz való hozzáférés nem jár le a folyamat aktív időszakán belül.
- Az URI-t a megfelelő tárolóban/blobban kell létrehozni az igény alapján. Egy blob megosztott hozzáférési aláírásuri-ja lehetővé teszi a Data Factory számára az adott blob elérését. A blob storage-tároló közös hozzáférési aláírásURI-ja lehetővé teszi a Data Factory számára, hogy a tárolóban lévő blobokon keresztül iterálni. Ha később több vagy kevesebb objektumhoz szeretne hozzáférést biztosítani, vagy frissíteni szeretné a megosztott hozzáférésű aláírás URI-ját, ne felejtse el frissíteni a csatolt szolgáltatást az új URI-val.

### <a name="service-principal-authentication"></a>Egyszerű szolgáltatásnév hitelesítése

Az Azure Storage egyszerű szolgáltatáshitelesítése általában [lásd: Hozzáférés hitelesítése az Azure Storage-hoz az Azure Active Directory használatával.](../storage/common/storage-auth-aad.md)

Az egyszerű szolgáltatáshitelesítés használatához hajtsa végre az alábbi lépéseket:

1. Regisztráljon egy alkalmazásentitást az Azure Active Directoryban (Azure AD) az [alkalmazás regisztrálása egy Azure AD-bérlővel](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)című könyvbe való regisztrálása című dal. Jegyezze fel a következő értékeket, amelyek segítségével definiálja a csatolt szolgáltatást:

    - Alkalmazásazonosító
    - Alkalmazáskulcs
    - Bérlőazonosító

2. Adja meg a szolgáltatás egyszerű megfelelő engedélyt az Azure Blob storage.Grant the service principal proper permission in Azure Blob storage. Tekintse meg az [Azure Storage-adatok hozzáférési jogosultságának kezelése az RBAC-mal](../storage/common/storage-auth-aad-rbac.md) a szerepkörök további részleteit.

    - **Forrásként**a hozzáférés-vezérlés (IAM) programban legalább **Storage Blob Data Reader** szerepkört adjon.
    - **A fogadó,** a hozzáférés-vezérlés (IAM), adjon meg legalább **Storage Blob Data Contributor** szerepkört.

Ezek a tulajdonságok egy Azure Blob storage-hoz csatolt szolgáltatás esetén támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A típustulajdonságot **Az AzureBlobStorage**tulajdonságra kell állítani. |Igen |
| serviceEndpoint | Adja meg az Azure Blob storage `https://<accountName>.blob.core.windows.net/`szolgáltatás végpontját a mintával. |Igen |
| servicePrincipalId | Adja meg az alkalmazás ügyfélazonosítóját. | Igen |
| servicePrincipalKey | Adja meg az alkalmazás kulcsát. Jelölje meg ezt a mezőt **SecureStringként** a Data Factory biztonságos tárolásához, vagy [hivatkozzon az Azure Key Vaultban tárolt titkos fájlokra.](store-credentials-in-key-vault.md) | Igen |
| Bérlő | Adja meg a bérlői adatokat (tartománynév vagy bérlőazonosító), amely alatt az alkalmazás található. Az egér egérrel az Azure Portal jobb felső sarkában való lebegtetésével. | Igen |
| connectVia | Az adattárhoz való csatlakozáshoz használandó [integrációs futásidő.](concepts-integration-runtime.md) Használhatja az Azure-integrációs runtime vagy saját üzemeltetésű integrációs runtime (ha az adattár egy magánhálózat). Ha nincs megadva, az alapértelmezett Azure-integrációs runtime-ot használja. |Nem |

>[!NOTE]
>A szolgáltatásegyszerű hitelesítést csak az "AzureBlobStorage" típusú kapcsolt szolgáltatás támogatja, de a korábbi "AzureStorage" típusú kapcsolt szolgáltatás nem.

**Példa:**

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

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Felügyelt identitások az Azure-erőforrások hitelesítéséhez

Az adat-előállító [azure-erőforrások felügyelt identitással](data-factory-service-identity.md)társítható, amely ezt az adott adat-előállítót jelöli. Ezt a felügyelt identitást közvetlenül használhatja a Blob storage-hitelesítéshez, hasonlóan a saját egyszerű szolgáltatáshoz. Lehetővé teszi, hogy ez a kijelölt gyári adatok elérése és másolása a Blob storage.It allows this designated factory to access and copy data from/to your Blob storage.

Lásd: [Hozzáférés hitelesítése az Azure Storage-hoz az Azure Active Directory](../storage/common/storage-auth-aad.md) az Azure Storage-hitelesítés általában. Ha felügyelt identitásokat szeretne használni az Azure-erőforrások hitelesítéséhez, kövesse az alábbi lépéseket:

1. [Az adat-előállító felügyelt identitásadatok lekéréséhez](data-factory-service-identity.md#retrieve-managed-identity) másolja a gyárral együtt létrehozott **felügyelt identitásobjektum-azonosító** értékét.

2. Adja meg a felügyelt identitás megfelelő engedélyt az Azure Blob storage-ban. Tekintse meg az [Azure Storage-adatok hozzáférési jogosultságának kezelése az RBAC-mal](../storage/common/storage-auth-aad-rbac.md) a szerepkörök további részleteit.

    - **Forrásként**a hozzáférés-vezérlés (IAM) programban legalább **Storage Blob Data Reader** szerepkört adjon.
    - **A fogadó,** a hozzáférés-vezérlés (IAM), adjon meg legalább **Storage Blob Data Contributor** szerepkört.

>[!IMPORTANT]
>Ha a PolyBase használatával tölti be az adatokat a Blobból (forrásként vagy átmenetiként) az SQL Data Warehouse-ba, a Blob felügyelt identitáshitelesítésének használatakor győződjön meg arról, hogy [az útmutató](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) 1. a többit a Data Factory kezeli. Ha a Blob storage konfigurálva van egy Azure virtuális hálózati végpont, a PolyBase használatával adatok betöltése belőle, a PolyBase által igényelt felügyelt identitás-hitelesítést kell használnia.

Ezek a tulajdonságok egy Azure Blob storage-hoz csatolt szolgáltatás esetén támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A típustulajdonságot **Az AzureBlobStorage**tulajdonságra kell állítani. |Igen |
| serviceEndpoint | Adja meg az Azure Blob storage `https://<accountName>.blob.core.windows.net/`szolgáltatás végpontját a mintával. |Igen |
| connectVia | Az adattárhoz való csatlakozáshoz használandó [integrációs futásidő.](concepts-integration-runtime.md) Használhatja az Azure-integrációs runtime vagy saját üzemeltetésű integrációs runtime (ha az adattár egy magánhálózat). Ha nincs megadva, az alapértelmezett Azure-integrációs runtime-ot használja. |Nem |

> [!NOTE]
> Felügyelt identitások az Azure-erőforrások hitelesítését csak a "AzureBlobStorage" típusú kapcsolt szolgáltatás, de nem a korábbi "AzureStorage" típusú kapcsolt szolgáltatás. 

**Példa:**

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

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [Adatkészletek](concepts-datasets-linked-services.md) című cikkben olvashatja. 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Az Azure Blob a következő `location` tulajdonságokat támogatja a formátumalapú adatkészlet beállításai között:

| Tulajdonság   | Leírás                                                  | Kötelező |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Az adatkészletben lévő hely típustulajdonságát **AzureBlobStorageLocation**beállításra kell állítani. | Igen      |
| tároló  | A blob tároló.                                          | Igen      |
| folderPath | Az adott tároló alatti mappa elérési útja. Ha helyettesítő karaktert szeretne használni a mappa szűréséhez, hagyja ki ezt a beállítást, és adja meg a tevékenységforrás beállításaiban. | Nem       |
| fileName   | A fájl név alatt a megadott tároló + folderPath. Ha helyettesítő karaktert szeretne használni a fájlok szűréséhez, hagyja ki ezt a beállítást, és adja meg a tevékenységforrás beállításaiban. | Nem       |

**Példa:**

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

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok](concepts-pipelines-activities.md) című cikkben olvashat. Ez a szakasz a Blob tárolóforrása és -fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="blob-storage-as-a-source-type"></a>Blob storage forrástípusként

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Az Azure Blob a következő `storeSettings` tulajdonságokat támogatja a formátumalapú másolási forrás beállításai között:

| Tulajdonság                 | Leírás                                                  | Kötelező                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | A típustulajdonságot `storeSettings` az **AzureBlobStorageReadSettings**beállításra kell állítani. | Igen                                           |
| Rekurzív                | Azt jelzi, hogy az adatok olvasása rekurzív anameddig az almappákból vagy csak a megadott mappából történik. Ne feledje, hogy ha a rekurzív érték igaz, és a fogadó fájlalapú tároló, a program nem másolja vagy hozza létre az üres mappát vagy almappát a fogadóban. Az engedélyezett értékek **igazak** (alapértelmezettek) és **hamisak.** | Nem                                            |
| Előtag                   | Előtag a blob nevét az adott tároló ban konfigurált adatforrásblobok szűrő. Azok a blobok, amelyeknek a neve ezzel az előtaggal kezdődik, ki vannak jelölve. <br>Csak akkor `wildcardFolderPath` `wildcardFileName` érvényes, ha nincsenek megadva tulajdonságok. | Nem                                                          |
| helyettesítő mappaelérési út       | A mappa elérési útja helyettesítő karakterekkel az adatkészletben a forrásmappák szűrésére konfigurált tároló alatt. <br>Az engedélyezett helyettesítő `*` karakterek a következők: `?` (nulla vagy több karakternek felel meg) és (nulla vagy egy karakternek felel meg); akkor `^` menekülhet, ha a mappa tényleges neve helyettesítő karaktert használ, vagy ha ez az escape karakter van benne. <br>További példák a [Mappa- és fájlszűrő-példákban](#folder-and-file-filter-examples)találhatók. | Nem                                            |
| helyettesítő fájlneve         | A forrásfájlok szűréséhez helyettesítő karakterekkel rendelkező fájlnév a megadott tároló + folderPath/wildcardFolderPath alatt. <br>Az engedélyezett helyettesítő `*` karakterek a következők: `?` (nulla vagy több karakternek felel meg) és (nulla vagy egy karakternek felel meg); akkor `^` menekülhet, ha a mappa tényleges neve helyettesítő karaktert használ, vagy ha ez az escape karakter van benne.  További példák a [Mappa- és fájlszűrő-példákban](#folder-and-file-filter-examples)találhatók. | Igen, `fileName` ha nincs megadva az adatkészletben |
| modifiedDatetimeStart    | A fájlok szűrője az attribútum alapján: Utolsó módosítás. A fájlok akkor lesznek kiválasztva, ha az `modifiedDatetimeStart` `modifiedDatetimeEnd`utolsó módosítási idejük a és a közötti időtartományon belül van. Az idő az UTC időzónára a "2018-12-01T05:00:00Z" formátumban kerül alkalmazásra. <br> A tulajdonságok null értékűek lehetnek, ami azt jelenti, hogy az adatkészletre nem lesz fájlattribútum-szűrő alkalmazva.  Ha `modifiedDatetimeStart` datetime értékkel rendelkezik, de `modifiedDatetimeEnd` NULL, az azt jelenti, hogy azokat a fájlokat, amelyek utolsó módosított attribútuma nagyobb vagy egyenlő, mint a datetime érték, ki lesznek jelölve.  Ha `modifiedDatetimeEnd` datetime értékkel rendelkezik, de `modifiedDatetimeStart` NULL, az azt jelenti, hogy azutolsó módosított attribútum kisebb, mint a datetime érték, ki lesznek jelölve. | Nem                                            |
| modifiedDatetimeEnd      | Ugyanaz, mint fent.                                               | Nem                                            |
| maxConcurrentConnections | A tárolóhoz egyidejűleg csatlakozó kapcsolatok száma. Csak akkor adja meg, ha korlátozni szeretné az egyidejű kapcsolatot az adattárhoz. | Nem                                            |

> [!NOTE]
> Parketta/tagolt szövegformátum esetén a következő szakaszban említett **BlobSource** típusú másolási tevékenység forrása továbbra is támogatott a visszamenőleges kompatibilitás érdekében. Javasoljuk, hogy használja ezt az új modellt a jövőben, és az ADF szerzői felhasználói felülete áttért az új típusok létrehozására.

**Példa:**

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

### <a name="blob-storage-as-a-sink-type"></a>Blob storage mint fogadótípus

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Az Azure Blob a következő `storeSettings` tulajdonságokat támogatja a formátumalapú példánygyűjtő beállításai alatt:

| Tulajdonság                 | Leírás                                                  | Kötelező |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | A típustulajdonságot `storeSettings` az **AzureBlobStorageWriteSettings**beállításra kell állítani. | Igen      |
| copyBehavior (Másként)             | Azt a másolási viselkedést határozza meg, amikor a forrás fájlalapú adattárból származó fájlok.<br/><br/>Az engedélyezett értékek a következők:<br/><b>- MegőrzéseHierarchy (alapértelmezett)</b>: Megőrzi a fájlhierarchiát a célmappában. A forrásfájl forrásmappához viszonyított elérési útja megegyezik a célfájl célmappához való relatív elérési útjával.<br/><b>- Ahierarcha összeolvasztása</b>: A forrásmappából származó összes fájl a célmappa első szintjén található. A célfájlok nak automatikusan generált neve van. <br/><b>- MergeFiles</b>: Egyesíti az összes fájlt a forrás mappát egy fájlt. Ha a fájl vagy a blob neve meg van adva, az egyesített fájlnév a megadott név. Ellenkező esetben ez egy automatikusan létrehozott fájlnév. | Nem       |
| blockSizeInMB | Adja meg a blokk méretét MB-ban, amely a blobok blokkolására használt adatok írásához szolgál. További információ [a blokkblobokról.](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) <br/>Az engedélyezett érték **4 és 100 MB között**van. <br/>Alapértelmezés szerint az ADF automatikusan meghatározza a blokkméretet a forrástár típusa és az adatok alapján. A Blobba történő nem bináris másolás esetén az alapértelmezett blokkméret 100 MB, hogy legbőszebb 4,95 TB-os adatokat férjen el. Előfordulhat, hogy nem optimális, ha az adatok nem nagy, különösen akkor, ha a saját üzemeltetésű integrációs futásidő gyenge hálózattal, ami a működési időtúl- vagy teljesítményprobléma. Explicit módon megadhatja a blokk mérete, míg biztosítja blockSizeInMB * 50000 elég nagy ahhoz, hogy tárolja az adatokat, különben másolási tevékenység futtatása sikertelen lesz. | Nem |
| maxConcurrentConnections | A tárolóhoz egyidejűleg csatlakozó kapcsolatok száma. Csak akkor adja meg, ha korlátozni szeretné az egyidejű kapcsolatot az adattárhoz. | Nem       |

**Példa:**

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

### <a name="folder-and-file-filter-examples"></a>Példák mappa- és fájlszűrőre

Ez a szakasz a mappa elérési útjának és a helyettesítő szűrőkkel rendelkező fájlnévnek a viselkedését ismerteti.

| folderPath | fileName | Rekurzív | A forrásmappa szerkezete és a szűrő eredménye (a **félkövérrel szedett** fájlok beolvasása)|
|:--- |:--- |:--- |:--- |
| `container/Folder*` | (üres, használja az alapértelmezettet) | hamis | tároló<br/>&nbsp;&nbsp;&nbsp;&nbsp;MappaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Egy másikFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl6.csv |
| `container/Folder*` | (üres, használja az alapértelmezettet) | igaz | tároló<br/>&nbsp;&nbsp;&nbsp;&nbsp;MappaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Egy másikFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl6.csv |
| `container/Folder*` | `*.csv` | hamis | tároló<br/>&nbsp;&nbsp;&nbsp;&nbsp;MappaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Egy másikFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl6.csv |
| `container/Folder*` | `*.csv` | igaz | tároló<br/>&nbsp;&nbsp;&nbsp;&nbsp;MappaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Egy másikFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl6.csv |

### <a name="some-recursive-and-copybehavior-examples"></a>Néhány rekurzív és copyBehavior példák

Ez a szakasz a Copy művelet viselkedését ismerteti a rekurzív és copyBehavior értékek különböző kombinációihoz.

| Rekurzív | copyBehavior (Másként) | Forrásmappa szerkezete | Eredményként kapott cél |
|:--- |:--- |:--- |:--- |
| igaz |megőrzési hierarchia | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5 | A Folder1 célmappa ugyanazzal a szerkezettel jön létre, mint a forrás:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5 |
| igaz |összeolvasztási hierarchia | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5 | A célmappa1 a következő struktúrával jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;fájl automatikusan létrehozott neve1<br/>&nbsp;&nbsp;&nbsp;&nbsp;fájl 2 automatikusan létrehozott neve<br/>&nbsp;&nbsp;&nbsp;&nbsp;fájl 3 automatikusan létrehozott neve<br/>&nbsp;&nbsp;&nbsp;&nbsp;fájl 4 automatikusan létrehozott neve<br/>&nbsp;&nbsp;&nbsp;&nbsp;fájl automatikusan létrehozott neve |
| igaz |mergeFiles | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5 | A célmappa1 a következő struktúrával jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;A fájl1 + File2 + File3 + File4 + File5 tartalom egyetlen fájlba egyesül, automatikusan generált fájlnévvel. |
| hamis |megőrzési hierarchia | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5 | A Folder1 célmappa a következő struktúrával jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/><br/>A File3, File4 és File5 mappát nem veszi fel a rendszer. |
| hamis |összeolvasztási hierarchia | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5 | A Folder1 célmappa a következő struktúrával jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;fájl automatikusan létrehozott neve1<br/>&nbsp;&nbsp;&nbsp;&nbsp;fájl 2 automatikusan létrehozott neve<br/><br/>A File3, File4 és File5 mappát nem veszi fel a rendszer. |
| hamis |mergeFiles | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5 | A Mappa1 mappa mappa a következő struktúrával jön létre:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;A Fájl1 + File2 tartalom egyetlen, automatikusan létrehozott fájlnévvel rendelkező fájlba egyesül. fájl automatikusan létrehozott neve1<br/><br/>A File3, File4 és File5 mappát nem veszi fel a rendszer. |

## <a name="preserve-metadata-during-copy"></a>Metaadatok megőrzése másolás közben

Amikor fájlokat másol az Amazon S3/Azure Blob/Azure Data Lake Storage Gen2-ről az Azure Data Lake Storage Gen2/Azure Blob szolgáltatásba, megőrizheti a fájl metaadatait az adatokkal együtt. További információ: [Metaadatok megőrzése](copy-activity-preserve-metadata.md#preserve-metadata).

## <a name="mapping-data-flow-properties"></a>Adatfolyam tulajdonságainak leképezése

Amikor átalakítja az adatokat az adatfolyam ban, olvashatés írhat és írhat fájlokat az Azure Blob Storage-ból JSON, Avro, Tagolt szöveg vagy Parketta formátumban. További információ: [forrásátalakítás](data-flow-source.md) és [a fogadó átalakítása](data-flow-sink.md) a leképezési adatfolyam-szolgáltatásban.

### <a name="source-transformation"></a>Forrás átalakítása

A forrásátalakítás, olvashat egy tárolóból, mappából vagy egyedi fájlból az Azure Blob Storage-ban. A **Forrás beállítások** lapon kezelheti a fájlok olvasásának módját. 

![Forrásbeállítások](media/data-flow/sourceOptions1.png "Forrásbeállítások")

**Helyettesítő karakter elérési útja:** Helyettesítő karakterminta használatával az ADF arra utasítja az ADF-et, hogy minden egyező mappát és fájlt egyetlen forrásátalakítással haladjon át. Ez egy hatékony módja annak, hogy több fájlt dolgozzon fel egyetlen folyamaton belül. Adjon hozzá több helyettesítő karakteregyezési mintát a + jellel, amely akkor jelenik meg, amikor az egérmutatót a meglévő helyettesítő karakterminta fölé viszi.

A forrástárolóból válasszon egy olyan fájlsorozatot, amely megfelel a mintának. Csak tároló adható meg az adatkészletben. A helyettesítő karakter elérési útjának ezért tartalmaznia kell a mappa elérési útját a gyökérmappából.

Példák helyettesítő karakterre:

* ```*```Bármely karakterkészletet jelöl
* ```**```Rekurzív könyvtárbeágyazását jelöli
* ```?```Egy karakter lecserélése
* ```[]```A zárójelben lévő karakterek közül egynek felel meg

* ```/data/sales/**/*.csv```Az összes csv fájl bediákora a /data/sales
* ```/data/sales/20??/**/```Az összes akta a 20.
* ```/data/sales/*/*/*.csv```A csv-fájlok bekerülése két szintre a /data/sales alatt
* ```/data/sales/2004/*/12/[XY]1?.csv```Az összes csv fájl betöltője 2004-ben, x vagy Y előtaggal kezdve egy kétjegyű számmal

**Partíció gyökérelérési útja:** Ha a fájlforrásban ```key=value``` egy formátumú mappa (például year=2019), akkor a partíciómappafa legfelső szintjét hozzárendelheti egy oszlopnévhez az adatfolyam-adatfolyamban.

Először állítson be egy helyettesítő karaktert úgy, hogy tartalmazza az összes olyan elérési utat, amely a particionált mappa, valamint az olvasni kívánt levélfájlokat.

![Partícióforrásfájlbeállításai](media/data-flow/partfile2.png "Partíciófájl beállítása")

A Partíció gyökérelérési útjának beállításával határozhatja meg, hogy mi a mappastruktúra legfelső szintje. Amikor az adatok tartalmát egy adatelőnézeten keresztül tekinti meg, látni fogja, hogy az ADF hozzáadja az egyes mappaszintekben található feloldott partíciókat.

![Partíció gyökérelérési útja](media/data-flow/partfile1.png "Partíció gyökérelérési útjának előnézete")

**Fájlok listája:** Ez egy fájlkészlet. Hozzon létre egy szövegfájlt, amely tartalmazza a feldolgozandó relatív elérési útfájlok listáját. Mutasson erre a szövegfájlra.

**Oszlop a fájlnév tárolásához:** A forrásfájl nevét az adatok egy oszlopában tárolhatja. Itt adjon meg egy új oszlopnevet a fájlnév-karakterlánc tárolásához.

**A befejezés után:** Válassza ki, hogy az adatfolyam futtatása után nem végez semmit a forrásfájllal, törölje a forrásfájlt, vagy helyezze át a forrásfájlt. Az áthelyezés útvonalai relatívak.

Ha a forrásfájlokat egy másik helyre szeretné áthelyezni utófeldolgozás után, először válassza az "Áthelyezés" lehetőséget a fájlművelethez. Ezután állítsa be a "from" könyvtárat. Ha nem használ helyettesítő karaktereket az elérési úthoz, akkor a "kezdő" beállítás ugyanaz a mappa lesz, mint a forrásmappában.

Ha helyettesítő karakterrel rendelkező forráselérési úttal rendelkezik, a szintaxis az alábbi módon fog kinézni:

```/data/sales/20??/**/*.csv```

Megadhatja a "from"

```/data/sales```

És "a", mint

```/backup/priorSales```

Ebben az esetben a /data/sales alatt beszerzett összes fájl a /backup/priorSales könyvtárba kerül.

> [!NOTE]
> A fájlműveletek csak akkor futnak, ha az adatfolyamot egy folyamat-hibakeresésből (folyamathiba-hibakeresésből vagy végrehajtási futtatásból) indítja el, amely az adatfolyam-végrehajtás tevékenységet használja egy folyamatban. A fájlműveletek *nem* futnak adatfolyam-hibakeresési módban.

**Szűrés utolsó módosítás szerint:** Szűrheti a feldolgozandó fájlokat, ha megadja az utolsó módosítás dátumtartományát. Minden dátum-idő UTC-ben van. 

### <a name="sink-properties"></a>Fogadó tulajdonságai

A fogadó átalakítása, írhat egy tárolóba vagy mappába az Azure Blob Storage-ban. a **Beállítások** lapon kezelheti a fájlok írásmódját.

![elfogadó beállításai](media/data-flow/file-sink-settings.png "elfogadó beállításai")

**Törölje a mappát:** Azt határozza meg, hogy a célmappa törlődjön-e az adatok írása előtt.

**Fájlnév beállítás:** Azt határozza meg, hogy a célfájlok hogyan lesznek elnevezve a célmappában. A fájlnév beállításai a következők:
   * **Alapértelmezett**: Engedélyezze, hogy a Spark a PART alapértelmezései alapján nevezze el a fájlokat.
   * **Minta**: Adjon meg egy mintát, amely partíciónként számba veszi a kimeneti fájlokat. Például **a hitelek[n].csv** hitelek1.csv, hitelek2.csv és így tovább.
   * **Partíciónként**: Partíciónként egy fájlnevet adjon meg.
   * **Oszlopban lévő adatokként:** Állítsa a kimeneti fájlt egy oszlop értékére. Az elérési út az adatkészlet-tárolóhoz viszonyítva van, nem a célmappához. Ha van egy mappaelérési út az adatkészletben, a rendszer felülbírálja azt.
   * **Kimenet egyetlen fájlba**: A particionált kimeneti fájlokat egyetlen elnevezett fájlba egyesítheti. Az elérési út az adatkészlet mappához viszonyítva van. Ne feledje, hogy a te egyesítési művelet a csomópont mérete alapján sikertelen lehet. Ez a beállítás nagy adatkészletek esetén nem ajánlott.

**Árajánlat az összes:** Azt határozza meg, hogy az összes értéket belemellékelje-e az idézőjelek közé.

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
| type | Az adatkészlet típustulajdonságát **AzureBlob (AzureBlob)** tulajdonságra kell állítani. |Igen |
| folderPath | A blob storage-ban lévő tároló és mappa elérési útja. <br/><br/>Helyettesítő karakter támogatja az elérési út, kivéve a tároló nevét. Az engedélyezett helyettesítő `*` karakterek a következők: `?` (nulla vagy több karakternek felel meg) és (nulla vagy egy karakternek felel meg); akkor `^` menekülhet, ha a mappa tényleges neve helyettesítő karaktert használ, vagy ha ez az escape karakter van benne. <br/><br/>Példák: myblobcontainer/myblobfolder/, további példák a [mappa- és fájlszűrőpéldákban.](#folder-and-file-filter-examples) |Igen másolási/kereshető tevékenységesetén, Nem a GetMetadata tevékenységhez |
| fileName | **A** blob(ok) neve vagy helyettesítő karaktere a megadott "folderPath" alatt. Ha nem ad meg értéket ehhez a tulajdonsághoz, az adatkészlet a mappában lévő összes blobra mutat. <br/><br/>Szűrő esetén az engedélyezett `*` helyettesítő karakterek a következők: `?` (nulla vagy több karakternek felel meg) és (nulla vagy egy karakternek felel meg).<br/>- 1. példa:`"fileName": "*.csv"`<br/>- 2. példa:`"fileName": "???20180427.txt"`<br/>Menekülésre használható, `^` ha a tényleges fájlnévben helyettesítő karakter szerepel, vagy ez az escape karakter van benne.<br/><br/>Ha a fájlnév nincs megadva egy kimeneti adatkészlethez, és a **preserveHierarchy** nincs megadva a tevékenységfogadóban, a másolási tevékenység automatikusan létrehozza a blob nevét a következő mintával: "*Data.[ tevékenység futtatási azonosító azonosítója GUID]. [GUID, ha összeolvasztási hierarchia]. [formátum, ha konfigurálva van]. [tömörítés, ha be van állítva]*", például "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz"; ha táblázatos forrásból a táblanevet használja a lekérdezés helyett, a névminta a "*[táblanév].[ formátum]. [tömörítés, ha be van állítva]*", pl. "MyTable.csv". |Nem |
| modifiedDatetimeStart | A fájlok szűrője az attribútum alapján: Utolsó módosítás. A fájlok akkor lesznek kiválasztva, ha az `modifiedDatetimeStart` `modifiedDatetimeEnd`utolsó módosítási idejük a és a közötti időtartományon belül van. Az idő az UTC időzónára a "2018-12-01T05:00:00Z" formátumban kerül alkalmazásra. <br/><br/> Ne feledje, hogy az adatok mozgásának általános teljesítményét befolyásolja, ha engedélyezi ezt a beállítást, ha nagy mennyiségű fájlból szeretne fájlszűrőt végezni. <br/><br/> A tulajdonságok null értékűek lehetnek, ami azt jelenti, hogy az adatkészletre nem lesz fájlattribútum-szűrő alkalmazva.  Ha `modifiedDatetimeStart` datetime értékkel rendelkezik, de `modifiedDatetimeEnd` NULL, az azt jelenti, hogy azokat a fájlokat, amelyek utolsó módosított attribútuma nagyobb vagy egyenlő, mint a datetime érték, ki lesznek jelölve.  Ha `modifiedDatetimeEnd` datetime értékkel rendelkezik, de `modifiedDatetimeStart` NULL, az azt jelenti, hogy azutolsó módosított attribútum kisebb, mint a datetime érték, ki lesznek jelölve.| Nem |
| modifiedDatetimeEnd | A fájlok szűrője az attribútum alapján: Utolsó módosítás. A fájlok akkor lesznek kiválasztva, ha az `modifiedDatetimeStart` `modifiedDatetimeEnd`utolsó módosítási idejük a és a közötti időtartományon belül van. Az idő az UTC időzónára a "2018-12-01T05:00:00Z" formátumban kerül alkalmazásra. <br/><br/> Ne feledje, hogy az adatok mozgásának általános teljesítményét befolyásolja, ha engedélyezi ezt a beállítást, ha nagy mennyiségű fájlból szeretne fájlszűrőt végezni. <br/><br/> A tulajdonságok null értékűek lehetnek, ami azt jelenti, hogy az adatkészletre nem lesz fájlattribútum-szűrő alkalmazva.  Ha `modifiedDatetimeStart` datetime értékkel rendelkezik, de `modifiedDatetimeEnd` NULL, az azt jelenti, hogy azokat a fájlokat, amelyek utolsó módosított attribútuma nagyobb vagy egyenlő, mint a datetime érték, ki lesznek jelölve.  Ha `modifiedDatetimeEnd` datetime értékkel rendelkezik, de `modifiedDatetimeStart` NULL, az azt jelenti, hogy azutolsó módosított attribútum kisebb, mint a datetime érték, ki lesznek jelölve.| Nem |
| Formátum | Ha a fájlokat úgy szeretné másolni, ahogy a fájlalapú tárolók között (bináris másolat), hagyja ki a formátum szakaszt a bemeneti és a kimeneti adatkészlet-definíciókban is.<br/><br/>Ha meghatározott formátumú fájlokat szeretne elemezni vagy létrehozni, a következő fájlformátum-típusok támogatottak: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**és **ParquetFormat**. Állítsa a **formátum** alatti **típustulajdonságot** ezen értékek egyikére. További információ: Text format , JSON format , Avro format , Orc format és [Parquet format](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) [(Szöveg formátum,](supported-file-formats-and-compression-codecs-legacy.md#text-format) [JSON formátum](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro formátum](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Orc format](supported-file-formats-and-compression-codecs-legacy.md#orc-format)) című szakasz. |Nem (csak bináris másolási forgatókönyv esetén) |
| tömörítés | Adja meg az adatok tömörítésének típusát és szintjét. További információt a [Támogatott fájlformátumok és tömörítési kodekek című témakörben talál.](supported-file-formats-and-compression-codecs-legacy.md#compression-support)<br/>A támogatott típusok: **GZip**, **Deflate,** **BZip2**és **ZipDeflate**.<br/>A támogatott szintek **optimálisak** és **leggyorsabbak.** |Nem |

>[!TIP]
>Ha egy mappa alá szeretné másolni az összes blobot, csak **folderPath értéket** adjon meg.<br>Egyetlen blob másolásához egy adott névvel, adja meg **folderPath** mapparész és **fileName** fájlnév.<br>Ha a blobok egy részhalmazát egy mappába szeretné másolni, adja meg a **folderPath** mapparészés **a fileName** helyettesítő karakterrel című elemét. 

**Példa:**

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

### <a name="legacy-copy-activity-source-model"></a>Örökölt másolási tevékenység forrásmodellje

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenységforrás típustulajdonságát **BlobSource (BlobSource)** típusú tulajdonságra kell állítani. |Igen |
| Rekurzív | Azt jelzi, hogy az adatok olvasása rekurzív anameddig az almappákból vagy csak a megadott mappából történik. Ne feledje, hogy ha a rekurzív érték igaz, és a fogadó fájlalapú tároló, a program nem másolja vagy hozza létre az üres mappát vagy almappát a fogadóban.<br/>Az engedélyezett értékek **igazak** (alapértelmezettek) és **hamisak.** | Nem |
| maxConcurrentConnections | A tárolóhoz egyidejűleg csatlakozó kapcsolatok száma. Csak akkor adja meg, ha korlátozni szeretné az egyidejű kapcsolatot az adattárhoz. | Nem |

**Példa:**

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

### <a name="legacy-copy-activity-sink-model"></a>Örökölt másolási tevékenység fogadójának modellje

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadójának típustulajdonságát **BlobSink (BlobSink)** beállításra kell állítani. |Igen |
| copyBehavior (Másként) | Azt a másolási viselkedést határozza meg, amikor a forrás fájlalapú adattárból származó fájlok.<br/><br/>Az engedélyezett értékek a következők:<br/><b>- MegőrzéseHierarchy (alapértelmezett)</b>: Megőrzi a fájlhierarchiát a célmappában. A forrásfájl forrásmappához viszonyított elérési útja megegyezik a célfájl célmappához való relatív elérési útjával.<br/><b>- Ahierarcha összeolvasztása</b>: A forrásmappából származó összes fájl a célmappa első szintjén található. A célfájlok nak automatikusan generált neve van. <br/><b>- MergeFiles</b>: Egyesíti az összes fájlt a forrás mappát egy fájlt. Ha a fájl vagy a blob neve meg van adva, az egyesített fájlnév a megadott név. Ellenkező esetben ez egy automatikusan létrehozott fájlnév. | Nem |
| maxConcurrentConnections | A tárolóhoz egyidejűleg csatlakozó kapcsolatok száma. Csak akkor adja meg, ha korlátozni szeretné az egyidejű kapcsolatot az adattárhoz. | Nem |

**Példa:**

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

## <a name="next-steps"></a>További lépések

A Data Factory másolási tevékenysége által forrásként és fogadóként támogatott adattárak listáját a Támogatott adattárak című témakörben [tetszését.](copy-activity-overview.md#supported-data-stores-and-formats)

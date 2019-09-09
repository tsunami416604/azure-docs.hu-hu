---
title: Adatok másolása, vagy az Azure Blob storage-ból a Data Factory használatával |} A Microsoft Docs
description: Ismerje meg, hogyan másolhat adatokat az Azure Blob storage-támogatott forrás adattárakból származó, vagy a támogatott fogadó adattárakba, Blob storage-ból a Data Factory használatával.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: jingwang
ms.openlocfilehash: 8925ea9da06ff718f08b7be73944c75d388bc01f
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/09/2019
ms.locfileid: "70814155"
---
# <a name="copy-data-to-or-from-azure-blob-storage-by-using-azure-data-factory"></a>Adatok másolása, vagy az Azure Blob storage-ból az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-azure-blob-connector.md)
> * [Aktuális verzió](connector-azure-blob-storage.md)

Ez a cikk az adatok Azure Blob Storage-ba és onnan történő másolását ismerteti. Azure Data Factory szolgáltatásról, olvassa el a [bevezető cikk](introduction.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az Azure Blob-összekötő a következő tevékenységek esetében támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Adatfolyam hozzárendelése](concepts-data-flow-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)

A Blob storage-összekötővel, a következőket támogatja:

- Blobok másolása, és az általános célú Azure storage-fiókok és a gyakori és ritka elérésű blob storage-bA. 
- Blobok másolása az Azure-erőforrások hitelesítések fiókkulcs, a szolgáltatás közös hozzáférésű jogosultságkódot, a fő- vagy felügyelt szolgáltatásidentitások használatával.
- Hozzáfűző BLOB másolása letiltása, vagy a lapblobok és az adatok másolása csak blokkblobok.
- A blobok másolása, vagy elemzés vagy generálása blobok [támogatott fájlformátumok és tömörítési kodek](supported-file-formats-and-compression-codecs.md).

>[!NOTE]
>Ha engedélyezi a _"megbízható Microsoft-szolgáltatások hozzáférésének engedélyezése ehhez a Storage-fiókhoz"_ lehetőséget az Azure Storage-tűzfal beállításainál, Azure Integration Runtime használatával a blob Storage-hoz való kapcsolódáshoz nem sikerül a művelet, mert az ADF nem megbízhatóként van kezelve Microsoft-szolgáltatás. Ehelyett saját üzemeltetésű Integration Runtime használatával csatlakozhat.

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok segítségével határozhatók meg a Data Factory-entitások adott Blob Storage-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Az Azure Blob-összekötő a következő hitelesítési típusok támogatása, tekintse meg a megfelelő szakaszban talál:

- [Fiók kulcsos hitelesítés](#account-key-authentication)
- [Közös hozzáférésű jogosultságkódos hitelesítés](#shared-access-signature-authentication)
- [Egyszerű szolgáltatás hitelesítése](#service-principal-authentication)
- [Felügyelt identitások Azure-erőforrások hitelesítéshez](#managed-identity)

>[!NOTE]
>Ha az adatok SQL Data Warehouseba való betöltéséhez a forrás-vagy előkészítési blob Storage-t Virtual Network-végponttal konfigurálja, akkor a rendszer a következőt használja a felügyelt identitások hitelesítéséhez Integration Runtime: 3,18 vagy újabb. Tekintse meg a [felügyelt identitás hitelesítése](#managed-identity) szakaszt további konfigurálási előfeltételekkel.

>[!NOTE]
>A Hdinsight és a Azure Machine Learning tevékenységek csak az Azure Blob Storage-fiók kulcsának hitelesítését támogatják.

### <a name="account-key-authentication"></a>Fiók kulcsos hitelesítés

A storage-fiók kulcsos hitelesítést használ, a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot állítsa **Azure BLOB Storage szolgáltatásról** (javasolt) vagy **AzureStorage** (a megjegyzéseket lásd alább). |Igen |
| connectionString | Adja meg a connectionString tulajdonság tárolási való csatlakozáshoz szükséges információk. <br/>A mező megjelölése SecureString, hogy biztonságosan tárolja Data Factoryban. A Azure Key Vault is elhelyezheti a fiók kulcsát, és `accountKey` lekérheti a konfigurációt a kapcsolatok sztringből. További részletekért tekintse meg a következő mintákat, és [tárolja a hitelesítő adatokat Azure Key Vault](store-credentials-in-key-vault.md) cikkben. |Igen |
| connectVia | A [integrációs modul](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. Használhatja az Azure integrációs modul vagy a helyi integrációs modul (ha az adattár egy magánhálózaton található). Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

>[!NOTE]
>A másodlagos blob Service-végpont nem támogatott a fiók kulcsos hitelesítésének használatakor. Más hitelesítési típusokat is használhat.

>[!NOTE]
>Ha "AzureStorage" típusú társított szolgáltatás használja, továbbra is támogatott-van, amíg használja az új "Azure BLOB Storage szolgáltatásról" társított szolgáltatás típusa, a jövőben használata javasolt.

**Példa:**

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

### <a name="shared-access-signature-authentication"></a>Közös hozzáférésű jogosultságkódos hitelesítés

Közös hozzáférésű jogosultságkód a tárfiókban található erőforrások delegált hozzáférést biztosít. Közös hozzáférésű jogosultságkód használatával az ügyfeleknek a tárfiókban lévő objektumokra vonatkozó egy megadott ideig. Nem kell megosztani a fiók hozzáférési kulcsait. A közös hozzáférésű jogosultságkód URI, amely a lekérdezési paraméterek magában foglalja a tárolási erőforrásokhoz való hitelesített hozzáférés szükséges összes információt. A közös hozzáférésű jogosultságkód-adattároló erőforrások elérésére, az ügyfél csak kell a megfelelő konstruktor vagy a metódus a közös hozzáférésű jogosultságkód adja át. A közös hozzáférésű aláírásokkal kapcsolatos további információkért [lásd: közös hozzáférésű aláírások: A közös hozzáférésű aláírás modelljének](../storage/common/storage-dotnet-shared-access-signature-part-1.md)megismerése.

> [!NOTE]
>- A Data Factory mostantól támogatja mindkét **szolgáltatás közös hozzáférésű jogosultságkódot** és **fiók közös hozzáférésű jogosultságkód**. A közös hozzáférésű aláírásokkal kapcsolatos további információkért lásd: [korlátozott hozzáférés engedélyezése az Azure Storage-erőforrásokhoz közös hozzáférésű aláírások (SAS) használatával](../storage/common/storage-sas-overview.md).
>- A későbbi adatkészlet konfigurációja a mappa elérési útja az abszolút elérési út tároló szint kezdve. Kell egy vonalba az elérési utat a SAS URI-t a konfigurálása.

> [!TIP]
> A tárfiók szolgáltatás közös hozzáférési aláírásokat létrehozni, hajtsa végre a következő PowerShell-parancsokat. Cserélje le a zárójelben, és a szükséges engedélyeket.
> `$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Közös hozzáférésű jogosultságkódos hitelesítés használatához a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot állítsa **Azure BLOB Storage szolgáltatásról** (javasolt) vagy **AzureStorage** (a megjegyzéseket lásd alább). |Igen |
| sasUri | A Storage-erőforrásokhoz, például a blobhoz vagy a tárolóhoz tartozó megosztott hozzáférési aláírás URI azonosítójának megadása. <br/>A mező megjelölése SecureString, hogy biztonságosan tárolja Data Factoryban. A Azure Key Vault SAS-tokent is helyezhet el az automatikus elforgatás kihasználása és a jogkivonat-rész eltávolításához. További részletekért tekintse meg a következő mintákat, és [tárolja a hitelesítő adatokat Azure Key Vault](store-credentials-in-key-vault.md) cikkben. |Igen |
| connectVia | A [integrációs modul](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. Használhatja az Azure integrációs modul vagy a helyi integrációs modul (ha az adattár egy magánhálózaton található). Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

>[!NOTE]
>Ha "AzureStorage" típusú társított szolgáltatás használja, továbbra is támogatott-van, amíg használja az új "Azure BLOB Storage szolgáltatásról" társított szolgáltatás típusa, a jövőben használata javasolt.

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
- Állítsa be **lejárati idő** megfelelően. Győződjön meg arról, hogy a tároló objektumok elérése le nem jár, a folyamat aktív időszakon belül.
- Az URI-t a megfelelő tárolóban vagy blobban kell létrehozni a szükséglet alapján. A blob közös hozzáférésű jogosultságkód URI lehetővé teszi, hogy a Data Factory, hogy a blob eléréséhez. A blob Storage-tárolók közös hozzáférésű aláírási URI-ja lehetővé teszi, hogy Data Factory megismételje a tárolóban lévő blobokat. Később több vagy kevesebb objektumok eléréséhez, vagy a közös hozzáférésű jogosultságkód URI Azonosítójának frissítése, ne felejtse el frissíteni a társított szolgáltatás az új URI-t.

### <a name="service-principal-authentication"></a>Egyszerű szolgáltatásnév hitelesítése

Azure Storage egyszerű szolgáltatásnév hitelesítése általánosságban, tekintse meg [hitelesíti a hozzáférést az Azure Storage, Azure Active Directory használatával](../storage/common/storage-auth-aad.md).

Egyszerű szolgáltatásnév hitelesítése használatához kövesse az alábbi lépéseket:

1. Regisztráljon az Azure Active Directoryban (Azure AD) application entitás a következő [regisztrálja az alkalmazást az Azure AD-bérlő](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Jegyezze fel a következő értékeket, mert a társított szolgáltatás definiálásához használja:

    - Alkalmazásazonosító
    - Alkalmazáskulcs
    - Bérlőazonosító

2. Adja meg a szolgáltatás egyszerű megfelelő engedéllyel az Azure Blob storage-ban. Tekintse meg [kezelés hozzáférési jogosultsága ahhoz, hogy az RBAC Azure Storage-adatokkal](../storage/common/storage-auth-aad-rbac.md) a szerepköröket a részletekkel kapcsolatban.

    - **Forrásként**, az Access (IAM) vezérlőelemet, adja meg legalább **Storage-Blobadatok olvasója** szerepkör.
    - **Fogadóként**, az Access (IAM) vezérlőelemet, adja meg legalább **Storage-Blobadatok Közreműködője** szerepkör.

Ezek a Tulajdonságok támogatottak az Azure Blob storage társított szolgáltatása:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot állítsa **Azure BLOB Storage szolgáltatásról**. |Igen |
| serviceEndpoint | Adja meg az Azure Blob storage-szolgáltatásvégpont minta `https://<accountName>.blob.core.windows.net/`. |Igen |
| servicePrincipalId | Adja meg az alkalmazás ügyfél-azonosítót. | Igen |
| servicePrincipalKey | Adja meg az alkalmazáskulcsot. Jelölje meg a mező egy **SecureString** tárolja biztonságos helyen a Data Factory áttekintése, vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Igen |
| tenant | Adja meg a bérlő információkat (tartomány neve vagy a bérlő azonosítója) alatt az alkalmazás található. Az Azure portal jobb felső sarkában az egér viszi, lekéréséhez. | Igen |
| connectVia | A [integrációs modul](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. Használhatja az Azure integrációs modul vagy a helyi integrációs modul (ha az adattár egy magánhálózaton található). Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

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

### <a name="managed-identity"></a> Felügyelt identitások Azure-erőforrások hitelesítéshez

Adat-előállító társítható egy [-identitás az Azure-erőforrások](data-factory-service-identity.md), amely az adott adat-előállító jelöli. Ezt a felügyelt identitást közvetlenül használhatja a blob Storage-hitelesítéshez, hasonlóan a saját egyszerű szolgáltatásnév használatához. Lehetővé teszi a kijelölt factory eléréséhez és másolásához adatokhoz, és a Blob storage-bA.

Tekintse át az Azure [Storage-hoz való hozzáférés hitelesítése](../storage/common/storage-auth-aad.md) az Azure Storage-hitelesítéshez Azure Active Directory az általános lehetőséget. Felügyelt identitások Azure-erőforrások hitelesítés használatához kövesse az alábbi lépéseket:

1. Az [adat-előállító által felügyelt személyazonossági adatok beolvasása](data-factory-service-identity.md#retrieve-managed-identity) a gyári szolgáltatással együtt GENERÁLT "Service Identity Application id" értékének másolásával.

2. Adja meg az Azure Blob storage-felügyelt identitásnak megfelelő jogosultságot. Tekintse meg [kezelés hozzáférési jogosultsága ahhoz, hogy az RBAC Azure Storage-adatokkal](../storage/common/storage-auth-aad-rbac.md) a szerepköröket a részletekkel kapcsolatban.

    - **Forrásként**, az Access (IAM) vezérlőelemet, adja meg legalább **Storage-Blobadatok olvasója** szerepkör.
    - **Fogadóként**, az Access (IAM) vezérlőelemet, adja meg legalább **Storage-Blobadatok Közreműködője** szerepkör.

>[!IMPORTANT]
>Ha a blobból (forrásként vagy átmenetiként) lévő adatok betöltését végzi a SQL Data Warehouseba, ha felügyelt identitásos hitelesítést használ a Blobhoz, akkor az [útmutató](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) 1. és 2. lépését is kövesse az Azure Active SQL Database-kiszolgáló regisztrálásához. A könyvtár (Azure AD) és a 2) rendelje hozzá a Storage blob adatközreműködői szerepkört a SQL Database-kiszolgálóhoz; a REST-et a Data Factory kezeli. Ha a blob Storage egy Azure Virtual Network-végponttal van konfigurálva, akkor az adatoknak a használatával történő betöltéséhez használja a felügyelt identitások hitelesítését.

Ezek a Tulajdonságok támogatottak az Azure Blob storage társított szolgáltatása:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot állítsa **Azure BLOB Storage szolgáltatásról**. |Igen |
| serviceEndpoint | Adja meg az Azure Blob storage-szolgáltatásvégpont minta `https://<accountName>.blob.core.windows.net/`. |Igen |
| connectVia | A [integrációs modul](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. Használhatja az Azure integrációs modul vagy a helyi integrációs modul (ha az adattár egy magánhálózaton található). Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

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

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. 

- A **Parquet, a tagolt szöveget, a JSON-t, a Avro és a bináris formátumot**a [parketta, a tagolt szöveg, a JSON, a Avro és a bináris formátum adatkészlet](#format-based-dataset) szakaszban találja.
- Más formátumok, például az **ork/JSON formátum**esetében tekintse meg a [más formátumú adatkészlet](#other-format-dataset) szakaszt.

### <a name="format-based-dataset"></a>Parketta, tagolt szöveg, JSON, Avro és bináris formátumú adatkészlet

Adatok másolása a blob Storage-ba és a-ból a parketta, a tagolt szöveg, a Avro vagy a bináris formátum alapján, a következő témakörben található: [parketta formátum](format-parquet.md), [tagolt szöveg formátum](format-delimited-text.md), [Avro formátum](format-avro.md) és [bináris formátum](format-binary.md) cikk a Format-alapú adatkészletek és a támogatott beállítások alapján Az Azure Blob a következő tulajdonságokat támogatja a Format `location` -alapú adatkészlet beállításai alatt:

| Tulajdonság   | Leírás                                                  | Szükséges |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Az adatkészlet helyének Type tulajdonságát **AzureBlobStorageLocation**értékre kell állítani. | Igen      |
| container  | A blob-tároló.                                          | Igen      |
| folderPath | Az adott tárolóban lévő mappa elérési útja. Ha a mappa szűréséhez helyettesítő karaktert szeretne használni, hagyja ki ezt a beállítást, és a tevékenység forrásának beállításai között válassza a lehetőséget. | Nem       |
| fileName   | A fájlnév a megadott tároló + folderPath alatt. Ha helyettesítő karaktereket szeretne használni a fájlok szűréséhez, hagyja ki ezt a beállítást, és a tevékenység forrásának beállításai között válassza a lehetőséget. | Nem       |

> [!NOTE]
>
> A következő szakaszban említett, a Parquet/Text formátumot tartalmazó **AzureBlob** -típus adatkészlete továbbra is támogatott a visszamenőleges kompatibilitás érdekében a másolási/keresési/GetMetaData tevékenységek esetében, de nem működik a leképezési adatfolyammal. Azt javasoljuk, hogy ezt az új modellt fogja használni, és az ADF szerzői felhasználói felülete átváltott az új típusok létrehozásához.

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

### <a name="other-format-dataset"></a>Egyéb formátumú adatkészlet

Ha az adatokat a blob Storage-ba vagy az ork/JSON formátumba kívánja másolni, állítsa az adatkészlet Type (típus) tulajdonságát **AzureBlob**értékre. A következő tulajdonságok támogatottak.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adatkészlet értékre kell állítani **AzureBlob**. |Igen |
| folderPath | A tároló és a blob Storage-mappa elérési útja. <br/><br/>A helyettesítő karakteres szűrő a tároló neve nélkül használható. Az engedélyezett helyettesítő karakterek a `*` következők: (nulla vagy több karakternek `?` felel meg) és (a nulla vagy `^` egy karakter egyezése) <br/><br/>Példák: myblobcontainer/myblobfolder/, további példák a [mappák és a fájlok szűrése példákban](#folder-and-file-filter-examples). |Igen, a Másolás/keresési tevékenység, GetMetadata tevékenység esetében: nem |
| fileName | **Név vagy helyettesítő karaktert tartalmazó szűrő** számára megadott "folderPath" alatt a BLOB(ok). Ez a tulajdonság értékét nem adja meg, ha az adatkészlet a mappában lévő összes BLOB mutat. <br/><br/>Szűrő esetén engedélyezett a helyettesítő karaktereket: `*` (nulla vagy több olyan karakterre illeszkedik) és `?` (megegyezik a nulla vagy önálló karakter).<br/>-1. példa: `"fileName": "*.csv"`<br/>– 2. példa: `"fileName": "???20180427.txt"`<br/>Használat `^` elkerülésére, ha a fájl tényleges nevét helyettesítő elemet vagy a escape karaktere belül.<br/><br/>Ha nincs megadva a fájlnév egy kimeneti adatkészlethez, és a **preserveHierarchy** nincs megadva a tevékenység fogadójában, a másolási tevékenység automatikusan létrehozza a blob nevét a következő mintával: "*Az adatgyűjtés. [tevékenység futtatásának azonosítója GUID]. [GUID if FlattenHierarchy]. [formátum, ha konfigurálva]. [tömörítés, ha konfigurálva]* ", például: "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz"; tábla neve helyett lekérdezési táblázatos forrásból másol, ha van-e a névminta " *[táblanév]. [ formátum]. [Ha konfigurálta a tömörítés]* ", például: "MyTable.csv". |Nem |
| modifiedDatetimeStart | A fájlok szűrése az attribútum alapján: Utolsó módosítás. A fájlok lesz kiválasztva, ha az utolsó módosítás időpontja közötti időtartományban `modifiedDatetimeStart` és `modifiedDatetimeEnd`. Az idő UTC időzóna szerint formátumban alkalmazott "2018-12-01T05:00:00Z". <br/><br/> Ügyeljen arra, hogy az adatáthelyezés általános teljesítményét a beállítás engedélyezésével befolyásolja, ha nagy mennyiségű fájlból szeretne szűrni a fájlmegosztást. <br/><br/> A tulajdonságok értéke NULL lehet, ami azt jelenti, hogy a rendszer nem alkalmazza a file Attribute szűrőt az adatkészletre.  Amikor `modifiedDatetimeStart` dátum és idő értékkel rendelkezik, de `modifiedDatetimeEnd` má hodnotu NULL, azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútum értéke nagyobb, mint vagy egyenlő a dátum és idő értékkel lesz kiválasztva.  Amikor `modifiedDatetimeEnd` dátum és idő értékkel rendelkezik, de `modifiedDatetimeStart` má hodnotu NULL, azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútum értéke kisebb, mint a dátum/idő értéket fog jelölni.| Nem |
| modifiedDatetimeEnd | A fájlok szűrése az attribútum alapján: Utolsó módosítás. A fájlok lesz kiválasztva, ha az utolsó módosítás időpontja közötti időtartományban `modifiedDatetimeStart` és `modifiedDatetimeEnd`. Az idő UTC időzóna szerint formátumban alkalmazott "2018-12-01T05:00:00Z". <br/><br/> Ügyeljen arra, hogy az adatáthelyezés általános teljesítményét a beállítás engedélyezésével befolyásolja, ha nagy mennyiségű fájlból szeretne szűrni a fájlmegosztást. <br/><br/> A tulajdonságok értéke NULL lehet, ami azt jelenti, hogy a rendszer nem alkalmazza a file Attribute szűrőt az adatkészletre.  Amikor `modifiedDatetimeStart` dátum és idő értékkel rendelkezik, de `modifiedDatetimeEnd` má hodnotu NULL, azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútum értéke nagyobb, mint vagy egyenlő a dátum és idő értékkel lesz kiválasztva.  Amikor `modifiedDatetimeEnd` dátum és idő értékkel rendelkezik, de `modifiedDatetimeStart` má hodnotu NULL, azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútum értéke kisebb, mint a dátum/idő értéket fog jelölni.| Nem |
| format | Ha szeretné másolni a fájlokat, a fájlalapú tárolók (bináris másolat) közötti, hagyja ki a format szakaszban, mind a bemeneti és kimeneti adatkészlet-definíciókban.<br/><br/>Ha a fájlokat egy adott formátummal szeretné elemezni vagy előállítani, a következő fájlformátum-típusok támogatottak: **Szövegformátum**, **JsonFormat**, **AvroFormat**, **OrcFormat**és **ParquetFormat**. Állítsa be a **típus** tulajdonság alatt **formátum** az alábbi értékek egyikére. További információkért lásd: a [szövegformátum](supported-file-formats-and-compression-codecs.md#text-format), [JSON formátumban](supported-file-formats-and-compression-codecs.md#json-format), [Avro formátum](supported-file-formats-and-compression-codecs.md#avro-format), [Orc formátum](supported-file-formats-and-compression-codecs.md#orc-format), és [Parquet formátum ](supported-file-formats-and-compression-codecs.md#parquet-format) szakaszokat. |Nem (csak a bináris másolás esetén) |
| compression | Adja meg a típus és az adatok tömörítési szintje. További információkért lásd: [támogatott fájlformátumok és tömörítési kodek](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Támogatott típusok a következők **GZip**, **Deflate**, **BZip2**, és **ZipDeflate**.<br/>Támogatott szintek a következők **Optimal** és **leggyorsabb**. |Nem |

>[!TIP]
>Egy mappa alatti összes blobot másol, adja meg a **folderPath** csak.<br>Adja meg a megadott nevű egy blob átmásolása **folderPath** mappára vonatkozó részt a és **fileName** nevére.<br>Adjon meg egy részhalmazát egy mappát a blobok másolása, **folderPath** mappára vonatkozó részt a és **fileName** helyettesítő szűrővel. 

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

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a Blob storage-forrás és fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="blob-storage-as-a-source-type"></a>BLOB storage-ot egy adatforrás típusa

- A parketta, a **tagolt szöveg, a JSON, a Avro és a bináris formátum**másolásához tekintse meg a [parketta, a tagolt szöveg, a JSON, a Avro és a bináris formátum forrás](#format-based-source) szakaszát.
- Más formátumokból, például az **ork formátumból**való másoláshoz tekintse meg a [más formátumú forrás](#other-format-source) szakaszt.

#### <a name="format-based-source"></a>Parketta, tagolt szöveg, JSON, Avro és bináris formátum forrása

Adatok másolása a blob Storage-ba és a vágólapról a **parketta, a tagolt szöveg, a JSON, a Avro és a bináris formátum**között, a [parketta formátuma](format-parquet.md), a [tagolt szöveg formátuma](format-delimited-text.md), a [Avro formátum](format-avro.md) és a [bináris formátum](format-binary.md) cikke a Format-alapú adatkészletek esetében támogatott beállítások. Az Azure Blob a következő tulajdonságokat támogatja a Format `storeSettings` -alapú másolási forrás beállításai alatt:

| Tulajdonság                 | Leírás                                                  | Szükséges                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | A Type tulajdonságot `storeSettings` a **AzureBlobStorageReadSetting**értékre kell állítani. | Igen                                           |
| recursive                | Azt jelzi, hogy az adatok olvasható rekurzív módon az almappák vagy csak a megadott mappába. Vegye figyelembe, hogy ha a rekurzív értéke igaz, és a fogadó a fájlalapú tároló, egy üres mappát vagy almappát nem másolja vagy létrehozott, a fogadó. Engedélyezett értékek a következők **igaz** (alapértelmezett), és **hamis**. | Nem                                            |
| wildcardFolderPath       | A mappa elérési útja a forrás mappák szűréséhez az adatkészletben konfigurált megadott tárolóban helyettesítő karakterekkel. <br>Az engedélyezett helyettesítő karakterek a `*` következők: (nulla vagy több karakternek `?` felel meg) és (a nulla vagy `^` egy karakter egyezése) <br>További példákat a [mappák és a fájlok szűrésére szolgáló példákban](#folder-and-file-filter-examples)talál. | Nem                                            |
| wildcardFileName         | A forrásfájl szűréséhez a megadott tároló + folderPath/wildcardFolderPath helyettesítő karakterekkel rendelkező fájlnév. <br>Az engedélyezett helyettesítő karakterek a `*` következők: (nulla vagy több karakternek `?` felel meg) és (a nulla vagy `^` egy karakter egyezése)  További példákat a [mappák és a fájlok szűrésére szolgáló példákban](#folder-and-file-filter-examples)talál. | Igen, `fileName` ha nincs megadva az adatkészletben |
| modifiedDatetimeStart    | A fájlok szűrése az attribútum alapján: Utolsó módosítás. A fájlok lesz kiválasztva, ha az utolsó módosítás időpontja közötti időtartományban `modifiedDatetimeStart` és `modifiedDatetimeEnd`. Az idő UTC időzóna szerint formátumban alkalmazott "2018-12-01T05:00:00Z". <br> A Tulajdonságok lehet null értékű, ami jelenti azt, hogy nincs fájlszűrő attribútum alkalmazandó az adatkészletet.  Amikor `modifiedDatetimeStart` dátum és idő értékkel rendelkezik, de `modifiedDatetimeEnd` má hodnotu NULL, azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútum értéke nagyobb, mint vagy egyenlő a dátum és idő értékkel lesz kiválasztva.  Amikor `modifiedDatetimeEnd` dátum és idő értékkel rendelkezik, de `modifiedDatetimeStart` má hodnotu NULL, azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútum értéke kisebb, mint a dátum/idő értéket fog jelölni. | Nem                                            |
| modifiedDatetimeEnd      | Ugyanaz, mint a fenti.                                               | Nem                                            |
| maxConcurrentConnections | A tárolási tárolóhoz való kapcsolódáshoz szükséges kapcsolatok száma egyidejűleg. Csak akkor kell megadni, ha az egyidejű kapcsolódást szeretné korlátozni az adattárral. | Nem                                            |

> [!NOTE]
> A Parquet/tagolt szöveg formátuma esetén a következő szakaszban említett, **BlobSource** típusú másolási tevékenység továbbra is támogatott a visszafelé kompatibilitás érdekében. Azt javasoljuk, hogy ezt az új modellt fogja használni, és az ADF szerzői felhasználói felülete átváltott az új típusok létrehozásához.

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

#### <a name="other-format-source"></a>Egyéb formátum forrása

Az adatoknak a blob Storage-ból az **ork formátumba**való másolásához állítsa a forrás típusát a másolás tevékenység **BlobSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrása típusa tulajdonságát állítsa **BlobSource**. |Igen |
| recursive | Azt jelzi, hogy az adatok olvasható rekurzív módon az almappák vagy csak a megadott mappába. Vegye figyelembe, hogy ha a rekurzív értéke igaz, és a fogadó a fájlalapú tároló, egy üres mappát vagy almappát nem másolja vagy létrehozott, a fogadó.<br/>Engedélyezett értékek a következők **igaz** (alapértelmezett), és **hamis**. | Nem |
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

### <a name="blob-storage-as-a-sink-type"></a>BLOB storage-ot a fogadó típusa

- A parketta, a **tagolt szöveg, a JSON, a Avro és a bináris formátum**másolásához tekintse meg a [parketta, a tagolt szöveg, a JSON, a Avro és a bináris formátum forrás](#format-based-source) szakaszát.
- Más formátumokból, például az **ork formátumból**való másoláshoz tekintse meg a [más formátumú forrás](#other-format-source) szakaszt.

#### <a name="format-based-source"></a>Parketta, tagolt szöveg, JSON, Avro és bináris formátum forrása

Adatok másolása a blob Storage-ból a **parketta, a tagolt szöveg, a JSON, a Avro és a bináris formátum**között: a [parketta formátuma](format-parquet.md), a [tagolt szöveg formátuma](format-delimited-text.md), a [Avro formátum](format-avro.md) és a [bináris formátum](format-binary.md) cikk a Format-alapú másolási tevékenység forrásán és támogatott beállítások. Az Azure Blob `storeSettings` a következő tulajdonságokat támogatja a Format-alapú másolási fogadó beállításainál:

| Tulajdonság                 | Leírás                                                  | Szükséges |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | A Type tulajdonságot `storeSettings` a **AzureBlobStorageWriteSetting**értékre kell állítani. | Igen      |
| copyBehavior             | A másolási viselkedés határozza meg, ha a forrás fájlok fájlalapú adattárból.<br/><br/>Engedélyezett értékek a következők:<br/><b>-PreserveHierarchy (alapértelmezett)</b>: Megőrzi a fájl hierarchiáját a célmappában. A forrásmappa forrás-fájl elérési útja megegyezik a célmappában a célfájl elérési útja.<br/><b>-FlattenHierarchy</b>: A forrás mappából származó összes fájl a célmappa első szintjén található. A cél fájlok automatikusan létrehozott névvel rendelkeznek. <br/><b>-MergeFiles</b>: Egyesít minden fájlt a forrás mappájából egy fájlba. A fájl- vagy blob neve meg van adva, az egyesített fájlnév-e a megadott néven. Ellenkező esetben egy automatikusan létrehozott nevét. | Nem       |
| maxConcurrentConnections | A tárolási tárolóhoz való kapcsolódáshoz szükséges kapcsolatok száma egyidejűleg. Csak akkor kell megadni, ha az egyidejű kapcsolódást szeretné korlátozni az adattárral. | Nem       |

> [!NOTE]
> A Parquet/tagolt szöveges formátum esetében a következő szakaszban említett, **BlobSink** típusú másolási tevékenység fogadója továbbra is támogatott a visszamenőleges kompatibilitás érdekében. Azt javasoljuk, hogy ezt az új modellt fogja használni, és az ADF szerzői felhasználói felülete átváltott az új típusok létrehozásához.

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
                    "type": "AzureBlobStorageWriteSetting",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

#### <a name="other-format-sink"></a>Egyéb formátumú fogadó

Az Adatmásolás a blob Storage-ba **ork-formátumban**való másolásához a másolási tevékenységben állítsa be a fogadó típusát a **BlobSink**értékre. A következő tulajdonságok támogatottak a **fogadó** szakaszban.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadó típusa tulajdonságát állítsa **BlobSink**. |Igen |
| copyBehavior | A másolási viselkedés határozza meg, ha a forrás fájlok fájlalapú adattárból.<br/><br/>Engedélyezett értékek a következők:<br/><b>-PreserveHierarchy (alapértelmezett)</b>: Megőrzi a fájl hierarchiáját a célmappában. A forrásmappa forrás-fájl elérési útja megegyezik a célmappában a célfájl elérési útja.<br/><b>-FlattenHierarchy</b>: A forrás mappából származó összes fájl a célmappa első szintjén található. A cél fájlok automatikusan létrehozott névvel rendelkeznek. <br/><b>-MergeFiles</b>: Egyesít minden fájlt a forrás mappájából egy fájlba. A fájl- vagy blob neve meg van adva, az egyesített fájlnév-e a megadott néven. Ellenkező esetben egy automatikusan létrehozott nevét. | Nem |
| maxConcurrentConnections | A tárolási tárolóhoz való kapcsolódáshoz szükséges kapcsolatok száma egyidejűleg. Csak akkor kell megadni, ha az egyidejű kapcsolódást szeretné korlátozni az adattárral. | Nem |

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

### <a name="folder-and-file-filter-examples"></a>Példák a mappák és a fájlok szűrésére

Ez a szakasz a mappa elérési útjának és fájlnevének a helyettesítő karakteres szűrőkkel való viselkedését írja le.

| folderPath | fileName | recursive | A forrás mappa szerkezete és a szűrő eredménye (a **félkövérrel szedett** fájlok beolvasása)|
|:--- |:--- |:--- |:--- |
| `container/Folder*` | (üres, alapértelmezett használata) | false | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | (üres, alapértelmezett használata) | true | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | false | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | true | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fájl3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="some-recursive-and-copybehavior-examples"></a>A rekurzív és copyBehavior példákat

Ez a szakasz ismerteti az eredményül kapott viselkedéstől a másolási művelet rekurzív és copyBehavior értékek különböző kombinációihoz.

| recursive | copyBehavior | Forrás mappastruktúra | Eredményül kapott cél |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | a célmappában mappa1 szerkezete ugyanaz, mint a forrás hozzon létre:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A cél mappa1 jön létre az alábbi struktúra használatával: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet, a file1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File5 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A cél mappa1 jön létre az alábbi struktúra használatával: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + fájl3 + File4 + File5 tartalma egyesítve egy fájlt egy automatikusan létrehozott nevét. |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | a célmappában mappa1 az alábbi struktúra használatával jön létre: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Fájl3, File4 és File5 Subfolder1 nem mértékének. |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | a célmappában mappa1 az alábbi struktúra használatával jön létre: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet, a file1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File2<br/><br/>Fájl3, File4 és File5 Subfolder1 nem mértékének. |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Az alábbi struktúra használatával jön létre a célmappában mappa1<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 tartalma egyesítve egy fájlt egy automatikusan létrehozott nevét. automatikusan létrehozott nevet, a file1 kiszolgálón<br/><br/>Fájl3, File4 és File5 Subfolder1 nem mértékének. |

## <a name="mapping-data-flow-properties"></a>Adatfolyam-tulajdonságok leképezése

A forrás- [átalakítás](data-flow-source.md) és a fogadó [átalakítás](data-flow-sink.md) részleteinek megismerése a leképezési folyamatokban.

## <a name="next-steps"></a>További lépések

A másolási tevékenység, Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md##supported-data-stores-and-formats).

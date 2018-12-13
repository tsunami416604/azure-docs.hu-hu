---
title: Adatok másolása, vagy az Azure Blob storage-ból a Data Factory használatával |} A Microsoft Docs
description: Ismerje meg, hogyan másolhat adatokat az Azure Blob storage-támogatott forrás adattárakból származó, vagy a támogatott fogadó adattárakba, Blob storage-ból a Data Factory használatával.
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: jingwang
ms.openlocfilehash: a020b0a803fd9cfd00dd7da2fa6d28388bc268a3
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53095346"
---
# <a name="copy-data-to-or-from-azure-blob-storage-by-using-azure-data-factory"></a>Adatok másolása, vagy az Azure Blob storage-ból az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](v1/data-factory-azure-blob-connector.md)
> * [Aktuális verzió](connector-azure-blob-storage.md)

Ez a cikk ismerteti, hogyan használható a másolási tevékenység az Azure Data Factoryban az adatok másolása az Azure Blob storage szolgáltatásba vagy onnan. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

Azure Data Factory szolgáltatásról, olvassa el a [bevezető cikk](introduction.md).

## <a name="supported-capabilities"></a>Támogatott képességek

Bármely támogatott forrásadattárból adatokat másolhatja a Blob storage. Is másolhatja adatok Blob storage-ból bármelyik támogatott fogadó adattárba. A másolási tevékenység által források vagy fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md) tábla.

A Blob storage-összekötővel, a következőket támogatja:

- Blobok másolása, és az általános célú Azure storage-fiókok és a gyakori és ritka elérésű blob storage-bA. 
- Blobok másolása az Azure-erőforrások hitelesítések fiókkulcs, a szolgáltatás közös hozzáférésű jogosultságkódot, a fő- vagy felügyelt szolgáltatásidentitások használatával.
- Hozzáfűző BLOB másolása letiltása, vagy a lapblobok és az adatok másolása csak blokkblobok.
- A blobok másolása, vagy elemzés vagy generálása blobok [támogatott fájlformátumok és tömörítési kodek](supported-file-formats-and-compression-codecs.md).

>[!NOTE]
>Ha Ön engedélyezi _"Allow megbízható Microsoft-szolgáltatások a tárfiók"_ Azure integrációs modul használatával szeretne csatlakozni a Blob storage Azure Storage tűzfalbeállítások kapcsolót sikertelen lesz, és tiltott hibaüzenet ADF nincsenek megbízható Microsoft-szolgáltatás kezeli. Használjon helyi Integration Runtime,-n keresztül csatlakozik.

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
>Platformját, az Azure Machine Learning és az Azure SQL Data Warehouse PolyBase betöltési csak Azure Blob storage-fiók kulcsos hitelesítést támogatják.

### <a name="account-key-authentication"></a>Fiók kulcsos hitelesítés

A storage-fiók kulcsos hitelesítést használ, a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot állítsa **Azure BLOB Storage szolgáltatásról** (javasolt) vagy **AzureStorage** (a megjegyzéseket lásd alább). |Igen |
| kapcsolati Sztringje | Adja meg a connectionString tulajdonság tárolási való csatlakozáshoz szükséges információk. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). |Igen |
| connectVia | A [integrációs modul](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. Használhatja az Azure integrációs modul vagy a helyi integrációs modul (ha az adattár egy magánhálózaton található). Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

>[!NOTE]
>Ha "AzureStorage" típusú társított szolgáltatás használja, továbbra is támogatott-van, amíg használja az új "Azure BLOB Storage szolgáltatásról" társított szolgáltatás típusa, a jövőben használata javasolt.

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

### <a name="shared-access-signature-authentication"></a>Közös hozzáférésű jogosultságkódos hitelesítés

Közös hozzáférésű jogosultságkód a tárfiókban található erőforrások delegált hozzáférést biztosít. Közös hozzáférésű jogosultságkód használatával az ügyfeleknek a tárfiókban lévő objektumokra vonatkozó egy megadott ideig. Nem kell megosztani a fiók hozzáférési kulcsait. A közös hozzáférésű jogosultságkód URI, amely a lekérdezési paraméterek magában foglalja a tárolási erőforrásokhoz való hitelesített hozzáférés szükséges összes információt. A közös hozzáférésű jogosultságkód-adattároló erőforrások elérésére, az ügyfél csak kell a megfelelő konstruktor vagy a metódus a közös hozzáférésű jogosultságkód adja át. Közös hozzáférésű jogosultságkódokkal kapcsolatos további információkért lásd: [közös hozzáférésű jogosultságkódok: megismerheti a közös hozzáférésű jogosultságkód modell](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
>- A Data Factory mostantól támogatja mindkét **szolgáltatás közös hozzáférésű jogosultságkódot** és **fiók közös hozzáférésű jogosultságkód**. Két, és hogyan kell őket összeállítani kapcsolatos további információkért lásd: [közös hozzáférésű jogosultságkódok típusú](../storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures).
>- A későbbi adatkészlet konfigurációja a mappa elérési útja az abszolút elérési út tároló szint kezdve. Kell egy vonalba az elérési utat a SAS URI-t a konfigurálása.

> [!TIP]
> A tárfiók szolgáltatás közös hozzáférési aláírásokat létrehozni, hajtsa végre a következő PowerShell-parancsokat. Cserélje le a zárójelben, és a szükséges engedélyeket.
> `$context = New-AzureStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzureStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Közös hozzáférésű jogosultságkódos hitelesítés használatához a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot állítsa **Azure BLOB Storage szolgáltatásról** (javasolt) vagy **AzureStorage** (a megjegyzéseket lásd alább). |Igen |
| sasUri | Adja meg a közös hozzáférésű jogosultságkód URI-t, a tárolási erőforrások, például blob, tárolót vagy tábla. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). |Igen |
| connectVia | A [integrációs modul](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. Használhatja az Azure integrációs modul vagy a helyi integrációs modul (ha az adattár egy magánhálózaton található). Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

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
                "value": "<SAS URI of the Azure Storage resource>"
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
- Az URI-t az igényei alapján megfelelő tároló/blob vagy table szintjén kell létrehozni. A blob közös hozzáférésű jogosultságkód URI lehetővé teszi, hogy a Data Factory, hogy a blob eléréséhez. A Blob storage-tároló közös hozzáférésű jogosultságkód URI lehetővé teszi, hogy a Data Factory iterálódnak a tárolóban lévő blobokat. Később több vagy kevesebb objektumok eléréséhez, vagy a közös hozzáférésű jogosultságkód URI Azonosítójának frissítése, ne felejtse el frissíteni a társított szolgáltatás az új URI-t.

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
| bérlő | Adja meg a bérlő információkat (tartomány neve vagy a bérlő azonosítója) alatt az alkalmazás található. Az Azure portal jobb felső sarkában az egér viszi, lekéréséhez. | Igen |
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

Adat-előállító társítható egy [-identitás az Azure-erőforrások](data-factory-service-identity.md), amely az adott adat-előállító jelöli. Használhatja a felügyeltszolgáltatás-identitás közvetlenül a Blob storage hitelesítéshez hasonló saját egyszerű szolgáltatás használatával. Lehetővé teszi a kijelölt factory eléréséhez és másolásához adatokhoz, és a Blob storage-bA.

Az Azure Storage MSI-hitelesítéssel, tekintse meg [hitelesíti a hozzáférést az Azure Storage, Azure Active Directory használatával](../storage/common/storage-auth-aad.md).

Felügyelt identitások Azure-erőforrások hitelesítés használatához kövesse az alábbi lépéseket:

1. [Beolvasni a data factory-szolgáltatásidentitás](data-factory-service-identity.md#retrieve-service-identity) másolásával "SZOLGÁLTATÁSIDENTITÁS Alkalmazásazonosítója" az előállító együtt létrehozott értékét.

2. Adja meg az Azure Blob storage-felügyelt identitásnak megfelelő jogosultságot. Tekintse meg [kezelés hozzáférési jogosultsága ahhoz, hogy az RBAC Azure Storage-adatokkal](../storage/common/storage-auth-aad-rbac.md) a szerepköröket a részletekkel kapcsolatban.

    - **Forrásként**, az Access (IAM) vezérlőelemet, adja meg legalább **Storage-Blobadatok olvasója** szerepkör.
    - **Fogadóként**, az Access (IAM) vezérlőelemet, adja meg legalább **Storage-Blobadatok Közreműködője** szerepkör.

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

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a szakasz a Blob storage-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Adatok másolása Blob storage szolgáltatásba vagy onnan, állítsa be a type tulajdonság, az adatkészlet **AzureBlob**. A következő tulajdonságok támogatottak.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adatkészlet értékre kell állítani **AzureBlob**. |Igen |
| folderPath | A tároló és a blob Storage-mappa elérési útja. Helyettesítő karaktert tartalmazó szűrő nem támogatott. Ilyen például, myblobcontainer/myblobfolder /. |Igen, a Másolás/keresési tevékenység, GetMetadata tevékenység esetében: nem |
| fileName | **Név vagy helyettesítő karaktert tartalmazó szűrő** számára megadott "folderPath" alatt a BLOB(ok). Ez a tulajdonság értékét nem adja meg, ha az adatkészlet a mappában lévő összes BLOB mutat. <br/><br/>Szűrő esetén engedélyezett a helyettesítő karaktereket: `*` (nulla vagy több olyan karakterre illeszkedik) és `?` (megegyezik a nulla vagy önálló karakter).<br/>-1. példa: `"fileName": "*.csv"`<br/>– 2. példa: `"fileName": "???20180427.txt"`<br/>Használat `^` elkerülésére, ha a fájl tényleges nevét helyettesítő elemet vagy a escape karaktere belül.<br/><br/>Ha nincs megadva fájlnév egy kimeneti adatkészletet és **preserveHierarchy** nincs megadva a tevékenység fogadó, a másolási tevékenység létrehozza a blob neve a következő mintával: "*adatokat. [ tevékenység-végrehajtásonként azonosító GUID]. [GUID Ha FlattenHierarchy]. [Ha a konfigurált formátum]. [Ha konfigurálta a tömörítés]* ", például: "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz"; tábla neve helyett lekérdezési táblázatos forrásból másol, ha van-e a névminta "*[táblanév]. [ formátum]. [Ha konfigurálta a tömörítés]* ", például: "MyTable.csv". |Nem |
| modifiedDatetimeStart | Az attribútum alapján fájlok szűrés: utolsó módosítás. A fájlok lesz kiválasztva, ha az utolsó módosítás időpontja közötti időtartományban `modifiedDatetimeStart` és `modifiedDatetimeEnd`. Az idő UTC időzóna szerint formátumban alkalmazott "2018-12-01T05:00:00Z". <br/><br/> A Tulajdonságok lehet null értékű, ami jelenti azt, hogy nincs fájlszűrő attribútum alkalmazandó az adatkészletet.  Amikor `modifiedDatetimeStart` dátum és idő értékkel rendelkezik, de `modifiedDatetimeEnd` má hodnotu NULL, azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútum értéke nagyobb, mint vagy egyenlő a dátum és idő értékkel lesz kiválasztva.  Amikor `modifiedDatetimeEnd` dátum és idő értékkel rendelkezik, de `modifiedDatetimeStart` má hodnotu NULL, azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútum értéke kisebb, mint a dátum/idő értéket fog jelölni.| Nem |
| modifiedDatetimeEnd | Az attribútum alapján fájlok szűrés: utolsó módosítás. A fájlok lesz kiválasztva, ha az utolsó módosítás időpontja közötti időtartományban `modifiedDatetimeStart` és `modifiedDatetimeEnd`. Az idő UTC időzóna szerint formátumban alkalmazott "2018-12-01T05:00:00Z". <br/><br/> A Tulajdonságok lehet null értékű, ami jelenti azt, hogy nincs fájlszűrő attribútum alkalmazandó az adatkészletet.  Amikor `modifiedDatetimeStart` dátum és idő értékkel rendelkezik, de `modifiedDatetimeEnd` má hodnotu NULL, azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútum értéke nagyobb, mint vagy egyenlő a dátum és idő értékkel lesz kiválasztva.  Amikor `modifiedDatetimeEnd` dátum és idő értékkel rendelkezik, de `modifiedDatetimeStart` má hodnotu NULL, azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútum értéke kisebb, mint a dátum/idő értéket fog jelölni.| Nem |
| Formátum | Ha szeretné másolni a fájlokat, a fájlalapú tárolók (bináris másolat) közötti, hagyja ki a format szakaszban, mind a bemeneti és kimeneti adatkészlet-definíciókban.<br/><br/>Ha szeretné elemezni, vagy hozzon létre egy adott formátumú fájlok, formátuma a következő fájltípusokat támogatja: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, és **ParquetFormat**. Állítsa be a **típus** tulajdonság alatt **formátum** az alábbi értékek egyikére. További információkért lásd: a [szövegformátum](supported-file-formats-and-compression-codecs.md#text-format), [JSON formátumban](supported-file-formats-and-compression-codecs.md#json-format), [Avro formátum](supported-file-formats-and-compression-codecs.md#avro-format), [Orc formátum](supported-file-formats-and-compression-codecs.md#orc-format), és [Parquet formátum ](supported-file-formats-and-compression-codecs.md#parquet-format) szakaszokat. |Nem (csak a bináris másolás esetén) |
| A tömörítés | Adja meg a típus és az adatok tömörítési szintje. További információkért lásd: [támogatott fájlformátumok és tömörítési kodek](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Támogatott típusok a következők **GZip**, **Deflate**, **BZip2**, és **ZipDeflate**.<br/>Támogatott szintek a következők **Optimal** és **leggyorsabb**. |Nem |

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

Adatok másolása Blob storage-ból, állítsa be a forrás típusaként a másolási tevékenység **BlobSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrása típusa tulajdonságát állítsa **BlobSource**. |Igen |
| a rekurzív | Azt jelzi, hogy az adatok olvasható rekurzív módon az almappák vagy csak a megadott mappába. Vegye figyelembe, hogy ha a rekurzív értéke igaz, és a fogadó a fájlalapú tároló, egy üres mappát vagy almappát nem másolja vagy létrehozott, a fogadó.<br/>Engedélyezett értékek a következők **igaz** (alapértelmezett), és **hamis**. | Nem |

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

Adatok másolása Blob storage, állítsa be a fogadó típusa a másolási tevékenység **BlobSink**. A következő tulajdonságok támogatottak a **fogadó** szakaszban.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadó típusa tulajdonságát állítsa **BlobSink**. |Igen |
| a copyBehavior | A másolási viselkedés határozza meg, ha a forrás fájlok fájlalapú adattárból.<br/><br/>Engedélyezett értékek a következők:<br/><b>-PreserveHierarchy (alapértelmezett)</b>: megőrzi a hierarchiája a célmappában. A forrásmappa forrás-fájl elérési útja megegyezik a célmappában a célfájl elérési útja.<br/><b>-FlattenHierarchy</b>: a forrásmappából a fájlok az első szintjét a célmappában található is. A cél fájlok automatikusan létrehozott névvel rendelkeznek. <br/><b>-MergeFiles</b>: egyesíti a forrásmappából egy fájl összes fájlt. A fájl- vagy blob neve meg van adva, az egyesített fájlnév-e a megadott néven. Ellenkező esetben egy automatikusan létrehozott nevét. | Nem |

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

### <a name="some-recursive-and-copybehavior-examples"></a>A rekurzív és copyBehavior példákat

Ez a szakasz ismerteti az eredményül kapott viselkedéstől a másolási művelet rekurzív és copyBehavior értékek különböző kombinációihoz.

| a rekurzív | a copyBehavior | Forrás mappastruktúra | Eredményül kapott cél |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | a célmappában mappa1 szerkezete ugyanaz, mint a forrás hozzon létre:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A cél mappa1 jön létre az alábbi struktúra használatával: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet, a file1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File5 |
| true |mergeFiles | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A cél mappa1 jön létre az alábbi struktúra használatával: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + fájl3 + File4 + File5 tartalma egyesítve egy fájlt egy automatikusan létrehozott nevét. |
| false |preserveHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | a célmappában mappa1 az alábbi struktúra használatával jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Fájl3, File4 és File5 Subfolder1 nem mértékének. |
| false |flattenHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | a célmappában mappa1 az alábbi struktúra használatával jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet, a file1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File2<br/><br/>Fájl3, File4 és File5 Subfolder1 nem mértékének. |
| false |mergeFiles | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Az alábbi struktúra használatával jön létre a célmappában mappa1<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 tartalma egyesítve egy fájlt egy automatikusan létrehozott nevét. automatikusan létrehozott nevet, a file1 kiszolgálón<br/><br/>Fájl3, File4 és File5 Subfolder1 nem mértékének. |

## <a name="next-steps"></a>További lépések
A másolási tevékenység, Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md##supported-data-stores-and-formats).

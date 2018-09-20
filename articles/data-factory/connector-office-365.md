---
title: Adatok másolása az Office 365-be az Azure Data Factory (előzetes verzió) |} A Microsoft Docs
description: 'Útmutató: adatok másolása az Office 365-höz a támogatott fogadó adattárakba az Azure Data Factory-folyamatot másolási tevékenység használatával.'
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/19/2018
ms.author: jingwang
ms.openlocfilehash: 3783bc6ccda7a559e749a84005f20d860ab56755
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46368810"
---
# <a name="copy-data-from-office-365-into-azure-using-azure-data-factory-preview"></a>Adatok másolása az Office 365-ből az Azure-bA az Azure Data Factory (előzetes verzió) használatával 

Az Azure Data Factory lehetővé teszi a szervezeti adatokat az Office 365 bérlői az Azure-ba, skálázható módon, és analytics alkalmazásokat hozhat létre, és az elemzések alapján értékes adategységekhez gazdag használata. Integráció a Privileged Access Management az értékes összeválogatott adatokat, az Office 365-ben a biztonságos hozzáférés-vezérlést biztosít.  A Microsoft Azure előzetes verzióban érhető el a Microsoft Graph felügyelt hozzáférést további információkért tekintse meg [ezt a hivatkozást](https://github.com/OfficeDev/ManagedAccessMSGraph/wiki).

Ez a cikk ismerteti, hogyan használja a másolási tevékenység az Azure Data Factoryban az adatok másolása az Office 365-höz. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek

Most egy másolási tevékenységgel belül is csak **adatok másolása az Office 365-ből [Azure Blob Storage](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), és [Azure Data Lake Storage Gen2 () Előzetes verzió)](connector-azure-data-lake-storage.md) JSON formátumban** (setOfObjects típusa). Ha azt szeretné, az Office 365-ba való betöltésének egyéb típusú adattárakban vagy más formátumú, láncolhatja össze az adatokat további tölthet be bármelyikét későbbi másolási tevékenységgel rendelkező első másolási tevékenység a [ADF céltárolót támogatott](copy-activity-overview.md#supported-data-stores-and-formats) (lásd a" fogadóként támogatott"oszlop a"Támogatott adattárak és formázza az"tábla).

>[!IMPORTANT]
>- A data factory és a fogadó adattár tartalmazó Azure-előfizetés, az Office 365-bérlő ugyanahhoz az Azure Active Directory (Azure AD) bérlőhöz kell tartozniuk.
>- Győződjön meg, hogy a másolási tevékenységhez használt Azure integrációs modul régióban, valamint a cél van ugyanabban a régióban, ahol az Office 365 bérlői felhasználói postaláda. Tekintse meg [Itt](concepts-integration-runtime.md#integration-runtime-location) megérteni, hogyan határozza meg, az Azure integrációs modul helye. Tekintse meg [itt tábla](https://github.com/OfficeDev/ManagedAccessMSGraph/wiki/Capabilities#data-regions) támogatott Office-régiók és a megfelelő Azure-régiók listáját.
>-  Ha az Office 365-adatokat tölt be **Azure Blob Storage** célként, győződjön meg arról, hogy használ **[egyszerű szolgáltatásnév hitelesítése](connector-azure-blob-storage.md#service-principal-authentication)** a társított meghatározásakor Az Azure Blob Storage szolgáltatásra, és nem használ [fiókkulcs](connector-azure-blob-storage.md#account-key-authentication), [közös hozzáférésű jogosultságkód](connector-azure-blob-storage.md#shared-access-signature-authentication) vagy [felügyeltszolgáltatás-identitás ](connector-azure-blob-storage.md#managed-service-identity-authentication) üzenetküldéshez használ.
>-  Ha az Office 365-adatokat tölt be **Azure Data Lake Storage Gen1** célként, győződjön meg arról, hogy használ [ **egyszerű szolgáltatásnév hitelesítése** ](connector-azure-data-lake-store.md#using-service-principal-authentication) meghatározásakor a Társított szolgáltatás az Azure Data Lake Storage Gen1 és nem használ [managed service identity hitelesítési](connector-azure-data-lake-store.md#using-managed-service-identity-authentication).

## <a name="prerequisites"></a>Előfeltételek

Adatok másolása az Office 365-ből az Azure-ba, a következő előfeltételként szükséges lépéseket kell:

- Az Office 365-bérlői rendszergazdai leírtak szerint kell végeznie az előkészítési művelet [Itt](https://github.com/OfficeDev/ManagedAccessMSGraph/wiki/On-boarding).
- Hozzon létre, és a egy Azure AD-webalkalmazás konfigurálása az Azure Active Directoryban.  Útmutatásért lásd: [hozzon létre egy Azure AD-alkalmazást](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application).
- Jegyezze fel a következő értékeket, és az Office 365-höz, a társított szolgáltatás definiálásához használhat:
    - Bérlő azonosítója.  Útmutatásért lásd: [Bérlőazonosító beszerzése](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id).
    - Alkalmazás azonosítója és az alkalmazás kulcsa.  Útmutatásért lásd: [Get Alkalmazásazonosító és hitelesítési kulcs](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key).
- Adja hozzá a felhasználó identitását, aki a hozzáférési kérelem végez a tulajdonosa, az Azure AD-webalkalmazás (az Azure AD-ből a webes alkalmazás > Beállítások > tulajdonosok > Hozzáadás tulajdonosa).
- _(Ajánlott)_  [Hozzárendelése az Azure-szabályzatok](../azure-policy/assign-policy-definition.md) az adattárak, az adattitkosítás. Szabályzat megfelelőségi információk nem jelenik meg az adatokat a jóváhagyók a kérelem részeként. Szabályzat-hozzárendelés minden másolási tevékenység futtatása, történik, ha az ADF ellenőrzi, hogy a szabályzat-hozzárendelés kényszerítése. Tekintse meg [Itt](https://github.com/OfficeDev/ManagedAccessMSGraph/wiki/Capabilities#policies) támogatott szabályzatok teljes listáját.

## <a name="approving-new-data-access-requests"></a>Hozzáférési kérelmek jóváhagyásáról az új adatok

Ha első alkalommal a kért adatokat az ebben a környezetben (kombinációjával, hogy mely tábla folyamatban van a hozzáférés, mely cél fiók be az adatokat, és mely felhasználói identitás, hogy így az adatok hozzáférési kérés), megjelenik a másolási tevékenység állapota "Folyamatban", és csak akkor, ha a megoldásra kattint ["Details" hivatkozásra a műveletek](copy-activity-overview.md#monitoring) , megjelenik az állapot szerint "RequestingConsent".  Az adatelérési jóváhagyó csoportja tagjának kell jóváhagyhatja a kérést a Privileged Access Management, mielőtt konfigurálhatná az adatok kinyerése.

Tekintse meg [Itt](https://github.com/OfficeDev/ManagedAccessMSGraph/wiki/Approving-a-data-access-request) hogyan hagyhatja jóvá, a jóváhagyó a az adatok eléréséhez a kérelmet, és tekintse meg [Itt](https://github.com/OfficeDev/ManagedAccessMSGraph/wiki/On-boarding) általában véve az integrációra, a Privileged Access Management a magyarázat, többek között az adatok beállítása hozzáférés-jóváhagyója csoport.

## <a name="getting-started"></a>Első lépések

.NET SDK-t, Python SDK-t, az Azure PowerShell, REST API-t vagy az Azure Resource Manager-sablon használatával másolási tevékenységgel rendelkező folyamatot hozhat létre. Lásd: [másolási tevékenység oktatóanyagát](quickstart-create-data-factory-dot-net.md) egy másolási tevékenységgel ellátott adatcsatorna létrehozása a részletes útmutatóját.

A következő szakaszok segítségével határozhatók meg a Data Factory-entitások adott, az Office 365-összekötő-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Office 365-beli társított szolgáltatás a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonság értékre kell állítani: **Office 365** | Igen |
| office365TenantId | Az Azure-bérlő azonosítója, amelyhez az Office 365-fiók tartozik. | Igen |
| servicePrincipalTenantId | Adja meg a bérlő adatokat, amely alatt az Azure AD webes alkalmazás található. | Igen |
| servicePrincipalId | Adja meg az alkalmazás ügyfél-azonosítót. | Igen |
| servicePrincipalKey | Adja meg az alkalmazáskulcsot. Ez a mező jelölhetnek egy SecureString tárolja biztonságos helyen a Data Factoryban. | Igen |
| connectVia | Az integrációs modul az adattárban való kapcsolódáshoz használandó.  Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. | Nem |

>[!NOTE]
> A különbség a között **office365TenantId** és **servicePrincipalTenantId** és a megfelelő értéket adjon meg:
>- Ha Ön egy vállalati fejlesztői Office 365-adatokat a saját szervezet használati, szemben az alkalmazások fejlesztése, akkor meg kell adnia a ugyanazt bérlőhöz mindkét tulajdonság, amely a szervezet AAD-bérlő azonosítója azonosítóját.
>- Egy független Szoftverszállító fejlesztők az alkalmazások fejlesztése az ügyfelek számára, akkor office365TenantId lesz az ügyfél (az alkalmazástelepítő) AAD-Bérlőazonosítóval és servicePrincipalTenantId lesz a vállalati AAD-bérlő azonosítója.

**Példa**

```json
{
    "name": "Office365LinkedService",
    "properties": {
        "type": "Office365",
        "typeProperties": {
            "office365TenantId": "<Office 365 tenant id>",
            "servicePrincipalTenantId": "<AAD app service principal tenant id>",
            "servicePrincipalId": "<AAD app service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<AAD app service principal key>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a szakasz az Office 365-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Adatok másolása az Office 365-höz, a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adatkészlet értékre kell állítani: **Office365Table** | Igen |
| tableName | Az adatkészlet nevét a Office 365-ből kibontásához. Tekintse meg itt érhető el a a kivonási Office 365-adatkészletek listáját. | Igen |
| Predikátum | A predikátum kifejezés, amely segítségével kibontani az Office 365-höz adott sorok szűrése.  Tekintse meg itt megtudhatja, mely oszlopok predikátum szűrést az egyes táblákat és a szűrő kifejezésnek formátumban is használható. | Nem<br>(Ha nincs predikátum nem lett megadva, az alapértelmezett érték adatokat nyerhet ki az utolsó 30 nap) |

**Példa**

```json
{
    "name": "Office365Table1",
    "properties": {
        "type": "Office365Table",
        "linkedServiceName": {
            "referenceName": "<Office 365 linked service name>",
            "type": "LinkedServiceReference"
        },
        "structure": [
            {
                "name": "ReceivedDateTime",
                "type": "datetime"
            },
            {
                "name": "SentDateTime",
                "type": "datetime"
            },
            {
                "name": "HasAttachments",
                "type": "boolean"
            },
            {
                "name": "InternetMessageId",
                "type": "string"
            },
            {
                "name": "Subject",
                "type": "string"
            },
            {
                "name": "Importance",
                "type": "string"
            },
            {
                "name": "ParentFolderId",
                "type": "string"
            },
            {
                "name": "Sender",
                "type": "string"
            },
            {
                "name": "From",
                "type": "string"
            },
            {
                "name": "ToRecipients",
                "type": "string"
            },
            {
                "name": "CcRecipients",
                "type": "string"
            },
            {
                "name": "BccRecipients",
                "type": "string"
            },
            {
                "name": "ReplyTo",
                "type": "string"
            },
            {
                "name": "ConversationId",
                "type": "string"
            },
            {
                "name": "UniqueBody",
                "type": "string"
            },
            {
                "name": "IsDeliveryReceiptRequested",
                "type": "boolean"
            },
            {
                "name": "IsReadReceiptRequested",
                "type": "boolean"
            },
            {
                "name": "IsRead",
                "type": "boolean"
            },
            {
                "name": "IsDraft",
                "type": "boolean"
            },
            {
                "name": "WebLink",
                "type": "string"
            },
            {
                "name": "CreatedDateTime",
                "type": "datetime"
            },
            {
                "name": "LastModifiedDateTime",
                "type": "datetime"
            },
            {
                "name": "ChangeKey",
                "type": "string"
            },
            {
                "name": "Categories",
                "type": "string"
            },
            {
                "name": "Id",
                "type": "string"
            },
            {
                "name": "Attachments",
                "type": "string"
            }
        ],
        "typeProperties": {
            "tableName": "BasicDataSet_v0.Contact_v0",
            "predicate": "CreatedDateTime >= 2018-07-11T16:00:00.000Z AND CreatedDateTime <= 2018-07-18T16:00:00.000Z"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz az Office 365-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="office-365-as-source"></a>Az Office 365 forrásként

Adatok másolása az Office 365-höz, állítsa be a forrás típusaként a másolási tevékenység **Office365Source**. Nincsenek további tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban.

**Példa**

```json
"activities": [
    {
        "name": "CopyFromO365ToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Office 365 input dataset name>",
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
                "type": "Office365Source"
            },
            "sink": {
                "type": "BlobSink"
            }
        }
    }
]
```

## <a name="next-steps"></a>További lépések
A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).

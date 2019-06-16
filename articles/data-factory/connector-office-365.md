---
title: Adatok másolása az Office 365, Azure Data Factory használatával |} A Microsoft Docs
description: 'Útmutató: adatok másolása az Office 365-höz a támogatott fogadó adattárakba az Azure Data Factory-folyamatot másolási tevékenység használatával.'
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: jingwang
ms.openlocfilehash: 1a8d622aa280794d9a4d6fe7320ddcc21ac044f4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66475665"
---
# <a name="copy-data-from-office-365-into-azure-using-azure-data-factory"></a>Adatok másolása az Office 365-ből az Azure-bA az Azure Data Factory használatával

Integrálható az Azure Data Factory [csatlakoztatása a Microsoft Graph adatváltozásainak](https://docs.microsoft.com/graph/data-connect-concept-overview), így ahhoz, hogy a szervezeti adatokat az Office 365 bérlői az Azure-ba, skálázható módon, valamint analytics alkalmazások gazdag és az elemzések alapján ezek értékes adatvagyon. Integráció a Privileged Access Management az értékes összeválogatott adatokat, az Office 365-ben a biztonságos hozzáférés-vezérlést biztosít.  Tekintse meg [ezt a hivatkozást](https://docs.microsoft.com/graph/data-connect-concept-overview) áttekintését a Microsoft Graph adatváltozásainak csatlakozzon és tekintse meg [ezt a hivatkozást](https://docs.microsoft.com/graph/data-connect-policies#licensing) licencelési információkat.

Ez a cikk ismerteti, hogyan használja a másolási tevékenység az Azure Data Factoryban az adatok másolása az Office 365-höz. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek
ADF Office 365-összekötő és a Microsoft Graph adatváltozásainak csatlakozás gyűjtése a különböző típusú adathalmazok, lehetővé teszi, hogy engedélyezve van az Exchange e-mailek postaládájából, beleértve a címjegyzékében szereplő, naptárbeli események, e-mailek, felhasználói adatok, postafiók beállításait, és így tovább.  Tekintse meg [Itt](https://docs.microsoft.com/graph/data-connect-datasets) adathalmazlistából teljes listájának megtekintéséhez.

Most egy másolási tevékenységgel belül is csak **adatok másolása az Office 365-ből [Azure Blob Storage](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), és [Azure Data Lake Storage Gen2 ](connector-azure-data-lake-storage.md) JSON formátumban** (setOfObjects típusa). Ha azt szeretné, az Office 365-ba való betöltésének egyéb típusú adattárakban vagy más formátumú, láncolhatja össze az adatokat további tölthet be bármelyikét későbbi másolási tevékenységgel rendelkező első másolási tevékenység a [ADF céltárolót támogatott](copy-activity-overview.md#supported-data-stores-and-formats) (lásd a" fogadóként támogatott"oszlop a"Támogatott adattárak és formázza az"tábla).

>[!IMPORTANT]
>- A data factory és a fogadó adattár tartalmazó Azure-előfizetés, az Office 365-bérlő ugyanahhoz az Azure Active Directory (Azure AD) bérlőhöz kell tartozniuk.
>- Győződjön meg, hogy a másolási tevékenységhez használt Azure integrációs modul régióban, valamint a cél van ugyanabban a régióban, ahol az Office 365 bérlői felhasználói postaláda. Tekintse meg [Itt](concepts-integration-runtime.md#integration-runtime-location) megérteni, hogyan határozza meg, az Azure integrációs modul helye. Tekintse meg [itt tábla](https://docs.microsoft.com/graph/data-connect-datasets#regions) támogatott Office-régiók és a megfelelő Azure-régiók listáját.
>- Egyszerű szolgáltatás hitelesítése az Azure Blob Storage, Azure Data Lake Storage Gen1 és az Azure Data Lake Storage Gen2 való céltárolót is támogatott, egyetlen hitelesítési mechanizmusa.

## <a name="prerequisites"></a>Előfeltételek

Adatok másolása az Office 365-ből az Azure-ba, a következő előfeltételként szükséges lépéseket kell:

- Az Office 365-bérlői rendszergazdai leírtak szerint kell végeznie az előkészítési művelet [Itt](https://docs.microsoft.com/graph/data-connect-get-started).
- Hozzon létre, és a egy Azure AD-webalkalmazás konfigurálása az Azure Active Directoryban.  Útmutatásért lásd: [hozzon létre egy Azure AD-alkalmazást](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application).
- Jegyezze fel a következő értékeket, és az Office 365-höz, a társított szolgáltatás definiálásához használhat:
    - Bérlő azonosítója. Útmutatásért lásd: [Bérlőazonosító beszerzése](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).
    - Alkalmazás azonosítója és az alkalmazás kulcsa.  Útmutatásért lásd: [Get Alkalmazásazonosító és hitelesítési kulcs](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).
- Adja hozzá a felhasználó identitását, aki a hozzáférési kérelem végez a tulajdonosa, az Azure AD-webalkalmazás (az Azure AD-ből a webes alkalmazás > Beállítások > tulajdonosok > Hozzáadás tulajdonosa). 
    - A felhasználói identitás az Office 365-szervezethez kihozhatják származó adatokat, és nem lehet a vendégfelhasználó kell lennie.

## <a name="approving-new-data-access-requests"></a>Hozzáférési kérelmek jóváhagyásáról az új adatok

Ha első alkalommal a kért adatokat az ebben a környezetben (kombinációjával, hogy mely tábla folyamatban van a hozzáférés, mely cél fiók be az adatokat, és mely felhasználói identitás, hogy így az adatok hozzáférési kérés), megjelenik a másolási tevékenység állapota "Folyamatban", és csak akkor, ha a megoldásra kattint ["Details" hivatkozásra a műveletek](copy-activity-overview.md#monitoring) , megjelenik az állapot szerint "RequestingConsent".  Az adatelérési jóváhagyó csoportja tagjának kell jóváhagyhatja a kérést a Privileged Access Management, mielőtt konfigurálhatná az adatok kinyerése.

Tekintse meg [Itt](https://docs.microsoft.com/graph/data-connect-tips#approve-pam-requests-via-office-365-admin-portal) hogyan hagyhatja jóvá, a jóváhagyó a az adatok eléréséhez a kérelmet, és tekintse meg [Itt](https://docs.microsoft.com/graph/data-connect-pam) általában véve az integrációra, a Privileged Access Management a magyarázat, többek között az adatok beállítása hozzáférés-jóváhagyója csoport.

## <a name="policy-validation"></a>Házirend érvényesítése

Ha a felügyeleti csoporton belüli erőforrásokon végrehajtott Azure szabályzatok hozzárendeléseinek felügyelt alkalmazás részeként jön létre az ADF, majd minden másolási tevékenység futtatása, ADF ellenőrzi, győződjön meg arról, hogy a szabályzat-hozzárendelések érvényben vannak. Tekintse meg [Itt](https://docs.microsoft.com/graph/data-connect-policies#policies) támogatott szabályzatok listája.

## <a name="getting-started"></a>Első lépések

>[!TIP]
>Az Office 365-összekötő használatával, olvassa [adatok betöltése az Office 365](load-office-365-data.md) cikk.

A másolási tevékenységgel rendelkező folyamatot hozhat létre egyet a következő eszközök és az SDK-k használatával. Nyissa meg egy másolási tevékenységgel rendelkező folyamat létrehozása a részletes oktatóanyag mutató hivatkozás kiválasztása. 

- [Azure Portal](quickstart-create-data-factory-portal.md)
- [.NET SDK](quickstart-create-data-factory-dot-net.md)
- [Python SDK](quickstart-create-data-factory-python.md)
- [Azure PowerShell](quickstart-create-data-factory-powershell.md)
- [REST API](quickstart-create-data-factory-rest-api.md)
- [Az Azure Resource Manager-sablon](quickstart-create-data-factory-resource-manager-template.md). 

A következő szakaszok segítségével határozhatók meg a Data Factory-entitások adott, az Office 365-összekötő-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Office 365-beli társított szolgáltatás a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **Office365** | Igen |
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
| tableName | Az adatkészlet nevét a Office 365-ből kibontásához. Tekintse meg [Itt](https://docs.microsoft.com/graph/data-connect-datasets#datasets) kinyerési érhető el az Office 365-adatkészletek listáját. | Igen |
| allowedGroups | Csoport kiválasztása predikátum.  Ez a tulajdonság legfeljebb 10 felhasználói csoportokat, akik számára az adatok lekérésének kiválasztásához használja.  Ha nincsenek csoportok meg van adva, majd az adatok visszaadásához fog a teljes cég számára. | Nem |
| userScopeFilterUri | Amikor `allowedGroups` tulajdonság nincs megadva, használhat egy predikátum kifejezés, a teljes bérlő kibontani az Office 365-höz adott sorok szűrése a alkalmazni. A predikátum formátum meg kell egyeznie a lekérdezési formátumban a Microsoft Graph API-k, például `https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'`. | Nem |
| dateFilterColumn | A dátum/idő szűrőoszlop neve. Ez a tulajdonság segítségével korlátozhatja az időtartományt, mely Office 365-höz készült adatok ki kell olvasni. | Igen, ha az adatkészlet egy vagy több dátum/idő-oszlop rendelkezik. Tekintse meg [Itt](https://docs.microsoft.com/graph/data-connect-filtering#filtering) igénylő Ez a DateTime típusú szűrő az adatkészletek listáját. |
| startTime | Indítsa el a szűréshez használandó dátum/idő értéket. | Igen, ha `dateFilterColumn` van megadva |
| endTime | Záró dátum és idő szűréshez használandó értéket. | Igen, ha `dateFilterColumn` van megadva |

**Példa**

```json
{
    "name": "DS_May2019_O365_Message",
    "properties": {
        "type": "Office365Table",
        "linkedServiceName": {
            "referenceName": "<Office 365 linked service name>",
            "type": "LinkedServiceReference"
        },
        "structure": [
            {
                "name": "Id",
                "type": "String",
                "description": "The unique identifier of the event."
            },
            {
                "name": "CreatedDateTime",
                "type": "DateTime",
                "description": "The date and time that the event was created."
            },
            {
                "name": "LastModifiedDateTime",
                "type": "DateTime",
                "description": "The date and time that the event was last modified."
            },
            {
                "name": "ChangeKey",
                "type": "String",
                "description": "Identifies the version of the event object. Every time the event is changed, ChangeKey changes as well. This allows Exchange to apply changes to the correct version of the object."
            },
            {
                "name": "Categories",
                "type": "String",
                "description": "The categories associated with the event. Format: ARRAY<STRING>"
            },
            {
                "name": "OriginalStartTimeZone",
                "type": "String",
                "description": "The start time zone that was set when the event was created. See DateTimeTimeZone for a list of valid time zones."
            },
            {
                "name": "OriginalEndTimeZone",
                "type": "String",
                "description": "The end time zone that was set when the event was created. See DateTimeTimeZone for a list of valid time zones."
            },
            {
                "name": "ResponseStatus",
                "type": "String",
                "description": "Indicates the type of response sent in response to an event message. Format: STRUCT<Response: STRING, Time: STRING>"
            },
            {
                "name": "iCalUId",
                "type": "String",
                "description": "A unique identifier that is shared by all instances of an event across different calendars."
            },
            {
                "name": "ReminderMinutesBeforeStart",
                "type": "Int32",
                "description": "The number of minutes before the event start time that the reminder alert occurs."
            },
            {
                "name": "IsReminderOn",
                "type": "Boolean",
                "description": "Set to true if an alert is set to remind the user of the event."
            },
            {
                "name": "HasAttachments",
                "type": "Boolean",
                "description": "Set to true if the event has attachments."
            },
            {
                "name": "Subject",
                "type": "String",
                "description": "The text of the event's subject line."
            },
            {
                "name": "Body",
                "type": "String",
                "description": "The body of the message associated with the event.Format: STRUCT<ContentType: STRING, Content: STRING>"
            },
            {
                "name": "Importance",
                "type": "String",
                "description": "The importance of the event: Low, Normal, High."
            },
            {
                "name": "Sensitivity",
                "type": "String",
                "description": "Indicates the level of privacy for the event: Normal, Personal, Private, Confidential."
            },
            {
                "name": "Start",
                "type": "String",
                "description": "The start time of the event. Format: STRUCT<DateTime: STRING, TimeZone: STRING>"
            },
            {
                "name": "End",
                "type": "String",
                "description": "The date and time that the event ends. Format: STRUCT<DateTime: STRING, TimeZone: STRING>"
            },
            {
                "name": "Location",
                "type": "String",
                "description": "Location information of the event. Format: STRUCT<DisplayName: STRING, Address: STRUCT<Street: STRING, City: STRING, State: STRING, CountryOrRegion: STRING, PostalCode: STRING>, Coordinates: STRUCT<Altitude: DOUBLE, Latitude: DOUBLE, Longitude: DOUBLE, Accuracy: DOUBLE, AltitudeAccuracy: DOUBLE>>"
            },
            {
                "name": "IsAllDay",
                "type": "Boolean",
                "description": "Set to true if the event lasts all day. Adjusting this property requires adjusting the Start and End properties of the event as well."
            },
            {
                "name": "IsCancelled",
                "type": "Boolean",
                "description": "Set to true if the event has been canceled."
            },
            {
                "name": "IsOrganizer",
                "type": "Boolean",
                "description": "Set to true if the message sender is also the organizer."
            },
            {
                "name": "Recurrence",
                "type": "String",
                "description": "The recurrence pattern for the event. Format: STRUCT<Pattern: STRUCT<Type: STRING, `Interval`: INT, Month: INT, DayOfMonth: INT, DaysOfWeek: ARRAY<STRING>, FirstDayOfWeek: STRING, Index: STRING>, `Range`: STRUCT<Type: STRING, StartDate: STRING, EndDate: STRING, RecurrenceTimeZone: STRING, NumberOfOccurrences: INT>>"
            },
            {
                "name": "ResponseRequested",
                "type": "Boolean",
                "description": "Set to true if the sender would like a response when the event is accepted or declined."
            },
            {
                "name": "ShowAs",
                "type": "String",
                "description": "The status to show: Free, Tentative, Busy, Oof, WorkingElsewhere, Unknown."
            },
            {
                "name": "Type",
                "type": "String",
                "description": "The event type: SingleInstance, Occurrence, Exception, SeriesMaster."
            },
            {
                "name": "Attendees",
                "type": "String",
                "description": "The collection of attendees for the event. Format: ARRAY<STRUCT<EmailAddress: STRUCT<Name: STRING, Address: STRING>, Status: STRUCT<Response: STRING, Time: STRING>, Type: STRING>>"
            },
            {
                "name": "Organizer",
                "type": "String",
                "description": "The organizer of the event. Format: STRUCT<EmailAddress: STRUCT<Name: STRING, Address: STRING>>"
            },
            {
                "name": "WebLink",
                "type": "String",
                "description": "The URL to open the event in Outlook Web App."
            },
            {
                "name": "Attachments",
                "type": "String",
                "description": "The FileAttachment and ItemAttachment attachments for the message. Navigation property. Format: ARRAY<STRUCT<LastModifiedDateTime: STRING, Name: STRING, ContentType: STRING, Size: INT, IsInline: BOOLEAN, Id: STRING>>"
            },
            {
                "name": "BodyPreview",
                "type": "String",
                "description": "The preview of the message associated with the event. It is in text format."
            },
            {
                "name": "Locations",
                "type": "String",
                "description": "The locations where the event is held or attended from. The location and locations properties always correspond with each other. Format:  ARRAY<STRUCT<DisplayName: STRING, Address: STRUCT<Street: STRING, City: STRING, State: STRING, CountryOrRegion: STRING, PostalCode: STRING>, Coordinates: STRUCT<Altitude: DOUBLE, Latitude: DOUBLE, Longitude: DOUBLE, Accuracy: DOUBLE, AltitudeAccuracy: DOUBLE>, LocationEmailAddress: STRING, LocationUri: STRING, LocationType: STRING, UniqueId: STRING, UniqueIdType: STRING>>"
            },
            {
                "name": "OnlineMeetingUrl",
                "type": "String",
                "description": "A URL for an online meeting. The property is set only when an organizer specifies an event as an online meeting such as a Skype meeting"
            },
            {
                "name": "OriginalStart",
                "type": "DateTime",
                "description": "The start time that was set when the event was created in UTC time."
            },
            {
                "name": "SeriesMasterId",
                "type": "String",
                "description": "The ID for the recurring series master item, if this event is part of a recurring series."
            }
        ],
        "typeProperties": {
            "tableName": "BasicDataSet_v0.Event_v1",
            "dateFilterColumn": "CreatedDateTime",
            "startTime": "2019-04-28T16:00:00.000Z",
            "endTime": "2019-05-05T16:00:00.000Z",
            "userScopeFilterUri": "https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'"
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

---
title: Adatok másolása az Office 365-ből az Azure Data Factory használatával
description: Megtudhatja, hogy miként másolhat adatokat az Office 365-ből a támogatott fogadó adattárakba az Azure Data Factory-folyamat másolási tevékenységének használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/20/2019
ms.author: jingwang
ms.openlocfilehash: ea68fa8d9326e6d9ebb4f475d16ac83959cae6e5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416878"
---
# <a name="copy-data-from-office-365-into-azure-using-azure-data-factory"></a>Adatok másolása az Office 365-ből az Azure-ba az Azure Data Factory használatával
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Az Azure Data Factory integrálható a [Microsoft Graph adatcsatlakozással,](https://docs.microsoft.com/graph/data-connect-concept-overview)lehetővé téve, hogy az Office 365-ös bérlőben található gazdag szervezeti adatokat méretezhető módon hozza be az Azure-ba, és elemzési alkalmazásokat hozzon létre, és elemzéseket nyerjen ki ezen értékes adateszközök alapján. A Kiemelt hozzáférés-kezelésszolgáltatással való integráció biztonságos hozzáférés-vezérlést biztosít az értékes válogatott adatokhoz az Office 365-ben.  A Microsoft Graph adatcsatlakozásáról a hivatkozást ebben a [linkben](https://docs.microsoft.com/graph/data-connect-concept-overview) találja, és a licenceléssel kapcsolatos információkat lásd ebben a [linkben.](https://docs.microsoft.com/graph/data-connect-policies#licensing)

Ez a cikk bemutatja, hogyan használhatja a másolási tevékenységet az Azure Data Factory-ban az Office 365-ből történő adatok másolásához. A [másolási tevékenység áttekintése](copy-activity-overview.md) cikkre épül, amely a másolási tevékenység általános áttekintését mutatja be.

## <a name="supported-capabilities"></a>Támogatott képességek
Az ADF Office 365-összekötő és a Microsoft Graph-adatok összekapcsolása lehetővé teszi az Exchange Email-kompatibilis postaládákkülönböző típusú adatkészleteinek nagymértékű betöltését, beleértve a címjegyzékbeli névjegyeket, a naptáreseményeket, az e-mail üzeneteket, a felhasználói adatokat, a postaláda-beállításokat és így tovább.  A rendelkezésre álló adatkészletek teljes listájának megtekintéséhez tekintse meg [itt.](https://docs.microsoft.com/graph/data-connect-datasets)

Egyelőre egyetlen másolási tevékenységen belül csak **az Office 365-ből másolhat adatokat [az Azure Blob Storage](connector-azure-blob-storage.md), az Azure Data Lake Storage [Gen1](connector-azure-data-lake-store.md)és az [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) json formátumban** (type setOfObjects). Ha az Office 365-öt más típusú adattárakba vagy más formátumokba szeretné betölteni, az első másolási tevékenységet egy későbbi másolási tevékenységgel láncolhatja, hogy további adatokat töltsön be a [támogatott ADF céltárolók](copy-activity-overview.md#supported-data-stores-and-formats) bármelyikébe (lásd a "Támogatott adattárak és formátumok" táblázat "Támogatott fogadóként" oszlopát).

>[!IMPORTANT]
>- Az adat-előállítót és a fogadó adattárat tartalmazó Azure-előfizetésnek az Office 365-ös bérlővel azonos Azure Active Directory (Azure AD) bérlő alatt kell lennie.
>- Győződjön meg arról, hogy a másolási tevékenységhez használt Azure-integrációs runtime régió, valamint a cél ugyanabban a régióban található, ahol az Office 365-ös bérlői felhasználók postaládája található. Tekintse meg [itt,](concepts-integration-runtime.md#integration-runtime-location) hogy az Azure IR-hely meghatározása. A támogatott Office-régiók és a megfelelő Azure-régiók listáját itt [található táblázatban](https://docs.microsoft.com/graph/data-connect-datasets#regions) olvashatja.
>- Az Egyszerű szolgáltatáshitelesítés az egyetlen hitelesítési mechanizmus, amelyet az Azure Blob Storage, az Azure Data Lake Storage Gen1 és az Azure Data Lake Storage Gen2 támogatott céltárolóként.

## <a name="prerequisites"></a>Előfeltételek

Ha adatokat szeretne másolni az Office 365-ből az Azure-ba, a következő előfeltételeket kell végrehajtania:

- Az Office 365-ös bérlői rendszergazdájának [az itt](https://docs.microsoft.com/graph/data-connect-get-started)leírtak szerint beszállási műveleteket kell végrehajtania.
- Hozzon létre és konfiguráljon egy Azure AD webalkalmazást az Azure Active Directoryban.  További információt az [Azure AD-alkalmazás létrehozása című témakörben talál.](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)
- Jegyezze fel a következő értékeket, amelyeket az Office 365 csatolt szolgáltatásának definiálására fog használni:
    - Bérlőazonosítója. További információt a [Bérlőazonosító beolvassa.](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in)
    - Alkalmazásazonosító és alkalmazáskulcs.  További információt az [Alkalmazásazonosító és a hitelesítési kulcs beírása című témakörben talál.](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in)
- Adja hozzá a felhasználói identitást, aki az Azure AD webalkalmazás tulajdonosaként az adatelérési kérelmet fogja megadni (az Azure AD webalkalmazásból > settings > owners > Add owner). 
    - A felhasználói identitásnak abban az Office 365-szervezetben kell lennie, amelytől adatokat kap, és nem lehet vendégfelhasználó.

## <a name="approving-new-data-access-requests"></a>Új adathozzáférési kérelmek jóváhagyása

Ha ez az első alkalom, hogy adatokat kér ehhez a környezethez (amelynek kombinációja az adattábla elérése, melyik célfiókba töltődik be, és melyik felhasználói identitás teszi az adathozzáférési kérelmet), akkor a másolási tevékenység állapota "Folyamatban" lesz, és csak akkor, ha a Műveletek alatt a ["Részletek" hivatkozásra](copy-activity-overview.md#monitoring) kattint, "Consent kérése" állapotot fog látni.  Az adatelérés-jóváhagyó csoport egyik tagjának jóvá kell hagynia a kérelmet a kiemelt hozzáférés-kezelésben, mielőtt az adatok kinyerése folytatódhatna.

Itt [here](https://docs.microsoft.com/graph/data-connect-tips#approve-pam-requests-via-office-365-admin-portal) olvashat arról, hogy a jóváhagyó hogyan hagyhatja jóvá az adatelérési kérelmet, és [itt](https://docs.microsoft.com/graph/data-connect-pam) olvashat a Kiemelt hozzáférés-kezeléssel való általános integrációról, beleértve az adatelérés-jóváhagyó csoport beállításának magyarázatát.

## <a name="policy-validation"></a>Szabályzat érvényesítése

Ha az ADF egy felügyelt alkalmazás részeként jön létre, és az Azure-szabályzatok hozzárendelései a felügyeleti erőforráscsoporton belüli erőforrásokon történiknek, majd minden másolási tevékenység futtatásakor az ADF ellenőrzi, hogy a házirend-hozzárendelések érvénybe lépnek-e. A támogatott házirendek listáját [itt](https://docs.microsoft.com/graph/data-connect-policies#policies) olvashatja el.

## <a name="getting-started"></a>Első lépések

>[!TIP]
>Az Office 365-összekötő használatának forgatókönyvét az [Adatok betöltése az Office 365-ből](load-office-365-data.md) című cikkben olvashat.

A másolási tevékenységgel egy folyamatot az alábbi eszközök vagy SDK-k egyikével hozhat létre. Válasszon egy hivatkozást, amely egy oktatóanyagra való ugráshoz lépésenkénti utasításokat tartalmaz egy másolási tevékenységet rendelkező folyamat létrehozásához. 

- [Azure Portal](quickstart-create-data-factory-portal.md)
- [.NET SDK](quickstart-create-data-factory-dot-net.md)
- [Python SDK](quickstart-create-data-factory-python.md)
- [Azure PowerShell](quickstart-create-data-factory-powershell.md)
- [REST API](quickstart-create-data-factory-rest-api.md)
- [Az Azure Resource Manager sablonja](quickstart-create-data-factory-resource-manager-template.md). 

Az alábbi szakaszok az Office 365-összekötőre jellemző Data Factory-entitások definiálásához használt tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

Az Office 365 csatolt szolgáltatása a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A típustulajdonságnak a következőre kell állítania: **Office365** | Igen |
| office365Bérlőazonosító | Az Azure-bérlői azonosító, amelyhez az Office 365-fiók tartozik. | Igen |
| servicePrincipalTenantId | Adja meg a bérlői adatokat, amelyek alatt az Azure AD webalkalmazás található. | Igen |
| servicePrincipalId | Adja meg az alkalmazás ügyfélazonosítóját. | Igen |
| servicePrincipalKey | Adja meg az alkalmazás kulcsát. Jelölje meg ezt a mezőt SecureStringként, hogy biztonságosan tárolhatja a Data Factory-ban. | Igen |
| connectVia | Az adattárhoz való csatlakozáshoz használandó integrációs futásidő.  Ha nincs megadva, az alapértelmezett Azure-integrációs runtime-ot használja. | Nem |

>[!NOTE]
> Az **office365TenantId** és **a servicePrincipalTenantId** és a megfelelő megadott érték közötti különbség:
>- Ha Ön nagyvállalati fejlesztő, aki saját szervezete számára fejleszt alkalmazást az Office 365-adatokkal szemben, akkor ugyanazt a bérlői azonosítót kell megadnia mindkét tulajdonsághoz, amely a szervezet AAD-bérlői azonosítója.
>- Ha ön egy független szoftverszállító fejlesztő, aki alkalmazást fejleszt az ügyfelek számára, akkor az office365TenantId lesz az ügyfél (alkalmazástelepítő) AAD-bérlőazonosítója, és a servicePrincipalTenantId lesz a vállalat AAD-bérlőazonosítója.

**Példa:**

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

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [adatkészletekről](concepts-datasets-linked-services.md) szóló cikkben olvashatja. Ez a szakasz az Office 365-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Az Office 365-ből történő adatok másolásához a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet típustulajdonságát a következőre kell állítani: **Office365Table** | Igen |
| tableName | Az Office 365-ből kibontandó adatkészlet neve. A kinyerésre rendelkezésre álló Office 365-adatkészletek listáját [itt](https://docs.microsoft.com/graph/data-connect-datasets#datasets) olvashatja. | Igen |

Ha a `dateFilterColumn`, `startTime` `endTime`, `userScopeFilterUri` , és az adatkészletben, akkor továbbra is támogatott, ahogy van, míg azt javasoljuk, hogy használja az új modell tevékenységforrás megy előre.

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
        "schema": [],
        "typeProperties": {
            "tableName": "BasicDataSet_v0.Event_v1"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok](concepts-pipelines-activities.md) című cikkben olvashat. Ez a szakasz az Office 365-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="office-365-as-source"></a>Az Office 365 forrása

Az Office 365-ből történő adatok másolásához a másolási tevékenység **forrásszakaszában** a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenységforrás típustulajdonságának a következőre kell állítania: **Office365Source** | Igen |
| allowedGroups (engedélyezettcsoportok) | Csoportkiválasztási állítmány.  Ezzel a tulajdonsággal legfeljebb 10 olyan felhasználói csoportot választhat ki, amelyek számára az adatokat beolvassa a rendszer.  Ha nincs megadva csoport, akkor a rendszer a teljes szervezet adatait visszaadja. | Nem |
| userScopeFilterUri | Ha `allowedGroups` a tulajdonság nincs megadva, használhat egy predikátumkifejezést, amely a teljes bérlőre vonatkozik az adott sorok szűréséhez az Office 365-ből való kibontáshoz. Az alapformátumnak meg kell egyeznie a Microsoft Graph API-k lekérdezési formátumával, pl. . `https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'` | Nem |
| dateFilterColumn oszlop | A DateTime szűrőoszlop neve. Ezzel a tulajdonsággal korlátozhatja azt az időtartományt, amelyre vonatkozóan az Office 365-adatokat kinyeri. | Igen, ha az adatkészlet egy vagy több DateTime oszlopot tartalmaz. Itt [here](https://docs.microsoft.com/graph/data-connect-filtering#filtering) található a DateTime szűrőt igénylő adatkészletek listája. |
| startTime | A szűréshez a DateTime kezdő értéke. | Igen, `dateFilterColumn` ha meg van adva |
| endTime | A szűréshez a DateTime érték befejezése. | Igen, `dateFilterColumn` ha meg van adva |
| outputOszlopok | A fogadóba másolandó oszlopok tömbje. | Nem |

**Példa:**

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
                "type": "Office365Source",
                "dateFilterColumn": "CreatedDateTime",
                "startTime": "2019-04-28T16:00:00.000Z",
                "endTime": "2019-05-05T16:00:00.000Z",
                "userScopeFilterUri": "https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'",
                "outputColumns": [
                    {
                        "name": "Id"
                    },
                    {
                        "name": "CreatedDateTime"
                    },
                    {
                        "name": "LastModifiedDateTime"
                    },
                    {
                        "name": "ChangeKey"
                    },
                    {
                        "name": "Categories"
                    },
                    {
                        "name": "OriginalStartTimeZone"
                    },
                    {
                        "name": "OriginalEndTimeZone"
                    },
                    {
                        "name": "ResponseStatus"
                    },
                    {
                        "name": "iCalUId"
                    },
                    {
                        "name": "ReminderMinutesBeforeStart"
                    },
                    {
                        "name": "IsReminderOn"
                    },
                    {
                        "name": "HasAttachments"
                    },
                    {
                        "name": "Subject"
                    },
                    {
                        "name": "Body"
                    },
                    {
                        "name": "Importance"
                    },
                    {
                        "name": "Sensitivity"
                    },
                    {
                        "name": "Start"
                    },
                    {
                        "name": "End"
                    },
                    {
                        "name": "Location"
                    },
                    {
                        "name": "IsAllDay"
                    },
                    {
                        "name": "IsCancelled"
                    },
                    {
                        "name": "IsOrganizer"
                    },
                    {
                        "name": "Recurrence"
                    },
                    {
                        "name": "ResponseRequested"
                    },
                    {
                        "name": "ShowAs"
                    },
                    {
                        "name": "Type"
                    },
                    {
                        "name": "Attendees"
                    },
                    {
                        "name": "Organizer"
                    },
                    {
                        "name": "WebLink"
                    },
                    {
                        "name": "Attachments"
                    },
                    {
                        "name": "BodyPreview"
                    },
                    {
                        "name": "Locations"
                    },
                    {
                        "name": "OnlineMeetingUrl"
                    },
                    {
                        "name": "OriginalStart"
                    },
                    {
                        "name": "SeriesMasterId"
                    }
                ]
            },
            "sink": {
                "type": "BlobSink"
            }
        }
    }
]
```

## <a name="next-steps"></a>További lépések
A forrásként támogatott és fogadóként az Azure Data Factory másolási tevékenysége által támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats)című témakörben tetszhet.

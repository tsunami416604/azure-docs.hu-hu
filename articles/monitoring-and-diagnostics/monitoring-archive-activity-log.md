---
title: "Az Azure tevékenységnapló archiválására |} Microsoft Docs"
description: "Útmutató az Azure tevékenységnapló a hosszú távú megőrzési tárfiókokban archiválja."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: d37d3fda-8ef1-477c-a360-a855b418de84
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2016
ms.author: johnkem
ms.openlocfilehash: 0e3a5b84f57eac96249430fa1c2c4cc076c2926a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="archive-the-azure-activity-log"></a>Az Azure tevékenységnapló archiválása
Ebben a cikkben megmutatjuk használatát az Azure portálon, a PowerShell-parancsmagokkal vagy a platformfüggetlen parancssori felület archiválására a [ **Azure tevékenységnapló** ](monitoring-overview-activity-logs.md) tárfiókokban. Ez a beállítás akkor hasznos, ha azt szeretné, hogy megőrzi a naplózási, statikus elemzési vagy biztonsági mentése (a teljes hozzáféréssel az adatmegőrzési) 90 napnál hosszabb tevékenységnapló. Ha csak szeretné megőrizni az események 90 napig, vagy kevesebb nem kell beállítása archiválási tárfiókba, mert tevékenységnapló események kerülnek be az Azure platformon 90 napig engedélyezése archiválás nélkül.

## <a name="prerequisites"></a>Előfeltételek
Kezdés előtt kell [hozzon létre egy tárfiókot](../storage/common/storage-create-storage-account.md#create-a-storage-account) , amelyhez a tevékenységnapló archiválhatók. Erősen ajánlott, hogy nem használja a benne tárolt, így jobban szabályozhatja a hozzáférést a figyelési adatok, amelyeket más, nem figyelési adatokat tartalmazó meglévő tárfiókot. Azonban ha is archiválás diagnosztikai naplók és a metrikák egy tárfiókba, logikus összes figyelési adatot elhelyez egy központi helyen, hogy a tevékenységnapló tárfiók használatával. A storage-fiók használata egy általános célú tárfiókkal, nem a blob storage-fiók kell lennie. A tárfiók nem kell ugyanazt az előfizetést, mint az előfizetés naplók kibocsátó mindaddig, amíg a beállítás konfigurálása felhasználó hozzáfér megfelelő RBAC mindkét előfizetéshez lehet.

## <a name="log-profile"></a>Napló-profil
Az alábbi módszerekkel történő tevékenységnapló archiválására, állítsa be a **napló profil** az előfizetéshez. A napló profil határozza meg az eseményeket, amelyek tárolt vagy a folyamatos átviteli és a kimenetek – tárolási fiók és/vagy az event hub. A storage-fiókban tárolt eseményeket az adatmegőrzési (a megőrizni kívánt napok száma) is meghatároz. Ha az adatmegőrzési nullára van beállítva, események határozatlan ideig tárolja. Ellenkező esetben ez állítható 1 és 2147483647 között bármilyen érték. Adatmegőrzési alkalmazott napi,, így napi (UTC) szerint naplókat, amelyik most már a megőrzési túl napjától végén házirend törlődni fog. Például ha egy nap adatmegőrzési, mai nap kezdetén a napló, a nap előtt tegnap törlése akkor történik meg. [Részletesebb naplófájl kapcsolatos profilok Itt](monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile). 

## <a name="archive-the-activity-log-using-the-portal"></a>A tevékenység naplót, a portál használatával
1. A portálon kattintson a **tevékenységnapló** a bal oldali navigációs hivatkozásra kattintva. Ha a tevékenység napló hivatkozás nem látható, kattintson a **több szolgáltatások** először hivatkozásra.
   
    ![Navigáljon a tevékenységnapló panel](media/monitoring-archive-activity-log/act-log-portal-navigate.png)
2. Kattintson a panel tetején **exportálása**.
   
    ![Kattintson az Exportálás gomb](media/monitoring-archive-activity-log/act-log-portal-export-button.png)
3. A megjelenő panelen, jelölje be a **tárfiókba exportálása** , és válasszon egy tárfiókot.
   
    ![A storage-fiók beállítása](media/monitoring-archive-activity-log/act-log-portal-export-blade.png)
4. A vagy a csúszka definiálja, amelynek tevékenységnapló események kell tartani a tárfiókban lévő napok számát. Ha szeretné adatait őrzi meg a tárfiók határozatlan ideig, ez az érték nullára.
5. Kattintson a **Save** (Mentés) gombra.

## <a name="archive-the-activity-log-via-powershell"></a>A műveletnapló PowerShell archiválása
```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus -RetentionInDays 180 -Categories Write,Delete,Action
```

| Tulajdonság | Szükséges | Leírás |
| --- | --- | --- |
| StorageAccountId |Nem |Erőforrás-azonosító a tárfiók tevékenységi naplóit mentésére. |
| Helyek |Igen |Régiók, amelynek szeretné tevékenységnapló eseményeinek gyűjtése vesszővel tagolt listája. Megtekintheti az összes régiók listáját [ezen a lapon felkeresésével](https://azure.microsoft.com/en-us/regions) vagy használatával [az Azure felügyeleti REST API](https://msdn.microsoft.com/library/azure/gg441293.aspx). |
| retentionInDays |Igen |Az eseményeket meg kell őrizni, 1 és 2147483647 közötti napok számát. A nulla érték a naplók határozatlan ideig tárolja (végtelen). |
| Kategóriák |Igen |Be kell esemény kategóriák vesszővel tagolt listája. Lehetséges értékek a következők: Olvasás, törlés és művelet. |

## <a name="archive-the-activity-log-via-cli"></a>A tevékenység naplót parancssori felület használatával
```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --locations global,westus,eastus,northeurope --retentionInDays 180 –categories Write,Delete,Action
```

| Tulajdonság | Szükséges | Leírás |
| --- | --- | --- |
| név |Igen |A napló profil neve. |
| storageId |Nem |Erőforrás-azonosító a tárfiók tevékenységi naplóit mentésére. |
| Helyek |Igen |Régiók, amelynek szeretné tevékenységnapló eseményeinek gyűjtése vesszővel tagolt listája. Megtekintheti az összes régiók listáját [ezen a lapon felkeresésével](https://azure.microsoft.com/en-us/regions) vagy használatával [az Azure felügyeleti REST API](https://msdn.microsoft.com/library/azure/gg441293.aspx). |
| retentionInDays |Igen |Az eseményeket meg kell őrizni, 1 és 2147483647 közötti napok számát. A nulla érték határozatlan ideig tárolja a naplók (végtelen). |
| Kategóriák |Igen |Be kell esemény kategóriák vesszővel tagolt listája. Lehetséges értékek a következők: Olvasás, törlés és művelet. |

## <a name="storage-schema-of-the-activity-log"></a>A műveletnapló tárolási séma
Miután állított be archiválási, tárolót létrejön a tárfiók, amint tevékenységnapló esemény következik be. A tárolóban található blobok kövesse ugyanazt a formátumot minden tevékenységnapló és diagnosztikai naplókat. A blobok szerkezete van:

> insights-működési-naplók/name = alapértelmezett/resourceId = / SUBSCRIPTIONS / {előfizetés-azonosító} / y = {négyjegyű numerikus year} / m = {kétjegyű numerikus month} / d {kétjegyű számozott napja} = / h = {kétjegyű 24 órás hour}/m=00/PT1H.json
> 
> 

A blob neve lehet, például:

> insights-Operational-Logs/Name=default/resourceId=/Subscriptions/s1id1234-5679-0123-4567-890123456789/y=2016/m=08/d=22/h=18/m=00/PT1H.JSON
> 
> 

Minden egyes PT1H.json blob tartalmaz a blob URL-címben megadott órán belül előforduló eseményeket a JSON blob (pl. h = 12). A jelenlegi órán belül események lesz hozzáfűzve a PT1H.json fájl azok bekövetkezésekor. A perc értéket (m = 00) mindig 00, mivel tevékenységnapló események óránként egyes blobok van felosztva.

PT1H.json fájlon belül mindegyik esemény tárolja a "rekordok" tömb, a következő formátumban:

```
{
    "records": [
        {
            "time": "2015-01-21T22:14:26.9792776Z",
            "resourceId": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
            "operationName": "microsoft.support/supporttickets/write",
            "category": "Write",
            "resultType": "Success",
            "resultSignature": "Succeeded.Created",
            "durationMs": 2826,
            "callerIpAddress": "111.111.111.11",
            "correlationId": "c776f9f4-36e5-4e0e-809b-c9b3c3fb62a8",
            "identity": {
                "authorization": {
                    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
                    "action": "microsoft.support/supporttickets/write",
                    "evidence": {
                        "role": "Subscription Admin"
                    }
                },
                "claims": {
                    "aud": "https://management.core.windows.net/",
                    "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
                    "iat": "1421876371",
                    "nbf": "1421876371",
                    "exp": "1421880271",
                    "ver": "1.0",
                    "http://schemas.microsoft.com/identity/claims/tenantid": "1e8d8218-c5e7-4578-9acc-9abbd5d23315 ",
                    "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
                    "puid": "20030000801A118C",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
                    "name": "John Smith",
                    "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
                    "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
                    "appidacr": "2",
                    "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
                    "http://schemas.microsoft.com/claims/authnclassreference": "1"
                }
            },
            "level": "Information",
            "location": "global",
            "properties": {
                "statusCode": "Created",
                "serviceRequestId": "50d5cddb-8ca0-47ad-9b80-6cde2207f97c"
            }
        }
    ]
}
```


| Elem neve | Leírás |
| --- | --- |
| time |Az esemény az esemény megfelelő a kérés feldolgozása az Azure-szolgáltatás által kiváltott idejét jelző időbélyegző. |
| resourceId |Erőforrás-azonosító az érintett erőforrás. |
| operationName |A művelet neve. |
| category |A művelet kategória eg. Írási, olvassa el, a műveletet. |
| resultType |Az eredmény típusú eg. Sikeres, sikertelen, kezdő |
| resultSignature |Az erőforrástípus függ. |
| durationMs |A művelet ezredmásodpercben időtartama |
| callerIpAddress |Felhasználó rendelkezik a művelet, a jogcím vagy a rendelkezésre állás alapján SPN jogcím IP-címe. |
| correlationId |Általában egy GUID Azonosítót a karakterlánc formátuma. Az eseményeket, amelyek megosztása a correlationId ugyanazon uber művelet tartozik. |
| identity |Az engedélyezési és a jogcímek leíró JSON-blob. |
| Engedélyezési |Az esemény tulajdonságainak RBAC BLOB. Az "action", "szerepkör" és "hatókör" Tulajdonságok általában tartalmazza. |
| szint |Az esemény szintje. A következő értékek egyikét: "Kritikus", "Error", "Figyelmeztetés", "Tájékoztató" és "Részletes" |
| location |A hely történt (vagy globális) régióban. |
| properties |Állítsa be a `<Key, Value>` az esemény részleteit leíró párok (azaz szótárában). |

> [!NOTE]
> A tulajdonságok és ezek a tulajdonságok használatát eltérőek lehetnek attól függően, hogy az erőforrás.
> 
> 

## <a name="next-steps"></a>Következő lépések
* [Elemzés blobok letöltése](../storage/blobs/storage-dotnet-how-to-use-blobs.md#download-blobs)
* [Az Event hubs tevékenységnapló adatfolyam](monitoring-stream-activity-logs-event-hubs.md)
* [További tudnivalók a műveletnapló](monitoring-overview-activity-logs.md)


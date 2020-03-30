---
title: Naplónaplók elérése és áttekintése
titleSuffix: Azure AD B2C
description: Az Azure AD B2C naplózási naplóinak elérése programozott módon és az Azure Portalon.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 02/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 83086fa2cb96eba423b9111134a0406d7256821f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264218"
---
# <a name="accessing-azure-ad-b2c-audit-logs"></a>Az Azure AD B2C naplózási naplóinak elérése

Az Azure Active Directory B2C (Azure AD B2C) naplót bocsát ki, amely a B2C-erőforrásokkal, a kiadott jogkivonatokkal és a rendszergazdai hozzáféréssel kapcsolatos tevékenységinformációkat tartalmaz. Ez a cikk rövid áttekintést nyújt a naplónaplókban elérhető információkról, és útmutatást nyújt az Azure AD B2C-bérlő adatainak eléréséhez.

A naplóesemények csak **hét napig**őrződnek meg . Tervezze meg a naplók letöltését és tárolását az alábbi módszerek egyikével, ha hosszabb megőrzési időszakra van szüksége.

> [!NOTE]
> Az Azure Active Directory vagy az Azure AD B2C-lapok Az **Azure-portál** **On-T in** s in-t az Azure Active Directory vagy az **Azure AD B2C-lapok** felhasználók szakaszában nem láthatja az egyes Azure AD B2C-alkalmazások felhasználói bejelentkezései. Az ottani bejelentkezési események felhasználói tevékenységet mutatnak, de nem korrelálhatók vissza azzal a B2C alkalmazással, amelybe a felhasználó bejelentkezett. Ehhez a naplónaplókat kell használnia, ahogy azt a cikk ben tovább kifejtette.

## <a name="overview-of-activities-available-in-the-b2c-category-of-audit-logs"></a>A naplónaplók B2C kategóriájában elérhető tevékenységek áttekintése

A naplóban lévő **B2C** kategória a következő típusú tevékenységeket tartalmazza:

|Tevékenység típusa |Leírás  |
|---------|---------|
|Engedélyezés |A felhasználó nak a B2C-erőforrásokhoz való hozzáférésengedélyezésével kapcsolatos tevékenységek (például a B2C-házirendek listájához hozzáférő rendszergazda).         |
|Címtár |Az Azure Portal használatával bejelentkező címtárattribútumokhoz kapcsolódó tevékenységek. |
|Alkalmazás | Létrehozása, olvasása, frissítése és törlése (CRUD) műveletek B2C alkalmazások. |
|Kulcs |CRUD-műveletek a B2C kulcstárolóban tárolt kulcsokon. |
|Erőforrás |CRUD műveletek B2C erőforrásokon. Például a házirendek és az identitásszolgáltatók.
|Hitelesítés |A felhasználói hitelesítő adatok és a jogkivonat-kiállítás érvényesítése.|

A CRUD felhasználói objektum tevékenységeiről a **Core Directory** kategóriában tájékozódhat.

## <a name="example-activity"></a>Példa tevékenységre

Ez a példakép az Azure Portalon jeleníti meg a rögzített adatokat, amikor egy felhasználó bejelentkezik egy külső identitásszolgáltató, ebben az esetben a Facebook:

![Példa a naplótevékenység részletei lapra az Azure Portalon](./media/view-audit-logs/audit-logs-example.png)

A tevékenység részletei panel a következő releváns információkat tartalmazza:

|Section|Mező|Leírás|
|-------|-----|-----------|
| Tevékenység | Név | Melyik tevékenység zajlott le. Például *adjon ki egy id_token az alkalmazásnak,* amely a tényleges felhasználói bejelentkezést zárja. |
| Kezdeményezte (aktor) | ObjectId | Annak a B2C alkalmazásnak az **objektumazonosítója,** amelybe a felhasználó bejelentkezik. Ez az azonosító nem látható az Azure Portalon, de elérhető a Microsoft Graph API-n keresztül. |
| Kezdeményezte (aktor) | Sz. | Annak a B2C alkalmazásnak az **alkalmazásazonosítója,** amelybe a felhasználó bejelentkezik. |
| Cél(ok) | ObjectId | A bejelentkező felhasználó **objektumazonosítója.** |
| További részletek | TenantId | Az Azure AD B2C-bérlő **bérlői** azonosítója. |
| További részletek | PolicyId | A felhasználó bejelentkezéséhez használt felhasználói folyamat (házirend) **házirend-azonosítója.** |
| További részletek | ApplicationId | Annak a B2C alkalmazásnak az **alkalmazásazonosítója,** amelybe a felhasználó bejelentkezik. |

## <a name="view-audit-logs-in-the-azure-portal"></a>Naplónaplók megtekintése az Azure Portalon

Az Azure Portal hozzáférést biztosít a naplózási események az Azure AD B2C bérlőben.

1. Bejelentkezés az [Azure Portalra](https://portal.azure.com)
1. Váltson az Azure AD B2C-bérlőt tartalmazó könyvtárra, majd keresse meg az **Azure AD B2C -t.**
1. A bal oldali menü **Tevékenységek** területén válassza a **Naplók naplózása**lehetőséget.

Megjelenik az elmúlt hét napban naplózott tevékenységesemények listája.

![Példaszűrő két tevékenységi eseményt az Azure Portalon](./media/view-audit-logs/audit-logs-example-filter.png)

Számos szűrési lehetőség áll rendelkezésre, többek között:

* **Tevékenységerőforrás típusa** – Szűrés az elérhető tevékenységek [áttekintése](#overview-of-activities-available-in-the-b2c-category-of-audit-logs) szakaszban a táblázatban látható tevékenységtípusok szerint.
* **Dátum** – a megjelenített tevékenységek dátumtartományának szűrése.

Ha kijelöl egy sort a listában, az esemény tevékenységrészletei jelennek meg.

Ha a tevékenységesemények listáját vesszővel tagolt értékfájlban (CSV) szeretné letölteni, válassza a **Letöltés**gombot.

## <a name="get-audit-logs-with-the-azure-ad-reporting-api"></a>Naplózási naplók beszereznie az Azure AD jelentéskészítési API-val

A naplónaplók ugyanannál a folyamatnál vannak közzétéve, mint az Azure Active Directory egyéb tevékenységei, így az [Azure Active Directory jelentéskészítési API-n](https://docs.microsoft.com/graph/api/directoryaudit-list)keresztül érhetők el. További információ: [Az Azure Active Directory jelentéskészítési API– ismerkedés.](../active-directory/reports-monitoring/concept-reporting-api.md)

### <a name="enable-reporting-api-access"></a>Jelentéskészítési API-hozzáférés engedélyezése

Ahhoz, hogy script- vagy alkalmazásalapú hozzáférést az Azure AD jelentéskészítési API-hoz, szüksége van egy alkalmazás regisztrálva az Azure AD B2C-bérlő a következő API-engedélyekkel. Ezeket az engedélyeket a B2C-bérlőn belüli meglévő alkalmazásregisztráción engedélyezheti, vagy létrehozhat egy újat kifejezetten a naplóautomatizáláshoz.

* A Microsoft Graph alkalmazásengedélyeket > > az AuditLog > az AuditLog.Read.Read fájlhoz.

Az alábbi cikkben ismertetett lépéseket követve regisztrálhat egy alkalmazást a szükséges engedélyekkel:

[Az Azure AD B2C kezelése a Microsoft Graph segítségével](microsoft-graph-get-started.md)

Miután regisztrált egy alkalmazást a megfelelő engedélyekkel, tekintse meg a PowerShell-parancsfájl szakasz a cikk későbbi részében egy példát, hogyan kaphat tevékenységesemények et egy parancsfájlt.

### <a name="access-the-api"></a>Az API elérése

Az Azure AD B2C naplózási naplóeseményeinek az API-n keresztüli letöltéséhez szűrje a `B2C` kategória naplóit. Kategória szerinti szűréshez `filter` használja a lekérdezési karakterlánc paramétert az Azure AD jelentéskészítési API-végpont jának meghívásakor.

```HTTP
https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?$filter=loggedByService eq 'B2C' and activityDateTime gt 2019-09-10T02:28:17Z
```

### <a name="powershell-script"></a>PowerShell-szkript

A következő PowerShell-parancsfájl bemutatja az Azure AD jelentéskészítési API lekérdezésének példáját. Az API lekérdezése után kinyomtatja a naplózott eseményeket a szabványos kimenetre, majd a JSON-kimenetet egy fájlba írja.

Ezt a parancsfájlt az [Azure Cloud Shell](overview.md)ben próbálhatja ki. Győződjön meg róla, hogy frissítse azt az alkalmazásazonosítóval, az ügyféltitkos adattal és az Azure AD B2C-bérlő nevével.

```powershell
# This script requires an application registration that's granted Microsoft Graph API permission
# https://docs.microsoft.com/azure/active-directory-b2c/microsoft-graph-get-started

# Constants
$ClientID       = "your-client-application-id-here"       # Insert your application's client ID, a GUID
$ClientSecret   = "your-client-application-secret-here"   # Insert your application's client secret
$tenantdomain   = "your-b2c-tenant.onmicrosoft.com"       # Insert your Azure AD B2C tenant domain name

$loginURL       = "https://login.microsoftonline.com"
$resource       = "https://graph.microsoft.com"           # Microsoft Graph API resource URI
$7daysago       = "{0:s}" -f (get-date).AddDays(-7) + "Z" # Use 'AddMinutes(-5)' to decrement minutes, for example
Write-Output "Searching for events starting $7daysago"

# Create HTTP header, get an OAuth2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

# Parse audit report items, save output to file(s): auditX.json, where X = 0 through n for number of nextLink pages
if ($oauth.access_token -ne $null) {
    $i=0
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    $url = "https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?`$filter=loggedByService eq 'B2C' and activityDateTime gt  " + $7daysago

    # loop through each query page (1 through n)
    Do {
        # display each event on the console window
        Write-Output "Fetching data using Uri: $url"
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output ($event | ConvertTo-Json)
        }

        # save the query page to an output file
        Write-Output "Save the output to a file audit$i.json"
        $myReport.Content | Out-File -FilePath audit$i.json -Force
        $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
        $i = $i+1
    } while($url -ne $null)
} else {
    Write-Host "ERROR: No Access Token"
}
```

Itt a JSON ábrázolása a példa tevékenység esemény korábban látható a cikkben:

```JSON
{
    "id": "B2C_DQO3J_4984536",
    "category": "Authentication",
    "correlationId": "00000000-0000-0000-0000-000000000000",
    "result": "success",
    "resultReason": "N/A",
    "activityDisplayName": "Issue an id_token to the application",
    "activityDateTime": "2019-09-14T18:13:17.0618117Z",
    "loggedByService": "B2C",
    "operationType": "",
    "initiatedBy": {
        "user": null,
        "app": {
            "appId": "00000000-0000-0000-0000-000000000000",
            "displayName": null,
            "servicePrincipalId": null,
            "servicePrincipalName": "00000000-0000-0000-0000-000000000000"
        }
    },
    "targetResources": [
        {
            "id": "00000000-0000-0000-0000-000000000000",
            "displayName": null,
            "type": "User",
            "userPrincipalName": null,
            "groupType": null,
            "modifiedProperties": []
        }
    ],
    "additionalDetails": [
        {
            "key": "TenantId",
            "value": "test.onmicrosoft.com"
        },
        {
            "key": "PolicyId",
            "value": "B2C_1A_signup_signin"
        },
        {
            "key": "ApplicationId",
            "value": "00000000-0000-0000-0000-000000000000"
        },
        {
            "key": "Client",
            "value": "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.132 Safari/537.36"
        },
        {
            "key": "IdentityProviderName",
            "value": "facebook"
        },
        {
            "key": "IdentityProviderApplicationId",
            "value": "0000000000000000"
        },
        {
            "key": "ClientIpAddress",
            "value": "127.0.0.1"
        }
    ]
}
```

## <a name="next-steps"></a>További lépések

Automatizálhatja az egyéb felügyeleti feladatokat, például [kezelheti az Azure AD B2C felhasználói fiókokat a Microsoft Graph segítségével.](manage-user-accounts-graph-api.md)

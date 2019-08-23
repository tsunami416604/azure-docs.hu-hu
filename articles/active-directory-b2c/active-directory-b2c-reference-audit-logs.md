---
title: Naplók Azure Active Directory B2Cban szereplő minták és definíciók naplózása | Microsoft Docs
description: Útmutató és példák a Azure AD B2C naplók eléréséhez.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 08/04/2017
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: d8cc67b8e243fb2b97cd1522a850adc63c84428e
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69969633"
---
# <a name="accessing-azure-ad-b2c-audit-logs"></a>Azure AD B2C naplók elérése

A Azure Active Directory B2C (Azure AD B2C) a B2C-erőforrásokkal, a kiadott jogkivonatokkal és a rendszergazdai hozzáféréssel kapcsolatos tevékenységeket tartalmazó naplókat bocsát ki. Ez a cikk röviden áttekinti a naplókban elérhető információkat, és leírja, hogyan érheti el ezeket az adatokat a Azure AD B2C bérlőhöz.

> [!IMPORTANT]
> A naplókat csak hét napig őrzi meg a rendszer. Tervezze meg a naplók letöltését és tárolását az alább látható módszerek valamelyikével, ha hosszabb megőrzési időtartamra van szüksége.

> [!NOTE]
> Az egyes Azure AD B2C alkalmazásokhoz tartozó felhasználói bejelentkezések nem jelennek meg a **Azure Active Directory** vagy a **Azure ad B2C** -lapok **felhasználók** szakaszában. A bejelentkezések felhasználói tevékenységet jelenítenek meg, de nem kapcsolhatók vissza arra a B2C-alkalmazásra, amelyhez a felhasználó bejelentkezett. Az ehhez szükséges naplókat a jelen cikkben ismertetett módon kell használni.

## <a name="overview-of-activities-available-in-the-b2c-category-of-audit-logs"></a>A B2C-kategóriákban elérhető tevékenységek áttekintése
A naplókban a **B2C** kategória a következő típusú tevékenységeket tartalmazza:

|Tevékenységtípus |Leírás  |
|---------|---------|
|Authorization |A B2C-erőforrások elérésére irányuló felhasználók engedélyezésével kapcsolatos tevékenységek (például egy, a B2C-szabályzatok listáját elérő rendszergazda)         |
|Címtár |Címtár-attribútumokkal kapcsolatos tevékenységek, amelyek akkor jelentkeznek le, amikor egy rendszergazda bejelentkezik a Azure Portal használatával |
|Alkalmazás | A B2C-alkalmazásokkal kapcsolatos szifilisz-műveletek |
|Kulcs |A B2C-kulcstárolóban tárolt kulcsokkal kapcsolatos szifilisz-műveletek |
|Resource |A B2C-erőforrásokkal kapcsolatos szifilisz-műveletek (például házirendek és identitás-szolgáltatók)
|Authentication |A felhasználói hitelesítő adatok és a jogkivonatok kiállításának érvényesítése|

> [!NOTE]
> A felhasználói objektumhoz tartozó szifilisz-tevékenységek esetében tekintse meg az **alapvető könyvtár** kategóriát.

## <a name="example-activity"></a>Példa tevékenységre
Az alábbi példa azt mutatja be, hogy milyen adatok vannak rögzítve, amikor a felhasználó bejelentkezik egy külső identitás-szolgáltatóval:  
    ![Példa a naplózási tevékenység részleteinek oldalára Azure Portal](./media/active-directory-b2c-reference-audit-logs/audit-logs-example.png)

A tevékenység részletei panel a következő releváns információkat tartalmazza:

|`Section`|Mező|Leírás|
|-------|-----|-----------|
| Tevékenység | Name (Név) | Melyik tevékenység történt. Például: "id_token kiküldése az alkalmazásnak" (amely a tényleges felhasználói bejelentkezést vonja le). |
| Kezdeményező (színész) | ObjectId | Annak a B2C-alkalmazásnak az **azonosítója** , amelyhez a felhasználó bejelentkezik (ez az azonosító nem látható a Azure Portalban, de a Graph API keresztül érhető el). |
| Kezdeményező (színész) | Egyszerű szolgáltatásnév | Annak a B2C-alkalmazásnak az **azonosítója** , amelyhez a felhasználó bejelentkezik. |
| Cél (ok) | ObjectId | A bejelentkezett felhasználó **objektumazonosító** . |
| További részletek | TenantId | A Azure AD B2C bérlő **bérlői azonosítója** . |
| További részletek | `PolicyId` | Annak a felhasználói folyamatnak (házirendnek) a **házirend-azonosítója** , amellyel a rendszer aláírja a felhasználót a alkalmazásban. |
| További részletek | ApplicationId | Annak a B2C-alkalmazásnak az **azonosítója** , amelyhez a felhasználó bejelentkezik. |

## <a name="accessing-audit-logs-through-the-azure-portal"></a>Naplózási naplók elérése a Azure Portal
1. Nyissa meg az [Azure Portal](https://portal.azure.com). Győződjön meg arról, hogy a B2C-címtárban van.
2. Kattintson a bal oldali Kedvencek sáv **Azure Active Directory** elemére.

    ![A bal oldali portál menüjében Kiemelt Azure Active Directory gomb](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-aad.png)

1. A **tevékenység**területen kattintson a **naplók** elemre.

    ![Naplók gomb kiemelve a menü tevékenység szakaszában](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-section.png)

2. A Dropbox **kategóriában** válassza a **B2C** elemet.
3. Kattintson az **alkalmaz** gombra

    ![Kategória és alkalmaz gomb kiemelve a naplózási napló szűrőben](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-category.png)

Az elmúlt hét napban naplózott tevékenységek listáját fogja látni.
- A **tevékenység** típusa legördülő menü használatával szűrheti a fentiekben leírt tevékenységtípusok szerinti szűrést.
- A **dátumtartomány** legördülő lista használatával szűrheti a megjelenített tevékenységek dátumtartományt
- Ha a lista egy adott sorára kattint, a jobb oldali kontextus mező a tevékenységhez kapcsolódó további attribútumokat jelenít meg.
- Kattintson a **Letöltés** gombra a tevékenységek CSV-fájlként való letöltéséhez

> [!NOTE]
> A naplókat úgy is megtekintheti, hogy a bal oldali Kedvencek sávon **Azure Active Directory** helyett a **Azure ad B2C** navigál. A **tevékenységek**területen kattintson a **naplók**elemre, ahol ugyanazok a naplók találhatók, mint a hasonló szűrési képességekkel.

## <a name="accessing-audit-logs-through-the-azure-ad-reporting-api"></a>Naplók elérése az Azure AD Reporting API-val
A naplók ugyanazon a folyamaton lesznek közzétéve, mint a Azure Active Directory egyéb tevékenységei, így azok a [Azure Active Directory jelentési API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-audit-reference)-n keresztül érhetők el.

### <a name="prerequisites"></a>Előfeltételek
Az Azure AD Reporting API-ban való hitelesítéshez először regisztrálnia kell egy alkalmazást. Ügyeljen arra, hogy kövesse az [Előfeltételek az Azure ad Reporting API](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/)-k eléréséhez című témakör lépéseit.

### <a name="accessing-the-api"></a>Az API elérése
Ha az API-n keresztül szeretné letölteni a Azure AD B2C naplókat, a naplókat a **B2C** kategóriába kell szűrnie. A kategória szerinti szűréshez használja a lekérdezési karakterlánc paramétert az Azure AD Reporting API-végpont meghívásakor, az alábbi ábrán látható módon:

`https://graph.windows.net/your-b2c-tentant.onmicrosoft.com/activities/audit?api-version=beta&$filter=category eq 'B2C'`

### <a name="powershell-script"></a>PowerShell-szkript
Az alábbi parancsfájl egy példát mutat be arra, hogyan lehet a PowerShell használatával lekérdezni az Azure AD Reporting API-t, és hogyan tárolhatja az eredményeket JSON-fájlként:

```powershell
# This script will require registration of a Web Application in Azure Active Directory (see https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/)

# Constants
$ClientID       = "your-client-application-id-here"       # Insert your application's Client ID, a Globally Unique ID (registered by Global Admin)
$ClientSecret   = "your-client-application-secret-here"   # Insert your application's Client Key/Secret string
$loginURL       = "https://login.microsoftonline.com"
$tenantdomain   = "your-b2c-tenant.onmicrosoft.com"       # AAD B2C Tenant; for example, contoso.onmicrosoft.com
$resource       = "https://graph.windows.net"             # Azure AD Graph API resource URI
$7daysago       = "{0:s}" -f (get-date).AddDays(-7) + "Z" # Use 'AddMinutes(-5)' to decrement minutes, for example
Write-Output "Searching for events starting $7daysago"

# Create HTTP header, get an OAuth2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

# Parse audit report items, save output to file(s): auditX.json, where X = 0 thru n for number of nextLink pages
if ($oauth.access_token -ne $null) {
    $i=0
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    $url = 'https://graph.windows.net/' + $tenantdomain + '/activities/audit?api-version=beta&$filter=category eq ''B2C''and activityDate gt ' + $7daysago

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

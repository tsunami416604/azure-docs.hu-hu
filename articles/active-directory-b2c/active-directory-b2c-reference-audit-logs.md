---
title: Naplózási minták és a definíciók bejelentkezik az Azure Active Directory B2C |} A Microsoft Docs
description: Útmutató és -példák az Azure AD B2C-naplók elérése a.
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
ms.openlocfilehash: 2c1bfd9e2659127ab77e9db661b54fde18a8d25c
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67205366"
---
# <a name="accessing-azure-ad-b2c-audit-logs"></a>Auditnaplók eléréséhez az Azure AD B2C-vel

Az Azure Active Directory B2C (Azure AD B2C-vel) tartalmazó rendszertevékenység információk a B2C-erőforrás kiadott jogkivonatokat, és a rendszergazdai hozzáférés auditnaplók bocsát ki. Ez a cikk az információ a vizsgálati naplók és utasításokat rövid áttekintést nyújt az Azure AD B2C-bérlő számára az adatok elérésével.

> [!IMPORTANT]
> Auditnaplók csak megmaradnak a hét napja. Tervezze meg, töltse le és tárolja a naplókat az alább látható, ha szüksége van-e a hosszabb adatmegőrzési idő módszerek egyikének használatával.

> [!NOTE]
> Felhasználói bejelentkezések a az egyes Azure AD B2C-alkalmazások nem látja a **felhasználók** szakaszában a **Azure Active Directory** vagy **Azure AD B2C-vel** paneljein. A bejelentkezések nincs felhasználói tevékenység megjelenik, de nem lehet visszamenőleges korrelációban állnak a B2C-alkalmazást, amely a felhasználó bejelentkezett. A vizsgálati naplók kell használnia, amint ez a cikk további azt.

## <a name="overview-of-activities-available-in-the-b2c-category-of-audit-logs"></a>A naplók a B2C kategóriában elérhető tevékenységek áttekintése
A **B2C** kategória-naplók az alábbi típusú tevékenységeket tartalmazza:

|Tevékenység típusa |Leírás  |
|---------|---------|
|Engedélyezés |Az erőforrások eléréséhez B2C felhasználói engedély vonatkozó (például egy rendszergazda fér hozzá a B2C-szabályzatok listája) tevékenységek         |
|Címtár |Directory-attribútumok kérhető le, ha rendszergazdaként jelentkezik be az Azure Portallal kapcsolatos tevékenységek |
|Alkalmazás | A B2C-alkalmazások CRUD-műveletek |
|Kulcs |A B2C-kulcstároló tárolt kulcsok CRUD-műveletek |
|Resource |B2C-erőforrásokat (például a szabályzatok és az identitás-szolgáltatóktól) CRUD-műveletek
|Hitelesítés |Felhasználói hitelesítő adatok és a kiállítási érvényesítése|

> [!NOTE]
> A felhasználói objektum CRUD tevékenységhez, tekintse meg a **alapvető könyvtár** kategória.

## <a name="example-activity"></a>Példa tevékenység
Az alábbi példában látható, amikor egy felhasználó jelentkezik be egy külső identitásszolgáltatónak rögzített adatokat: ![Auditnaplók – példa](./media/active-directory-b2c-reference-audit-logs/audit-logs-example.png)

A tevékenység részletei panelen a következő vonatkozó információkat tartalmazza:

|`Section`|Mező|Leírás|
|-------|-----|-----------|
| Tevékenység | Name (Név) | Milyen tevékenység került sor. Például "Id_token kiadása az alkalmazásnak" (amely arra a következtetésre jut a tényleges felhasználói bejelentkezési). |
| Kezdeményező (szereplő) | ObjectId | A **Objektumazonosító** , a B2C-alkalmazást, hogy a felhasználó bejelentkezik (Ez az azonosító nem látható az Azure Portalon de érhető el a Graph API-n keresztül például). |
| Kezdeményező (szereplő) | Spn | A **Alkalmazásazonosító** , a B2C-alkalmazást, amely a felhasználó jelentkezik be. |
| Cél(ok) | ObjectId | A **Objektumazonosító** felhasználó jelentkezik be. |
| További részletek | TenantId | A **Bérlőazonosító** az Azure AD B2C-bérlő. |
| További részletek | `PolicyId` | A **házirend-azonosító** használt beléptetni a felhasználót a felhasználói folyamat (házirend). |
| További részletek | Alkalmazásazonosító | A **Alkalmazásazonosító** , a B2C-alkalmazást, amely a felhasználó jelentkezik be. |

## <a name="accessing-audit-logs-through-the-azure-portal"></a>Az Azure Portalon keresztül éri el a vizsgálati naplók
1. Nyissa meg az [Azure Portal](https://portal.azure.com). Győződjön meg róla, hogy a B2C-címtárban.
2. Kattintson a **Azure Active Directory** a Kedvencek sávra a bal oldalon található
    
    ![Auditnaplók – AAD gomb](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-aad.png)

1. A **tevékenység**, kattintson a **Auditnaplók**

    ![Auditnaplók – naplók szakasz](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-section.png)

2. Az a **kategória** dropbox válassza **B2C-vel**
3. Kattintson a **alkalmazása**

    ![Auditnaplók – kategória](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-category.png)

Látni fogja az elmúlt hét napban naplózott tevékenységek listája.
- Használja a **tevékenység erőforrástípusa** legördülő menüben szűrhet a fent vázolt tevékenységtípusok
- Használja a **dátumtartomány** legördülő menü jelenik meg a tevékenységek dátumtartománya szűrése
- Kattint egy adott sorra a listában, ha egy környezetfüggő a jobb oldali mezőbe megjelenik a tevékenység társított további attribútumok
- Kattintson a **letöltése** tevékenységek letöltése csv-fájlként

> [!NOTE]
> Emellett megtekintheti az auditnaplók az **Azure AD B2C-vel** helyett **Azure Active Directory** a Kedvencek sávra a bal oldalon található. A **tevékenységek**, kattintson a **Auditnaplók**, ahol megtalálja a hasonló szűrési képességek azonos naplók.

## <a name="accessing-audit-logs-through-the-azure-ad-reporting-api"></a>Az Azure AD reporting API keresztül éri el a vizsgálati naplók
Auditnaplók más tevékenységek azonos folyamatra számára közzétett Azure Active Directoryban, így azok keresztül érhetők el a [Azure Active Directory reporting API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-audit-reference).

### <a name="prerequisites"></a>Előfeltételek
Az Azure ad reporting API hitelesítésére először kell regisztrálni egy alkalmazást. Ügyeljen arra, hogy kövesse a [az Azure AD reporting API elérésének előfeltételeit](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/).

### <a name="accessing-the-api"></a>Az API elérése
Az API-n keresztül az Azure AD B2C-naplók letöltéséhez, a naplók szűrése szeretné a **B2C** kategória. Szűrés kategória szerint, használja a lekérdezési karakterlánc paramétereként az Azure AD reporting API-végpont hívásakor, ahogy az alábbi:

`https://graph.windows.net/your-b2c-tentant.onmicrosoft.com/activities/audit?api-version=beta&$filter=category eq 'B2C'`

### <a name="powershell-script"></a>PowerShell-szkript
Az alábbi parancsprogram azt szemlélteti, lekérdezése az Azure AD reporting API és az eredményeket JSON-fájlként tárolja a PowerShell segítségével:

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

---
title: Naplók az mintákat és meghatározások az Azure Active Directory B2C |} Microsoft Docs
description: Útmutató és minták a naplókat az Azure AD B2C eléréséhez.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.date: 08/04/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 4828bf2f0faa596c8222c3a36dc3d38ec1b3bd1a
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34709818"
---
# <a name="accessing-azure-ad-b2c-audit-logs"></a>Hozzáférés az Azure AD B2C-naplók

Az Azure Active Directory B2C (az Azure AD B2C) B2C erőforrások kiadott jogkivonatokat, és a rendszergazdai hozzáférés tevékenység adatait tartalmazó naplófájlok bocsát ki. Ez a cikk az információ a vizsgálati naplók és utasításokat rövid áttekintést nyújt a elérésével ezeket az adatokat az Azure AD B2C-bérlő.

> [!IMPORTANT]
> Naplók csak megmaradnak a hét napja. Tervezze meg letöltéséhez és az alább látható, ha szüksége van-e a hosszabb megőrzési időtartam módszerek egyikét használva a naplók tárolásához. 

##<a name="overview-of-activities-available-in-the-b2c-category-of-audit-logs"></a>A vizsgálati naplók B2C kategóriáját tevékenységeit áttekintése
A **B2C** a vizsgálati naplók kategória az alábbi típusú tevékenységeket tartalmazza:
|Tevékenység típusa |Leírás  |
|---------|---------|
|Engedélyezés |Tevékenységek engedélyezéséről a felhasználó B2C hozzáférését az erőforrásokhoz (például egy rendszergazda B2C házirendek listájának elérése)         |
|Címtár |Attribútumok beolvasni, ha rendszergazdaként jelentkezik be az Azure portál használatával kapcsolatos tevékenységek |
|Alkalmazás | CRUD-műveleteknek a B2C-alkalmazásokhoz |
|Kulcs |CRUD-műveleteknek a B2C kulcstároló tárolt kulcsok |
|Erőforrás |CRUD-műveleteknek a B2C-erőforrások (például a házirendek és az identitás-szolgáltatóktól)
|Hitelesítés |Felhasználói hitelesítő adatok és a hitelesítési karakterláncok kiállításához érvényesítése|

> [!NOTE]
> A felhasználói objektum CRUD tevékenységhez, tekintse meg a **Core Directory** kategóriát.

##<a name="example-activity"></a>Példa tevékenység
Az alábbi példában látható az adatokat, amikor egy felhasználó jelentkezik be egy külső identitásszolgáltatótól rögzített: ![naplók – példa](./media/active-directory-b2c-reference-audit-logs/audit-logs-example.png)

##<a name="accessing-audit-logs-through-the-azure-portal"></a>Naplók elérése az Azure portálon keresztül
1. Nyissa meg az [Azure Portal](https://portal.azure.com). Ellenőrizze, hogy a B2C-címtárban.
2. Kattintson a **Azure Active Directory** a Kedvencek sávon a bal oldali 
    
    ![A naplók - AAD gomb](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-aad.png)

1. A **tevékenység**, kattintson a **naplók**

    ![A naplók - naplók szakasz](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-section.png)

2. Az a **kategória** dropbox, jelölje be **B2C**
3. Kattintson a **alkalmazása**

    ![A naplók - kategória](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-category.png)

Az elmúlt hét napban naplózott tevékenységek listája jelenik meg. 
- Használja a **tevékenység erőforrástípus** legördülő lista a fent vázolt tevékenység típusok szűrése
- Használja a **dátumtartomány** legördülő látható tevékenységek dátumtartományát szűrése
- Ha a lista adott sorában kattint, környezetfüggő be a jobb oldali megjelenik a tevékenységhez társított további attribútumok
- Kattintson a **letöltése** tevékenységek letöltése csv-fájlként

##<a name="accessing-audit-logs-through-the-azure-ad-reporting-api"></a>Az Azure AD reporting API naplókat el
Naplók közzétett más tevékenységként azonos folyamat az Azure Active Directory, így azok a következőkkel érhetők el a [Azure Active Directory reporting API-val](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-api-audit-reference). 

###<a name="prerequisites"></a>Előfeltételek
Az Azure AD reporting API felé történő hitelesítésre először regisztrálnia kell az alkalmazás. Ügyeljen arra, hogy kövesse a [Előfeltételek az Azure AD reporting API-k elérésére](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/).

###<a name="accesing-the-api"></a>Az API-t egypéldányú
Az API-n keresztül az Azure AD B2C naplókat letölteni, érdemes a naplók szűrése a **B2C** kategóriát. Szűrés kategória szerint, használja a lekérdezési karakterláncot az Azure AD reporting API-végpont meghívásakor, alább látható módon:

`https://graph.windows.net/your-b2c-tentant.onmicrosoft.com/activities/audit?api-version=beta&$filter=category eq 'B2C'`

###<a name="powershell-script"></a>PowerShell-szkript
A következő parancsfájl egy példát lekérdezése az Azure AD reporting API és az eredmény egy JSON-fájlba a PowerShell használatával:

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
    Do{
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


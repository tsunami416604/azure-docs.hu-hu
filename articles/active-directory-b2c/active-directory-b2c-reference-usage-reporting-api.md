---
title: Használati jelentéskészítési API mintákat, és az Azure Active Directory B2C definíciók |} A Microsoft Docs
description: Útmutató és a példák a jelentések az Azure AD B2C-bérlőben a felhasználók, hitelesítés és többtényezős hitelesítés.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 08/04/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 544b0618f9135b684846c42bb7edeb37cf599883
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37445534"
---
# <a name="accessing-usage-reports-in-azure-ad-b2c-via-the-reporting-api"></a>A jelentéskészítési API-n keresztül az Azure AD B2C-ben a használati jelentések elérése

Az Azure Active Directory B2C (Azure AD B2C-vel) alapján a felhasználó be- és Azure multi-factor Authentication hitelesítést nyújt. Hitelesítését a végfelhasználók számára az alkalmazás termékcsalád Identitásszolgáltatók között. Ha ismeri a típus szerint a bérlő, a szolgáltatók regisztrálása használják és hitelesítések számát a regisztrált felhasználók száma, a válasz hasonló kérdésekre:
* A különböző típusú identitásszolgáltató (például egy Microsoft- vagy LinkedIn fiókkal), hogy hány felhasználó regisztrálta az elmúlt 10 napban?
* Hány hitelesítések multi-factor Authentication szolgáltatás használatával sikeresen lefutott-e az elmúlt hónapban?
* Hány sign-az-alapú hitelesítések hajtottuk végre ebben a hónapban? Naponta? Alkalmazásonkénti?
* Hogyan tudja becsülni a saját Azure AD B2C-bérlő tevékenység várható havi költségét?

Ez a cikk a számlázási tevékenységet, amely alapján a felhasználók, számlázható sign-az-alapú hitelesítések számát, és a multi-factor Authentication hitelesítések kötött jelentéseket összpontosít.


## <a name="prerequisites"></a>Előfeltételek
Mielőtt elkezdené, a lépések elvégzéséhez szüksége [az Azure AD reporting API elérésének előfeltételeit](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/). Hozzon létre egy alkalmazást, a hozzá tartozó titkos kulcs beszerzése és a hozzáférés biztosítása az Azure AD B2C-bérlő jelentések jogosultsággal. *Bash-szkript* és *Python-szkriptet* példákat itt is biztosítja. 

## <a name="powershell-script"></a>PowerShell-szkript
Ez a szkript használatával mutatja be a négy használati jelentés létrehozása a `TimeStamp` paraméter és a `ApplicationId` szűrő.

```powershell
# This script will require the Web Application and permissions setup in Azure Active Directory

# Constants
$ClientID      = "your-client-application-id-here"  
$ClientSecret  = "your-client-application-secret-here"
$loginURL      = "https://login.microsoftonline.com"
$tenantdomain  = "your-b2c-tenant-domain.onmicrosoft.com"  
# Get an Oauth 2 access token based on client id, secret and tenant domain
$body          = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth         = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body
if ($oauth.access_token -ne $null) {
    $headerParams  = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

    Write-host Data from the tenantUserCount report
    Write-host ====================================================
     # Returns a JSON document for the report
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/tenantUserCount?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the tenantUserCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/tenantUserCount?%24filter=TimeStamp+gt+2016-10-15&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCountSummary report
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCountSummary?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCount?%24filter=TimeStamp+gt+2016-09-20+and+TimeStamp+lt+2016-10-03&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCount report with ApplicationId filter
    Write-host ====================================================
    # Returns a JSON document for the " " report
        $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCount?%24filter=ApplicationId+eq+ada78934-a6da-4e69-b816-10de0d79db1d&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCountSummary
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCountSummary?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCount?%24filter=TimeStamp+gt+2016-09-10+and+TimeStamp+lt+2016-10-04&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCount report with ApplicationId filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCountSummary?%24filter=ApplicationId+eq+ada78934-a6da-4e69-b816-10de0d79db1d&api-version=beta")
     Write-host $myReport.Content

} else {
    Write-Host "ERROR: No Access Token"
    }
```


## <a name="usage-report-definitions"></a>Használati jelentés definíciók
* **tenantUserCount**: identitásszolgáltató naponta az elmúlt 30 napban típus szerint a bérlőben található felhasználók száma. (Ha szükséges, egy `TimeStamp` szűrő felhasználószám a megadott dátum az aktuális dátum biztosítja). A jelentés a következőket tartalmazza:
  * **TotalUserCount**: az összes felhasználói objektumok száma.
  * **OtherUserCount**: Azure Active Directory-felhasználók (nem Azure AD B2C-felhasználókat).
  * **LocalUserCount**: az Azure AD B2C felhasználói fiókok száma létrehozott hitelesítő adatokkal rendelkező helyi, az Azure AD B2C-bérlőben.

* **AlternateIdUserCount**: külső Identitásszolgáltatók regisztrált Azure AD B2C felhasználók száma (például Facebook, a Microsoft-fiókkal vagy egy másik Azure Active Directory-bérlővel, más néven egy `OrgId`).

* **b2cAuthenticationCountSummary**: a napi száma az elmúlt 30 nap, napi és a hitelesítési folyamat számlázható hitelesítések összefoglalása.

* **b2cAuthenticationCount**: időn belül hitelesítések számát. Az alapértelmezett érték az elmúlt 30 napban.  (Nem kötelező: kezdete és vége `TimeStamp` paraméterek meghatározása egy adott időszakban.) A parancs kimenete `StartTimeStamp` (ezen a bérlőn tevékenység legkorábbi dátum) és `EndTimeStamp` (legújabb frissítés).

* **b2cMfaRequestCountSummary**: Összegzés a multi-factor Authentication hitelesítést, nap, és a típusa (SMS vagy szóbeli) napi számát.


## <a name="limitations"></a>Korlátozások
Felhasználók száma az adatok 24-48 óránként frissülnek. Hitelesítések frissülnek naponta több alkalommal. Használatakor a `ApplicationId` szűrőt, a jelentés üres választ lehet miatt a következő feltételek valamelyike:
  * Az alkalmazás azonosítója nem létezik a bérlőben. Győződjön meg arról, hogy helyes-e.
  * Az alkalmazás azonosítója létezik, de nincs adat a jelentési időszak nem található. Ellenőrizze a dátum/idő paramétereket.


## <a name="next-steps"></a>További lépések
### <a name="monthly-bill-estimates-for-azure-ad"></a>Havi számla becslése az Azure ad-hez
Kombinálva [a legújabb Azure AD B2C díjszabása elérhető](https://azure.microsoft.com/pricing/details/active-directory-b2c/), meg tudja becsülni a napi, heti és havi Azure-használat.  Becsült különösen akkor hasznos, ha a bérlő működését, ami hatással lehet a teljes költségeinek tervezése. A tényleges költségek áttekintheti a [Azure-előfizetéshez társított](active-directory-b2c-how-to-enable-billing.md).

### <a name="options-for-other-output-formats"></a>Más kimeneti formátum lehetőségeit
A következő kód bemutatja a kimeneti küldésére JSON, az értéklista nevét és XML-példákat:
```powershell
# to output to JSON use following line in the PowerShell sample
$myReport.Content | Out-File -FilePath name-your-file.json -Force

# to output the content to a name value list
($myReport.Content | ConvertFrom-Json).value | Out-File -FilePath name-your-file.txt -Force

# to output the content in XML use the following line
(($myReport.Content | ConvertFrom-Json).value | ConvertTo-Xml).InnerXml | Out-File -FilePath name-your-file.xml -Force
```

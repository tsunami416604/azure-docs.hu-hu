---
title: Használati jelentéskészítési API-minták és-definíciók
titleSuffix: Azure AD B2C
description: Útmutató és minták a Azure AD B2C bérlői felhasználók, hitelesítések és többtényezős hitelesítések jelentéseinek beszerzéséhez.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 08/04/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: f81acf28b502965f896cd8b38767e7c2e925156c
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74949338"
---
# <a name="accessing-usage-reports-in-azure-ad-b2c-via-the-reporting-api"></a>Használati jelentések elérése Azure AD B2C a jelentéskészítési API használatával

A Azure Active Directory B2C (Azure AD B2C) a felhasználói bejelentkezés és az Azure Multi-Factor Authentication alapján biztosítja a hitelesítést. A hitelesítés az alkalmazás családjának végfelhasználói számára biztosítható az identitás-szolgáltatók között. Ha ismeri a bérlőben regisztrált felhasználók számát, a regisztráláshoz használt szolgáltatókat és a hitelesítések számát típus szerint, a következő kérdésekre kaphat választ:
* Az egyes identitás-szolgáltatók (például egy Microsoft-vagy LinkedIn-fiók) hány felhasználója regisztrálva van az elmúlt 10 napban?
* A Multi-Factor Authentication használó hitelesítések száma az elmúlt hónapban sikeresen befejeződött?
* Hány bejelentkezési alapú hitelesítés lett végrehajtva ebben a hónapban? Naponta? Egy alkalmazásban?
* Hogyan becsülhető meg a Azure AD B2C bérlői tevékenység várható havi költsége?

Ez a cikk a számlázási tevékenységhez kötődő jelentésekre összpontosít, amely a felhasználók számától, a számlázható bejelentkezési hitelesítéstől és a többtényezős hitelesítéstől függ.


## <a name="prerequisites"></a>Előfeltételek
Az első lépések előtt végre kell hajtania az [Előfeltételek az Azure ad Reporting API-k eléréséhez](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/)című témakör lépéseit. Hozzon létre egy alkalmazást, szerezzen be egy titkot, és adjon hozzáférési jogosultságokat a Azure AD B2C bérlő jelentéseihez. A *bash-szkript* és a Python- *szkriptek* példái itt is elérhetők.

## <a name="powershell-script"></a>PowerShell-szkript
Ez a szkript a `TimeStamp` paraméter és a `ApplicationId` szűrő használatával négy használati jelentés létrehozását mutatja be.

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


## <a name="usage-report-definitions"></a>Használati jelentések definíciói
* **tenantUserCount**: a bérlőben lévő felhasználók száma az azonosító típusa szerint, az elmúlt 30 napban. (Opcionálisan egy `TimeStamp` szűrő biztosítja a felhasználók számát egy adott dátumtól az aktuális dátumig). A jelentés a következőket tartalmazza:
  * **TotalUserCount**: az összes felhasználói objektum száma.
  * **OtherUserCount**: Azure Active Directory felhasználók száma (nem Azure ad B2C felhasználók).
  * **LocalUserCount**: a Azure ad B2C bérlő helyi hitelesítő adataival létrehozott Azure ad B2C felhasználói fiókok száma.

* **AlternateIdUserCount**: a külső azonosítókkal regisztrált Azure ad B2C felhasználók száma (például Facebook, a Microsoft-fiók vagy más Azure Active Directory bérlő, más néven `OrgId`).

* **b2cAuthenticationCountSummary**: a számlázási hitelesítések napi számának összefoglalása az elmúlt 30 napban, a hitelesítési folyamat napja és típusa szerint.

* **b2cAuthenticationCount**: az adott időszakon belüli hitelesítések száma. Az alapértelmezett érték az utolsó 30 nap.  (Nem kötelező: a `TimeStamp`-paraméterek kezdési és befejezési paramétere meghatározott időtartamot határoz meg.) A kimenetben `StartTimeStamp` (a legkorábbi tevékenység a bérlőnél) és `EndTimeStamp` (legújabb frissítés) szerepel.

* **b2cMfaRequestCountSummary**: a többtényezős hitelesítések napi számának összefoglalása nap és típus szerint (SMS vagy hang).


## <a name="limitations"></a>Korlátozások
A felhasználók száma 24 – 48 óráig frissül. A hitelesítés naponta többször frissül. Az `ApplicationId` szűrő használatakor az üres jelentésre adott válasz az alábbi feltételek egyike lehet:
  * Az alkalmazás azonosítója nem létezik a bérlőben. Ellenőrizze, hogy helyes-e.
  * Az alkalmazás-azonosító létezik, de a jelentési időszakban nem találhatók adatértékek. Tekintse át a dátum/idő paramétereit.


## <a name="next-steps"></a>Következő lépések
### <a name="monthly-bill-estimates-for-azure-ad"></a>Havi számlázási becslések az Azure AD-hez
[A jelenleg elérhető Azure ad B2C díjszabással](https://azure.microsoft.com/pricing/details/active-directory-b2c/)együtt napi, heti és havi Azure-felhasználást is megbecsülheti.  A becslés különösen akkor hasznos, ha a bérlői viselkedés változásait tervezi, ami hatással lehet a teljes összegre. A [kapcsolódó Azure-előfizetésében](active-directory-b2c-how-to-enable-billing.md)áttekintheti a tényleges költségeket.

### <a name="options-for-other-output-formats"></a>Egyéb kimeneti formátumok beállításai
A következő kód példákat mutat be a kimenet JSON-ba való küldésére, a név értékének listájára és az XML-re:
```powershell
# to output to JSON use following line in the PowerShell sample
$myReport.Content | Out-File -FilePath name-your-file.json -Force

# to output the content to a name value list
($myReport.Content | ConvertFrom-Json).value | Out-File -FilePath name-your-file.txt -Force

# to output the content in XML use the following line
(($myReport.Content | ConvertFrom-Json).value | ConvertTo-Xml).InnerXml | Out-File -FilePath name-your-file.xml -Force
```

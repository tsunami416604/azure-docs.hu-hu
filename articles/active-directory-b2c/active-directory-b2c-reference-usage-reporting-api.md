---
title: 'Az Azure Active Directory B2C: Használati jelentéskészítési API-mintákat és meghatározások |} Microsoft Docs'
description: Útmutató és a felhasználók, hitelesítés és többtényezős hitelesítés az Azure AD B2C bérlő jelentések témáját minták
services: active-directory-b2c
documentationcenter: dev-center-name
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.topic: article
ms.workload: identity
ms.date: 08/04/2017
ms.author: davidmu
ms.openlocfilehash: 07029181423927f0796cb85e728df416d01466e8
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="accessing-usage-reports-in-azure-ad-b2c-via-the-reporting-api"></a>Az Azure AD B2C a reporting API-n keresztül használati jelentések használata

Az Azure Active Directory B2C (az Azure AD B2C) alapján a felhasználói bejelentkezés és az Azure multi-factor Authentication hitelesítést nyújt. Hitelesítését a végfelhasználók számára az alkalmazás termékcsalád identitás-szolgáltatóktól között. Amikor tudja, hogy a bérlő, a szolgáltatók regisztrálásához használt és hitelesítési események száma típusonként regisztrált felhasználók száma, a válasz hasonló kérdések:
* Az identitásszolgáltató (például a Microsoft vagy LinkedIn fiókok) különböző típusú hány felhasználó regisztrált az elmúlt 10 napban?
* Hány hitelesítések multi-factor Authentication használatának sikeresen befejeződött az utolsó hónapban?
* Hány bejelentkezési-a-alapú hitelesítés befejeződtek ebben a hónapban? Napi? Alkalmazásonként?
* Hogyan megbecsülhető a saját Azure AD B2C bérlő tevékenység várható havi költségét?

Ez a cikk foglalkozik számlázási tevékenység, a felhasználók számát, számlázható bejelentkezési-a-alapú hitelesítés, és a multi-factor Authentication hitelesítések alapuló kötve a jelentésekre.


## <a name="prerequisites"></a>Előfeltételek
Mielőtt hozzáfogna, végre kell hajtania a lépéseket [Előfeltételek az Azure AD reporting API-k elérésére](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/). Hozzon létre egy alkalmazást, a titkos kulcs beszerzése és a hozzáférés engedélyezése az Azure AD B2C-bérlő jelentések jogosultsággal. *Parancsfájl bash* és *Python-parancsfájl* példákat itt is megadja. 

## <a name="powershell-script"></a>PowerShell-szkript
Ez a parancsfájl használatával négy használati jelentések létrehozását mutatja be a `TimeStamp` paraméter és a `ApplicationId` szűrő.

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


## <a name="usage-report-definitions"></a>Használati jelentésdefiníciók
* **tenantUserCount**: a bérlő típusú identitásszolgáltató naponta az elmúlt 30 napban lévő felhasználók száma. (Ha szükséges, egy `TimeStamp` szűrő biztosít felhasználószám megadott dátum az aktuális dátum). A jelentés tartalmazza:
  * **TotalUserCount**: A minden felhasználói objektumok száma.
  * **OtherUserCount**: Azure Active Directory-felhasználók (nem az Azure AD B2C felhasználók) száma.
  * **LocalUserCount**: Azure AD B2C felhasználói fiókok létrehozott hitelesítő adatokkal rendelkező helyi, az Azure AD B2C-bérlő.

* **AlternateIdUserCount**: külső Identitásszolgáltatók regisztrálva az Azure AD B2C-felhasználók száma (például Facebook, a Microsoft-fiók vagy egy másik Azure Active Directory-bérlőt, más néven egy `OrgId`).

* **b2cAuthenticationCountSummary**: napi száma az elmúlt 30 nap, napi és a hitelesítési folyamat típusú számlázható hitelesítések összefoglalását.

* **b2cAuthenticationCount**: az egy időtartamon belül számát. Az alapértelmezett érték az elmúlt 30 napban.  (Nem kötelező: A kezdő és befejező `TimeStamp` paraméterek megadása egy adott időszakra vonatkozóan.) A parancs kimenete `StartTimeStamp` (ennél a bérlőnél tevékenység legkorábbi dátum) és `EndTimeStamp` (legújabb frissítés).

* **b2cMfaRequestCountSummary**: összefoglaló a multi-factor Authentication hitelesítést, a nap és típusa (SMS vagy hangos) napi számát.


## <a name="limitations"></a>Korlátozások
Felhasználói száma az adatok frissítése a 48 24 óránként. Hitelesítési események frissülnek naponta több alkalommal. Használatakor a `ApplicationId` szűrő, egy üres válasz okozhatja, hogy következő feltételek valamelyike esetén:
  * Az alkalmazás-azonosítója nem létezik a bérlőben. Győződjön meg arról, hogy helyes-e.
  * Az Alkalmazásazonosító létezik, de a jelentési időszak adatot nem található. Ellenőrizze a dátum/idő paramétereket.


## <a name="next-steps"></a>További lépések
### <a name="monthly-bill-estimates-for-azure-ad"></a>Havi számlán az Azure AD becslése
Együtt [a legfrissebb Azure AD B2C díjszabási elérhető](https://azure.microsoft.com/pricing/details/active-directory-b2c/), megbecsülheti napi, heti és havi Azure-használatát.  Becsült különösen fontos, ha azt tervezi, hogy a bérlő működését, ami hatással lehet a teljes költség szempontjából. Tekintse át a tényleges költségek a [Azure-előfizetéssel társított](active-directory-b2c-how-to-enable-billing.md).

### <a name="options-for-other-output-formats"></a>Egyéb formátumokban beállításai
A következő kód kimenetének küldése a JSON-NÁ, a név-érték listáját és az XML-példákat szemlélteti:
```powershell
# to output to JSON use following line in the PowerShell sample
$myReport.Content | Out-File -FilePath name-your-file.json -Force

# to output the content to a name value list
($myReport.Content | ConvertFrom-Json).value | Out-File -FilePath name-your-file.txt -Force

# to output the content in XML use the following line
(($myReport.Content | ConvertFrom-Json).value | ConvertTo-Xml).InnerXml | Out-File -FilePath name-your-file.xml -Force
```

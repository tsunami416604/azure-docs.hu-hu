---
title: Konvergens regisztráció letiltása az Azure AD SSPR és a többtényezős hitelesítés (nyilvános előzetes verzió)
description: Tiltsa le az Azure AD multi-factor Authentication és az önkiszolgáló jelszó-átállítási regisztrációk (nyilvános előzetes verzió)
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 08/02/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry, michmcla
ms.openlocfilehash: 6a51b1a3050b37fdcc822006f9e25d6662c65fb2
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54426352"
---
# <a name="disable-azure-ad-converged-registration-public-preview"></a>Tiltsa le az Azure AD összevont regisztrációs (nyilvános előzetes verzió)

Élmény, a szolgáltatás stampek amikor egy felhasználó regisztrál a telefonszám és/vagy mobilalkalmazás az új converged jelzőket (StrongAuthenticationMethods) azokat a módszereket, hogy a felhasználó a számára. Ez a funkció lehetővé teszi, hogy a felhasználónak az Azure multi-factor Authentication (MFA) az azokat a módszereket, amikor az MFA megadása kötelező.

A módszereket, az új funkció révén a felhasználóknak regisztrálniuk kell a StrongAuthenticationMethods tulajdonság értéke. Ez a viselkedés akkor is előfordul, ha a nyilvános előzetes verzióban érhető el. Ha egy rendszergazda lehetővé teszi, hogy az előzetes verzió, a felhasználóknak regisztrálniuk az új felhasználói felület segítségével, és a rendszergazda letiltja az előzetes verzióra, felhasználók tudtukon regisztrálható a multi-factor Authentication is.

Ha egy felhasználó, aki befejeződött összevont regisztrációs navigál az SSPR regisztrációs oldalhoz [ https://aka.ms/ssprsetup ](https://aka.ms/ssprsetup), kérni fogja a hajthatok végre MFA, az oldal eléréséhez. Ez a lépés nem a várt viselkedéssel technikai szempontból, de a felhasználók számára, akik korábban már regisztrált az SSPR csak, ez a lépés, egy új funkció. Bár ez a lépés további javításához a felhasználó biztonsági állapotáról azáltal, hogy egy további biztonsági szint rendszergazdáknak érdemes visszaállítása lehetőséget a felhasználók számára, hogy azok nem lesznek képesek többtényezős hitelesítés elvégzése.  

## <a name="how-to-roll-back-users"></a>Hogyan állítható vissza a felhasználók

Ha rendszergazdaként szeretné a felhasználó MFA-beállítások alaphelyzetbe állítása, egy PowerShell-parancsprogram, amely törli a felhasználó mobilalkalmazásában és/vagy a telefonszámot a StrongAuthenticationMethods tulajdonság hoztunk létre. A felhasználók számára a parancsfájl futtatása, az azt jelenti, hogy szükség esetén a multi-factor Authentication újra regisztrálnia kell. Azt javasoljuk, hogy egy vagy két felhasználókkal való visszaállítás tesztelése előtt az érintett felhasználók visszaállítása.

A következő lépések segítenek állítja vissza egy felhasználó vagy felhasználók csoportja:

### <a name="pre-requisites"></a>Előfeltételek

1. Szüksége lesz a megfelelő Azure AD PowerShell-modulok telepítéséhez. Egy PowerShell-ablakban futtassa a következő parancsokat a modulok telepítéséhez:

   ```powershell
   Install-Module -Name MSOnline
   Import-Module MSOnline
   ```

1. Mentse az érintett felhasználói objektum azonosítója vagy azonosítói listája a gép minden sorában egy azonosítójú szöveges fájlból. Jegyezze meg a fájl helyét.
1. Mentse a következő szkriptet a gépre, és jegyezze fel a parancsprogram helye:

```powershell
<# 
//********************************************************
//*                                                      *
//*   Copyright (C) Microsoft. All rights reserved.      *
//*                                                      *
//********************************************************
#>

param($path)

# Define Remediation Fn
function RemediateUser {

    param  
    (
        $ObjectId
    )

    $user = Get-MsolUser -ObjectId $ObjectId

    Write-Host "Checking if user is eligible for rollback: UPN: "  $user.UserPrincipalName  " ObjectId: "  $user.ObjectId -ForegroundColor Yellow

    $hasMfaRelyingParty = $false
    foreach($p in $user.StrongAuthenticationRequirements)
    {
        if ($p.RelyingParty -eq "*")
        {
            $hasMfaRelyingParty = $true
            Write-Host "User was enabled for per-user MFA." -ForegroundColor Yellow
        }
    }

    if ($user.StrongAuthenticationMethods.Count -gt 0 -and -not $hasMfaRelyingParty)
    {
        Write-Host $user.UserPrincipalName " is eligible for rollback" -ForegroundColor Yellow
        Write-Host "Rolling back user ..." -ForegroundColor Yellow
        Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName $user.UserPrincipalName
        Write-Host "Successfully rolled back user " $user.UserPrincipalName -ForegroundColor Green
    }
    else
    {
        Write-Host $user.UserPrincipalName " is not eligible for rollback. No action required."
    }

    Write-Host ""
    Start-Sleep -Milliseconds 750
}

# Connect
Import-Module MSOnline
Connect-MsolService

foreach($line in Get-Content $path)
{
    RemediateUser -ObjectId $line
}
```

### <a name="rollback"></a>Visszaállítás

Egy PowerShell-ablakban futtassa a következő parancsot a kijelölt helyek frissítése után. Adja meg globális rendszergazdai hitelesítő adatait, amikor a rendszer kéri. A parancsfájl kimenete minden egyes felhasználó-frissítési művelet eredményét.

`<script location> -path <user file location>`

## <a name="disable-preview-experience"></a>Tiltsa le az előzetes felület

A felhasználók számára az előzetes felület letiltásához kövesse az alábbi lépéseket:

1. Jelentkezzen be egy globális rendszergazdai vagy a felhasználó rendszergazdaként az Azure Portalon.
2. Keresse meg a **Azure Active Directory**, **felhasználói beállítások**, **kezelheti a hozzáférési panel előzetes verziójú funkciók beállításait**.
3. A **felhasználók használhatják a előzetes verziójú funkciók regisztrálásához és a biztonsági adatok kezelése**, állítsa a választó **nincs** kattintson **mentése**.

Többé nem kéri a felhasználótól regisztrálni az előzetes felület használatával.

## <a name="next-steps"></a>További lépések

[További információ a konvergens regisztráció önkiszolgáló jelszó-visszaállítás és az Azure multi-factor Authentication szolgáltatás nyilvános előzetes verzió](concept-registration-mfa-sspr-converged.md)
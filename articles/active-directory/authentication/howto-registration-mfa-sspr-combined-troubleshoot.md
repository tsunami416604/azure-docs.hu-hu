---
title: Az Azure AD SSPR és a többtényezős hitelesítés (előzetes verzió) – az Azure Active Directory kombinált regisztrációs hibaelhárítása
description: Hibaelhárítása Azure AD multi-factor Authentication és az önkiszolgáló jelszó-visszaállítási kombinált regisztrációs (előzetes verzió)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: d926f7312b62e788289939dfd81c236a33503b43
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370465"
---
# <a name="troubleshooting-combined-security-information-registration-preview"></a>Hibaelhárítási kombinált biztonsági információk regisztrációs (előzetes verzió)

Ebben a cikkben található információk segítségével útmutató rendszergazdák, akik a kombinált regisztrációs élményét, hogy a végfelhasználók által jelentett problémák hibaelhárítást.

|     |
| --- |
| Egyesített biztonsági információkat regisztráció Azure multi-factor Authentication és az Azure AD önkiszolgáló jelszó-visszaállítás az Azure Active Directory nyilvános előzetes verziójú funkció. Előzetes verziók kapcsolatos további információkért lásd: [kiegészítő használati feltételek a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="audit-logs"></a>Naplók

Az Azure AD-ben a "Hitelesítési módszerek" kategóriában vannak az események naplózása kombinált regisztrációs naplók.

![Az Azure AD naplók felületet megjelenítő regisztrációs események](media/howto-registration-mfa-sspr-combined-troubleshoot/combined-security-info-audit-log.png)

Az alábbi listában kombinált regisztrációs által létrehozott összes naplózási eseményt:

| Tevékenység | status | Ok | Leírás |
| --- | --- | --- | --- |
| A felhasználó regisztrált az összes szükséges biztonsági adatai | Sikeres | A felhasználó regisztrált az összes szükséges biztonsági adatait. | Ez az esemény akkor fordul elő, amikor egy felhasználó sikeresen befejezte a regisztrációs.|
| A felhasználó regisztrált az összes szükséges biztonsági adatai | Hiba | A felhasználó megszakította a biztonsági adatok regisztrálása. | Ez az esemény akkor fordul elő, amikor egy felhasználó megszakítja a megszakítási módból regisztrációs.|
| Felhasználó által regisztrált biztonsági adatai | Sikeres | A felhasználó regisztrált a "method". | Ez az esemény akkor fordul elő, amikor egy felhasználó regisztrál egy egyéni módszer. "Method" lehet az Authenticator alkalmazás képe, telefonszám, E-mail, biztonsági kérdéseket, alkalmazásjelszót, telefonszámok és stb.| 
| Felhasználó, tekintse át biztonsági adatait | Sikeres | Felhasználó sikeresen tekintse át biztonsági adatait. | Ez az esemény akkor fordul elő, amikor egy felhasználó rákattint a "Jól" a biztonsági adatok áttekintése lapon.|
| Felhasználó, tekintse át biztonsági adatait | Hiba | A felhasználó nem tudta ellenőrizni biztonsági adatait. | Ez az esemény akkor fordul elő, amikor egy felhasználó rákattint "Rendben" a megadott biztonsági adatára tekintse át a lapot, de hiba lép fel a háttérben.|
| Törölt felhasználó biztonsági adatai | Sikeres | Felhasználó törölte a "method". | Ez az esemény akkor fordul elő, amikor a felhasználó töröl egy egyéni módszer. "Method" lehet az Authenticator alkalmazás képe, telefonszám, E-mail, biztonsági kérdéseket, alkalmazásjelszót, telefonszámok és stb.|
| Törölt felhasználó biztonsági adatai | Hiba | Felhasználó nem sikerült törölni a "method". | Ez az esemény akkor fordul elő, amikor egy felhasználó próbál törölni egy metódust, de valamilyen okból sikertelen. "Method" lehet az Authenticator alkalmazás képe, telefonszám, E-mail, biztonsági kérdéseket, alkalmazásjelszót, telefonszámok és stb.|
| Beállítás változása alapértelmezett biztonsági adatai | Sikeres | Alapértelmezett biztonsági adatok módosította a "method". | Ez az esemény akkor fordul elő, amikor a felhasználók megadják az alapértelmezett módot. "Method" is Authenticator alkalmazásban megjelenő értesítésre, saját hitelesítő alkalmazásból vagy a jogkivonatot, hívás + X XXXXXXXXXX, szöveges code egy kódot + X XXXXXXXXX stb.|
| Beállítás változása alapértelmezett biztonsági adatai | Hiba | Felhasználó nem sikerült módosítani az alapértelmezett biztonsági adatok a "method". | Ez az esemény akkor fordul elő, amikor a felhasználó megpróbálja módosítani az alapértelmezett módszer, de valamilyen okból sikertelen. "Method" lehet a hitelesítő alkalmazásban megjelenő értesítésre, egy kód a saját hitelesítő alkalmazásból vagy a jogkivonatot, hívás + X XXXXXXXXXX, szöveg egy kódot + X XXXXXXXXX stb.|

## <a name="troubleshooting-interrupt-mode"></a>Hibaelhárítási megszakítási mód

| Jelenség | Hibaelhárítási lépések |
| --- | --- |
| Nem látok a módszereket, tekintse meg a várt. | 1. Ellenőrizze, hogy ha a felhasználó rendelkezik-e az Azure AD-Rendszergazda szerepkörhöz. Ha igen, tekintse át az SSPR rendszergazdai házirend különbségek. <br> 2. Határozza meg, hogy a felhasználó megszakad folyamatban van az MFA regisztrációs kényszerítési vagy az SSPR regisztrációs kényszerítési miatt. Tekintse át a folyamatábra kombinált regisztrációs módokban meghatározni, melyik módszert jelenjenek meg. <br> 3. Határozza meg, hogyan nemrég az MFA vagy az SSPR házirend megváltozott. Ha a módosítás történt legutóbbi, eltarthat egy kis ideig propagálása a frissített szabályzatot.|

## <a name="troubleshooting-manage-mode"></a>Hibaelhárítási módban kezelése

| Jelenség | Hibaelhárítási lépések |
| --- | --- |
| Nem rendelkezem egy adott metódust hozzáadását. | 1. Határozza meg, a metódus engedélyezve van-e a multi-factor Authentication vagy az SSPR. <br> 2. Ha engedélyezve van a módszert, mentse el újra a házirendeket, és várjon, 1 – 2 óra újra tesztelése előtt. <br> 3. Ha a metódus engedélyezve van, győződjön meg arról, nem a felhasználó már beállított a módszer, amely jogosult beállítani a maximális számát.|

## <a name="disable-combined-registration"></a>Egyesített regisztrációs letiltása

Élmény, a szolgáltatás stampek amikor egy felhasználó regisztrál a telefonszám és/vagy mobilalkalmazás az új kombinált jelzőket (StrongAuthenticationMethods) azokat a módszereket, hogy a felhasználó a számára. Ez a funkció lehetővé teszi, hogy a felhasználónak az Azure multi-factor Authentication (MFA) az azokat a módszereket, amikor az MFA megadása kötelező.

A módszereket, az új funkció révén a felhasználóknak regisztrálniuk kell a StrongAuthenticationMethods tulajdonság értéke. Ha egy rendszergazda lehetővé teszi, hogy az előzetes verzió, a felhasználóknak regisztrálniuk az új felhasználói felület segítségével, és a rendszergazda letiltja az előzetes verzióra, felhasználók tudtukon regisztrálható a multi-factor Authentication is.

Ha egy felhasználó, aki befejeződött kombinált regisztrációs navigál az aktuális önkiszolgáló jelszó-visszaállítási (SSPR) regisztrációs oldal [ https://aka.ms/ssprsetup ](https://aka.ms/ssprsetup), kérni fogja a hajthatok végre MFA, az oldal eléréséhez. Ez a lépés nem a várt viselkedéssel technikai szempontból, de a felhasználók számára, akik korábban már regisztrált az SSPR csak, ez a lépés, egy új funkció. Bár ez a lépés további javításához a felhasználó biztonsági állapotáról azáltal, hogy egy további biztonsági szint rendszergazdáknak érdemes visszaállítása lehetőséget a felhasználók számára, hogy azok nem lesznek képesek többtényezős hitelesítés elvégzése.  

### <a name="how-to-roll-back-users"></a>Hogyan állítható vissza a felhasználók

Ha rendszergazdaként szeretné a felhasználó MFA-beállítások alaphelyzetbe állítása, egy PowerShell-parancsprogram, amely törli a felhasználó mobilalkalmazásában és/vagy a telefonszámot a StrongAuthenticationMethods tulajdonság hoztunk létre. A felhasználók számára a parancsfájl futtatása, az azt jelenti, hogy szükség esetén a multi-factor Authentication újra regisztrálnia kell. Azt javasoljuk, hogy egy vagy két felhasználókkal való visszaállítás tesztelése előtt az érintett felhasználók visszaállítása.

A következő lépések segítenek állítja vissza egy felhasználó vagy felhasználók csoportja:

#### <a name="prerequisites"></a>Előfeltételek

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

#### <a name="rollback"></a>Visszaállítás

Egy PowerShell-ablakban futtassa a következő parancsot a kijelölt helyek frissítése után. Adja meg globális rendszergazdai hitelesítő adatait, amikor a rendszer kéri. A parancsfájl kimenete minden egyes felhasználó-frissítési művelet eredményét.

`<script location> -path <user file location>`

### <a name="disable-preview-experience"></a>Tiltsa le az előzetes felület

A felhasználók számára az előzetes felület letiltásához kövesse az alábbi lépéseket:

1. Jelentkezzen be egy globális rendszergazdai vagy a felhasználó rendszergazdaként az Azure Portalon.
2. Keresse meg a **Azure Active Directory**, **felhasználói beállítások**, **kezelheti a hozzáférési panel előzetes verziójú funkciók beállításait**.
3. A **felhasználók használhatják a előzetes verziójú funkciók regisztrálásához és a biztonsági adatok kezelése**, állítsa a választó **nincs** kattintson **mentése**.

Többé nem kéri a felhasználótól regisztrálni az előzetes felület használatával.

## <a name="next-steps"></a>További lépések

* [További információ a kombinált regisztráció önkiszolgáló jelszó-visszaállítás és az Azure multi-factor Authentication szolgáltatás nyilvános előzetes verzió](concept-registration-mfa-sspr-combined.md)

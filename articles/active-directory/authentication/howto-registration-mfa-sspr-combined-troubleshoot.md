---
title: Az Azure AD SSPR és a multi-factor Authentication (előzetes verzió) – az Azure Active Directory kombinált regisztrációs hibaelhárítása
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
ms.openlocfilehash: ba77772352d3f6f6494abeddc7faf9f12e5f80c2
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59262567"
---
# <a name="troubleshooting-combined-security-information-registration-preview"></a>Hibaelhárítási kombinált biztonsági információk regisztrációs (előzetes verzió)

Ebben a cikkben található információk az adott rendszergazdák, akik számára a kombinált regisztrációs felület a felhasználók által jelentett hibák hibaelhárítást is.

|     |
| --- |
| Egyesített biztonsági információk regisztráció az Azure multi-factor Authentication és az Azure Active Directory (Azure AD) önkiszolgáló jelszóátállítás az nyilvános előzetes verziójú funkció az Azure AD. További információ az előzetes verziókról: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

## <a name="audit-logs"></a>Naplók

Az Azure AD-ben a hitelesítési módszerek kategóriába vannak az események naplózása kombinált regisztrációs naplók.

![Az Azure AD naplók felületet megjelenítő regisztrációs események](media/howto-registration-mfa-sspr-combined-troubleshoot/combined-security-info-audit-log.png)

Az alábbi táblázat a kombinált regisztrációs által létrehozott összes naplózási eseményt:

| Tevékenység | status | Ok | Leírás |
| --- | --- | --- | --- |
| A felhasználó regisztrált az összes szükséges biztonsági adatai | Sikeres | A felhasználó regisztrált az összes szükséges biztonsági adatait. | Ez az esemény akkor fordul elő, amikor egy felhasználó sikeresen befejezte a regisztrációs.|
| A felhasználó regisztrált az összes szükséges biztonsági adatai | Hiba | A felhasználó megszakította a biztonsági adatok regisztrálása. | Ez az esemény akkor fordul elő, amikor egy felhasználó megszakítja a megszakítási módból regisztrációs.|
| Felhasználó által regisztrált biztonsági adatai | Sikeres | Felhasználó által regisztrált *metódus*. | Ez az esemény akkor fordul elő, amikor egy felhasználó regisztrál egy egyéni módszer. *Módszer* lehet hitelesítő alkalmazás, telefonos, E-mail, biztonsági kérdések, alkalmazás jelszó, másodlagos telefonszám, és így tovább.| 
| Felhasználó, tekintse át biztonsági adatait | Sikeres | Felhasználó sikeresen tekintse át biztonsági adatait. | Ez az esemény akkor fordul elő, amikor egy felhasználó kijelöli **rendben** a biztonsági adatok áttekintése lapon.|
| Felhasználó, tekintse át biztonsági adatait | Hiba | A felhasználó nem tudta ellenőrizni biztonsági adatait. | Ez az esemény akkor fordul elő, amikor egy felhasználó kijelöli **rendben** lap tekintse át a biztonsági adatokat, de hiba lép fel a háttérkiszolgálón.|
| Törölt felhasználó biztonsági adatai | Sikeres | Törölt felhasználó *metódus*. | Ez az esemény akkor fordul elő, amikor a felhasználó töröl egy egyéni módszer. *Módszer* lehet hitelesítő alkalmazás, telefonos, E-mail, biztonsági kérdések, alkalmazás jelszó, másodlagos telefonszám, és így tovább.|
| Törölt felhasználó biztonsági adatai | Hiba | Nem sikerült törölni a felhasználói *metódus*. | Ez az esemény akkor fordul elő, amikor egy felhasználó próbál törölni egy metódust, de valamilyen okból sikertelen. *Módszer* lehet hitelesítő alkalmazás, telefonos, E-mail, biztonsági kérdések, alkalmazás jelszó, másodlagos telefonszám, és így tovább.|
| Beállítás változása alapértelmezett biztonsági adatai | Sikeres | Felhasználó módosította a következő alapértelmezett biztonsági adatait *metódus*. | Ez az esemény akkor fordul elő, amikor a felhasználók az alapértelmezett mód. *Módszer* is Authenticator alkalmazásban megjelenő értesítésre, egy kód a saját hitelesítő alkalmazásból vagy a jogkivonatot, hívás + X XXXXXXXXXX, szöveg egy kódot + X XXXXXXXXX, és így tovább.|
| Beállítás változása alapértelmezett biztonsági adatai | Hiba | A felhasználó nem tudta módosítani az alapértelmezett biztonsági adatainak *metódus*. | Ez az esemény akkor fordul elő, amikor a felhasználó megpróbálja módosítani az alapértelmezett módszer, de valamilyen okból sikertelen. *Módszer* is Authenticator alkalmazásban megjelenő értesítésre, egy kód a saját hitelesítő alkalmazásból vagy a jogkivonatot, hívás + X XXXXXXXXXX, szöveg egy kódot + X XXXXXXXXX, és így tovább.|

## <a name="troubleshooting-interrupt-mode"></a>Hibaelhárítási megszakítási mód

| Jelenség | Hibaelhárítási lépések |
| --- | --- |
| Nem látok a módszereket, tekintse meg a várt. | 1. Ellenőrizze, hogy ha a felhasználó rendelkezik-e az Azure AD-rendszergazdai szerepkörhöz. Ha igen, tekintse meg az SSPR felügyeleti házirend különbségek. <br> 2. Határozza meg, hogy e a felhasználó multi-factor Authentication regisztrációs kényszerítési vagy az SSPR regisztrációs kényszerítési miatt folyamatban megszakadt. Tekintse meg a [folyamatábra](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md#combined-registration-modes) "Kombinált regisztrációs módokban" meghatározni, melyik módszert jelenjenek meg. <br> 3. Határozza meg, hogyan mostanában a multi-factor Authentication vagy az SSPR házirend megváltozott. Ha a módosítás történt legutóbbi, eltarthat egy kis ideig propagálása a frissített szabályzatot.|

## <a name="troubleshooting-manage-mode"></a>Hibaelhárítási módban kezelése

| Jelenség | Hibaelhárítási lépések |
| --- | --- |
| Nem rendelkezem egy adott metódust hozzáadását. | 1. Határozza meg, a metódus engedélyezve van-e a multi-factor Authentication vagy az SSPR. <br> 2. A módszer engedélyezett, ha mentse újból a szabályzatokat, és várjon 1-2 órát újra tesztelés előtt. <br> 3. Ha a metódus engedélyezve van, győződjön meg arról, nem a felhasználó már beállított a módszer, amely jogosult beállítani a maximális számát.|

## <a name="disable-combined-registration"></a>Egyesített regisztrációs letiltása

Élmény, a szolgáltatás stampek amikor egy felhasználó regisztrál egy telefonszám és/vagy a mobilalkalmazást az új kombinált jelzőket (StrongAuthenticationMethods) azokat a módszereket, hogy a felhasználó a számára. Ez a funkció lehetővé teszi, hogy a felhasználó számára a multi-factor Authentication végrehajtása a azokat a módszereket, amikor a többtényezős hitelesítésre szükség.

Ha egy rendszergazda lehetővé teszi, hogy az előzetes verzió, a felhasználóknak regisztrálniuk az új felhasználói felület segítségével, és a rendszergazda letiltja az előzetes verzióra, felhasználók tudtukon regisztrálható a multi-factor Authentication is.

Ha olyan felhasználó, aki kombinált regisztrációs befejeződött, az aktuális önkiszolgáló jelszó-visszaállítási (SSPR) regisztrációs oldal, [ https://aka.ms/ssprsetup ](https://aka.ms/ssprsetup), a felhasználó felszólítást kap a multi-factor Authentication végrehajtása a hozzáférés előtt a lapon. Ebben a lépésben technikai szempontból várt, de új felhasználók számára, akik korábban már regisztrált az SSPR csak van. Bár ez a lépés további javításához a felhasználó biztonsági állapotáról azáltal, hogy egy másik biztonsági szintjét állítja vissza a felhasználók úgy, hogy azok a továbbiakban nem sikerült elvégezni a multi-factor Authentication rendszergazdák érdemes.  

### <a name="how-to-roll-back-users"></a>Hogyan állítható vissza a felhasználók

Ha, rendszergazdaként szeretné egy felhasználó a multi-factor Authentication szolgáltatás beállításainak visszaállítása, használhatja a következő szakaszban található PowerShell-parancsfájlt. A parancsfájl törölni fogja a StrongAuthenticationMethods tulajdonságát a felhasználó mobilalkalmazásában és/vagy a telefonszámot. Ez a szkript futtatásakor a felhasználók számára, akkor kell újból a regisztrálást a multi-factor Authentication szolgáltatás szükség esetén tudjanak. Javasoljuk, hogy tesztelési visszaállítás egy vagy két felhasználót az összes érintett felhasználók visszaállítása előtt.

A következő lépések segítséget, állítsa vissza egy felhasználó vagy felhasználók csoportja.

#### <a name="prerequisites"></a>Előfeltételek

1. A megfelelő Azure AD PowerShell-modulok telepítéséhez. Egy PowerShell-ablakban futtassa a következő parancsokat a modulok telepítéséhez:

   ```powershell
   Install-Module -Name MSOnline
   Import-Module MSOnline
   ```

1. Érintett felhasználó objektumazonosítók listájának soronként egy Azonosítót szöveges fájlba mentése a számítógépre. Jegyezze meg a fájl helyét.
1. Mentse a következő szkriptet a számítógépre, és jegyezze fel a parancsprogram helye:

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

Egy PowerShell-ablakban, futtassa a következő parancsot a szkript és a felhasználó fájlhelyeket biztosítása. Adja meg globális rendszergazdai hitelesítő adatait, amikor a rendszer kéri. A parancsfájl kimenete minden egyes felhasználó-frissítési művelet eredményét.

`<script location> -path <user file location>`

### <a name="disable-the-preview-experience"></a>Az előzetes felület letiltása

A felhasználók számára az előzetes felület letiltásához hajtsa végre ezeket a lépéseket:

1. Jelentkezzen be egy globális rendszergazdai vagy a felhasználó rendszergazdaként az Azure Portalon.
2. Lépjen a **Azure Active Directory** > **felhasználói beállítások** > **kezelheti a hozzáférési panel előzetes verziójú funkciók beállításait**.
3. A **felhasználók használhatják a előzetes verziójú funkciók regisztrálásához és a biztonsági adatok kezelése**, állítsa a választó **nincs**, majd válassza ki **mentése**.

Többé nem kéri a felhasználótól regisztrálni az előzetes felület használatával.

## <a name="next-steps"></a>További lépések

* [További információ a kombinált regisztráció önkiszolgáló jelszó-visszaállítás és az Azure multi-factor Authentication szolgáltatás nyilvános előzetes verzió](concept-registration-mfa-sspr-combined.md)

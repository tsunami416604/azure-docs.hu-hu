---
title: Kombinált regisztráció – Azure Active Directory
description: Az Azure AD többtényezős hitelesítés és az önkiszolgáló jelszó-visszaállítás kombinált regisztrációjának hibaelhárítása
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 04/15/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c840df2c53554519f62a3d1d7a7d8b305187ffb
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81450938"
---
# <a name="troubleshooting-combined-security-information-registration"></a>A biztonsági adatok kombinált regisztrációjának hibáinak elhárítása

Az ebben a cikkben található információk célja, hogy a rendszergazdák, akik hibaelhárítási problémák által jelentett felhasználók a kombinált regisztrációs élményt.

## <a name="audit-logs"></a>Naplók

A kombinált regisztrációhoz naplózott események az Azure AD naplózási naplóinak hitelesítési módszerek kategóriájában találhatók.

![Az Azure AD naplózási naplóinak felülete regisztrációs eseményeket jelenít meg](media/howto-registration-mfa-sspr-combined-troubleshoot/combined-security-info-audit-log.png)

Az alábbi táblázat a kombinált regisztráció által generált összes naplózási eseményt sorolja fel:

| Tevékenység | status | Ok | Leírás |
| --- | --- | --- | --- |
| A felhasználó regisztrálta az összes szükséges biztonsági adatot | Sikeres | A felhasználó regisztrálta az összes szükséges biztonsági adatot. | Ez az esemény akkor következik be, ha a felhasználó sikeresen befejezte a regisztrációt.|
| A felhasználó regisztrálta az összes szükséges biztonsági adatot | Hiba | A felhasználó visszavonta a biztonsági adatok regisztrációját. | Ez az esemény akkor következik be, amikor a felhasználó megszakítja a regisztrációt a megszakítási módból.|
| Felhasználó által regisztrált biztonsági adatok | Sikeres | Felhasználó által regisztrált *módszer*. | Ez az esemény akkor következik be, amikor a felhasználó regisztrál egy egyedi metódust. *Módszer* lehet Hitelesítő app, Telefon, E-mail, Biztonsági kérdések, App jelszó, Alternatív telefon, és így tovább.| 
| A felhasználó áttekintette a biztonsági adatokat | Sikeres | A felhasználó sikeresen áttekintette a biztonsági adatokat. | Ez az esemény akkor következik be, amikor a felhasználó a **Jól néz ki** lehetőséget választja a biztonsági adatok ellenőrzéslapján.|
| A felhasználó áttekintette a biztonsági adatokat | Hiba | A felhasználó nem tudta áttekinteni a biztonsági adatokat. | Ez az esemény akkor következik be, amikor a felhasználó a **Jól néz ki** lehetőséget választja a biztonsági adatok ellenőrzéslapján, de valami nem sikerül a háttérrendszeren.|
| A felhasználó által törölt biztonsági adatok | Sikeres | A felhasználó által törölt *módszer*. | Ez az esemény akkor következik be, amikor a felhasználó töröl egy adott metódust. *Módszer* lehet Hitelesítő app, Telefon, E-mail, Biztonsági kérdések, App jelszó, Alternatív telefon, és így tovább.|
| A felhasználó által törölt biztonsági adatok | Hiba | A felhasználó nem tudta törölni a *metódust*. | Ez az esemény akkor következik be, amikor a felhasználó megpróbál törölni egy metódust, de a kísérlet valamilyen okból sikertelen. *Módszer* lehet Hitelesítő app, Telefon, E-mail, Biztonsági kérdések, App jelszó, Alternatív telefon, és így tovább.|
| A felhasználó módosította az alapértelmezett biztonsági adatokat | Sikeres | A felhasználó megváltoztatta a *metódus*alapértelmezett biztonsági adatait. | Ez az esemény akkor következik be, amikor a felhasználó módosítja az alapértelmezett módszert. *Módszer* lehet Hitelesítő app értesítés, A kódot az én hitelesítő app vagy token, Call +X XXXXXXXXXX, Szöveg egy kódot +X XXXXXXXXX, és így tovább.|
| A felhasználó módosította az alapértelmezett biztonsági adatokat | Hiba | A felhasználó nem tudta módosítani a *metódus*alapértelmezett biztonsági adatait. | Ez az esemény akkor következik be, amikor a felhasználó megpróbálja módosítani az alapértelmezett módszert, de a kísérlet valamilyen okból sikertelen. *Módszer* lehet Hitelesítő app értesítés, A kódot az én hitelesítő app vagy token, Call +X XXXXXXXXXX, Szöveg egy kódot +X XXXXXXXXX, és így tovább.|

## <a name="troubleshooting-interrupt-mode"></a>Megszakítási mód elhárítása

| Hibajelenség | Hibaelhárítási lépések |
| --- | --- |
| Nem látom azáltal a várt módszereket. | 1. Ellenőrizze, hogy a felhasználó rendelkezik-e Azure AD-rendszergazdai szerepkörrel. Ha igen, tekintse meg az SSPR felügyeleti házirend-különbségeit. <br> 2. Határozza meg, hogy a felhasználó megszakad-e a többtényezős hitelesítés regisztrációs kényszerítése vagy az SSPR regisztrációs kényszerítése miatt. A "Kombinált regisztrációs módok" alatt találja meg a [folyamatábrát,](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md#combined-registration-modes) hogy meghatározza, mely módszereket kell megjeleníteni. <br> 3. Határozza meg, hogy a közelmúltban hogyan módosult a többtényezős hitelesítés vagy az SSPR-házirend. Ha a módosítás legutóbbi volt, eltarthat egy ideig, amíg a frissített házirend propagálása megtörtén.|

## <a name="troubleshooting-manage-mode"></a>Kezelési kezelési mód – hibaelhárítás

| Hibajelenség | Hibaelhárítási lépések |
| --- | --- |
| Nincs lehetőségem egy adott módszer hozzáadására. | 1. Határozza meg, hogy a módszer engedélyezve van-e a többtényezős hitelesítéshez vagy az SSPR-hez. <br> 2. Ha a módszer engedélyezve van, mentse újra a házirendeket, és várjon 1-2 órát a tesztelés előtt. <br> 3. Ha a metódus engedélyezve van, győződjön meg arról, hogy a felhasználó még nem állította be a beállítani kívánt módszer maximális számát.|

## <a name="disable-combined-registration"></a>Kombinált regisztráció letiltása

Amikor egy felhasználó regisztrál egy telefonszámot és/vagy mobilalkalmazást az új kombinált élményben, a szolgáltatásunk lebélyegzi az adott felhasználó metódusaihoz tartozó jelzők (StrongAuthenticationMethods) készletét. Ez a funkció lehetővé teszi a felhasználó számára, hogy többtényezős hitelesítést hajtson végre ezekkel a módszerekkel, amikor többtényezős hitelesítésre van szükség.

Ha egy rendszergazda engedélyezi az előzetes verziót, a felhasználók regisztrálnak az új felületen keresztül, majd a rendszergazda letiltja az előnézetet, a felhasználók tudatlanul regisztrálhatnak a többtényezős hitelesítéshez is.

Ha egy kombinált regisztrációt végző felhasználó a jelenlegi önkiszolgáló jelszó-visszaállítási [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)(SSPR) regisztrációs lapra lép a> on, a rendszer a többtényezős hitelesítést kéri, mielőtt elérnék az adott oldalt. Ez a lépés technikai szempontból várható, de ez az új felhasználók számára, akik korábban csak sspr regisztrált. Bár ez az extra lépés javítja a felhasználó biztonsági állapotát azáltal, hogy egy másik biztonsági szintet biztosít, a rendszergazdák érdemes lehet visszavonni a felhasználókat, hogy ők már nem képesek végrehajtani a többtényezős hitelesítést.  

### <a name="how-to-roll-back-users"></a>A felhasználók visszaállítása

Ha rendszergazdaként szeretné alaphelyzetbe állítani a felhasználó többtényezős hitelesítési beállításait, használhatja a következő szakaszban biztosított PowerShell-parancsfájlt. A parancsfájl törli a Felhasználó mobilalkalmazásának és/vagy telefonszámának StrongAuthenticationMethods tulajdonságát. Ha ezt a parancsfájlt a felhasználók számára futtatja, újra regisztrálniuk kell őket a többtényezős hitelesítéshez, ha szükségük van rá. Azt javasoljuk, hogy tesztelje a visszaállítást egy vagy két felhasználóval, mielőtt az összes érintett felhasználót visszaállítja.

Az alábbi lépések segítségével visszaállíthatja a felhasználókat vagy felhasználói csoportokat.

#### <a name="prerequisites"></a>Előfeltételek

1. Telepítse a megfelelő Azure AD PowerShell-modulokat. Egy PowerShell-ablakban futtassa ezeket a parancsokat a modulok telepítéséhez:

   ```powershell
   Install-Module -Name MSOnline
   Import-Module MSOnline
   ```

1. Mentse az érintett felhasználói objektumazonosítók listáját a számítógépre, soronként egy azonosítóval rendelkező szövegfájlként. Jegyezze fel a fájl helyét.
1. Mentse a következő parancsfájlt a számítógépre, és jegyezze fel a parancsfájl helyét:

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

#### <a name="rollback"></a>Visszagurítás

Egy PowerShell-ablakban futtassa a következő parancsot, megadva a parancsfájl és a felhasználói fájl helyét. Amikor a rendszer kéri, adja meg a globális rendszergazdai hitelesítő adatokat. A parancsfájl minden egyes felhasználói frissítési művelet eredményét adja ki.

`<script location> -path <user file location>`

### <a name="disable-the-updated-experience"></a>A frissített élmény letiltása

A felhasználók frissített élményének letiltásához hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be az Azure Portalon felhasználói rendszergazdaként.
2. Nyissa meg az **Azure Active Directory** > **felhasználói beállításai** > **: A hozzáférési panel előnézeti szolgáltatásainak kezelése című lapban.**
3. A **Felhasználók csoportban a biztonsági adatok regisztrálásához és kezeléséhez használhatjuk az előnézeti funkciókat,** állítsa a választógombot **Nincs**beállításra, majd válassza a **Mentés lehetőséget.**

A felhasználók a továbbiakban nem lesznek arra kérik, hogy a frissített felület használatával regisztráljanak.

## <a name="next-steps"></a>További lépések

* [További információ az önkiszolgáló jelszó-visszaállítás és az Azure többtényezős hitelesítésegyüttes regisztrációjáról](concept-registration-mfa-sspr-combined.md)

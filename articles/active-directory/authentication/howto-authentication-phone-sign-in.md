---
title: Jelszó nélküli jelentkezzen be a Microsoft Authenticator alkalmazás (előzetes verzió) – az Azure Active Directory
description: Jelentkezzen be Azure ad-bA a Microsoft Authenticator alkalmazást a jelszót (nyilvános előzetes verzió) használata nélkül
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.custom: seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 47e8541b82a1cd38f07684508a96b9789df20e92
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370382"
---
# <a name="password-less-phone-sign-in-with-the-microsoft-authenticator-app-public-preview"></a>Jelszó nélküli telefonnal bejelentkezni a Microsoft Authenticator alkalmazással (nyilvános előzetes verzió)

A Microsoft Authenticator alkalmazás segítségével jelentkezzen be minden olyan Azure AD-fiókot jelszó használata nélkül. A technológia, hasonló [Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-identity-verification), a Microsoft Authenticator-alapú hitelesítést használ a felhasználói hitelesítő adatokat, amelyek egy eszközhöz kötődik, és használja a biometrikus adatokat vagy PIN-kód engedélyezése.

![Egy böngészőben jelentkezzen be kéri a felhasználót, hogy a bejelentkezés jóváhagyása – példa](./media/howto-authentication-phone-sign-in/phone-sign-in-microsoft-authenticator-app.png)

Helyett jelenik meg a jelszó kérése a felhasználónév megadása után, a személy, aki engedélyezve van a telefonnal bejelentkezni a Microsoft Authenticator alkalmazásban megjelenik egy üzenet, postaládához, olyan szám, hogy az alkalmazás elemre. Az alkalmazásban a felhasználó kell egyezik meg a válassza a jóváhagyás, majd adja meg a PIN-kódjukat, vagy biometrikus, majd a hitelesítés befejezi.

## <a name="enable-my-users"></a>A felhasználók engedélyezése

Nyilvános előzetes verzióként, a rendszergazda először hozzá kell egy szabályzatot, engedélyezheti a hitelesítő adatok használatát a bérlőben a powershellen keresztül. Tekintse át a "Ismert problémák" szakasz ebben a lépésben állapotba helyezése előtt.

### <a name="tenant-prerequisites"></a>Bérlő Előfeltételek

* Azure Active Directory
* Az Azure multi-factor Authentication engedélyezése végfelhasználók számára
* Felhasználók regisztrálhatják eszközeiket

### <a name="steps-to-enable"></a>További lépések elvégzésével

1. Győződjön meg arról, hogy a nyilvános előzetes kiadását az Azure Active Directory V2 PowerShell-modul legújabb verzióját. Kezdésként érdemes lehet eltávolítani, majd telepítse a következő parancsok végrehajtásával ellenőrizheti:

    ```powershell
    Uninstall-Module -Name AzureADPreview
    Install-Module -Name AzureADPreview
    ```

2. Hitelesítés az Azure AD V2 PowerShell modul használata az Azure AD-bérlőhöz. A használt fiók biztonsági rendszergazdai vagy globális rendszergazdai vagy kell lennie.

    ```powershell
    Connect-AzureAD
    ```

3. Az Authenticator bejelentkezési szabályzat létrehozása:

    ```powershell
    New-AzureADPolicy -Type AuthenticatorAppSignInPolicy -Definition '{"AuthenticatorAppSignInPolicy":{"Enabled":true}}' -isOrganizationDefault $true -DisplayName AuthenticatorAppSignIn
    ```

## <a name="how-do-my-end-users-enable-phone-sign-in"></a>A végfelhasználók hogyan tegye lehetővé telefonnal bejelentkezni?

A nyilvános előzetes verziójához nincs lehetőség a felhasználók létrehozása vagy használata az új hitelesítő adatok érvényesítésére. A felhasználó csak a jelszó nélküli bejelentkezés fog történni, miután egy rendszergazda engedélyezte a bérlők és a felhasználó frissítette a telefonos bejelentkezés engedélyezése a Microsoft Authenticator alkalmazást.

> [!NOTE]
> Ez a funkció már évek óta az alkalmazásban 2017. március, így annak a lehetősége, hogy ha a szabályzat engedélyezve van a bérlők, felhasználók You may encounter ezzel a folyamattal azonnal. Vegye figyelembe, és a felhasználók a változás előkészítéséhez.
>

1. Az Azure multi-factor Authentication regisztrálása
1. A Microsoft Authenticator telepített iOS 8.0-s vagy újabb rendszert futtató eszközök vagy az Android 6.0-s vagy újabb legújabb verzióját.
1. Munkahelyi vagy iskolai fiók hozzáadása az alkalmazáshoz a leküldéses értesítésekkel. A végfelhasználói dokumentáció itt található [ https://aka.ms/authappstart ](https://aka.ms/authappstart).

Miután a felhasználó rendelkezik az MFA-fiókot állítsa be a Microsoft Authenticator alkalmazást a leküldéses értesítések, a cikk lépéseit követve [jelentkezzen be a telefonjára, nem kell jelszót](../user-help/microsoft-authenticator-app-phone-signin-faq.md) a telefonos bejelentkezést regisztráció befejezéséhez.

## <a name="known-issues"></a>Ismert problémák

### <a name="ad-fs-integration"></a>AD FS-integráció

Amikor egy felhasználó engedélyezte a Microsoft Authenticator jelszó nélküli hitelesítő adatokat, az adott felhasználó hitelesítési mindig alapértelmezés szerint egy jóváhagyási értesítést küld. A logikai meggátolja a felhasználókat a hibrid bérlő irányítja az ADFS-be a bejelentkezés-ellenőrző a felhasználó egy további lépést tart nélkül kattintson a "Inkább jelszó használata." Ez a folyamat is megkerülik bármilyen helyszíni feltételes hozzáférési szabályzatokat, és átmenő hitelesítési folyamatok. A kivétel ez alól egy login_hint-e megadva, a felhasználó autoforwarded az AD FS-hez, és megkerülése arra, hogy a jelszó nélküli hitelesítő adatokat használja.

### <a name="azure-mfa-server"></a>Az Azure MFA-kiszolgáló

Végfelhasználók, akik az MFA engedélyezve vannak a szervezet helyszíni Azure MFA-kiszolgálón keresztül továbbra is létrehozhat és egyetlen jelszó nélküli telefon bejelentkezési hitelesítő adatokat használja. Ha a felhasználó megpróbálja több telepített (5 +) frissítése a Microsoft Authenticator a hitelesítő adataival, a módosítás hibát eredményezhet.  

### <a name="device-registration"></a>Eszközregisztráció

Az előfeltételeket az új, erős hitelesítő adat, létrehozása egyik célja, hogy hol vannak az eszköz regisztrálva van-e belül az Azure AD-bérlővel, egy adott felhasználóhoz. Eszköz regisztrációs korlátozások miatt egy eszközhöz csak egy bérlőben lehet regisztrálni. Ezt a korlátot, az azt jelenti, hogy a csak egy munkahelyi vagy iskolai fiókkal, a Microsoft Authenticator alkalmazást a telefonos bejelentkezés esetén is engedélyezhető.

## <a name="next-steps"></a>További lépések

[Ismerje meg az eszköz regisztrálása](../devices/overview.md#getting-devices-under-the-control-of-azure-ad)

[További tudnivalók az Azure multi-factor Authentication](../authentication/howto-mfa-getstarted.md)

---
title: SMS-alapú felhasználói bejelentkezés Azure Active Directory
description: Megtudhatja, hogyan konfigurálhatja és engedélyezheti a felhasználók számára, hogy bejelentkezzenek Azure Active Directory SMS-ben (előzetes verzió)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rateller
ms.collection: M365-identity-device-management
ms.openlocfilehash: 450e6aa1b22806fbd4d142e47caee720d7f63648
ms.sourcegitcommit: 95269d1eae0f95d42d9de410f86e8e7b4fbbb049
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/26/2020
ms.locfileid: "83860171"
---
# <a name="configure-and-enable-users-for-sms-based-authentication-using-azure-active-directory-preview"></a>Felhasználók konfigurálása és engedélyezése SMS-alapú hitelesítéshez Azure Active Directory használatával (előzetes verzió)

A felhasználók által az alkalmazásokhoz és szolgáltatásokhoz való bejelentkezés bonyolultságának és biztonsági kockázatának csökkentése érdekében Azure Active Directory (Azure AD) több hitelesítési lehetőséget is biztosít. A jelenleg előzetes verzióban elérhető SMS-alapú hitelesítés lehetővé teszi a felhasználók számára, hogy a Felhasználónév és a jelszó megadása nélkül is bejelentkezzenek. Miután a fiókját egy identitás-rendszergazda létrehozta, megadhatják a telefonszámot a bejelentkezési parancssorban, és egy szöveges üzenetben elküldött hitelesítő kódot adhatnak meg. Ez a hitelesítési módszer leegyszerűsíti az alkalmazásokhoz és szolgáltatásokhoz való hozzáférést, különösen az első sorban dolgozóknak.

Ez a cikk bemutatja, hogyan engedélyezhető az SMS-alapú hitelesítés az Azure AD-beli felhasználók vagy csoportok kiválasztásához.

|     |
| --- |
| Az SMS-alapú hitelesítés a felhasználók számára a Azure Active Directory egy nyilvános előzetes funkciója. További információ az előzetes verziókról: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="before-you-begin"></a>Előkészületek

A cikk elvégzéséhez a következő erőforrásokra és jogosultságokra van szüksége:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Az előfizetéshez társított Azure Active Directory bérlő.
    * Ha szükséges, [hozzon létre egy Azure Active Directory bérlőt][create-azure-ad-tenant] , vagy [rendeljen hozzá egy Azure-előfizetést a fiókjához][associate-azure-ad-tenant].
* Az SMS-alapú hitelesítés engedélyezéséhez *globális rendszergazdai* jogosultságok szükségesek az Azure ad-bérlőben.
* Az SMS-hitelesítési módszer házirendjében engedélyezett összes felhasználónak licenccel kell rendelkeznie, még akkor is, ha nem használják azt. Minden engedélyezett felhasználónak rendelkeznie kell a következő Azure AD-, EMS-és Microsoft 365-licencek egyikével:
    * [prémium szintű Azure AD P1 vagy P2][azuread-licensing]
    * [Microsoft 365 (M365) F1 vagy F3][m365-firstline-workers-licensing]
    * [Enterprise Mobility + Security (EMS) E3 vagy E5][ems-licensing] vagy [Microsoft 365 (M365) E3 vagy E5][m365-licensing]

## <a name="limitations"></a>Korlátozások

Az SMS-alapú hitelesítés nyilvános előzetese alatt a következő korlátozások érvényesek:

* Az SMS-alapú hitelesítés jelenleg nem kompatibilis az Azure Multi-Factor Authenticationával.
* A csapatok kivételével az SMS-alapú hitelesítés jelenleg nem kompatibilis a natív Office-alkalmazásokkal.
* VÁLLALATKÖZI fiókok esetén nem ajánlott SMS-alapú hitelesítés.
* Az összevont felhasználók nem fognak hitelesíteni a Kezdőlap bérlőben. Csak a felhőben végzik a hitelesítést.

## <a name="enable-the-sms-based-authentication-method"></a>SMS-alapú hitelesítési módszer engedélyezése

A szervezeten belül az SMS-alapú hitelesítés engedélyezése és használata három fő lépésből áll:

* Engedélyezze a hitelesítési módszer házirendjét.
* Válassza ki azokat a felhasználókat vagy csoportokat, akik használhatják az SMS-alapú hitelesítési módszert.
* Rendeljen hozzá egy telefonszámot minden egyes felhasználói fiókhoz.
    * Ez a telefonszám a Azure Portalban (amely ebben a cikkben látható), valamint a *saját munkatársaiban* vagy *a saját profilban*is hozzárendelhető.

Először is engedélyezzük az SMS-alapú hitelesítést az Azure AD-bérlő számára.

1. Jelentkezzen be a [Azure Portal][azure-portal] *globális rendszergazdaként*.
1. Keresse meg és válassza ki az **Azure Active Directoryt**.
1. A Azure Active Directory ablak bal oldali navigációs menüjében válassza a **biztonsági > hitelesítési módszerek > a hitelesítési módszer házirendje (előzetes verzió)** lehetőséget.

    [![](media/howto-authentication-sms-signin/authentication-method-policy-cropped.png "Browse to and select the Authentication method policy (preview) window in the Azure portal")](media/howto-authentication-sms-signin/authentication-method-policy.png#lightbox)

1. Az elérhető hitelesítési módszerek listájából válassza a **szöveges üzenet**lehetőséget.
1. Állítsa az **Engedélyezés** beállítást *Igen*értékre.

    ![Szöveges hitelesítés engedélyezése a hitelesítési módszer házirend ablakában](./media/howto-authentication-sms-signin/enable-text-authentication-method.png)

    Dönthet úgy is, hogy engedélyezi az SMS-alapú hitelesítést *minden felhasználó* számára, vagy *kiválasztja a felhasználók* és csoportok lehetőséget. A következő szakaszban engedélyezheti az SMS-alapú hitelesítést egy teszt felhasználó számára.

## <a name="assign-the-authentication-method-to-users-and-groups"></a>A hitelesítési módszer társítása felhasználókhoz és csoportokhoz

Ha az SMS-alapú hitelesítés engedélyezve van az Azure AD-bérlőben, most válassza ki azokat a felhasználókat vagy csoportokat, akik számára engedélyezni szeretné ezt a hitelesítési módszert.

1. A szöveges üzenet hitelesítési házirendje ablakban állítsa a **cél** *elemet a felhasználók kiválasztásához*.
1. Válassza a **felhasználók vagy csoportok hozzáadása**lehetőséget, majd válasszon ki egy teszt felhasználót vagy csoportot, például *contoso felhasználói* vagy *contoso SMS-felhasználókat*.

    [![](media/howto-authentication-sms-signin/add-users-or-groups-cropped.png "Choose users or groups to enable for SMS-based authentication in the Azure portal")](media/howto-authentication-sms-signin/add-users-or-groups.png#lightbox)

1. Ha kiválasztotta a felhasználókat vagy csoportokat, válassza a **kiválasztás**lehetőséget, majd **mentse** a frissített hitelesítési módszer házirendet.

Az SMS-hitelesítési módszer házirendjében engedélyezett összes felhasználónak licenccel kell rendelkeznie, még akkor is, ha nem használják azt. Győződjön meg arról, hogy rendelkezik a megfelelő licenccel a hitelesítési módszer házirendjében engedélyezett felhasználókhoz, különösen akkor, ha a szolgáltatást nagy felhasználói csoportok esetében engedélyezi.

## <a name="set-a-phone-number-for-user-accounts"></a>Telefonszám beállítása felhasználói fiókokhoz

A felhasználók most már engedélyezve vannak az SMS-alapú hitelesítéshez, de a bejelentkezés előtt hozzá kell rendelni a felhasználói profilhoz tartozó telefonszámot az Azure AD-ben. A felhasználó a *saját profilban* [állíthatja be ezt a telefonszámot](../user-help/sms-sign-in-explainer.md) , vagy a Azure Portal használatával is hozzárendelheti a telefonszámot. A telefonszámokat a *globális rendszergazdák*, a *hitelesítési rendszergazdák*vagy a *privilegizált hitelesítési rendszergazdák*is megadhatják.

Ha az SMS-Sign telefonszáma be van állítva, akkor az [Azure multi-Factor Authentication][tutorial-azure-mfa] és az [önkiszolgáló jelszó-visszaállítás][tutorial-sspr]szolgáltatáshoz is használható.

1. Keresse meg és válassza ki az **Azure Active Directoryt**.
1. A Azure Active Directory ablak bal oldali navigációs menüjében válassza a **felhasználók**lehetőséget.
1. Válassza ki az SMS-alapú hitelesítéshez engedélyezett felhasználót az előző szakaszban (például contoso- *felhasználó*), majd válassza a **hitelesítési módszerek**lehetőséget.
1. Adja meg a felhasználó telefonszámát, beleértve az országkód *(például + 1 XXXXXXXXX*) értéket. A Azure Portal ellenőrzi, hogy a telefonszám formátuma megfelelő-e.

    ![Az SMS-alapú hitelesítéshez használandó Azure Portal telefonszámának beállítása a felhasználó számára](./media/howto-authentication-sms-signin/set-user-phone-number.png)

    A telefonszámnak egyedinek kell lennie a bérlőben. Ha több felhasználóhoz ugyanazt a telefonszámot próbálja használni, hibaüzenet jelenik meg.

1. Ha a telefonszámot egy felhasználói fiókra szeretné alkalmazni, válassza a **Mentés**lehetőséget.

A sikeres kiépítés után egy pipa jelenik meg az *SMS-bejelentkezés engedélyezéséhez*.

## <a name="test-sms-based-sign-in"></a>SMS-alapú bejelentkezés tesztelése

Az SMS-alapú bejelentkezéshez már engedélyezett felhasználói fiók teszteléséhez hajtsa végre a következő lépéseket:

1. Nyisson meg egy új InPrivate-vagy inkognitóban-böngészőablakot a következőhöz[https://www.office.com][office]
1. A jobb felső sarokban válassza a **Bejelentkezés**lehetőséget.
1. A bejelentkezési kérésben adja meg az előző szakaszban a felhasználóhoz társított telefonszámot, majd kattintson a **tovább**gombra.

    ![Adjon meg egy telefonszámot a tesztelési felhasználó bejelentkezési kéréséhez](./media/howto-authentication-sms-signin/sign-in-with-phone-number.png)

1. A rendszer szöveges üzenetet küld a megadott telefonszámra. A bejelentkezési folyamat befejezéséhez adja meg a szöveges üzenetben megadott 6 számjegyű kódot a bejelentkezési kérésben.

    ![Adja meg a szöveges üzeneten keresztül küldött megerősítő kódot a felhasználó telefonszámára.](./media/howto-authentication-sms-signin/sign-in-with-phone-number-confirmation-code.png)

1. A felhasználó mostantól nem kell felhasználónevet vagy jelszót megadnia.

## <a name="troubleshoot-sms-based-sign-in"></a>SMS-alapú bejelentkezés – problémamegoldás

A következő forgatókönyvek és hibaelhárítási lépések akkor használhatók, ha problémák merülnek fel az SMS-alapú bejelentkezés engedélyezésével és használatával kapcsolatban.

### <a name="phone-number-already-set-for-a-user-account"></a>Egy felhasználói fiókhoz már be van állítva a telefonszám

Ha egy felhasználó már regisztrálva van az Azure Multi-Factor Authentication és/vagy az önkiszolgáló jelszó-visszaállítás (SSPR) szolgáltatásban, akkor már rendelkezik a fiókjához rendelt telefonszámmal. Ez a telefonszám nem érhető el automatikusan SMS-alapú bejelentkezéssel való használatra.

Egy olyan felhasználó, aki már beállított egy telefonszámot a fiókjához, megjelenik egy gomb, amely *lehetővé teszi az SMS-bejelentkezést* a saját **profil** lapon. Válassza ezt a gombot, és a fiók engedélyezve van az SMS-alapú bejelentkezéshez és az előző Azure Multi-Factor Authentication vagy SSPR-regisztrációhoz.

További információ a végfelhasználói élményről: [SMS bejelentkezési felhasználói élmény telefonszámra (előzetes verzió)](../user-help/sms-sign-in-explainer.md).

### <a name="error-when-trying-to-set-a-phone-number-on-a-users-account"></a>Hiba történt a felhasználói fiókhoz tartozó telefonszám beállításakor

Ha hibaüzenet jelenik meg, amikor a Azure Portal egy felhasználói fiókhoz próbál meg telefonszámot beállítani, tekintse át a következő hibaelhárítási lépéseket:

1. Győződjön meg arról, hogy engedélyezve van az SMS-alapú bejelentkezési előzetes verzió.
1. Győződjön meg arról, hogy a felhasználói fiók engedélyezve van a *szöveges üzenet* hitelesítési módszerének házirendjében.
1. Győződjön meg arról, hogy a telefonszámot a megfelelő formázással állítja be, ahogy az a Azure Portalban van érvényesítve (például *+ 1 4251234567*).
1. Győződjön meg arról, hogy a telefonszám nem használatos a bérlő más részén.
1. Győződjön meg arról, hogy a fiókhoz nincs beállítva hangszám. Ha a hangszám be van állítva, törölje a telefonszámot, és próbálkozzon újra.

## <a name="next-steps"></a>További lépések

Az Azure AD-ba való bejelentkezés további módjai jelszó nélkül, például a Microsoft Authenticator alkalmazás-vagy FIDO2 biztonsági kulcsainak használata: az [Azure ad-hez készült jelszó nélküli hitelesítési beállítások][concepts-passwordless].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../fundamentals/active-directory-how-subscriptions-associated-directory.md
[concepts-passwordless]: concept-authentication-passwordless.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[tutorial-sspr]: tutorial-enable-sspr.md

<!-- EXTERNAL LINKS -->
[azure-portal]: https://portal.azure.com
[office]: https://www.office.com
[m365-firstline-workers-licensing]: https://www.microsoft.com/licensing/news/m365-firstline-workers
[azuread-licensing]: https://azure.microsoft.com/pricing/details/active-directory/
[ems-licensing]: https://www.microsoft.com/microsoft-365/enterprise-mobility-security/compare-plans-and-pricing
[m365-licensing]: https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans
[o365-f1]: https://www.microsoft.com/microsoft-365/business/office-365-f1?market=af
[o365-f3]: https://www.microsoft.com/microsoft-365/business/office-365-f3?activetab=pivot%3aoverviewtab

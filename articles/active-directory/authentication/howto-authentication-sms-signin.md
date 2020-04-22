---
title: SMS-alapú felhasználó bejelentkezés az Azure Active Directoryba
description: Megtudhatja, hogy miként konfigurálhatja és engedélyezheti a felhasználóknak, hogy SMS-ben jelentkezzenek be az Azure Active Directoryba (előzetes verzió)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rateller
ms.collection: M365-identity-device-management
ms.openlocfilehash: 845948d9aec28ee79a11fb11aaef4cfbf1b263fa
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770554"
---
# <a name="configure-and-enable-users-for-sms-based-authentication-using-azure-active-directory-preview"></a>A felhasználók konfigurálása és engedélyezése SMS-alapú hitelesítéshez az Azure Active Directory használatával (előzetes verzió)

Az Azure Active Directory (Azure AD) több hitelesítési lehetőséget biztosít az alkalmazásokba és szolgáltatásokba való bejelentkezés összetettségének és biztonsági kockázatainak csökkentése érdekében. A jelenleg előzetes verzióban elérhető SMS-alapú hitelesítés lehetővé teszi a felhasználók bejelentkezését anélkül, hogy meg kellene adniuk, vagy akár tudniuk kellene felhasználónevüket és jelszavukat. Miután a fiókjukat egy identitáskezelő hozta létre, megadhatja a telefonszámát a bejelentkezési kérdésben, és megadhat egy hitelesítési kódot, amelyet szöveges üzenetben küld nekik. Ez a hitelesítési módszer leegyszerűsíti az alkalmazásokhoz és szolgáltatásokhoz való hozzáférést, különösen a frontvonalban dolgozók számára.

Ez a cikk bemutatja, hogyan engedélyezheti az SMS-alapú hitelesítést az Azure AD bizonyos felhasználói vagy csoportjai számára.

|     |
| --- |
| Az SMS-alapú hitelesítés a felhasználók számára az Azure Active Directory nyilvános előzetes verziójú szolgáltatása. Az előzetes verziókról további információt a [Kiegészítő használati feltételek a Microsoft Azure előzetes verzióihoz című](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) témakörben talál.|
|     |

## <a name="before-you-begin"></a>Előkészületek

A cikk végrehajtásához a következő erőforrásokra és jogosultságokra van szükség:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Az előfizetéshez társított Azure Active Directory-bérlő.
    * Szükség esetén [hozzon létre egy Azure Active Directory-bérlőt,][create-azure-ad-tenant] vagy [társítson egy Azure-előfizetést a fiókjához.][associate-azure-ad-tenant]
* Az SMS-alapú hitelesítés engedélyezéséhez *globális rendszergazdai* jogosultságokra van szüksége az Azure AD-bérlőben.
* Minden olyan felhasználónak, aki engedélyezve van a szöveges üzenet hitelesítési módszer házirendkell licenccel, akkor is, ha nem használja. Minden engedélyezett felhasználónak rendelkeznie kell az alábbi Azure AD- vagy Microsoft 365-licencekkel:
    * [Azure AD Premium P1 vagy P2][azuread-licensing]
    * [Microsoft 365 (M365) F1 vagy F3][m365-firstline-workers-licensing]
    * [Enterprise Mobility + Security (EMS) E3 vagy E5][ems-licensing] vagy [Microsoft 365 (M365) E3 vagy E5][m365-licensing]

## <a name="limitations"></a>Korlátozások

Az SMS-alapú hitelesítés nyilvános előzetes verziója során a következő korlátozások érvényesek:

* Az SMS-alapú hitelesítés jelenleg nem kompatibilis az Azure többtényezős hitelesítésével.
* A Teams kivételével az SMS-alapú hitelesítés jelenleg nem kompatibilis a natív Office-alkalmazásokkal.
* Az SMS-alapú hitelesítés nem ajánlott B2B fiókok esetén.
* Az összevont felhasználók nem hitelesítik magukat az otthoni bérlőben. Csak a felhőben hitelesítik magukat.

## <a name="enable-the-sms-based-authentication-method"></a>Az SMS-alapú hitelesítési módszer engedélyezése

Az SMS-alapú hitelesítés engedélyezésének és használatának három fő lépése van a szervezetben:

* Engedélyezze a hitelesítési módszer házirendjének engedélyezését.
* Válassza ki azokat a felhasználókat vagy csoportokat, amelyek használhatják az SMS-alapú hitelesítési módszert.
* Rendeljen telefonszámot minden felhasználói fiókhoz.
    * Ez a telefonszám rendelhető az Azure Portalon (amely ebben a cikkben látható), valamint a *Saját személyzet* vagy a Saját *profil .*

Először engedélyezze az SMS-alapú hitelesítést az Azure AD-bérlőhöz.

1. Jelentkezzen be az [Azure Portalra][azure-portal] *globális rendszergazdaként.*
1. Keresse meg és válassza ki az **Azure Active Directoryt**.
1. Az Azure Active Directory ablakbal oldali navigációs menüjében válassza a **Security > Authentication metódusok > hitelesítési metódusházirend (előzetes verzió)** lehetőséget.

    [![](media/howto-authentication-sms-signin/authentication-method-policy-cropped.png "Browse to and select the Authentication method policy (preview) window in the Azure portal")](media/howto-authentication-sms-signin/authentication-method-policy.png#lightbox)

1. Az elérhető hitelesítési módszerek listájában válassza a **Szöveges üzenet**lehetőséget.
1. Állítsa **az Engedélyezés** t *Igen*-re.

    ![Szöveghitelesítés engedélyezése a hitelesítési módszer házirendablakában](./media/howto-authentication-sms-signin/enable-text-authentication-method.png)

    Engedélyezheti az SMS-alapú hitelesítést *minden felhasználó* számára, vagy kiválaszthatja a *felhasználókat* és csoportokat. A következő szakaszban engedélyezi az SMS-alapú hitelesítést egy tesztfelhasználó számára.

## <a name="assign-the-authentication-method-to-users-and-groups"></a>A hitelesítési módszer hozzárendelése felhasználókhoz és csoportokhoz

Ha az SMS-alapú hitelesítés engedélyezve van az Azure AD-bérlőben, most válassza ki azokat a felhasználókat vagy csoportokat, akik használhatják ezt a hitelesítési módszert.

1. A szöveges üzenet hitelesítési házirendablakában állítsa a **Cél** választó felhasználók *lehetőséget.*
1. Válassza a **Felhasználók vagy csoportok hozzáadása**lehetőséget, majd válasszon ki egy tesztfelhasználót vagy csoportot, például a Contoso User vagy a *Contoso* *SMS Users lehetőséget.*

    [![](media/howto-authentication-sms-signin/add-users-or-groups-cropped.png "Choose users or groups to enable for SMS-based authentication in the Azure portal")](media/howto-authentication-sms-signin/add-users-or-groups.png#lightbox)

1. Miután kiválasztotta a felhasználókat vagy csoportokat, válassza a **Kijelölés**lehetőséget, majd **mentse** a frissített hitelesítési módszer házirendjét.

Minden olyan felhasználónak, aki engedélyezve van a szöveges üzenet hitelesítési módszer házirendkell licenccel, akkor is, ha nem használja. Győződjön meg arról, hogy rendelkezik a megfelelő licencekkel a hitelesítési módszer házirendjében engedélyezett felhasználók számára, különösen akkor, ha a szolgáltatást nagy felhasználói csoportok számára engedélyezi.

## <a name="set-a-phone-number-for-user-accounts"></a>Telefonszám beállítása a felhasználói fiókokhoz

A felhasználók most már engedélyezve vannak az SMS-alapú hitelesítéshez, de a telefonszámukat az Azure AD felhasználói profiljához kell társálni, mielőtt bejelentkezhetnek. A felhasználó [beállíthatja ezt a telefonszámot maguk](../user-help/sms-sign-in-explainer.md) a *Saját profil*, vagy hozzárendelheti a telefonszámot az Azure Portalon. A telefonszámokat *globális rendszergazdák*, *hitelesítési rendszergazdák*vagy kiemelt *hitelesítési rendszergazdák állíthatják*be.

Ha egy telefonszám sms-jelre van beállítva, akkor az [azure-os többtényezős hitelesítéssel][tutorial-azure-mfa] és [önkiszolgáló jelszó-visszaállítással][tutorial-sspr]is használható.

1. Keresse meg és válassza ki az **Azure Active Directoryt**.
1. Az Azure Active Directory ablak bal oldalán található navigációs menüben válassza a **Felhasználók**lehetőséget.
1. Válassza ki az előző szakaszban az SMS-alapú hitelesítéshez engedélyezett felhasználót, például *a Contoso User lehetőséget,* majd válassza a **Hitelesítési módszerek**lehetőséget.
1. Adja meg a felhasználó telefonszámát, az országkódot is beleértve, például *+1 xxxxxxxxx*. Az Azure Portal ellenőrzi, hogy a telefonszám a megfelelő formátumú.

    ![Az Azure Portalon egy sms-alapú hitelesítéssel használható felhasználó telefonszámának beállítása](./media/howto-authentication-sms-signin/set-user-phone-number.png)

    A telefonszámnak egyedinek kell lennie a bérlőben. Ha ugyanazt a telefonszámot próbálja használni több felhasználó számára, hibaüzenet jelenik meg.

1. Ha a telefonszámot egy felhasználó fiókjára szeretné alkalmazni, válassza a **Mentés gombot.**

Sikeres kiépítés esetén egy pipa jelenik meg az *SMS bejelentkezés engedélyezve.*

## <a name="test-sms-based-sign-in"></a>SMS-alapú bejelentkezés tesztelése

Az SMS-alapú bejelentkezéshez engedélyezett felhasználói fiók teszteléséhez hajtsa végre az alábbi lépéseket:

1. Új InPrivate- vagy inkognitóböngésző ablak megnyitása[https://www.office.com][office]
1. A jobb felső **sarokban**válassza a Bejelentkezés lehetőséget.
1. A bejelentkezési kérdésben írja be a felhasználóhoz társított telefonszámot az előző szakaszban, majd válassza a **Tovább**gombot.

    ![Adja meg a telefonszámot a bejelentkezési kérdés a teszt felhasználó](./media/howto-authentication-sms-signin/sign-in-with-phone-number.png)

1. A rendszer szöveges üzenetet küld a megadott telefonszámra. A bejelentkezési folyamat befejezéséhez írja be a szöveges üzenetben megadott hatjegyű kódot a bejelentkezési kérdésben.

    ![Adja meg a felhasználó telefonszámára szöveges üzenetben küldött megerősítő kódot](./media/howto-authentication-sms-signin/sign-in-with-phone-number-confirmation-code.png)

1. A felhasználó most már be van jelentkezve anélkül, hogy felhasználónevet vagy jelszót kellene megadnia.

## <a name="troubleshoot-sms-based-sign-in"></a>SMS-alapú bejelentkezés – problémamegoldás

A következő forgatókönyvek és hibaelhárítási lépések akkor használhatók, ha problémái vannak az SMS-alapú bejelentkezés engedélyezésével és használatával.

### <a name="phone-number-already-set-for-a-user-account"></a>A felhasználói fiókhoz már beállított telefonszám

Ha egy felhasználó már regisztrált az Azure többtényezős hitelesítésre és / vagy az önkiszolgáló jelszó-visszaállításra (SSPR), akkor már rendelkezik a fiókjához társított telefonszámmal. Ez a telefonszám nem érhető el automatikusan SMS-alapú bejelentkezéshez.

Az a felhasználó, akinek már be van állítva a telefonszáma a fiókjához, megjelenik egy gomb, *amelyen engedélyezze az SMS-bejelentkezést* a **Saját profil** lapon. Válassza ezt a gombot, és a fiók engedélyezve van az SMS-alapú bejelentkezéshez és a korábbi Azure többtényezős hitelesítéshez vagy SSPR-regisztrációhoz.

A végfelhasználói élményről további információt az [SMS-bejelentkezési felhasználói élmény ben talál a telefonszámhoz (előzetes verzió)](../user-help/sms-sign-in-explainer.md).

### <a name="error-when-trying-to-set-a-phone-number-on-a-users-account"></a>Hiba, amikor egy felhasználó fiókjában próbál beállítani egy telefonszámot

Ha hibaüzenetet kap, amikor megpróbál beállítani egy telefonszámot egy felhasználói fiókhoz az Azure Portalon, tekintse át az alábbi hibaelhárítási lépéseket:

1. Győződjön meg arról, hogy engedélyezve van az SMS-alapú bejelentkezési előnézet.
1. Ellenőrizze, hogy a felhasználói fiók engedélyezve van-e a *Szöveges üzenet* hitelesítési metódusházirendjében.
1. Győződjön meg arról, hogy a telefonszámot a megfelelő formázással állította be, az Azure Portalon érvényesítve (például *+1 4251234567).*
1. Győződjön meg arról, hogy a telefonszámot nem használják máshol a bérlőben.
1. Ellenőrizze, hogy nincs-e hangszám a számlán. Ha be van állítva egy hangszám, törölje a delete t, majd próbálja meg újra a telefonszámmal.

## <a name="next-steps"></a>További lépések

Az Azure AD-be jelszó nélkül való bejelentkezés további módjait, például a Microsoft Authenticator alkalmazást vagy a FIDO2 biztonsági kulcsokat az [Azure AD jelszó nélküli hitelesítési beállításai című][concepts-passwordless]témakörben található.

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

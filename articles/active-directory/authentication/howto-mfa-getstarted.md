---
title: Első lépések az Azure MFA a felhőben
description: A Microsoft Azure multi-factor Authentication – első lépések a feltételes hozzáférés
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 09/01/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: 07e9126b73b0282f61567fe8d06f785bf9a04fef
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48803477"
---
# <a name="deploy-cloud-based-azure-multi-factor-authentication"></a>Felhőalapú Azure multi-factor Authentication szolgáltatás üzembe helyezése

Ismerkedés az Azure multi-factor Authentication (az Azure MFA) egy olyan egyszerű folyamat.

Mielőtt elkezdené, győződjön meg arról, az alábbi előfeltételek:

* Egy globális rendszergazdai fiókkal az Azure AD-bérlőben. Ha a lépés befejezése segítségre van szüksége, tekintse meg a cikket [első lépései az Azure ad-vel](../get-started-azure-ad.md).
* Helyes a felhasználókhoz rendelt licenceket. Ha további információra van szüksége, tekintse meg a cikket [beszerzése az Azure multi-factor Authentication](concept-mfa-licensing.md).

## <a name="choose-how-to-enable"></a>Válassza ki a engedélyezése

**Feltételes hozzáférési szabályzat által engedélyezett** – Ez a módszer van a cikkben leírtak szerint. Célszerű a legrugalmasabb, hogy engedélyezze a kétlépéses ellenőrzést, a felhasználók számára. Engedélyezése csak a feltételes hozzáférési szabályzat segítségével az Azure multi-factor Authentication a felhőben működik, és az Azure AD prémium szintű szolgáltatása.

**Az Azure AD Identity Protection által engedélyezett** – ezt a módszert használja az Azure AD Identity Protection kockázati szabályzat minden felhőalapú alkalmazásra bejelentkezési kockázat alapján a kétlépéses ellenőrzés megkövetelése. Ez a metódus szükséges, Azure Active Directory P2 licencelési. Ez a módszer további információ található [konfigurálása a felhasználói kockázati házirend](../identity-protection/howto-user-risk-policy.md).

**Engedélyezve van a felhasználói állapot módosításával** – Ez az a kétlépéses ellenőrzést igénylő hagyományos módszere. Mind a felhőbeli Azure MFA és az Azure MFA-kiszolgáló működik. Ezzel a módszerrel megköveteli a felhasználóktól a kétlépéses ellenőrzés végrehajtására **minden alkalommal, amikor** jelentkezzen be, és felülírja a feltételes hozzáférési szabályzatokat. Ez a módszer további információ található [igénylése a kétlépéses ellenőrzés egy felhasználó](howto-mfa-userstates.md).

> [!Note]
> További információ a licencekkel és a díjszabás találhatók a [Azure ad-ben](https://azure.microsoft.com/pricing/details/active-directory/
) és [multi-factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) díjszabási lapjait.

## <a name="choose-authentication-methods"></a>Válassza ki a hitelesítési módszerek

Engedélyezze a felhasználók a szervezet követelményei alapján legalább egy hitelesítési módszert. Találtunk, hogy ha engedélyezett a felhasználók a Microsoft Authenticator alkalmazást a legjobb felhasználói élményt biztosít. Ha szeretné megérteni, milyen módszerek érhetők el, és hogyan állíthatja be azokat, tekintse meg a cikket [hitelesítési módszerei](concept-authentication-methods.md).

## <a name="get-users-to-enroll"></a>Felhasználók regisztrálása

Miután engedélyezte a feltételes hozzáférési szabályzatot, felhasználókat kényszeríti a szabályzattal védett alkalmazás használata a következő alkalommal regisztrálni. Ha engedélyezte a többtényezős hitelesítés minden felhőalapú alkalmazásra összes felhasználója számára szabályzat, ez a művelet támadó fejfájást okozhat a felhasználók és a támogatási szolgálathoz. Az javasoljuk, hogy kérje meg a felhasználóknak regisztrálniuk a felhasználásával a regisztrációs portálon, a hitelesítési módszerek [ https://aka.ms/mfasetup ](https://aka.ms/mfasetup). Számos szervezet találja, hogy poszterek, a tábla kártyák és az e-mailek segít a meghajtó bevezetését.

## <a name="enable-multi-factor-authentication-with-conditional-access"></a>A feltételes hozzáférés többtényezős hitelesítés engedélyezése

Jelentkezzen be a [az Azure portal](https://portal.azure.com) egy globális rendszergazdai fiókkal.

### <a name="choose-verification-options"></a>Ellenőrzési beállítások kiválasztása

Mielőtt engedélyezné az Azure multi-factor Authentication szolgáltatás, a szervezet kell határoznia, milyen ellenőrzési beállítások lehetővé teszik. Ebben a gyakorlatban céljából engedélyezi a telefonszám és a szöveges üzenet telefonjára hívás azok általános beállítások, hogy a legtöbb egyaránt használhatják. Hitelesítési módszerek és a használatuk, kapcsolatos további információkat a cikkben található [hitelesítési módszerei?](concept-authentication-methods.md)

1. Keresse meg a **az Azure Active Directory**, **felhasználók**, **multi-factor Authentication**.

   ![A multi-factor Authentication-portál elérése az Azure AD-felhasználók panel az Azure Portalon](media/howto-mfa-getstarted/users-mfa.png)

1. Az új lapon megnyíló keresse meg a **Szolgáltatásbeállítások**.
1. A **ellenőrzési lehetőségek**, jelölje be a felhasználók által választható módszerek mindegyike.

   ![Ellenőrzési módszerek konfigurálása a multi-factor Authentication szolgáltatás beállítások lap](media/howto-mfa-getstarted/mfa-servicesettings-verificationoptions.png)

4. Kattintson a **Mentés** gombra.
5. Zárja be a **Szolgáltatásbeállítások** fülre.

### <a name="create-conditional-access-policy"></a>Feltételes hozzáférési szabályzat létrehozása

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) egy globális rendszergazdai fiókkal.
1. Lépjen az **Azure Active Directory**, **Feltételes hozzáférés** területre.
1. Válassza ki **új szabályzat**.
1. Adjon meg egy kifejező nevet a szabályzathoz.
1. A **felhasználók és csoportok**:
   * Az a **Belefoglalás** lapon jelölje be a **minden felhasználó** választógomb
   * AJÁNLOTT: A a **kizárása** lapra, jelölje be a **felhasználók és csoportok** , és válasszon egy csoportot, amikor a felhasználók nem rendelkeznek hozzáféréssel a hitelesítési módszereket kizárások használandó.
   * Kattintson a **Done** (Kész) gombra.
1. A **Felhőalkalmazások**, jelölje be a **az összes felhőalapú alkalmazások** választógombot.
   * Választható: A a **kizárása** adja meg, hogy a szervezetnek nincs szüksége MFA felhőalapú alkalmazásokat.
   * Kattintson a **Done** (Kész) gombra.
1. A **feltételek** szakaszban:
   * Igény szerint: Ha engedélyezte az Azure Identity Protection, ha szeretné, bejelentkezési kockázat kiértékelése a szabályzat részeként.
   * Szükség esetén: Ha megbízható helyekre konfigurált és nevesített helyek, megadhatja vagy kizárja a ezeken a helyeken a szabályzat alól.
1. A **Grant**, győződjön meg arról, a **hozzáférést** választógomb van kiválasztva.
    * Jelölje be a **többtényezős hitelesítés megkövetelése**.
    * Kattintson a **Kiválasztás** gombra.
1. Ugrás a **munkamenet** szakaszban.
1. Állítsa be a **házirend engedélyezése** kapcsolót **a**.
1. Kattintson a **Create** (Létrehozás) gombra.

![Az MFA engedélyezése az Azure portálon a felhasználóknak a próbaüzemi csoport feltételes hozzáférési szabályzat létrehozása](media/howto-mfa-getstarted/conditionalaccess-newpolicy.png)

### <a name="test-azure-multi-factor-authentication"></a>Az Azure Multi-Factor Authentication tesztelése

Annak ellenőrzéséhez, hogy működik-e a feltételes hozzáférési szabályzatot, tesztelje a többtényezős hitelesítés elvégzéséhez nem szükséges erőforrás és az Azure Portalra, amely többtényezős Hitelesítést követel meg.

1. Nyisson meg egy új böngészőablakot InPrivate vagy inkognitó módban, és lépjen a következő oldalra: [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com).
   * Jelentkezzen be a tesztfelhasználó számára ez a cikk, és vegye figyelembe, hogy azt nem kérdezze meg, hogy végezze el az MFA Előfeltételek szakaszában létrehozott.
   * Zárja be a böngészőablakot.
2. Nyisson meg egy új böngészőablakot InPrivate vagy inkognitó módban, és lépjen a következő oldalra: [https://portal.azure.com](https://portal.azure.com).
   * Jelentkezzen be a teszt részeként ez a cikk, és vegye figyelembe, hogy most már Előfeltételek szakaszában létrehozott felhasználói regisztráljon, és az Azure multi-factor Authentication használata szükséges.
   * Zárja be a böngészőablakot.

## <a name="next-steps"></a>További lépések

Gratulálunk, az meg van adva az Azure multi-factor Authentication a felhőben.

További beállítások, például a megbízható IP-címek, egyedi Hangüzenetek és visszaélési riasztás küldésének, lásd: a cikk [konfigurálása az Azure multi-factor Authentication szolgáltatás beállításainak](howto-mfa-mfasettings.md).

Felhasználói beállítások kezelése az Azure multi-factor Authentication a cikkben található információ [az Azure multi-factor Authentication a felhőben a felhasználói beállítások kezelése](howto-mfa-userdevicesettings.md).

[A hiperkonvergens regisztráció az Azure multi-factor Authentication és az Azure AD önkiszolgáló jelszó-visszaállítás engedélyezése](concept-registration-mfa-sspr-converged.md).

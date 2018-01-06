---
title: "Rendszergazdai fiókok sürgős-hozzáférés kezelése az Azure AD |} Microsoft Docs"
description: "A cikkből megtudhatja, hogyan használható vészhelyzeti fiókok segítségével a szervezetek korlátozni a rendszerjogosultságú hozzáférést egy meglévő Azure Active Directory-környezeten belül."
services: active-directory
keywords: "Nem adjon hozzá vagy kulcsszavak szerkesztése nélkül tanácsadás a Keresőmotor-optimalizálást végző szakemberrel."
author: markwahl-msft
ms.author: billmath
ms.date: 12/13/2017
ms.topic: article-type-from-white-list
ms.service: active-directory
ms.workload: identity
ms.custom: it-pro
ms.reviewer: markwahl-msft
ms.openlocfilehash: 039012b8ba0b83f6338128a2200d1232ae6467f3
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2018
---
# <a name="manage-emergency-access-administrative-accounts-in-azure-ad"></a>Sürgős-hozzáférési rendszergazdai fiókok kezelése az Azure ad-ben 

A legtöbb napi tevékenységhez *globális rendszergazda* jogok a felhasználók számára nem szükséges. Felhasználók nem véglegesen rendelhető hozzá a szerepkört, mert előfordulhat, hogy véletlenül általuk egy feladatot, amely kell rendelkezniük, mint a magasabb szintű engedélyekkel kell rendelkeznie. Ha felhasználók címtára globális rendszergazdájaként eljáró nincs szükség, azok a szerepkör-hozzárendelés Azure Active Directory (Azure AD) Privileged Identity Management (PIM), a saját fiók vagy egy másik rendszergazda fiók használatával kell aktiválása.

Felhasználók saját maguk véve a rendszergazdai hozzáférési jogokkal, mellett meg kell akadályozni véletlenül zárolva kívül az Azure AD-bérlő a felügyeletét is, sem a bejelentkezés sem aktiválásához egy meglévő egyéni felhasználói fiókot, mert egy rendszergazda. A rendszergazdai hozzáférés véletlen hiánya hatásának mérséklésére vonatkozó útmutatások két vagy több elhelyezésével *vészhelyzeti fiókok* az Ön bérelt szolgáltatásának.

Vészhelyzeti fiókok segítségével a szervezetek korlátozni a rendszerjogosultságú hozzáférést egy meglévő Azure Active Directory-környezeten belül. Az ilyen fiókok amelyek magas szintű jogosultságokkal, és nem konkrét személyek vannak rendelve. Vészhelyzeti fiókok sürgős vagy "vészhelyzeti" forgatókönyvek, ahol normál rendszergazdai fiókok nem használható olyan helyzetekben korlátozódnak. A szervezetek kell fenntartani a vészhelyzeti fiók használat korlátozása csak, időpont, amikor szükség az a célja.

Egy szervezet vészhelyzeti hozzáférési fiókot használja a következő esetekben lehet szükség:

 - A felhasználói fiókok összevont vannak, és összevonási jelenleg nem érhető el egy cella hálózati break vagy identitásszolgáltató kimaradás miatt. Például ha az identitás szolgáltató állomás a környezetben jelenleg nem aktív, felhasználók lehet nem jelentkezhetnek be, amikor az identitásszolgáltató átirányítja az Azure AD. 
 - A rendszergazdák regisztrált Azure multi-factor Authentication segítségével, és az egyes eszközök nem érhetők el. Lehet, hogy a felhasználók nem lehet befejezni a multi-factor Authentication egy szerepkör aktiválásához. Például egy cella hálózati kimaradás megakadályozza az telefonhívásokat megválaszolását vagy üzeneteket, a csak két hitelesítési mechanizmusok, hogy regisztrálják azokat az eszközön. 
 - A legutóbbi globális rendszergazdai hozzáféréssel rendelkező személy elhagyta a szervezetben. Az Azure AD megakadályozza, hogy az utolsó *globális rendszergazda* fiók törlését, de nem akadályozza meg a fiók nem törli vagy letiltása a helyszínen. Mindkét esetben tehetik a szervezet nem lehet helyreállítani a fiókot.

## <a name="initial-configuration"></a>Kezdeti konfigurálása

Két vagy több vészhelyzeti hozzáférés fiókokat létrehozni. Ezek csak felhőalapú fiókok használó kell lennie a \*. onmicrosoft.com tartományt, és hogy nem összevont vagy szinkronizálja a helyszíni környezetben. 

A számítógépfiókok nem lehetnek a szervezet minden egyes felhasználója társítva. Győződjön meg arról, hogy ezek a fiókok hitelesítő adatai mindig biztonságos és ismert csak az egyéni felhasználók számára, akik jogosultak a is használhatja őket a szervezeteknek kell. 

> [!NOTE]
> Egy fiók jelszavát vészhelyzeti hozzáférés általában két vagy három részekre osztva, külön részein dokumentum írása, és biztonságos, tűzbiztos egy olyan széf, amelyek biztonságos, különböző helyeken vannak tárolva. 
>
> Győződjön meg arról, hogy a sürgős hozzáférési fiókok nem csatlakoztatott bármilyen alkalmazott által megadott mobiltelefonok, hardver jogkivonatok egyes alkalmazottak, vagy más alkalmazott vonatkozó hitelesítő adatokat, hogy utazás. Ez okokból esetekben, amikor egy alkalmazott nem érhető el, ha szükséges a hitelesítő adatokat tartalmazza. 

### <a name="initial-configuration-with-permanent-assignments"></a>Állandó hozzárendelésekkel kezdeti konfigurálása

Egy lehetőség, hogy a felhasználók állandó tagjai a *globális rendszergazda* szerepkör. Ez a beállítás akkor lehet megfelelő, a szervezeteknek, amelyek nem rendelkeznek az Azure AD Premium P2 előfizetések.

Sérült biztonságú jelszó eredő támadások kockázatok csökkentése érdekében az Azure AD azt javasolja, hogy többtényezős hitelesítés megkövetelése minden egyes felhasználó. Ennek a csoportnak kell foglalni, rendszergazdák és az összes többi (például pénzügyi tisztviselő) sérült biztonságú fiók jelentős hatással lenne. 

Azonban ha a szervezet nem rendelkezik megosztott eszközök, a multi-factor Authentication nem esetleg vészhelyzeti hozzáférés fiókok. Ha konfigurál egy feltételes hozzáférési szabályzatot, [minden rendszergazda multi-factor Authentication regisztráció](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states) az Azure AD és a kapcsolódó egyéb szoftverek egy szolgáltatott szoftverként (SaaS) alkalmazások, szükség lehet házirend konfigurálása kivételek vészhelyzeti fiókok kizárása ezt a követelményt.

### <a name="initial-configuration-with-approvals"></a>A jóváhagyások kezdeti konfiguráció

Lehetősége a felhasználók konfigurálása jogosult és a jóváhagyóknak aktiválásához a *globális rendszergazda* szerepkör. Ezt a beállítást igényel a szervezet Azure AD Premium P2-előfizetéssel rendelkezik. Azt is igényelnének egy multi-factor Authentication beállítása, amely lehetővé teszi több egyéni felhasználók számára és a hálózati környezet között megosztott használatra. Ezek a követelmények azért, mert az aktiválás a *globális rendszergazda* szerepkör megköveteli a felhasználóktól korábban végeztek el a multi-factor Authentication. További információkért lásd: [használatának megkövetelése a többtényezős hitelesítés az Azure AD Privileged Identity Management](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-privileged-identity-management-how-to-require-mfa).

Személyes eszközök vészhelyzeti hozzáférés fiókokhoz tartozó többtényezős hitelesítés használatát nem javasoljuk. Tényleges vészhelyzet esetén a személy, aki hozzá kell férnie a többtényezős hitelesítés regisztrált eszköz nem feltétlenül a személyes eszköz rendelkező azt. 

Is érdemes lehet a fenyegetésekről alkotott képet. Például egy előre nem látható körülmény, például a sürgős természeti katasztrófa léphetnek fel, amikor mobiltelefon- és egyéb hálózati előfordulhat, hogy nem érhető el. Fontos annak biztosítása érdekében, hogy a regisztrált eszközök ismert, biztonságos helyen, amely több azt jelenti, hogy kommunikálni az Azure ad-val rendelkezik.

## <a name="ongoing-monitoring"></a>Figyelése

A figyelő a [naplózza az Azure AD-bejelentkezés és a naplózási](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-activity-sign-ins) a bármely bejelentkezések és naplózási tevékenység törlődik a sürgős-hozzáférési fiókokat. Ezek a fiókok általában nem kell jelentkezik be, és kell nem lehet módosítása, így azok használata valószínűleg rendellenes és biztonsági vizsgálat szükséges.

## <a name="account-check-validation-must-occur-at-regular-intervals"></a>Érvényesítési rendszeres időközönként kell magukat a fiók ellenőrzése

A fiók érvényesítéséhez a következő lépésekkel minimum:
- 90 naponta.
- Ha az informatikai részleg, például egy feladat módosítása, egy indító vagy egy új felvételi legutóbbi változás történt.
- Ha a szervezet az Azure AD előfizetéseinek megváltoztak.

Betanítása alkalmazottjának vészhelyzeti hozzáférés fiókok használatára, tegye a következőket:

* Gondoskodjon arról, hogy a személyzet biztonsági figyelés vegye figyelembe, hogy a számítógépfiók-ellenőrzésének tevékenység folyamatban.
* Ellenőrizze, hogy a felhő felhasználói fiókok bejelentkezhetnek és azok a szerepkörök aktiválása és, hogy a felhasználók, akik módosítania kell a következő lépésekkel vészhelyzetben képzett folyamat.
* Győződjön meg arról, hogy azok nem regisztrált multi-factor Authentication vagy az önkiszolgáló jelszó-visszaállításból (SSPR) minden egyes felhasználói eszközön vagy személyes adatok. 
* Ha a fiókok van regisztrálva a többtényezős hitelesítés az eszközt, a szerepkör aktiválásakor, gondoskodjon arról, hogy az eszköz összes rendszergazdák szüksége lehet az vészhelyzetben érhető el. Azt is ellenőrizze, hogy az eszköz regisztrálva van-e, amely nem ugyanazt a közös hibaállapotba legalább két módon. Például az eszköz kommunikálhatnak a létesítmény vezeték nélküli hálózat és a cella szolgáltató hálózaton keresztül.
* A fiók hitelesítő adatainak frissítése.

## <a name="next-steps"></a>További lépések
- [Adjon hozzá egy felhőalapú felhasználói](add-users-azure-active-directory.md) és [az új felhasználó hozzárendelése a globális rendszergazdai szerepkörrel](active-directory-users-assign-role-azure-portal.md).
- [Előfizetés az Azure Active Directory Premium](active-directory-get-started-premium.md), ha még nem volt már.
- [Azure multi-factor Authentication kérése a rendszergazdáknak egyedi felhasználók](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states).
- [Az Office 365-ben globális rendszergazdák számára további védelmet konfigurálása](https://support.office.com/article/Protect-your-Office-365-global-administrator-accounts-6b4ded77-ac8d-42ed-8606-c014fd947560), az Office 365 használatakor.
- [Hajtsa végre egy globális rendszergazdák áttekintése](active-directory-privileged-identity-management-how-to-start-security-review.md) és [áttérés meglévő globális rendszergazdák számára, hogy pontosabb rendszergazdai szerepkörök](active-directory-assign-admin-roles-azure-portal.md).



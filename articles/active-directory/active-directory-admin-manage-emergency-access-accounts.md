---
title: "Vészhelyzeti hozzáférés felügyeleti fiókok kezelése az Azure AD |} Microsoft Docs"
description: "Használja a szervezetek számára korlátozni a rendszerjogosultságú hozzáférést egy meglévő Azure Active Directory-környezeten belül vészhelyzeti fiókokat ismerteti."
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
ms.openlocfilehash: 75ea8e445c890e7af5ab14f4fc4c53cfb1af4568
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2017
---
# <a name="managing-emergency-access-administrative-accounts-in-azure-ad"></a>Vészhelyzeti hozzáférés rendszergazdai fiókok kezelése az Azure ad-ben 

A legtöbb napi tevékenységhez globális rendszergazdai szerepkör nem szükségesek a jogosultságok.  Felhasználók kell nem tartósan hozzá lehet rendelni a szerepkört, a felhasználó véletlenül hajthat végre egy feladatot a szükségesnél nagyobb engedélyekkel. Amikor egy felhasználó egy globális rendszergazda összekötőként kell, azokat a szerepkör-hozzárendelés Azure AD PIM, vagy a saját vagy egy másodlagos rendszergazdai fiók használatával kell aktiválása.

A felhasználók saját maguk rendszergazdai hozzáférési jogok véve, mellett ügyfelek elő kell készíteni annak érdekében, hogy azok nem bejutni olyan helyzet, ahol azok sikerült véletlenül zárolni felügyelete az Azure AD-bérlő miatt hogy képtelen jelentkezzen ki a meglévő egyéni felhasználói fiók rendszergazdai vagy aktiválása.  Azok mérséklésére vonatkozó útmutatások keresztül tárolása a két vagy több bérlői rendszergazdai hozzáférés véletlen hiánya hatását *vészhelyzeti fiókok*.

Vészhelyzeti fiókok segítségével a szervezetek korlátozni a rendszerjogosultságú hozzáférést egy meglévő Azure Active Directory-környezeten belül. Ezek a fiókok magas szintű jogosultságokkal, és konkrét személyek nincsenek hozzárendelve. Vészhelyzeti fiókok korlátozódnak vészhelyzeti "vészhelyzeti" forgatókönyvek, ahol a normál rendszergazdai fiókok nem használható.  A szervezetek biztosítania kell vezérlése, és csak amelyek esetében szükséges idő csökkentése a vészhelyzeti fiók használati céljából.

Forgatókönyvek, ahol a szervezet érdemes lehet végrehajtania vészhelyzeti hozzáférés fiók használata:

 - A felhasználói fiókokat a rendszer összevont és összevonási hálózati szünet vagy kimaradás lép fel az identitásszolgáltató miatt nem érhető el.  Például a identitás szolgáltató gazdagépre, amely a felhasználói környezet jelenleg nem aktív, ha a felhasználó nem lehet használva jelentkezhet be, amikor az identitásszolgáltató átirányítja az Azure AD. 
 - A rendszergazdák van regisztrálva a többtényezős Hitelesítést, és minden egyes eszközükön nem érhetők el.  Lehetséges, a rendszer nem képes a szerepkör aktiválásához, ha például egy cella hálózati kimaradás előfordulhat, hogy a felhasználók nem tudnak telefonhívásokat vagy szöveges üzeneteket, valamint az egyetlen módszer teljes multi-factor Authentication felhasználókat, hogy azok az eszköz regisztrálva. 
 - A felhasználónak, aki utoljára balra a szervezet globális rendszergazdai hozzáférés volna.  Az Azure AD megakadályozza, hogy az utolsó globális rendszergazdai fiókkal törlés alatt áll, de nem akadályozza meg a fiók nem törli vagy letiltása a helyszínen, ami olyan helyzet, ahol a szervezet nem tudja helyreállítani az adott fiókkal.

## <a name="initial-configuration"></a>Kezdeti konfigurálása

Két vagy több vészhelyzeti hozzáférés fiókokat létrehozni.  Ezek kizárólag felhőalapú fiókokkal kell lennie a *. onmicrosoft.com tartományt, nem összevont vagy szinkronizálja a helyszíni környezetben.  

Nem kell a szervezetben lévő egyes felhasználókhoz van társítva.  A szervezetek kell annak biztosítása érdekében ezek a fiókok hitelesítő adatait biztonságos és ismert csak az egyéni felhasználók számára, akik jogosultak a is használhatja őket. 

Megjegyzés: Általában a fiók jelszavát egy vészhelyzeti hozzáférés fiók van két vagy három részekre osztva külön részein dokumentum írása és biztonságos, tűzbiztos egy olyan széf, amelyek biztonságos külön helyen tárolódnak. Győződjön meg arról, hogy a sürgős hozzáférési fiókok nem csatlakoztatott bármilyen alkalmazott által megadott mobiltelefonok, hardver jogkivonatok egyes alkalmazottak, vagy más alkalmazott-specifikus hitelesítő adatait, az adott utazás, abban az esetben, hogy egyes alkalmazott nem érhető el jelenleg a a hitelesítő adatok szükségesek. 

### <a name="initial-configuration-with-permanent-assignments"></a>Állandó hozzárendelésekkel kezdeti konfigurálása

Egy lehetőség azoknak a felhasználóknak a globális rendszergazdai szerepkör tagjai állandó hozzárendelni.  Azure AD Premium P2 előfizetések nem rendelkező szervezetek számára megfelelő lenne.

Az Azure AD azt javasolja, hogy a többtényezős hitelesítés (MFA) minden egyes felhasználó, többek között a rendszergazdák és az összes többi felhasználója jelentős hatással lenne, ha a fiók nem sérült (pl. pénzügyi tisztviselő) szükséges. Ez csökkenti a sérült biztonságú jelszó miatt a támadás kockázatát. Azonban ha a szervezet nem rendelkezik megosztott eszközök, többtényezős hitelesítés nem lehet vészhelyzeti hozzáférés fiókok lehetséges.  Ha konfigurál egy feltételes hozzáférési szabályzatot, [MFA regisztrációs minden rendszergazda](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states) az Azure AD és az SaaS-alkalmazásokhoz kapcsolódó egyéb, szükség lehet a házirend kivételek vészhelyzeti fiókok kizárása ez konfigurálása követelmény.

### <a name="initial-configuration-with-approvals"></a>A jóváhagyások kezdeti konfiguráció

Egy másik lehetőség egy konfigurálása ezen jogosult felhasználók és a jóváhagyóknak, a globális rendszergazdai szerepkör aktiválásához.  Ehhez a szervezet Azure AD Premium P2 előfizetések, valamint egy többtényezős hitelesítés beállítása több egyéni felhasználók számára és a hálózati környezet között megosztott alkalmas van szükség.  Ez azért van így, mert a globális rendszergazdai szerepkör aktiválása megköveteli a felhasználót, hogy korábban végeztek MFA.  További tudnivalókért olvassa el [Azure AD Privileged Identity Management a többtényezős hitelesítés megkövetelése](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-privileged-identity-management-how-to-require-mfa).

Személyes eszközök társított MFA használata nem ajánlott vészhelyzeti hozzáférési fiókokat, mert egy tényleges vészhelyzeti egy személy, aki rendelkeznie kell a többtényezős hitelesítés regisztrált eszközre való hozzáférés nem lehet a rendszer rendelkezik a személyes eszköz egy.  Figyelembe venni a veszélyforrásainak tükrében megfigyelhető, ha előre nem látható körülmények miatt mobiltelefon- és egyéb hálózati nem használható vészhelyzeti természeti katasztrófa során.  Ezért fontos annak biztosítása érdekében, hogy a regisztrált eszközök ismert, biztonságos helyen, amely több azt jelenti, hogy kommunikálni az Azure ad-val rendelkezik.

## <a name="ongoing-monitoring"></a>Figyelése

A figyelő a [jelentkezzen be az Azure AD és az auditnaplók](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-activity-sign-ins) a bármely bejelentkezések és a vészhelyzeti hozzáférés fiókoktól műveletek naplózása.  Ezek a fiókok általában nem kell jelentkezik be, és kell nem lehet módosítása, így azok használata valószínűleg rendellenes és biztonsági vizsgálat szükséges.

## <a name="account-check-validation-must-occur-at-regular-intervals"></a>Rendszeres időközönként kell magukat a fiók ellenőrzése érvényesítése

Legalább hajtsa végre a következő lépéseket:
 - 90 naponta
 - Ha az informatikai munkatársak – legutóbbi változás történt a feladat módosításához indító vagy új felvételi
 - Ha a szervezet az Azure AD előfizetéseinek változásai

Győződjön meg arról, hogy hogyan használja a vészhelyzeti fiókokat a személyzet képzett:

1.  Győződjön meg róla biztonsági figyelési személyzet vegye figyelembe, hogy a fiók ellenőrzése tevékenység folyamatban.
2.  Ellenőrizze, hogy a felhő felhasználói fiókok bejelentkezhetnek és azok a szerepkörök aktiválása és, hogy a felhasználók számára lehetséges, hogy a következő lépésekkel vészhelyzetben képzett folyamat.
3.  Győződjön meg arról, hogy azok rendelkeznek nincs regisztrálva a többtényezős Hitelesítést vagy az önkiszolgáló jelszó-Változtatási az minden egyes felhasználói eszköz vagy a személyes adatok.  
4. Ha a fiókok regisztrálva van az MFA szolgáltatásra az eszközt, a szerepkör aktiválásakor, győződjön meg arról, az eszköz nem érhető el minden rendszergazdák esetleg vészhelyzetben használandó.  Ellenőrizze, hogy az eszköz regisztrálva van-e, amely nem ugyanazt a közös hibaállapotba legalább két módon is (például az eszköz kommunikálni képes az internetre, mind a létesítmény vezeték nélküli hálózaton keresztül, valamint egy cella szolgáltató hálózaton keresztül).
5.  Frissítse a hitelesítő adatokat.

## <a name="next-steps"></a>Következő lépések
- [Adjon hozzá egy felhőalapú felhasználói](add-users-azure-active-directory.md) és [az új felhasználó hozzárendelése a globális rendszergazdai szerepkörrel](active-directory-users-assign-role-azure-portal.md)
- [Előfizetés az Azure Active Directory Premium](active-directory-get-started-premium.md), ha még nem tette meg
- [Azure MFA hitelesítés megkövetelésére rendszergazdaként rendelt egyedi felhasználók számára](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states)
- [Az Office 365-ben globális rendszergazdák számára további védelmet konfigurálása](https://support.office.com/article/Protect-your-Office-365-global-administrator-accounts-6b4ded77-ac8d-42ed-8606-c014fd947560), az Office 365 használata
- [Hajtsa végre egy globális rendszergazdák áttekintése](active-directory-privileged-identity-management-how-to-start-security-review.md) és [áttérés meglévő globális rendszergazdák számára, hogy pontosabb rendszergazdai szerepkörök](active-directory-assign-admin-roles-azure-portal.md)


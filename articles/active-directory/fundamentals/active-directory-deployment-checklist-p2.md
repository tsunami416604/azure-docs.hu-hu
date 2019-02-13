---
title: Az Azure AD-telepítési ellenőrzőlista
description: Az Azure Active Directory szolgáltatás telepítési ellenőrzőlista
services: active-directory
ms.service: active-directory
ms.subservice: ''
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8046721244b31539dc79eba3f2ba11d34c2df256
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56170363"
---
# <a name="azure-active-directory-feature-deployment-guide"></a>Az Azure Active Directory szolgáltatás üzembe helyezési útmutató

Azt is tűnhet üzembe helyezése az Azure Active Directory (Azure AD) a szervezet számára, és tárolja biztonságos helyen. Ez a cikk gyakori feladatokat, hogy ügyfeleink hasznos végrehajtásához a fázisokban, folyamán 30, 60, 90 napig, vagy több, a biztonság növelése érdekében állapotáról azonosítja. Még akkor is, szervezetek számára, akik már üzembe helyezte az Azure ad-ben ez az útmutató használatával biztosíthatja azok kihozhatják a legtöbbet hozhassa ki a befektetés.

Egy jól megtervezett és végrehajtott identitás-infrastruktúra előkészíti az biztonságos hozzáférés a termelékenység számítási feladatok és az ismert felhasználók és eszközök csak adatokat.

Emellett ellenőrizheti a felhasználók saját [identitás biztonságos pontszám](identity-secure-score.md) hogyan igazított vannak a Microsoft ajánlott eljárások megtekintéséhez. A biztonságos pontszám előtt ellenőrizze, és ezeket a javaslatokat tekintse meg a végrehajtási arról, hogy végez összevet az iparág más és más szervezetek a méretű.

## <a name="prerequisites"></a>Előfeltételek

Ebben az útmutatóban szereplő javaslatok implementálható az Azure AD ingyenes, alapszintű vagy licenc nem minden. Ahol licencekre szükség a Microsoft állapot a melyik licencre szükség, legalább a feladatnak a.

Licenceléssel kapcsolatos további információ található a következő lapokon:

* [Az Azure AD licencelése](https://azure.microsoft.com/pricing/details/active-directory/)
* [A Microsoft 365 nagyvállalati verzió](https://www.microsoft.com/en-us/licensing/product-licensing/microsoft-365-enterprise)
* [Enterprise Mobility + Security](https://www.microsoft.com/en-us/licensing/product-licensing/enterprise-mobility-security)
* [Az Azure AD B2B licencelési útmutató](../b2b/licensing-guidance.md)

## <a name="phase-1-build-a-foundation-of-security"></a>1. fázis: Biztonsági épít

Ebben a fázisban a rendszergazdák alapkonfiguráció biztonsági funkciók az Azure AD, hogy importálni, vagy normál felhasználói fiókok létrehozása előtt hozzon létre egy biztonságos, és könnyen használható foundation engedélyezése. Ez a alapvető fázis, a kezdetektől biztonságosabb állapotban van, és, hogy a végfelhasználók csak kell bevezetni új fogalmakat egyszer biztosítja.

| Tevékenység | Részlet | Licenc szükséges |
| ---- | ------ | ---------------- |
| [Egynél több globális rendszergazda kijelölése](../users-groups-roles/directory-emergency-access.md) | Rendelje hozzá legalább két állandó csak felhőalapú globális rendszergazdai fiókok, vészhelyzet esetén. Ezek a fiókok nincsenek napi használható, és rendelkeznie kell a hosszú és összetett jelszavak. | Azure AD Free |
| [Használja a nem globális rendszergazdai szerepkört, ahol lehetséges](../users-groups-roles/directory-assign-admin-roles.md) | A rendszergazdák csak a szükséges hozzáférést biztosít csak azokat a területeket, szükségük van a hozzáférést. Nem minden rendszergazdának kell lennie a globális rendszergazdák. | Azure AD Free |
| [Rendszergazdai szerepkör használat nyomon követése a Privileged Identity Management engedélyezése](../privileged-identity-management/pim-getting-started.md) | Engedélyezze a Privileged Identity Management elindításához a rendszergazdai szerepkör használat nyomon követése. | Prémium szintű Azure AD P2 |
| [Önkiszolgáló jelszó-visszaállítás bevezetése](../authentication/howto-sspr-deployment.md) | Csökkentse a telefonhívások, a jelszavak alaphelyzetbe állításának azáltal, hogy munkatársak, alaphelyzetbe állíthatja saját jelszavait, házirendekkel, egy rendszergazda vezérlőként. | Azure AD Basic |
| [Hozzon létre egy szervezet meghatározott egyéni letiltott jelszavak](../authentication/howto-password-ban-bad-configure.md) | Gyakori szavakat vagy kifejezéseket a szervezet vagy terület tartalmazó jelszavak létrehozásával megakadályozhatja a felhasználókat. | Azure AD Basic |
| [A helyszíni integrációjának engedélyezése az Azure AD jelszóvédelem](../authentication/concept-password-ban-bad-on-premises.md) | A helyszíni címtár, jelszavak beállítása a helyszíni meg is megfelelnek-e a globális a letiltott jelszavak listájának kibővítése, és a bérlő-specifikus le van tiltva a jelszavak listáit. | Prémium szintű Azure AD P1 |
| [A Microsoft tájékoztató a jelszavakról engedélyezése](https://www.microsoft.com/research/publication/password-guidance/) | Állítsa le a felhasználók számára beállított ütemezés szerint a jelszó módosítására, tiltsa le a bonyolultsági feltételeknek, és a felhasználók számára olyan több apt, ne felejtse el a jelszavukat, és láthatóan tartja őket olyan biztonságos. | Azure AD Free |
| [Tiltsa le a felhőalapú felhasználói fiókok rendszeres jelszó kérésekor](../authentication/concept-sspr-policy.md#set-a-password-to-never-expire) | Rendszeres jelszó-visszaállítási ösztönözze a felhasználókat jelszavuk meglévő növekszik. Használja az irányelveket a Microsoft jelszó útmutatást a dokumentum, és csak felhőbeli felhasználók számára a helyszíni szabályzat tükrözéséhez. | Azure AD Free |
| [Azure Active Directory intelligens zárolás testreszabása](../authentication/howto-password-smart-lockout.md) | A felhőalapú felhasználók a helyszíni Active Directory-felhasználók replikációját próbálkozhassanak leállítása | Azure AD Basic |
| [Az AD FS Extranet az intelligens zárolás engedélyezése](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) | AD FS extranet kizárás tranzakciómentes találgatásos jelszótalálgatásos támadások, míg a érvényes AD FS felhasználók továbbra is használhatja a fiókjaikat. | |
| [A feltételes hozzáférési szabályzatokkal az Azure AD multi-factor Authentication üzembe helyezése](../authentication/howto-mfa-getstarted.md) | A felhasználóktól a kétlépéses hitelesítés végrehajtására, a feltételes hozzáférési szabályzatokkal érzékeny alkalmazások elérésekor. | Prémium szintű Azure AD P1 |
| [Az Azure Active Directory Identity Protection engedélyezése](../identity-protection/enable.md) | Kockázatos bejelentkezések és a szervezet felhasználói hitelesítő adatokkal való visszaélés nyomon követését lehetővé. | Prémium szintű Azure AD P2 |
| [A multi-factor authentication és a jelszó módosítására a kockázati események használatával](../authentication/tutorial-risk-based-sspr-mfa.md) | Aktiválhat eseményeket, mint például a többtényezős hitelesítés, jelszó-visszaállítás és a bejelentkezési kockázat alapján blokkolásának-automatizálás engedélyezése. | Prémium szintű Azure AD P2 |
| [A hiperkonvergens regisztráció önkiszolgáló jelszó-visszaállítás és az Azure AD multi-factor Authentication (előzetes verzió) engedélyezése](../authentication/concept-registration-mfa-sspr-converged.md) | Lehetővé teszi a felhasználók regisztráljanak az Azure multi-factor Authentication és az önkiszolgáló jelszó-visszaállítás egy közös élményének. | Prémium szintű Azure AD P1 |

## <a name="phase-2-import-users-enable-synchronization-and-manage-devices"></a>2. fázis: Felhasználók importálása, -szinkronizálás engedélyezése és eszközök kezelése

Ezután hozzáadjuk az alapítvány meghatározni 1. fázis importálása, hogy a felhasználók és engedélyezésével a szinkronizálás vendéghozzáférés tervezése és előkészítése további funkciók támogatásához.

| Tevékenység | Részlet | Licenc szükséges |
| ---- | ------ | ---------------- |
| [Az Azure AD Connect telepítése](../connect/active-directory-aadconnect-select-installation.md) | Készítse elő a felhőbe meglévő helyszíni címtárából felhasználókat szinkronizálja. | Azure AD Free |
| [Jelszókivonat-szinkronizálás megvalósítása](../connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md) | Replikálni a jelszó módosítására, a helytelen jelszó észlelési és a szervizelés és a kiszivárgott hitelesítő adatokat a jelentéskészítés engedélyezéséhez a jelszókivonatok szinkronizálása. | Prémium szintű Azure AD P1 |
| [A Jelszóvisszaíró megvalósítása](../authentication/howto-sspr-writeback.md) | Lehetővé teszi a felhőben is visszaírását a helyszíni Windows Server Active Directory-környezeten a jelszó módosítására. | Prémium szintű Azure AD P1 |
| [Megvalósítása az Azure AD Connect Health](../connect-health/active-directory-aadconnect-health.md) | Az Azure AD Connect-kiszolgálók, az AD FS-kiszolgálók és tartományvezérlők kulcs egészségügyi statisztika figyelés engedélyezése. | Prémium szintű Azure AD P1 |
| [Licencek hozzárendelése a felhasználók által az Azure Active Directory biztonságicsoport-tagság](../users-groups-roles/licensing-groups-assign.md) | Időt és fáradságot takarít csoport beállítással felhasználónként helyett szolgáltatások engedélyezése és letiltása licencelési csoportok létrehozásával. | |
| [Hozzon létre egy csomagot való vendégfelhasználói hozzáférés](../b2b/what-is-b2b.md) | Vendégfelhasználók együttműködhet, hogy jelentkezzen be az alkalmazások és szolgáltatások a saját munkahelyi, iskolai vagy a közösségi identitásuk őket. | [Az Azure AD B2B licencelési útmutató](../b2b/licensing-guidance.md) |
| [Döntse el, az eszközkezelési stratégiától](../devices/overview.md) | Döntse el, eszközökre vonatkozó teszi a szervezet. Vállalati megadott regisztrálása való csatlakozás, saját eszközök használata a vs vs. | |
| [Windows Hello for Business a szervezetében üzembe helyezi](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization) | Felkészülés a Windows Hello használata jelszó nélküli hitelesítése | |

## <a name="phase-3-manage-applications"></a>3. fázis: Alkalmazások kezelése

Továbbra is az előző fázisok épülnek, mint hogy áttelepítését és integrálását az Azure ad-vel jelölt alkalmazások azonosítása, és ezeknek az alkalmazásoknak a telepítés befejezéséhez.

| Tevékenység | Részlet | Licenc szükséges |
| ---- | ------ | ---------------- |
| Az alkalmazások azonosítása | A szervezetben használt alkalmazások meghatározása: a helyszíni, felhőalapú SaaS-alkalmazások és más üzleti alkalmazások. Határozza meg, ha ezek az alkalmazások és az Azure ad-vel kell kezelni. | Nincs licenc szükséges |
| [A katalógus támogatott SaaS-alkalmazások integrálása](../manage-apps/add-application-portal.md) | Az Azure AD egy katalógus, amely több ezer előre integrált alkalmazások rendelkezik. A szervezete az alkalmazások néhány valószínűleg a katalógusban elérhető közvetlenül az Azure Portalról. | Azure AD Free |
| [Az alkalmazásproxy használatával a helyszíni alkalmazások integrálása](../manage-apps/application-proxy-add-on-premises-application.md) | Az alkalmazásproxy lehetővé teszi, hogy a felhasználók hozzáférhessenek a helyszíni alkalmazásokhoz bejelentkezik az Azure AD-fiókja. | Azure AD Basic |

## <a name="phase-4-audit-privileged-identities-complete-an-access-review-and-manage-user-lifecycle"></a>4. fázis: Emelt szintű identitások naplózása, a hozzáférési felülvizsgálat befejezése és a felhasználói életciklus kezelése

4. fázis rendszergazdák kényszerítése a legalacsonyabb jogosultsági elveket, felügyeleti, az első a hozzáférési felülvizsgálatok elvégzése, illetve engedélyezni kívánja gyakori felhasználói életciklus-feladatok automatizálása megkeresheti.

| Tevékenység | Részlet | Licenc szükséges |
| ---- | ------ | ---------------- |
| [Privileged Identity Management használatának kényszerítése](../privileged-identity-management/pim-security-wizard.md) | Rendszergazdai szerepkörök eltávolítása átlagos napi felhasználói fiókokat. Jogosulttá a rendszergazda felhasználók a szerepkör használatához egy multi-factor authentication szolgáltatás-ellenőrzés sikeres, egy üzleti indoklás megadása vagy jóváhagyás kérése a kijelölt jóváhagyókat után. | Prémium szintű Azure AD P2 |
| [A PIM az Azure AD-címtárbeli szerepkörökhöz tartozó hozzáférési felülvizsgálat befejezése](../privileged-identity-management/pim-how-to-start-security-review.md) | Működik a biztonság és a vezetőség esetlegesen csapataival, tekintse át a szervezet szabályzatait alapuló felügyeleti hozzáférés hozzáférési felülvizsgálati házirend létrehozásához. | Prémium szintű Azure AD P2 |
| [Dinamikus csoport tagsági házirendek megvalósítása](../users-groups-roles/groups-dynamic-membership.md) | Dinamikus csoportok használatával automatikusan felhasználók attribútumaik HR (vagy a forrásból hiteles), például a részleg, cím, régió, és egyéb attribútumok alapján csoportokhoz rendelhetők hozzá. |  |
| [Csoport alapú alkalmazások üzembe helyezése megvalósítása](../manage-apps/what-is-access-management.md) | Használja a Csoportalapú hozzáférés-felügyeleti kiépítés automatikusan az SaaS-alkalmazások üzembe helyezése felhasználók számára. |  |
| [Felhasználókiépítés és -megszüntetés automatizálása](../manage-apps/user-provisioning.md) | Távolítsa el manuális lépések az alkalmazott fiók életciklusa jogosulatlan hozzáférés elkerülése érdekében. Az Azure AD a forrásból (HR-rendszerbe) hiteles identitások szinkronizálásával. |  |

## <a name="next-steps"></a>További lépések

[Az Azure AD-licencelés és díjszabás](https://azure.microsoft.com/pricing/details/active-directory/)

[Identitás- és eszköz-hozzáférési konfigurációk](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)

[Közös identitás- és eszköz-hozzáférési szabályzatok ajánlott](https://docs.microsoft.com/microsoft-365/enterprise/identity-access-policies)

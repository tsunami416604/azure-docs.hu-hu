---
title: Azure AD-üzembehelyezési ellenőrzőlista
description: Azure Active Directory funkció üzembe helyezési ellenőrzőlistája
services: active-directory
ms.service: active-directory
ms.subservice: ''
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 767e3caf577bc1b7a49bd56570b8aeedbe307edc
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68693878"
---
# <a name="azure-active-directory-feature-deployment-guide"></a>Azure Active Directory funkció telepítési útmutatója

Úgy tűnhet, hogy az Azure Active Directory (Azure AD) üzembe helyezése a szervezet számára, és biztonságban marad. Ez a cikk azokat az általános feladatokat ismerteti, amelyeket az ügyfelek hasznosnak találnak a fázisokban való végrehajtáshoz, akár 30, 60, 90 nap, akár több, a biztonsági helyzetük javítása érdekében. Még az Azure AD-t már üzembe helyezett szervezetek is használhatják ezt az útmutatót annak biztosítására, hogy a lehető leghatékonyabban használják a saját befektetését.

A jól megtervezett és elvégezhető identitás-infrastruktúrák segítségével biztosítható, hogy csak ismert felhasználók és eszközök férhessenek hozzá a termelékenységi munkaterhelésekhez és az adatokhoz.

Emellett az ügyfelek is megtekinthetik a [személyazonosságuk biztonságos pontszámát](identity-secure-score.md) , hogy meglássuk, hogyan igazították be a Microsoft ajánlott eljárásait. Tekintse át a biztonságos pontszámot a javaslatok megvalósítása előtt és után, és nézze meg, milyen jól csinálja az iparágban és a méretében lévő többi szervezethez képest.

## <a name="prerequisites"></a>Előfeltételek

A jelen útmutatóban foglalt javaslatok közül sokat ingyenes Azure AD vagy egyetlen licenccel sem lehet megvalósítani. A licencek megadása kötelező, hogy legalább a feladat elvégzéséhez szükség van-e a licencre.

A licenceléssel kapcsolatos további információkért tekintse meg a következő lapokat:

* [Az Azure AD licencelése](https://azure.microsoft.com/pricing/details/active-directory/)
* [A Microsoft 365 nagyvállalati verzió](https://www.microsoft.com/en-us/licensing/product-licensing/microsoft-365-enterprise)
* [Enterprise Mobility + Security](https://www.microsoft.com/en-us/licensing/product-licensing/enterprise-mobility-security)
* [Az Azure AD B2B licencelési útmutatója](../b2b/licensing-guidance.md)

## <a name="phase-1-build-a-foundation-of-security"></a>1\. fázis: Biztonsági alap létrehozása

Ebben a fázisban a rendszergazdák lehetővé teszik az alapkonfiguráció biztonsági funkcióinak, hogy biztonságosabb és könnyen használható alapokat hozzanak létre az Azure AD-ben a normál felhasználói fiókok importálása vagy létrehozása előtt. Ez az alapszintű fázis gondoskodik arról, hogy a kezdeti időszakban biztonságosabb állapotban legyen, és hogy a végfelhasználók csak egyszer legyenek bevezetve az új fogalmakra.

| Tevékenység | Részletek | Szükséges licenc |
| ---- | ------ | ---------------- |
| [Egynél több globális rendszergazda kijelölése](../users-groups-roles/directory-emergency-access.md) | Ha vészhelyzet van, rendeljen legalább két kizárólag felhőalapú, állandó globális rendszergazdai fiókot. Ezeket a fiókokat nem kell naponta használni, és hosszú és összetett jelszavakkal kell rendelkezniük. | Azure AD Free |
| [A nem globális rendszergazdai szerepkörök használata, ahol lehetséges](../users-groups-roles/directory-assign-admin-roles.md) | Csak azokhoz a területekhez adjon hozzáférést a rendszergazdáknak, akiknek hozzáférésre van szükségük. Nem minden rendszergazdának kell globális rendszergazdának lennie. | Azure AD Free |
| [Privileged Identity Management engedélyezése a rendszergazdai szerepkör használatának nyomon követéséhez](../privileged-identity-management/pim-getting-started.md) | A rendszergazdai szerepkör használatának követésének megkezdéséhez Privileged Identity Management engedélyezése. | Azure AD Premium P2 |
| [Önkiszolgáló jelszó-visszaállítás bevezetése](../authentication/howto-sspr-deployment.md) | Csökkentse az ügyfélszolgálati hívásokat a jelszó-visszaállításhoz, mivel lehetővé teszi a munkatársak számára, hogy a rendszergazdáknak szóló szabályzatok segítségével visszaállítsák saját jelszavukat. | |
| [Szervezethez tartozó egyéni tiltott jelszavak listájának létrehozása](../authentication/howto-password-ban-bad-configure.md) | Annak megakadályozása, hogy a felhasználók olyan jelszavakat hozzanak létre, amelyek a szervezet vagy a körzet általános szavait vagy kifejezéseit tartalmazzák. | |
| [Helyszíni integráció engedélyezése az Azure AD jelszavas védelemmel](../authentication/concept-password-ban-bad-on-premises.md) | Terjessze ki a betiltott jelszavak listáját a helyszíni címtárba, hogy a helyszíni jelszavak a helyszínen is megfeleljenek a globális és a bérlőre jellemző tiltott jelszavak listájának. | Azure AD Premium P1 |
| [A Microsoft jelszavas útmutatásának engedélyezése](https://www.microsoft.com/research/publication/password-guidance/) | Állítsa le, hogy a felhasználóknak ne kelljen módosítaniuk a jelszavukat a beállított időpontokban, le kell tiltaniuk a bonyolultsági követelményeket, és a felhasználók jobban feltehetik a jelszavukat, és így biztonságban tarthatják őket. | Azure AD Free |
| [A felhőalapú felhasználói fiókok rendszeres jelszavának alaphelyzetbe állításának letiltása](../authentication/concept-sspr-policy.md#set-a-password-to-never-expire) | Az időszakos jelszó-visszaállítja a felhasználókat a meglévő jelszavaik növelésére. A Microsoft jelszó-útmutatási dokumentációjában található útmutatást követve a helyi házirendet a csak felhőalapú felhasználók számára tükrözheti. | Azure AD Free |
| [Azure Active Directory intelligens zárolás testreszabása](../authentication/howto-password-smart-lockout.md) | A felhőalapú felhasználók zárolásának leállítása a helyszíni Active Directory felhasználók számára | |
| [Extranetes intelligens zárolás engedélyezése AD FS](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) | AD FS extranetes zárolás védi a találgatásos támadások elleni támadásokat, miközben érvényes AD FS-felhasználók továbbra is használhatják a fiókjaikat. | |
| [Az Azure AD multi-Factor Authentication üzembe helyezése feltételes hozzáférési házirendekkel](../authentication/howto-mfa-getstarted.md) | Megkövetelheti a felhasználóktól, hogy a feltételes hozzáférési szabályzatok használatával kétlépéses ellenőrzést végezzenek az érzékeny alkalmazások elérésekor. | Azure AD Premium P1 |
| [Az Azure Active Directory Identity Protection engedélyezése](../identity-protection/enable.md) | A kockázatos bejelentkezések és a feltört hitelesítő adatok nyomon követésének engedélyezése a szervezet felhasználói számára. | Azure AD Premium P2 |
| [Kockázati események használata a többtényezős hitelesítés és a jelszó módosításának elindításához](../authentication/tutorial-risk-based-sspr-mfa.md) | Olyan automatizálás engedélyezése, amely képes eseményeket, például a többtényezős hitelesítést, a jelszó-visszaállítást és a bejelentkezések kockázaton alapuló blokkolását. | Azure AD Premium P2 |
| [A hiperkonvergens regisztráció önkiszolgáló jelszó-visszaállítás és az Azure AD multi-factor Authentication (előzetes verzió) engedélyezése](../authentication/concept-registration-mfa-sspr-converged.md) | Lehetővé teszi a felhasználók számára, hogy az Azure multi-Factor Authentication és az önkiszolgáló jelszó-visszaállítás egyik gyakori felületéről regisztráljon. | Azure AD Premium P1 |

## <a name="phase-2-import-users-enable-synchronization-and-manage-devices"></a>2\. fázis: Felhasználók importálása, szinkronizálás engedélyezése és eszközök kezelése

Ezután hozzáadjuk az 1. fázisban meghatározott alapítványhoz a felhasználókat és a szinkronizálást, a vendég-hozzáférés megtervezését, valamint a további funkciók támogatásának előkészítését.

| Tevékenység | Részletek | Szükséges licenc |
| ---- | ------ | ---------------- |
| [Az Azure AD Connect telepítése](../connect/active-directory-aadconnect-select-installation.md) | Készítse elő a felhasználók szinkronizálását a meglévő helyszíni címtárból a felhőbe. | Azure AD Free |
| [Jelszókivonat-szinkronizálás megvalósítása](../connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md) | Jelszó-kivonatok szinkronizálása a jelszó-változtatások replikálásához, a jelszavak hibás észleléséhez és szervizeléséhez, valamint a kiszivárgott hitelesítő adatok jelentéséhez. | Azure AD Free |
| [A Jelszóvisszaíró megvalósítása](../authentication/howto-sspr-writeback.md) | A Felhőbeli jelszavak módosításának engedélyezése a helyi Windows Server Active Directory környezetbe való visszaíráshoz. | Azure AD Premium P1 |
| [Megvalósítása az Azure AD Connect Health](../connect-health/active-directory-aadconnect-health.md) | Engedélyezze a Azure AD Connect-kiszolgálók, a AD FS-kiszolgálók és a tartományvezérlők kulcsfontosságú állapot-statisztikáinak figyelését. | Azure AD Premium P1 |
| [Licencek hozzárendelése a felhasználók által az Azure Active Directory biztonságicsoport-tagság](../users-groups-roles/licensing-groups-assign.md) | Időt és fáradságot takaríthat meg olyan licencelési csoportok létrehozásával, amelyek felhasználónként engedélyezik vagy letiltják a szolgáltatások használatát a csoportok beállítása helyett. | |
| [Csomag létrehozása a vendég felhasználói hozzáféréshez](../b2b/what-is-b2b.md) | Együttműködés a vendég felhasználókkal az alkalmazások és szolgáltatások saját munkahelyi, iskolai vagy közösségi identitással való bejelentkezni. | [Az Azure AD B2B licencelési útmutatója](../b2b/licensing-guidance.md) |
| [Döntse el, az eszközkezelési stratégiától](../devices/overview.md) | Döntse el, hogy a szervezet hogyan teszi lehetővé az eszközök használatát. A vs-csatlakozás regisztrálása, a saját eszköz és a vállalat által biztosított eszközök használata. | |
| [A vállalati Windows Hello üzembe helyezése a munkahelyen](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization) | A jelszóval nem rendelkező hitelesítés előkészítése a Windows Hello használatával | |
| [Jelszóval nem rendelkező hitelesítési módszerek üzembe helyezése a felhasználók számára](../authentication/concept-authentication-passwordless.md) | A felhasználók számára kényelmes, jelszóval nem rendelkező hitelesítési módszereket biztosíthat | Azure AD Premium P1 |

## <a name="phase-3-manage-applications"></a>3\. fázis: Alkalmazások kezelése

Ahogy továbbra is az előző fázisokon dolgozunk, azonosítjuk a tagjelölt alkalmazásokat az Azure AD-vel való áttelepítéshez és integrációhoz, valamint az alkalmazások telepítésének befejezéséhez.

| Tevékenység | Részletek | Szükséges licenc |
| ---- | ------ | ---------------- |
| Alkalmazások azonosítása | A szervezetben használt alkalmazások azonosítása: helyszíni, SaaS-alkalmazások a felhőben és más üzletági alkalmazások. Állapítsa meg, hogy ezeket az alkalmazásokat az Azure AD-vel kell-e felügyelni. | Nincs szükség licencre |
| [Támogatott SaaS-alkalmazások integrálása a katalógusban](../manage-apps/add-application-portal.md) | Az Azure AD olyan katalógussal rendelkezik, amely több ezer előre integrált alkalmazást tartalmaz. A szervezet által használt alkalmazások némelyike valószínűleg a katalógusban érhető el, közvetlenül a Azure Portal. | Azure AD Free |
| [Alkalmazás-proxy használata a helyszíni alkalmazások integrálásához](../manage-apps/application-proxy-add-on-premises-application.md) | Az alkalmazásproxy lehetővé teszi a felhasználók számára, hogy az Azure AD-fiókjával való bejelentkezéssel hozzáférjenek a helyszíni alkalmazásokhoz. | |

## <a name="phase-4-audit-privileged-identities-complete-an-access-review-and-manage-user-lifecycle"></a>4\. fázis: Magas jogosultsági szintű identitások naplózása, hozzáférési felülvizsgálat végrehajtása és felhasználói életciklus kezelése

A 4. fázis azt látja, hogy a rendszergazdák a legkevesebb jogosultsági alapelveket érvényesítik az adminisztrációhoz, az első hozzáférési felülvizsgálatok befejezését, és lehetővé teszik a közös felhasználói életciklus

| Tevékenység | Részletek | Szükséges licenc |
| ---- | ------ | ---------------- |
| [Privileged Identity Management használatának kikényszerítása](../privileged-identity-management/pim-security-wizard.md) | Távolítsa el a felügyeleti szerepköröket a normál napi és a napi felhasználói fiókok között. A rendszergazda felhasználók jogosultak arra, hogy a szerepkört a multi-Factor Authentication-ellenőrzések sikeres végrehajtása, az üzleti indoklás megadásával vagy a kijelölt jóváhagyók jóváhagyásának kérelmezésével használják. | Azure AD Premium P2 |
| [A PIM az Azure AD-címtárbeli szerepkörökhöz tartozó hozzáférési felülvizsgálat befejezése](../privileged-identity-management/pim-how-to-start-security-review.md) | A biztonsági és vezetői csapatokkal együttműködve hozhat létre hozzáférési felülvizsgálati szabályzatot a szervezeti szabályzatok alapján történő rendszergazdai hozzáférés áttekintéséhez. | Azure AD Premium P2 |
| [Dinamikus csoporttagság-házirendek implementálása](../users-groups-roles/groups-dynamic-membership.md) | A dinamikus csoportok használatával a felhasználók a HR (vagy az igazság forrása) alapján automatikusan hozzárendelhetők a csoportokhoz, például részleg, cím, régió és egyéb attribútumok. |  |
| [Csoportos alkalmazás-kiépítés implementálása](../manage-apps/what-is-access-management.md) | A csoport alapú hozzáférés-kezelés kiépítésével automatikusan kiépítheti a felhasználókat az SaaS-alkalmazásokhoz. |  |
| [A felhasználók üzembe helyezésének és megszüntetésének automatizálása](../manage-apps/user-provisioning.md) | A jogosulatlan hozzáférés megakadályozása érdekében távolítsa el az alkalmazotti fiók életciklusának manuális lépéseit. Az Azure AD a forrásból (HR-rendszerbe) hiteles identitások szinkronizálásával. |  |

## <a name="next-steps"></a>További lépések

[Az Azure AD licencelési és díjszabási adatai](https://azure.microsoft.com/pricing/details/active-directory/)

[Identitás- és eszköz-hozzáférési konfigurációk](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)

[Közös identitás- és eszköz-hozzáférési szabályzatok ajánlott](https://docs.microsoft.com/microsoft-365/enterprise/identity-access-policies)

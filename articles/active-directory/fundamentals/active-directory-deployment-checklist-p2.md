---
title: Azure AD-üzembehelyezési ellenőrzőlista
description: Az Azure Active Directory szolgáltatástelepítési ellenőrzőlistája
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
ms.openlocfilehash: f84226a631014b51338d47887fe3bafc969dc571
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063645"
---
# <a name="azure-active-directory-feature-deployment-guide"></a>Az Azure Active Directory szolgáltatástelepítési útmutatója

Ijesztőnek tűnhet az Azure Active Directory (Azure AD) telepítése a szervezet számára, és biztonságban tartása. Ez a cikk azokat a gyakori feladatokat határozza meg, amelyeket az ügyfelek 30, 60, 90 nap vagy több fázisban hasznosnak találnak a biztonsági állapotuk javítása érdekében. Még az Azure AD-t már üzembe helyezett szervezetek is használhatják ezt az útmutatót annak biztosítására, hogy a legtöbbet hozhassák ki befektetéseikből.

A jól megtervezett és végrehajtott identitás-infrastruktúra kikövezi az utat a megbízható hozzáférést a termelékenységi számítási feladatokhoz és adatokhoz csak az ismert felhasználók és eszközök számára.

Emellett az ügyfelek ellenőrizhetik [az identitásuk biztonságos pontszámát,](identity-secure-score.md) hogy megtudják, mennyire igazodnak a Microsoft ajánlott eljárásokhoz. Ellenőrizze a biztonságos pontszám végrehajtása előtt és után ezeket az ajánlásokat, hogy milyen jól csinálsz, mint mások az iparágban, és más szervezetek a méret.

## <a name="prerequisites"></a>Előfeltételek

Az útmutatóban szereplő számos javaslat az Azure AD Free szolgáltatással vagy licenc nélkül valósítható meg. Ahol licencre van szükség, kijelentjük, hogy a feladat elvégzéséhez legalább melyik licenc szükséges.

A licenceléssel kapcsolatos további információk a következő oldalakon találhatók:

* [Az Azure AD licencelése](https://azure.microsoft.com/pricing/details/active-directory/)
* [Microsoft 365 Nagyvállalati verzió](https://www.microsoft.com/en-us/licensing/product-licensing/microsoft-365-enterprise)
* [Enterprise Mobility + Security](https://www.microsoft.com/en-us/licensing/product-licensing/enterprise-mobility-security)
* [Az Azure AD B2B licencelési útmutatója](../b2b/licensing-guidance.md)

## <a name="phase-1-build-a-foundation-of-security"></a>1. fázis: A biztonság alapjainak kiépítése

Ebben a fázisban a rendszergazdák engedélyezik az alapszintű biztonsági funkciókat, hogy biztonságosabb és könnyebben használható alapot hozzanak létre az Azure AD-ben, mielőtt normál felhasználói fiókokat importálnánk vagy létrehoznánk. Ez az alapfázis biztosítja, hogy a kezdetektől fogva biztonságosabb állapotban legyen, és hogy a végfelhasználókat csak egyszer kell bevezetni az új fogalmakba.

| Tevékenység | Részletek | Szükséges licenc |
| ---- | ------ | ---------------- |
| [Egynél több globális rendszergazda kijelölése](../users-groups-roles/directory-emergency-access.md) | Rendeljen hozzá legalább két csak felhőalapú állandó globális rendszergazdai fiókot vészhelyzet esetén. Ezek a fiókok nem használhatók naponta, és hosszú és összetett jelszóval kell rendelkezniük. | Azure AD Free |
| [Lehetőség szerint használjon nem globális felügyeleti szerepköröket](../users-groups-roles/directory-assign-admin-roles.md) | Csak azokat a hozzáférést adja a rendszergazdáknak, amelyekhez csak azokat a területeket kell biztosítaniuk, amelyekhez hozzáférésre van szükségük. Nem minden rendszergazdának kell globális rendszergazdának lennie. | Azure AD Free |
| [Kiemelt identitáskezelés engedélyezése a felügyeleti szerepkör használatának nyomon követéséhez](../privileged-identity-management/pim-getting-started.md) | A kiemelt identitáskezelés engedélyezése a felügyeleti szerepkör használatának nyomon követéséhez. | Prémium szintű Azure AD P2 |
| [Önkiszolgáló jelszó-visszaállítás bevezetése](../authentication/howto-sspr-deployment.md) | Csökkentse az ügyfélszolgálati hívások jelszó-visszaállítását azáltal, hogy lehetővé teszi a munkatársak számára, hogy alaphelyzetbe állítsák saját jelszavukat az Ön rendszergazdai vezérlőként használt házirendek használatával. | |
| [Szervezetspecifikus egyéni tiltott jelszólista létrehozása](../authentication/howto-password-ban-bad-configure.md) | Megakadályozhatja, hogy a felhasználók olyan jelszavakat hozzanak létre, amelyek a szervezetből vagy területről származó gyakori szavakat vagy kifejezéseket tartalmaznak. | |
| [Helyszíni integráció engedélyezése az Azure AD jelszavas védelmével](../authentication/concept-password-ban-bad-on-premises.md) | A letiltott jelszavak listájának kiterjesztése a helyszíni címtárra annak biztosítása érdekében, hogy a helyszíni jelszavak is megfeleljenek a globális és bérlőspecifikus tiltott jelszólistáknak. | Prémium szintű Azure AD P1 |
| [A Microsoft jelszóra vonatkozó útmutatójának engedélyezése](https://www.microsoft.com/research/publication/password-guidance/) | Ne követelje meg a felhasználóktól, hogy meghatározott ütemezés szerint módosítsák a jelszavukat, tiltsák le az összetettségi követelményeket, és a felhasználók jobban emlékeznek a jelszavukra, és megtarthatják őket valami biztonságosnak. | Azure AD Free |
| [Felhőalapú felhasználói fiókok rendszeres jelszó-alaphelyzetbe állításának letiltása](../authentication/concept-sspr-policy.md#set-a-password-to-never-expire) | Az időszakos jelszó-visszaállítások arra ösztönzik a felhasználókat, hogy a meglévő jelszavakat. Használja a Microsoft jelszó-útmutató dokumentumában található irányelveket, és tükrözze a helyszíni szabályzatot a csak felhőbeli felhasználók számára. | Azure AD Free |
| [Az Azure Active Directory intelligens zárolásának testreszabása](../authentication/howto-password-smart-lockout.md) | A felhőalapú felhasználók helyszíni Active Directory-felhasználókra való replikálásának leállítása | |
| [Extranet intelligens zárolás engedélyezése az AD FS-hez](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) | Az AD FS extranet zárolása védelmet nyújt a találgatásos jelszótalálgatásos támadások ellen, miközben lehetővé teszi, hogy az érvényes AD FS-felhasználók továbbra is használják a fiókjukat. | |
| [Az Azure AD többtényezős hitelesítés ének telepítése feltételes hozzáférési szabályzatok használatával](../authentication/howto-mfa-getstarted.md) | A bizalmas alkalmazások feltételes hozzáférési házirendeket használó elérésekor a felhasználóknak kétlépéses ellenőrzést kell végrehajtaniuk. | Prémium szintű Azure AD P1 |
| [Az Azure Active Directory-identitásvédelem engedélyezése](../identity-protection/overview-identity-protection.md) | Engedélyezze a kockázatos bejelentkezések és a feltört hitelesítő adatok nyomon követését a szervezet felhasználói számára. | Prémium szintű Azure AD P2 |
| [Kockázatészlelés használata többtényezős hitelesítés és jelszómódosítások indításához](../authentication/tutorial-risk-based-sspr-mfa.md) | Engedélyezze az automatizálást, amely olyan eseményeket indíthat el, mint a többtényezős hitelesítés, a jelszó-visszaállítás és a bejelentkezések kockázat on-tisza. | Prémium szintű Azure AD P2 |
| [Konvergens regisztráció engedélyezése az önkiszolgáló jelszó-visszaállításhoz és az Azure AD többtényezős hitelesítéshez (előzetes verzió)](../authentication/concept-registration-mfa-sspr-converged.md) | Lehetővé teszi a felhasználók számára, hogy egyetlen közös felületről regisztráljanak az Azure többtényezős hitelesítéshez és az önkiszolgáló jelszó-visszaállításhoz. | Prémium szintű Azure AD P1 |

## <a name="phase-2-import-users-enable-synchronization-and-manage-devices"></a>2. fázis: Felhasználók importálása, szinkronizálás engedélyezése és eszközök kezelése

Ezután hozzáadjuk az 1.

| Tevékenység | Részletek | Szükséges licenc |
| ---- | ------ | ---------------- |
| [Az Azure AD Connect telepítése](../connect/active-directory-aadconnect-select-installation.md) | Felkészülés a felhasználók szinkronizálására a meglévő helyszíni címtárból a felhőbe. | Azure AD Free |
| [Jelszókivonat-szinkronizálás megvalósítása](../connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md) | Szinkronizálja a jelszókijelentéseket, hogy lehetővé tegye a jelszómódosítások replikálását, a rossz jelszóészlelést és -helyreállítást, valamint a kiszivárgott hitelesítő adatok jelentését. | Azure AD Free |
| [Jelszó-visszaírás megvalósítása](../authentication/howto-sspr-writeback.md) | A felhőben végrehajtott jelszómódosítások visszaírásának engedélyezése a helyszíni Windows Server Active Directory-környezetbe. | Prémium szintű Azure AD P1 |
| [Valósítsa meg az Azure AD Connect állapotát](../connect-health/active-directory-aadconnect-health.md) | Az Azure AD Connect-kiszolgálók, az AD FS-kiszolgálók és a tartományvezérlők kulcsfontosságú állapotstatisztikáinak figyelése. | Prémium szintű Azure AD P1 |
| [Licencek hozzárendelése a felhasználókhoz csoporttagság szerint az Azure Active Directoryban](../users-groups-roles/licensing-groups-assign.md) | Időt és energiát takaríthat meg, ha olyan licenccsoportokat hoz létre, amelyek a felhasználónkénti beállítás helyett csoportonként engedélyezik vagy tiltják le a szolgáltatásokat. | |
| [Terv létrehozása a vendégfelhasználók hozzáféréséhez](../b2b/what-is-b2b.md) | A vendégfelhasználókkal együttműködve lehetővé teszi számukra, hogy saját munkahelyi, iskolai vagy közösségi identitásukkal jelentkezzenek be az alkalmazásokba és szolgáltatásokba. | [Az Azure AD B2B licencelési útmutatója](../b2b/licensing-guidance.md) |
| [Az eszközkezelési stratégia eldöntése](../devices/overview.md) | Döntse el, hogy a szervezet mit engedélyez az eszközök tekintetében. Regisztráció vs csatlakozás, Hozd el a saját eszköz vs cég biztosított. | |
| [A Windows Hello vállalati verzió telepítése a szervezetben](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization) | Felkészülés a jelszó nélküli hitelesítésre a Windows Hello használatával | |
| [Jelszó nélküli hitelesítési módszerek telepítése a felhasználók számára](../authentication/concept-authentication-passwordless.md) | Kényelmes jelszó nélküli hitelesítési módszerek biztosítása a felhasználók számára | Prémium szintű Azure AD P1 |

## <a name="phase-3-manage-applications"></a>3. fázis: Alkalmazások kezelése

Ahogy továbbra is az előző fázisokra építünk, azonosítjuk a migrációra és az Azure AD-vel való integrációra vonatkozó jelölt alkalmazásokat, és befejezzük az alkalmazások beállítását.

| Tevékenység | Részletek | Szükséges licenc |
| ---- | ------ | ---------------- |
| Az alkalmazások azonosítása | Azonosítsa a szervezetben használt alkalmazásokat: a helyszíni, a felhőben lévő SaaS-alkalmazásokat és más üzletági alkalmazásokat. Határozza meg, hogy ezek az alkalmazások kezelhetők-e és kell-e az Azure AD-vel. | Nincs szükség licencre |
| [A támogatott SaaS-alkalmazások integrálása a galériába](../manage-apps/add-application-portal.md) | Az Azure AD egy katalógus, amely több ezer előre integrált alkalmazások. A szervezet által használt alkalmazások némelyike valószínűleg a közvetlenül az Azure Portalról elérhető katalógusban található. | Azure AD Free |
| [Helyszíni alkalmazások integrálása az alkalmazásproxy használatával](../manage-apps/application-proxy-add-on-premises-application.md) | Az alkalmazásproxy lehetővé teszi a felhasználók számára, hogy az Azure AD-fiókjukkal bejelentkezve hozzáférjenek a helyszíni alkalmazásokhoz. | |

## <a name="phase-4-audit-privileged-identities-complete-an-access-review-and-manage-user-lifecycle"></a>4. fázis: Kiemelt identitások naplózása, hozzáférés-felülvizsgálat befejezése és a felhasználói életciklus kezelése

fázis 4 látja a rendszergazdák érvényesítése a legkevesebb jogosultsági elvek et a felügyelet, az első hozzáférési felülvizsgálatok befejezése, és lehetővé teszi a közös felhasználói életciklus-feladatok automatizálását.

| Tevékenység | Részletek | Szükséges licenc |
| ---- | ------ | ---------------- |
| [A kiemelt identitáskezelés használatának kényszerítése](../privileged-identity-management/pim-security-wizard.md) | A felügyeleti szerepkörök eltávolítása a szokásos napi felhasználói fiókokból. Tegye a rendszergazda felhasználókat arra, hogy a többtényezős hitelesítés ellenőrzése, az üzleti indoklás megadása vagy a kijelölt jóváhagyók jóváhagyását kérjenek, hogy használhassák a szerepüket. | Prémium szintű Azure AD P2 |
| [Hozzáférés-felülvizsgálat befejezése az Azure AD címtárszerepkörökhöz a PIM-ben](../privileged-identity-management/pim-how-to-start-security-review.md) | A biztonsági és vezetői csapatokkal együttműködve hozzon létre egy hozzáférés-felülvizsgálati szabályzatot a felügyeleti hozzáférés áttekintéséhez a szervezet szabályzatai alapján. | Prémium szintű Azure AD P2 |
| [Dinamikus csoporttagsági házirendek megvalósítása](../users-groups-roles/groups-dynamic-membership.md) | A dinamikus csoportok segítségével automatikusan csoportokhoz rendelheti a felhasználókat a HR-ből (vagy az igazság forrásából), például a részlegből, a beosztásból, a régióból és más attribútumokból származó attribútumok alapján. |  |
| [Csoportalapú alkalmazáskiépítés megvalósítása](../manage-apps/what-is-access-management.md) | Csoportalapú hozzáférés-kezelési kiépítés használatával automatikusan kiépítheti a felhasználókat az SaaS-alkalmazásokhoz. |  |
| [A felhasználók kiépítésének és megszüntetésének automatizálása](../app-provisioning/user-provisioning.md) | Manuális lépések eltávolítása az alkalmazotti fiók életciklusából a jogosulatlan hozzáférés megakadályozása érdekében. Szinkronizálja az identitásokat az igazság forrásától (HR-rendszer) az Azure AD-vel. |  |

## <a name="next-steps"></a>További lépések

[Az Azure AD licencelési és díjszabási részletei](https://azure.microsoft.com/pricing/details/active-directory/)

[Identitás- és eszköz-hozzáférési konfigurációk](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)

[Közös ajánlott identitás- és eszközhozzáférési házirendek](https://docs.microsoft.com/microsoft-365/enterprise/identity-access-policies)

---
title: Feladatlista - Azure Active Directory Premium P2 licencelés funkció |} A Microsoft Docs
description: Telepítési ellenőrzőlista az Azure Active Directory Premium P2 a szolgáltatás telepítési 30 nappal, 90 nap, és más alkalmazásokhoz.
services: active-directory
ms.service: active-directory
ms.component: ''
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.custom: it-pro, seodec18
ms.openlocfilehash: 61cefe762b639dcc58c2e10fc845dd9729cb2cb2
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53078235"
---
# <a name="azure-active-directory-premium-p2-licensing-feature-checklist"></a>Az Azure Active Directory Premium P2 licencelési szolgáltatás ellenőrzőlista

Azt is kezdünk, üzembe helyezése az Azure Active Directory (Azure AD) a szervezet számára, és tárolja biztonságos helyen. Ez a cikk néhány gyakori feladatot, amely hasznos ügyfelek azonosítja. Ügyfelek általában hajtsa végre ezeket a feladatokat 30 napig, 90 napig, vagy túl folyamán javíthatja azok biztonsági állapotáról. Még akkor is, a szervezeteknek, akik már telepítették az Azure AD használatával ezzel az ellenőrzőlistával ellenőrizze, hogy a legtöbbet hozhassa ki a befektetési jutnak.

Egy jól megtervezett és végrehajtott identitás-infrastruktúra előkészíti az biztonságosabb hozzáférés a termelékenység számítási feladatok és az adatok csak a hitelesített felhasználók és eszközök számára.

## <a name="prerequisites"></a>Előfeltételek

Ez az útmutató feltételezi, hogy az Azure AD Premium P2 licenccel, Enterprise Mobility + Security E5, Microsoft 365 E5 csomag vagy egy hasonló licenc-csomagot.

[Az Azure AD licencelése](https://azure.microsoft.com/pricing/details/active-directory/)

[A Microsoft 365 nagyvállalati verzió](https://www.microsoft.com/en-us/licensing/product-licensing/microsoft-365-enterprise)

[Enterprise Mobility + Security](https://www.microsoft.com/en-us/licensing/product-licensing/enterprise-mobility-security)

## <a name="plan-and-deploy-day-1-30"></a>Tervezése és üzembe helyezése: 1 – 30 nap

- Egynél több globális rendszergazda (break-vészhelyzeti fiók) kijelölése
   - [A válságkezelési-hozzáférési rendszergazdai fiókok kezelése az Azure ad-ben](../users-groups-roles/directory-emergency-access.md)
- Kapcsolja be az Azure AD Privileged Identity Management (PIM) jelentések megtekintése
   - [Ismerkedés a PIM-mel](../privileged-identity-management/pim-getting-started.md)
- Ahol lehetséges, használja a nem globális rendszergazdai szerepkört.
   - [Rendszergazdai szerepkörök hozzárendelése az Azure Active Directoryban](../users-groups-roles/directory-assign-admin-roles.md)
- Hitelesítés
   - [Önkiszolgáló jelszó-visszaállítás bevezetése](../authentication/howto-sspr-deployment.md)
   - Az Azure AD jelszóvédelem (előzetes verzió) üzembe helyezése
      - [Rossz jelszavak, a szervezet számára](../authentication/concept-password-ban-bad.md)
      - [Az Azure AD jelszóvédelem a Windows Server Active Directory kényszerítése](../authentication/concept-password-ban-bad-on-premises.md)
   - Fiókzárolási házirendek konfigurálásához
      - [Az Azure Active Directory intelligens zárolás](../authentication/howto-password-smart-lockout.md)
      - [AD FS Extranet kizárás és intelligens extranetes fiókzárolás](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection)
   - [A feltételes hozzáférési szabályzatokkal az Azure AD multi-factor Authentication üzembe helyezése](../authentication/howto-mfa-getstarted.md)
   - [A hiperkonvergens regisztráció önkiszolgáló jelszó-visszaállítás és az Azure AD multi-factor Authentication (előzetes verzió) engedélyezése](../authentication/concept-registration-mfa-sspr-converged.md)
   - [Az Azure Active Directory Identity Protection engedélyezése](../identity-protection/enable.md)
      - [Kockázati események eseményindító multi-factor Authentication és a jelszó módosítására használata](../authentication/tutorial-risk-based-sspr-mfa.md)
   - [Tájékoztató a jelszavakról](https://www.microsoft.com/research/publication/password-guidance/)
      - Egy nyolc karakter minimálisan megengedettnél karbantartása, már nem szükségszerűen jobb.
      - Kiküszöbölése karakter-összeállítás követelményeinek.
      - [Felhasználói fiókok esetében kötelező rendszeres jelszó kérésekor kiküszöbölése.](../authentication/concept-sspr-policy.md#set-a-password-to-never-expire)
- Az Active Directoryból a helyszíni felhasználók szinkronizálása
   - [Az Azure AD Connect telepítése](../connect/active-directory-aadconnect-select-installation.md)
   - [Jelszókivonat-szinkronizálás megvalósítása](../connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md)
   - [A Jelszóvisszaíró megvalósítása](../authentication/howto-sspr-writeback.md)
   - [Megvalósítása az Azure AD Connect Health](../connect-health/active-directory-aadconnect-health.md)
- [Licencek hozzárendelése a felhasználók által az Azure Active Directory biztonságicsoport-tagság](../users-groups-roles/licensing-groups-assign.md)

## <a name="plan-and-deploy-day-31-90"></a>Tervezése és üzembe helyezése: 31 – 90 nap

- [Vendégfelhasználói hozzáférés tervezése](../b2b/what-is-b2b.md)
   - [Azure Active Directory B2B együttműködés felhasználók hozzáadása az Azure Portalon](../b2b/add-users-administrator.md)
   - [Az adott szervezetek a B2B-felhasználók meghívások engedélyezése vagy letiltása](../b2b/allow-deny-list.md)
   - [Támogatás B2B-felhasználók Azure AD-ben a hozzáférést a helyszíni alkalmazások](../b2b/hybrid-cloud-to-on-premises.md)
- Felhasználói életciklus-kezelési stratégia kapcsolatos döntések
- [Döntse el, az eszközkezelési stratégiától](../devices/overview.md)
   - [Használati forgatókönyvek és az Azure AD-csatlakozás telepítési szempontjai](../devices/azureadjoin-plan.md)
- [Kezelheti Windows Hello for Business a szervezetben](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization)

## <a name="plan-and-deploy-day-90-and-beyond"></a>Tervezése és üzembe helyezése: 90 nap és azt követően

- [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md)
   - [A PIM az Azure AD directory szerepkör-beállítások konfigurálása](../privileged-identity-management/pim-how-to-change-default-settings.md)
   - [A PIM az Azure AD-címtárbeli szerepkörök hozzárendelése](../privileged-identity-management/pim-how-to-add-role-to-user.md)
- [A PIM az Azure AD-címtárbeli szerepkörökhöz tartozó hozzáférési felülvizsgálat befejezése](../privileged-identity-management/pim-how-to-start-security-review.md)
- A felhasználói életciklus azok alkalmazásfüggőségeit kezelése
   - Az Azure AD identitás-életciklus kezelésének megközelítése rendelkezik
   - Jogosulatlan hozzáférés elkerülése érdekében távolítsa el az alkalmazott fiók életciklusa, manuális lépések:
      - Az Azure AD a forrásból (HR-rendszerbe) hiteles identitások szinkronizálásával. mutató hivatkozást a HR-rendszerekkel támogatott)
      - [Dinamikus csoportok használatával automatikusan felhasználók attribútumaik HR (vagy a forrásból hiteles), például a részleg, cím, régió, és egyéb attribútumok alapján csoportokhoz rendelhetők hozzá.](../users-groups-roles/groups-dynamic-membership.md)
      - [Használja a Csoportalapú hozzáférés-felügyeleti kiépítés automatikusan az SaaS-alkalmazások üzembe helyezése felhasználók számára.](../manage-apps/what-is-access-management.md)

## <a name="next-steps"></a>További lépések

[Identitás- és eszköz-hozzáférési konfigurációk](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)

[Közös identitás- és eszköz-hozzáférési szabályzatok ajánlott](https://docs.microsoft.com/microsoft-365/enterprise/identity-access-policies)

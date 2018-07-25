---
title: Használati forgatókönyvek és az Azure AD-csatlakozás telepítési szempontjai |} A Microsoft Docs
description: Ismerteti a rendszergazdák miként állíthatják be az Azure AD Join (alkalmazottak, diákok, más felhasználók) saját végfelhasználóik számára. Emellett ismerteti a különböző valós életből vett példák az Azure AD Join használatával.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
tags: azure-classic-portal
ms.component: devices
ms.assetid: 81d4461e-21c8-4fdd-9076-0e4991979f62
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: markvi
ms.openlocfilehash: 29480390e6854dcedeaf8f06c078ed2e4ed2b94d
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2018
ms.locfileid: "39223007"
---
# <a name="usage-scenarios-and-deployment-considerations-for-azure-ad-join"></a>Használati forgatókönyvek és az Azure AD-csatlakozás telepítési szempontjai
## <a name="usage-scenarios-for-azure-ad-join"></a>Az Azure AD Join használati forgatókönyvek
### <a name="scenario-1-businesses-largely-in-the-cloud"></a>1. forgatókönyv: Vállalkozások nagyrészt a felhőben
Az Azure Active Directory-csatlakozás (Azure AD-csatlakozás) előnyeit, ha jelenleg működik és identitáskezelést a felhőben a vállalkozása számára vagy helyez át a felhőbe hamarosan. Használhatja a bejelentkezni a Windows 10 Azure AD-ben létrehozott fiók. Keresztül [első tapasztalatok (FRX) folyamat](active-directory-azureadjoin-user-frx.md), vagy az Azure ad-ben való csatlakozás [a beállítások menüben](active-directory-azureadjoin-user-upgrade.md), a felhasználók gépeik csatlakozhat az Azure ad-hez.  A felhasználók számára is élvezheti az egyszeri bejelentkezés (SSO) hozzáférést felhőbeli erőforrások, például az Office 365, a böngészőjük vagy az Office-alkalmazásokban.

### <a name="scenario-2-educational-institutions"></a>2. forgatókönyv: Oktatási intézmények számára
Az oktatási intézmények általában rendelkeznek kétféle felhasználói: oktatók és diákok. Órákra járó oktatók számít a szervezet hosszabb távú tagjai. A helyszíni fiókok hoz kívánatos. De a tanulók a szervezet tagjai shorter-term és azok a fiókok kezelheti az Azure ad-ben. Ez azt jelenti, hogy directory méretezési lehet leküldeni a felhő folyamatban van a helyben tárolt helyett. Azt is jelenti, hogy tanulók fog tudni Windows jelentkezzen be az Azure AD-fiókjukat, és Office 365-erőforrások eléréséről az Office-alkalmazásokban.

### <a name="scenario-3-retail-businesses"></a>3. forgatókönyv: Kiskereskedelmi vállalkozások
Kiskereskedelmi vállalkozások idénymunkásoknak és a hosszú távú alkalmazottak rendelkezik. Általában a helyszíni fiókok létrehozása és használata a tartományhoz csatlakoztatott gépeket hosszabb távú teljes munkaidejű alkalmazottak számára. De idénymunkásoknak a szervezet tagjai shorter-term, és kívánatos, ahol a felhasználói licencek könnyen átvihetők a fiókjaik kezelését. A felhőben, az Office 365-licencet a felhasználói fiókokat hozhat létre, ha ezek a felhasználók a bejelentkezés a Windows és az Office alkalmazások az Azure AD-fiókot, amíg licenceiket az rugalmasabb karbantartása, miután elhagyják előnyeinek kihasználása.

### <a name="scenario-4-additional-scenarios"></a>4. példa: További forgatókönyvek
A korábban említett előnyei, valamint hogy a felhasználók, eszközök csatlakoztatása az Azure AD miatt egy egyszerűsített csatlakozó élmény, hatékony eszköz kezelése, automatikus mobileszköz-felügyeleti beléptetése és egyszeri bejelentkezés az Azure AD előnyei és a helyszíni erőforrásokhoz.  

## <a name="deployment-considerations-for-azure-ad-join"></a>Azure AD-csatlakozás telepítési szempontjai
### <a name="enable-your-users-to-join-a-company-owned-device-directly-to-azure-ad"></a>A vállalat által birtokolt eszköz csatlakoztatása közvetlenül az Azure AD-felhasználók
Vállalatok csak felhőalapú fiókok nyújthat partner cégek és szervezetek számára. Ezek a partnerek egyszerűen ezután hozzáférhetnek vállalati alkalmazásokhoz és erőforrásokhoz való egyszeri bejelentkezéshez. Ez a forgatókönyv akkor alkalmazható, a felhasználók számára, akik elsősorban a felhőben, például az Office 365-höz vagy SaaS-alkalmazásokat, amelyek az Azure AD-hitelesítés az erőforrások eléréséhez.

### <a name="prerequisites"></a>Előfeltételek
**Vállalati szintű (rendszergazdai)**

* Azure-előfizetés az Azure Active Directoryval  

**A felhasználói szinten**

* A Windows 10-es (Professional és Enterprise kiadás)

### <a name="administrator-tasks"></a>Rendszergazdai feladatok
* [Az eszközregisztráció beállítása](active-directory-azureadjoin-setup.md)

### <a name="user-tasks"></a>Felhasználói feladatok
* [Állítsa be az Azure AD-telepítés során egy új Windows 10 rendszerű eszköz](active-directory-azureadjoin-user-frx.md)
* [A beállítások menüből a Windows 10-es eszköz az Azure AD beállítása](active-directory-azureadjoin-user-upgrade.md)
* [Személyes Windows 10-es eszköz csatlakoztatása a szervezethez](active-directory-azureadjoin-personal-device.md)

## <a name="enable-byod-in-your-organization-for-windows-10"></a>BYOD engedélyezése a Windows 10-es szervezet
Állíthat be a felhasználók és az alkalmazottak a személyes (BYOD) eléréséhez a vállalati alkalmazások Windows-eszközök és erőforrások használatára. A felhasználók saját Azure AD-fiókok (munkahelyi vagy iskolai fiókok esetében) adhat hozzá egy saját Windows-eszköz biztonságos és megfelelő módon erőforrások eléréséhez.

### <a name="prerequisites"></a>Előfeltételek
**Vállalati szintű (rendszergazdai)**

* Azure AD-előfizetés

**A felhasználói szinten**

* A Windows 10-es (Professional és Enterprise kiadás)

### <a name="administrator-tasks"></a>Rendszergazdai feladatok
* [Az eszközregisztráció beállítása](active-directory-azureadjoin-setup.md)

### <a name="user-tasks"></a>Felhasználói feladatok
* [Személyes Windows 10-es eszköz csatlakoztatása a szervezethez](active-directory-azureadjoin-personal-device.md)

## <a name="additional-information"></a>További információ
* [Vállalati használatú Windows 10: Az eszközök munkahelyi célú használata](active-directory-azureadjoin-windows10-devices-overview.md)
* [A felhőalapú képességek kiterjesztése a Windows 10-eszközökre az Azure Active Directory Joinon keresztül](active-directory-azureadjoin-user-upgrade.md)
* [Identitások Microsoft Passporton keresztül jelszó nélküli hitelesítése](active-directory-azureadjoin-passport.md)
* [További információk az Azure AD Join használati forgatókönyveiről](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Tartományhoz csatlakoztatott eszközök csatlakoztatása az Azure AD-hez Windows 10-es környezetben](active-directory-azureadjoin-devices-group-policy.md)
* [Az Azure AD Join beállítása](active-directory-azureadjoin-setup.md)


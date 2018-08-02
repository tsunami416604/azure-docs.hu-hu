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
ms.date: 08/01/2018
ms.author: markvi
ms.openlocfilehash: a145b4184fbebd9c4f447face1c47bec20c0ec32
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39414888"
---
# <a name="usage-scenarios-and-deployment-considerations-for-azure-ad-join"></a>Használati forgatókönyvek és az Azure AD-csatlakozás telepítési szempontjai
## <a name="usage-scenarios-for-azure-ad-join"></a>Az Azure AD Join használati forgatókönyvek
### <a name="scenario-1-businesses-largely-in-the-cloud"></a>1. forgatókönyv: Vállalkozások nagyrészt a felhőben
Az Azure Active Directory-csatlakozás (Azure AD-csatlakozás) előnyeit, ha jelenleg működik és identitáskezelést a felhőben a vállalkozása számára vagy helyez át a felhőbe hamarosan. Használhatja a bejelentkezni a Windows 10 Azure AD-ben létrehozott fiók. Keresztül [első tapasztalatok (FRX) folyamat](azuread-joined-devices-frx.md), vagy az Azure ad-ben való csatlakozás [a beállítások menüben](../user-help/device-management-azuread-joined-devices-setup.md), a felhasználók gépeik csatlakozhat az Azure ad-hez.  A felhasználók számára is élvezheti az egyszeri bejelentkezés (SSO) hozzáférést felhőbeli erőforrások, például az Office 365, a böngészőjük vagy az Office-alkalmazásokban.

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
* [Az eszközregisztráció beállítása](device-management-azure-portal.md)

### <a name="user-tasks"></a>Felhasználói feladatok
* [Állítsa be az Azure AD-telepítés során egy új Windows 10 rendszerű eszköz](azuread-joined-devices-frx.md)
* [A beállítások menüből a Windows 10-es eszköz az Azure AD beállítása](../user-help/device-management-azuread-registered-devices-windows10-setup.md)
* [Személyes Windows 10-es eszköz csatlakoztatása a szervezethez](../user-help/device-management-azuread-joined-devices-setup.md)

## <a name="enable-byod-in-your-organization-for-windows-10"></a>BYOD engedélyezése a Windows 10-es szervezet
Állíthat be a felhasználók és az alkalmazottak a személyes (BYOD) eléréséhez a vállalati alkalmazások Windows-eszközök és erőforrások használatára. A felhasználók saját Azure AD-fiókok (munkahelyi vagy iskolai fiókok esetében) adhat hozzá egy saját Windows-eszköz biztonságos és megfelelő módon erőforrások eléréséhez.

### <a name="prerequisites"></a>Előfeltételek
**Vállalati szintű (rendszergazdai)**

* Azure AD-előfizetés

**A felhasználói szinten**

* A Windows 10-es (Professional és Enterprise kiadás)

### <a name="administrator-tasks"></a>Rendszergazdai feladatok
* [Az eszközregisztráció beállítása](device-management-azure-portal.md)

### <a name="user-tasks"></a>Felhasználói feladatok
* [Személyes Windows 10-es eszköz csatlakoztatása a szervezethez](../user-help/device-management-azuread-joined-devices-setup.md)

## <a name="next-steps"></a>További lépések

- [Eszközkezelés](overview.md)


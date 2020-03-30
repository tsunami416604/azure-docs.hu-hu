---
title: Az Azure AD alkalmazásokba való integrálásának első lépései | Microsoft dokumentumok
description: Ez a cikk egy első lépések útmutató az Azure Active Directory (AD) helyszíni alkalmazásokkal és felhőalapú alkalmazásokkal való integrálásához.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89b16a8479f8975d101b8a4e26dcb1885d9730bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063380"
---
# <a name="integrating-azure-active-directory-with-applications-getting-started-guide"></a>Az Azure Active Directory integrálása az első lépésekhez kapcsolódó alkalmazásokkal útmutató

Ez a témakör összefoglalja az alkalmazások Azure Active Directoryval (AD) való integrálásának folyamatát. Az alábbi szakaszok mindegyike egy részletesebb témakör rövid összefoglalását tartalmazza, így azonosíthatja, hogy az első lépések útmutatómely részei relevánsak az Ön számára.

A részletes telepítési tervek letöltéséhez olvassa el a [Következő lépések ( További lépések) részt.](#next-steps)

## <a name="take-inventory"></a>Leltározás
Mielőtt integrálna az alkalmazásokat az Azure AD-vel, fontos tudni, hogy hol van, és hová szeretne menni.  A következő kérdések célja, hogy segítsen gondolkodni az Azure AD-alkalmazás integrációs projekt.

### <a name="application-inventory"></a>Alkalmazásleltár
* Hol vannak az összes jelentkezésed? Kié?
* Milyen típusú hitelesítésre van szükségük az alkalmazásoknak?
* Kinek van szüksége hozzáférésre, hogy mely alkalmazások?
* Telepít egy új alkalmazást?
  * Házon belül fogja megépíteni, és üzembe helyezi egy Azure-számítási példányon?
  * Fogja használni az Azure-alkalmazásgalériában elérhetőt?

### <a name="user-and-group-inventory"></a>Felhasználói és csoportkészlet
* Hol laknak a felhasználói fiókok?
  * Helyszíni Active Directory
  * Azure AD
  * Egy különálló alkalmazásadatbázison belül, amely a tulajdonában van
  * Nem engedélyezett kérelmekben
  * A fentiek közül az összes
* Milyen engedélyekkel és szerepkör-hozzárendelésekkel rendelkeznek jelenleg az egyes felhasználók? Át kell tekintenie a hozzáférésüket, vagy biztos benne, hogy a felhasználói hozzáférés és a szerepkör-hozzárendelések megfelelőek most?
* Vannak már létrehozott csoportok a helyszíni Active Directoryban?
  * Hogyan szerveződnek a csoportok?
  * Kik a csoport tagjai?
  * Milyen engedélyekkel/szerepkör-hozzárendelésekkel rendelkeznek jelenleg a csoportok?
* Meg kell tisztítania a felhasználói/csoport adatbázisokat az integráció előtt?  (Ez egy nagyon fontos kérdés. Szemét be, szemét ki.)

### <a name="access-management-inventory"></a>Hozzáférés-kezelési leltár
* Jelenleg hogyan kezeli az alkalmazásokhoz való felhasználói hozzáférést? Ezen változtatni kell?  Gondolt már más módon a hozzáférés kezelésére, például [az RBAC-val?](../../role-based-access-control/role-assignments-portal.md)
* Kinek milyen hozzáférése van?

Lehet, hogy nem tudja a választ az összes kérdésre előre, de ez rendben van.  Ez az útmutató segít megválaszolni néhány ilyen kérdést, és megalapozott döntéseket hozni.

### <a name="find-unsanctioned-cloud-applications-with-cloud-discovery"></a>Nem engedélyezett felhőalkalmazások keresése a Cloud Discovery segítségével

Mint már említettük, előfordulhat, hogy az alkalmazások, amelyek nem kezeli a szervezet eddig.  A leltározási folyamat részeként nem engedélyezett felhőalkalmazások at találhat. Lásd: [A felhőfelderítés beállítása.](/cloud-app-security/set-up-cloud-discovery)

## <a name="integrating-applications-with-azure-ad"></a>Alkalmazások integrálása az Azure AD segítségével
Az alábbi cikkek ismertetik a különböző módon alkalmazások integrálása az Azure AD-vel, és némi útmutatást nyújt.

* [A használandó Active Directory meghatározása](../fundamentals/active-directory-administer.md)
* [Alkalmazások használata az Azure-alkalmazásgyűjteményben](what-is-single-sign-on.md)
* [SaaS-alkalmazások oktatóanyaglistájának integrálása](../active-directory-saas-tutorial-list.md)

### <a name="authentication-types"></a>Hitelesítési típusok
Az egyes alkalmazások különböző hitelesítési követelményekkel rendelkezhetnek. Az Azure AD-vel az aláírási tanúsítványok saml 2.0, WS-federation vagy OpenID Connect protokollokat, valamint jelszó egyszeri bejelentkezést használó alkalmazásokkal használhatók. Az Azure AD-vel használható alkalmazáshitelesítési típusokról az [összevont egyszeri bejelentkezés hez](manage-certificates-for-federated-single-sign-on.md) szükséges tanúsítványok kezelése az Azure Active Directoryban és a Jelszó egyszeri [bejelentkezés.](what-is-single-sign-on.md)

### <a name="enabling-sso-with-azure-ad-app-proxy"></a>SSO engedélyezése az Azure AD alkalmazásproxyval
A Microsoft Azure AD alkalmazásproxyval biztonságosan, bárhonnan és bármilyen eszközről hozzáférést biztosíthat a magánhálózaton belül található alkalmazásokhoz. Miután telepítette az alkalmazásproxy-összekötőt a környezetben, könnyen konfigurálható az Azure AD-vel.

### <a name="integrating-custom-applications"></a>Egyéni alkalmazások integrálása
Ha új alkalmazást ír, és segítséget szeretne nyújtani a fejlesztőknek az Azure AD nyújtotta lehetőségek kihasználásában, olvassa [el a Fejlesztők irányítása](../active-directory-applications-guiding-developers-for-lob-applications.md)című témakört.

Ha hozzá szeretné adni az egyéni alkalmazást az Azure-alkalmazástárhoz, olvassa el [a "Saját alkalmazás hozása" című témakört az Azure AD önkiszolgáló SAML-konfigurációval.](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-now-in-preview/)

## <a name="managing-access-to-applications"></a>Az alkalmazásokhoz való hozzáférés kezelése
Az alábbi cikkek ismertetik, hogyan kezelheti az alkalmazásokhoz való hozzáférést, miután az Azure AD-vel integrálva lettek az Azure AD-vel az Azure AD-összekötők és az Azure AD használatával.

* [Alkalmazásokhoz való hozzáférés kezelése az Azure AD használatával](what-is-access-management.md)
* [Automatizálás az Azure AD-összekötőkkel](../app-provisioning/user-provisioning.md)
* [Felhasználók hozzárendelése egy alkalmazáshoz](../active-directory-applications-guiding-developers-assigning-users.md)
* [Csoportok hozzárendelése egy alkalmazáshoz](../active-directory-applications-guiding-developers-assigning-groups.md)
* [Fiókok megosztása](../active-directory-sharing-accounts.md)

## <a name="next-steps"></a>További lépések
Részletes információkért töltse le az Azure Active Directory telepítési terveit a [GitHubról.](https://aka.ms/deploymentplans) A katalógusalkalmazások, letöltheti a központi telepítési tervek egyszeri bejelentkezés, feltételes hozzáférés és a felhasználói kiépítés az [Azure Portalon](https://portal.azure.com)keresztül. 

Központi telepítési terv letöltése az Azure Portalról:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Válassza a **Vállalati alkalmazások** | **válasszon alkalmazástelepítési** | **tervet.**

Kérjük, a [telepítési terv felmérésével](https://aka.ms/DeploymentPlanFeedback)adjon visszajelzést a telepítési tervekről.

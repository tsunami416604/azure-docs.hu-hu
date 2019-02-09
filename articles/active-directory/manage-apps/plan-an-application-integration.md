---
title: Ismerkedés az Azure AD integrálása-alkalmazásokba |} A Microsoft Docs
description: Ez a cikk az első lépésekről szóló útmutatót az Azure Active Directory (AD) integrálása a helyszíni alkalmazások és a felhőalapú alkalmazások.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2018
ms.author: celested
ms.reviewer: asteen
ms.openlocfilehash: a412627378d574e28cc1a41fa0e5f9059ab62761
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55960700"
---
# <a name="integrating-azure-active-directory-with-applications-getting-started-guide"></a>Azure Active Directory integrálása az alkalmazások első lépések útmutató

Ez a témakör felsorolja az alkalmazások integrálása az Azure Active Directory (AD) a folyamat. Az alábbi szakaszokban mindegyike tartalmaz egy részletesebb témakör rövid összefoglalása, így azonosíthatja a kezdeti lépéseket ismertető útmutató mely részei az Ön számára.

Részletes üzembe helyezési csomagok letöltéséhez lásd: [további lépések](#next-steps).

## <a name="take-inventory"></a>Leltározása
Alkalmazások integrálása az Azure ad-ben, mielőtt fontos tudni, hogy hol vannak és hol szeretne belépni.  Az alábbi kérdések célja, hogy segítsen, gondolja át az Azure AD-integrációs projektet.

### <a name="application-inventory"></a>Alkalmazásleltár
* Hol találhatók az alkalmazásokat? Kié őket?
* Milyen típusú hitelesítést igényelnek az alkalmazások?
* Ki kell, hogy mely alkalmazásokhoz való hozzáférés?
* Szeretné üzembe helyezni egy új alkalmazást?
  * Lesz, házon belül hozhat létre és hogyan telepítheti egy Azure-beli számítási példányon?
  * Fog használni, amely az Azure Alkalmazásgyűjteményben érhető el?

### <a name="user-and-group-inventory"></a>Felhasználó- és szoftverleltár
* Hol találhatók a a felhasználói fiókokat?
  * Helyszíni Active Directory
  * Azure AD
  * Egy különálló alkalmazás-adatbázis, amely a saját belül
  * Nem engedélyezett alkalmazások
  * A fentiek mindegyikét
* Milyen engedélyeket és a szerepkör-hozzárendeléseket az egyes felhasználók jelenleg rendelkezik? Van szüksége, tekintse át a hozzáférésüket, vagy arra, hogy a felhasználói hozzáférés és a szerepkör-hozzárendelések megfelelő most már Ön?
* Csoportok már meghatározott a helyszíni Active Directoryban?
  * Hogyan vannak rendszerezve a csoportok?
  * Kik a csoport tagjai?
  * Milyen engedélyek/szerepkör-hozzárendeléseit a csoportok jelenleg rendelkezik?
* Kell felhasználó/csoport adatbázisok karbantartása előtt integrálása?  (Ez a kérdés meglehetősen fontos. Szemétgyűjtési a szemétgyűjtési ki.)

### <a name="access-management-inventory"></a>Hozzáférés – leltár
* Hogyan jelenleg kezelhetők az alkalmazásokhoz való felhasználói hozzáférést? Nem kell módosítani?  Tekintette kezelésére, mint például a más módon [RBAC](../../role-based-access-control/role-assignments-portal.md) például?
* Akik milyen hozzáférésre van szüksége?

Talán nem rendelkezik az összes ezekre a kérdésekre adott válaszokat meghozni, de ez nem probléma.  Ez az útmutató segítségével kérdések megválaszolásához és néhány megalapozottabb döntéseket hozhat.

### <a name="find-unsanctioned-cloud-applications-with-cloud-discovery"></a>Keresse meg a Cloud Discovery nem engedélyezett felhőalkalmazások

Ahogy említettük, az alkalmazásokat, amelyek még nem lett az eddig a szervezet által felügyelt lehet.  A hardverleltározási folyamatának részeként nem engedélyezett felhőalkalmazások megkereséséhez lehetőség. Lásd: [Cloud Discovery beállítása](/cloud-app-security/set-up-cloud-discovery).

## <a name="integrating-applications-with-azure-ad"></a>Alkalmazások integrálása az Azure ad-ben
Az alábbi cikkekben különböző módokon alkalmazások integrálása az Azure ad-ben, és útmutatást.

* [Amely meghatározza, hogy mely Active Directory használata](../fundamentals/active-directory-administer.md)
* [Az Azure alkalmazásgyűjteményben alkalmazások használata](what-is-single-sign-on.md)
* [Integrálása az SaaS-alkalmazások oktatóanyagok listáját](../active-directory-saas-tutorial-list.md)

### <a name="authentication-types"></a>Hitelesítési típusok
Különböző hitelesítési követelmények vonatkozhatnak a kulcsláncot. Az Azure AD-aláíró tanúsítványok használható SAML 2.0, WS-Federation, vagy OpenID Connect protokollok, valamint jelszó az egyszeri bejelentkezést használó alkalmazások. Alkalmazással kapcsolatos további információk használható az Azure AD-hitelesítési típusok: [tanúsítványok kezelése az összevont egyszeri bejelentkezés az Azure Active Directoryban](manage-certificates-for-federated-single-sign-on.md) és [jelszó alapján egyszeri bejelentkezési](what-is-single-sign-on.md).

### <a name="enabling-sso-with-azure-ad-app-proxy"></a>Egyszeri bejelentkezés az Azure AD-alkalmazásproxy engedélyezése
A Microsoft Azure AD-alkalmazásproxy található alkalmazások futnak a magánhálózaton belülről biztonságosan, bárhonnan, bármilyen eszközről hozzáférést biztosíthat. A környezetben egy alkalmazásproxy-összekötő telepítését követően, könnyen konfigurálható az Azure ad-ben.

### <a name="integrating-custom-applications"></a>Egyéni alkalmazások integrálása
Ha egy új alkalmazást és a kívánt a fejlesztők kihasználva az Azure AD-ben talál [Guiding fejlesztők](../active-directory-applications-guiding-developers-for-lob-applications.md).

Ha azt szeretné, adja hozzá az egyéni alkalmazást az Azure Alkalmazásgyűjteményben, lásd: ["Saját alkalmazások használata" Azure AD önkiszolgáló SAML-konfigurációja az](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-now-in-preview/).

## <a name="managing-access-to-applications"></a>Alkalmazásokhoz való hozzáférés kezelése
Az alábbi cikkek ismertetik, hogyan alkalmazásokhoz való hozzáférést kezelheti az Azure AD-bA az Azure AD-összekötők és az Azure AD integrálása után.

* [Az Azure AD-vel az alkalmazásokhoz való hozzáférés kezelése](what-is-access-management.md)
* [Az Azure AD-összekötők automatizálása](user-provisioning.md)
* [Felhasználók hozzárendelése egy alkalmazáshoz](../active-directory-applications-guiding-developers-assigning-users.md)
* [Csoportok hozzárendelése egy alkalmazáshoz](../active-directory-applications-guiding-developers-assigning-groups.md)
* [Fiókok megosztása](../active-directory-sharing-accounts.md)

## <a name="next-steps"></a>További lépések
Részletes információkért töltse le az Azure Active Directory központi telepítési csomagot a következőből [GitHub](https://aka.ms/deploymentplans). Katalógus-alkalmazások, központi telepítési csomagok egyszeri bejelentkezést, a feltételes hozzáférés és a felhasználói telepítés használatával letöltheti a [az Azure portal](https://portal.azure.com). 

A központi telepítési csomag letöltése az Azure Portalról:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **vállalati alkalmazások** | **válasszon ki egy alkalmazást** | **telepítési tervének**.

Kérjük, jelezze a központi telepítési csomagok tovább a [központi telepítési csomag felmérés](https://aka.ms/DeploymentPlanFeedback).

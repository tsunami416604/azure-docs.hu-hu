---
title: Ismerkedés az Azure AD integrálása az alkalmazásokkal |} Microsoft Docs
description: Ez a cikk egy első lépésekről szóló útmutatót az Azure Active Directory (AD) integrálása a helyszíni alkalmazások és a felhőalapú alkalmazásokhoz.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/15/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 523508bc2caeb4315caa6597662174aa0fdeb47b
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
---
# <a name="integrating-azure-active-directory-with-applications-getting-started-guide"></a>Azure Active Directory integrálása alkalmazások első lépések útmutató
## <a name="overview"></a>Áttekintés
Ez a témakör célja, akkor a terv az alkalmazások integrálása az Azure Active Directory (AD). Minden, az alábbi szakaszokból áll tartalmaz részletes témakör röviden ismerteti, hogy azonosíthassa az első lépésekről szóló útmutatót részeket relevánsak.  Mélyebb bemutatója a hivatkozások követése egyes témák.

## <a name="before-you-begin-take-inventory"></a>Mielőtt elkezdené, leltározása
Alkalmazások integrálása az Azure ad-vel való ugrás előtt fontos tudni, hogy hol van, és ahol kíván lépni.  Az alábbi kérdések célja, hogy segítséget gondolja át az Azure AD-integrációs projektet.

### <a name="application-inventory"></a>Alkalmazásleltár
* Hol találhatók az alkalmazásokat? Azok ki tulajdonosa?
* Milyen típusú hitelesítés van szükség az alkalmazások?
* Ki kell, hogy mely alkalmazások elérésére?
* Szeretné központilag telepíti egy új alkalmazást?
  * Fog összeállítani, belső fejlesztésű-telepítheti az Azure compute példányhoz?
  * Fog használni, amely elérhető az Azure alkalmazás katalógusában?

### <a name="user-and-group-inventory"></a>Felhasználó- és szoftverleltár
* Hol találhatók a felhasználói fiókjait?
  * Helyszíni Active Directory
  * Azure AD
  * Egy különálló alkalmazás adatbázis saját belül
  * Jóvá nem hagyott alkalmazások
  * A fentiek mindegyikét
* Milyen engedélyeket és a szerepkör-hozzárendelések egyes felhasználók jelenleg rendelkeznek? Tekintse át a hozzáférésüket szeretne, vagy biztos, hogy a felhasználói hozzáférés és a szerepkör-hozzárendelések is megfelelő most?
* Csoportok már meghatározott a helyszíni Active Directoryban?
  * Hogyan vannak rendszerezve a csoportok?
  * A csoport tagjai számára?
  * Milyen engedélyekkel/szerepkör-hozzárendelések a csoportok jelenleg rendelkeznek?
* Szükség lesz-felhasználó vagy csoport adatbázisok karbantartása integrálása előtt?  (Ez a közérthető fontos kérdésre. Szemétgyűjtési a szemétgyűjtési ki.)

### <a name="access-management-inventory"></a>Access felügyeleti készlet
* Hogyan jelenleg felügyel alkalmazásokhoz való felhasználói hozzáférések? Nem, amely kell?  Tekintette egyéb módjai kezelésére, többek között a [RBAC](../../role-based-access-control/role-assignments-portal.md) például?
* Ki kell, hogy mi a hozzáférést?

Lehet, hogy nincs minden ezekre a kérdésekre adott válaszokat előre, de ez nem probléma.  Ez az útmutató segítséget nyújt válaszolja meg a fenti kérdések közül, és néhány kérdésekre vonatkozó döntések meghozatalában.

## <a name="prerequisites"></a>Előfeltételek
* Azure-előfizetés és Azure Active Directory címtárhoz.  Ha még nem rendelkezik Azure-előfizetéssel, kipróbálhatja Azure ingyenes 30 napig. [Próbálja ki!](https://azure.microsoft.com/trial/get-started-active-directory/)

## <a name="application-integration-with-azure-ad"></a>Alkalmazások integrálása az Azure ad szolgáltatással
### <a name="finding-unsanctioned-cloud-applications-with-cloud-app-discovery"></a>A Cloud App Discovery megállapítás nem engedélyezett a felhőalapú alkalmazásokhoz
Fent említett, alkalmazásokat, amelyek még nem lett kezeli a szervezet eddig lehet.  A folyamat részeként jóvá nem hagyott alkalmazások kereséséhez. Lásd: [jóvá nem hagyott alkalmazások a Cloud App Discovery keresése](cloud-app-discovery.md).

### <a name="authentication-types"></a>Hitelesítési típusok
Az alkalmazások előfordulhat, hogy különböző hitelesítési követelményekkel rendelkező. Az Azure AD aláíró tanúsítványok használható SAML 2.0, WS-Federation, vagy OpenID Connect protokollok, valamint jelszó egyszeri bejelentkezést használó alkalmazásokat. Alkalmazással kapcsolatos további információk az Azure AD hitelesítési típus: [tanúsítványok kezelése az összevont egyszeri bejelentkezés az Azure Active Directoryban](../active-directory-sso-certs.md) és [jelszó alapján egyszeri bejelentkezési](../active-directory-appssoaccess-whatis.md).

### <a name="enabling-sso-with-azure-ad-app-proxy"></a>Az Azure AD alkalmazás Proxy egyszeri bejelentkezés engedélyezése
A Microsoft Azure AD-alkalmazásproxy belül található alkalmazásokhoz a magánhálózaton biztonságosan, bárhonnan és bármilyen eszközről hozzáférést biztosíthat. Miután telepítette az alkalmazásproxy-összekötő a környezetben, könnyen beállítható az Azure ad-val.

### <a name="integrating-applications-with-azure-ad"></a>Alkalmazások integrálása az Azure ad szolgáltatással
A következő cikkekben ismertetik a különböző alkalmazások az Azure AD integrálása és néhány útmutatást nyújtanak.

* [Active Directory használandó meghatározása](../active-directory-administer.md)
* [Alkalmazások használata az az Azure alkalmazáskatalógusában](../active-directory-appssoaccess-whatis.md)
* [Integrálása SaaS-alkalmazások oktatóanyagok listáját](../active-directory-saas-tutorial-list.md)

## <a name="managing-access-to-applications"></a>Alkalmazás-hozzáférés kezelése
A következő cikkek ismertetik, hogyan után az Azure AD-összekötők használata az Azure AD és az Azure AD integrált alkalmazások elérésére kezelheti.

* [Az Azure AD alkalmazásokhoz való hozzáférés kezelése](../active-directory-managing-access-to-apps.md)
* [Az Azure AD-összekötők automatizálása](../active-directory-saas-app-provisioning.md)
* [Felhasználók hozzárendelése egy alkalmazáshoz](../active-directory-applications-guiding-developers-assigning-users.md)
* [Csoportok hozzárendelése egy alkalmazáshoz](../active-directory-applications-guiding-developers-assigning-groups.md)
* [Fiókok megosztása](../active-directory-sharing-accounts.md)

## <a name="integrating-custom-applications"></a>Egyéni alkalmazások integrálása
Ha egy új alkalmazás és a kívánt fejlesztők segít az Azure AD alkalmazásaiban [Guiding fejlesztők](../active-directory-applications-guiding-developers-for-lob-applications.md).

Ha hozzá szeretne adni az alkalmazást az Azure alkalmazás gyűjteményébe, lásd: ["Állapotba az alkalmazásba" Azure AD önkiszolgáló SAML konfigurációval](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-now-in-preview/).

## <a name="see-also"></a>Lásd még
* [Az Azure Active Directory segítségével végzett alkalmazásfelügyeletre vonatkozó cikkek jegyzéke](../active-directory-apps-index.md)


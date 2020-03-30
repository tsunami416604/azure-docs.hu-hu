---
title: Atlassian Jira/Confluence felügyeleti útmutató – Azure Active Directory| Microsoft dokumentumok
description: Felügyeleti útmutató az Atlassian Jira és az Azure Active Directoryval (Azure AD) való összefolyás használatához.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8679f9a03fded546db68f058bca716ba053aa0fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73161210"
---
# <a name="atlassian-jira-and-confluence-admin-guide-for-azure-active-directory"></a>Atlassian Jira és összefolyásadmin útmutató az Azure Active Directoryhoz

## <a name="overview"></a>Áttekintés

Az Azure Active Directory (Azure AD) egyszeri bejelentkezés (SSO) beépülő modul lehetővé teszi a Microsoft Azure AD-ügyfelek számára, hogy munkahelyi vagy iskolai fiókjukat használják az Atlassian Jira és a Confluence Server alapú termékekbe való bejelentkezéshez. SamL 2.0-alapú egyszeri felosztott at valósít meg.

## <a name="how-it-works"></a>Működés

Ha a felhasználók be szeretnének jelentkezni az Atlassian Jira vagy a Confluence alkalmazásba, a bejelentkezés **az Azure AD-vel** gomb jelenik meg a bejelentkezési oldalon. Amikor kiválasztják, akkor az Azure AD-szervezet bejelentkezési lapján (azaz a munkahelyi vagy iskolai fiók) használatával kell bejelentkezniük.

A felhasználók hitelesítése után képesnek kell lennie arra, hogy jelentkezzen be az alkalmazásba. Ha már hitelesítették a munkahelyi vagy iskolai fiókjuk azonosítójával és jelszavával, akkor közvetlenül bejelentkeznek az alkalmazásba. 

A bejelentkezés a Jira és az Összefolyás között működik. Ha a felhasználók be vannak jelentkezve a Jira alkalmazásba, és a Összefolyás ugyanabban a böngészőablakban nyílik meg, nem kell megadniuk a másik alkalmazás hitelesítő adatait. 

A felhasználók a Munkahelyi vagy iskolai fiók alatt a Saját alkalmazások on keresztül is eljuthatnak az Atlassian termékhez. A hitelesítő adatok megadása nélkül kell bejelentkezniük.

> [!NOTE]
> A felhasználói kiépítés nem a beépülő modulon keresztül történik.

## <a name="audience"></a>Célközönség

A Jira és a Confluence rendszergazdái a beépülő modul segítségével engedélyezhetik az Egyszeri bejelentkezést az Azure AD használatával.

## <a name="assumptions"></a>Feltételezések

* Jira és összefolyása példányok HTTPS engedélyezve vannak.
* A felhasználók már létre Jira vagy összefolyása.
* A felhasználók szerepköröket rendelnek a Jira vagy a Összefolyás.
* A rendszergazdák hozzáférhetnek a beépülő modul konfigurálásához szükséges információkhoz.
* Jira vagy összefolyása a vállalati hálózaton kívül is elérhető.
* A beépülő modul csak a Jira és a Confluence helyszíni verziójával működik.

## <a name="prerequisites"></a>Előfeltételek

A beépülő modul telepítése előtt vegye figyelembe az alábbi információkat:

* A Jira és az Összefolyás windowsos 64 bites verzióra van telepítve.
* Jira és összefolyása verziók HTTPS engedélyezve.
* Jira és összefolyása áll rendelkezésre az interneten.
* A Jira és a Confluence rendszergazdai hitelesítő adatai érvényben vannak.
* Rendszergazdai hitelesítő adatok vannak érvényben az Azure AD.
* WebSudo le van tiltva Jira és összefolyása.

## <a name="supported-versions-of-jira-and-confluence"></a>A Jira és a Összefolyás támogatott változatai

A beépülő modul a Jira és a Confluence következő verzióit támogatja:

* Jira Core és szoftver: 6,0-7,12
* Jira ügyfélszolgálat: 3.0.0-tól 3.5.0-ig
* A JIRA támogatja az 5.2.-t is. További részletekért kattintson [a Microsoft Azure Active Directory egyszeri bejelentkezési címre a JIRA 5.2-es csomaghoz.](https://docs.microsoft.com/azure/active-directory/saas-apps/jira52microsoft-tutorial)
* Összefolyás: 5,0-5,10
* Összefolyás: 6.0.1
* Összefolyás: 6.1.1
* Összefolyás: 6.2.1
* Összefolyás: 6.3.4
* Összefolyás: 6.4.0
* Összefolyás: 6.5.0
* Összefolyás: 6.6.2
* Összefolyás: 6.7.0
* Összefolyás: 6.8.1
* Összefolyás: 6.9.0
* Összefolyás: 6.10.0
* Összefolyás: 6.11.0
* Összefolyás: 6.12.0

## <a name="installation"></a>Telepítés

A beépülő modul telepítéséhez hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be a Jira vagy összefolyása példány, mint egy admin.

2. Nyissa meg a Jira/Összefolyás felügyeleti **konzolt,** és válassza a Bővítmények lehetőséget.

3. A Microsoft letöltőközpontjából töltse le a [Microsoft SAML SSO plugint](https://www.microsoft.com/download/details.aspx?id=56506)/ a Jira[Microsoft SAML SSO Plugin for Confluence számára.](https://www.microsoft.com/download/details.aspx?id=56503)

   A beépülő modul megfelelő verziója megjelenik a keresési eredmények között.

4. Válassza ki a beépülő modult, és az Univerzális beépülő modul kezelője (UPM) telepíti azt.

A beépülő modul telepítése után megjelenik a **Bővítmények kezelése**című bővítmény csoport Felhasználó **által telepített** bővítmények szakaszában.

## <a name="plug-in-configuration"></a>Beépülő modul konfigurációja

A beépülő modul használatának megkezdése előtt konfigurálnia kell azt. Válassza ki a beépülő modult, válassza a **Konfigurálás** gombot, és adja meg a konfiguráció részleteit.

Az alábbi képen a konfigurációs képernyő látható jira és összefolyása:

![Beépülő modul konfigurációs képernyője](./media/ms-confluence-jira-plugin-adminguide/jira.png)

* **Metaadat URL-címe:** Az összevonási metaadatok azure AD-ből való lekérése url-címe.

* **Azonosítók:** Az URL-cím, amelyet az Azure AD a kérelem forrásának érvényesítéséhez használ. Leképezi az Azure AD **azonosító** elemét. A beépülő modul automatikusan ezt az URL-címet https://*\<tartomány:port>*/.

* **Válasz URL-címe:** Az identitásszolgáltatóban (IdP) található válasz URL-címe, amely elindítja az SAML-bejelentkezést. Leképezi a **válasz URL-elem** az Azure AD-ben. A beépülő modul automatikusan ezt az URL-címet https://*\<domain:port>*/plugins/servlet/saml/auth néven nyeri.

* **Bejelentkezési URL:Az**idP-ben lévő bejelentkezési URL-cím, amely elindítja az SAML-bejelentkezést. Leképezi a **Bejelentkezési** elem az Azure AD-ben. A beépülő modul automatikusan ezt az URL-címet https://*\<domain:port>*/plugins/servlet/saml/auth néven nyeri.

* **IdP-entitásazonosító:** az idp által használt entitásazonosító. Ez a mező a metaadat-URL feloldásakor kerül feltöltésre.

* **Bejelentkezési URL:** Az IdP bejelentkezési URL-címe. Ez a mező az Azure AD-ből kerül feltöltésre, amikor a metaadat-URL-cím fel van oldva.

* **Kijelentkezési URL:** Az IdP-ből származó kijelentkezési URL.Logout URL: The logout URL from your IdP. Ez a mező az Azure AD-ből kerül feltöltésre, amikor a metaadat-URL-cím fel van oldva.

* **X.509 tanúsítvány:** Az IdP X.509 tanúsítványa. Ez a mező az Azure AD-ből kerül feltöltésre, amikor a metaadat-URL-cím fel van oldva.

* **Bejelentkezési gomb neve:** Annak a bejelentkezési gombnak a neve, amelyet a szervezet a felhasználóknak látnia kell a bejelentkezési lapon.

* **SAML felhasználói azonosító helyek:** Az a hely, ahol a Jira vagy összefolyása felhasználói azonosító várható az SAML válaszban. Lehet **NameID** vagy egyéni attribútumnévben.

* **Attribútum neve**: Annak az attribútumnak a neve, ahol a felhasználói azonosító várható.

* **Home Realm Discovery**engedélyezése : A választás, ha a vállalat Active Directory összevonási szolgáltatások (AD FS) alapú bejelentkezést használ.

* **Tartománynév**: A tartománynév, ha a bejelentkezés AD FS alapú.

* **Egyszeri kijelentkezés engedélyezése:** A kiválasztás, ha ki szeretne jelentkezni az Azure AD-ből, amikor egy felhasználó kijelentkezik a Jira vagy a Confluence.

## <a name="troubleshooting"></a>Hibaelhárítás

* **Több tanúsítványhibát kap:** Jelentkezzen be az Azure AD-be, és távolítsa el az alkalmazáson belül elérhető több tanúsítványt. Győződjön meg arról, hogy csak egy tanúsítvány van jelen.

* **A tanúsítvány hamarosan lejár az Azure AD:** Bővítmények gondoskodnak a tanúsítvány automatikus előgörgetése. Amikor egy tanúsítvány hamarosan lejár, az új tanúsítványt aktívként kell megjelölni, és a nem használt tanúsítványokat törölni kell. Ebben az esetben a felhasználó megpróbál bejelentkezni a Jira szolgáltatásba, a beépülő modul lekéri és menti az új tanúsítványt.

* **Le szeretné tiltani a WebSudo alkalmazást (a biztonságos rendszergazdai munkamenetletiltása):**

  * A Jira esetében a biztonságos rendszergazdai munkamenetek (azaz a jelszó megerősítése a felügyeleti funkciók elérése előtt) alapértelmezés szerint engedélyezve vannak. Ha el szeretné távolítani ezt a képességet a Jira példányban, adja meg a következő sort a jira-config.properties fájlban:`ira.websudo.is.disabled = true`

  * Az összefolyás esetén kövesse az [Összefolyás támogatási webhelyének lépéseit.](https://confluence.atlassian.com/doc/configuring-secure-administrator-sessions-218269595.html)

* **A metaadat-URL-cím által feltöltendő mezők nem töltődnek fel:**

  * Ellenőrizze, hogy az URL helyes-e. Ellenőrizze, hogy a megfelelő bérlői és alkalmazásazonosítót képezte-e le.

  * Írja be az URL-címet a böngészőben, és nézze meg, hogy megkapja-e az összevonási metaadat-XML-t.

* **Belső kiszolgálóhiba történt:** Tekintse át a telepítés naplókönyvtárában lévő naplókat. Ha a hibaüzenet jelenik meg, amikor a felhasználó az Azure AD SSO használatával próbál bejelentkezni, megoszthatja a naplókat a támogatási csapattal.

* **A "Felhasználói azonosító nem található" hibaüzenet jelenik meg, amikor a felhasználó megpróbál bejelentkezni:** Hozza létre a felhasználói azonosítót a Jira vagy a Összefolyás ban.

* **Az Azure AD-ben "Az alkalmazás nem található" hibaüzenet**jelenik meg: Tekintse meg, hogy a megfelelő URL-cím le van-e képezve az alkalmazáshoz az Azure AD-ben.

* **Támogatásra van szüksége:** Az [Azure AD SSO-integrációs csapat](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)elérése. A csapat 24-48 óra múlva válaszol.

  Támogatási jegyet is létrehozhat a Microsofttal az Azure Portal csatornán keresztül.

## <a name="plug-in-faq"></a>Beépülő modul – gyakori kérdések

Kérjük, olvassa el az alábbi GYIK- et, ha bármilyen kérdése van ezzel a beépülő modulval kapcsolatban.

### <a name="what-does-the-plug-in-do"></a>Mit csinál a beépülő modul?

A plug-in egyszeri bejelentkezési (SSO) lehetőséget biztosít az Atlassian Jira (beleértve a Jira Core, Jira Software, Jira Service Desk) és a confluence helyszíni szoftverek számára. A beépülő modul az Azure Active Directory (Azure AD) identitásszolgáltatóként (IdP) működik.

### <a name="which-atlassian-products-does-the-plug-in-work-with"></a>Mely Atlassian termékekkel működik együtt a plug-in?

A beépülő modul a Jira és a Confluence helyszíni verzióival működik.

### <a name="does-the-plug-in-work-on-cloud-versions"></a>Működik a beépülő modul a felhőalapú verziókon?

Nem. A beépülő modul csak a Jira és a Confluence helyszíni verzióit támogatja.

### <a name="which-versions-of-jira-and-confluence-does-the-plug-in-support"></a>A Jira és a Confluence mely verzióit támogatja a plug-in?

A beépülő modul a következő verziókat támogatja:

* Jira Core és szoftver: 6,0-7,12
* Jira ügyfélszolgálat: 3.0.0-tól 3.5.0-ig
* A JIRA támogatja az 5.2.-t is. További részletekért kattintson [a Microsoft Azure Active Directory egyszeri bejelentkezési címre a JIRA 5.2-es csomaghoz.](https://docs.microsoft.com/azure/active-directory/saas-apps/jira52microsoft-tutorial)
* Összefolyás: 5,0-5,10
* Összefolyás: 6.0.1
* Összefolyás: 6.1.1
* Összefolyás: 6.2.1
* Összefolyás: 6.3.4
* Összefolyás: 6.4.0
* Összefolyás: 6.5.0
* Összefolyás: 6.6.2
* Összefolyás: 6.7.0
* Összefolyás: 6.8.1
* Összefolyás: 6.9.0
* Összefolyás: 6.10.0
* Összefolyás: 6.11.0
* Összefolyás: 6.12.0

### <a name="is-the-plug-in-free-or-paid"></a>A plug-in ingyenes vagy fizetős?

Ez egy ingyenes kiegészítő.

### <a name="do-i-need-to-restart-jira-or-confluence-after-i-deploy-the-plug-in"></a>Újra kell indítanom a Jira vagy az Összefolyás a beépülő modul telepítése után?

Nincs szükség újraindításra. Azonnal elkezdheti használni a beépülő modult.

### <a name="how-do-i-get-support-for-the-plug-in"></a>Hogyan kaphatok támogatást a beépülő modulhoz?

A beépülő modulhoz szükséges támogatásért forduljon az [Azure AD egyszeri bejelentkezés integrációs csapatához.](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) A csapat 24-48 óra múlva válaszol.

Támogatási jegyet is létrehozhat a Microsofttal az Azure Portal csatornán keresztül.

### <a name="would-the-plug-in-work-on-a-mac-or-ubuntu-installation-of-jira-and-confluence"></a>Vajon a plug-in munka egy Mac vagy Ubuntu telepítése Jira és összefolyása?

A beépülő modult csak a Jira és a Confluence 64 bites Windows Server telepítésein teszteltük.

### <a name="does-the-plug-in-work-with-idps-other-than-azure-ad"></a>A beépülő modul nem az Azure AD-n kívüli azonosítókkal működik?

Nem. Csak az Azure AD-vel működik.

### <a name="what-version-of-saml-does-the-plug-in-work-with"></a>Az SAML melyik verziójával működik a beépülő modul?

SamL 2.0-val működik.

### <a name="does-the-plug-in-do-user-provisioning"></a>A beépülő modul rendelkezik a felhasználói kiépítésben?

Nem. A beépülő modul csak SAML 2.0-alapú egyszeri bejelentkezést biztosít. A felhasználót ki kell építeni az alkalmazásban az egyszeri bejelentkezés előtt.

### <a name="does-the-plug-in-support-cluster-versions-of-jira-and-confluence"></a>A beépülő modul támogatja a Jira és a Confluence fürtverzióit?

Nem. A beépülő modul a Jira és a Confluence helyszíni verzióival működik.

### <a name="does-the-plug-in-work-with-http-versions-of-jira-and-confluence"></a>Működik a beépülő modul a Jira és a Confluence HTTP-verzióival?

Nem. A beépülő modul csak HTTPS-kompatibilis telepítésekkel működik.

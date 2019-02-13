---
title: Jira való Atlassian/összefolyás felett rendszergazdai útmutatója – Azure Active Directory |} A Microsoft Docs
description: Rendszergazdai útmutató az Azure Active Directory (Azure AD) Atlassian Jira és való összefolyás felett használandó...
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b81129ada4710eff58d50bf998802e8ac1b2788
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56191061"
---
# <a name="atlassian-jira-and-confluence-admin-guide-for-azure-active-directory"></a>Az Azure Active Directory Atlassian Jira és való összefolyás felett rendszergazdai útmutatója

## <a name="overview"></a>Áttekintés

Az Azure Active Directory (Azure AD) egyszeri bejelentkezés (SSO) beépülő modul használatát teszi lehetővé a Microsoft Azure AD Atlassian Jira és való összefolyás felett Server-alapú termékeknél való bejelentkezéshez használja a munkahelyi vagy iskolai fiókjával. SAML 2.0-alapú egyszeri bejelentkezés valósítja meg.

## <a name="how-it-works"></a>Működés

Amikor a felhasználók szeretne bejelentkezni az Atlassian Jira vagy való összefolyás felett alkalmazásba, akkor látni fogja a **az Azure AD bejelentkezési** gomb a bejelentkezési oldalon. Amikor kiválasztják, hogy kell jelentkezzen be az Azure AD szervezeti bejelentkezési oldal (vagyis munkahelyi vagy iskolai fiókjuk) használatával.

A felhasználók hitelesítése után kell tudni bejelentkezni az alkalmazásba. Már hitelesített azonosítója és a munkahelyi vagy iskolai fiók jelszavát, majd közvetlenül bejelentkeznek az alkalmazásba. 

Jelentkezzen be egyaránt működik Jira és való összefolyás felett. Ha a Jira alkalmazáshoz be van jelentkezve a felhasználói és való összefolyás felett böngészőablakban nyílik meg, nincs szükség a hitelesítő adatok megadása céljából a többi alkalmazás. 

Felhasználók a munkahelyi vagy iskolai fiók Atlassian termék saját alkalmazásokon keresztül is elérhető. Akkor kell jelentkeznie a anélkül, hogy a rendszer a hitelesítő adatokat.

> [!NOTE]
> Felhasználók átadása nem történik a beépülő modul használatával.

## <a name="audience"></a>Célközönség

Jira és való összefolyás felett is segítségével a rendszergazdák a beépülő modul az Azure AD egyszeri bejelentkezés engedélyezése.

## <a name="assumptions"></a>Előfeltételek

* Jira és való összefolyás felett példányai HTTPS-kompatibilis.
* Felhasználók már létrehozása Jira vagy való összefolyás felett.
* Felhasználók Jira vagy való összefolyás felett hozzárendelt szerepkörök rendelkeznek.
* Rendszergazdák hozzáférhetnek a beépülő modul konfigurálásához szükséges információkat.
* Jira vagy való összefolyás felett érhető el, valamint a vállalati hálózaton kívülről.
* A beépülő modul működését csak a helyszíni verzióval, Jira és való összefolyás felett.

## <a name="prerequisites"></a>Előfeltételek

A beépülő modul telepítése előtt, vegye figyelembe a következő információkat:

* Jira és való összefolyás felett a Windows 64 bites verziója van telepítve.
* Jira és való összefolyás felett edmversion HTTPS-kompatibilis.
* Jira és való összefolyás felett érhetők el az interneten.
* Rendszergazdai hitelesítő adatok vannak érvényben a Jira és való összefolyás felett.
* Rendszergazdai hitelesítő adatok az Azure ad vannak érvényben.
* WebSudo le van tiltva, a Jira és való összefolyás felett.

## <a name="supported-versions-of-jira-and-confluence"></a>Jira és való összefolyás felett támogatott verziói

A beépülő modul Jira és való összefolyás felett alábbi verzióit támogatja:

* Jira Core és a szoftvereket: a 7.12 6.0
* Jira Service Desk: 3.0.0-s való 3.5.0
* JIRA 5.2 is támogatja. További részletekért kattintson [Microsoft Azure Active Directory egyszeri bejelentkezés a JIRA 5.2.](https://docs.microsoft.com/azure/active-directory/saas-apps/jira52microsoft-tutorial)
* Való összefolyás felett: 5.0-s az 5.10.
* Való összefolyás felett: 6.0.1
* Való összefolyás felett: 6.1.1
* Való összefolyás felett: 6.2.1
* Való összefolyás felett: 6.3.4
* Való összefolyás felett: 6.4.0
* Való összefolyás felett: 6.5.0
* Való összefolyás felett: 6.6.2
* Való összefolyás felett: 6.7.0
* Való összefolyás felett: 6.8.1
* Való összefolyás felett: 6.9.0
* Való összefolyás felett: 6.10.0
* Való összefolyás felett: 6.11.0
* Való összefolyás felett: 6.12.0

## <a name="installation"></a>Telepítés

A beépülő modul telepítéséhez kövesse az alábbi lépéseket:

1. A Jira vagy való összefolyás felett példány jelentkezzen be rendszergazdaként.

2. A Jira/való összefolyás felett felügyeleti konzolon, és válassza ki **bővítmények**.

3. A Microsoft Download Center, töltse le a [Microsoft SAML SSO beépülő modulja a Jira](https://www.microsoft.com/download/details.aspx?id=56506)/ [Microsoft SAML SSO beépülő modulja való összefolyás felett](https://www.microsoft.com/download/details.aspx?id=56503).

   A beépülő modul a megfelelő verzióját a keresési eredmények között megjelenik.

4. Válassza ki a beépülő modult, és az univerzális Plug-in Manager (UPM) telepíti azt.

A beépülő modul telepítése után megjelenik a **felhasználói telepített bővítmények** szakaszában **bővítmények kezelése**.

## <a name="plug-in-configuration"></a>Beépülő modul konfigurálása

A beépülő modul használata előtt konfigurálnia kell azt. Válassza ki a beépülő modult, válassza a **konfigurálása** gombra, és adja meg a konfigurációs adatokat.

Az alábbi képen látható Jira és való összefolyás felett is a következő konfigurációs képernyőjén:

![Beépülő modul konfigurációs képernyőjén](./media/ms-confluence-jira-plugin-adminguide/jira.png)

*   **Metaadatok URL-címe**: Az Azure AD összevonási metaadatok beolvasása az URL-címe.

*   **Azonosítók**: Az URL-címet használó Azure ad-ben a forrás, a kérelem ellenőrzése. Vannak leképezve a **azonosító** elem az Azure ad-ben. A beépülő modul automatikusan osztályból a https:// URL-cím *< tartomány: port >*/.

*   **Válasz URL-cím**: Az identitásszolgáltató (IdP) kezdeményező SAML bejelentkezési válasz URL-cím. Vannak leképezve a **válasz URL-cím** elem az Azure ad-ben. A beépülő modul automatikusan osztályból a https:// URL-cím *< tartomány: port >*/plugins/servlet/saml/auth.

*   **Bejelentkezési URL-cím**: A bejelentkezési URL-címet az identitásszolgáltató SAML bejelentkezési kezdeményező. Vannak leképezve a **bejelentkezés** elem az Azure ad-ben. A beépülő modul automatikusan osztályból a https:// URL-cím *< tartomány: port >*/plugins/servlet/saml/auth.

*   **Identitásszolgáltató Entitásazonosító**: Az entitás azonosítója, amely az identitásszolgáltató használja. Ez a mező fel van töltve, ha megoldották a metaadatok URL-címe.

*   **Bejelentkezési URL-cím**: A bejelentkezési URL-cím az identitásszolgáltató. Ez a mező fel van töltve, ha megoldották a metaadatok URL-címe az Azure AD-ből.

*   **Kijelentkezési URL-címe**: A kijelentkezési URL-címe az identitásszolgáltató. Ez a mező fel van töltve, ha megoldották a metaadatok URL-címe az Azure AD-ből.

*   **X.509-tanúsítvány**: Az IdP X.509-tanúsítvány. Ez a mező fel van töltve, ha megoldották a metaadatok URL-címe az Azure AD-ből.

*   **Bejelentkezési gomb neve**: A bejelentkezés gombot, amely a szervezet azt szeretné, a bejelentkezési lapon látható, hogy a felhasználók neve.

*   **SAML-felhasználói azonosító helyek**: A hely, ahol a Jira vagy való összefolyás felett felhasználói azonosítónak a SAML-válasz. Is lehet **NameID** vagy egy egyéni attribútum nevében.

*   **Attribútum neve**: A nevét, ahol a felhasználói azonosítónak.

*   **A Kezdőtartomány felderítésének engedélyezése kezdőlap**: A kijelölést, győződjön meg arról, ha a vállalat használ az Active Directory összevonási szolgáltatások (AD FS) - alapú bejelentkezést - a.

*   **Tartománynév**: A tartománynév, ha be-e az AD FS-alapú.

*   **Engedélyezze az egyszeri Kijelentkezés**: Győződjön meg arról, ha azt szeretné, jelentkezzen ki, amikor egy felhasználó kijelentkezik a Jira vagy való összefolyás felett az Azure AD-ből a kijelölést.

## <a name="troubleshooting"></a>Hibaelhárítás

* **Több tanúsítvány hibákat kap**: Jelentkezzen be Azure ad-ben, és távolítsa el a több tanúsítványt, az alkalmazás elleni elérhető. Győződjön meg arról, hogy csak egy tanúsítványra jelen.

* **A tanúsítvány érvényessége hamarosan lejár az Azure ad-ben**: A bővítmények a tanúsítvány automatikus helyettesítő gondoskodik. Ha egy tanúsítvány hamarosan lejár, új tanúsítványt kell megjelölni aktív, és fel nem használt tanúsítványok azt törölni kell. Amikor egy felhasználó megpróbál bejelentkezni a Jira ebben a forgatókönyvben a beépülő modul fetches, és menti az új tanúsítványt.

* **Le kívánja tiltani (tiltsa le a biztonságos felügyeleti munkamenet) WebSudo**:

  * A Jira biztosíthat a rendszergazda alapértelmezés szerint engedélyezve vannak a munkamenetek (vagyis a jelszó megerősítése felügyeleti funkciók elérése előtt). Ha el kívánja távolítani ezt a lehetőséget a Jira-példányban, adja meg a következő sort a jira-config.properties fájlt: `ira.websudo.is.disabled = true`

  * A való összefolyás felett, kövesse a lépéseket a [való összefolyás felett támogatási oldalunkra](https://confluence.atlassian.com/doc/configuring-secure-administrator-sessions-218269595.html).

* **Feltölthető a metaadatok URL-CÍMÉT kell menteniük mezők nem kerülnek első**:

  * Ellenőrizze, ha az URL-cím helyes-e. Ellenőrizze, hogy ha képezett le a megfelelő bérlői és az alkalmazás azonosítóját.

  * Adja meg az URL-címet egy böngészőben, és tekintse meg a kap, ha az összevonási metaadatok XML.

* **Belső hiba történt**: Tekintse át a naplókat a telepítési naplófájl könyvtárában. Amikor a felhasználó próbál jelentkezzen be az Azure AD SSO használatával a hibát kap, ha a naplók megoszthatja a csapatával.

* **Van egy "Felhasználói azonosító nem található" hiba történt, amikor a felhasználó megpróbál bejelentkezni**: A felhasználói azonosító létrehozása Jira vagy való összefolyás felett.

* **"Az alkalmazás nem található" hiba van az Azure ad-ben**: Tekintse meg, ha a megfelelő URL-cím van rendelve az alkalmazás Azure AD-ben.

* **Támogatásra van szüksége**: Keresse fel a [Azure AD SSO integrációs csapat](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). A csapat változásokhoz üzleti 24-48 órában.

  Az Azure portal csatornán keresztül a Microsoft támogatási jegyet is tehet.

## <a name="plug-in-faq"></a>Beépülő modul – gyakori kérdések

Olvassa el alább – gyakori kérdések, ha minden lekérdezés felvegye beépülő modult.

### <a name="what-does-the-plug-in-do"></a>A beépülő modul ne funkciója?

A beépülő modul egyszeri bejelentkezés (SSO) képességet nyújt Atlassian Jira (beleértve a Jira-Core, a Jira Software, a Jira ügyfélszolgálat) és a telephelyi szoftverek való összefolyás felett. A beépülő modul működését az Azure Active Directoryval (Azure AD) Identitásszolgáltatóként (IdP).

### <a name="which-atlassian-products-does-the-plug-in-work-with"></a>Mely Atlassian termékek a beépülő modul működik az?

A beépülő modul működését Jira és való összefolyás felett helyszíni verzióival.

### <a name="does-the-plug-in-work-on-cloud-versions"></a>A beépülő modul működik a felhő-verziókról?

Nem. A beépülő modul által támogatott csak a helyszíni verzióit Jira és való összefolyás felett.

### <a name="which-versions-of-jira-and-confluence-does-the-plug-in-support"></a>Jira és való összefolyás felett melyik verziója támogatja a beépülő modul?

A beépülő modul alábbi verzióit támogatja:

* Jira Core és a szoftvereket: a 7.12 6.0
* Jira Service Desk: 3.0.0-s való 3.5.0
* JIRA 5.2 is támogatja. További részletekért kattintson [Microsoft Azure Active Directory egyszeri bejelentkezés a JIRA 5.2.](https://docs.microsoft.com/azure/active-directory/saas-apps/jira52microsoft-tutorial)
* Való összefolyás felett: 5.0-s az 5.10.
* Való összefolyás felett: 6.0.1
* Való összefolyás felett: 6.1.1
* Való összefolyás felett: 6.2.1
* Való összefolyás felett: 6.3.4
* Való összefolyás felett: 6.4.0
* Való összefolyás felett: 6.5.0
* Való összefolyás felett: 6.6.2
* Való összefolyás felett: 6.7.0
* Való összefolyás felett: 6.8.1
* Való összefolyás felett: 6.9.0
* Való összefolyás felett: 6.10.0
* Való összefolyás felett: 6.11.0
* Való összefolyás felett: 6.12.0

### <a name="is-the-plug-in-free-or-paid"></a>A beépülő modul ingyenes vagy fizetős?

Egy ingyenes bővítményről.

### <a name="do-i-need-to-restart-jira-or-confluence-after-i-deploy-the-plug-in"></a>Szükséges indítsa újra a Jira vagy való összefolyás felett után üzembe helyezhetem a beépülő modul?

A számítógép újraindítását, nem szükséges. Meg azonnal a beépülő modul használatának megkezdéséhez.

### <a name="how-do-i-get-support-for-the-plug-in"></a>Hogyan kérhet támogatást a beépülő modul?

Elérheti hogy a [Azure AD SSO integrációs csapat](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) támogatás számára szükséges a beépülő modul. A csapat változásokhoz üzleti 24-48 órában.

Az Azure portal csatornán keresztül a Microsoft támogatási jegyet is tehet.

### <a name="would-the-plug-in-work-on-a-mac-or-ubuntu-installation-of-jira-and-confluence"></a>A Mac- vagy Ubuntu-telepítésen Jira és való összefolyás felett beépülő modul munka volna?

Általunk tesztelt, a beépülő modul csak a 64 bites Windows Server-példányok Jira és való összefolyás felett.

### <a name="does-the-plug-in-work-with-idps-other-than-azure-ad"></a>A beépülő modul működik az Azure AD-től eltérő identitásszolgáltató használatát?

Nem. Csak az Azure ad-vel működik.

### <a name="what-version-of-saml-does-the-plug-in-work-with"></a>Milyen SAML-verziót a beépülő modul működik az?

Az SAML 2.0-val működik.

### <a name="does-the-plug-in-do-user-provisioning"></a>A beépülő modul feladata felhasználókiépítése?

Nem. A beépülő modul csak SAML 2.0-alapú egyszeri Bejelentkezést biztosít. A felhasználók számára az alkalmazás Egyszeri bejelentkezési előtt ki kell építeni.

### <a name="does-the-plug-in-support-cluster-versions-of-jira-and-confluence"></a>A beépülő modul támogatása fürtverziók Jira és való összefolyás felett nem?

Nem. A beépülő modul működését Jira és való összefolyás felett helyszíni verzióival.

### <a name="does-the-plug-in-work-with-http-versions-of-jira-and-confluence"></a>A beépülő modul működik az Jira és való összefolyás felett HTTP-verziók?

Nem. A beépülő modul csak a HTTPS használatára konfigurált rendszerek együttműködik.

---
title: Problémák az alkalmazásba való bejelentkezéskor a hozzáférési panelről | Microsoft dokumentumok
description: Alkalmazás elérésével kapcsolatos problémák elhárítása a Microsoft Azure AD hozzáférési panelről myapps.microsoft.com
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b93ee38666b93253c7cda6c756d4f58daaea236
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74082162"
---
# <a name="problems-signing-in-to-an-application-from-the-access-panel"></a>Problémák az alkalmazásba való bejelentkezéskor a hozzáférési panelről

A Hozzáférési panel egy webalapú portál, amely lehetővé teszi, hogy az Azure Active Directoryban (Azure AD) munkahelyi vagy iskolai fiókkal rendelkező felhasználó megtekintse és elindítsa azokat a felhőalapú alkalmazásokat, amelyekhez az Azure AD-rendszergazda hozzáférést biztosított számukra. 

Ezek az alkalmazások a felhasználó nevében vannak konfigurálva az Azure AD portálon. Az alkalmazást megfelelően kell konfigurálni, és hozzá kell rendelni ahhoz a felhasználóhoz vagy csoporthoz, amelynek a felhasználó tagja ahhoz, hogy az alkalmazást a Hozzáférési panelen láthassa.

A felhasználó által látott alkalmazások típusa a következő kategóriákba sorolható:

-   Office 365-alkalmazások

-   Összevonásalapú egyszeri felvonással konfigurált Microsoft- és külső alkalmazások

-   Jelszóalapú egyszeri bejelentkezésalkalmazások

-   Meglévő SSO-megoldásokkal rendelkező alkalmazások

## <a name="general-issues-to-check-first"></a>Általános kérdések, amelyeket először ellenőrizni kell

-   Győződjön meg arról, hogy olyan **böngészőt** használ, amely megfelel a Hozzáférési panel minimális követelményeinek.

-   Győződjön meg arról, hogy a felhasználó böngészője hozzáadta az alkalmazás URL-címét a **megbízható helyekhez.**

-   Ellenőrizze, hogy az alkalmazás megfelelően **van-e konfigurálva.**

-   Győződjön meg arról, hogy a felhasználó fiókja **engedélyezve** van a bejelentkezéshez.

-   Győződjön meg arról, hogy a felhasználó fiókja **nincs zárolva.**

-   Győződjön meg arról, hogy a felhasználó **jelszava nem járt le vagy nem feledje el.**

-   Győződjön meg arról, hogy a **többtényezős hitelesítés** nem blokkolja a felhasználói hozzáférést.

-   Győződjön meg arról, hogy a **feltételes hozzáférési szabályzat** vagy az **identitásvédelmi** házirend nem blokkolja a felhasználói hozzáférést.

-   Győződjön meg arról, hogy a felhasználó **hitelesítési kapcsolattartási adatai** naprakészek a többtényezős hitelesítési vagy feltételes hozzáférési házirendek érvényesítéséhez.

-   Győződjön meg róla, hogy megpróbálja törölni a böngésző cookie-kat, és megpróbál újra bejelentkezni.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>A Hozzáférési panel böngészőkövetelményeinek teljesítése

A hozzáférési panelhez olyan böngészőre van szükség, amely támogatja a JavaScriptet, és engedélyezve van a CSS. A hozzáférési panelen a jelszóalapú egyszeri bejelentkezés (SSO) használatához a hozzáférési panel bővítményt telepíteni kell a felhasználó böngészőjében. Ez a bővítmény automatikusan letöltődik, amikor a felhasználó olyan alkalmazást választ, amely jelszóalapú egyszeri bejelentkezésre van konfigurálva.

A jelszóalapú egyszeri bejelentkezés esetén a végfelhasználó böngészői a következők lehetnek:

-   Internet Explorer 8, 9, 10, 11 – Windows 7 vagy újabb rendszeren

-   Microsoft Edge windows 10-es évfordulós kiadásban vagy újabb verzióban

-   Chrome – Windows 7 vagy újabb rendszeren, valamint MacOS X-en vagy újabb rendszeren

-   Firefox 26.0 vagy újabb verzió – Windows XP SP2 vagy újabb, valamint Mac OS X 10.6-os vagy újabb rendszeren

## <a name="how-to-install-the-access-panel-browser-extension"></a>Az Access Panel böngészőbővítményének telepítése

Az Access Panel browser bővítményének telepítéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg a [hozzáférési panelt](https://myapps.microsoft.com) az egyik támogatott böngészőben, és jelentkezzen be **felhasználóként** az Azure AD-ben.

2.  Kattintson egy **jelszó-Egyszeri bejelentkezés alkalmazásra** a hozzáférési panelen.

3.  A szoftver telepítését kérő üzenetben válassza a **Telepítés most**lehetőséget.

4.  A böngésző alapján a letöltési linkre irányítjuk. **Adja hozzá** a bővítményt a böngészőhöz.

5.  Ha a böngésző kérdezi, válassza a **bővítmény engedélyezése** vagy **engedélyezése** lehetőséget.

6.  A telepítés után **indítsa újra** a böngészőmunkamenetet.

7.  Jelentkezzen be a hozzáférési panelen, és nézze meg, hogy **el tudja-e indítani** a jelszó-Egyszeri bejelentkezés alkalmazásokat

A Chrome és a Microsoft Edge bővítményét az alábbi közvetlen linkekről is letöltheti:

-   [Chrome-hozzáférési panel bővítmény](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Microsoft Edge Access Panel bővítmény](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84)

## <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Az összevont egyszeri bejelentkezés konfigurálása egy Azure AD-katalógus-alkalmazáshoz

Az Azure AD-gyűjteményben engedélyezett összes alkalmazás a vállalati egyszeri bejelentkezés funkcióval rendelkezik egy lépésről-lépésre elérhető oktatóanyaggal. Részletes útmutatást nyújt vagnált részletes útmutatásért az Azure Active Directoryval való [SaaS-alkalmazások integrálásáról szóló oktatóanyagok listája.](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)

Egy alkalmazás konfigurálásához az Azure AD-gyűjteményben meg kell:

-   Alkalmazás hozzáadása az Azure AD-gyűjteményből

-   [Konfigurálja az alkalmazás metaadat-értékeit az Azure AD-ben (Bejelentkezés URL-cím, azonosító, válasz URL)Configure the application's metadata values in Azure AD (Sign on URL, Identifier, Reply URL)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Válassza ki a felhasználói azonosítót, és adja hozzá az alkalmazásnak küldendő felhasználói attribútumokat](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Az Azure AD metaadatainak és tanúsítványának lekérése](#download-the-azure-ad-metadata-or-certificate)

-   [Azure AD metaadat-értékek konfigurálása az alkalmazásban (Bejelentkezés URL-cím, kiállító, kijelentkezési URL és tanúsítvány)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   Felhasználók hozzárendelése az alkalmazáshoz

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Alkalmazás hozzáadása az Azure AD-gyűjteményből

Ha alkalmazást szeretne hozzáadni az Azure AD-galériából, kövesse az alábbi lépéseket:

1.  Nyissa meg az [Azure-portált,](https://portal.azure.com) és jelentkezzen be **globális rendszergazdaként** vagy **társrendszergazdaként**

2.  Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3.  Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4.  kattintson az Azure Active Directory bal oldali navigációs **menüjében** az Enterprise Applications parancsra.

5.  kattintson a Vállalati **alkalmazások** ablaktábla jobb felső sarkában található **Hozzáadás** gombra.

6.  A **Enter a name** Katalógus **hozzáadás a szakaszban** írja be az alkalmazás nevét.

7.  Válassza ki azt az alkalmazást, amelyet egyszeri bejelentkezéshez szeretne konfigurálni.

8.  Az alkalmazás hozzáadása előtt a **Név** mezőben módosíthatja a nevét.

9.  Az alkalmazás hozzáadásához kattintson a **Hozzáadás** gombra.

Rövid idő elteltével láthatja az alkalmazás konfigurációs ablaktábláját.

### <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Egyszeri bejelentkezés konfigurálása egy alkalmazáshoz az Azure AD-gyűjteményből

Az alkalmazás egyszeri bejelentkezésének konfigurálásához kövesse az alábbi lépéseket:

1. <span id="_Hlk477187909" class="anchor"><span id="_Hlk477001983" class="anchor"></span></span>Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként** vagy **társrendszergazdaként.**

2. Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3. Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4. kattintson az Azure Active Directory bal oldali navigációs **menüjében** az Enterprise Applications parancsra.

5. Kattintson **a Minden alkalmazás** elemre az összes alkalmazás listájának megtekintéséhez.

   * Ha nem látja a kívánt alkalmazást, használja a **Szűrő** vezérlőt a **Minden alkalmazás lista** tetején, és állítsa a **Megjelenítés** beállítást **minden alkalmazásra.**

6. Jelölje ki az egyszeri bejelentkezést konfigurálni kívánt alkalmazást.

7. Miután az alkalmazás betöltődik, kattintson az **egyszeri bejelentkezésre** az alkalmazás bal oldali navigációs menüjéből.

8. Válassza **az SAML-alapú bejelentkezés** lehetőséget a **Mód** legördülő menüből.

9. Adja meg a szükséges értékeket **a Tartomány és az URL-címek között.** Ezeket az értékeket az alkalmazás szállítójától kell beszereznie.

   1. Az alkalmazás SP által kezdeményezett egyszeri bejelentkezésként való konfigurálásához a bejelentkezési URL-cím kötelező érték. Egyes alkalmazások esetében az azonosító is kötelező érték.

   2. Az alkalmazás IdP által kezdeményezett egyszeri szolgáltatóként történő konfigurálásához a válasz URL-címe kötelező érték. Egyes alkalmazások esetében az azonosító is kötelező érték.

10. **Nem kötelező:** kattintson **a Speciális URL-beállítások megjelenítése** gombra, ha meg szeretné tekinteni a nem kötelező értékeket.

11. A **Felhasználói attribútumok között**válassza ki a felhasználók egyedi azonosítóját a **Felhasználói azonosító** legördülő menüben.

12. **Nem kötelező:** kattintson **az Összes többi felhasználói attribútum megtekintése és szerkesztése** parancsra az SAML-jogkivonatban az alkalmazásnak küldendő attribútumok szerkesztéséhez, amikor a felhasználók bejelentkeznek.

    Attribútum hozzáadása:

    1. kattintson **az Attribútum hozzáadása gombra.** Írja be a **Nevet,** és válassza ki az **Értéket** a legördülő menüből.

    2. Kattintson a **Mentés gombra.** Megjelenik az új attribútum a táblán.

13. Kattintson **az&gt; Alkalmazásnév &lt;konfigurálása** elemre az egyszeri bejelentkezés konfigurálásához szükséges dokumentáció eléréséhez. Emellett rendelkezik az sso alkalmazással való beállításához szükséges metaadat-URL-címekkel és tanúsítvánnyal.

14. A konfiguráció mentéséhez kattintson a **Mentés** gombra.

15. Felhasználók hozzárendelése az alkalmazáshoz.

### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Válassza ki a felhasználói azonosítót, és adja hozzá az alkalmazásnak küldendő felhasználói attribútumokat

A felhasználói azonosító kiválasztásához vagy felhasználói attribútumok hozzáadásához kövesse az alábbi lépéseket:

1. Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként** vagy **társrendszergazdaként.**

2. Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3. Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4. kattintson az Azure Active Directory bal oldali navigációs **menüjében** az Enterprise Applications parancsra.

5. Kattintson **a Minden alkalmazás** elemre az összes alkalmazás listájának megtekintéséhez.

   * Ha nem látja az itt megjeleníteni kívánt alkalmazást, használja a **Szűrő** vezérlőt a **Minden alkalmazás lista** tetején, és állítsa a **Megjelenítés** beállítást **minden alkalmazásra.**

6. Válassza ki az egyszeri bejelentkezést konfigurált alkalmazást.

7. Miután az alkalmazás betöltődik, kattintson az **egyszeri bejelentkezésre** az alkalmazás bal oldali navigációs menüjéből.

8. A **Felhasználói attribútumok** csoportban válassza ki a felhasználók egyedi azonosítóját a **Felhasználói azonosító** legördülő menüben. A kiválasztott beállításnak meg kell egyeznie az alkalmazás várt értékével a felhasználó hitelesítéséhez.

   >[!NOTE]
   >Az Azure AD válassza ki a NameID attribútum (felhasználói azonosító) formátumát a kiválasztott érték vagy az alkalmazás által az SAML AuthRequest-ben kért formátum alapján. További információkért látogasson el a cikk [Egyszeri bejelentkezés SAML protokoll](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) szakaszban NameIDPolicy.
   >
   >

9. Felhasználói attribútumok hozzáadásához kattintson **az Összes többi felhasználói attribútum megtekintése és szerkesztése** gombra az alkalmazásnak az SAML-jogkivonatban elküldendő attribútumok szerkesztéséhez, amikor a felhasználók bejelentkeznek.

   Attribútum hozzáadása:

   1. kattintson **az Attribútum hozzáadása gombra.** Írja be a **Nevet,** és válassza ki az **Értéket** a legördülő menüből.

   2. Kattintson a **Mentés gombra.** Megjelenik az új attribútum a táblán.

### <a name="download-the-azure-ad-metadata-or-certificate"></a>Az Azure AD metaadatainak vagy tanúsítványának letöltése

Az alkalmazás metaadatainak vagy tanúsítványának az Azure AD-ből való letöltéséhez kövesse az alábbi lépéseket:

1. Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként** vagy **társrendszergazdaként.**

2. Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3. Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4. kattintson az Azure Active Directory bal oldali navigációs **menüjében** az Enterprise Applications parancsra.

5. Kattintson **a Minden alkalmazás** elemre az összes alkalmazás listájának megtekintéséhez.

   * Ha nem látja a kívánt alkalmazást, használja a **Szűrő** vezérlőt a **Minden alkalmazás lista** tetején, és állítsa a **Megjelenítés** beállítást **minden alkalmazásra.**

6. Válassza ki az egyszeri bejelentkezést konfigurált alkalmazást.

7. Miután az alkalmazás betöltődik, kattintson az **egyszeri bejelentkezésre** az alkalmazás bal oldali navigációs menüjéből.

8. Nyissa meg az **SAML aláíró tanúsítvány szakaszt,** majd kattintson **az Oszlopérték letöltése** parancsra. Attól függően, hogy az alkalmazás nak mi szükséges egyszeri bejelentkezés konfigurálásához, megjelenik a metaadat-XML vagy a tanúsítvány letöltésének lehetősége.

   Az Azure AD nem ad meg URL-címet a metaadatok leéséhez. A metaadatok csak XML-fájlként olvashatók be.

## <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Nem katalógusalkalmazások összevont egyszeri bejelentkezésének konfigurálása

Egy nem katalógusalkalmazás konfigurálásához azure AD prémium szintű azure-prémiummal kell rendelkeznie, és az alkalmazás támogatja az SAML 2.0-t. Az Azure AD-verziókról az [Azure AD díjszabása](https://azure.microsoft.com/pricing/details/active-directory/)című webhelyen talál további információt.

-   Konfigurálja az alkalmazás metaadat-értékeit az Azure AD-ben (Bejelentkezés URL-cím, azonosító, válasz URL)Configure the application's metadata values in Azure AD (Sign on URL, Identifier, Reply URL)

-   [Válassza ki a felhasználói azonosítót, és adja hozzá az alkalmazásnak küldendő felhasználói attribútumokat](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Az Azure AD metaadatainak és tanúsítványának lekérése](#download-the-azure-ad-metadata-or-certificate)

-   Azure AD metaadat-értékek konfigurálása az alkalmazásban (Bejelentkezés URL-cím, kiállító, kijelentkezési URL és tanúsítvány)

### <a name="configure-the-applications-metadata-values-in-azure-ad-sign-on-url-identifier-reply-url"></a>Konfigurálja az alkalmazás metaadat-értékeit az Azure AD-ben (Bejelentkezés URL-cím, azonosító, válasz URL)Configure the application's metadata values in Azure AD (Sign on URL, Identifier, Reply URL)

Ha nem az Azure AD-gyűjteményben található alkalmazások egyszeri bejelentkezését szeretné konfigurálni, kövesse az alábbi lépéseket:

1. Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként** vagy **társrendszergazdaként.**

2. Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3. Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4. kattintson az Azure Active Directory bal oldali navigációs **menüjében** az Enterprise Applications parancsra.

5. kattintson a Vállalati **alkalmazások** ablaktábla jobb felső sarkában található **Hozzáadás** gombra.

6. Kattintson a **Nem galéria alkalmazás** elemre a Saját alkalmazás **hozzáadása** szakaszban.

7. Írja be az alkalmazás nevét a **Név** mezőbe.

8. Az alkalmazás hozzáadásához kattintson a **Hozzáadás** gombra.

9. Miután az alkalmazás betöltődik, kattintson az **egyszeri bejelentkezésre** az alkalmazás bal oldali navigációs menüjéből.

10. **Válassza az SAML-alapú bejelentkezés** lehetőséget a Mód legördülő **menüben**

11. Adja meg a szükséges értékeket **a Tartomány és az URL-címek között.** Ezeket az értékeket az alkalmazás szállítójától kell beszereznie.

    1. Ha az alkalmazást IdP által kezdeményezett egyszeri szolgáltatóként szeretné konfigurálni, írja be a Válasz URL-címét és az azonosítót.

    2. **Nem kötelező:** Az alkalmazás SP által kezdeményezett egyszeri bejelentkezésként való konfigurálásához a bejelentkezési URL-cím kötelező érték.

12. A **Felhasználói attribútumok között**válassza ki a felhasználók egyedi azonosítóját a **Felhasználói azonosító** legördülő menüben.

13. **Nem kötelező:** kattintson **az Összes többi felhasználói attribútum megtekintése és szerkesztése** parancsra az SAML-jogkivonatban az alkalmazásnak küldendő attribútumok szerkesztéséhez, amikor a felhasználók bejelentkeznek.

    Attribútum hozzáadása:

    1. kattintson **az Attribútum hozzáadása gombra.** Írja be a **Nevet,** és válassza ki az **Értéket** a legördülő menüből.

    2. Kattintson a **Mentés gombra.** Megjelenik az új attribútum a táblán.

14. Kattintson **az&gt; Alkalmazásnév &lt;konfigurálása** elemre az egyszeri bejelentkezés konfigurálásához szükséges dokumentáció eléréséhez. Emellett rendelkezik Az Azure AD URL-címek és tanúsítvány szükséges az alkalmazáshoz.

### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Válassza ki a felhasználói azonosítót, és adja hozzá az alkalmazásnak küldendő felhasználói attribútumokat

A felhasználói azonosító kiválasztásához vagy felhasználói attribútumok hozzáadásához kövesse az alábbi lépéseket:

1. Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként** vagy **társrendszergazdaként.**

2. Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3. Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4. kattintson az Azure Active Directory bal oldali navigációs **menüjében** az Enterprise Applications parancsra.

5. Kattintson **a Minden alkalmazás** elemre az összes alkalmazás listájának megtekintéséhez.

   * Ha nem látja a kívánt alkalmazást, használja a **Szűrő** vezérlőt a **Minden alkalmazás lista** tetején, és állítsa a **Megjelenítés** beállítást **minden alkalmazásra.**

6. Válassza ki az egyszeri bejelentkezést konfigurált alkalmazást.

7. Miután az alkalmazás betöltődik, kattintson az **egyszeri bejelentkezésre** az alkalmazás bal oldali navigációs menüjéből.

8. A **Felhasználói attribútumok** csoportban válassza ki a felhasználók egyedi azonosítóját a **Felhasználói azonosító** legördülő menüben. A kiválasztott beállításnak meg kell egyeznie az alkalmazás várt értékével a felhasználó hitelesítéséhez.

   >[!NOTE]
   >Az Azure AD válassza ki a NameID attribútum (felhasználói azonosító) formátumát a kiválasztott érték vagy az alkalmazás által az SAML AuthRequest-ben kért formátum alapján. További információkért látogasson el a cikk [Egyszeri bejelentkezés SAML protokoll](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) szakaszban NameIDPolicy.
   >
   >

9. Felhasználói attribútumok hozzáadásához kattintson **az Összes többi felhasználói attribútum megtekintése és szerkesztése** gombra az alkalmazásnak az SAML-jogkivonatban elküldendő attribútumok szerkesztéséhez, amikor a felhasználók bejelentkeznek.

   Attribútum hozzáadása:

   1.Kattintson **az Attribútum hozzáadása gombra.** Írja be a **Nevet,** és válassza ki az **Értéket** a legördülő menüből.

   2 Kattintson a **Mentés gombra.** Megjelenik az új attribútum a táblán.

### <a name="download-the-azure-ad-metadata-or-certificate"></a>Az Azure AD metaadatainak vagy tanúsítványának letöltése

Az alkalmazás metaadatainak vagy tanúsítványának az Azure AD-ből való letöltéséhez kövesse az alábbi lépéseket:

1. Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként** vagy **társrendszergazdaként.**

2. Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3. Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4. kattintson az Azure Active Directory bal oldali navigációs **menüjében** az Enterprise Applications parancsra.

5. Kattintson **a Minden alkalmazás** elemre az összes alkalmazás listájának megtekintéséhez.

   * Ha nem látja a kívánt alkalmazást, használja a **Szűrő** vezérlőt a **Minden alkalmazás lista** tetején, és állítsa a **Megjelenítés** beállítást **minden alkalmazásra.**

6. Válassza ki az egyszeri bejelentkezést konfigurált alkalmazást.

7. Miután az alkalmazás betöltődik, kattintson az **egyszeri bejelentkezésre** az alkalmazás bal oldali navigációs menüjéből.

8. Nyissa meg az **SAML aláíró tanúsítvány szakaszt,** majd kattintson **az Oszlopérték letöltése** parancsra. Attól függően, hogy az alkalmazás nak mi szükséges egyszeri bejelentkezés konfigurálásához, megjelenik a metaadat-XML vagy a tanúsítvány letöltésének lehetősége.

   Az Azure AD nem ad meg URL-címet a metaadatok leéséhez. A metaadatok csak XML-fájlként olvashatók be.

## <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Az Azure AD-katalógusalkalmazás hoz való egyszeri bejelentkezés jelszó konfigurálása

Egy alkalmazás konfigurálásához az Azure AD-gyűjteményben meg kell:

-   Alkalmazás hozzáadása az Azure AD-gyűjteményből

-   Az alkalmazás konfigurálása egyszeri bejelentkezés jelszóhoz

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Alkalmazás hozzáadása az Azure AD-gyűjteményből

Ha alkalmazást szeretne hozzáadni az Azure AD-galériából, kövesse az alábbi lépéseket:

1.  Nyissa meg az [Azure-portált,](https://portal.azure.com) és jelentkezzen be **globális rendszergazdaként** vagy **társrendszergazdaként**

2.  Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3.  Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4.  kattintson az Azure Active Directory bal oldali navigációs **menüjében** az Enterprise Applications parancsra.

5.  kattintson a Vállalati **alkalmazások** ablaktábla jobb felső sarkában található **Hozzáadás** gombra.

6.  A **Katalógus hozzáadás a szakasznevének** **megadása** csoportba írja be az alkalmazás nevét.

7.  Válassza ki az egyszeri bejelentkezésre konfigurálni kívánt alkalmazást.

8.  Az alkalmazás hozzáadása előtt a **Név** mezőben módosíthatja a nevét.

9.  Az alkalmazás hozzáadásához kattintson a **Hozzáadás** gombra.

Rövid idő elteltével láthatja az alkalmazás konfigurációs ablaktábláját.

### <a name="configure-the-application-for-password-single-sign-on"></a>Az alkalmazás konfigurálása egyszeri bejelentkezés jelszóhoz

Az alkalmazás egyszeri bejelentkezésének konfigurálásához kövesse az alábbi lépéseket:

1. Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként** vagy **társrendszergazdaként.**

2. Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3. Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4. kattintson az Azure Active Directory bal oldali navigációs **menüjében** az Enterprise Applications parancsra.

5. Kattintson **a Minden alkalmazás** elemre az összes alkalmazás listájának megtekintéséhez.

   * Ha nem látja a kívánt alkalmazást, használja a **Szűrő** vezérlőt a **Minden alkalmazás lista** tetején, és állítsa a **Megjelenítés** beállítást **minden alkalmazásra.**

6. Válassza ki az egyszeri bejelentkezést konfigurálni kívánt alkalmazást

7. Miután az alkalmazás betöltődik, kattintson az **egyszeri bejelentkezésre** az alkalmazás bal oldali navigációs menüjéből.

8. Válassza ki a **jelszóalapú bejelentkezés módot.**

9. Felhasználók hozzárendelése az alkalmazáshoz.

10. Ezenkívül a felhasználó nevében is megadhat hitelesítő adatokat, ha kiválasztja a felhasználók sorait, majd a **Hitelesítő adatok frissítése** elemre kattint, és a felhasználók nevében megadja a felhasználónevet és a jelszót. Ellenkező esetben a rendszer felszólítja a felhasználókat, hogy az indításkor maguk adják meg a hitelesítő adatokat.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>A jelszó egyszeri bejelentkezésének konfigurálása nem katalógusalkalmazásokhoz

Egy alkalmazás konfigurálásához az Azure AD-gyűjteményben meg kell:

-   [Nem katalógusalkalmazás hozzáadása](#add-a-non-gallery-application)

-   [Az alkalmazás konfigurálása egyszeri bejelentkezés jelszóhoz](#configure-the-application-for-password-single-sign-on)

### <a name="add-a-non-gallery-application"></a>Nem katalógusalkalmazás hozzáadása

Ha alkalmazást szeretne hozzáadni az Azure AD-galériából, kövesse az alábbi lépéseket:

1.  Nyissa meg az [Azure-portált,](https://portal.azure.com) és jelentkezzen be **globális rendszergazdaként** vagy **társrendszergazdaként**

2.  Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3.  Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4.  kattintson az Azure Active Directory bal oldali navigációs **menüjében** az Enterprise Applications parancsra.

5.  kattintson a Vállalati **alkalmazások** ablaktábla jobb felső sarkában található **Hozzáadás** gombra.

6.  kattintson **a Nem galéria alkalmazás elemre.**

7.  Írja be az alkalmazás nevét a **Név** mezőbe. Válassza a **Hozzáadás lehetőséget.**

Rövid idő elteltével láthatja az alkalmazás konfigurációs ablaktábláját.

### <a name="configure-the-application-for-password-single-sign-on"></a>Az alkalmazás konfigurálása egyszeri bejelentkezés jelszóhoz

Az alkalmazás egyszeri bejelentkezésének konfigurálásához kövesse az alábbi lépéseket:

1. Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként** vagy **társrendszergazdaként.**

2. Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3. Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4. kattintson az Azure Active Directory bal oldali navigációs **menüjében** az Enterprise Applications parancsra.

5. Kattintson **a Minden alkalmazás** elemre az összes alkalmazás listájának megtekintéséhez.

   * Ha nem látja a kívánt alkalmazást, használja a **Szűrő** vezérlőt a **Minden alkalmazás lista** tetején, és állítsa a **Megjelenítés** beállítást **minden alkalmazásra.**

6. Jelölje ki az egyszeri bejelentkezést konfigurálni kívánt alkalmazást.

7. Miután az alkalmazás betöltődik, kattintson az **egyszeri bejelentkezésre** az alkalmazás bal oldali navigációs menüjéből.

8. Válassza ki a **jelszóalapú bejelentkezés módot.**

9. Adja meg a **bejelentkezési URL-címet**. Ez az az URL-cím, ahol a felhasználók beírják a felhasználónevüket és jelszavukat a bejelentkezéshez. Győződjön meg arról, hogy a bejelentkezési mezők láthatók az URL-címen.

10. Felhasználók hozzárendelése az alkalmazáshoz.

11. Ezenkívül a felhasználó nevében is megadhat hitelesítő adatokat, ha kiválasztja a felhasználók sorait, majd a **Hitelesítő adatok frissítése** elemre kattint, és a felhasználók nevében megadja a felhasználónevet és a jelszót. Ellenkező esetben a rendszer felszólítja a felhasználókat, hogy az indításkor maguk adják meg a hitelesítő adatokat.

## <a name="how-to-assign-a-user-to-an-application-directly"></a>Felhasználó közvetlen hozzárendelése egy alkalmazáshoz

Ha egy vagy több felhasználót közvetlenül szeretne egy alkalmazáshoz rendelni, kövesse az alábbi lépéseket:

1. Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként.**

2. Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3. Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4. kattintson az Azure Active Directory bal oldali navigációs **menüjében** az Enterprise Applications parancsra.

5. Kattintson **a Minden alkalmazás** elemre az összes alkalmazás listájának megtekintéséhez.

   * Ha nem látja a kívánt alkalmazást, használja a **Szűrő** vezérlőt a **Minden alkalmazás lista** tetején, és állítsa a **Megjelenítés** beállítást **minden alkalmazásra.**

6. A listából jelölje ki azt az alkalmazást, amelyhez felhasználót szeretne rendelni.

7. Miután az alkalmazás betöltődik, kattintson az alkalmazás bal oldali navigációs menüjének **Felhasználók és csoportok** parancsára.

8. Kattintson a **Hozzáadás** gombra a **Felhasználók és csoportok** lista tetején a Hozzárendelés **hozzáadása** ablaktábla megnyitásához.

9. kattintson a **Felhasználók és csoportok** választóra a Hozzárendelés **hozzáadása** ablaktáblán.

10. Írja be annak a felhasználónak a **teljes nevét** vagy **e-mail címét,** akit szeretne hozzárendelni a **Keresés név vagy e-mail cím** alapján keresőmezőbe.

11. A **jelölőnégyzet**felfedéséhez mutasson a **felhasználó** fölé a listában. Kattintson a felhasználó profilfotója vagy emblémája melletti jelölőnégyzetre, ha hozzá szeretné adni a felhasználót a **Kijelölt** listához.

12. **Nem kötelező:** Ha **egynél több felhasználót**szeretne felvenni, írja be a másik **teljes nevet** vagy **e-mail címet** a Keresés név vagy **e-mail cím** alapján keresőmezőbe, és kattintson a jelölőnégyzetre, ha hozzá szeretné adni a **felhasználót** a Kiválasztott listához.

13. Ha befejezte a felhasználók kiválasztását, a **Kijelölés gombra** kattintva hozzáadhatja őket az alkalmazáshoz rendelendő felhasználók és csoportok listájához.

14. **Nem kötelező:** kattintson a Hozzárendelés **hozzáadása** ablaktábla **Szerepkör kiválasztása** kijelölő gombra a kijelölt felhasználókhoz rendelő szerepkör kiválasztásához.

15. Kattintson a **Hozzárendelés** gombra az alkalmazás kijelölt felhasználókhoz rendeléséhez.

Rövid idő elteltével a kiválasztott felhasználók elindíthatják ezeket az alkalmazásokat a Hozzáférési panelen.

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Ha ezek a hibaelhárítási lépések nem oldják meg a problémát

nyisson meg egy támogatási jegyet a következő információkkal, ha azok rendelkezésre állnak:

-   Korrelációs hiba azonosítója

-   UPN (felhasználói e-mail cím)

-   TenantID

-   Böngésző típusa

-   Időzóna és idő/időkeret hiba esetén

-   Hegedűs nyomok

## <a name="next-steps"></a>További lépések
[Egyszeri bejelentkezés az alkalmazásokba alkalmazásproxyval](application-proxy-configure-single-sign-on-with-kcd.md)


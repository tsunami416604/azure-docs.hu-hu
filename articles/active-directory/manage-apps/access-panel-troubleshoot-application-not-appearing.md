---
title: A hozzárendelt alkalmazás nem jelenik meg a hozzáférési panelen | Microsoft dokumentumok
description: Hibaelhárítás, hogy miért nem jelenik meg egy alkalmazás a Hozzáférési panelen
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
ms.topic: article
ms.date: 09/09/2018
ms.author: mimart
ms.reviwer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10dfcf337dc75a202e781e931f38783291a72fe7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67272744"
---
# <a name="an-assigned-application-is-not-appearing-on-the-access-panel"></a>A hozzárendelt alkalmazás nem jelenik meg a hozzáférési panelen

A Hozzáférési panel egy webalapú portál, amely lehetővé teszi, hogy az Azure Active Directoryban (Azure AD) munkahelyi vagy iskolai fiókkal rendelkező felhasználó megtekintse és elindítsa azokat a felhőalapú alkalmazásokat, amelyekhez az Azure AD-rendszergazda hozzáférést biztosított számukra. Ezek az alkalmazások a felhasználó nevében vannak konfigurálva az Azure AD portálon. Az alkalmazást megfelelően kell konfigurálni, és hozzá kell rendelni ahhoz a felhasználóhoz vagy csoporthoz, amelynek a felhasználó tagja ahhoz, hogy az alkalmazást a Hozzáférési panelen láthassa.

A felhasználó által látott alkalmazások típusa a következő kategóriákba sorolható:

-   Office 365-alkalmazások

-   Összevonásalapú egyszeri felvonással konfigurált Microsoft- és külső alkalmazások

-   Jelszóalapú egyszeri bejelentkezésalkalmazások

-   Meglévő SSO-megoldásokkal rendelkező alkalmazások

## <a name="general-issues-to-check-first"></a>Általános kérdések, amelyeket először ellenőrizni kell

-   Ha egy alkalmazást csak egy felhasználóhoz adtak hozzá, próbáljon meg bejelentkezni és kijelentkezni a felhasználó hozzáférési paneljén néhány perc múlva, hogy lássa, az alkalmazás hozzá van-e adva.

-   Ha egy licencet éppen most távolítottak el egy felhasználótól vagy csoportból, a felhasználó ennek a tagja, a csoport méretétől és összetettségétől függően a módosítások végrehajtása hosszú időt vehet igénybe. Hagyjon több időt a hozzáférési panelre való bejelentkezés előtt.

## <a name="problems-related-to-application-configuration"></a>Az alkalmazás konfigurációjával kapcsolatos problémák

Előfordulhat, hogy egy alkalmazás nem jelenik meg a felhasználó hozzáférési paneljén, mert nincs megfelelően konfigurálva. Az alábbiakban néhány módszert olvashat az alkalmazáskonfigurációval kapcsolatos problémák elhárításáról:

-   [Az összevont egyszeri bejelentkezés konfigurálása egy Azure AD-katalógus-alkalmazáshoz](#how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application)

-   [Nem katalógusalkalmazások összevont egyszeri bejelentkezésének konfigurálása](#how-to-configure-federated-single-sign-on-for-a-non-gallery-application)

-   [Egyszeri bejelentkezési alkalmazás konfigurálása egy Azure AD-katalógusalkalmazáshoz](#how-to-configure-password-single-sign-on-for-a-non-gallery-application)

-   [Jelszó egyszeri bejelentkezési alkalmazás konfigurálása nem katalógusalkalmazáshoz](#how-to-configure-password-single-sign-on-for-a-non-gallery-application)

### <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Az összevont egyszeri bejelentkezés konfigurálása egy Azure AD-katalógus-alkalmazáshoz

Az Azure AD-gyűjteményben a vállalati egyszeri bejelentkezés funkcióval engedélyezett összes alkalmazás lépésről lépésre elérhető. Részletes útmutatást nyújt vagnált részletes útmutatásért az Azure Active Directoryval való [SaaS-alkalmazások integrálásáról szóló oktatóanyagok listája.](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)

Egy alkalmazás konfigurálásához az Azure AD-gyűjteményben meg kell:

-   [Alkalmazás hozzáadása az Azure AD-gyűjteményből](#add-an-application-from-the-azure-ad-gallery)

-   [Konfigurálja az alkalmazás metaadat-értékeit az Azure AD-ben (Bejelentkezés URL-cím, azonosító, válasz URL)Configure the application's metadata values in Azure AD (Sign on URL, Identifier, Reply URL)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Válassza ki a felhasználói azonosítót, és adja hozzá az alkalmazásnak küldendő felhasználói attribútumokat](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Az Azure AD metaadatainak és tanúsítványának lekérése](#download-the-azure-ad-metadata-or-certificate)

-   [Azure AD metaadat-értékek konfigurálása az alkalmazásban (Bejelentkezés URL-cím, kiállító, kijelentkezési URL és tanúsítvány)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

#### <a name="add-an-application-from-the-azure-ad-gallery"></a>Alkalmazás hozzáadása az Azure AD-gyűjteményből

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

#### <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Egyszeri bejelentkezés konfigurálása egy alkalmazáshoz az Azure AD-gyűjteményből

Az alkalmazás egyszeri bejelentkezésének konfigurálásához kövesse az alábbi lépéseket:

1. Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként** vagy **társrendszergazdaként.**

2. Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3. Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4. kattintson az Azure Active Directory bal oldali navigációs **menüjében** az Enterprise Applications parancsra.

5. Kattintson **a Minden alkalmazás** elemre az összes alkalmazás listájának megtekintéséhez.

   * Ha nem látja a kívánt alkalmazást, használja a **Szűrő** vezérlőt a **Minden alkalmazás lista** tetején, és állítsa a **Megjelenítés** beállítást **minden alkalmazásra.**

6. Jelölje ki az egyszeri bejelentkezést konfigurálni kívánt alkalmazást.

7. Miután az alkalmazás betöltődik, kattintson az **egyszeri bejelentkezésre** az alkalmazás bal oldali navigációs menüjéből.

8. Válassza **az SAML-alapú bejelentkezés** lehetőséget a **Mód** legördülő menüből.

9. Adja meg a szükséges értékeket **a Tartomány és az URL-címek között.** Ezeket az értékeket az alkalmazás szállítójától kell beszereznie.

   1. Az alkalmazás SP által kezdeményezett egyszeri bejelentkezésként való konfigurálásához a Bejelentkezési URL-cím kötelező érték. Egyes alkalmazások esetében az azonosító is kötelező érték.

   2. Az alkalmazás IdP által kezdeményezett egyszeri szolgáltatóként való konfigurálásához a válasz URL-címe kötelező érték. Egyes alkalmazások esetében az azonosító is kötelező érték.

10. **Nem kötelező:** kattintson **a Speciális URL-beállítások megjelenítése** gombra, ha meg szeretné tekinteni a nem kötelező értékeket.

11. A **Felhasználói attribútumok között**válassza ki a felhasználók egyedi azonosítóját a **Felhasználói azonosító** legördülő menüben.

12. **Nem kötelező:** kattintson **az Összes többi felhasználói attribútum megtekintése és szerkesztése** parancsra az SAML-jogkivonatban az alkalmazásnak küldendő attribútumok szerkesztéséhez, amikor a felhasználók bejelentkeznek.

    Attribútum hozzáadása:

    1. kattintson **az Attribútum hozzáadása gombra.** Írja be a **Nevet,** és válassza ki az **Értéket** a legördülő menüből.

    2. Kattintson a **Mentés gombra.** Megjelenik az új attribútum a táblán.

13. Kattintson **az&gt; Alkalmazásnév &lt;konfigurálása** elemre az egyszeri bejelentkezés konfigurálásához szükséges dokumentáció eléréséhez. Emellett rendelkezik az sso és az alkalmazás beállításához szükséges metaadat-URL-címek és tanúsítvány.

14. a konfiguráció mentéséhez kattintson a **Mentés** gombra.

15. Felhasználók hozzárendelése az alkalmazáshoz.

#### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Válassza ki a felhasználói azonosítót, és adja hozzá az alkalmazásnak küldendő felhasználói attribútumokat

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
   >Az Azure AD kiválasztja a NameID attribútum (felhasználói azonosító) formátumát a kiválasztott érték vagy az alkalmazás által az SAML AuthRequest-ben kért formátum alapján. További információkért látogasson el a cikk [Egyszeri bejelentkezés SAML protokoll](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) szakaszban NameIDPolicy.
   >
   >

9. Felhasználói attribútumok hozzáadásához kattintson **az Összes többi felhasználói attribútum megtekintése és szerkesztése** gombra az alkalmazásnak az SAML-jogkivonatban elküldendő attribútumok szerkesztéséhez, amikor a felhasználók bejelentkeznek.

   Attribútum hozzáadása:

   1. kattintson **az Attribútum hozzáadása gombra.** Írja be a **Nevet,** és válassza ki az **Értéket** a legördülő menüből.

   2. Kattintson a **Mentés gombra.** Az új attribútum megjelenik a táblázatban.

#### <a name="download-the-azure-ad-metadata-or-certificate"></a>Az Azure AD metaadatainak vagy tanúsítványának letöltése

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

### <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Nem katalógusalkalmazások összevont egyszeri bejelentkezésének konfigurálása

Egy nem katalógusalkalmazás konfigurálásához azure AD prémium szintű azure-prémiummal kell rendelkeznie, és az alkalmazás támogatja az SAML 2.0-t. Az Azure AD-verziókról az [Azure AD díjszabása](https://azure.microsoft.com/pricing/details/active-directory/)című webhelyen talál további információt.

-   [Konfigurálja az alkalmazás metaadat-értékeit az Azure AD-ben (Bejelentkezés URL-cím, azonosító, válasz URL)Configure the application's metadata values in Azure AD (Sign on URL, Identifier, Reply URL)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Válassza ki a felhasználói azonosítót, és adja hozzá az alkalmazásnak küldendő felhasználói attribútumokat](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Az Azure AD metaadatainak és tanúsítványának lekérése](#download-the-azure-ad-metadata-or-certificate)

-   [Azure AD metaadat-értékek konfigurálása az alkalmazásban (Bejelentkezés URL-cím, kiállító, kijelentkezési URL és tanúsítvány)](#configure-the-application-for-password-single-sign-on-1)

#### <a name="configure-the-applications-metadata-values-in-azure-ad-sign-on-url-identifier-reply-url"></a>Konfigurálja az alkalmazás metaadat-értékeit az Azure AD-ben (Bejelentkezés URL-cím, azonosító, válasz URL)Configure the application's metadata values in Azure AD (Sign on URL, Identifier, Reply URL)

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

10. Válassza **az SAML-alapú bejelentkezés** lehetőséget a Mód legördülő **menüben.**

11. Adja meg a szükséges értékeket **a Tartomány és az URL-címek között.** Ezeket az értékeket az alkalmazás szállítójától kell beszereznie.

    1. Ha az alkalmazást IdP által kezdeményezett egyszeri szolgáltatóként szeretné konfigurálni, írja be a Válasz URL-címét és az azonosítót.

    2.  **Nem kötelező:** Az alkalmazás SP által kezdeményezett egyszeri bejelentkezésként való konfigurálásához a Bejelentkezési URL-cím kötelező érték.

12. A **Felhasználói attribútumok között**válassza ki a felhasználók egyedi azonosítóját a **Felhasználói azonosító** legördülő menüben.

13. **Nem kötelező:** kattintson **az Összes többi felhasználói attribútum megtekintése és szerkesztése** parancsra az SAML-jogkivonatban az alkalmazásnak küldendő attribútumok szerkesztéséhez, amikor a felhasználók bejelentkeznek.

    Attribútum hozzáadása:

    1. kattintson **az Attribútum hozzáadása gombra.** Írja be a **Nevet,** és válassza ki az **Értéket** a legördülő menüből.

    2. Kattintson a **Mentés gombra.** Megjelenik az új attribútum a táblán.

14. Kattintson **az&gt; Alkalmazásnév &lt;konfigurálása** elemre az egyszeri bejelentkezés konfigurálásához szükséges dokumentáció eléréséhez. Emellett rendelkezik az azure-beli AD URL-címek és az alkalmazás szükséges tanúsítványokat.

#### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Válassza ki a felhasználói azonosítót, és adja hozzá az alkalmazásnak küldendő felhasználói attribútumokat

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
   >Az Azure AD kiválasztja a NameID attribútum (felhasználói azonosító) formátumát a kiválasztott érték vagy az alkalmazás által az SAML AuthRequest-ben kért formátum alapján. További információkért látogasson el a cikk [Egyszeri bejelentkezés SAML protokoll](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) szakaszban NameIDPolicy.
   >
   >

9. Felhasználói attribútumok hozzáadásához kattintson **az Összes többi felhasználói attribútum megtekintése és szerkesztése** gombra az alkalmazásnak az SAML-jogkivonatban elküldendő attribútumok szerkesztéséhez, amikor a felhasználók bejelentkeznek.

   Attribútum hozzáadása:

   1. kattintson **az Attribútum hozzáadása gombra.** Írja be a **Nevet,** és válassza ki az **Értéket** a legördülő menüből.

   2. Kattintson a **Mentés gombra.** Megjelenik az új attribútum a táblán.

#### <a name="download-the-azure-ad-metadata-or-certificate"></a>Az Azure AD metaadatainak vagy tanúsítványának letöltése

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

### <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Az Azure AD-katalógusalkalmazás hoz való egyszeri bejelentkezés jelszó konfigurálása

Egy alkalmazás konfigurálásához az Azure AD-gyűjteményben meg kell:

-   [Alkalmazás hozzáadása az Azure AD-gyűjteményből](#add-an-application-from-the-azure-ad-gallery)

-   [Az alkalmazás konfigurálása egyszeri bejelentkezés jelszóhoz](#configure-the-application-for-password-single-sign-on)

#### <a name="add-an-application-from-the-azure-ad-gallery"></a>Alkalmazás hozzáadása az Azure AD-gyűjteményből

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

#### <a name="configure-the-application-for-password-single-sign-on"></a>Az alkalmazás konfigurálása egyszeri bejelentkezés jelszóhoz

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

9. [Felhasználók hozzárendelése az alkalmazáshoz](#how-to-assign-a-user-to-an-application-directly).

10. Ezenkívül a felhasználó nevében is megadhat hitelesítő adatokat, ha kiválasztja a felhasználók sorait, majd a **Hitelesítő adatok frissítése** elemre kattint, és a felhasználók nevében megadja a felhasználónevet és a jelszót. Ellenkező esetben a rendszer felszólítja a felhasználókat, hogy az indításkor maguk adják meg a hitelesítő adatokat.

### <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>A jelszó egyszeri bejelentkezésének konfigurálása nem katalógusalkalmazásokhoz

Egy alkalmazás konfigurálásához az Azure AD-gyűjteményben meg kell:

-   [Nem katalógusalkalmazás hozzáadása](#add-a-non-gallery-application)

-   [Az alkalmazás konfigurálása egyszeri bejelentkezés jelszóhoz](#configure-the-application-for-password-single-sign-on)

#### <a name="add-a-non-gallery-application"></a>Nem katalógusalkalmazás hozzáadása

Ha alkalmazást szeretne hozzáadni az Azure AD-galériából, kövesse az alábbi lépéseket:

1.  Nyissa meg az [Azure-portált,](https://portal.azure.com) és jelentkezzen be **globális rendszergazdaként** vagy **társrendszergazdaként.**

2.  Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3.  Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4.  kattintson az Azure Active Directory bal oldali navigációs **menüjében** az Enterprise Applications parancsra.

5.  kattintson a Vállalati **alkalmazások** ablaktábla jobb felső sarkában található **Hozzáadás** gombra.

6.  kattintson **a Nem galéria alkalmazás elemre.**

7.  Írja be az alkalmazás nevét a **Név** mezőbe. Válassza a **Hozzáadás lehetőséget.**

Rövid idő elteltével láthatja az alkalmazás konfigurációs ablaktábláját.

#### <a name="configure-the-application-for-password-single-sign-on"></a><a name="configure-the-application-for-password-single-sign-on-1"></a>Az alkalmazás konfigurálása egyszeri bejelentkezés jelszóhoz

Az alkalmazás egyszeri bejelentkezésének konfigurálásához kövesse az alábbi lépéseket:

1.  Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként** vagy **társrendszergazdaként.**

2.  Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3.  Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4.  kattintson az Azure Active Directory bal oldali navigációs **menüjében** az Enterprise Applications parancsra.

5.  Kattintson **a Minden alkalmazás** elemre az összes alkalmazás listájának megtekintéséhez.

    1.  Ha nem látja a kívánt alkalmazást, használja a **Szűrő** vezérlőt a **Minden alkalmazás lista** tetején, és állítsa a **Megjelenítés** beállítást **minden alkalmazásra.**

6.  Jelölje ki az egyszeri bejelentkezést konfigurálni kívánt alkalmazást.

7.  Miután az alkalmazás betöltődik, kattintson az **egyszeri bejelentkezésre** az alkalmazás bal oldali navigációs menüjéből.

8.  Válassza ki a **jelszóalapú bejelentkezés módot.**

9.  Adja meg a **bejelentkezési URL-címet**. Ez az az URL-cím, ahová a felhasználók beadják a felhasználónevüket és jelszavukat, hogy bejelentkezhessenek. Győződjön meg arról, hogy a bejelentkezési mezők láthatók az URL-címen.

10. [Felhasználók hozzárendelése az alkalmazáshoz](#how-to-assign-a-user-to-an-application-directly).

11. Ezenkívül a felhasználó nevében is megadhat hitelesítő adatokat, ha kiválasztja a felhasználók sorait, majd a **Hitelesítő adatok frissítése** elemre kattint, és a felhasználók nevében megadja a felhasználónevet és a jelszót. Ellenkező esetben a rendszer felszólítja a felhasználókat, hogy az indításkor maguk adják meg a hitelesítő adatokat.

## <a name="problems-related-to-assigning-applications-to-users"></a>Az alkalmazások felhasználókhoz való hozzárendelésével kapcsolatos problémák

Előfordulhat, hogy egy felhasználó nem látja az alkalmazást a hozzáférési panelen, mert nincs hozzárendelve az alkalmazáshoz. Az alábbiakban néhány módja annak, hogy ellenőrizze:

-   [Annak ellenőrzése, hogy egy felhasználó hozzá van-e rendelve az alkalmazáshoz](#check-if-a-user-is-assigned-to-the-application)

-   [Felhasználó közvetlen hozzárendelése egy alkalmazáshoz](#how-to-assign-a-user-to-an-application-directly)

-   [Annak ellenőrzése, hogy egy felhasználó hozzá van-e rendelve az alkalmazáshoz kapcsolódó licenchez](#check-if-a-user-is-under-a-license-related-to-the-application)

-   [Licenc hozzárendelése felhasználóhoz](#how-to-assign-a-user-a-license)

### <a name="check-if-a-user-is-assigned-to-the-application"></a>Annak ellenőrzése, hogy egy felhasználó hozzá van-e rendelve az alkalmazáshoz

Annak ellenőrzéséhez, hogy egy felhasználó hozzá van-e rendelve az alkalmazáshoz, kövesse az alábbi lépéseket:

1. Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként.**

2. Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3. Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4. kattintson az Azure Active Directory bal oldali navigációs **menüjében** az Enterprise Applications parancsra.

5. Kattintson **a Minden alkalmazás** elemre az összes alkalmazás listájának megtekintéséhez.

6. **Keresse meg** a kérdéses alkalmazás nevét.

7. kattintson a **Felhasználók és csoportok**elemre.

8. Ellenőrizze, hogy a felhasználó hozzá van-e rendelve az alkalmazáshoz.

   * Ha nem követi a "Hogyan lehet egy felhasználót közvetlenül hozzárendelni egy alkalmazáshoz" című részben leírt lépéseket.

### <a name="how-to-assign-a-user-to-an-application-directly"></a>Felhasználó közvetlen hozzárendelése egy alkalmazáshoz

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

### <a name="check-if-a-user-is-under-a-license-related-to-the-application"></a>Annak ellenőrzése, hogy a felhasználó az alkalmazáshoz kapcsolódó licenc alatt áll-e

A felhasználó hozzárendelt licenceinek ellenőrzéséhez kövesse az alábbi lépéseket:

1. Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként.**

2. Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3. Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4. kattintson a navigációs menü **Felhasználók és csoportok** parancsára.

5. kattintson **a Minden felhasználó gombra.**

6. **Keresse meg** az önt érdeklő felhasználót, és **kattintson a** kijelölni kívánt sorra.

7. Kattintson **a Licencek** elemre, ha meg szeretné tekinteni, hogy a felhasználó jelenleg mely licenceket rendelte hozzá.

   * Ha a felhasználó Office-licenchez van rendelve, ez lehetővé teszi, hogy a First Party Office alkalmazások megjelenjenek a felhasználó hozzáférési paneljén.

### <a name="how-to-assign-a-user-a-license"></a>Hogyan rendelhet hozzá egy felhasználóhoz licencet? 

Ha licencet szeretne hozzárendelni egy felhasználóhoz, kövesse az alábbi lépéseket:

1.  Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként.**

2.  Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3.  Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4.  kattintson a navigációs menü **Felhasználók és csoportok** parancsára.

5.  kattintson **a Minden felhasználó gombra.**

6.  **Keresse meg** az önt érdeklő felhasználót, és **kattintson a** kijelölni kívánt sorra.

7.  Kattintson **a Licencek** elemre, ha meg szeretné tekinteni, hogy a felhasználó jelenleg mely licenceket rendelte hozzá.

8.  kattintson a **Hozzárendelés** gombra.

9.  Válasszon ki **egy vagy több terméket** az elérhető termékek listájából.

10. **Nem kötelező,** hogy a termékek részletes hozzárendeléséhez kattintson a **hozzárendelési beállítások** elemre. Ha ennek befejeződött, kattintson az **Ok** gombra.

11. A **Hozzárendelés** gombra kattintva rendelje hozzá ezeket a licenceket a felhasználóhoz.

## <a name="problems-related-to-assigning-applications-to-groups"></a>Az alkalmazások csoportokhoz való hozzárendelésével kapcsolatos problémák

Előfordulhat, hogy egy felhasználó egy alkalmazást lát a hozzáférési panelen, mert egy olyan csoport része, amelyhez az alkalmazás hozzá van rendelve. Az alábbiakban néhány módja annak, hogy ellenőrizze:

-   [Felhasználó csoporttagságának ellenőrzése](#check-a-users-group-memberships)

-   [Alkalmazás közvetlen hozzárendelése egy csoporthoz](#how-to-assign-an-application-to-a-group-directly)

-   [Annak ellenőrzése, hogy egy felhasználó része-e a licenchez rendelt csoportnak](#check-if-a-user-is-part-of-group-assigned-to-a-license)

-   [Licenc hozzárendelése csoporthoz](#how-to-assign-a-license-to-a-group)

### <a name="check-a-users-group-memberships"></a>Felhasználó csoporttagságának ellenőrzése

A csoport tagságának ellenőrzéséhez kövesse az alábbi lépéseket:

1. Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként.**

2. Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3. Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4. kattintson a navigációs menü **Felhasználók és csoportok** parancsára.

5. kattintson **a Minden felhasználó gombra.**

6. **Keresse meg** az önt érdeklő felhasználót, és **kattintson a** kijelölni kívánt sorra.

7. kattintson **a Csoportok gombra.**

8. Ellenőrizze, hogy a felhasználó tagja-e az alkalmazáshoz rendelt csoportnak.

   * Ha el szeretné távolítani a felhasználót a csoportból, **kattintson a** csoport sorára, és válassza a törlés lehetőséget.

### <a name="how-to-assign-an-application-to-a-group-directly"></a>Alkalmazás közvetlen hozzárendelése egy csoporthoz

Ha egy vagy több csoportot közvetlenül szeretne egy alkalmazáshoz rendelni, kövesse az alábbi lépéseket:

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

10. Írja be annak a csoportnak a **teljes csoportnevét,** amelyet hozzá szeretne rendelni a **Keresés név vagy e-mail cím** alapján keresőmezőbe.

11. A jelölőnégyzet felfedéséhez mutasson a **csoportra** a **listában.** Kattintson a csoport profilfotója vagy emblémája melletti jelölőnégyzetre, ha hozzá szeretné adni felhasználóját a **Kijelölt** listához.

12. **Nem kötelező:** Ha **egynél több csoportot**szeretne hozzáadni, írja be a teljes csoport **nevét** a Keresés név **vagy e-mail cím** alapján keresőmezőbe, és kattintson a jelölőnégyzetre, ha hozzá szeretné adni ezt a csoportot a **Kijelölt** listához.

13. Ha befejezte a csoportok kijelölését, a **Kijelölés gombra** kattintva vegye fel őket az alkalmazáshoz rendelendő felhasználók és csoportok listájára.

14. **Nem kötelező:** kattintson a Hozzárendelés **hozzáadása** ablaktábla **Szerepkör kiválasztása** kijelölő gombra a kijelölt csoportokhoz rendelő szerepkör kiválasztásához.

15. Kattintson a **Hozzárendelés** gombra, ha az alkalmazást a kijelölt csoportokhoz szeretné rendelni.

Rövid idő elteltével a kiválasztott felhasználók elindíthatják ezeket az alkalmazásokat a Hozzáférési panelen.

### <a name="check-if-a-user-is-part-of-group-assigned-to-a-license"></a>Annak ellenőrzése, hogy egy felhasználó része-e a licenchez rendelt csoportnak

1. Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként.**

2. Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3. Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4. kattintson a navigációs menü **Felhasználók és csoportok** parancsára.

5. kattintson **a Minden felhasználó gombra.**

6. **Keresse meg** az önt érdeklő felhasználót, és **kattintson a** kijelölni kívánt sorra.

7. kattintson **a Csoportok gombra.**

8. kattintson egy adott csoport sorára.

9. **Kattintson a Licencek** elemre, ha meg szeretné tekinteni, hogy a csoport mely licenceket rendelte hozzá.

   * Ha a csoport Office-licenchez van rendelve, előfordulhat, hogy bizonyos First Party Office alkalmazások megjelennek a felhasználó hozzáférési paneljén.

### <a name="how-to-assign-a-license-to-a-group"></a>Licenc hozzárendelése csoporthoz

Ha licencet szeretne hozzárendelni egy csoporthoz, kövesse az alábbi lépéseket:

1.  Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként.**

2.  Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3.  Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4.  kattintson a navigációs menü **Felhasználók és csoportok** parancsára.

5.  kattintson **a Minden csoport gombra.**

6.  **Keresse meg** az önt érdeklő csoportot, és **kattintson a** kijelölni kívánt sorra.

7.  **Kattintson a Licencek** elemre, ha meg szeretné tekinteni, hogy a csoport jelenleg mely licenceket rendelte hozzá.

8.  kattintson a **Hozzárendelés** gombra.

9.  Válasszon ki **egy vagy több terméket** az elérhető termékek listájából.

10. **Nem kötelező,** hogy a termékek részletes hozzárendeléséhez kattintson a **hozzárendelési beállítások** elemre. Ha ennek befejeződött, kattintson az **Ok** gombra.

11. A **Hozzárendelés** gombra kattintva rendelje hozzá ezeket a licenceket ehhez a csoporthoz. Ez hosszú időt vehet igénybe, a csoport méretétől és összetettségétől függően.

>[!NOTE]
>A gyorsabb, fontolja meg egy licenc ideiglenes hozzárendelése a felhasználó közvetlenül. 
>
>

## <a name="next-steps"></a>További lépések
[Új felhasználók hozzáadása az Azure Active Directoryhoz](./../fundamentals/add-users-azure-active-directory.md)


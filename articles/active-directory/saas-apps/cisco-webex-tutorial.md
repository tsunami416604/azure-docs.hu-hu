---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Cisco Webex-értekezletekkel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Cisco Webex-értekezletek között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/21/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4e497c556bde1be4e498cd85a68282a0e3b2666
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "72026269"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex-meetings"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Cisco Webex-értekezletekkel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Cisco Webex-értekezleteket az Azure Active Directoryval (Azure AD). Ha integrálja a Cisco Webex-értekezleteket az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-t, amely hozzáfér a Cisco Webex-értekezletekhez.
* Lehetővé teszi, hogy a felhasználók automatikusan bejelentkezhessenek a Cisco Webex-értekezletekbe az Azure AD-fiókjukkal.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Cisco Webex Meetings egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* A Cisco Webex Meetings támogatja az **SP és az IDP** által kezdeményezett sso-t

* A Cisco Webex Meetings támogatja **a Just In Time** felhasználói kiépítést

## <a name="adding-cisco-webex-meetings-from-the-gallery"></a>Cisco Webex-értekezletek hozzáadása a galériából

A Cisco Webex-értekezletek Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Cisco Webex-értekezleteket a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **gyűjtemény hozzáadásszakaszában** írja be a **Cisco Webex-értekezletek** kifejezést a keresőmezőbe.
1. Válassza a **Cisco Webex Meetings lehetőséget** az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cisco-webex-meetings"></a>Az Azure AD egyszeri bejelentkezésének konfigurálása és tesztelése a Cisco Webex-értekezletekhez

Konfigurálja és tesztelje az Azure AD SSO-t a Cisco Webex-értekezletekkel egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Cisco Webex-értekezletekben.

Az Azure AD SSO cisco webex-értekezletekkel való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    1. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
2. **[Konfigurálja a Cisco Webex Meetings Egyszeri bejelentkezés egyszeri](#configure-cisco-webex-meetings-sso)** beállítását – az alkalmazás oldalon az egyszeri bejelentkezési beállítások konfigurálásához.
    1. **[Hozzon létre Cisco Webex Meetings teszt felhasználó](#create-cisco-webex-meetings-test-user)** - hogy egy megfelelője B.Simon a Cisco Webex találkozók, amely kapcsolódik az Azure AD felhasználói ábrázolása.
3. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Cisco Webex-értekezletek** alkalmazásintegrációs lapján keresse meg a **Kezelés szakaszt,** és válassza **az egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az alkalmazást **IDP** által kezdeményezett módban konfigurálhatja a **Szolgáltató metaadatfájljának** feltöltésével az alábbiak szerint:

    a. Kattintson **a Metaadatfájl feltöltése gombra.**

    b. Kattintson a **mappaemblémára** a metaadatfájl kijelöléséhez, majd a **Feltöltés gombra.**

    c. A Szolgáltató metaadatfájljának sikeres feltöltése után az **azonosító** és a **válasz URL-értéke** automatikusan feltöltődik az **Egyszerű SAML konfiguráció szakaszban.**

    >[!Note]
    >A Szolgáltató metaadatfájlját a **Configure Cisco Webex Meetings SSO** szakaszból kapja, amelyet az oktatóanyag későbbi részében ismertetünk. 

1. Ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni, hajtsa végre a következő lépéseket:  

    a. Az **Egyszerű SAML-konfiguráció csoportban** kattintson a szerkesztés/toll ikonra.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)
    
    b. A Bejelentkezés az **URL-cím** mezőbe írja be az URL-címet a következő minta használatával:` https://<customername>.my.webex.com`

5. A Cisco Webex Meetings alkalmazás az SAML-állításokat egy adott formátumban várja, amely megköveteli, hogy egyéni attribútumleképezéseket adjon hozzá az SAML token attribútumkonfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Kattintson a **Szerkesztés** ikonra a Felhasználói attribútumok párbeszédpanel megnyitásához.

    ![image](common/edit-attribute.png)

6. A fentieken kívül a Cisco Webex Meetings alkalmazás arra számít, hogy még néhány attribútum ot kell visszaadni az SAML válaszban. A Felhasználói attribútumok párbeszédpanel Felhasználói jogcímek szakaszában hajtsa végre az alábbi lépéseket az SAML token attribútum hozzáadásához az alábbi táblázatban látható módon: 

    | Név | Forrás attribútuma|
    | ---------------|  --------- |
    |   Utónév    | user.givenname |
    |   Vezetéknév    | user.vezetéknév |
    |   e-mail       | user.mail |
    |   Uid    | user.mail |

    a. Kattintson **az Új jogcím hozzáadása** gombra a Felhasználói **jogcímek kezelése** párbeszédpanel megnyitásához.

    b. A **Név** mezőbe írja be a sor attribútumnevét.

    c. Hagyja üresen a **névteret.**

    d. Válassza a Forrás **attribútumként lehetőséget.**

    e. A **Forrás attribútumlistában** válassza ki az adott sor attribútumértékét a legördülő listából.

    f. Kattintson a **Mentés** gombra.

4. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az SAML aláíró tanúsítvány szakaszban keresse meg az **összevonási** **metaadatok XML-jét,** és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. A **Cisco Webex-értekezletek beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy tesztfelhasználót hoz létre az Azure Portalon B.Simon néven.

1. Az Azure Portal bal oldali ablaktáblájában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd válassza az **Összes felhasználó**lehetőséget.
1. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.
1. A **Felhasználói** tulajdonságok csoportban hajtsa végre az alábbi lépéseket:
    1. A **Név** mezőbe írja a következőt: `B.Simon`.  
    1. A **Felhasználónév** mezőbe írja username@companydomain.extensionbe a mezőt. Például: `B.Simon@contoso.com`.
    1. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet, majd írja le a **Jelszó** mezőben megjelenő értéket.
    1. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban lehetővé teszi b.Simon azure egyszeri bejelentkezés t a Cisco Webex találkozók hozzáférést biztosít.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Cisco Webex Meetings lehetőséget.**
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-cisco-webex-meetings-sso"></a>A Cisco Webex Találkozók SSO-jának konfigurálása

1. Nyissa `https://<customername>.webex.com/admin` meg az URL-címet a felügyeleti hitelesítő adatokkal.

2. Nyissa meg a **Közös webhelybeállításait,** és keresse meg az **SSO-konfiguráció lehetőséget.**
 
    ![Egyszeri bejelentkezés konfigurálása](./media/cisco-webex-tutorial/tutorial-cisco-webex-11.png)

3. A **Webex Adminisztráció** lapján hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/cisco-webex-tutorial/tutorial-cisco-webex-10.png)

    a. válassza **az SAML 2.0-t** **összevonási protokollként.**

    b. Kattintson **az SAML metaadatok importálása** hivatkozásra az Azure Portalról letöltött metaadatfájl feltöltéséhez.

    c. Kattintson az **Exportálás** gombra a Szolgáltató metaadatfájljának letöltéséhez és feltöltéséhez az Azure Portal **egyszerű SAML-konfigurációja** szakaszában.

    d. Az **AuthContextClassRef** szövegmezőbe `urn:oasis:names:tc:SAML:2.0:ac:classes:unspecified` írja be a következőt, és ha engedélyezni szeretné az MFA-t az Azure AD használatával, írja be a két értéket, például`urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport;urn:oasis:names:tc:SAML:2.0:ac:classes:X509`

    e. Válassza **az Automatikus fiók létrehozása lehetőséget.**

    >[!NOTE]
    >A **just-in-time** felhasználói kiépítés engedélyezéséhez ellenőriznie kell az **Automatikus fiók létrehozása című verziót.** Amellett, hogy saml token attribútumok at kell átadni az SAML válasz.

    f. Kattintson a **Mentés** gombra.

    >[!NOTE]
    >Ez a konfiguráció csak azoknak az ügyfeleknek szól, akik e-mail formátumban használják a Webex UserID-t.

### <a name="create-cisco-webex-meetings-test-user"></a>Cisco Webex Meetings tesztfelhasználó létrehozása

A cél ebben a szakaszban, hogy hozzon létre egy felhasználó nevű B.Simon a Cisco Webex találkozók. A Cisco Webex Meetings támogatja **a just-in-time** kiépítést, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nincs műveletelem. Ha a felhasználó még nem létezik a Cisco Webex találkozók, egy új jön létre, amikor megpróbálja elérni a Cisco Webex találkozók.

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a Cisco Webex Meetings csempére kattint, automatikusan be kell jelentkeznie a Cisco Webex-értekezletekbe, amelyekhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a ServiceNow szolgáltatást az Azure AD-vel](https://aad.portal.azure.com)

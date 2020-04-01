---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a ScaleX Enterprise-szal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a ScaleX Enterprise között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c2379a8d-a659-45f1-87db-9ba156d83183
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e54994d02dd1abbca1602952fbad058b3ad993d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "72594261"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-scalex-enterprise"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a ScaleX Enterprise-szal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a ScaleX Enterprise-t az Azure Active Directoryval (Azure AD). Ha integrálja a ScaleX Enterprise-t az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a ScaleX Enterprise-hoz.
* Lehetővé teszi, hogy a felhasználók automatikusan bejelentkezve ScaleX Enterprise az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* ScaleX Enterprise egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* A ScaleX Enterprise támogatja az **SP és az IDP** által kezdeményezett sso-t

## <a name="adding-scalex-enterprise-from-the-gallery"></a>ScaleX Enterprise hozzáadása a galériából

A ScaleX Enterprise azure AD-be való integrálásának konfigurálásához hozzá kell adnia a ScaleX Enterprise-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **Hozzáadás a gyűjteményből szakaszban** írja be a **ScaleX Enterprise** kifejezést a keresőmezőbe.
1. Válassza a **ScaleX Enterprise** elemet az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.


## <a name="configure-and-test-azure-ad-single-sign-on-for-scalex-enterprise"></a>Az Azure AD egyszeri bejelentkezésének konfigurálása és tesztelése a ScaleX Enterprise-hoz

Konfigurálja és tesztelje az Azure AD SSO-t a ScaleX Enterprise-szal egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a scalex enterprise kapcsolódó felhasználója között.

Az Azure AD SSO scaleX Enterprise-szal való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    * **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    * **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[A ScaleX Enterprise Egyszeri bejelentkezés konfigurálása](#configure-scalex-enterprise-sso)** – az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
    * **[ScaleX Enterprise tesztfelhasználó](#create-scalex-enterprise-test-user)** létrehozása – b.Simon megfelelője a ScaleX Enterprise-on, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **ScaleX Enterprise** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban, ha az alkalmazást **IDP** által kezdeményezett módban szeretné konfigurálni, adja meg a következő mezők értékeit:

    a. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`https://platform.rescale.com/saml2/<company id>/`

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://platform.rescale.com/saml2/<company id>/acs/`

1. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://platform.rescale.com/saml2/<company id>/sso/`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Lépjen kapcsolatba [a ScaleX Enterprise Client támogatási csapatával,](https://info.rescale.com/contact_sales) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. A ScaleX Enterprise alkalmazás elvárja az SAML-állításokat egy adott formátumban, amely megköveteli, hogy egyéni attribútum-hozzárendelések hozzáadása az SAML token attribútumok konfigurációjának. Az alábbi képernyőképen látható az alapértelmezett attribútumok listája, ahol az **e-mail cím** a **user.mail**. A ScaleX Enterprise alkalmazás azt várja, hogy **az e-mail cím** le legyen képezve **a user.userprincipalname-nel,** ezért az attribútumleképezést a **Szerkesztés** ikonra kattintva kell módosítania, és módosítania kell az attribútumleképezést.

    ![image](common/edit-attribute.png)

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában keresse meg a **Tanúsítvány (Base64)** lehetőséget, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **ScaleX Enterprise beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t a ScaleX Enterprise-hoz való hozzáférés biztosításával.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **ScaleX Enterprise**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-scalex-enterprise-sso"></a>ScaleX vállalati sso konfigurálása

1. A ScaleX Enterprise konfigurációjának automatizálásához telepítenie kell a **My Apps Secure Sign-in böngészőbővítményt** **a Bővítmény telepítése**gombra kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

1. Miután hozzátette a bővítményt a böngészőhöz, kattintson a **ScaleX Enterprise beállítása** gombra, amely a ScaleX Enterprise alkalmazáshoz irányítja. Innen adja meg a rendszergazdai hitelesítő adatokat a ScaleX Enterprise-ba való bejelentkezéshez. A böngésző bővítmény automatikusan konfigurálja az alkalmazást, és automatizálja a 3-6.

    ![Beállítási konfiguráció](common/setup-sso.png)

1. Ha manuálisan szeretné beállítani a ScaleX Enterprise vállalatot, nyisson meg egy új böngészőablakot, és jelentkezzen be rendszergazdaként a ScaleX Enterprise vállalati webhelyére, és hajtsa végre a következő lépéseket:

1. Kattintson a jobb felső sarokban található menüre, és válassza a **Contoso Administration**parancsot.

    > [!NOTE]
    > Contoso csak egy példa. Ez legyen a tényleges cégneve.

    ![Egyszeri bejelentkezés konfigurálása](./media/scalex-enterprise-tutorial/Test_Admin.png)

1. Válassza a felső menü **Integrációk parancsát,** majd **válassza az egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezés konfigurálása](./media/scalex-enterprise-tutorial/admin_sso.png) 

1. Töltse ki az űrlapot az alábbiak szerint:

    ![Egyszeri bejelentkezés konfigurálása](./media/scalex-enterprise-tutorial/scalex_admin_save.png)

    a. Válassza **a Bármely felhasználó létrehozása lehetőséget, aki hitelesíthető az SSO-val**

    b. **Szolgáltató saml**: Az ***urna érték beillesztése:oázis:nevek:tc:SAML:2.0:nameid-format:persistent***

    c. **Az Identitásszolgáltató e-mail mezőjének neve az ACS-válaszban**: Az érték beillesztése`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. **Identitásszolgáltató entitásleíró entitásazonosítója:** Illessze be az **Azure AD-azonosító** értékét az Azure Portalról másolt.

    e. **Egyetlen identitásszolgáltató URL-címe:** Illessze be a **bejelentkezési URL-címet** az Azure Portalról.

    f. **Személyazonosság-szolgáltató nyilvános X509 tanúsítványa:** Nyissa meg az Azure-ból letöltött X509-tanúsítványt a jegyzettömbben, és illessze be a tartalmát ebbe a mezőbe. Győződjön meg arról, hogy nincsenek sortörések a tanúsítvány tartalmának közepén.

    g. Jelölje be a következő jelölőnégyzeteket: **Engedélyezve, Titkosítsa a NameID azonosítót és írja alá az AuthnRequests-t.**

    h. A beállítások mentéséhez kattintson az **SSO-beállítások frissítése** gombra.

### <a name="create-scalex-enterprise-test-user"></a>ScaleX Enterprise tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezhessenek a ScaleX Enterprise-ra, ki kell építeni őket a ScaleX Enterprise-ba. A ScaleX Enterprise esetében a kiépítés automatikus feladat, és nincs szükség manuális lépésekre. Minden felhasználó, aki sikeresen hitelesíti az SSO hitelesítő adatokautomatikusan kilesz építve a ScaleX oldalon.

## <a name="test-sso"></a>SSO tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a ScaleX Enterprise csempére kattint, automatikusan be kell jelentkeznie a ScaleX Enterprise-ba, amelyhez beállítja az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a ScaleX Enterprise-t az Azure AD-vel](https://aad.portal.azure.com/)
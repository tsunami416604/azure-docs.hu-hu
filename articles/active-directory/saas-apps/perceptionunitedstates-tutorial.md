---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az Perception United States (Non-UltiPro) szolgáltatással | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Perception United States (Nem UltiPro) között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b4a8f026-cb5f-41eb-9680-68eddc33565e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: e9ba42f780c93486409077383750d0635637e99b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67094835"
---
# <a name="tutorial-azure-active-directory-integration-with-perception-united-states-non-ultipro"></a>Oktatóanyag: Az Azure Active Directory integrációja az Perception United States (Non-UltiPro) rendszerrel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az Észlelés I. Államok (Nem UltiPro) integrálását az Azure Active Directoryval (Azure AD).
Az Észlelés Egyesült Államok (Non-UltiPro) integrálása az Azure AD-vel a következő előnyökkel jár:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Perception United States (Non-UltiPro).
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve perception Egyesült Államok (Non-UltiPro) (Single Sign-On) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a Perception United States (Non-UltiPro) alkalmazáshoz a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* Perception Egyesült Államok (Non-UltiPro) egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* Perception Egyesült Államok (Non-UltiPro) támogatja **az IDP** által kezdeményezett Egyszeri

## <a name="adding-perception-united-states-non-ultipro-from-the-gallery"></a>Az Perception United States (Non-UltiPro) hozzáadása a galériából

Az Perception United States (Non-UltiPro) Azure AD-be való integrációjának konfigurálásához hozzá kell adnia az Perception United States (Non-UltiPro) alkalmazást a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha hozzá szeretné adni az Perception United States (Non-UltiPro) képet a galériából, hajtsa végre a következő lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **Perception United States (Non-UltiPro)** kifejezést, válassza az **Észlelés Egyesült Államok (Nem UltiPro)** lehetőséget az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Perception Egyesült Államok (Non-UltiPro) az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését az Perception United States (Non-UltiPro) szolgáltatással egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, az Azure AD-felhasználó és a kapcsolódó felhasználó között a Perception United States (Nem UltiPro) közötti kapcsolat létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez az Észlelés Egyesült Államok (Nem UltiPro) segítségével a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Állítsa be a Perception United States (Non-UltiPro) Single Sign-On](#configure-perception-united-states-non-ultipro-single-sign-on)** beállítást – az alkalmazás oldalon az egyszeri bejelentkezés beállításainak konfigurálásához.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre Perception United States (Non-UltiPro) tesztfelhasználót](#create-perception-united-states-non-ultipro-test-user)** – hogy a Britta Simon in Perception United States (Non-UltiPro) megfelelője, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához a Perception United States (Non-UltiPro) szolgáltatással hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **Perception United States (Non-UltiPro) alkalmazásintegrációs** lapon válassza az Egyszeri **bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon hajtsa végre az alábbi lépéseket:

    ![Perception Egyesült Államok (Non-UltiPro) Domain és URL-ek egyszeri bejelentkezési információk](common/idp-intiated.png)

    a. Az **Azonosító** mezőbe írjon be egy URL-címet:`https://perception.kanjoya.com/sp`

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://perception.kanjoya.com/sso?idp=<entity_id>`

    c. A **Perception United States (Non-UltiPro)** alkalmazás megköveteli az Azure **AD-azonosító** értéke, mint <entity_id>, amelyet kap a **Set up Perception Egyesült Államok (Nem UltiPro)** szakasz, uri kódolt. Az uri kódolású érték benyomásához használja **http://www.url-encode-decode.com/** az alábbi hivatkozást: .

    d. Után szerzés a uri kódolt érték összeköt ez -val a **Válasz URL** mint említett alul-

    `https://perception.kanjoya.com/sso?idp=<URI encooded entity_id>`
    
    e. Illessze be a fenti értéket a **Válasz URL-cím** mezőbe.

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány csoportjában** kattintson a **Letöltés** gombra, ha letöltheti az **összevonási metaadat-XML-t** a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. A **Set up Perception United States (Non-UltiPro) (Beállítás: Észlelés I. Államok ( Non-UltiPro)** című szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe   

### <a name="configure-perception-united-states-non-ultipro-single-sign-on"></a>Az Észlelés Egyesült Államok (Nem UltiPro) egyszeri bejelentkezéskonfigurálása

1. Egy másik böngészőablakban jelentkezzen be az Perception United States (Non-UltiPro) vállalati webhelyére rendszergazdaként.

2. A fő eszköztáron kattintson a **Fiókbeállítások gombra.**

    ![Perception Egyesült Államok (Non-UltiPro) felhasználó](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_user.png)

3. A **Fiókbeállítások** lapon hajtsa végre az alábbi lépéseket:

    ![Perception Egyesült Államok (Non-UltiPro) felhasználó](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_account.png)

    a. A **Vállalat neve** mezőbe írja be a **Vállalat**nevét.
    
    b. A **Fióknév** mezőbe írja be a **Fiók**nevét.

    c. Az **Alapértelmezett válaszküldéses e-mail** mezőbe írja be az érvényes **e-mailt.**

    d. Válassza az **SSO-identitásszolgáltatót** **SAML 2.0**néven.

4. Az **SSO-konfiguráció** lapon hajtsa végre a következő lépéseket:

    ![Perception Egyesült Államok (Non-UltiPro) SSOConfig](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_ssoconfig.png)

    a. Válassza **az SAML NameID típust** **e-mailként.**

    b. Az **SSO konfigurációneve** mezőbe írja be a **konfiguráció**nevét.
    
    c. Az **Identitásszolgáltató neve** szövegmezőbe illessze be az **Azure AD-azonosító**értékét, amelyet az Azure Portalról másolt. 

    d. Az **SAML tartomány mezőbe**írja @contoso.combe a tartományt a hoz hasonlóan.

    e. A **Metaadat XML-fájl** feltöltéséhez kattintson a **Feltöltés gombra.**

    f. Kattintson a **Frissítés** parancsra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása 

Ez a szakasz célja, hogy hozzon létre egy tesztfelhasználót az Azure Portalon Britta Simon.

1. Az Azure Portalon a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd az **Összes felhasználó**lehetőséget.

    ![A "Felhasználók és csoportok" és a "Minden felhasználó" linkek](common/users.png)

2. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A Felhasználó tulajdonságokban hajtsa végre a következő lépéseket.

    ![A Felhasználó párbeszédpanel](common/user-properties.png)

    a. A **Név** mezőbe írja be **a BrittaSimon**értéket.
  
    b. A **Felhasználónév** mező brittasimon@yourcompanydomain.extensiontípusa mezőben. Például: BrittaSimon@contoso.com

    c. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket.

    d. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés t engedve a Perception United States (Non-UltiPro) hozzáférést biztosít.

1. Az Azure portalon válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza az **Észlelés I. Államok (Nem UltiPro) lehetőséget.**

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza az **Észlelés Egyesült Államok (Nem-UltiPro) lehetőséget.**

    ![Az Perception United States (Non-UltiPro) hivatkozás a Jelentkezések listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-perception-united-states-non-ultipro-test-user"></a>Create Perception Egyesült Államok (Non-UltiPro) teszt felhasználó

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre az Észlelés Egyesült Államokban (Nem UltiPro). Együttműködve [Perception United States (Non-UltiPro) támogatási csapatával,](https://www.ultimatesoftware.com/Contact/ContactUs) hogy hozzáadja a felhasználókat az Perception United States (Non-UltiPro) platformon.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha a Hozzáférési panelen az Észlelés Az Egyesült Államok (Nem UltiPro) csempére kattint, automatikusan be kell jelentkeznie az Észlelés Magyarország (Nem UltiPro) rendszerbe, amelyhez beállította az Egyszeri bejelentkezés t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


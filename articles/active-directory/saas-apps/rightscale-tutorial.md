---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Rightscale szolgáltatással | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Rightscale között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3a8d376d-95fb-4dd7-832a-4fdd4dd7c87c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 53799b62da043b7680f010e1eaaf0d9243f07dd5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67093075"
---
# <a name="tutorial-azure-active-directory-integration-with-rightscale"></a>Oktatóanyag: Az Azure Active Directory integrációja a Rightscale szolgáltatással

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Rightscale-t az Azure Active Directoryval (Azure AD).
A Rightscale integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a Rightscale-hez.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve a Rightscale (Single Sign-On) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció beállítása a Rightscale, szüksége van a következő elemeket:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) kaphat
* Jogskálás egyszeri bejelentkezéssel rendelkező előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A Rightscale támogatja az **SP-t és az IDP** által kezdeményezett SSO-t

## <a name="adding-rightscale-from-the-gallery"></a>Jobb skála hozzáadása a galériából

A Rightscale azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Rightscale-et a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha rightscale-et szeretne hozzáadni a galériából, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a Rightscale ( Jobb skála ) **parancsot,** válassza a **Jobb skála** lehetőséget az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Jobb skála az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezést a Rightscale szolgáltatással egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, az Azure AD-felhasználó és a kapcsolódó felhasználó rightscale közötti kapcsolat létrehozásához.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a Rightscale szolgáltatással a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. Állítsa be a **[rightscale Single Sign-On -konfigurálásához](#configure-rightscale-single-sign-on)** az egyszeri bejelentkezés beállításait az alkalmazás oldalán.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre rightscale teszt felhasználó](#create-rightscale-test-user)** - egy megfelelője Britta Simon a Rightscale, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének a Rightscale szolgáltatással való konfigurálásához hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **Megfelelő szintű** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban a felhasználónak nem kell végrehajtania semmilyen lépést, mivel az alkalmazás már előre integrálva van az Azure-ral.

    ![A jobb skálatartomány és az URL-címek egyszeri bejelentkezési adatai](common/preintegrated.png)

5. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    ![A jobb skálatartomány és az URL-címek egyszeri bejelentkezési adatai](common/metadata-upload-additional-signon.png)

    A **Bejelentkezési URL-cím** mezőbe írja be az URL-címet:`https://login.rightscale.com/`

6. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

7. A **Rightscale beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-rightscale-single-sign-on"></a>A jobb oldali egyszeri bejelentkezés konfigurálása

1. Az alkalmazáshoz konfigurált Egyszeri bejelentkezés hez rendszergazdaként kell bejelentkeznie a RightScale-bérlőbe.

2. A felső menüben kattintson a **Beállítások** fülre, és válassza **az Egyszeri bejelentkezés parancsot**.

    ![Egyszeri bejelentkezés konfigurálása](./media/rightscale-tutorial/tutorial_rightscale_001.png)

3. Kattintson az **új** gombra **az SAML-identitásszolgáltatók hozzáadásához.**

    ![Egyszeri bejelentkezés konfigurálása](./media/rightscale-tutorial/tutorial_rightscale_002.png)

4. A **Megjelenítendő név**mezőbe írja be a vállalat nevét.

    ![Egyszeri bejelentkezés konfigurálása](./media/rightscale-tutorial/tutorial_rightscale_003.png)

5. Válassza **a RightScale által kezdeményezett egyszeri bejelentkezés engedélyezése felderítési emlékeztető vel lehetőséget,** és adja meg a **tartománynevét** az alábbi szövegmezőbe.

    ![Egyszeri bejelentkezés konfigurálása](./media/rightscale-tutorial/tutorial_rightscale_004.png)

6. Illessze be a **bejelentkezési URL-cím** értékét, amelyet az Azure Portalról másolt **a RightScale SAML SSO-végpontjába.**

    ![Egyszeri bejelentkezés konfigurálása](./media/rightscale-tutorial/tutorial_rightscale_006.png)

7. Illessze be az **Azure AD-azonosító** értékét, amelyet az Azure Portalról másolt a **RightScale SAML EntityID-be.**

    ![Egyszeri bejelentkezés konfigurálása](./media/rightscale-tutorial/tutorial_rightscale_008.png)

8. Kattintson a **Böngésző** gombra az Azure Portalról letöltött tanúsítvány feltöltéséhez.


    ![Egyszeri bejelentkezés konfigurálása](./media/rightscale-tutorial/tutorial_rightscale_009.png)

9. Kattintson a **Mentés** gombra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ez a szakasz célja, hogy hozzon létre egy tesztfelhasználót az Azure Portalon Britta Simon.

1. Az Azure Portalon a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd az **Összes felhasználó**lehetőséget.

    ![A "Felhasználók és csoportok" és a "Minden felhasználó" linkek](common/users.png)

2. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A Felhasználó tulajdonságokban hajtsa végre a következő lépéseket.

    ![A Felhasználó párbeszédpanel](common/user-properties.png)

    a. A **Név** mezőbe írja be **a BrittaSimon**értéket.
  
    b. A **Felhasználónév** mezőtípusban`brittasimon@yourcompanydomain.extension`  
    Például: BrittaSimon@contoso.com

    c. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket.

    d. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés t a Rightscale hozzáférést biztosítva.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza a **Rightscale**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Rightscale lehetőséget.**

    ![A Jobb skála hivatkozás az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-rightscale-test-user"></a>Megfelelő árnyalatú tesztfelhasználó létrehozása

Ebben a szakaszban hozzon létre egy felhasználó nevű Britta Simon a Rightscale. Együttműködve [Rightscale ügyfél támogatási csapat](mailto:support@rightscale.com) a felhasználók hozzáadása a Jobb skálán platformon. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a hozzáférési panelen a Jobb skála csempére kattint, automatikusan be kell jelentkeznie arra a Jobb iktahelyre, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Clear Review programmal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Clear Review között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8264159a-11a2-4a8c-8285-4efea0adac8c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f2a0560163f9806053f49944cbec0db2b1a9de8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67105458"
---
# <a name="tutorial-azure-active-directory-integration-with-clear-review"></a>Oktatóanyag: Az Azure Active Directory integrációja a Clear Review szolgáltatással

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Clear Review-t az Azure Active Directoryval (Azure AD).
A Clear Review with Azure AD integrálása a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a Clear Review.You can control in Azure AD who has access to Clear Review.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve clear review (Single Sign-On) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció clear review-val való konfigurálásához a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) kaphat
* Egyszeri bejelentkezéssel rendelkező előfizetés áttekintésének törlése

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A Clear Review támogatja az **SP és az IDP** által kezdeményezett sso-t

## <a name="adding-clear-review-from-the-gallery"></a>Ellenőrzés törlése a galériából

A Clear Review integrálása az Azure AD-be, hozzá kell adnia a katalógusból a tiszta ellenőrzés a felügyelt SaaS-alkalmazások listájához.

**Ha a katalógusból szeretné hozzáadni a Vélemény és ellenőrzés törlése funkciót, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Véleményezés törlése parancsot,** válassza a **Véleményezés törlése** elemet az eredménypanelről, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Ellenőrzés törlése az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a Clear Review segítségével egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, az Azure AD-felhasználó és a kapcsolódó felhasználó közötti kapcsolat clear review létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a Clear Review segítségével a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja](#configure-clear-review-single-sign-on)** az egyszeri bejelentkezés törlése beállítást – az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre clear review teszt felhasználó](#create-clear-review-test-user)** - egy megfelelője Britta Simon a Clear Review, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének a Clear Review használatával való konfigurálásához hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **Clear Review** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban, ha az alkalmazást **IDP** által kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    ![A tartomány és az URL-címek egyszeri bejelentkezési adatainak törlése](common/idp-intiated.png)

    a. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<customer name>.clearreview.com/sso/metadata/`

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://<customer name>.clearreview.com/sso/acs/`

5. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    ![A tartomány és az URL-címek egyszeri bejelentkezési adatainak törlése](common/metadata-upload-additional-signon.png)

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<customer name>.clearreview.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Lépjen kapcsolatba [az ügyfél támogatási csapatának ellenőrzésével,](https://clearreview.com/contact/) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

6. A Clear Review alkalmazás az SAML-állításokat egy adott formátumban várja, amely megköveteli, hogy egyéni attribútumleképezéseket adjon hozzá az SAML token attribútumok konfigurációjához. A következő képernyőképen az alapértelmezett attribútumok listája látható, ahol a **nameidentifier** a **user.userprincipalname**. A Clear Review alkalmazás azt várja, hogy a **névazonosító** le legyen képezve a **user.mail alkalmazással,** ezért az attribútumleképezést a **Szerkesztés** ikonra kattintva és az attribútumleképezés módosításával kell módosítania.

    ![image](common/edit-attribute.png)

7. A **Felhasználói attribútumok & Jogcímek** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    a. Kattintson a **Névazonosító érték**jobb oldalán található **Szerkesztés ikonra.**

    ![image](./media/clearreview-tutorial/attribute02.png)

    ![image](./media/clearreview-tutorial/attribute01.png)

    b. A **Forrás attribútumlistában** válassza ki a **sor user.mail** attribútumértékét.

    c. Kattintson a **Mentés** gombra.

8. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

9. A **Véleményezés beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-clear-review-single-sign-on"></a>Egyszeri bejelentkezés egyértelmű ellenőrzéskonfigurálása

1. Az egyszeri bejelentkezés konfigurálásához a **Clear Review** oldalon nyissa meg a **Törlés portált** rendszergazdai hitelesítő adatokkal.

2. Válassza a **Rendszergazda** lehetőséget a bal oldali navigációs sávon.

    ![Egyszeri bejelentkezés – mentés konfigurálása gomb](./media/clearreview-tutorial/tutorial_clearreview_app_admin1.png)

3. A lap alján található **Integrációk** szakaszban kattintson a **Módosítás** gombra az **Egyszeri bejelentkezés beállításaitól jobbra.**

    ![Egyszeri bejelentkezés – mentés konfigurálása gomb](./media/clearreview-tutorial/tutorial_clearreview_app_admin2.png)

4. A következő lépések végrehajtása az **Egyszeri bejelentkezés beállításai** lapon

    ![Egyszeri bejelentkezés – mentés konfigurálása gomb](./media/clearreview-tutorial/tutorial_clearreview_app_admin3.png)

    a. A **Kiállító URL-címmezőjébe** illessze be az **Azure AD-azonosító** értékét, amelyet az Azure Portalról másolt.

    b. Az **SAML-végpont** szövegdobozába illessze be az Azure Portalról másolt **bejelentkezési URL-cím** értékét.  

    c. Az **SLO-végpont** szövegdobozába illessze be a **kijelentkezési URL-cím** értékét, amelyet az Azure Portalról másolt.  

    d. Nyissa meg a letöltött tanúsítványt a jegyzettömbben, és illessze be a tartalmat az **X.509 Tanúsítvány** szövegmezőbe.   

    e. Kattintson a **Mentés** gombra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása 

Ez a szakasz célja, hogy hozzon létre egy tesztfelhasználót az Azure Portalon Britta Simon.

1. Az Azure Portalon a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd az **Összes felhasználó**lehetőséget.

    ![A "Felhasználók és csoportok" és a "Minden felhasználó" linkek](common/users.png)

2. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A Felhasználó tulajdonságokban hajtsa végre a következő lépéseket.

    ![A Felhasználó párbeszédpanel](common/user-properties.png)

    a. A **Név** mezőbe írja be **a BrittaSimon**értéket.
  
    b. A **Felhasználónév** mezőbe írja be **a\@brittasimon yourcompanydomain.extension típusú felhasználónév mezőt.**  
    Például: BrittaSimon@contoso.com

    c. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket.

    d. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés t azáltal, hogy hozzáférést biztosít a Clear Review.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, válassza a **Minden alkalmazás**lehetőséget, majd a Véleményezés **törlése**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Véleményezés törlése**lehetőséget.

    ![Az Alkalmazások listában található Véleményezés törlése hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-clear-review-test-user"></a>Ellenőrzés törlése tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a Clear Review-ban. A [Clear Review támogatási csapatával](https://clearreview.com/contact/) együttműködve vegye fel a felhasználókat a Clear Review platformra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panel en a Véleményezés törlése csempére kattint, automatikusan be kell jelentkeznie a Törlés ellenőrzésbe, amelyhez az SSO-t állította be. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


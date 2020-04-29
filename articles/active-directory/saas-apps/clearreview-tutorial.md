---
title: 'Oktatóanyag: Azure Active Directory integráció az egyértelmű áttekintéssel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és törölje a jelet a felülvizsgálat.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "67105458"
---
# <a name="tutorial-azure-active-directory-integration-with-clear-review"></a>Oktatóanyag: Azure Active Directory integráció egyértelmű áttekintéssel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhat egyértelmű áttekintést Azure Active Directory (Azure AD) használatával.
Az Azure AD-val való egyértelmű felülvizsgálat integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a törlési ellenőrzéshez.
* Engedélyezheti a felhasználók számára, hogy automatikusan bejelentkezzenek, hogy az Azure AD-fiókjával törölje az ellenőrzést (egyszeri bejelentkezés).
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció egyértelmű felülvizsgálattal való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) szerezhet be
* Egyszeri bejelentkezést engedélyező előfizetés törlése

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Clear Review **az SP és a identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést támogatja

## <a name="adding-clear-review-from-the-gallery"></a>Egyértelmű áttekintés hozzáadása a katalógusból

Az egyértelmű áttekintés az Azure AD-be való integrálásának konfigurálásához egyértelmű áttekintést kell adnia a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Az alábbi lépések végrehajtásával adhat egyértelmű áttekintést a katalógusból:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Clear Review**kifejezést, majd az alkalmazás hozzáadásához válassza az **Áttekintés** az eredményekből elemet, majd kattintson a **Hozzáadás** gombra.

     ![Áttekintés törlése az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés konfigurálását és tesztelését a **Britta Simon**nevű tesztelési felhasználó alapján, egyértelmű felülvizsgálattal végezheti el.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a kapcsolódó felhasználó közötti kapcsolat törlését kell létrehozni.

Az Azure AD egyszeri bejelentkezés az egyértelmű áttekintéssel való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. Az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán a **[Clear Review szolgáltatás egyszeri bejelentkezésének konfigurálásával](#configure-clear-review-single-sign-on)** .
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Törlési teszt felhasználó törlése](#create-clear-review-test-user)** – a Britta Simon-nek a felhasználó Azure ad-képviseletéhez kapcsolódó egyértelmű áttekintése.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Ha az Azure AD egyszeri bejelentkezését egyértelmű áttekintéssel szeretné konfigurálni, hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)az alkalmazás-integráció **törlése** lapon válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    ![A tartomány és az URL-címek egyszeri bejelentkezési adatainak törlése](common/idp-intiated.png)

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<customer name>.clearreview.com/sso/metadata/`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<customer name>.clearreview.com/sso/acs/`

5. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    ![A tartomány és az URL-címek egyszeri bejelentkezési adatainak törlése](common/metadata-upload-additional-signon.png)

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<customer name>.clearreview.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Az értékek lekéréséhez forduljon az [ügyfél-támogatási csoport egyértelmű áttekintése című csoporthoz](https://clearreview.com/contact/) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

6. Az ellenőrző alkalmazás törlése az SAML-jogkivonatokat egy adott formátumban várja, amelyhez egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőfelvételen az alapértelmezett attribútumok listája látható, ahol a **NameIdentifier** a **User. userPrincipalName**leképezéssel van leképezve. Az alkalmazás törlése: a **NameIdentifier** a **User. mail**használatával kell leképezni, ezért az attribútum-hozzárendelést úgy kell módosítania, hogy a **Szerkesztés** ikonra kattint, és megváltoztatja az attribútumok leképezését.

    ![image](common/edit-attribute.png)

7. A **felhasználói attribútumok & jogcímek** párbeszédpanelen hajtsa végre a következő lépéseket:

    a. Kattintson a **név azonosító értékének**jobb oldalán található **Szerkesztés ikonra** .

    ![image](./media/clearreview-tutorial/attribute02.png)

    ![image](./media/clearreview-tutorial/attribute01.png)

    b. A **forrás attribútum** listából válassza ki az adott sorhoz tartozó **User. mail** attribútum értékét.

    c. Kattintson a **Save** (Mentés) gombra.

8. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

9. A **törlési ellenőrzés beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-clear-review-single-sign-on"></a>Az egyszeri bejelentkezés törlésének beállítása

1. Ha az egyszeri bejelentkezést az **egyértelmű felülvizsgálati** oldalon szeretné beállítani, nyissa meg a **törlési** portált rendszergazdai hitelesítő adatokkal.

2. A bal oldali navigációs sávon válassza a **rendszergazda** elemet.

    ![Egyszeri bejelentkezéses mentés beállítása gomb](./media/clearreview-tutorial/tutorial_clearreview_app_admin1.png)

3. Az oldal alján található **integrációk** szakaszban kattintson az **egyszeri bejelentkezés beállításaitól**jobbra található **módosítás** gombra.

    ![Egyszeri bejelentkezéses mentés beállítása gomb](./media/clearreview-tutorial/tutorial_clearreview_app_admin2.png)

4. Hajtsa végre az alábbi lépéseket az **egyszeri bejelentkezési beállítások** oldalon

    ![Egyszeri bejelentkezéses mentés beállítása gomb](./media/clearreview-tutorial/tutorial_clearreview_app_admin3.png)

    a. A **kiállító URL-címe** szövegmezőbe illessze be a Azure Portalból másolt **Azure ad-azonosító** értékét.

    b. Az **SAML-végpont** szövegmezőben illessze be a **bejelentkezési URL-cím** értékét, amelyet a Azure Portal másolt.  

    c. Az **slo-végpont** szövegmezőbe illessze be a **kijelentkezési URL-cím** értékét, amelyet a Azure Portal másolt.  

    d. Nyissa meg a letöltött tanúsítványt a Jegyzettömbben, és illessze be a tartalmat az **X. 509 tanúsítvány** szövegmezőbe.   

    e. Kattintson a **Save** (Mentés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása 

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságainál végezze el a következő lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A Felhasználónév mezőbe írja be a **brittasimon\@yourcompanydomain. Extension** **nevet**  
    Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Létrehozás**gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít a törlési ellenőrzéshez.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **felülvizsgálat törlése**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza az **Áttekintés törlése**elemet.

    ![Az alkalmazások listájának Clear Review (áttekintés) hivatkozása](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-clear-review-test-user"></a>Felhasználó törlése – ellenőrzés tesztelése

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre az egyértelmű felülvizsgálatban. Az egyértelmű felülvizsgálati platform segítségével vegye fel a felhasználókat a [Clear Review támogatási csapatával](https://clearreview.com/contact/) .

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a törlési áttekintés csempére kattint, automatikusan be kell jelentkeznie arra a törlési felülvizsgálatra, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


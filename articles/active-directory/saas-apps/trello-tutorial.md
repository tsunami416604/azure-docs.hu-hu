---
title: 'Oktatóanyag: Az Azure Active Directory-integráció, a Trello |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Trello között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: cd5ae365-9ed6-43a6-920b-f7814b993949
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 453827b42d12333fd4f27761e7f73484fb749532
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2019
ms.locfileid: "59578424"
---
# <a name="tutorial-azure-active-directory-integration-with-trello"></a>Oktatóanyag: Trello az Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja, hogyan Trello integrálása az Azure Active Directory (Azure AD).
Trello integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá a Trello Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezve Trello (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen kezelheti: az Azure Portalon.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Trello, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a [egy havi próbalehetőség](https://azure.microsoft.com/pricing/free-trial/).
* Trello single-sign-,-engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Trello támogatja az SP - és IDP-által kezdeményezett egyszeri bejelentkezés

* Trello támogatja az igény szerinti felhasználók átadása

## <a name="add-trello-from-the-gallery"></a>Trello hozzáadása a katalógusból

Az Azure AD integrálása a Trello konfigurálásához először hozzáadása Trello a katalógusból a felügyelt SaaS-alkalmazások listájában.

Trello hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com), a bal oldali panelen válassza ki a **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Válassza ki **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához válassza a **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Trello**, majd válassza ki **Trello** az eredmények ablaktáblán.

5. Válassza ki a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Trello a találatok listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés alapján nevű tesztfelhasználó Trello- **Britta Simon**.

Egyszeri bejelentkezés működjön, az Azure AD-felhasználót és a kapcsolódó felhasználó közötti kapcsolat létrehozása a trello alkalmazásban kell.

Az Azure AD egyszeri bejelentkezés a Trello tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. [Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on) ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. [Trello egyszeri bejelentkezés konfigurálása](#configure-trello-single-sign-on) az egyszeri bejelentkezési beállításainak konfigurálása az alkalmazás oldalán.
3. [Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user) az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. [Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user) Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. [Hozzon létre egy Trello-tesztfelhasználót](#create-a-trello-test-user) , amely kapcsolódik a felhasználó Azure ad-ben reprezentációja trello-megfelelője a Britta Simon rendelkeznie.
6. [Egyszeri bejelentkezés tesztelése](#test-single-sign-on) , hogy működik-e a konfiguráció ellenőrzéséhez.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

> [!NOTE]
> Meg kell kapnia a **\<vállalati\>** trello slug. Ha nem rendelkezik a slug érték, lépjen kapcsolatba a [Trello-támogatási csoport](mailto:support@trello.com) a slug lekérni az Ön vállalata számára.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Trello, tegye a következőket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **Trello** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válasszon **SAML** egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **beállítani az egyszeri bejelentkezés az SAML** lapon válassza ki a **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel bezárásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszra, ha szeretné konfigurálni az alkalmazás Identitásszolgáltató által kezdeményezett módban, az alábbi lépéseket:

    ![Trello-tartomány és URL-címeket egyetlen bejelentkezési adatait](common/idp-intiated.png)

    a. Az a **azonosító** mezőbe írjon be egy URL-címet a következő minta használatával: `https://trello.com/auth/saml/metadata`

    b. Az a **válasz URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával: `https://trello.com/auth/saml/consume/<enterprise>`

5. Válassza ki **további URL-címet beállítani**, majd tegye a következőket, ha az alkalmazás konfigurálása a Szolgáltató által kezdeményezett módban, és:

    ![Trello-tartomány és URL-címeket egyetlen bejelentkezési adatait](common/metadata-upload-additional-signon.png)

    Az a **bejelentkezési URL-** mezőbe írjon be egy URL-címet a következő minta használatával:  `https://trello.com/auth/saml/login/<enterprise>`

    > [!NOTE]
    > Ezek a értékei nem valódi. Az értékeket módosítsa a tényleges azonosítóval, válasz URL-cím és bejelentkezés URL-címe. Forduljon a [Trello-ügyfél-támogatási csapatának](mailto:support@trello.com) beolvasni ezeket az értékeket. Emellett tekintse meg a mintákat a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

6. A Trello-alkalmazás a SAML helyességi feltételek vár egy megadott formátumban. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a **felhasználói attribútumok** szakaszban az alkalmazás integrációs oldalán található. Az a **állítsa be egyszeri bejelentkezést az SAML** lapon válassza ki a **szerkesztése** gombra kattintva nyissa meg a **felhasználói attribútumok** párbeszédpanel bezárásához.

    ![Felhasználói attribútumok párbeszédpanel](common/edit-attribute.png)

7. Az a **felhasználói jogcímek** szakasz a **felhasználói attribútumok** párbeszédpanelen adja meg az SAML-jogkivonat attribútum az előző képen látható módon. Ezután az alábbi lépéseket:

    | Name (Név) |  Adatforrás-attribútum|
    | --- | --- |
    | User.Email | user.mail |
    | User.FirstName | user.givenname |
    | User.LastName | user.surname |

    a. Válassza ki **hozzáadása új jogcímet** megnyitásához a **kezelheti a felhasználói jogcímek** párbeszédpanel bezárásához.

    ![Felhasználói jogcímek párbeszédpanel](common/new-save-attribute.png)

    ![Felhasználói jogcímek kezelése](common/new-attribute-details.png)

    b. Az a **neve** mezőben megadhatja azon attribútum nevét, amely a sorhoz látható.

    c. Hagyja **Namespace** üres.

    d. A **forrás**válassza **attribútum**.

    e. Az a **forrásattribútum** listában, adja meg az attribútum értéke, amely a sorhoz látható.

    f. Kattintson az **OK** gombra.

    g. Kattintson a **Mentés** gombra.

8. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** szakaszban jelölje be **letöltése** letöltéséhez a **tanúsítvány (Base64)**  igény szerint a megadott lehetőségek közül. Mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

9. Az a **Trello beállítása** területén másolja a megfelelő URL-címe az igényeknek megfelelően.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Az Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-trello-single-sign-on"></a>Trello egyszeri bejelentkezés konfigurálása

Egyszeri bejelentkezés konfigurálásához a Trello-oldalon, először küldeni a letöltött **tanúsítvány (Base64)** másolt URL-címek az Azure Portalról, és a [Trello-támogatási csoport](mailto:support@trello.com). Akkor győződjön meg arról, hogy a SAML SSO-kapcsolat mindkét oldalán megfelelően beállítva.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Az új felhasználó gomb](common/new-user.png)

3. Az a **felhasználói** párbeszédpanel mezőbe az alábbi lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** írja be a következőt **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőben adja meg "brittasimon@yourcompanydomain.extension". Ebben az esetben, előfordulhat, hogy adja meg például "BrittaSimon@contoso.com".

    c. Válassza ki a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Trello Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**válassza **minden alkalmazás**, majd válassza ki **Trello**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Trello**.

    ![Az alkalmazások listáját a Trello hivatkozásra](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Válassza ki a **felhasználó hozzáadása** gombra. Ezt követően a a **hozzárendelés hozzáadása** párbeszédpanelen jelölje ki **felhasználók és csoportok**.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen jelölje ki **Britta Simon** a felhasználók listában. Kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha a SAML helyességi feltétel, majd, a szerepkör értéket várt a **Szerepkörválasztás** párbeszédpanelen jelölje ki a megfelelő szerepkört a felhasználóhoz a listából. Kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen válassza ki a **hozzárendelése** gombra.

### <a name="create-a-trello-test-user"></a>Trello tesztfelhasználó létrehozása

Ebben a szakaszban egy trello Britta Simon nevű felhasználó létrehozásához. Trello támogatja a csak a felhasználók létrehozásának időpontja, amely alapértelmezés szerint engedélyezve van. Nincs meg ebben a szakaszban a művelet elem. Ha a felhasználó még nem létezik a trello alkalmazásban, a hitelesítés után egy új jön létre.

> [!NOTE]
> Ha manuálisan hozzon létre egy felhasználót van szüksége, lépjen kapcsolatba a [Trello-támogatási csoport](mailto:support@trello.com).

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a MyApps portálról segítségével tesztelheti.

A MyApps portálról, a Trello-csempe kijelölésekor meg kell automatikusan megtörténik a trellóhoz. A saját alkalmazások portál kapcsolatos további információkért lásd: [Mi az a MyApps portálról?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


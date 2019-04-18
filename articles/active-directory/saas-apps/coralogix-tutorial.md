---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Coralogix |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Coralogix között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: ba79bfc1-992e-4924-b76a-8eb0dfb97724
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/2/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d0095a825f5582dc795f5bebdcf08be07a92946e
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678292"
---
# <a name="tutorial-azure-active-directory-integration-with-coralogix"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Coralogix

Ebben az oktatóanyagban elsajátíthatja, hogyan Coralogix integrálása az Azure Active Directory (Azure AD).
Coralogix integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá Coralogix Azure AD-ben.
* Engedélyezheti a felhasználók számára, hogy automatikusan jelentkezzenek be Coralogix (egyszeri bejelentkezés), az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen kezelheti: az Azure Portalon.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Coralogix az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a [egy havi próbalehetőség](https://azure.microsoft.com/pricing/free-trial/).
- Single-sign-on egy Coralogix előfizetés engedélyezve van. 

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Coralogix SP által kezdeményezett egyszeri Bejelentkezést támogatja.

## <a name="add-coralogix-from-the-gallery"></a>Coralogix hozzáadása a katalógusból

Először hozzá Coralogix a galériából a felügyelt SaaS-alkalmazások listájában konfigurálhatja Coralogix integrálása az Azure AD-ben.

A katalógusból Coralogix hozzáadásához tegye a következőket:

1. Az a [az Azure portal](https://portal.azure.com), a bal oldali panelen válassza ki a **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához válassza a **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Coralogix**. Válassza ki **Coralogix** az eredmények ablaktáblájában, és válassza ki a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Az eredmények listájában Coralogix](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Coralogix Britta Simon nevű tesztfelhasználó alapján.
Az egyszeri bejelentkezés működéséhez Azure AD-felhasználót és a kapcsolódó felhasználó közötti kapcsolatot létesít a Coralogix kell.

Az Azure AD egyszeri bejelentkezés az Coralogix tesztelése és konfigurálása, először hajtsa végre a következő építőelemeket:

1. [Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on) ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. [Coralogix egyszeri bejelentkezés konfigurálása](#configure-coralogix-single-sign-on) az egyszeri bejelentkezési beállításainak konfigurálása az alkalmazás oldalán.
3. [Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user) az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. [Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user) Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. [Hozzon létre egy Coralogix tesztfelhasználót](#create-a-coralogix-test-user) van egy megfelelője a Britta Simon Coralogix, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. [Egyszeri bejelentkezés tesztelése](#test-single-sign-on) , hogy működik-e a konfiguráció ellenőrzéséhez.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Coralogix, tegye a következőket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **Coralogix** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válasszon **SAML** egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** lapon válassza ki a **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel bezárásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** párbeszédpanel mezőbe az alábbi lépéseket:

    ![Coralogix tartomány és URL-címeket egyetlen bejelentkezési adatait](common/sp-identifier.png)

    a. Az a **bejelentkezési URL-cím** mezőbe írjon be egy URL-CÍMÉT a következő mintának: `https://<SUBDOMAIN>.coralogix.com`

    b. Az a **azonosító (entityid)** szöveg mezőbe írjon be egy URL-címet, például:
    
    `https://api.coralogix.com/saml/metadata.xml`

    vagy

    `https://aws-client-prod.coralogix.com/saml/metadata.xml` 

    > [!NOTE]
    > A bejelentkezési URL-érték nem valódi. Frissítse az értéket a tényleges bejelentkezési URL-CÍMÉT. Forduljon a [Coralogix ügyfél-támogatási csapatának](mailto:info@coralogix.com) a gépkulcsengedélyek értékének. Emellett tekintse meg a mintákat a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

5. A Coralogix alkalmazás a SAML helyességi feltételek vár egy megadott formátumban. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a **felhasználói attribútumok** szakaszban az alkalmazás integrációs oldalán található. Az a **állítsa be egyszeri bejelentkezést az SAML** lapon válassza ki a **szerkesztése** gombra kattintva nyissa meg a **felhasználói attribútumok** párbeszédpanel bezárásához.

    ![image](common/edit-attribute.png)

6. Az a **felhasználói jogcímek** című rész a **felhasználói attribútumok** párbeszédpanelen szerkessze a jogcímek használatával a **szerkesztése** ikonra. Azt is megteheti a jogcímek használatával **hozzáadása új jogcímet** konfigurálása az SAML-jogkivonat attribútum, az előző képen látható módon. Ezután az alábbi lépéseket:
    
    a. Válassza ki a **Szerkesztés ikon** megnyitásához a **kezelheti a felhasználói jogcímek** párbeszédpanel bezárásához.

    ![kép](./media/coralogix-tutorial/tutorial_usermail.png) ![kép](./media/coralogix-tutorial/tutorial_usermailedit.png)

    b. Az a **válasszon azonosító formátuma** listáról válassza ki **E-mail-cím**.

    c. Az a **forrásattribútum** listáról válassza ki **user.mail**.

    d. Kattintson a **Mentés** gombra.

7. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** szakaszban jelölje be **letöltése** letöltéséhez a **összevonási metaadatainak XML**  az igényeknek megfelelően a megadott lehetőségek közül. Mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

8. Az a **Coralogix beállítása** területén másolja a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-coralogix-single-sign-on"></a>Coralogix egyszeri bejelentkezés konfigurálása

Az egyszeri bejelentkezés konfigurálásához a **Coralogix** oldalán, a letöltött küldése **összevonási metaadatainak XML** másolt URL-címek az Azure Portalról, és a [Coralogix támogatási csapatának](mailto:info@coralogix.com). Akkor győződjön meg arról, hogy a SAML SSO-kapcsolat mindkét oldalán megfelelően beállítva.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki a képernyő tetején lévő **új felhasználó**.

    ![Új felhasználó gomb](common/new-user.png)

3. Az a **felhasználói** párbeszédpanel mezőbe az alábbi lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** írja be a következőt **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőben adja meg "brittasimon@yourcompanydomain.extension." Ebben az esetben, előfordulhat, hogy adja meg például "brittasimon@contoso.com."

    c. Válassza ki a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Coralogix Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**válassza **minden alkalmazás**, majd válassza ki **Coralogix**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Coralogix**.

    ![Az alkalmazások listáját a Coralogix hivatkozásra](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Válassza ki a **felhasználó hozzáadása** gombra. Válassza ki **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel bezárásához.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen jelölje ki **Britta Simon** a felhasználók listában. Kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha az a SAML-előfeltétel szerepkör értéket vár a **Szerepkörválasztás** párbeszédpanelen jelölje ki a megfelelő szerepkört a felhasználóhoz a listából. Kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen válassza ki a **hozzárendelése** gombra.

### <a name="create-a-coralogix-test-user"></a>Coralogix tesztfelhasználó létrehozása

Ebben a szakaszban egy felhasználói Britta Simon nevű Coralogix hoz létre. Együttműködik a [Coralogix támogatási csapatának](mailto:info@coralogix.com) a felhasználók hozzáadása az Coralogix platformon. Kell létrehozni, és aktiválja a felhasználók egyszeri bejelentkezés használata előtt.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a MyApps portálról segítségével tesztelheti.

A Coralogix csempe kiválasztásakor a MyApps portálról, érdemes lehet automatikusan bejelentkezett Coralogix. A MyApps portálról kapcsolatos további információkért lásd: [Mi az a MyApps portálról?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


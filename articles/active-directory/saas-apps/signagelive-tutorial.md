---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Signagelive |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Signagelive között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: d923f0e7-ad31-4d59-a6fd-f0e895e1a32d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36d4bb38d7a12edddac9d64ecc1ed3ee5a34456c
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2019
ms.locfileid: "59577812"
---
# <a name="tutorial-azure-active-directory-integration-with-signagelive"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Signagelive

Ebben az oktatóanyagban elsajátíthatja, hogyan Signagelive integrálása az Azure Active Directory (Azure AD).
Signagelive integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá Signagelive Azure AD-ben.
* Engedélyezheti a felhasználók számára, hogy automatikusan jelentkezzenek be Signagelive (egyszeri bejelentkezés), az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen kezelheti: az Azure Portalon.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis). Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Signagelive az Azure AD-integráció konfigurálásához a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a [egy havi próbalehetőség](https://azure.microsoft.com/pricing/free-trial/).
* Signagelive single-sign-,-engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Signagelive SP által kezdeményezett egyszeri Bejelentkezést támogatja.

## <a name="add-signagelive-from-the-gallery"></a>Signagelive hozzáadása a katalógusból

Először hozzá Signagelive a galériából a felügyelt SaaS-alkalmazások listájában konfigurálhatja Signagelive integrálása az Azure AD-ben.

A katalógusból Signagelive hozzáadásához tegye a következőket:

1. Az a [az Azure portal](https://portal.azure.com), a bal oldali panelen válassza ki a **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**, majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához válassza a **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Signagelive**. 

     ![Az eredmények listájában Signagelive](common/search-new-app.png)

5. Válassza ki **Signagelive** az eredmények ablaktáblájában, és válassza ki a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az Signagelive nevű tesztfelhasználó alapján **Britta Simon**.
Az egyszeri bejelentkezés működjön a Signagelive a kell létesítenie az Azure AD-felhasználót és a kapcsolódó felhasználó közötti kapcsolat.

Az Azure AD egyszeri bejelentkezés az Signagelive tesztelése és konfigurálása, először hajtsa végre a következő építőelemeket:

1. [Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on) ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. [Signagelive egyszeri bejelentkezés konfigurálása](#configure-signagelive-single-sign-on) az egyszeri bejelentkezési beállításainak konfigurálása az alkalmazás oldalán.
3. [Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user) az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. [Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user) Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. [Hozzon létre egy Signagelive tesztfelhasználót](#create-a-signagelive-test-user) egy megfelelője a Britta Simon Signagelive, amely a felhasználó Azure ad-ben reprezentációja van csatolva van.
6. [Egyszeri bejelentkezés tesztelése](#test-single-sign-on) , hogy működik-e a konfiguráció ellenőrzéséhez.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Signagelive, tegye a következőket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **Signagelive** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válasszon **SAML** egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **beállítani az egyszeri bejelentkezés az SAML** lapon jelölje be **szerkesztése** megnyitásához a **alapszintű SAML-konfigurációja** párbeszédpanel bezárásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** területén az alábbi lépéseket:

    ![Signagelive tartomány és URL-címeket egyetlen bejelentkezési adatait](common/sp-signonurl.png)

    Az a **bejelentkezési URL-** mezőbe írjon be egy URL-címet, amely használja a következő mintának:  `https://login.signagelive.com/sso/<ORGANIZATIONALUNITNAME>`

    > [!NOTE]
    > Az érték nem valódi. Frissítse az értéket a tényleges bejelentkezési URL-CÍMÉT. Az érték beszerzéséhez forduljon a [Signagelive ügyfél-támogatási csapatának](mailto:support@signagelive.com) . Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** szakaszban jelölje be **letöltése** letöltéséhez a **(Raw)tanúsítvány** száma az igényektől az adott közül. Mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozás](common/certificateraw.png)

6. Az a **Signagelive beállítása** területén másolja a szükséges URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-signagelive-single-sign-on"></a>Signagelive egyszeri bejelentkezés konfigurálása

Egyszeri bejelentkezés konfigurálása Signagelive oldalán, küldjön a letöltött **tanúsítvány (Raw)** másolt URL-címek az Azure Portalról, és a [Signagelive támogatási csapatával](mailto:support@signagelive.com). Akkor győződjön meg arról, hogy a SAML SSO-kapcsolat mindkét oldalán megfelelően beállítva.

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

    c. Válassza ki a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Signagelive Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**válassza **minden alkalmazás**, majd válassza ki **Signagelive**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Signagelive**.

    ![Az alkalmazások listáját a Signagelive hivatkozásra](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Válassza ki a **felhasználó hozzáadása** gombra. Ezt követően a a **hozzárendelés hozzáadása** párbeszédpanelen jelölje ki **felhasználók és csoportok**.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanel a **felhasználók** listáról válassza ki **Britta Simon**. Kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha a SAML helyességi feltétel, majd, a szerepkör értéket várt a **Szerepkörválasztás** párbeszédpanelen jelölje ki a megfelelő szerepkört a felhasználóhoz a listából. Ezután kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen válassza ki a **hozzárendelése** gombra.

### <a name="create-a-signagelive-test-user"></a>Signagelive tesztfelhasználó létrehozása

Ebben a szakaszban egy felhasználói Britta Simon nevű Signagelive hoz létre. Együttműködik a [Signagelive támogatási csapatának](mailto:support@signagelive.com) a felhasználók hozzáadása az Signagelive platformon. Kell létrehozni, és aktiválja a felhasználók egyszeri bejelentkezés használata előtt.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a MyApps portálról segítségével tesztelheti.

Amikor kiválasztja a **Signagelive** csempéje a MyApps portálról, meg kell hogy automatikusan jelentkezzenek be. A MyApps portálról kapcsolatos további információkért lásd: [Mi az a MyApps portálról?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


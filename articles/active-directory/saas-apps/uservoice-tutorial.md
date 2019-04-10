---
title: 'Oktatóanyag: Az Azure Active Directory-integráció a uservoice-on |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a UserVoice között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 684a405b-8932-46f6-b43a-4d97a42b6b87
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: dbd7189b1761a9ea88ce32dae3d7b45a88301ff6
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59278122"
---
# <a name="tutorial-azure-active-directory-integration-with-uservoice"></a>Oktatóanyag: Az Azure Active Directory-integráció a uservoice-on

Ebben az oktatóanyagban elsajátíthatja, hogyan UserVoice integrálása az Azure Active Directory (Azure AD).
UserVoice integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá a uservoice-on Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezett a uservoice-hoz (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a uservoice-on, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a [ingyenes fiókkal](https://azure.microsoft.com/free/)
* UserVoice egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja a UserVoice **SP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-uservoice-from-the-gallery"></a>UserVoice hozzáadása a katalógusból

Konfigurálhatja az Azure AD integrálása a uservoice-on, hozzá kell uservoice-on a galériából a felügyelt SaaS-alkalmazások listájára.

**UserVoice hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **UserVoice**, jelölje be **UserVoice** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Az eredmények listájában a uservoice-on](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a uservoice-on alapuló nevű tesztfelhasználó **Britta Simon**.
Egyszeri bejelentkezés működjön, az Azure AD-felhasználót és a kapcsolódó felhasználó uservoice hivatkozás kapcsolata kell hozható létre.

Az Azure AD egyszeri bejelentkezés a UserVoice tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[UserVoice egyszeri bejelentkezés konfigurálása](#configure-uservoice-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre UserVoice tesztfelhasználót](#create-uservoice-test-user)**  – van egy Britta Simon megfelelője a uservoice-on, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Konfigurálja az Azure AD egyszeri bejelentkezés a uservoice-on, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **UserVoice** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![UserVoice-tartomány és URL-címeket egyetlen bejelentkezési adatait](common/sp-identifier.png)

    a. Az a **bejelentkezési URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<tenantname>.UserVoice.com`

    b. Az a **azonosító (entityid)** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<tenantname>.UserVoice.com`

    > [!NOTE]
    > Ezek a értékei nem valódi. Frissítse a tényleges bejelentkezési URL-címet és azonosító ezeket az értékeket. Kapcsolattartó [UserVoice ügyfél-támogatási csapatának](https://www.uservoice.com/) beolvasni ezeket az értékeket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

5. Az a **SAML-aláíró tanúsítvány** területén kattintson **szerkesztése** gombra kattintva nyissa meg a **SAML-aláíró tanúsítvány** párbeszédpanel.

    ![SAML-aláíró tanúsítvány szerkesztése](common/edit-certificate.png)

6. Az a **SAML-aláíró tanúsítvány** területén másolja a **ujjlenyomat** , és mentse a számítógépre.

    ![Másolja ki az ujjlenyomat értéket](common/copy-thumbprint.png)

7. Az a **állítsa be a UserVoice** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-uservoice-single-sign-on"></a>Configure UserVoice Single Sign-On

1. Egy másik böngészőablakban jelentkezzen be a UserVoice vállalati hely rendszergazdaként.

2. A felső eszköztáron kattintson **beállítások**, majd válassza ki **webportál** a menüből.
   
    ![Beállítások szakaszban az alkalmazás oldalán](./media/uservoice-tutorial/ic777519.png "beállításai")

3. Az a **webes portálon** lap a **felhasználói hitelesítés** területén kattintson **szerkesztése** megnyitásához a **felhasználói hitelesítés szerkesztése** párbeszédpanel lap.
   
    ![Webes portál lapon](./media/uservoice-tutorial/ic777520.png "webes portálon")

4. Az a **felhasználói hitelesítés szerkesztése** párbeszédpanel lapon, a következő lépésekkel:
   
    ![Felhasználói hitelesítés szerkesztése](./media/uservoice-tutorial/ic777521.png "felhasználói hitelesítés szerkesztése")
   
    a. Kattintson a **egyszeri bejelentkezés (SSO)**.
 
    b. Illessze be a **bejelentkezési URL-cím** érték, amely az Azure Portalról történő másolta a **SSO távoli bejelentkezési** szövegmezőbe.

    c. Illessze be a **kijelentkezési URL-címe** érték, amely az Azure Portalról történő másolta a **SSO távoli kijelentkezéses szövegmező**.
 
    d. Illessze be a **ujjlenyomat** érték, amely az Azure Portalról másolta a **jelenlegi tanúsítvány SHA1-ujjlenyomatot** szövegmezőbe.
    
    e. Kattintson a **hitelesítési beállítások mentése**.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be brittasimon@yourcompanydomain.extension. Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon használandó Azure egyszeri bejelentkezés a uservoice-hoz a hozzáférés biztosításával.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **UserVoice**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **UserVoice**.

    ![A UserVoice-hivatkozás alkalmazásainak listájában](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-uservoice-test-user"></a>UserVoice tesztfelhasználó létrehozása

Ahhoz, hogy jelentkezzen be a uservoice-hoz az Azure AD-felhasználók, akkor ki kell építeni UserVoice-bA. Uservoice-on, esetén kiépítése a manuális feladat.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be a **UserVoice** bérlő.

2. Lépjen a **beállítások**.
   
    ![Beállítások](./media/uservoice-tutorial/ic777811.png "beállításai")

3. Kattintson a **általános**.

4. Kattintson a **az ügynökök és az engedélyek**.
   
    ![Az ügynökök és az engedélyek](./media/uservoice-tutorial/ic777812.png "az ügynökök és engedélyek")

5. Kattintson a **rendszergazdai**.
   
    ![Adja hozzá a rendszergazdák](./media/uservoice-tutorial/ic777813.png "rendszergazdák hozzáadása")

6. Az a **rendszergazdák meghívása** párbeszédpanelen hajtsa végre az alábbi lépéseket:
   
    ![A rendszergazdák meghívása](./media/uservoice-tutorial/ic777814.png "rendszergazdák meghívása")
   
    a. Az e-mailek szövegmezőbe írja be kíván üzembe helyezni, és kattintson a fiók e-mail-címe **Hozzáadás**.
   
    b. Kattintson a **meghívása**.

> [!NOTE]
> Bármely más UserVoice felhasználói fiók létrehozása eszközöket használhatja, vagy az aad-ben a felhasználói fiókok kiépítését UserVoice által biztosított API-k.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Kattintva a UserVoice-csempét, a hozzáférési panelen, meg kell lehet automatikusan bejelentkezett a uservoice-on, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


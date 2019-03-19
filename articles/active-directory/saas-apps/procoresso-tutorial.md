---
title: 'Oktatóanyag: Az Azure Active Directory-integráció Procore SSO-val |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Procore egyszeri bejelentkezés és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 9818edd3-48c0-411d-b05a-3ec805eafb2e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36e1c8d6cae79147c5cd4b5a46f5e1c330811ab8
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57877649"
---
# <a name="tutorial-azure-active-directory-integration-with-procore-sso"></a>Oktatóanyag: Az Azure Active Directory-integráció Procore SSO-val

Ebben az oktatóanyagban elsajátíthatja, hogyan Procore SSO integrálása az Azure Active Directory (Azure AD).

Procore SSO integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá Procore egyszeri bejelentkezés az Azure AD-ben.
- A felhasználók automatikusan első bejelentkezett Procore egyszeri bejelentkezés (egyszeri bejelentkezés), engedélyezheti az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása Procore SSO-val, a következőkre van szükség:

- Azure AD-előfizetés
- Egy Procore SSO egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Procore SSO hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-procore-sso-from-the-gallery"></a>Procore SSO hozzáadása a katalógusból

Procore SSO integrálása az Azure AD beállítása, hozzá kell Procore SSO a galériából a felügyelt SaaS-alkalmazások listájára.

**Procore SSO hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **Procore SSO**, jelölje be **Procore SSO** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában procore egyszeri bejelentkezés](./media/procoresso-tutorial/tutorial_procoresso_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban konfigurálni, és a teszt "Britta Simon" nevű felhasználó Procore SSO-val az Azure AD egyszeri bejelentkezés teszteléséhez.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Procore egyszeri bejelentkezés a mi egy felhasználó számára az Azure ad-ben. Más szóval Azure AD-felhasználót és a kapcsolódó felhasználó Procore egyszeri bejelentkezési hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés Procore SSO-val tesztelése és konfigurálása, kell hajtsa végre a következő építőelemeket:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Procore SSO tesztfelhasználó létrehozása](#creating-a-procore-sso-test-user)**  – egy megfelelője a Britta Simon Procore SSO, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
4. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Procore SSO-alkalmazását az egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálása Procore SSO-val, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Procore SSO** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen kattintson **kiválasztása** a **SAML** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés konfigurálása](common/tutorial_general_301.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](common/editconfigure.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, a felhasználónak nem kell végrehajthatja a lépéseket, ahogy az alkalmazás már előre integrálva van az Azure-ral.

    ![Procore SSO-tartomány és URL-címek egyszeri bejelentkezési adatait](./media/procoresso-tutorial/tutorial_procoresso_url.png)

5. Az a **SAML-aláíró tanúsítvány** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez **összevonási metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/procoresso-tutorial/tutorial_procoresso_certificate.png) 

6. Az a **Procore egyszeri bejelentkezés beállítása** területén másolja a megfelelő URL-címet a követelmény alapján.

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

    ![Procore egyszeri bejelentkezés konfigurálása](common/configuresection.png)

7. Az egyszeri bejelentkezés konfigurálása **Procore SSO** ügyféloldali, jelentkezzen be rendszergazdaként procore vállalat webhelye.

8. Az eszközkészlet legördülő lefelé, kattintson a **rendszergazdai** az egyszeri bejelentkezés beállításai lap megnyitásához.

    ![Egyszeri bejelentkezés konfigurálása](./media/procoresso-tutorial/procore_tool_admin.png)

9. Illessze be az értékeket a mezőkben, amint az alábbi-

    ![Egyszeri bejelentkezés konfigurálása](./media/procoresso-tutorial/procore_setting_admin.png)  

    a. Az a **egyszeri bejelentkezést a kibocsátó URL-cím** szöveg mezőbe illessze be az értékét **az Azure AD-azonosító** az Azure Portalról másolt.

    b. Az a **SAML bejelentkezési cél URL-cím** mezőbe illessze be az értékét **bejelentkezési URL-cím** az Azure Portalról másolt.

    c. Ezután nyissa meg a **összevonási metaadatainak XML** az Azure Portalról a fent letöltött és másolja a tanúsítványt a kódban nevű **X509Certificate**. Illessze be a másolt érték a **az egyszeri bejelentkezést x509 tanúsítvány** mezőbe.

10. Kattintson a **Save Changes** gombra.

11. Ezek a beállítások után meg kell küldenie a a **tartománynév** (például: **contoso.com**) keresztül, amely jelentkezik be a Procore a [Procore támogatási csapatának](https://support.procore.com/) és csatlakoznak az aktiválja az összevont egyszeri bejelentkezés ahhoz a tartományhoz.

<!--### Next steps

To ensure users can sign-in to Procore SSO after it has been configured to use Azure Active Directory, review the following tasks and topics:

- User accounts must be pre-provisioned into Procore SSO prior to sign-in. To set this up, see Provisioning.
 
- Users must be assigned access to Procore SSO in Azure AD to sign-in. To assign users, see Users.
 
- To configure access polices for Procore SSO users, see Access Policies.
 
- For additional information on deploying single sign-on to users, see [this article](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users).-->

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![Az Azure AD-felhasználó létrehozása][100]

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](common/create_aaduser_01.png) 

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](common/create_aaduser_02.png)

    a. Az a **neve** írja be a következőt **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be a **brittasimon\@yourcompanydomain.extension**  
       Például: BrittaSimon@contoso.com

    c. Válassza ki **tulajdonságok**, jelölje be a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Létrehozás** gombra.

### <a name="creating-a-procore-sso-test-user"></a>Procore SSO tesztfelhasználó létrehozása

Kérjük, kövesse az alábbi lépéseket Procore tesztfelhasználó létrehozása Procore SSOc oldalán.

1. Jelentkezzen be rendszergazdaként procore vállalat webhelye.  

2. Az eszközkészlet legördülő lefelé, kattintson a **Directory** a vállalati címtár lap megnyitásához.

    ![Egyszeri bejelentkezés konfigurálása](./media/procoresso-tutorial/Procore_sso_directory.png)

3. Kattintson a **adjon hozzá egy személy** nyissa meg a képernyőt, és adja meg a beállítás hajtsa végre a következő beállítások -

    ![Egyszeri bejelentkezés konfigurálása](./media/procoresso-tutorial/Procore_user_add.png)

    a. Az a **Utónév** szövegmezőbe írja be a felhasználó első neve például **Britta**.

    b. Az a **Vezetéknév** szövegmezőbe írja be a felhasználó vezetékneve például **Simon**.

    c. Az a **E-mail cím** szövegmezőbe írja be a felhasználó e-mail címét, például **BrittaSimon\@contoso.com**.

    d. Válassza ki **jogosultsági sablon** , **jogosultsági sablon alkalmazása később**.

    e. Kattintson a **Create** (Létrehozás) gombra.

4. Ellenőrizze, és az újonnan hozzáadott kapcsolattartó adatainak frissítése.

    ![Egyszeri bejelentkezés konfigurálása](./media/procoresso-tutorial/Procore_user_check.png)

5. Kattintson a **mentés és küldés meghívó** (ha szükséges mail keresztül) vagy **mentése** (mentse közvetlenül) a felhasználói regisztráció befejezéséhez.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/procoresso-tutorial/Procore_user_save.png)

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Procore SSO Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**válassza **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201]

2. Az alkalmazások listájában jelölje ki a **Procore SSO**.

    ![Egyszeri bejelentkezés konfigurálása](./media/procoresso-tutorial/tutorial_procoresso_app.png)

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Az a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelése** gombra.

### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Procore SSO csempére kattint, akkor kell lekérése automatikusan bejelentkezett a Procore egyszeri bejelentkezés alkalmazásba.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png

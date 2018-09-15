---
title: 'Oktatóanyag: Azure Active Directory-integráció az dmarcian |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és dmarcian között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a04b9383-3a60-4d54-9412-123daaddff3b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/14/2018
ms.author: jeedes
ms.openlocfilehash: 677c40932a557b8a15a51b947794b4281801f65a
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2018
ms.locfileid: "45637652"
---
# <a name="tutorial-azure-active-directory-integration-with-dmarcian"></a>Oktatóanyag: Azure Active Directory-integráció az dmarcian

Ebben az oktatóanyagban elsajátíthatja, hogyan dmarcian integrálása az Azure Active Directory (Azure AD).

Dmarcian integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá dmarcian Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett az Azure AD-fiókjukkal (egyszeri bejelentkezés) dmarcian.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Dmarcian az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy dmarcian egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Dmarcian hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-dmarcian-from-the-gallery"></a>Dmarcian hozzáadása a katalógusból
Az Azure AD integrálása a dmarcian konfigurálásához hozzá kell dmarcian a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Dmarcian hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **dmarcian**válassza **dmarcian** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![az eredmények listájában dmarcian](./media/dmarcian-tutorial/tutorial_dmarcian_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés dmarcian a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó dmarcian mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó dmarcian hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az dmarcian tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Hozzon létre egy dmarcian tesztfelhasználót](#create-a-dmarcian-test-user)**  – egy megfelelője a Britta Simon a felhasználó Azure ad-ben reprezentációja kapcsolódó dmarcian rendelkeznie.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és dmarcian alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés dmarcian, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **dmarcian** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/dmarcian-tutorial/tutorial_dmarcian_samlbase.png)

3. Az a **dmarcian tartomány és URL-címek** területén kövesse az alábbi lépéseket, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód:

    ![dmarcian tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/dmarcian-tutorial/tutorial_dmarcian_url.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe:
    | |
    | -- |
    | `https://us.dmarcian.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian.com-eu/sso/saml/<ACCOUNT_ID>/sp.xml ` |
    | `https://dmarcian.com-ap/sso/saml/<ACCOUNT_ID>/sp.xml` |

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe:
    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian.com-eu/login/<ACCOUNT_ID>/handle/ `|
    | `https://dmarcian.com-ap/login/<ACCOUNT_ID>/handle/`|

4. Ellenőrizze **speciális URL-beállítások megjelenítése** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![dmarcian tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/dmarcian-tutorial/tutorial_dmarcian_url1.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe:
    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>` |
    | `https://dmarcian.com-eu/login/<ACCOUNT_ID>` |
    | `https://dmarcian.com-ap/login/<ACCOUNT_ID>` |
     
    > [!NOTE] 
    > Ezek a értékei nem valódi. A tényleges azonosítója, válasz URL-cím és bejelentkezési URL-címet az oktatóanyag későbbi részében ismertetett frissíteni ezeket az értékeket. 

5. Az a **SAML-aláíró tanúsítvány** területén kattintson a Másolás gombra, hogy **alkalmazás összevonási metaadatainak URL-címe** , és illessze be a Jegyzettömbbe.

    ![A tanúsítvány letöltési hivatkozás](./media/dmarcian-tutorial/tutorial_dmarcian_certificate.png) 

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/dmarcian-tutorial/tutorial_general_400.png)
    
7. Egy másik böngészőablakban jelentkezzen be dmarcian egy biztonsági-rendszergazdaként.

8. Kattintson a **profil** jobb felső sarokban, és navigáljon a **beállítások**.

    ![A beállítások ](./media/dmarcian-tutorial/tutorial_dmarcian_pref.png)

9. Görgessen lefelé, és kattintson a **egyszeri bejelentkezés** területen, majd kattintson a **konfigurálása**.

    ![Az egyetlen ](./media/dmarcian-tutorial/tutorial_dmarcian_sso.png)

10. Az a **SAML egyszeri bejelentkezés** lapon állítsa be a **állapot** , **engedélyezve** , és hajtsa végre az alábbi lépéseket:

    ![A hitelesítés ](./media/dmarcian-tutorial/tutorial_dmarcian_auth.png)

    * A **dmarcian ad hozzá az identitásszolgáltató** területén kattintson **MÁSOLÁSI** másolása a **helyességi feltétel fogyasztói URL-címe** a példány, és illessze be a  **Válasz URL-cím** szövegmezőjébe **dmarcian tartomány és URL-címek szakaszt** az Azure Portalon.

    * Alatt **dmarcian ad hozzá az identitásszolgáltató** területén kattintson **MÁSOLÁSI** másolása a **Entitásazonosító** a példány, és illessze be a **azonosító**szövegmezőjébe **dmarcian tartomány és URL-címek szakaszt** az Azure Portalon.

    * Alatt **hitelesítés beállítása** ebben a szakaszban a **Identity Provider metaadatok** szövegmező illessze be a **alkalmazás összevonási metaadatainak URL-címe**, az Azure Portalról másolt.

    * Alatt **hitelesítés beállítása** ebben a szakaszban a **attribútum utasítások** szövegmező illessze be az URL-cím `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    * A **állítsa be a bejelentkezési URL-cím** területén másolja a **bejelentkezési URL-cím** a példány, és illessze be a **bejelentkezési URL-** szövegmezőjébe **dmarcian tartomány és URL-címek szakaszt** az Azure Portalon.

        > [!Note]
        > Módosíthatja a **bejelentkezési URL-cím** a szervezetnek megfelelően.

    * Kattintson a **Save** (Mentés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/dmarcian-tutorial/create_aaduser_01.png)

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/dmarcian-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/dmarcian-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/dmarcian-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-dmarcian-test-user"></a>Dmarcian tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók dmarcian bejelentkezni, akkor ki kell építeni dmarcian be. Dmarcian a kiépítés manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be dmarcian egy biztonsági-rendszergazdaként.

2. Kattintson a **profil** jobb felső sarokban, és navigáljon a **felhasználók kezelése**.

    ![A felhasználó ](./media/dmarcian-tutorial/tutorial_dmarcian_user.png)

3. A jobb oldalán található **SSO felhasználók** területén kattintson a **új felhasználó hozzáadása**.

    ![A felhasználó hozzáadása ](./media/dmarcian-tutorial/tutorial_dmarcian_addnewuser.png)

4. Az a **új felhasználó hozzáadása** előugró ablakban hajtsa végre az alábbi lépéseket:

    ![Az új felhasználó ](./media/dmarcian-tutorial/tutorial_dmarcian_save.png)

    a. Az a **új felhasználó e-mail-címe** szövegmezőben adja meg az e-mail címét, például a felhasználó **brittasimon@contoso.com**.

    b. Ha azt szeretné, hogy a felhasználó rendszergazdai jogokat, válassza ki a **győződjön meg arról, felhasználói rendszergazda**.

    c. Kattintson a **felhasználó hozzáadása**.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon használja az Azure egyszeri bejelentkezés dmarcian való hozzáférést.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel dmarcian, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **dmarcian**.

    ![Az alkalmazások listáját a dmarcian hivatkozásra](./media/dmarcian-tutorial/tutorial_dmarcian_app.png)  

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a dmarcian csempére kattint, meg kell lekérése automatikusan bejelentkezett az dmarcian alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/dmarcian-tutorial/tutorial_general_01.png
[2]: ./media/dmarcian-tutorial/tutorial_general_02.png
[3]: ./media/dmarcian-tutorial/tutorial_general_03.png
[4]: ./media/dmarcian-tutorial/tutorial_general_04.png

[100]: ./media/dmarcian-tutorial/tutorial_general_100.png

[200]: ./media/dmarcian-tutorial/tutorial_general_200.png
[201]: ./media/dmarcian-tutorial/tutorial_general_201.png
[202]: ./media/dmarcian-tutorial/tutorial_general_202.png
[203]: ./media/dmarcian-tutorial/tutorial_general_203.png


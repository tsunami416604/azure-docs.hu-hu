---
title: 'Oktatóanyag: Azure Active Directory-integráció az AirWatch |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és az AirWatch között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 96a3bb1c-96c6-40dc-8ea0-060b0c2a62e5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: f3bbcbb70759e7a995797cf89ad75a2a39314927
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39048480"
---
# <a name="tutorial-azure-active-directory-integration-with-airwatch"></a>Oktatóanyag: Azure Active Directory-integráció az AirWatch

Ebben az oktatóanyagban elsajátíthatja, hogyan AirWatch integrálása az Azure Active Directory (Azure AD).

AirWatch integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá az AirWatch Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett az AirWatch (egyszeri bejelentkezés) az Azure AD-fiókjukat
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása az AirWatch, a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Az AirWatch egyszeri bejelentkezéses engedélyezett előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. AirWatch hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-airwatch-from-the-gallery"></a>AirWatch hozzáadása a katalógusból
Az Azure AD integrálása a AirWatch konfigurálásához hozzá kell AirWatch a katalógusból a felügyelt SaaS-alkalmazások listájára.

**AirWatch hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

4. A Keresés mezőbe írja be a **AirWatch**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/airwatch-tutorial/tutorial_airwatch_search.png)

5. Az eredmények panelen válassza ki a **AirWatch**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/airwatch-tutorial/tutorial_airwatch_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az AirWatch egy "Britta Simon." nevű tesztelési felhasználó alapján

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó AirWatch mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó AirWatch hivatkozás kapcsolata kell létrehozni.

Ez a hivatkozás-kapcsolat létesítéséhez értéket rendeli az **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** az AirWatch.

Az Azure AD egyszeri bejelentkezés az AirWatch tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[AirWatch tesztfelhasználó létrehozása](#creating-a-airwatch-test-user)**  – egy megfelelője a Britta Simon AirWatch, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
4. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és az AirWatch alkalmazás egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálása az AirWatch, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **AirWatch** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/airwatch-tutorial/tutorial_airwatch_samlbase.png)

3. Az a **AirWatch tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/airwatch-tutorial/tutorial_airwatch_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode`

    b. Az a **azonosító** szövegmezőbe írja be az értéket, mint `AirWatch`

    > [!NOTE] 
    > Ez az érték nem a valós. Ez az érték frissítse a tényleges bejelentkezési URL-CÍMÉT. Kapcsolattartó [AirWatch ügyfél-támogatási csapatának](http://www.air-watch.com/company/contact-us/) lekérni ezt az értéket. 
 
4. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse az XML-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/airwatch-tutorial/tutorial_airwatch_certificate.png) 

5. Az a **AirWatch konfigurációs** területén kattintson **konfigurálása AirWatch** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/airwatch-tutorial/tutorial_airwatch_configure.png) 

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/airwatch-tutorial/tutorial_general_400.png)
<CS>
7. Egy másik böngészőablakban jelentkezzen be az AirWatch vállalati hely rendszergazdaként.

8. Kattintson a bal oldali navigációs ablaktáblában **fiókok**, és kattintson a **rendszergazdák**.
   
   ![A rendszergazdák](./media/airwatch-tutorial/ic791920.png "rendszergazdák")

9. Bontsa ki a **beállítások** menüre, majd **címtárszolgáltatások**.
   
   ![Beállítások](./media/airwatch-tutorial/ic791921.png "beállításai")

10. Kattintson a **felhasználói** lap a **Alap DN** szövegmezőbe írja be a tartomány nevét, és kattintson **mentése**.
   
   ![Felhasználói](./media/airwatch-tutorial/ic791922.png "felhasználó")

11. Kattintson a **kiszolgáló** fülre.
   
   ![Kiszolgáló](./media/airwatch-tutorial/ic791923.png "kiszolgáló")

12. Hajtsa végre az alábbi lépéseket:
    
    ![Töltse fel](./media/airwatch-tutorial/ic791924.png "feltöltése")   
    
    a. Mint **címtár típusa**válassza **None**.

    b. Válassza ki **SAML hitelesítéshez használandó**.

    c. A letöltött tanúsítvány feltöltéséhez kattintson **feltöltése**.

13. Az a **kérelem** szakaszban, hajtsa végre az alábbi lépéseket:
    
    ![Kérelem](./media/airwatch-tutorial/ic791925.png "kérése")  

    a. Mint **kötési típus kérése**válassza **POST**.

    b. Az Azure Portalon a a **Airwatch, egyszeri bejelentkezés konfigurálása** párbeszédpanel oldalon, a Másolás a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** értéket, és illessze be azt a **Identity Provider egyszeri bejelentkezés URL-cím** szövegmezőbe.

    c. Mint **nameid-formátumához**válassza **E-mail cím**.

    d. Kattintson a **Save** (Mentés) gombra.

14. Kattintson a **felhasználói** lapon újra.
    
    ![Felhasználói](./media/airwatch-tutorial/ic791926.png "felhasználó")

15. Az a **attribútum** szakaszban, hajtsa végre az alábbi lépéseket:
    
    ![Attribútum](./media/airwatch-tutorial/ic791927.png "attribútum")

    a. Az a **objektumazonosító** szövegmezőbe írja be **http://schemas.microsoft.com/identity/claims/objectidentifier**.

    b. Az a **felhasználónév** szövegmezőbe írja be **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    c. Az a **megjelenítendő név** szövegmezőbe írja be **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.

    d. Az a **Utónév** szövegmezőbe írja be **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.

    e. Az a **Vezetéknév** szövegmezőbe írja be **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.

    f. Az a **E-mail** szövegmezőbe írja be **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    g. Kattintson a **Save** (Mentés) gombra.

<CE>

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/airwatch-tutorial/create_aaduser_01.png) 

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/airwatch-tutorial/create_aaduser_02.png) 

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/airwatch-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/airwatch-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** Britta Simon.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-airwatch-test-user"></a>AirWatch tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók AirWatch jelentkezzen be, akkor ki kell építenie az AirWatch.

* AirWatch, ha a manuális tevékenység kiépítése.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **AirWatch** rendszergazdaként a vállalati webhely.
2. A bal oldali navigációs panelén kattintson **fiókok**, és kattintson a **felhasználók**.
   
   ![Felhasználók](./media/airwatch-tutorial/ic791929.png "felhasználók")
3. Az a **felhasználók** menüben kattintson a **listanézet**, és kattintson a **Hozzáadás \> felhasználó hozzáadása**.
   
   ![Felhasználó hozzáadása](./media/airwatch-tutorial/ic791930.png "felhasználó hozzáadása")
4. Az a **hozzáadása / szerkesztése felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

   ![Felhasználó hozzáadása](./media/airwatch-tutorial/ic791931.png "felhasználó hozzáadása")   
   1. Írja be a **felhasználónév**, **jelszó**, **jelszó megerősítése**, **Utónév**, **Vezetéknév**,  **E-mail-cím** egy érvényes Azure Active Directory-fióknevet, amelyet a kapcsolódó szövegmezőkben létrehozásához.
   2. Kattintson a **Save** (Mentés) gombra.

>[!NOTE]
>Bármely más AirWatch felhasználói fiók létrehozása eszközöket használhatja, vagy az aad-ben a felhasználói fiókok kiépítését AirWatch által biztosított API-k.
>  

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés AirWatch Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel AirWatch, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **AirWatch**.

    ![Egyszeri bejelentkezés konfigurálása](./media/airwatch-tutorial/tutorial_airwatch_app.png) 

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha szeretné tesztelni az egyszeri bejelentkezés beállításai, nyissa meg a hozzáférési panelen. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).


## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/airwatch-tutorial/tutorial_general_01.png
[2]: ./media/airwatch-tutorial/tutorial_general_02.png
[3]: ./media/airwatch-tutorial/tutorial_general_03.png
[4]: ./media/airwatch-tutorial/tutorial_general_04.png

[100]: ./media/airwatch-tutorial/tutorial_general_100.png

[200]: ./media/airwatch-tutorial/tutorial_general_200.png
[201]: ./media/airwatch-tutorial/tutorial_general_201.png
[202]: ./media/airwatch-tutorial/tutorial_general_202.png
[203]: ./media/airwatch-tutorial/tutorial_general_203.png


---
title: 'Oktatóanyag: Azure Active Directory-integráció az TalentLMS |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és TalentLMS között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c903d20d-18e3-42b0-b997-6349c5412dde
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: jeedes
ms.openlocfilehash: 79e3b853728f6f997ad97a653ac3c119b2760cbd
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39430639"
---
# <a name="tutorial-azure-active-directory-integration-with-talentlms"></a>Oktatóanyag: Azure Active Directory-integráció az TalentLMS

Ebben az oktatóanyagban elsajátíthatja, hogyan TalentLMS integrálása az Azure Active Directory (Azure AD).

TalentLMS integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá TalentLMS Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett TalentLMS (egyszeri bejelentkezés) az Azure AD-fiókjukkal
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

TalentLMS az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy TalentLMS egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a ide-egy havi próbalehetőség: [próba](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. TalentLMS hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-talentlms-from-the-gallery"></a>TalentLMS hozzáadása a katalógusból
Az Azure AD integrálása a TalentLMS konfigurálásához hozzá kell TalentLMS a katalógusból a felügyelt SaaS-alkalmazások listájára.

**TalentLMS hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **TalentLMS**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/talentlms-tutorial/tutorial_talentlms_search.png)

1. Az eredmények panelen válassza ki a **TalentLMS**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/talentlms-tutorial/tutorial_talentlms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban konfigurálja, és a teszt "Britta Simon." nevű felhasználó TalentLMS az Azure AD egyszeri bejelentkezés tesztelése

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó TalentLMS mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó TalentLMS hivatkozás kapcsolata kell létrehozni.

TalentLMS, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az TalentLMS tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[TalentLMS tesztfelhasználó létrehozása](#creating-a-talentlms-test-user)**  – egy megfelelője a Britta Simon TalentLMS, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és TalentLMS alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés TalentLMS, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **TalentLMS** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/talentlms-tutorial/tutorial_talentlms_samlbase.png)

1. Az a **TalentLMS tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/talentlms-tutorial/tutorial_talentlms_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<tenant-name>.TalentLMSapp.com`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `http://<tenant-name>.talentlms.com`

    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL- és azonosító. Kapcsolattartó [TalentLMS ügyfél-támogatási csapatának](https://www.talentlms.com/contact) beolvasni ezeket az értékeket. 
 
1. Az a **SAML-aláíró tanúsítvány** területén másolja a **UJJLENYOMAT** érték a tanúsítványból.

    ![Egyszeri bejelentkezés konfigurálása](./media/talentlms-tutorial/tutorial_talentlms_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/talentlms-tutorial/tutorial_general_400.png)

1. Az a **TalentLMS konfigurációs** területén kattintson **konfigurálása TalentLMS** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/talentlms-tutorial/tutorial_talentlms_configure.png)  

1. Egy másik böngészőablakban jelentkezzen be a TalentLMS vállalati hely rendszergazdaként.

1. Az a **fiók & beállítások** területén kattintson a **felhasználók** fülre.
   
    ![Fiók & beállítások](./media/talentlms-tutorial/IC777296.png "fiók & beállításai")

1. Kattintson a **egyszeri bejelentkezés (SSO)**,

1. Az egyszeri bejelentkezés szakaszban hajtsa végre az alábbi lépéseket:
   
    ![Egyszeri bejelentkezés](./media/talentlms-tutorial/IC777297.png "egyszeri bejelentkezés")   

    a. Az a **SSO integrációs típus** listáról válassza ki **SAML 2.0**.

    b. Az a **identitásszolgáltató (IDP)** szövegmező, illessze be az értéket a **SAML Entitásazonosító**, az Azure Portalról másolt.
 
    c. Illessze be a **ujjlenyomat** értéket az Azure Portalról a **tanúsítvány-ujjlenyomat** szövegmezőbe.    

    d.  Az a **távoli bejelentkezési URL-cím** szövegmezőbe, illessze be az értéket a **SAML egyszeri bejelentkezési szolgáltatás URL-cím**, az Azure Portalról másolt.
 
    e. Az a **távoli kijelentkezési URL-cím** szövegmező, illessze be az értéket a **kijelentkezéses URL-cím**, az Azure Portalról másolt.

    f. Töltse ki a következőket: 

    * Az a **TargetedID** szövegmezőbe írja be `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`
     
    * Az a **Utónév** szövegmezőbe írja be `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`
    
    * Az a **Vezetéknév** szövegmezőbe írja be `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`
    
    * Az a **E-mail** szövegmezőbe írja be `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`
    
1. Kattintson a **Save** (Mentés) gombra.
 
> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/talentlms-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/talentlms-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/talentlms-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/talentlms-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-talentlms-test-user"></a>TalentLMS tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók TalentLMS jelentkezzen be, akkor ki kell építeni TalentLMS be. TalentLMS, esetén kiépítése a manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **TalentLMS** bérlő.

1. Kattintson a **felhasználók**, és kattintson a **felhasználó hozzáadása**.

1. Az a **felhasználó hozzáadása** párbeszédpanel lapon, a következő lépésekkel:
   
    ![Felhasználó hozzáadása](./media/talentlms-tutorial/IC777299.png "felhasználó hozzáadása")  

    a. Az a **Utónév** szövegmezőbe írja be például a felhasználó utónevét **Britta**.

    b. Az a **Vezetéknév** szövegmezőbe írja be például a felhasználó vezetékneve **Simon**.
 
    c. Az a **E-mail-cím** szövegmezőben adja meg az e-mail címét, például a felhasználó **brittasimon@contoso.com**.

    d. Kattintson a **felhasználó hozzáadása**.

>[!NOTE]
>Bármely más TalentLMS felhasználói fiók létrehozása eszközöket használhatja, vagy az aad-ben a felhasználói fiókok kiépítését TalentLMS által biztosított API-k.
 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés TalentLMS Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel TalentLMS, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **TalentLMS**.

    ![Egyszeri bejelentkezés konfigurálása](./media/talentlms-tutorial/tutorial_talentlms_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ez a szakasz célja tesztelése az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a TalentLMS csempére kattint, meg kell lekérése automatikusan bejelentkezett az TalentLMS alkalmazáshoz

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/talentlms-tutorial/tutorial_general_01.png
[2]: ./media/talentlms-tutorial/tutorial_general_02.png
[3]: ./media/talentlms-tutorial/tutorial_general_03.png
[4]: ./media/talentlms-tutorial/tutorial_general_04.png

[100]: ./media/talentlms-tutorial/tutorial_general_100.png

[200]: ./media/talentlms-tutorial/tutorial_general_200.png
[201]: ./media/talentlms-tutorial/tutorial_general_201.png
[202]: ./media/talentlms-tutorial/tutorial_general_202.png
[203]: ./media/talentlms-tutorial/tutorial_general_203.png


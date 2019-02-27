---
title: 'Oktatóanyag: A TINFOIL SECURITY szolgáltatással az Azure Active Directory-integráció |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a TINFOIL SECURITY között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: da02da92-e3b0-4c09-ad6c-180882b0f9f8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d2001e221ef9c02ee2ab80b647a9bafe6490432
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56882144"
---
# <a name="tutorial-azure-active-directory-integration-with-tinfoil-security"></a>Oktatóanyag: A TINFOIL SECURITY szolgáltatással az Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja, hogyan teljesíteni a TINFOIL SECURITY integrálása az Azure Active Directory (Azure AD).

TINFOIL SECURITY integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá a TINFOIL SECURITY az Azure AD-ben
- Az Azure AD-fiókjukat engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a TINFOIL SECURITY (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a TINFOIL SECURITY szolgáltatással, a következőkre van szükség:

- Azure AD-előfizetés
- A TINFOIL SECURITY egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. TINFOIL SECURITY hozzáadása a katalógusból
1. Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

## <a name="add-tinfoil-security-from-the-gallery"></a>TINFOIL SECURITY hozzáadása a katalógusból
Az Azure AD integrálása a TINFOIL SECURITY konfigurálásához hozzá kell teljesíteni a TINFOIL SECURITY a galériából a felügyelt SaaS-alkalmazások listájára.

**TINFOIL SECURITY hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **teljesíteni a TINFOIL SECURITY**válassza **teljesíteni a TINFOIL SECURITY** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![TINFOIL SECURITY-galériából](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés teljesíteni a TINFOIL SECURITY "Britta Simon" nevű tesztfelhasználó alapján.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó a TINFOIL SECURITY mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó a TINFOIL SECURITY felhasználói hivatkozás kapcsolata kell létrehozni.

TINFOIL SECURITY, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés a TINFOIL SECURITY szolgáltatással tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy teljesíteni a TINFOIL SECURITY tesztfelhasználót](#create-a-tinfoil-security-test-user)**  – egy megfelelője a Britta Simon kell teljesíteni a TINFOIL SECURITY, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és teljesíteni a TINFOIL SECURITY alkalmazását az egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálásához a TINFOIL SECURITY szolgáltatással, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **teljesíteni a TINFOIL SECURITY** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![SAML-alapú bejelentkezésre](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_samlbase.png)

1. Az a **teljesíteni a TINFOIL SECURITY tartomány és URL-címek** szakaszban, a felhasználónak nem kell végrehajthatja a lépéseket, ahogy az alkalmazás már előre integrálva van az Azure-ral.

    ![Egyszeri bejelentkezés konfigurálása](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_url.png)


1. Az a **SAML-aláíró tanúsítvány** területén másolja a **UJJLENYOMAT** értéket.

    ![SAML-aláíró tanúsítvány szakaszban](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_certificate.png) 

1. Adja hozzá a szükséges attribútumleképezések, hajtsa végre az alábbi lépéseket:
    
    ![Attribútumok](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_attribute1.png "attribútumok")
    
    | Attribútum neve    |   Attribútum értéke |
    | ------------------- | -------------------- |
    | fiókazonosító | UXXXXXXXXXXXXX |
    
    a. Kattintson a **adja hozzá a felhasználói attribútum**.
    
    ![Attribútum hozzáadása](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_attribute.png "attribútumok")
    
    ![Attribútum hozzáadása](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_addatt.png "attribútumok")
    
    b. Az a **attribútumnév** szövegmezőbe írja be **accountid**.
    
    c. Az a **attribútumérték** szövegmezőjébe illessze be a Fiókazonosító-értéket, amelyet később az oktatóanyag fog kapni.
    
    d. Kattintson az **OK** gombra.    

1. Kattintson a **mentése** gombra.

    ![Mentés gomb](./media/tinfoil-security-tutorial/tutorial_general_400.png)

1. Az a **teljesíteni a TINFOIL SECURITY Configuration** területén kattintson **konfigurálása teljesíteni a TINFOIL SECURITY** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![A TINFOIL SECURITY Configuration](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_configure.png) 

1. Egy másik böngészőablakban jelentkezzen be a TINFOIL SECURITY vállalati hely rendszergazdaként.

1. A felső eszköztáron kattintson **My Account**.
   
    ![Irányítópult](./media/tinfoil-security-tutorial/ic798971.png "irányítópult")

1. Kattintson a **biztonsági**.
   
    ![Biztonsági](./media/tinfoil-security-tutorial/ic798972.png "biztonsági")

1. Az a **egyszeri bejelentkezés** konfiguráció lapon, a következő lépésekkel:
   
    ![Egyszeri bejelentkezés](./media/tinfoil-security-tutorial/ic798973.png "egyszeri bejelentkezés")
   
    a. Válassza ki **SAML engedélyezése**.
   
    b. Kattintson a **manuális konfigurációs**.
   
    c. A **SAML Post URL-címe** szövegmezőjébe illessze be az értéket, **SAML egyszeri bejelentkezési szolgáltatás URL-cím** Azure Portalról másolt
   
    d. A **SAML-tanúsítvány ujjlenyomat** szövegmezőjébe illessze be az értéket, **ujjlenyomat** másolta amely **SAML-aláíró tanúsítvány** szakasz.
  
    e. Másolás **a Fiókazonosító** értékét, és illessze be az értéket a **attribútumérték** szövegmező alatt **attribútum hozzáadása** szakaszban az Azure Portalon.
   
    f. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/tinfoil-security-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Felhasználók és csoportok -> minden felhasználó](./media/tinfoil-security-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Felhasználó](./media/tinfoil-security-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/tinfoil-security-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-tinfoil-security-test-user"></a>TINFOIL SECURITY tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók teljesíteni a TINFOIL SECURITY-ba való bejelentkezéshez, akkor ki kell építeni teljesíteni a TINFOIL SECURITY be. TINFOIL SECURITY, esetén kézi tevékenység kiépítése.

**Kiépített felhasználó lekérése, hajtsa végre az alábbi lépéseket:**

1. Ha a felhasználó egy vállalati fiók tagja, akkor kell [fel a kapcsolatot a TINFOIL SECURITY csapatával](https://www.tinfoilsecurity.com/contact) beolvasni a létrehozott felhasználói fiókkal.

1. Ha a felhasználó egy rendszeres teljesíteni a TINFOIL SECURITY SaaS-felhasználó, majd a felhasználót adhat hozzá egy közreműködő a felhasználó webhelyek bármelyikéhez. Ez elindít egy folyamatot, hogy hozzon létre egy új teljesíteni a TINFOIL SECURITY-felhasználói fiókot a megadott e-mail-meghívó küldése.

> [!NOTE]
> TINFOIL SECURITY felhasználói fiók létrehozása eszközöket és teljesíteni a TINFOIL SECURITY által biztosított API-k segítségével az Azure AD-felhasználói fiókok kiépítése.
> 
> 

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon a hozzáférés biztosításával a TINFOIL SECURITY Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendelhet TINFOIL SECURITY, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **teljesíteni a TINFOIL SECURITY**.

    ![Válassza ki a TINFOIL SECURITY](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a TINFOIL SECURITY csempére kattint, akkor kell lekérése automatikusan bejelentkezett a TINFOIL SECURITY alkalmazáshoz. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/tinfoil-security-tutorial/tutorial_general_01.png
[2]: ./media/tinfoil-security-tutorial/tutorial_general_02.png
[3]: ./media/tinfoil-security-tutorial/tutorial_general_03.png
[4]: ./media/tinfoil-security-tutorial/tutorial_general_04.png

[100]: ./media/tinfoil-security-tutorial/tutorial_general_100.png

[200]: ./media/tinfoil-security-tutorial/tutorial_general_200.png
[201]: ./media/tinfoil-security-tutorial/tutorial_general_201.png
[202]: ./media/tinfoil-security-tutorial/tutorial_general_202.png
[203]: ./media/tinfoil-security-tutorial/tutorial_general_203.png


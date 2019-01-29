---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező SmarterU |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és SmarterU között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 95fe3212-d052-4ac8-87eb-ac5305227e85
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: bfba86ba453c40a7612c28aa2f84365f03b9e3a6
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55174312"
---
# <a name="tutorial-azure-active-directory-integration-with-smarteru"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező SmarterU

Ebben az oktatóanyagban elsajátíthatja, hogyan SmarterU integrálása az Azure Active Directory (Azure AD).

SmarterU integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá SmarterU Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett SmarterU (egyszeri bejelentkezés) az Azure AD-fiókjukkal
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

SmarterU az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy SmarterU egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. SmarterU hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-smarteru-from-the-gallery"></a>SmarterU hozzáadása a katalógusból
Az Azure AD integrálása a SmarterU konfigurálásához hozzá kell SmarterU a katalógusból a felügyelt SaaS-alkalmazások listájára.

**SmarterU hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **SmarterU**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/smarteru-tutorial/tutorial_smarteru_search.png)

1. Az eredmények panelen válassza ki a **SmarterU**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/smarteru-tutorial/tutorial_smarteru_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban konfigurálja, és a teszt "Britta Simon." nevű felhasználó SmarterU az Azure AD egyszeri bejelentkezés tesztelése

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó SmarterU mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó SmarterU hivatkozás kapcsolata kell létrehozni.

SmarterU, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az SmarterU tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[SmarterU tesztfelhasználó létrehozása](#creating-a-smarteru-test-user)**  – egy megfelelője a Britta Simon SmarterU, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és SmarterU alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés SmarterU, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **SmarterU** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/smarteru-tutorial/tutorial_smarteru_samlbase.png)

1. Az a **SmarterU tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket: 

    ![Egyszeri bejelentkezés konfigurálása](./media/smarteru-tutorial/tutorial_smarteru_url.png)

    Az a **azonosító** szövegmezőbe írja be az URL-cím: `https://www.smarteru.com/`

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/smarteru-tutorial/tutorial_smarteru_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/smarteru-tutorial/tutorial_general_400.png)

1. Egy másik böngészőablakban jelentkezzen be a SmarterU vállalati hely rendszergazdaként.

1. A felső eszköztáron kattintson **fiókbeállításokat**.
   
    ![Fiókbeállítások](./media/smarteru-tutorial/accountsettings.png)

1. A fiók konfigurálása lapon hajtsa végre az alábbi lépéseket:
   
    ![Külső engedélyezési](./media/smarteru-tutorial/externalauthorizationconfiguration.png) 
 
      a. Válassza ki **engedélyezése külső engedélyezési**.
  
      b. Az a **fő bejelentkezés-ellenőrző** szakaszban jelölje be a **SmarterU** fülre.
  
      c. Az a **felhasználó alapértelmezett bejelentkezési** szakaszban jelölje be a **SmarterU** fülre.
  
      d. Válassza ki **SAML engedélyezése**.
  
      e. Másolja a letöltött metaadatait tartalmazó fájl tartalmát, és illessze be azt a **identitásszolgáltató metaadatok** szövegmezőbe.
      
      f. Válasszon egy **azonosító attribútum/jogcím**.
  
      g. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/smarteru-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/smarteru-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/smarteru-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/smarteru-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-smarteru-test-user"></a>SmarterU tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók SmarterU jelentkezzen be, akkor ki kell építeni SmarterU be.

SmarterU, ha a manuális tevékenység kiépítése.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **SmarterU** bérlő.

1. Lépjen a **felhasználók**.

1. A felhasználói csoportban hajtsa végre az alábbi lépéseket:
   
    ![Új felhasználó](./media/smarteru-tutorial/adduser.png)  

    a. Kattintson a **+ felhasználó**.
    
    b. Az Azure AD felhasználói fiók kapcsolódó attribútumértékek írja be a következő szövegmezőkben: **Elsődleges E-mail**, **alkalmazott azonosítója**, **jelszó**, **jelszó ellenőrzése**, **Utónév**, **vezetékneve**.
    
    c. Kattintson a **aktív**. 
    
    d. Kattintson a **Save** (Mentés) gombra.

>[!NOTE]
>Bármely más SmarterU felhasználói fiók létrehozása eszközöket használhatja, vagy az aad-ben a felhasználói fiókok kiépítését SmarterU által biztosított API-k.
> 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés SmarterU Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel SmarterU, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **SmarterU**.

    ![Egyszeri bejelentkezés konfigurálása](./media/smarteru-tutorial/tutorial_smarteru_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.
 
Ha a hozzáférési panelen a SmarterU csempére kattint, meg kell lekérése automatikusan bejelentkezett az SmarterU alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 


## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/smarteru-tutorial/tutorial_general_01.png
[2]: ./media/smarteru-tutorial/tutorial_general_02.png
[3]: ./media/smarteru-tutorial/tutorial_general_03.png
[4]: ./media/smarteru-tutorial/tutorial_general_04.png

[100]: ./media/smarteru-tutorial/tutorial_general_100.png

[200]: ./media/smarteru-tutorial/tutorial_general_200.png
[201]: ./media/smarteru-tutorial/tutorial_general_201.png
[202]: ./media/smarteru-tutorial/tutorial_general_202.png
[203]: ./media/smarteru-tutorial/tutorial_general_203.png


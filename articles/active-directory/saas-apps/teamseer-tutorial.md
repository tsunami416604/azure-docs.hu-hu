---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező TeamSeer |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és TeamSeer között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6ec4806f-fe0f-4ed7-8cfa-32d1c840433f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.openlocfilehash: ffe94d8d3e08bb35cf8ea20f4b1e32b1ec84aa35
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54814154"
---
# <a name="tutorial-azure-active-directory-integration-with-teamseer"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező TeamSeer

Ebben az oktatóanyagban elsajátíthatja, hogyan TeamSeer integrálása az Azure Active Directory (Azure AD).

TeamSeer integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá TeamSeer Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett TeamSeer (egyszeri bejelentkezés) az Azure AD-fiókjukkal
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

TeamSeer az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy TeamSeer egyszeri bejelentkezéses engedélyezett előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. TeamSeer hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-teamseer-from-the-gallery"></a>TeamSeer hozzáadása a katalógusból
TeamSeer az Azure ad-integráció konfigurálásához, hozzá kell TeamSeer a galériából a felügyelt SaaS-alkalmazások listájára.

**TeamSeer hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **TeamSeer**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/teamseer-tutorial/tutorial_teamseer_search.png)

1. Az eredmények panelen válassza ki a **TeamSeer**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/teamseer-tutorial/tutorial_teamseer_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban konfigurálja, és a teszt "Britta Simon." nevű felhasználó TeamSeer az Azure AD egyszeri bejelentkezés tesztelése

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó TeamSeer mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó TeamSeer hivatkozás kapcsolata kell létrehozni.

TeamSeer, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az TeamSeer tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[TeamSeer tesztfelhasználó létrehozása](#creating-a-teamseer-test-user)**  – egy megfelelője a Britta Simon TeamSeer, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és TeamSeer alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés TeamSeer, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **TeamSeer** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/teamseer-tutorial/tutorial_teamseer_samlbase.png)

1. Az a **TeamSeer tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/teamseer-tutorial/tutorial_teamseer_url.png)

     Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://www.teamseer.com/<companyid>`

    > [!NOTE] 
    > Az érték nem valódi. Frissítse az értéket a tényleges bejelentkezési URL-CÍMÉT. Kapcsolattartó [TeamSeer ügyfél-támogatási csapatának](https://pages.theaccessgroup.com/solutions_business-suite_absence-management_contact.html) a gépkulcsengedélyek értékének. 
 
1. Az a **SAML-aláíró tanúsítvány** területén kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/teamseer-tutorial/tutorial_teamseer_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/teamseer-tutorial/tutorial_general_400.png)

1. Az a **TeamSeer konfigurációs** területén kattintson **konfigurálása TeamSeer** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/teamseer-tutorial/tutorial_teamseer_configure.png)

1. Egy másik böngészőablakban jelentkezzen be a TeamSeer vállalati hely rendszergazdaként.

1. Lépjen a **HR rendszergazdai**.
   
    ![HR rendszergazdai](./media/teamseer-tutorial/ic789634.png "HR-rendszergazda")

1. Kattintson a **telepítő**.
   
    ![A telepítő](./media/teamseer-tutorial/ic789635.png "beállítása")

1. Kattintson a **SAML-szolgáltató részleteinek beállítása**.
   
    ![SAML-beállítások](./media/teamseer-tutorial/ic789636.png "SAML-beállítások")

1. Az SAML szolgáltatói részletek szakaszban hajtsa végre az alábbi lépéseket:
   
    ![SAML-beállítások](./media/teamseer-tutorial/ic789637.png "SAML-beállítások")   

    a. Illessze be a **egyszeri bejelentkezési szolgáltatás URL-cím** az értéket a **URL-cím** szövegmezőbe.
          
    b. Nyissa meg a base-64 kódolású tanúsítványt a Jegyzettömbben, a tartalmát a vágólapra másolja és illessze be azt a **identitásszolgáltató nyilvános tanúsítvány** szövegmezőbe.

1. Az SAML-szolgáltató konfigurációjának befejezéséhez hajtsa végre az alábbi lépéseket:
    
    ![SAML-beállítások](./media/teamseer-tutorial/ic789638.png "SAML-beállítások") 

    a. Az a **E-mail-címek tesztelése**, írja be a tesztfelhasználó e-mail címet. 
  
    b. Az a **kibocsátó** szövegmezőbe írja be a kibocsátó URL-címét a szolgáltató. 
  
    c. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/teamseer-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/teamseer-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/teamseer-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/teamseer-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-teamseer-test-user"></a>TeamSeer tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók TeamSeer jelentkezzen be, akkor ki kell építenie a ShiftPlanning. TeamSeer, esetén kiépítése a manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **TeamSeer** rendszergazdaként a vállalati webhely.

1. Hajtsa végre az alábbi lépéseket:
   
    ![HR rendszergazdai](./media/teamseer-tutorial/ic789640.png "HR-rendszergazda")  
 
    a. Lépjen a **HR rendszergazdai \> felhasználók**.
  
    b. Kattintson a **az új felhasználó varázsló futtatása**.

1. Az a **felhasználói adatok** szakaszban, hajtsa végre az alábbi lépéseket:
   
    ![Felhasználói adatok](./media/teamseer-tutorial/ic789641.png "felhasználó adatai")

    a. Írja be a **Utónév**, **Vezetéknév**, **felhasználói név (E-mail-cím)** belül üzembe helyezhető a kapcsolódó szövegmezőkben szeretné érvényes AAD-fiókok.
  
    b. Kattintson a **tovább**.

1. Kövesse a képernyőn megjelenő utasításokat az új felhasználót, kattintson **Befejezés**.

>[!NOTE]
>Eszközt is használhat bármilyen más TeamSeer felhasználói fiók létrehozása, vagy az Azure AD-felhasználói fiókok kiépítése TeamSeer által biztosított API-k. 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés TeamSeer Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel TeamSeer, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **TeamSeer**.

    ![Egyszeri bejelentkezés konfigurálása](./media/teamseer-tutorial/tutorial_teamseer_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ha szeretné tesztelni az egyszeri bejelentkezés beállításai, nyissa meg a hozzáférési panelen. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/teamseer-tutorial/tutorial_general_01.png
[2]: ./media/teamseer-tutorial/tutorial_general_02.png
[3]: ./media/teamseer-tutorial/tutorial_general_03.png
[4]: ./media/teamseer-tutorial/tutorial_general_04.png

[100]: ./media/teamseer-tutorial/tutorial_general_100.png

[200]: ./media/teamseer-tutorial/tutorial_general_200.png
[201]: ./media/teamseer-tutorial/tutorial_general_201.png
[202]: ./media/teamseer-tutorial/tutorial_general_202.png
[203]: ./media/teamseer-tutorial/tutorial_general_203.png


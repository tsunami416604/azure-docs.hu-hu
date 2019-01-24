---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező SumoLogic |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és SumoLogic között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: fbb76765-92d7-4801-9833-573b11b4d910
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2017
ms.author: jeedes
ms.openlocfilehash: 53a16977c74ebbf9e955ddd8e9d43315d71f84f6
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54808969"
---
# <a name="tutorial-azure-active-directory-integration-with-sumologic"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező SumoLogic

Ebben az oktatóanyagban elsajátíthatja, hogyan SumoLogic integrálása az Azure Active Directory (Azure AD).

SumoLogic integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá SumoLogic Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett SumoLogic (egyszeri bejelentkezés) az Azure AD-fiókjukkal
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

SumoLogic az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy SumoLogic egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. SumoLogic hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-sumologic-from-the-gallery"></a>SumoLogic hozzáadása a katalógusból
Az Azure AD integrálása a SumoLogic konfigurálásához hozzá kell SumoLogic a katalógusból a felügyelt SaaS-alkalmazások listájára.

**SumoLogic hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **SumoLogic**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/sumologic-tutorial/tutorial_sumologic_search.png)

1. Az eredmények panelen válassza ki a **SumoLogic**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/sumologic-tutorial/tutorial_sumologic_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés SumoLogic a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó SumoLogic mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó SumoLogic hivatkozás kapcsolata kell létrehozni.

SumoLogic, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az SumoLogic tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[SumoLogic tesztfelhasználó létrehozása](#creating-a-sumologic-test-user)**  – egy megfelelője a Britta Simon SumoLogic, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és SumoLogic alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés SumoLogic, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **SumoLogic** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/sumologic-tutorial/tutorial_sumologic_samlbase.png)

1. Az a **SumoLogic tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/sumologic-tutorial/tutorial_sumologic_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<tenantname>.SumoLogic.com`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe:
    | |
    |--|
    | `https://<tenantname>.us2.sumologic.com` |
    | `https://<tenantname>.sumologic.com` |
    | `https://<tenantname>.us4.sumologic.com` |
    | `https://<tenantname>.eu.sumologic.com` |
    | `https://<tenantname>.au.sumologic.com` |

    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL- és azonosító. Kapcsolattartó [SumoLogic ügyfél-támogatási csapatának](https://www.sumologic.com/contact-us/) beolvasni ezeket az értékeket. 
 
1. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/sumologic-tutorial/tutorial_sumologic_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/sumologic-tutorial/tutorial_general_400.png)

1. Az a **SumoLogic konfigurációs** területén kattintson **konfigurálása SumoLogic** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/sumologic-tutorial/tutorial_sumologic_configure.png) 

1. Egy másik böngészőablakban jelentkezzen be a SumoLogic vállalati hely rendszergazdaként.

1. Lépjen a **kezelése \> biztonsági**.
   
    ![Kezelése](./media/sumologic-tutorial/ic778556.png "kezelése")

1. Kattintson a **SAML**.
   
    ![A globális biztonsági beállításokat](./media/sumologic-tutorial/ic778557.png "globális biztonsági beállítások")

1. Az a **válassza ki a konfigurációt, vagy hozzon létre egy új** listájáról válassza ki a **Azure ad-ben**, és kattintson a **konfigurálása**.
   
    ![Konfigurálja a SAML 2.0-s](./media/sumologic-tutorial/ic778558.png "SAML 2.0 konfigurálása")

1. Az a **konfigurálása a SAML 2.0-s** párbeszédpanelen hajtsa végre az alábbi lépéseket:
   
    ![Konfigurálja a SAML 2.0-s](./media/sumologic-tutorial/ic778559.png "SAML 2.0 konfigurálása")
   
    a. Az a **konfiguráció neve** szövegmezőbe írja be **Azure ad-ben**. 

    b. Válassza ki **hibakeresési mód**.

    c. Az a **kibocsátó** szövegmező, illessze be az értéket a **SAML Entitásazonosító**, az Azure Portalról másolt. 

    d. Az a **Authn kérelem URL-Címének** szövegmezőbe, illessze be az értéket, **SAML egyszeri bejelentkezési szolgáltatás URL-cím**, az Azure Portalról másolt.

    e. Nyissa meg a base-64 kódolású tanúsítványt a Jegyzettömbben, a tartalmát a vágólapra másolja és illessze be a teljes tanúsítványt **X.509-tanúsítvány** szövegmezőbe.

    f. Mint **E-mail attribútum**, jelölje be **használható SAML-tulajdonos**.  

    g. Válassza ki **SP által kezdeményezett bejelentkezési konfigurációs**.

    h. Az a **bejelentkezési elérési** szövegmezőbe írja be **Azure** kattintson **mentése**.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/sumologic-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/sumologic-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/sumologic-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/sumologic-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-sumologic-test-user"></a>SumoLogic tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók SumoLogic jelentkezzen be, akkor ki kell építenie SumoLogic.  

* SumoLogic, esetén kiépítése a manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **SumoLogic** bérlő.

1. Lépjen a **kezelése \> felhasználók**.
   
    ![Felhasználók](./media/sumologic-tutorial/ic778561.png "felhasználók")

1. Kattintson a **Hozzáadás** parancsra.
   
    ![Felhasználók](./media/sumologic-tutorial/ic778562.png "felhasználók")

1. Az a **új felhasználó** párbeszédpanelen hajtsa végre az alábbi lépéseket:
   
    ![Új felhasználó](./media/sumologic-tutorial/ic778563.png "új felhasználó") 
 
    a. Írja be a kiépítendő be az Azure AD-fiók kapcsolódó információit a **Utónév**, **Vezetéknév**, és **E-mail** szövegmezőből.
  
    b. Válasszon ki egy szerepkört.
  
    c. Mint **állapot**válassza **aktív**.
  
    d. Kattintson a **Save** (Mentés) gombra.

>[!NOTE]
>Bármely más SumoLogic felhasználói fiók létrehozása eszközöket használhatja, vagy az aad-ben a felhasználói fiókok kiépítését SumoLogic által biztosított API-k. 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés SumoLogic Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel SumoLogic, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **SumoLogic**.

    ![Egyszeri bejelentkezés konfigurálása](./media/sumologic-tutorial/tutorial_sumologic_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ez a szakasz célja tesztelése az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a SumoLogic csempére kattint, meg kell lekérése automatikusan bejelentkezett az SumoLogic alkalmazáshoz.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/sumologic-tutorial/tutorial_general_01.png
[2]: ./media/sumologic-tutorial/tutorial_general_02.png
[3]: ./media/sumologic-tutorial/tutorial_general_03.png
[4]: ./media/sumologic-tutorial/tutorial_general_04.png

[100]: ./media/sumologic-tutorial/tutorial_general_100.png

[200]: ./media/sumologic-tutorial/tutorial_general_200.png
[201]: ./media/sumologic-tutorial/tutorial_general_201.png
[202]: ./media/sumologic-tutorial/tutorial_general_202.png
[203]: ./media/sumologic-tutorial/tutorial_general_203.png


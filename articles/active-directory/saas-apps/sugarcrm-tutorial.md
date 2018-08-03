---
title: 'Oktatóanyag: Azure Active Directory-integráció az cukor CRM |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és cukor CRM között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 3331b9fc-ebc0-4a3a-9f7b-bf20ee35d180
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: jeedes
ms.openlocfilehash: 1acaf5e530f5d5563901d8d498901ecc1bffecdb
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39427392"
---
# <a name="tutorial-azure-active-directory-integration-with-sugar-crm"></a>Oktatóanyag: Azure Active Directory-integráció az cukor CRM

Ebben az oktatóanyagban elsajátíthatja, hogyan cukor CRM integrálása az Azure Active Directory (Azure AD).

Cukor CRM integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá cukor CRM Azure AD-ben
- Az Azure AD-fiókjukat engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a cukor CRM-hez (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a cukor CRM, a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy cukor CRM egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Cukor CRM hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-sugar-crm-from-the-gallery"></a>Cukor CRM hozzáadása a katalógusból
Az Azure AD-be cukor CRM integráció konfigurálásához, hozzá kell cukor CRM a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Cukor CRM hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **cukor CRM**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/sugarcrm-tutorial/tutorial_sugarcrm_search.png)

1. Az eredmények panelen válassza ki a **cukor CRM**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/sugarcrm-tutorial/tutorial_sugarcrm_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés cukor CRM, a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben kell tudja, hogy mi a partner felhasználó cukor CRM-ben a felhasználó Azure AD-ben. Más szóval egy Azure AD-felhasználót és cukor CRM-ben a kapcsolódó felhasználó hivatkozás kapcsolatának kell létrehozni.

Cukor CRM, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés cukor CRM tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Cukor CRM tesztfelhasználó létrehozása](#creating-a-sugar-crm-test-user)**  - a-megfelelője a Britta Simon rendelkezik, amely kapcsolódik az Azure AD felhasználói ábrázolása cukor CRM-ben.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és az cukor CRM-alkalmazás egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés cukor CRM, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **cukor CRM** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/sugarcrm-tutorial/tutorial_sugarcrm_samlbase.png)

1. Az a **cukor CRM tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/sugarcrm-tutorial/tutorial_sugarcrm_url.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe:
    | |
    |--|
    | `https://<companyname>.sugarondemand.com` |
    | `https://<companyname>.trial.sugarcrm` |

    > [!NOTE] 
    > Az érték nem valódi. Frissítse az értéket a tényleges bejelentkezési URL-CÍMÉT. Kapcsolattartó [cukor CRM-ügyfél-támogatási csapatának](https://support.sugarcrm.com/) a gépkulcsengedélyek értékének. 
 
1. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/sugarcrm-tutorial/tutorial_sugarcrm_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/sugarcrm-tutorial/tutorial_general_400.png)

1. Az a **cukor CRM konfigurációs** területén kattintson **konfigurálása cukor CRM** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-címet, és a SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/sugarcrm-tutorial/tutorial_sugarcrm_configure.png) 

1. Egy másik böngészőablakban jelentkezzen be a cukor CRM vállalati hely rendszergazdaként.

1. Lépjen a **rendszergazdai**.
   
    ![Rendszergazdai](./media/sugarcrm-tutorial/ic795888.png "rendszergazda")

1. Az a **felügyeleti** területén kattintson **jelszókezelés**.
   
    ![Felügyeleti](./media/sugarcrm-tutorial/ic795889.png "felügyelete")

1. Válassza ki **SAML-hitelesítés engedélyezése**.
   
    ![Felügyeleti](./media/sugarcrm-tutorial/ic795890.png "felügyelete")

1. Az a **SAML-hitelesítés** szakaszban, hajtsa végre az alábbi lépéseket:
   
    ![SAML-hitelesítés](./media/sugarcrm-tutorial/ic795891.png "SAML-hitelesítés")  
 
    a. Az a **bejelentkezési URL-cím** szövegmezőbe, illessze be az értéket a **SAML egyszeri bejelentkezési szolgáltatás URL-cím**, az Azure Portalról másolt.
  
    b. Az a **SLO URL-cím** szövegmezőbe, illessze be az értéket a **kijelentkezéses URL-cím**, az Azure Portalról másolt.
  
    c. Nyissa meg a base-64 kódolású tanúsítványt a Jegyzettömbben, a tartalmát a vágólapra másolja és illessze be a teljes tanúsítványt **X.509-tanúsítvány** szövegmezőbe.
  
    d. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/sugarcrm-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/sugarcrm-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/sugarcrm-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/sugarcrm-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-sugar-crm-test-user"></a>Cukor CRM tesztfelhasználó létrehozása

Ahhoz, hogy jelentkezzen be a cukor CRM-hez az Azure AD-felhasználók, akkor ki kell építeni a cukor CRM-hez.

Esetén cukor CRM a manuális tevékenység kiépítése.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **cukor CRM** rendszergazdaként a vállalati webhely.

1. Lépjen a **rendszergazdai**.
   
    ![Rendszergazdai](./media/sugarcrm-tutorial/ic795888.png "rendszergazda")

1. Az a **felügyeleti** területén kattintson **felhasználókezelés**.
   
    ![Felügyeleti](./media/sugarcrm-tutorial/ic795893.png "felügyelete")

1. Lépjen a **felhasználók \> hozzon létre új felhasználót**.
   
    ![Új felhasználó létrehozása](./media/sugarcrm-tutorial/ic795894.png "új felhasználó létrehozása")

1. Az a **felhasználói profil** fülre, hajtsa végre az alábbi lépéseket:
   
    ![Új felhasználó](./media/sugarcrm-tutorial/ic795895.png "új felhasználó")

    a. Írja be a **felhasználónév**, **Vezetéknév**, és **e-mail-cím** egy érvényes Azure Active Directory felhasználó, a kapcsolódó szöveges mezőkben.
  
1. Mint **állapot**válassza **aktív**.

1. A jelszó lapon hajtsa végre az alábbi lépéseket:
   
    ![Új felhasználó](./media/sugarcrm-tutorial/ic795896.png "új felhasználó")

    a. A kapcsolódó szövegmezőbe írja be a jelszót.

    b. Kattintson a **Save** (Mentés) gombra.

>[!NOTE]
>API-k által cukor CRM aad-ben a felhasználói fiókok kiépítését vagy bármely más cukor CRM felhasználói fiók létrehozása eszközöket használhatja. 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon a hozzáférés biztosításával a cukor CRM-hez Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel cukor CRM, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **cukor CRM**.

    ![Egyszeri bejelentkezés konfigurálása](./media/sugarcrm-tutorial/tutorial_sugarcrm_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ez a szakasz célja tesztelése az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a cukor CRM csempére kattint, akkor kell lekérése automatikusan bejelentkezett az cukor CRM-alkalmazás.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/sugarcrm-tutorial/tutorial_general_01.png
[2]: ./media/sugarcrm-tutorial/tutorial_general_02.png
[3]: ./media/sugarcrm-tutorial/tutorial_general_03.png
[4]: ./media/sugarcrm-tutorial/tutorial_general_04.png

[100]: ./media/sugarcrm-tutorial/tutorial_general_100.png

[200]: ./media/sugarcrm-tutorial/tutorial_general_200.png
[201]: ./media/sugarcrm-tutorial/tutorial_general_201.png
[202]: ./media/sugarcrm-tutorial/tutorial_general_202.png
[203]: ./media/sugarcrm-tutorial/tutorial_general_203.png


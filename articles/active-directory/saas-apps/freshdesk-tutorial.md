---
title: 'Oktatóanyag: Azure Active Directory-integráció a freshdesk szolgáltatással |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a FreshDesk között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c2a3e5aa-7b5a-4fe4-9285-45dbe6e8efcc
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: d0fbed347805a581fb66e0218290993817277214
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39428332"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Oktatóanyag: Azure Active Directory-integráció a freshdesk szolgáltatással

Ebben az oktatóanyagban elsajátíthatja, hogyan átmásolása a Freshdeskbe integrálása az Azure Active Directory (Azure AD).

Freshdeskből integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá a freshdesk szolgáltatással az Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett átmásolása a freshdeskbe (egyszeri bejelentkezés) az Azure AD-fiókjukat
- A fiókok egyetlen központi helyen – az Azure felügyeleti portálján kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a freshdesk szolgáltatással, a következőkre van szükség:

- Az Azure AD-előfizetéshez
- A FreshDesk egyszeri bejelentkezéses engedélyezett előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Az éles környezetben ne használjon, ha erre szükség.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti az egy hónapos próbaidőszak [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Freshdeskből hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-freshdesk-from-the-gallery"></a>Freshdeskből hozzáadása a katalógusból
Az Azure AD integrálása a FreshDesk konfigurálásához hozzá kell átmásolása a Freshdeskbe a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Freshdeskből hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure felügyeleti portálján](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Kattintson a **Hozzáadás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **átmásolása a Freshdeskbe**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/freshdesk-tutorial/tutorial_freshdesk_search.png)

1. Az eredmények panelen válassza ki a **átmásolása a Freshdeskbe**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/freshdesk-tutorial/tutorial_freshdesk_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a freshdesk szolgáltatással a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó átmásolása a Freshdeskbe mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó a FreshDesk hivatkozás kapcsolata kell létrehozni.

Ez a hivatkozás-kapcsolat létesítéséhez értéket rendeli az **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a freshdesk szolgáltatással.

Az Azure AD egyszeri bejelentkezés vizsgálata a freshdesk szolgáltatással konfigurálni, kell hajtsa végre a következő építőelemeit:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Freshdeskből tesztfelhasználó létrehozása](#creating-a-freshdesk-test-user)**  – van egy Britta Simon megfelelője a freshdesk szolgáltatással, amely kapcsolódik az Azure ad-ben ábrázolása számára.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure felügyeleti portálon, és a freshdesk nevű alkalmazás egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálásához a freshdesk szolgáltatással, hajtsa végre az alábbi lépéseket:**

1. Az Azure felügyeleti portálon a a **átmásolása a Freshdeskbe** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédpanelen, **mód** kiválasztása **SAML-alapú bejelentkezés** való egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/freshdesk-tutorial/tutorial_freshdesk_samlbase.png)

1. A a **átmásolása a Freshdeskbe tartomány és URL-címek** területén adja meg a **bejelentkezési URL-** mint: `https://<tenant-name>.freshdesk.com` vagy bármely más érték javasolt átmásolása a Freshdeskbe.

    ![Egyszeri bejelentkezés konfigurálása](./media/freshdesk-tutorial/tutorial_freshdesk_url.png)

    > [!NOTE] 
    > Vegye figyelembe, hogy ez nem a tényleges érték. Frissítse az értéket a tényleges bejelentkezési URL-címmel rendelkezik. Kapcsolattartó [átmásolása a Freshdeskbe ügyfél-támogatási csapatának](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg) lekérni ezt az értéket.  

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány** , majd mentse a tanúsítványt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/freshdesk-tutorial/tutorial_freshdesk_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/freshdesk-tutorial/tutorial_general_400.png)

1. Az a **átmásolása a Freshdeskbe konfigurációs** területén kattintson **konfigurálása átmásolása a Freshdeskbe** konfigurálása bejelentkezési ablak megnyitásához. Másolja a SAML egyszeri bejelentkezési szolgáltatás URL-cím és kijelentkezéses URL-CÍMÉT a **rövid összefoglaló** szakaszban.

    ![Egyszeri bejelentkezés konfigurálása](./media/freshdesk-tutorial/tutorial_freshdesk_configure.png)

1. Egy másik böngészőablakban jelentkezzen be a freshdesk nevű vállalat webhelye rendszergazdaként.

1. A felső menüben kattintson **rendszergazdai**.
   
   ![Rendszergazdai](./media/freshdesk-tutorial/IC776768.png "rendszergazda")

1. Az a **általános beállítások** lapra, majd **biztonsági**.
   
   ![Biztonsági](./media/freshdesk-tutorial/IC776769.png "biztonsági")

1. Az a **biztonsági** szakaszban, hajtsa végre az alábbi lépéseket:
   
    ![Egyszeri bejelentkezés](./media/freshdesk-tutorial/IC776770.png "egyszeri bejelentkezés")
   
    a. A **egyszeri bejelentkezés (SSO)** válassza **a**.

    b. Válassza ki **SAML SSO**.

    c. Írja be a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** az Azure Portalról másolt a **SAML bejelentkezési URL-cím** szövegmezőbe.

    d. Írja be a **kijelentkezéses URL-cím** az Azure Portalról másolt a **kijelentkezési URL-címe** szövegmezőbe.

    e. Másolás a **ujjlenyomat** értéket a letöltött tanúsítvány az Azure Portalról, és illessze be azt a **biztonsági tanúsítvány-ujjlenyomat** szövegmezőbe.  
 
    >[!TIP]
    >További részletekért lásd: [hogyan kérhető le egy tanúsítvány ujjlenyomatának értékét](http://youtu.be/YKQF266SAxI). 
    
    f. Kattintson a **Save** (Mentés) gombra.


### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure felügyeleti portálján, Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure Management portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/freshdesk-tutorial/create_aaduser_01.png) 

1. Lépjen a **felhasználók és csoportok** kattintson **minden felhasználó** felhasználók listájának megjelenítéséhez.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/freshdesk-tutorial/create_aaduser_02.png) 

1. Kattintson a párbeszédpanel tetején **Hozzáadás** megnyitásához a **felhasználói** párbeszédpanel.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/freshdesk-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/freshdesk-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-freshdesk-test-user"></a>Freshdeskből tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók FreshDesk-ba való bejelentkezéshez, akkor ki kell építeni történő átmásolása a Freshdeskbe.  
Esetén a freshdesk szolgáltatással kiépítés a manuális feladat.

**A felhasználói fiókok kiépítése, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **átmásolása a Freshdeskbe** bérlő.
1. A felső menüben kattintson **rendszergazdai**.
   
   ![Rendszergazdai](./media/freshdesk-tutorial/IC776772.png "rendszergazda")

1. Az a **általános beállítások** lapra, majd **ügynökök**.
   
   ![Ügynökök](./media/freshdesk-tutorial/IC776773.png "ügynökök")

1. Kattintson a **új ügynök**.
   
    ![Új ügynök](./media/freshdesk-tutorial/IC776774.png "új ügynök")

1. Az ügynök adatait párbeszédpanelen hajtsa végre az alábbi lépéseket:
   
   ![Az ügynök adatait](./media/freshdesk-tutorial/IC776775.png "ügynök adatait")
   
   a. Az a **teljes fájlvisszaállítási név** szövegmezőbe írja be a kíván üzembe helyezni az Azure AD-fiók nevét.

   b. Az a **E-mail** szövegmezőbe írja be az Azure AD e-mail címét, az Azure AD-szolgáltatásfiók kíván üzembe helyezni.

   c. Az a **cím** szövegmezőbe írja be az Azure AD-szolgáltatásfiók kíván üzembe helyezni.

   d. Válassza ki **ügynökök szerepkör**, és kattintson a **hozzárendelése**.
       
   e. Kattintson a **Save** (Mentés) gombra.     
   
    >[!NOTE]
    >Az Azure ad-ben fióktulajdonos előtt aktiválva van, győződjön meg arról, hogy a fiók mutató hivatkozást tartalmazó e-mailt fog kapni. 
    > 
    
    >[!NOTE]
    >Bármely más átmásolása a Freshdeskbe felhasználói fiók létrehozása eszközöket használhatja, vagy az aad-ben a felhasználói fiókok kiépítését átmásolása a Freshdeskbe által biztosított API-k. átmásolása a freshdeskbe.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon számára a hozzáférés biztosításával a Boxba Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon átmásolása a freshdeskbe rendelni, végezze el az alábbi lépéseket:**

1. Az Azure felügyeleti portálon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **átmásolása a Freshdeskbe**.

    ![Egyszeri bejelentkezés konfigurálása](./media/freshdesk-tutorial/tutorial_freshdesk_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a FreshDesk csempére kattint, első bejelentkezett a FreshDesk-alkalmazásba való bejelentkezési oldal szerezheti be.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/freshdesk-tutorial/tutorial_general_01.png
[2]: ./media/freshdesk-tutorial/tutorial_general_02.png
[3]: ./media/freshdesk-tutorial/tutorial_general_03.png
[4]: ./media/freshdesk-tutorial/tutorial_general_04.png

[100]: ./media/freshdesk-tutorial/tutorial_general_100.png

[200]: ./media/freshdesk-tutorial/tutorial_general_200.png
[201]: ./media/freshdesk-tutorial/tutorial_general_201.png
[202]: ./media/freshdesk-tutorial/tutorial_general_202.png
[203]: ./media/freshdesk-tutorial/tutorial_general_203.png


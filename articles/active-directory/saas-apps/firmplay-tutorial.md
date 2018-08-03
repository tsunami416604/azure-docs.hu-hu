---
title: 'Oktatóanyag: Azure Active Directory-integráció az FirmPlay – a személyzeti osztályon dolgozók Ügyfélképviseletben |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés FirmPlay – a személyzeti osztályon dolgozók Ügyfélképviseletben és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: a6799629-7546-43f8-a966-956db32864b1
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: jeedes
ms.openlocfilehash: 6e3d8a3951a42e448eff005b055b3cf5169179f1
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39443449"
---
# <a name="tutorial-azure-active-directory-integration-with-firmplay---employee-advocacy-for-recruiting"></a>Oktatóanyag: Azure Active Directory-integráció az FirmPlay – a személyzeti osztályon dolgozók Ügyfélképviseletben

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálhatja FirmPlay – az Azure Active Directory (Azure AD) a személyzeti osztályon dolgozók Ügyfélképviseletben.

FirmPlay – az Azure AD-vel a személyzeti osztályon dolgozók Ügyfélképviseletben integrálása nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá FirmPlay – a személyzeti osztályon dolgozók Ügyfélképviseletben Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett FirmPlay - alkalmazott Ügyfélképviseletben a személyzeti osztályon (egyszeri bejelentkezés) az Azure AD-fiókjukat
- A fiókok egyetlen központi helyen – az Azure felügyeleti portálján kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

FirmPlay - alkalmazott Ügyfélképviseletben a személyzeti osztályon, az Azure AD-integráció konfigurálása a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy FirmPlay - alkalmazott Ügyfélképviseletben felvételi engedélyezett előfizetés egyszeri bejelentkezés


> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.


Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Az éles környezetben ne használjon, ha erre szükség.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti az egy hónapos próbaidőszak [Itt](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Hozzáadás FirmPlay - alkalmazott Ügyfélképviseletben a személyzeti osztályon a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés


## <a name="adding-firmplay---employee-advocacy-for-recruiting-from-the-gallery"></a>Hozzáadás FirmPlay - alkalmazott Ügyfélképviseletben a személyzeti osztályon a katalógusból
FirmPlay – az Azure ad-ben, a személyzeti osztályon dolgozók Ügyfélképviseletben integráció konfigurálásához hozzá kell FirmPlay - alkalmazott Ügyfélképviseletben a személyzeti osztályon a galériából a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a FirmPlay - alkalmazott Ügyfélképviseletben a személyzeti osztályon katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure felügyeleti portálján](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Kattintson a **Hozzáadás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **FirmPlay – a személyzeti osztályon dolgozók Ügyfélképviseletben**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/firmplay-tutorial/tutorial_firmplay_001.png)

1. Az eredmények panelen válassza ki a **FirmPlay – a személyzeti osztályon dolgozók Ügyfélképviseletben**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/firmplay-tutorial/tutorial_firmplay_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az FirmPlay - alkalmazott Ügyfélképviseletben a személyzeti osztályon a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés használatát Azure ad-ben tudnia kell, milyen a partner felhasználó FirmPlay – a személyzeti osztályon dolgozók Ügyfélképviseletben, hogy egy felhasználó Azure AD-ben. Más szóval egy hivatkozás közötti kapcsolat egy Azure AD-felhasználót és a kapcsolódó felhasználó FirmPlay - alkalmazott Ügyfélképviseletben felvételi kell létrehozni.

Ez a hivatkozás-kapcsolat létesítéséhez értéket rendeli az **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** FirmPlay – a személyzeti osztályon dolgozók Ügyfélképviseletben a.

Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés FirmPlay – a személyzeti osztályon, az alkalmazott Ügyfélképviseletben kell hajtsa végre a következő építőelemeket:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Egy FirmPlay - alkalmazott Ügyfélképviseletben a személyzeti osztályon tesztfelhasználó létrehozása](#creating-a-firmplay---employee-advocacy-for-recruiting-test-user)**  – van egy megfelelője a Britta Simon FirmPlay: alkalmazott Ügyfélképviseletben felvételi, amely csatolva az Azure ad-ben ábrázolása számára.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure felügyeleti portálon, és a FirmPlay - alkalmazott Ügyfélképviseletben személyzeti osztályon alkalmazáshoz az egyszeri bejelentkezés konfigurálása.

**FirmPlay - alkalmazott Ügyfélképviseletben a személyzeti osztályon, az Azure AD egyszeri bejelentkezés konfigurálásához hajtsa végre az alábbi lépéseket:**

1. Az Azure felügyeleti portálon a a **FirmPlay – a személyzeti osztályon dolgozók Ügyfélképviseletben** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédpanelen, **mód** kiválasztása **SAML-alapú bejelentkezés** való egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/firmplay-tutorial/tutorial_firmplay_01.png)

1. Az a **FirmPlay - alkalmazott Ügyfélképviseletben felvételi tartomány és URL-címek** ebben a szakaszban a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<your-subdomain>.firmplay.com/`

    ![Egyszeri bejelentkezés konfigurálása](./media/firmplay-tutorial/tutorial_firmplay_02.png)

    > [!NOTE] 
    > Vegye figyelembe, hogy ez nem a tényleges érték. Frissítse ezt az értéket a tényleges bejelentkezési URL-címet kell. Kapcsolattartó [FirmPlay - személyzeti osztályon támogatási csapat alkalmazott Ügyfélképviseletben](mailto:engineering@firmplay.com) lekérni ezt az értéket. 

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **új tanúsítvány létrehozása**.

    ![Egyszeri bejelentkezés konfigurálása](./media/firmplay-tutorial/tutorial_firmplay_03.png)     

1. Az a **új tanúsítvány létrehozása** párbeszédpanelen kattintson a naptár ikonra, és válassza ki egy **lejárati dátuma**. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/firmplay-tutorial/tutorial_general_300.png)

1. Az a **SAML-aláíró tanúsítvány** szakaszban jelölje be **új tanúsítvány aktívvá** kattintson **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/firmplay-tutorial/tutorial_firmplay_04.png)

1. Az előugró **helyettesítő tanúsítvány** ablakban kattintson a **OK**.

    ![Egyszeri bejelentkezés konfigurálása](./media/firmplay-tutorial/tutorial_general_400.png)

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (base64)** , és mentse a tanúsítványfájlt, a számítógépen. 

    ![Egyszeri bejelentkezés konfigurálása](./media/firmplay-tutorial/tutorial_firmplay_05.png) 

1. Az a **FirmPlay - konfiguráció felvételi alkalmazott Ügyfélképviseletben** területén kattintson **FirmPlay konfigurálása - a személyzeti osztályon dolgozók Ügyfélképviseletben** megnyitásához **bejelentkezés konfigurálása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/firmplay-tutorial/tutorial_firmplay_06.png) 

    ![Egyszeri bejelentkezés konfigurálása](./media/firmplay-tutorial/tutorial_firmplay_07.png)

1. Egyszeri bejelentkezés az alkalmazáshoz konfigurált beszerzéséhez forduljon [FirmPlay - személyzeti osztályon támogatási csapat alkalmazott Ügyfélképviseletben](mailto:engineering@firmplay.com) és adja meg a következő: 

    • A letöltött **tanúsítványfájlt**

    • A **SAML egyszeri bejelentkezési szolgáltatás URL-címe**

    • A **SAML entitás azonosítója**

    • A **kijelentkezési URL-cím**
  

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure felügyeleti portálján, Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure Management portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/firmplay-tutorial/create_aaduser_01.png) 

1. Lépjen a **felhasználók és csoportok** kattintson **minden felhasználó** felhasználók listájának megjelenítéséhez.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/firmplay-tutorial/create_aaduser_02.png) 

1. Kattintson a párbeszédpanel tetején **Hozzáadás** megnyitásához a **felhasználói** párbeszédpanel.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/firmplay-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/firmplay-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra. 



### <a name="creating-a-firmplay---employee-advocacy-for-recruiting-test-user"></a>Egy FirmPlay - alkalmazott Ügyfélképviseletben a személyzeti osztályon tesztfelhasználó létrehozása

Ebben a szakaszban egy felhasználói Britta Simon nevű FirmPlay – a személyzeti osztályon dolgozók Ügyfélképviseletben hoz létre. Együttműködve [FirmPlay - személyzeti osztályon támogatási csapat alkalmazott Ügyfélképviseletben](mailto:engineering@firmplay.com) felhasználót is hozzáadhat a FirmPlay - alkalmazott Ügyfélképviseletben személyzeti osztályon platform.


### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon Azure egyszeri bejelentkezés FirmPlay – a személyzeti osztályon dolgozók Ügyfélképviseletben saját hozzáférésének engedélyezésére használja.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése FirmPlay - alkalmazott Ügyfélképviseletben a személyzeti osztályon, hajtsa végre az alábbi lépéseket:**

1. Az Azure felügyeleti portálon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **FirmPlay – a személyzeti osztályon dolgozók Ügyfélképviseletben**.

    ![Egyszeri bejelentkezés konfigurálása](./media/firmplay-tutorial/tutorial_firmplay_50.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    


### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

FirmPlay – a hozzáférési panelen, a személyzeti osztályon csempe alkalmazott Ügyfélképviseletben kattintás után meg kell lekérése automatikusan bejelentkezett a FirmPlay - alkalmazott Ügyfélképviseletben személyzeti osztályon alkalmazáshoz való.


## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/firmplay-tutorial/tutorial_general_01.png
[2]: ./media/firmplay-tutorial/tutorial_general_02.png
[3]: ./media/firmplay-tutorial/tutorial_general_03.png
[4]: ./media/firmplay-tutorial/tutorial_general_04.png

[100]: ./media/firmplay-tutorial/tutorial_general_100.png

[200]: ./media/firmplay-tutorial/tutorial_general_200.png
[201]: ./media/firmplay-tutorial/tutorial_general_201.png
[202]: ./media/firmplay-tutorial/tutorial_general_202.png
[203]: ./media/firmplay-tutorial/tutorial_general_203.png
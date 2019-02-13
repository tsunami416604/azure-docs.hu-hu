---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Work.com |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Work.com között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 98e6739e-eb24-46bd-9dd3-20b489839076
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1e5d097afa3af111a7de8341b90d8c6c05ac0347
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56205749"
---
# <a name="tutorial-azure-active-directory-integration-with-workcom"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Work.com

Ebben az oktatóanyagban elsajátíthatja, hogyan Work.com integrálása az Azure Active Directory (Azure AD).

Work.com integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá Work.com Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Work.com (egyszeri bejelentkezés) az Azure AD-fiókjukkal
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Work.com az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy Work.com egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Work.com hozzáadása a katalógusból
1. Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

## <a name="add-workcom-from-the-gallery"></a>Work.com hozzáadása a katalógusból
Az Azure AD integrálása a Work.com konfigurálásához hozzá kell Work.com a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Work.com hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **Work.com**válassza **Work.com** az eredmények panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Katalógus hozzáadása](./media/work-com-tutorial/tutorial_work-com_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Work.com a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Work.com mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Work.com hivatkozás kapcsolata kell létrehozni.

Work.com, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az Work.com tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy Work.com tesztfelhasználót](#create-a-workcom-test-user)**  – egy megfelelője a Britta Simon Work.com, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Work.com alkalmazását az egyszeri bejelentkezés konfigurálása.

>[!NOTE]
>Egyszeri bejelentkezés konfigurálásához kell még a Work.com egyéni tartománynév beállítása. Legalább egy tartománynevet, tesztelje a tartománynevet, és telepítheti a teljes vállalat szüksége.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Work.com, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Work.com** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![SAML-alapú bejelentkezés](./media/work-com-tutorial/tutorial_work-com_samlbase.png)

1. Az a **Work.com tartomány és URL-címek** területén tegye a következőket:

    ![A szakasz Work.com tartomány és URL-címek](./media/work-com-tutorial/tutorial_work-com_url.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `http://<companyname>.my.salesforce.com`

    > [!NOTE] 
    > Az érték nem valódi. Ez az érték frissítse a tényleges bejelentkezési URL-CÍMÉT. Kapcsolattartó [Work.com ügyfél-támogatási csapatának](https://help.salesforce.com/articleView?id=000159855&type=3) lekérni ezt az értéket. 

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![SAML-aláíró tanúsítvány szakaszban](./media/work-com-tutorial/tutorial_work-com_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Mentés gomb](./media/work-com-tutorial/tutorial_general_400.png)

1. Az a **Work.com konfigurációs** területén kattintson **konfigurálása Work.com** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Work.com konfigurációs szakasz](./media/work-com-tutorial/tutorial_work-com_configure.png) 
1. Jelentkezzen be rendszergazdaként a Work.com bérlő.

1. Lépjen a **telepítő**.
   
    ![A telepítő](./media/work-com-tutorial/ic794108.png "beállítása")

1. A bal oldali navigációs ablaktáblán a a **Administer** területén kattintson **tartományok kezelése** bontsa ki a kapcsolódó csomópontot, majd **saját tartomány** megnyitásához a **Saját tartomány** lapot. 
   
    ![Saját tartomány](./media/work-com-tutorial/ic767825.png "saját tartomány")

1. Győződjön meg arról, hogy a tartomány megfelelően van beállítva, győződjön meg arról, hogy "**4. lépés telepíti a felhasználók számára**", és tekintse át a "**beállításaim tartomány**".
   
    ![Tartományi felhasználó](./media/work-com-tutorial/ic784377.png "tartományi felhasználóra")

1. Jelentkezzen be a Work.com bérlő.

1. Lépjen a **telepítő**.
    
    ![A telepítő](./media/work-com-tutorial/ic794108.png "beállítása")

1. Bontsa ki a **biztonsági vezérlők** menüre, majd **egyszeri bejelentkezési beállításainak**.
    
    ![Egyszeri bejelentkezés beállításai](./media/work-com-tutorial/ic794113.png "egyszeri bejelentkezés beállításai")

1. Az a **egyszeri bejelentkezési beállításainak** párbeszédpanel lapon, a következő lépésekkel:
    
    ![A SAML engedélyezett](./media/work-com-tutorial/ic781026.png "SAML engedélyezve")
    
    a. Válassza ki **SAML engedélyezett**.
    
    b. Kattintson az **Új** lehetőségre.

1. Az a **SAML egyszeri bejelentkezési beállításainak** szakaszban, hajtsa végre az alábbi lépéseket:
    
    ![SAML egyszeri bejelentkezési beállítás](./media/work-com-tutorial/ic794114.png "SAML egyszeri bejelentkezési beállítás")
    
    a. Az a **neve** szövegmezőbe írja be a konfiguráció nevét.  
       
    > [!NOTE]
    > Amely egy értéket a **neve** automatikusan feltölti a **API neve** szövegmezőbe.
    
    b. A **kibocsátó** szövegmezőjébe illessze be az értéket, **SAML Entitásazonosító** Azure Portalról másolt.
    
    c. Az Azure Portalról letöltött tanúsítvány feltöltéséhez kattintson **Tallózás**.
    
    d. Az a **entitásazonosító** szövegmezőbe írja be `https://salesforce-work.com`.
    
    e. Mint **SAML identitástípus**válassza **helyességi feltétel tartalmazza a felhasználói objektum összevonási Azonosítóját**.
    
    f. Mint **SAML identitás hely**, jelölje be **identitás a tulajdonos utasítás NameIdentfier elemében van**.
    
    g. A **Identity Provider bejelentkezési URL-cím** szövegmező, illessze be az értéket a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** Azure Portalról másolt.

    h. A **Identity Provider kijelentkezési URL-címe** szövegmezőjébe illessze be az értéket, **kijelentkezéses URL-cím** Azure Portalról másolt.
    
    i. Mint **szolgáltató által kezdeményezett kérelem Szolgáltatáskötést**válassza **HTTP Post**.
    
    j. Kattintson a **Save** (Mentés) gombra.

1. A Work.com klasszikus portálon, a bal oldali navigációs ablaktáblán kattintson a **tartományok** bontsa ki a kapcsolódó csomópontot, majd **saját tartomány** megnyitásához a **saját tartomány** oldal. 
    
    ![Saját tartomány](./media/work-com-tutorial/ic794115.png "saját tartomány")

1. Az a **saját tartomány** lap a **bejelentkezési oldal márkajelzési** területén kattintson **szerkesztése**.
    
    ![Bejelentkezési oldal márkajelzési](./media/work-com-tutorial/ic767826.png "arculat megjelenítése a bejelentkezési oldal")

1. Az a **bejelentkezési oldal márkajelzési** lap a **hitelesítési szolgáltatás** részben, a neve a **SAML egyszeri bejelentkezési beállításainak** jelenik meg. Válassza ki, és kattintson a **mentése**.
    
    ![Bejelentkezési oldal márkajelzési](./media/work-com-tutorial/ic784366.png "arculat megjelenítése a bejelentkezési oldal")

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/work-com-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Felhasználók és csoportok -> minden felhasználó](./media/work-com-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Hozzáadás](./media/work-com-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Felhasználói párbeszédpanel lap](./media/work-com-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-workcom-test-user"></a>Work.com tesztfelhasználó létrehozása
Azure Active Directory-felhasználók számára jelentkezhetnek be akkor ki kell építenie Work.com. Work.com, esetén kiépítése a manuális feladat.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Felhasználók átadásának konfigurálása, hajtsa végre az alábbi lépéseket:
1. Jelentkezzen be rendszergazdaként a Work.com vállalati webhely.

1. Lépjen a **telepítő**.
   
    ![A telepítő](./media/work-com-tutorial/IC794108.png "beállítása")
1. Lépjen a **felhasználók kezelése \> felhasználók**.
   
    ![Felhasználók kezelése](./media/work-com-tutorial/IC784369.png "felhasználók kezelése")

1. Kattintson a **új felhasználó**.
   
    ![Minden felhasználó](./media/work-com-tutorial/IC794117.png "minden felhasználó")

1. A felhasználó szerkesztheti a szakaszban a következő lépésekkel, az attribútumok egy érvényes Azure AD-fiókot szeretné a kapcsolódó szövegmezőkben létrehozásához:
   
    ![Felhasználó szerkesztése](./media/work-com-tutorial/ic794118.png "felhasználó szerkesztése")
   
    a. Az a **Utónév** szövegmezőbe írja be a **Utónév** felhasználó **Britta**.
    
    b. Az a **Vezetéknév** szövegmezőbe írja be a **Vezetéknév** felhasználó **Simon**.
    
    c. Az a **Alias** szövegmezőbe írja be a **neve** felhasználó **BrittaS**.
    
    d. Az a **E-mail** szövegmezőbe írja be a **e-mail-cím** felhasználó **Brittasimon@contoso.com**.
    
    e. Az a **felhasználónév** beviteli mező, írja be a felhasználónevet a felhasználó például **Brittasimon@contoso.com**.
    
    f. Az a **becenév** szövegmezőben adjon meg egy **becenév** felhasználó **Simon**.
    
    g. Válassza ki **szerepkör**, **felhasználói licenc**, és **profil**.
    
    h. Kattintson a **Save** (Mentés) gombra.  
      
    > [!NOTE]
    > Az Azure ad-ben fióktulajdonos fog kapni egy e-mailt és a telepítőre mutató erősítse meg a fiókot, mielőtt aktívvá válik.
    > 
    > 

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Work.com Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel Work.com, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Work.com**.

    ![Alkalmazás listában Work.com](./media/work-com-tutorial/tutorial_work-com_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Work.com csempére kattint, meg kell lekérése automatikusan bejelentkezett az Work.com alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/work-com-tutorial/tutorial_general_01.png
[2]: ./media/work-com-tutorial/tutorial_general_02.png
[3]: ./media/work-com-tutorial/tutorial_general_03.png
[4]: ./media/work-com-tutorial/tutorial_general_04.png

[100]: ./media/work-com-tutorial/tutorial_general_100.png

[200]: ./media/work-com-tutorial/tutorial_general_200.png
[201]: ./media/work-com-tutorial/tutorial_general_201.png
[202]: ./media/work-com-tutorial/tutorial_general_202.png
[203]: ./media/work-com-tutorial/tutorial_general_203.png


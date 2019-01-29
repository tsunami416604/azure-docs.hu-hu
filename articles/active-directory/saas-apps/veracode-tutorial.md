---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Veracode |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Veracode között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 4fe78050-cb6d-4db9-96ec-58cc0779167f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: 87eff8eb96f2a6b090883e0b557a568eb0a20d54
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55156335"
---
# <a name="tutorial-azure-active-directory-integration-with-veracode"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Veracode

Ebben az oktatóanyagban elsajátíthatja, hogyan Veracode integrálása az Azure Active Directory (Azure AD).

Veracode integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá Veracode Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Veracode (egyszeri bejelentkezés), az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Veracode az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy Veracode egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Veracode hozzáadása a katalógusból
1. Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

## <a name="add-veracode-from-the-gallery"></a>Veracode hozzáadása a katalógusból
Az Azure AD integrálása a Veracode konfigurálásához hozzá kell Veracode a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Veracode hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **Veracode**válassza **Veracode** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában Veracode](./media/veracode-tutorial/tutorial_veracode_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Veracode a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Veracode mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Veracode hivatkozás kapcsolata kell létrehozni.

Veracode, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az Veracode tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy Veracode tesztfelhasználót](#create-a-veracode-test-user)**  – egy megfelelője a Britta Simon Veracode, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Veracode alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Veracode, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Veracode** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/veracode-tutorial/tutorial_veracode_samlbase.png)

1. Az a **Veracode tartomány és URL-címek** szakaszban, a felhasználónak nem kell végrehajthatja a lépéseket, ahogy az alkalmazás már előre integrálva van az Azure-ral. 

    ![Egyszeri bejelentkezés konfigurálása](./media/veracode-tutorial/tutorial_veracode_url.png)

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/veracode-tutorial/tutorial_veracode_certificate.png) 

1. Ez a szakasz célja kidolgozására engedélyezése a felhasználóknak a hitelesítést Veracode fiókkal az Azure AD összevonási SAML protokoll használatával.

    Veracode alkalmazását a SAML helyességi feltételek vár egy megadott formátumban, amelyhez egyéni attribútumleképezések való hozzáadása a **saml-jogkivonat attribútumai** konfigurációja. Az alábbi képernyőfelvételen látható erre egy példa látható.
    
    ![Attribútumok](./media/veracode-tutorial/tutorial_veracode_attr.png "attribútumok")

1. Adja hozzá a szükséges attribútumleképezések, hajtsa végre az alábbi lépéseket:

    | Attribútum neve | Attribútum értéke |
    |--- |--- |
    | Keresztnév |User.givenname |
    | Vezetéknév |User.surname |
    | e-mail |User.mail |
    
    a. Kattintson a fenti táblázatban szereplő minden egyes adatok sorban **adja hozzá a felhasználói attribútum**.
    
    ![Attribútumok](./media/veracode-tutorial/tutorial_veracode_addattr.png "attribútumok")
    
    ![Attribútumok](./media/veracode-tutorial/tutorial_veracode_addattr1.png "attribútumok")
    
    b. Az a **attribútumnév** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.
    
    c. Az a **attribútumérték** szövegmezőben válassza ki az adott sorhoz feltüntetett attribútum értéke.
    
    d. Kattintson az **OK** gombra.

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/veracode-tutorial/tutorial_general_400.png)

1. Az a **Veracode konfigurációs** területén kattintson **konfigurálása Veracode** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító** származó a **gyors útmutató szakaszban.**

    ![Veracode konfiguráció](./media/veracode-tutorial/tutorial_veracode_configure.png) 

1. Egy másik böngészőablakban jelentkezzen be a Veracode vállalati hely rendszergazdaként.

1. A felső menüben kattintson **beállítások**, és kattintson a **rendszergazdai**.
   
    ![Felügyeleti](./media/veracode-tutorial/ic802911.png "felügyelete")

1. Kattintson a **SAML** fülre.

1. Az a **szervezet SAML-beállítások** szakaszban, hajtsa végre az alábbi lépéseket:
   
    ![Felügyeleti](./media/veracode-tutorial/ic802912.png "felügyelete")
   
    a.  A **kibocsátó** szövegmezőjébe illessze be az értéket, **SAML Entitásazonosító** Azure Portalról másolt.
    
    b. Az Azure Portalról letöltött tanúsítvány feltöltéséhez kattintson **fájl kiválasztása**.
   
    c. Válassza ki **önkiszolgáló regisztráció engedélyezése**.

1. Az a **önkiszolgáló regisztrációs beállításaira** szakaszt, hajtsa végre az alábbi lépéseket, és kattintson a **mentése**:
   
    ![Felügyeleti](./media/veracode-tutorial/ic802913.png "felügyelete")
   
    a. Mint **új felhasználó az aktiválás**válassza **nincs szükség aktiválásra**.
   
    b. Mint **felhasználói adatok frissítése**válassza **szabályozó Veracode felhasználói adatok**.
   
    c. A **SAML attribútum részletei**, jelölje be az alábbiakat:
      * **Felhasználói szerepkörök**
      * **Csoportházirendet felügyelő rendszergazda**
      * **Felülvizsgáló**
      * **Biztonsági vezető**
      * **Executive**
      * **Submitter**
      * **Létrehozója**
      * **Az összes vizsgálat típusa**
      * **A csoporttagságot**
      * **Alapértelmezett csoport**

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/veracode-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/veracode-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/veracode-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/veracode-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-veracode-test-user"></a>Veracode tesztfelhasználó létrehozása
Ahhoz, hogy az Azure AD-felhasználók Veracode szolgáltatásba való bejelentkezéshez, akkor ki kell építeni Veracode be. Veracode, esetén egy automatizált tevékenység kiépítése. Nincs művelet elem az Ön számára. Felhasználók automatikusan jönnek létre szükség esetén az első egyszeri bejelentkezési kísérlet során.

> [!NOTE]
> Eszközt is használhat bármilyen más Veracode felhasználói fiók létrehozása, vagy az Azure AD-felhasználói fiókok kiépítése Veracode által biztosított API-k.
> 

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Veracode Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel Veracode, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Veracode**.

    ![Az alkalmazások listáját a Veracode hivatkozásra](./media/veracode-tutorial/tutorial_veracode_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Veracode csempére kattint, meg kell lekérése automatikusan bejelentkezett az Veracode alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/veracode-tutorial/tutorial_general_01.png
[2]: ./media/veracode-tutorial/tutorial_general_02.png
[3]: ./media/veracode-tutorial/tutorial_general_03.png
[4]: ./media/veracode-tutorial/tutorial_general_04.png

[100]: ./media/veracode-tutorial/tutorial_general_100.png

[200]: ./media/veracode-tutorial/tutorial_general_200.png
[201]: ./media/veracode-tutorial/tutorial_general_201.png
[202]: ./media/veracode-tutorial/tutorial_general_202.png
[203]: ./media/veracode-tutorial/tutorial_general_203.png


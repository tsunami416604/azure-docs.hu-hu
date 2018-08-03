---
title: 'Oktatóanyag: Azure Active Directory-integráció az Dome9 ív |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Dome9 ív között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4c12875f-de71-40cb-b9ac-216a805334e5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: jeedes
ms.openlocfilehash: 944f1b7ecc3fdc9f063d764a6deae49a68a24d77
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39433527"
---
# <a name="tutorial-azure-active-directory-integration-with-dome9-arc"></a>Oktatóanyag: Azure Active Directory-integráció az Dome9 ívvé

Ebben az oktatóanyagban elsajátíthatja, hogyan Dome9 ív integrálása az Azure Active Directory (Azure AD).

Dome9 ív integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá Dome9 ív Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Dome9 ívvé (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Dome9 ív konfigurálni az Azure AD-integráció, a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy Dome9 ív egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Dome9 ív hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-dome9-arc-from-the-gallery"></a>Dome9 ív hozzáadása a katalógusból
Az Azure AD-be Dome9 ív integráció konfigurálásához, hozzá kell Dome9 Arc a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Dome9 ív hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **Dome9 ív**, jelölje be **Dome9 ív** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában Dome9 ívvé](./media/dome9arc-tutorial/tutorial_dome9arc_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Dome9 Arc a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Dome9 ív mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Dome9 ív hivatkozás kapcsolata kell létrehozni.

Dome9 ív, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az Dome9 ív tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy Dome9 ív tesztfelhasználót](#create-a-dome9-arc-test-user)**  – egy megfelelője a Britta Simon Dome9 ív, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Dome9 ív alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Dome9 ív, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Dome9 ív** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/dome9arc-tutorial/tutorial_dome9arc_samlbase.png)

1. Az a **Dome9 ív tartomány és URL-címek** területén kövesse az alábbi lépéseket, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód:

    ![Dome9 ív tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/dome9arc-tutorial/tutorial_dome9arc_url.png)

    a. Az a **azonosító** szövegmezőbe írja be az URL-cím: `https://secure.dome9.com/`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://secure.dome9.com/sso/saml/yourcompanyname`

    > [!NOTE]
    > A vállalati név-érték a dome9 felügyeleti portálon, az oktatóanyag későbbi részében ismertetett kiválaszthatja.

1. Ellenőrizze **speciális URL-beállítások megjelenítése** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![Dome9 ív tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/dome9arc-tutorial/tutorial_dome9arc_url1.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://secure.dome9.com/sso/saml/<yourcompanyname>`
     
    > [!NOTE] 
    > Ezek a értékei nem valódi. Frissítse a tényleges válasz URL-cím és a bejelentkezési URL-ezeket az értékeket. Kapcsolattartó [Dome9 ív ügyfél-támogatási csapatának](https://dome9.com/about/contact-us/) beolvasni ezeket az értékeket. 

1. A Dome9 ív szoftveralkalmazás a SAML helyességi feltételek vár egy megadott formátumban. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a "**felhasználói attribútumok**" szakasz alkalmazás integráció lapján. Az alábbi képernyőfelvételen látható erre egy példa látható.

    ![Egyszeri bejelentkezés attb konfigurálása](./media/dome9arc-tutorial/tutorial_dome9arc_attribute.png)

1. Az a **felhasználói attribútumok** szakaszában a **egyszeri bejelentkezési** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, a fenti képen látható módon, és hajtsa végre az alábbi lépéseket:
    
    | Attribútum neve  | Attribútum értéke | 
    | --------------- | --------------- | 
    | tagja(Pénzügy) | User.assignedroles | 
    
    a. Kattintson a **attribútum hozzáadása** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása attb hozzáadása](./media/dome9arc-tutorial/tutorial_dome9_04.png)

    ![Szerkesztés attb egyszeri bejelentkezés konfigurálása](./media/dome9arc-tutorial/tutorial_attribute_05.png)

    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    c. Az a **érték** list, írja be az adott sorhoz feltüntetett attribútumot értéket.
    
    d. Kattintson az **OK** gombra.

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/dome9arc-tutorial/tutorial_dome9arc_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/dome9arc-tutorial/tutorial_general_400.png)
    
1. Az a **Dome9 ív konfigurációs** területén kattintson **konfigurálása Dome9 ív** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Dome9 ív konfiguráció](./media/dome9arc-tutorial/tutorial_dome9arc_configure.png) 

1. Egy másik böngészőablakban jelentkezzen be a Dome9 ív vállalati hely rendszergazdaként.

1. Kattintson a a **Profilbeállítások** elemre a jobb felső sarokban, majd kattintson **fiókbeállításokat**. 

    ![Dome9 ív konfiguráció](./media/dome9arc-tutorial/configure1.png)

1. Navigáljon a **SSO** majd **engedélyezése**.

    ![Dome9 ív konfiguráció](./media/dome9arc-tutorial/configure2.png)

1. Az egyszeri bejelentkezés konfigurációs szakaszban hajtsa végre az alábbi lépéseket:

    ![Dome9 ív konfiguráció](./media/dome9arc-tutorial/configure3.png)

    a. Adja meg a cég neve a **Fiókazonosító** szövegmezőbe. Ezt az értéket, hogy a válasz URL-cím az Azure portál URL-cím szakaszban említett használható.

    b. Az a **kibocsátó** szövegmezőbe, illessze be az értéket, **SAML Entitásazonosító**, amely az Azure Portalról másolta.

    c. Az a **Idp-végpont URL-címe** szövegmezőbe, illessze be az értéket a **SAML egyszeri bejelentkezési szolgáltatás URL-cím**, amely az Azure Portalról másolt.

    d. Nyissa meg a letöltött Base64-kódolású tanúsítványt a Jegyzettömbben, a tartalmát a vágólapra másolja és illessze be azt a **X.509-tanúsítvány** szövegmezőbe.

    e. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/dome9arc-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/dome9arc-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/dome9arc-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/dome9arc-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-dome9-arc-test-user"></a>Dome9 ív tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Dome9 ív jelentkezzen be, akkor ki kell építeni alkalmazásba. Dome9 ív támogatja a just-in-time-kiépítés, de számára, hogy megfelelően működjön, a felhasználó rendelkezik, válassza ki az adott **szerepkör** és azonos hozzárendelése a felhasználóhoz.

   >[!Note] 
   >A **szerepkör** létrehozása és egyéb részletek ügyfél [Dome9 ív ügyfél-támogatási csapatának](https://dome9.com/about/contact-us/).

**Hozza létre manuálisan egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a Dome9 ív vállalati webhely.

1. Kattintson a a **felhasználók és szerepkörök** majd **felhasználók**.

    ![Alkalmazott hozzáadása](./media/dome9arc-tutorial/user1.png)

1. Kattintson a **felhasználó hozzáadása**.

    ![Alkalmazott hozzáadása](./media/dome9arc-tutorial/user2.png)

1. Az a **Create User** szakaszban, hajtsa végre az alábbi lépéseket:
    
    ![Alkalmazott hozzáadása](./media/dome9arc-tutorial/user3.png)

    a. Az a **E-mail** szövegmezőbe írja be az e-mailt, felhasználó, például Brittasimon@contoso.com.

    b. Az a **Utónév** szövegmezőbe írja be keresztnevét Britta például a felhasználó.

    c. Az a **Vezetéknév** szövegmezőbe írja be a felhasználó például Simon vezetékneve.

    d. Győződjön meg arról, **egyszeri bejelentkezési felhasználói** , **a**.

    e. Kattintson a **létrehozás**.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Dome9 ív Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel Dome9 ív, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Dome9 ív**.

    ![Az alkalmazások listáját a Dome9 ív hivatkozás](./media/dome9arc-tutorial/tutorial_dome9arc_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Dome9 ív csempére kattint, meg kell lekérése automatikusan bejelentkezett Dome9 ív alkalmazását a.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/dome9arc-tutorial/tutorial_general_01.png
[2]: ./media/dome9arc-tutorial/tutorial_general_02.png
[3]: ./media/dome9arc-tutorial/tutorial_general_03.png
[4]: ./media/dome9arc-tutorial/tutorial_general_04.png

[100]: ./media/dome9arc-tutorial/tutorial_general_100.png

[200]: ./media/dome9arc-tutorial/tutorial_general_200.png
[201]: ./media/dome9arc-tutorial/tutorial_general_201.png
[202]: ./media/dome9arc-tutorial/tutorial_general_202.png
[203]: ./media/dome9arc-tutorial/tutorial_general_203.png


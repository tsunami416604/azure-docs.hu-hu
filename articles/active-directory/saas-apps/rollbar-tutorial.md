---
title: 'Oktatóanyag: Azure Active Directory-integráció az BUKÓÍV |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és BUKÓÍV között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 57537e54-9388-4272-a610-805ce45a451f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/04/2017
ms.author: jeedes
ms.openlocfilehash: e12e3187893690b75dc69835312ec86a0e601d32
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39431905"
---
# <a name="tutorial-azure-active-directory-integration-with-rollbar"></a>Oktatóanyag: Azure Active Directory-integráció az BUKÓÍV

Ebben az oktatóanyagban elsajátíthatja, hogyan BUKÓÍV integrálása az Azure Active Directory (Azure AD).

Bukóív integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá BUKÓÍV Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Bukóív (egyszeri bejelentkezés), az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Bukóív az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy BUKÓÍV egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Bukóív hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-rollbar-from-the-gallery"></a>Bukóív hozzáadása a katalógusból
Az Azure AD integrálása a BUKÓÍV konfigurálásához hozzá kell BUKÓÍV a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Bukóív hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **BUKÓÍV**válassza **BUKÓÍV** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában BUKÓÍV](./media/rollbar-tutorial/tutorial_rollbar_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés BUKÓÍV a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó BUKÓÍV mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó BUKÓÍV hivatkozás kapcsolata kell létrehozni.

BUKÓÍV, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az BUKÓÍV tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy BUKÓÍV tesztfelhasználót](#create-a-rollbar-test-user)**  – egy megfelelője a Britta Simon BUKÓÍV, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és BUKÓÍV alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés BUKÓÍV, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **BUKÓÍV** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/rollbar-tutorial/tutorial_rollbar_samlbase.png)

1. Az a **BUKÓÍV tartomány és URL-címek** területén kövesse az alábbi lépéseket, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód:

    ![Bukóív tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/rollbar-tutorial/tutorial_rollbar_url.png)

    a. Az a **azonosító** szövegmezőbe írja be az URL-cím: `https://saml.rollbar.com`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://rollbar.com/<accountname>/saml/sso/azure/`

1. Ellenőrizze **speciális URL-beállítások megjelenítése** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![Bukóív tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/rollbar-tutorial/tutorial_rollbar_url1.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://rollbar.com/<accountname>/saml/login/azure/`
     
    > [!NOTE] 
    > Ezek a értékei nem valódi. Frissítse a tényleges válasz URL-cím és a bejelentkezési URL-ezeket az értékeket. Kapcsolattartó [BUKÓÍV ügyfél-támogatási csapatának](mailto:support@rollbar.com) beolvasni ezeket az értékeket. 

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/rollbar-tutorial/tutorial_rollbar_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/rollbar-tutorial/tutorial_general_400.png)
    
1. Egy másik böngészőablakban jelentkezzen be a BUKÓÍV vállalati hely rendszergazdaként.

1. Kattintson a **Profilbeállítások** elemre a jobb felső sarokban, majd kattintson **fióknév beállítások**.
    
    ![Konfiguráció](./media/rollbar-tutorial/general.png)

1. Kattintson a **identitásszolgáltató** biztonsági csoportban.

    ![Konfiguráció](./media/rollbar-tutorial/configure1.png)

1. Az a **identitásszolgáltató SAML** szakaszban, hajtsa végre az alábbi lépéseket:
    
    ![Konfiguráció](./media/rollbar-tutorial/configure2.png)

    a. Válassza ki **AZURE** származó a **identitásszolgáltató SAML** legördülő listából.

    b. Nyissa meg a metaadat-fájlt a Jegyzettömbben, a tartalmát a vágólapra másolja és illessze be azt a **SAML-metaadatokat** szövegmezőbe.

    c. Kattintson a **Save** (Mentés) gombra.

1. Után kattintson a Mentés gombra a képernyő lesz hasonló:
    
    ![Konfiguráció](./media/rollbar-tutorial/configure3.png)
    > [!NOTE] 
    > A következő lépés végrehajtásához először hozzá kell adnia saját kezűleg felhasználóként a BUKÓÍV alkalmazásba az Azure-ban.
    a. Ha szeretné-e előírása minden felhasználónak az Azure-on keresztül hitelesítendő, majd kattintson a **jelentkezzen be az identitásszolgáltató keresztül** újra hitelesíteni kell Azure-on keresztül.  

    b.  Miután visszatér a képernyő, válassza ki a **szükséges bejelentkezési oldalát az identitásszolgáltató SAML** jelölőnégyzetet.

    b. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/rollbar-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/rollbar-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/rollbar-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/rollbar-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-rollbar-test-user"></a>Bukóív tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók BUKÓÍV jelentkezzen be, akkor ki kell építeni BUKÓÍV be. BUKÓÍV, esetén kiépítése a manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a BUKÓÍV vállalati webhely.

1. Kattintson a **Profilbeállítások** elemre a jobb felső sarokban, majd kattintson **fióknév beállítások**.

    ![Felhasználó](./media/rollbar-tutorial/general.png)

1. Kattintson a **felhasználók**.
    
    ![Alkalmazott hozzáadása](./media/rollbar-tutorial/user1.png)

1. Kattintson a **csapattag meghívása**.

    ![Személyek meghívása](./media/rollbar-tutorial/user2.png)

1. A szövegmezőbe írja be például a felhasználó nevét **brittasimon@contoso.com** , és kattintson a **Hozzáadás/meghívó**.

    ![Személyek meghívása](./media/rollbar-tutorial/user3.png)

1. Felhasználó felkérést kap, és azt követő hallgatója a rendszer létre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés BUKÓÍV Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel BUKÓÍV, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **BUKÓÍV**.

    ![Az alkalmazások listáját a BUKÓÍV hivatkozás](./media/rollbar-tutorial/tutorial_rollbar_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a BUKÓÍV csempére kattint, meg kell lekérése automatikusan bejelentkezett az BUKÓÍV alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/rollbar-tutorial/tutorial_general_01.png
[2]: ./media/rollbar-tutorial/tutorial_general_02.png
[3]: ./media/rollbar-tutorial/tutorial_general_03.png
[4]: ./media/rollbar-tutorial/tutorial_general_04.png

[100]: ./media/rollbar-tutorial/tutorial_general_100.png

[200]: ./media/rollbar-tutorial/tutorial_general_200.png
[201]: ./media/rollbar-tutorial/tutorial_general_201.png
[202]: ./media/rollbar-tutorial/tutorial_general_202.png
[203]: ./media/rollbar-tutorial/tutorial_general_203.png


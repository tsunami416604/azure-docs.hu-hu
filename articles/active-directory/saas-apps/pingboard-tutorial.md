---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Pingboard |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Pingboard között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 084a921123badb786503ce6696eb4ace14dd59b8
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56170482"
---
# <a name="tutorial-azure-active-directory-integration-with-pingboard"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Pingboard

Ebben az oktatóanyagban elsajátíthatja, hogyan Pingboard integrálása az Azure Active Directory (Azure AD).

Pingboard integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá Pingboard Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Pingboard (egyszeri bejelentkezés) az Azure AD-fiókjukkal
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Pingboard az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy Pingboard egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Pingboard hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-pingboard-from-the-gallery"></a>Pingboard hozzáadása a katalógusból
Az Azure AD integrálása a Pingboard konfigurálásához hozzá kell Pingboard a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Pingboard hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások][2]

1. Kattintson a **Hozzáadás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **Pingboard**válassza **Pingboard** eredmény panelen, majd kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában Pingboard](./media/pingboard-tutorial/tutorial_pingboard_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Pingboard a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Pingboard mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Pingboard hivatkozás kapcsolata kell létrehozni.

Ez a hivatkozás-kapcsolat létesítéséhez értéket rendeli az **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** Pingboard a.

Az Azure AD egyszeri bejelentkezés az Pingboard tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy Pingboard tesztfelhasználót](#create-a-pingboard-test-user)**  – egy megfelelője a Britta Simon Pingboard, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Pingboard alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Pingboard, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Pingboard** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1.  Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési párbeszédpanel](./media/pingboard-tutorial/tutorial_pingboard_samlbase.png)

1. Az a **Pingboard tartomány és URL-címek** területén kövesse az alábbi lépéseket, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód:

    ![Pingboard tartomány és URL-címeket egyetlen bejelentkezési adatait Identitásszolgáltató](./media/pingboard-tutorial/tutorial_pingboard_url.png)

    a. Az a **azonosító** szövegmezőbe írja be az értéket, mint: `http://app.pingboard.com/sp`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<entity-id>.pingboard.com/auth/saml/consume`

1. Ellenőrizze **speciális URL-beállítások megjelenítése**, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![Pingboard tartomány és URL-címeket egyetlen bejelentkezési adatait SP](./media/pingboard-tutorial/tutorial_pingboard_sp_initiated01.png)

     Az a **bejelentkezési URL-** szövegmezőbe írja be az URL-cím a következő minta használatával: `https://<sub-domain>.pingboard.com/sign_in`

    > [!NOTE]
    > Vegye figyelembe, hogy ezek az értékek nem tesznek valós. Frissítse a tényleges válasz URL-cím és a bejelentkezési URL-ezeket az értékeket. Kapcsolattartó [Pingboard ügyfél-támogatási csapatának](https://support.pingboard.com/) beolvasni ezeket az értékeket.

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse az XML-fájlt a számítógépen.

    ![Pingboard metadata xml](./media/pingboard-tutorial/tutorial_pingboard_certificate.png)

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/pingboard-tutorial/tutorial_general_400.png)

1. Egyszeri bejelentkezés konfigurálása Pingboard oldalán, nyisson meg egy új böngészőablakot, és jelentkezzen be a Pingboard fiókjához. Az egyszeri bejelentkezés beállítása Pingboard rendszergazdának kell lennie.

1. Válassza ki a felső menüben, **alkalmazások > Integrációk**

    ![Egyszeri bejelentkezés konfigurálása](./media/pingboard-tutorial/Pingboard_integration.png)

1. Az a **Integrációk** lapon, keresse meg a **"Azure Active Directory"** csempére, majd kattintson rá.

    ![Pingboard egyszeri bejelentkezéses integráció](./media/pingboard-tutorial/Pingboard_aad.png)

1. Az a kattintson a következő modális **"Konfigurálás"**

    ![Pingboard konfigurációs gomb](./media/pingboard-tutorial/Pingboard_configure.png)

1. A következő oldalon láthatja, hogy "Azure-beli egyszeri bejelentkezés integrációs engedélyezve van". Nyissa meg a letöltött metaadatainak XML-fájlt a Jegyzettömbben, és illessze be a tartalom **Identitásszolgáltató metaadatok**.

    ![Pingboard egyszeri Bejelentkezést a beállítások képernyőn](./media/pingboard-tutorial/Pingboard_sso_configure.png)

1. A rendszer érvényesíti a fájlt, és ha minden rendben, az egyszeri bejelentkezés most engedélyezve lesz.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](./media/pingboard-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/pingboard-tutorial/create_aaduser_02.png)

1. Kattintson a párbeszédpanel tetején **Hozzáadás** megnyitásához a **felhasználói** párbeszédpanel.

    ![Hozzáadás gomb](./media/pingboard-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:

    ![A felhasználó párbeszédpanel](./media/pingboard-tutorial/create_aaduser_04.png)

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="create-a-pingboard-test-user"></a>Pingboard tesztfelhasználó létrehozása

Ez a szakasz célja Pingboard Britta Simon nevű felhasználó létrehozásához. Pingboard támogatja a felhasználók automatikus átadása, amely alapértelmezés szerint van engedélyezve. További részleteket talál [Itt](pingboard-provisioning-tutorial.md) konfigurálásának a felhasználók automatikus átadása.

**Hozza létre a felhasználó manuálisan kell, ha hajtsa végre a következő lépéseket:**

1. Jelentkezzen be rendszergazdaként a Pingboard vállalati webhely.

1. Kattintson a **"Alkalmazott hozzáadása"** gombot **Directory** lapot.

    ![Alkalmazott hozzáadása](./media/pingboard-tutorial/create_testuser_add.png)

1. Az a **"Alkalmazott hozzáadása"** párbeszédpanel lapon, a következő lépésekkel:

    ![Személyek meghívása](./media/pingboard-tutorial/create_testuser_name.png)

    a. Az a **teljes fájlvisszaállítási név** szövegmezőbe írja be a teljes nevet felhasználó, például **Britta Simon**.

    b. Az a **E-mail** szövegmezőbe írja be az e-mail-cím, felhasználó, például **brittasimon@contoso.com**.

    c. Az a **beosztás** szövegmezőbe írja be a Britta Simon feladat címe.

    d. Az a **hely** legördülő menüben válassza ki a helyet a Britta Simon.

    e. Kattintson a **Hozzáadás** parancsra.

1. A megerősítő képernyőn felhasználó hozzáadásának megerősítéséhez.

    ![Győződjön meg róla](./media/pingboard-tutorial/create_testuser_confirm.png)

    > [!NOTE]
    > Az Azure Active Directory fióktulajdonos kap egy e-mailt, és a egy hivatkozást, mielőtt aktívvá válik, győződjön meg arról, hogy fiókjuk követi.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Pingboard Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel Pingboard, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Pingboard**.

    ![Az alkalmazások listáját a Pingboard hivatkozásra](./media/pingboard-tutorial/tutorial_pingboard_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

Ha a hozzáférési panelen a Pingboard csempére kattint, meg kell lekérése automatikusan bejelentkezett az Pingboard alkalmazáshoz.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Felhasználók átadásának konfigurálása](pingboard-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/pingboard-tutorial/tutorial_general_01.png
[2]: ./media/pingboard-tutorial/tutorial_general_02.png
[3]: ./media/pingboard-tutorial/tutorial_general_03.png
[4]: ./media/pingboard-tutorial/tutorial_general_04.png

[100]: ./media/pingboard-tutorial/tutorial_general_100.png

[200]: ./media/pingboard-tutorial/tutorial_general_200.png
[201]: ./media/pingboard-tutorial/tutorial_general_201.png
[202]: ./media/pingboard-tutorial/tutorial_general_202.png
[203]: ./media/pingboard-tutorial/tutorial_general_203.png

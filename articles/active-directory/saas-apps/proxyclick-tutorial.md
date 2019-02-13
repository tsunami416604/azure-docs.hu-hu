---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Proxyclick |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Proxyclick között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 5c58a859-71c2-4542-ae92-e5f16a8e7f18
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 52d3e058c7e604a31493bf3b1972b39c9922fd90
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56163515"
---
# <a name="tutorial-azure-active-directory-integration-with-proxyclick"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Proxyclick

Ebben az oktatóanyagban elsajátíthatja, hogyan Proxyclick integrálása az Azure Active Directory (Azure AD).

Proxyclick integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá Proxyclick Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Proxyclick (egyszeri bejelentkezés), az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Proxyclick az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy Proxyclick egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Proxyclick hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-proxyclick-from-the-gallery"></a>Proxyclick hozzáadása a katalógusból
Az Azure AD integrálása a Proxyclick konfigurálásához hozzá kell Proxyclick a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Proxyclick hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **Proxyclick**válassza **Proxyclick** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában Proxyclick](./media/proxyclick-tutorial/tutorial_proxyclick_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Proxyclick a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Proxyclick mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Proxyclick hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az Proxyclick tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy Proxyclick tesztfelhasználót](#create-a-proxyclick-test-user)**  – egy megfelelője a Britta Simon Proxyclick, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Proxyclick alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Proxyclick, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Proxyclick** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési párbeszédpanel](./media/proxyclick-tutorial/tutorial_proxyclick_samlbase.png)

1. Az a **Proxyclick tartomány és URL-címek** területén kövesse az alábbi lépéseket, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód:

    ![Proxyclick tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/proxyclick-tutorial/tutorial_proxyclick_url2.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://saml.proxyclick.com/init/<companyId>`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://saml.proxyclick.com/consume/<companyId>`

1. Ellenőrizze **speciális URL-beállítások megjelenítése** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![Proxyclick tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/proxyclick-tutorial/tutorial_proxyclick_url1.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://saml.proxyclick.com/init/<companyId>`

    > [!NOTE]
    > Ezek a értékei nem valódi. A tényleges azonosítója, válasz URL-cím és a bejelentkezési URL-cím, az oktatóanyag későbbi részében ismertetett frissíteni ezeket az értékeket.

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/proxyclick-tutorial/tutorial_proxyclick_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/proxyclick-tutorial/tutorial_general_400.png)

1. Az a **Proxyclick konfigurációs** területén kattintson **konfigurálása Proxyclick** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Proxyclick Configuration](./media/proxyclick-tutorial/tutorial_proxyclick_configure.png)

1. Egy másik böngészőablakban jelentkezzen be a Proxyclick vállalati hely rendszergazdaként.

1. Válassza ki **fiók & beállítások**.

    ![Proxyclick Configuration](./media/proxyclick-tutorial/configure1.png)

1. Görgessen le a **INTEGRÁCIÓK** válassza **SAML**.

    ![Proxyclick Configuration](./media/proxyclick-tutorial/configure2.png)

1. Az a **SAML** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Proxyclick Configuration](./media/proxyclick-tutorial/configure3.png)

    a. Másolás **SAML ügyfél URL-címe** értékét, és illessze be azt **válasz URL-cím** szövegmezőjébe **Proxyclick tartomány és URL-címek** szakaszban az Azure Portalon.

    b. Másolás **SAML egyszeri bejelentkezés átirányítási URL-cím** értékét, és illessze be azt **bejelentkezési URL-cím** és **azonosító** a szövegmezőből **Proxyclick tartomány és URL-címek** szakasz az Azure Portalon.

    c. Válassza ki **SAML-kérelem metódusa** , **HTTP-átirányítás**.

    d. Az a **kibocsátó** szövegmező, illessze be az értéket a **SAML Entitásazonosító** érték, amely az Azure Portalról másolt.

    e. Az a **SAML 2.0-s végponti URL-cím** szövegmező, illessze be az értéket a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** Azure Portalról másolt.

    f. Nyissa meg a letöltött tanúsítvány-fájlt a Jegyzettömbben az Azure Portalról, és illessze be azt a **tanúsítvány** szövegmezőbe.

    g. Kattintson a **módosítások mentése**.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/proxyclick-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/proxyclick-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/proxyclick-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/proxyclick-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="create-a-proxyclick-test-user"></a>Proxyclick tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Proxyclick jelentkezzen be, akkor ki kell építeni Proxyclick be. Proxyclick, esetén kiépítése a manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a Proxyclick vállalati webhely.

1. Kattintson a **munkatársai** a felső navigációs sávban.

    ![Alkalmazott hozzáadása](./media/proxyclick-tutorial/user1.png)

1. Kattintson a **munkatársa hozzáadása**

    ![Alkalmazott hozzáadása](./media/proxyclick-tutorial/user2.png)

1. Az a **adjon hozzá egy munkatársa** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Alkalmazott hozzáadása](./media/proxyclick-tutorial/user3.png)

    a. Az a **E-mail** szövegmezőbe írja be az e-mail-cím, felhasználó, például **brittasimon@contoso.com**.

    b. Az a **Utónév** szövegmezőbe írja be az első felhasználóneve Britta például.

    c. Az a **Vezetéknév** szövegmezőbe írja be a Simon például a felhasználó vezetékneve.

    d. Kattintson a **felhasználó hozzáadása**.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Proxyclick Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200]

**Britta Simon rendel Proxyclick, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Proxyclick**.

    ![Az alkalmazások listáját a Proxyclick hivatkozásra](./media/proxyclick-tutorial/tutorial_proxyclick_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Proxyclick csempére kattint, meg kell lekérése automatikusan bejelentkezett az Proxyclick alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/proxyclick-tutorial/tutorial_general_01.png
[2]: ./media/proxyclick-tutorial/tutorial_general_02.png
[3]: ./media/proxyclick-tutorial/tutorial_general_03.png
[4]: ./media/proxyclick-tutorial/tutorial_general_04.png

[100]: ./media/proxyclick-tutorial/tutorial_general_100.png

[200]: ./media/proxyclick-tutorial/tutorial_general_200.png
[201]: ./media/proxyclick-tutorial/tutorial_general_201.png
[202]: ./media/proxyclick-tutorial/tutorial_general_202.png
[203]: ./media/proxyclick-tutorial/tutorial_general_203.png


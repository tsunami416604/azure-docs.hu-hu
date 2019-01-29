---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Envi MMIS |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Envi MMIS között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ab89f8ee-2507-4625-94bc-b24ef3d5e006
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2018
ms.author: jeedes
ms.openlocfilehash: b85dc27f6b6a23be6dc89a0f0a7cf9f78681446d
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55197449"
---
# <a name="tutorial-azure-active-directory-integration-with-envi-mmis"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Envi MMIS

Ebben az oktatóanyagban elsajátíthatja, hogyan Envi MMIS integrálható az Azure Active Directory (Azure AD).

Envi MMIS integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá Envi MMIS Azure AD-ben.
- A felhasználók automatikusan első bejelentkezett Envi MMIS (egyszeri bejelentkezés), engedélyezheti az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Envi MMIS az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy Envi MMIS egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Envi MMIS hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-envi-mmis-from-the-gallery"></a>Envi MMIS hozzáadása a katalógusból
Az Azure AD integrálása a Envi MMIS konfigurálásához hozzá kell Envi MMIS a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Envi MMIS hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **Envi MMIS**válassza **Envi MMIS** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában Envi MMIS](./media/envimmis-tutorial/tutorial_envimmis_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban konfigurálja, és tesztelés az Azure AD egyszeri bejelentkezés Envi MMIS "Britta Simon" nevű tesztfelhasználó alapján.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Envi MMIS mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Envi MMIS hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az Envi MMIS tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy Envi MMIS tesztfelhasználót](#create-an-envi-mmis-test-user)**  – egy megfelelője a Britta Simon Envi MMIS, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Envi MMIS alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Envi MMIS, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Envi MMIS** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/envimmis-tutorial/tutorial_envimmis_samlbase.png)

1. Az a **Envi MMIS tartomány és URL-címek** területén kövesse az alábbi lépéseket, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód:

    ![Envi MMIS tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/envimmis-tutorial/tutorial_envimmis_url.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://www.<CUSTOMER DOMAIN>.com/Account`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://www.<CUSTOMER DOMAIN>.com/Account/Acs`

1. Ellenőrizze **speciális URL-beállítások megjelenítése** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![Envi MMIS tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/envimmis-tutorial/tutorial_envimmis_url1.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://www.<CUSTOMER DOMAIN>.com/Account`
     
    > [!NOTE]
    > Ezek a értékei nem valódi. Frissítse a tényleges azonosítóját, válasz URL-cím és bejelentkezési URL-ezeket az értékeket. Kapcsolattartó [Envi MMIS ügyfél-támogatási csapatának](mailto:support@ioscorp.com) beolvasni ezeket az értékeket.

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/envimmis-tutorial/tutorial_envimmis_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/envimmis-tutorial/tutorial_general_400.png)

1. Egy másik böngészőablakban jelentkezzen be a Envi MMIS hely rendszergazdaként.

1. Kattintson a **saját tartomány** fülre.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/envimmis-tutorial/configure1.png)

1. Kattintson a **Szerkesztés** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/envimmis-tutorial/configure2.png)

1. Válassza ki **távoli hitelesítés használata** jelölőnégyzetet, majd **HTTP-átirányítás** származó a **hitelesítési típus** legördülő listából.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/envimmis-tutorial/configure3.png)

1. Válassza ki **erőforrások** fülre, majd **metaadatok feltöltése**.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/envimmis-tutorial/configure4.png)

1. Az a **metaadatok feltöltése** előugró ablakban hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/envimmis-tutorial/configure5.png)

    a. Válassza ki **fájl** parancsát a **töltse fel a** legördülő listából.

    b. Az Azure Portalról letöltött metaadatfájl feltöltése kiválasztásával a **válassza ki a fájl ikonra**.

    c. Kattintson az **OK** gombra.

1. A letöltött metaadatfájl feltöltése után a mezők automatikusan fog első betöltve. Kattintson a **Update**

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/envimmis-tutorial/configure6.png)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/envimmis-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/envimmis-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/envimmis-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/envimmis-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-an-envi-mmis-test-user"></a>Hozzon létre egy Envi MMIS tesztfelhasználó számára

Ahhoz, hogy az Azure AD-felhasználók Envi MMIS jelentkezzen be, akkor ki kell építeni Envi MMIS be.  
Envi MMIS, esetén kiépítése a manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a Envi MMIS vállalati webhely.

1. Kattintson a **Felhasználólista** fülre.

    ![Alkalmazott hozzáadása](./media/envimmis-tutorial/user1.png)

1. Kattintson a **felhasználó hozzáadása** gombra.

    ![Alkalmazott hozzáadása](./media/envimmis-tutorial/user2.png)

1. Az a **felhasználó hozzáadása** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Alkalmazott hozzáadása](./media/envimmis-tutorial/user3.png)

    a. Az a **felhasználónév** szövegmezőbe írja be a felhasználónevet Britta Simon fiók, például **brittasimon@contoso.com**.
    
    b. Az a **Utónév** szövegmezőbe BrittaSimon utónevét típusú, például **Britta**.

    c. Az a **Vezetéknév** szövegmezőbe BrittaSimon a Vezetéknév típusú, például **Simon**.

    d. Adja meg a cím a felhasználó a **cím** , a szövegmezőbe.
    
    e. Az a **E-mail cím** szövegmezőbe írja be az e-mail-cím Britta Simon fiók, például **brittasimon@contoso.com**.

    f. Az a **egyszeri bejelentkezési felhasználónév** szövegmezőbe írja be a felhasználónevet Britta Simon fiók, például **brittasimon@contoso.com**.

    g. Kattintson a **Save** (Mentés) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Envi MMIS Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel Envi MMIS, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Envi MMIS**.

    ![Az alkalmazások listáját a Envi MMIS hivatkozás](./media/envimmis-tutorial/tutorial_envimmis_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Envi MMIS csempére kattint, meg kell lekérése automatikusan bejelentkezett az Envi MMIS alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/envimmis-tutorial/tutorial_general_01.png
[2]: ./media/envimmis-tutorial/tutorial_general_02.png
[3]: ./media/envimmis-tutorial/tutorial_general_03.png
[4]: ./media/envimmis-tutorial/tutorial_general_04.png

[100]: ./media/envimmis-tutorial/tutorial_general_100.png

[200]: ./media/envimmis-tutorial/tutorial_general_200.png
[201]: ./media/envimmis-tutorial/tutorial_general_201.png
[202]: ./media/envimmis-tutorial/tutorial_general_202.png
[203]: ./media/envimmis-tutorial/tutorial_general_203.png


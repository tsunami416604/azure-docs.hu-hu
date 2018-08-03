---
title: 'Oktatóanyag: Azure Active Directory-integráció az egyik Zoho |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a egy Zoho között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: bbc3038c-0d8b-45dd-9645-368bd3d01a0f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2018
ms.author: jeedes
ms.openlocfilehash: 81e86df270a7286426363c26a0e8a87b99082428
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39438273"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho-one"></a>Oktatóanyag: Azure Active Directory-integráció az Zoho egy

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálhatja Zoho egy Azure Active Directoryval (Azure AD).

Az Azure AD integrálása Zoho egy nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá Zoho egy Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett egy Zoho (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Zoho egy az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy Zoho egy egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. A katalógusból Zoho egy hozzáadása
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-zoho-one-from-the-gallery"></a>A katalógusból Zoho egy hozzáadása
Az Azure AD integrálása a Zoho egy konfigurálásához hozzá kell Zoho egy a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a Zoho egy a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **Zoho egy**válassza **Zoho egy** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Zoho egy, a találatok listájában](./media/zohoone-tutorial/tutorial_zohoone_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Zoho egy úgynevezett "Britta Simon" tesztfelhasználó alapján.

Egyszeri bejelentkezés működjön, az Azure ad-ben kell tudja, hogy mi a partner felhasználó Zoho egy felhasználó Azure AD-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Zoho egy hivatkozás kapcsolatát kell létrehozni.

Az Azure AD egyszeri bejelentkezés az Zoho egy tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy Zoho egy tesztfelhasználót](#create-a-zoho-one-test-user)**  – egy megfelelője a Britta Simon Zoho egy felhasználó Azure ad-ben reprezentációja van csatolva van.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és az Zoho egy alkalmazás egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés egy Zoho, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Zoho egy** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/zohoone-tutorial/tutorial_zohoone_samlbase.png)

1. Az a **Zoho tartománya és URL-címek** területén kövesse az alábbi lépéseket, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód:

    ![Zoho tartománya és URL-címek egyszeri bejelentkezési adatait](./media/zohoone-tutorial/tutorial_zohoone_url.png)

    a. Az a **azonosító (entityid)** szövegmezőbe írja be egy URL-címe: `one.zoho.com`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://accounts.zoho.com/samlresponse/<saml-identifier>`

    c. Ellenőrizze **speciális URL-beállítások megjelenítése**.

    d. Az a **továbbítási állapot** szövegmezőbe írja be egy URL-címe:`https://one.zoho.com`

1. Ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett módban hajtsa végre a következő lépést:

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://accounts.zoho.com/samlauthrequest/<domain_name>?serviceurl=https://one.zoho.com`
     
    > [!NOTE] 
    > Az előző **válasz URL-cím** és **bejelentkezési URL-** értéke nem valódi. A tényleges válasz URL-cím és a bejelentkezési URL-címet az oktatóanyag későbbi részében ismertetett frissíteni az értéket. 

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/zohoone-tutorial/tutorial_zohoone_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/zohoone-tutorial/tutorial_general_400.png)
    
1. Az a **Zoho egy konfigurációs** területén kattintson **Zoho egy konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-CÍMÉT és a SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Zoho egy konfiguráció](./media/zohoone-tutorial/tutorial_zohoone_configure.png) 

1. Egy másik böngészőablakban jelentkezzen be a Zoho egy vállalati hely rendszergazdaként.

1. Az a **szervezet** lapra, kattintson **telepítő** alatt **SAML-hitelesítés**.

    ![Zoho egy szervezeti](./media/zohoone-tutorial/tutorial_zohoone_setup.png)

1. Az előugró oldalon hajtsa végre az alábbi lépéseket:

    ![Zoho egy sig](./media/zohoone-tutorial/tutorial_zohoone_save.png)

    a. A a **bejelentkezési URL** szövegmezőjébe illessze be az értéket, **SAML egyszeri bejelentkezési szolgáltatás URL-cím**, az Azure Portalról másolt.

    b. Az a **kijelentkezéses URL-cím** szövegmezőbe, illessze be az értéket a **kijelentkezéses URL-cím**, az Azure Portalról másolt.

    c. Kattintson a **Tallózás** feltölteni a **tanúsítvány (Base64)** , amely az Azure Portalról letöltött.

    d. Kattintson a **Save** (Mentés) gombra.

1. Az SAML-hitelesítés beállítása a mentés után másolja a **SAML-Identfier** értékét, és használja ezt az értéket a **válasz URL-cím** az Azure Portalon alatt **Zoho tartománya és URL-címek** a szakasz.

    ![Zoho egy saml](./media/zohoone-tutorial/tutorial_zohoone_samlidenti.png)

1. Nyissa meg a **tartományok** fülre, majd **hozzáadni Domain**.

    ![Zoho egy tartományt](./media/zohoone-tutorial/tutorial_zohoone_domain.png)

1. Az a **hozzáadni Domain** lapon, a következő lépésekkel:

    ![Zoho egy tartomány hozzáadása](./media/zohoone-tutorial/tutorial_zohoone_adddomain.png)

    a. Az a **tartománynév** szövegmező, például típus tartomány **contoso.com**.

    b. Kattintson a **Hozzáadás** parancsra.

    >[!Note]
    >Miután hozzáadta a tartomány kövesse [ezek](https://www.zoho.com/one/help/admin-guide/domain-verification.html) a tartomány ellenőrzésének lépéseit. Ha a tartomány verfified, használja a tartománynevet **bejelentkezési URL-** a **Zoho tartománya és URL-címek** szakaszban az Azure Portalon.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/zohoone-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/zohoone-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/zohoone-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/zohoone-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-zoho-one-test-user"></a>Zoho egy tesztfelhasználó létrehozása

Ahhoz, hogy jelentkezzen be a Zoho egy Azure AD-felhasználók, akkor ki kell építeni az Zoho egy. Zoho egy, a kiépítés a manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Zoho egy olyan biztonsági-rendszergazdaként jelentkezzen be.

1. Az a **felhasználók** lapra, kattintson a **felhasználói embléma**.

    ![Zoho egy felhasználó](./media/zohoone-tutorial/tutorial_zohoone_users.png)

1. Az a **felhasználó hozzáadása** lapon, a következő lépésekkel:

    ![Zoho több felhasználó hozzáadása](./media/zohoone-tutorial/tutorial_zohoone_adduser.png)
    
    a. A **neve** szöveget adja meg például a felhasználó nevét **Britta simon**.
    
    b. A **E-mail cím** szöveget adja meg az e-mailt, például a felhasználó **brittasimon@contoso.com**.

    >[!Note]
    >Válasszon ellenőrzött tartományt a tartomány listából.

    c. Kattintson a **Hozzáadás** parancsra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Zoho egy Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel egy Zoho, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Zoho egy**.

    ![Az alkalmazások listáját a Zoho egy hivatkozás](./media/zohoone-tutorial/tutorial_zohoone_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Zoho egy csempére kattint, meg kell lekérése automatikusan bejelentkezett a Zoho egy adott alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/zohoone-tutorial/tutorial_general_01.png
[2]: ./media/zohoone-tutorial/tutorial_general_02.png
[3]: ./media/zohoone-tutorial/tutorial_general_03.png
[4]: ./media/zohoone-tutorial/tutorial_general_04.png

[100]: ./media/zohoone-tutorial/tutorial_general_100.png

[200]: ./media/zohoone-tutorial/tutorial_general_200.png
[201]: ./media/zohoone-tutorial/tutorial_general_201.png
[202]: ./media/zohoone-tutorial/tutorial_general_202.png
[203]: ./media/zohoone-tutorial/tutorial_general_203.png


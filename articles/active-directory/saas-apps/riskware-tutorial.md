---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Riskware |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Riskware között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 81866167-b163-4695-8978-fd29a25dac7a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3de8422efdbb7802e0532503e2733518e8050f6d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57842532"
---
# <a name="tutorial-azure-active-directory-integration-with-riskware"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Riskware

Ebben az oktatóanyagban elsajátíthatja, hogyan Riskware integrálása az Azure Active Directory (Azure AD).

Riskware integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá Riskware Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Riskware (egyszeri bejelentkezés), az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Riskware az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy Riskware egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Riskware hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-riskware-from-the-gallery"></a>Riskware hozzáadása a katalógusból
Az Azure AD integrálása a Riskware konfigurálásához hozzá kell Riskware a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Riskware hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]

1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **Riskware**válassza **Riskware** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában Riskware](./media/riskware-tutorial/tutorial_riskware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Riskware a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Riskware mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Riskware hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az Riskware tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy Riskware tesztfelhasználót](#create-a-riskware-test-user)**  – egy megfelelője a Britta Simon Riskware, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Riskware alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Riskware, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Riskware** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési párbeszédpanel](./media/riskware-tutorial/tutorial_riskware_samlbase.png)

1. Az a **Riskware tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Riskware tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/riskware-tutorial/tutorial_riskware_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe:

    | Környezet| Az URL-minta|
    |--|--|
    | Felhasználói tesztelés|  `https://riskcloud.net/uat?ccode=<COMPANYCODE>` |
    | TERMÉKKATALÓGUS| `https://riskcloud.net/prod?ccode=<COMPANYCODE>` |
    | DEMO| `https://riskcloud.net/demo?ccode=<COMPANYCODE>` |
    |||

    b. Az a **azonosító (entityid)** szövegmezőbe írja be egy URL-címe:
    
    | Környezet| Az URL-minta|
    |--|--|
    | Felhasználói tesztelés| `https://riskcloud.net/uat` |
    | TERMÉKKATALÓGUS| `https://riskcloud.net/prod` |
    | DEMO| `https://riskcloud.net/demo` |
    |||

    > [!NOTE]
    > A bejelentkezési URL-érték nem valódi. Frissítse az értéket a tényleges bejelentkezési URL-CÍMÉT. Kapcsolattartó [Riskware ügyfél-támogatási csapatának](mailto:support@pansoftware.com.au) a gépkulcsengedélyek értékének.

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/riskware-tutorial/tutorial_riskware_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/riskware-tutorial/tutorial_general_400.png)

1. Az a **Riskware konfigurációs** területén kattintson **konfigurálása Riskware** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-CÍMÉT és a SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Riskware konfiguráció](./media/riskware-tutorial/tutorial_riskware_configure.png)

1. Egy másik böngészőablakban jelentkezzen be a Riskware vállalati hely rendszergazdaként.

1. Kattintson a jobb felső sarokban, **karbantartási** nyissa meg a karbantartást.

    ![Riskware konfigurációk kezelése](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. A karbantartás oldalon kattintson **hitelesítési**.

    ![Riskware konfigurációs authen](./media/riskware-tutorial/tutorial_riskware_authen.png)

1. A **hitelesítési konfiguráció** lapon, a következő lépésekkel:

    ![Riskware konfigurációs authenconfig](./media/riskware-tutorial/tutorial_riskware_config.png)

    a. Válassza ki **típus** , **SAML** a hitelesítéshez.

    b. Az a **kód** szövegmezőbe írja be a kódot, például AZURE_UAT.

    c. Az a **leírás** szövegmezőbe írja be a leírást. például az AZURE konfigurálása egyszeri bejelentkezéshez.

    d. A **egyszeri bejelentkezési oldalon** szövegmezőjébe illessze be a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** érték, amely az Azure Portalról másolta.

    e. A **lap Kijelentkezés** szövegmezőjébe illessze be a **kijelentkezéses URL-cím** érték, amely az Azure Portalról másolta.

    f. Az a **Post űrlapmező** szövegmező, található bejegyzés választ, amely tartalmazza a SAML SAMLResponse például a mező neve

    g. Az a **XML-címkenév identitás** szövegmezőbe típus attribútuma, amely tartalmazza a NameID például a SAML-válasz egyedi azonosítója.

    h. Nyissa meg a letöltött **metaadatainak Xml** a Jegyzettömbben az Azure portálról másolja a tanúsítványt a metaadat-fájlból, és illessze be azt a **tanúsítvány** szövegmező

    i. A **ügyfél URL-címe** szövegmezőbe, illessze be az értéket, **válasz URL-cím**, amely a támogatási csapat kap.

    j. A **kibocsátó** szövegmezőbe, illessze be az értéket a **azonosító**, amely kap a támogatási csoporthoz.

    > [!Note]
    > Kapcsolattartó [Riskware ügyfél-támogatási csapatának](mailto:support@pansoftware.com.au) beolvasni ezeket az értékeket

    k. Válassza ki **használata utáni** jelölőnégyzetet.

    l. Válassza ki **használható SAML-kérelmet** jelölőnégyzetet.

    m. Kattintson a **Save** (Mentés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/riskware-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/riskware-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/riskware-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/riskware-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="create-a-riskware-test-user"></a>Riskware tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Riskware bejelentkezni, akkor ki kell építeni Riskware be. Riskware a kiépítés manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be egy biztonsági-rendszergazdaként Riskware.

1. Kattintson a jobb felső sarokban, **karbantartási** nyissa meg a karbantartást. 

    ![Riskware konfigurációs kezeli](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. A karbantartás oldalon kattintson **személyek**.

    ![Riskware konfigurációs személyek](./media/riskware-tutorial/tutorial_riskware_people.png)

1. Válassza ki **részletek** lapra, és hajtsa végre az alábbi lépéseket:

    ![Riskware konfiguráció részletei](./media/riskware-tutorial/tutorial_riskware_details.png)

    a. Válassza ki **személy típusa** , például alkalmazottak.

    b. A **Utónév** szövegmezőbe írja be például a felhasználó utónevét **Britta**.

    c. A **Vezetéknév** szövegmezőbe írja be például a felhasználó vezetékneve **Simon**.

1. Az a **biztonsági** fülre, hajtsa végre az alábbi lépéseket:

    ![Riskware konfigurációs biztonság](./media/riskware-tutorial/tutorial_riskware_security.png)

    a. A **hitelesítési** szakaszban jelölje be a **hitelesítési** üzemmódot, amely rendelkezik beállítása AZURE-konfiguráció, például egyszeri bejelentkezéshez.

    b. Alatt **bejelentkezési adatait** ebben a szakaszban a **felhasználói azonosító** szövegmezőben adja meg az e-mail címét, például a felhasználó **brittasimon\@contoso.com**.

    c. Az a **jelszó** szövegmezőbe írja be a felhasználó jelszavát.

1. Az a **szervezet** fülre, hajtsa végre az alábbi lépéseket:

    ![Szervezeti Riskware konfiguráció](./media/riskware-tutorial/tutorial_riskware_org.png)

    a. Válassza ki a beállítás értéke **Level1** szervezet.

    b. Alatt **személy elsődleges munkahelyi** ebben a szakaszban a **helye** szövegmezőbe írja be a hely.

    c. A **alkalmazott** szakaszban jelölje be **alkalmazott állapota** magasabb, mint.

1. Kattintson a **Save** (Mentés) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Riskware Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200]

**Britta Simon rendel Riskware, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Riskware**.

    ![Az alkalmazások listáját a Riskware hivatkozásra](./media/riskware-tutorial/tutorial_riskware_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Riskware csempére kattint, meg kell lekérése automatikusan bejelentkezett az Riskware alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/riskware-tutorial/tutorial_general_01.png
[2]: ./media/riskware-tutorial/tutorial_general_02.png
[3]: ./media/riskware-tutorial/tutorial_general_03.png
[4]: ./media/riskware-tutorial/tutorial_general_04.png

[100]: ./media/riskware-tutorial/tutorial_general_100.png

[200]: ./media/riskware-tutorial/tutorial_general_200.png
[201]: ./media/riskware-tutorial/tutorial_general_201.png
[202]: ./media/riskware-tutorial/tutorial_general_202.png
[203]: ./media/riskware-tutorial/tutorial_general_203.png


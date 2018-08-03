---
title: 'Oktatóanyag: Azure Active Directory-integráció a szervezeti diagram most |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a szervezeti diagram most között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 50a1522f-81de-4d14-9b6b-dd27bb1338a4
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2018
ms.author: jeedes
ms.openlocfilehash: e23d76074f4b428b672e0cd5aeeaba99d080a4cf
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39435934"
---
# <a name="tutorial-azure-active-directory-integration-with-orgchart-now"></a>Oktatóanyag: Azure Active Directory-integráció a szervezeti diagram most

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálható a szervezeti diagram most az Azure Active Directory (Azure AD).

Szervezeti diagram most integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá a szervezeti diagram most az Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a szervezeti diagram most (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Szervezeti diagram most az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Az Azure AD-előfizetéshez
- A szervezeti diagram most egyszeri bejelentkezéses engedélyezett előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Szervezeti diagram most hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-orgchart-now-from-the-gallery"></a>Szervezeti diagram most hozzáadása a katalógusból
Az Azure AD-be a szervezeti diagram most az integráció konfigurálásához hozzá kell szervezeti diagram most a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Szervezeti diagram most hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **szervezeti diagram most**, jelölje be **szervezeti diagram most** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Szervezeti diagram most az eredmények listájában](./media/orgchartnow-tutorial/tutorial_orgchartnow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban konfigurálja, és a szervezeti diagram most az Azure AD egyszeri bejelentkezés tesztelése egy "Britta Simon" nevű tesztelési felhasználó alapján.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó szervezeti diagram most mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó szervezeti diagram most hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés a szervezeti diagram most tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy szervezeti diagram most tesztfelhasználót](#create-an-orgchart-now-test-user)**  - a-megfelelője a Britta Simon rendelkezik, amely kapcsolódik az Azure AD felhasználói ábrázolása szervezeti diagram most.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a szervezeti diagram most alkalmazás egyszeri bejelentkezés konfigurálása.

**Szervezeti diagram most az Azure AD egyszeri bejelentkezés konfigurálása, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **szervezeti diagram most** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/orgchartnow-tutorial/tutorial_orgchartnow_samlbase.png)

1. Az a **szervezeti diagram most tartomány és URL-címek** szakaszra, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód:

    ![Szervezeti diagram most tartomány és URL-címek egyszeri bejelentkezési adatait](./media/orgchartnow-tutorial/tutorial_orgchartnow_url.png)

    Az a **azonosító** szövegmezőbe írja be egy URL-címe: `https://sso2.orgchartnow.com`

1. Ellenőrizze **speciális URL-beállítások megjelenítése** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![Szervezeti diagram most tartomány és URL-címek egyszeri bejelentkezési adatait](./media/orgchartnow-tutorial/tutorial_orgchartnow_url1.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://sso2.orgchartnow.com/Shibboleth.sso/Login?entityID=<YourEntityID>&target=https://sso2.orgchartnow.com`
     
    > [!NOTE]
    > `<YourEntityID>` Az SAML Entitásazonosító átmásolva a gyors útmutató szakaszban az oktatóanyag egy későbbi része ismerteti.

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/orgchartnow-tutorial/tutorial_orgchartnow_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/orgchartnow-tutorial/tutorial_general_400.png)
    
1. Az a **szervezeti diagram most konfigurációs** területén kattintson **konfigurálása most a szervezeti diagram** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító** származó a **gyors útmutató szakaszban** és a segítségével elvégezheti **bejelentkezési URL-** a **szervezeti diagram most tartomány és URL-címek szakaszt**.

    ![Szervezeti diagram most konfiguráció](./media/orgchartnow-tutorial/tutorial_orgchartnow_configure.png) 

1. Az egyszeri bejelentkezés konfigurálása **szervezeti diagram most** oldalon kell küldenie a letöltött **metaadatainak XML** való [szervezeti diagram most támogatási csapatának](mailto:ocnsupport@officeworksoftware.com). Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/orgchartnow-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/orgchartnow-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/orgchartnow-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/orgchartnow-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-an-orgchart-now-test-user"></a>Hozzon létre egy szervezeti diagram most tesztfelhasználó számára

Ahhoz, hogy jelentkezzen be szervezeti diagram most az Azure AD-felhasználók, akkor ki kell építeni szervezeti diagram most be. 

1. Szervezeti diagram most támogatja a just-in-time-kiépítés, amely alapértelmezésben engedélyezve van. Új felhasználó próbál hozzáférni a szervezeti diagram most, ha még nem létezik jön létre. A just-in-time-felhasználó kiépítési szolgáltatás csak létrehoz egy **csak olvasható** egy egyszeri bejelentkezési kérelem érkezik egy felismert identitásszolgáltató és az e-mailt az SAML-előfeltétel a felhasználók listájában nem található felhasználó. Az Automatikus kiépítés funkció egy nevű-hozzáférési csoport létrehozására szeretne **általános** szervezeti diagram most. Kérjük, kövesse az alábbi lépéseket egy hozzáférési csoport létrehozására:

    a. Nyissa meg a **csoportok kezelése** kattintás után a beállítást a **fogaskerék** a felhasználói felület jobb felső sarkában található.

    ![Szervezeti diagram most csoportok](./media/orgchartnow-tutorial/tutorial_orgchartnow_manage.png)    

    b. Válassza ki a **Hozzáadás** ikon és a csoport nevét **általános** kattintson **OK**. 

    ![Szervezeti diagram most hozzáadása](./media/orgchartnow-tutorial/tutorial_orgchartnow_add.png)

    c. Válassza ki az általános vagy csak olvasható felhasználók érhetik el kívánja a mappák:

    ![Szervezeti diagram most mappák](./media/orgchartnow-tutorial/tutorial_orgchartnow_chart.png)

    d. **Zárolási** mappákat úgy, hogy csak a rendszergazda felhasználók módosíthatja őket. Nyomja le az **OK**.

    ![Szervezeti diagram zárolják](./media/orgchartnow-tutorial/tutorial_orgchartnow_lock.png)

1. Hozhat létre **rendszergazdai** felhasználók és **olvasási/írási** felhasználók, manuálisan kell létrehoznia a felhasználó annak érdekében, hogy az egyszeri bejelentkezés használatával a jogosultsági szint eléréséhez. Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:

    a. Szervezeti diagram most egy biztonsági-rendszergazdaként jelentkezzen be.

    b.  Kattintson a **beállítások** jobb felső sarokban, majd lépjen **felhasználók kezelése**.

    ![Szervezeti diagram most beállításai](./media/orgchartnow-tutorial/tutorial_orgchartnow_settings.png)

    c. Kattintson a **Hozzáadás** , és hajtsa végre az alábbi lépéseket:

    ![Szervezeti diagram most kezelése](./media/orgchartnow-tutorial/tutorial_orgchartnow_manageusers.png)

    * Az a **Felhasználóazonosító** szövegmezőbe írja be például a felhasználói azonosító **brittasimon@contoso.com**.

    * A **E-mail cím** szöveget adja meg az e-mailt, például a felhasználó **brittasimon@contoso.com**.

    * Kattintson a **Hozzáadás** parancsra.
    
### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon a hozzáférés biztosításával a szervezeti diagram most az Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Szervezeti diagram most Britta Simon rendel, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **szervezeti diagram most**.

    ![Az alkalmazások listáját a szervezeti diagram most hivatkozásra](./media/orgchartnow-tutorial/tutorial_orgchartnow_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a szervezeti diagram most csempére kattint, akkor kell lekérése automatikusan bejelentkezett a szervezeti diagram most alkalmazásba.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/orgchartnow-tutorial/tutorial_general_01.png
[2]: ./media/orgchartnow-tutorial/tutorial_general_02.png
[3]: ./media/orgchartnow-tutorial/tutorial_general_03.png
[4]: ./media/orgchartnow-tutorial/tutorial_general_04.png

[100]: ./media/orgchartnow-tutorial/tutorial_general_100.png

[200]: ./media/orgchartnow-tutorial/tutorial_general_200.png
[201]: ./media/orgchartnow-tutorial/tutorial_general_201.png
[202]: ./media/orgchartnow-tutorial/tutorial_general_202.png
[203]: ./media/orgchartnow-tutorial/tutorial_general_203.png


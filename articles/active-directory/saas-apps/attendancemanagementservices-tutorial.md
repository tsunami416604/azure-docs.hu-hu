---
title: 'Oktatóanyag: A részvételt szolgáltatások az Azure Active Directory-integráció |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a nyilvántartás szolgáltatások között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 1f56e612-728b-4203-a545-a81dc5efda00
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2018
ms.author: jeedes
ms.openlocfilehash: bcbb3d5d25fdc3de71045cedfd4634e47b3947bf
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55811954"
---
# <a name="tutorial-azure-active-directory-integration-with-attendance-management-services"></a>Oktatóanyag: Az Azure Active Directory-integráció jelenlét felügyeleti szolgáltatásaival

Ebben az oktatóanyagban elsajátíthatja, hogyan jelenlét felügyeleti szolgáltatások integrálása az Azure Active Directory (Azure AD).

Jelenlét szolgáltatások integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá jelenlét Management Services, Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett jelenlét Management Services (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a jelenléti szolgáltatások, a következőkre van szükség:

- Azure AD-előfizetés
- Egy jelenlét szolgáltatások egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Jelenlét szolgáltatások hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-attendance-management-services-from-the-gallery"></a>Jelenlét szolgáltatások hozzáadása a katalógusból
Jelenlét szolgáltatások integrálása az Azure AD beállításához szüksége jelenlét szolgáltatások hozzáadása a felügyelt SaaS-alkalmazások listájában a katalógusból.

**Jelenlét szolgáltatások hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **jelenlét szolgáltatások**, jelölje be **jelenlét szolgáltatások** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában jelenlét szolgáltatások](./media/attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés jelenlét szolgáltatások "Britta Simon" nevű tesztfelhasználó alapján.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó jelenlét felügyeleti szolgáltatások mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó jelenlét felügyeleti szolgáltatások közötti kapcsolat kapcsolatot kell hozható létre.

Az Azure AD egyszeri bejelentkezés a jelenléti szolgáltatások tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **Hozzon létre egy jelenlét szolgáltatások tesztfelhasználót** - a-megfelelője a Britta Simon rendelkezik, amely kapcsolódik az Azure AD felhasználói ábrázolása jelenlét felügyeleti szolgáltatások.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és jelenlét szolgáltatások alkalmazását az egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálása jelenlét felügyeleti szolgáltatásaival, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **jelenlét szolgáltatások** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_samlbase.png)

1. Az a **jelenlét felügyeleti szolgáltatások tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Jelenlét felügyeleti szolgáltatások tartomány és URL-címek egyszeri bejelentkezési adatait](./media/attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://id.obc.jp/<tenant information >/`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://id.obc.jp/<tenant information >/`

    > [!NOTE]
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL- és azonosító. Kapcsolattartó [jelenlét Management Services Client támogatási csapatának](https://www.obcnet.jp/) beolvasni ezeket az értékeket.

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/attendancemanagementservices-tutorial/tutorial_general_400.png)

1. Az a **jelenlét Felügyeletkonfigurálási szolgáltatások** területén kattintson **jelenlét szolgáltatások konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Jelenlét Felügyeletkonfigurálási szolgáltatások](./media/attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_configure.png) 

1. Egy másik böngészőablakban bejelentkezés jelenlét szolgáltatások vállalat webhelye rendszergazdaként.

1. Kattintson a **SAML-hitelesítés** alatt a **biztonság területén**.

    ![Jelenlét Felügyeletkonfigurálási szolgáltatások](./media/attendancemanagementservices-tutorial/user1.png)

1. Hajtsa végre az alábbi lépéseket:

    ![Jelenlét Felügyeletkonfigurálási szolgáltatások](./media/attendancemanagementservices-tutorial/user2.png)

    a. Válassza ki **használható SAML-hitelesítés**.

    b. Az a **azonosító** szövegmezőbe, illessze be az értéket a **SAML Entitásazonosító**, az Azure Portalról másolt. 

    c. Az a **hitelesítési végpont URL-címe** szövegmező, illessze be az értéket a **SAML egyszeri bejelentkezési szolgáltatás URL-cím**, az Azure Portalról másolt.

    d. Kattintson a **válasszon ki egy fájlt** feltölteni a tanúsítványt, amelyre letöltötte az Azure ad-ből.

    e. Válassza ki **tiltsa le a jelszó hitelesítést**.

    f. Kattintson a **regisztráció**

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás! Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/attendancemanagementservices-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/attendancemanagementservices-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/attendancemanagementservices-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/attendancemanagementservices-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-an-attendance-management-services-test-user"></a>Hozzon létre egy jelenlét szolgáltatások tesztfelhasználót.

Ahhoz, hogy az Azure AD-felhasználók jelenlét felügyeleti szolgáltatásokba, akkor ki kell építeni jelenlét Management szolgáltatásba. Felügyeleti szolgáltatások jelenlét, esetén kézi tevékenység kiépítése.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a jelenléti szolgáltatások vállalati webhely.

1. Kattintson a **felhasználókezelés** alatt a **biztonság területén**.

    ![Alkalmazott hozzáadása](./media/attendancemanagementservices-tutorial/user5.png)

1. Kattintson a **új szabályok bejelentkezés**.

    ![Alkalmazott hozzáadása](./media/attendancemanagementservices-tutorial/user3.png)

1. Az a **OBCiD információk** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Alkalmazott hozzáadása](./media/attendancemanagementservices-tutorial/user4.png)

    a. Az a **OBCiD** szövegmezőbe írja be az e-mailt, felhasználó, például **BrittaSimon@contoso.com**.

    b. Az a **jelszó** szövegmezőbe írja be a felhasználó jelszavát.

    c. Kattintson a **regisztráció**


### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon használandó a hozzáférés biztosításával jelenlét Management Services, Azure egyszeri bejelentkezést.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel jelenlét szolgáltatások, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **jelenlét szolgáltatások**.

    ![Az alkalmazások listáját a jelenléti szolgáltatások hivatkozás](./media/attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a jelenléti szolgáltatások csempére kattint, meg kell lekérése automatikusan bejelentkezett jelenlét szolgáltatások alkalmazását a.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/attendancemanagementservices-tutorial/tutorial_general_01.png
[2]: ./media/attendancemanagementservices-tutorial/tutorial_general_02.png
[3]: ./media/attendancemanagementservices-tutorial/tutorial_general_03.png
[4]: ./media/attendancemanagementservices-tutorial/tutorial_general_04.png

[100]: ./media/attendancemanagementservices-tutorial/tutorial_general_100.png

[200]: ./media/attendancemanagementservices-tutorial/tutorial_general_200.png
[201]: ./media/attendancemanagementservices-tutorial/tutorial_general_201.png
[202]: ./media/attendancemanagementservices-tutorial/tutorial_general_202.png
[203]: ./media/attendancemanagementservices-tutorial/tutorial_general_203.png


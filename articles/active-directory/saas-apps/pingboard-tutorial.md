---
title: 'Oktatóanyag: Azure Active Directoryval integrált Pingboard |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Pingboard között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: jeedes
ms.openlocfilehash: e07e85e60c8a4b93e4b0fd7bf43f470c4e3acc61
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36221189"
---
# <a name="tutorial-azure-active-directory-integration-with-pingboard"></a>Oktatóanyag: Azure Active Directoryval integrált Pingboard

Ebben az oktatóanyagban elsajátíthatja Pingboard integrálása az Azure Active Directory (Azure AD).

Pingboard integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a Pingboard hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett Pingboard (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs Pingboard, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Pingboard egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Pingboard hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-pingboard-from-the-gallery"></a>A gyűjteményből Pingboard hozzáadása
Az Azure AD integrálása a Pingboard konfigurálásához kell hozzáadnia Pingboard a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Pingboard hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások][2]

3. Kattintson a **Hozzáadás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **Pingboard**, jelölje be **Pingboard** eredmény panelen, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában Pingboard](./media/pingboard-tutorial/tutorial_pingboard_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján Pingboard.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Pingboard a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Pingboard közötti kapcsolat kapcsolatot kell létrehozni.

Ez a hivatkozás kapcsolat létesíti értéket rendeli az **felhasználónév** értékeként Azure AD-ben a **felhasználónév** Pingboard a.

Az Azure AD egyszeri bejelentkezést a Pingboard tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Pingboard tesztfelhasználó létrehozása](#create-a-pingboard-test-user)**  - való Britta Simon valami Pingboard, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Pingboard alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés Pingboard, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Pingboard** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2.  Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés párbeszédpanel](./media/pingboard-tutorial/tutorial_pingboard_samlbase.png)

3. Az a **Pingboard tartomány és az URL-címek** területen tegye a következőket, ha szeretne beállítani az alkalmazás **IDP** kezdeményezett mód:

    ![Az egyszeri bejelentkezés információk IDP Pingboard tartomány és az URL-címek](./media/pingboard-tutorial/tutorial_pingboard_url.png)

    a. Az a **azonosító** szövegmező, írja be az értéket, mint: `http://app.pingboard.com/sp`

    b. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe: `https://<entity-id>.pingboard.com/auth/saml/consume`

4. Ellenőrizze **megjelenítése speciális URL-beállításainak**, ha szeretne beállítani az alkalmazás **SP** kezdeményezett mód:

    ![Az egyszeri bejelentkezés információk SP Pingboard tartomány és az URL-címek](./media/pingboard-tutorial/tutorial_pingboard_sp_initiated01.png)

     Az a **bejelentkezési URL-cím** szövegmező, írja be az URL-CÍMÉT a következő mintát: `https://<sub-domain>.pingboard.com/sign_in`

    > [!NOTE]
    > Vegye figyelembe, hogy ezek az értékek nincsenek-e valódi. Frissítheti ezeket az értékeket a tényleges válasz URL-CÍMEN és bejelentkezési URL-cím. Ügyfél [Pingboard ügyfél-támogatási csoport](https://support.pingboard.com/) beolvasni ezeket az értékeket.

5. Az a **SAML-aláíró tanúsítványa** kattintson **metaadatainak XML-kódja** , és mentse az XML-fájlt a számítógépen.

    ![Pingboard metaadatainak XML-kódja](./media/pingboard-tutorial/tutorial_pingboard_certificate.png)

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/pingboard-tutorial/tutorial_general_400.png)

7. Egyszeri bejelentkezés konfigurálása Pingboard oldalon, nyisson meg egy új böngészőablakot, és jelentkezzen be a Pingboard fiókjához. Az egyszeri bejelentkezés beállítása Pingboard rendszergazdának kell lennie.

8. Válassza ki a felső menüben,, **alkalmazások > integrációja**

    ![Egyszeri bejelentkezés konfigurálása](./media/pingboard-tutorial/Pingboard_integration.png)

9. Az a **integrációja** lapon, keresse meg a **"Azure Active Directory"** csempére, majd kattintson rá.

    ![Pingboard egyszeri bejelentkezés integráció](./media/pingboard-tutorial/Pingboard_aad.png)

10. A következő kattintson a modális **"Beállítása"**

    ![Pingboard konfigurációs gomb](./media/pingboard-tutorial/Pingboard_configure.png)

11. A következő oldalon bizonyára észrevette, hogy "Azure SSO-integráció engedélyezve van". A letöltött metaadatok XML-fájl megnyitása a Jegyzettömbben, és illessze be a tartalom **IDP metaadatok**.

    ![Pingboard SSO konfigurálására szolgáló képernyőn](./media/pingboard-tutorial/Pingboard_sso_configure.png)

12. A fájl van hitelesítve, és minden rendben, ha az egyszeri bejelentkezés most engedélyezve lesz.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](./media/pingboard-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/pingboard-tutorial/create_aaduser_02.png)

3. Kattintson a párbeszédpanel tetején **Hozzáadás** megnyitásához a **felhasználói** párbeszédpanel.

    ![Hozzáadás gomb](./media/pingboard-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:

    ![A felhasználó párbeszédpanel](./media/pingboard-tutorial/create_aaduser_04.png)

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="create-a-pingboard-test-user"></a>Pingboard tesztfelhasználó létrehozása

Ez a szakasz célja Pingboard Britta Simon nevű felhasználót létrehozni. Pingboard támogatja az automatikus a felhasználók átadása, amely alapértelmezés szerint van engedélyezve. További részletek találhatók [Itt](pingboard-provisioning-tutorial.md) automatikus felhasználólétesítés konfigurálásához.

**Ha szeretne létrehozni a felhasználót manuálisan, akkor hajtsa végre a következő lépéseket:**

1. Jelentkezzen be rendszergazdaként a Pingboard vállalati webhely.

2. Kattintson a **"Alkalmazott hozzáadása"** gombra **Directory** lap.

    ![Alkalmazott hozzáadása](./media/pingboard-tutorial/create_testuser_add.png)

3. Az a **"Alkalmazott hozzáadása"** párbeszédpanel lapon, a következő lépésekkel:

    ![Felkérése](./media/pingboard-tutorial/create_testuser_name.png)

    a. Az a **teljes nevét** szövegmező, a felhasználó típusát a teljes nevet, például **Britta Simon**.

    b. Az a **E-mail** szövegmező, a felhasználó e-mail címe típusát, például **brittasimon@contoso.com**.

    c. Az a **beosztás** szövegmező, írja be a feladat Britta Simon.

    d. A a **hely** legördülő menüben válassza ki a helyet a Britta Simon.

    e. Kattintson a **Hozzáadás** parancsra.

4. A felhasználó hozzáadását megerősítéséhez megjelenik egy visszaigazoló képernyő.

    ![Erősítse meg](./media/pingboard-tutorial/create_testuser_confirm.png)

    > [!NOTE]
    > Az Azure Active Directory fióktulajdonos kap egy e-mailt, és a következő hivatkozás a fiók megerősítéséhez, mielőtt aktívvá válik.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Pingboard Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése Pingboard, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Pingboard**.

    ![Az alkalmazások listáját a Pingboard hivatkozás](./media/pingboard-tutorial/tutorial_pingboard_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](../active-directory-saas-access-panel-introduction.md).

Ha a hozzáférési panelen Pingboard csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az Pingboard alkalmazására.

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)
* [A felhasználók átadása konfigurálása](pingboard-provisioning-tutorial.md)

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
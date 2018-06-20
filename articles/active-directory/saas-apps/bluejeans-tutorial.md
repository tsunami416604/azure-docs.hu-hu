---
title: 'Oktatóanyag: Azure Active Directoryval integrált BlueJeans |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és BlueJeans között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: dfc634fd-1b55-4ba8-94a8-b8288429b6a9
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: jeedes
ms.openlocfilehash: 1e6875fb3795d558afdf05b0a57c6c9d3bf9dc78
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36216429"
---
# <a name="tutorial-azure-active-directory-integration-with-bluejeans"></a>Oktatóanyag: Azure Active Directoryval integrált BlueJeans

Ebben az oktatóanyagban elsajátíthatja BlueJeans integrálása az Azure Active Directory (Azure AD).

BlueJeans integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a BlueJeans hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett BlueJeans (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs BlueJeans, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy BlueJeans egyszeri bejelentkezés engedélyezve van az előfizetésben

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből BlueJeans hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-bluejeans-from-the-gallery"></a>A gyűjteményből BlueJeans hozzáadása
Az Azure AD integrálása a BlueJeans konfigurálásához kell hozzáadnia BlueJeans a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből BlueJeans hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra.

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **BlueJeans**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/bluejeans-tutorial/tutorial_bluejeans_search.png)

5. Az eredmények panelen válassza ki a **BlueJeans**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/bluejeans-tutorial/tutorial_bluejeans_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban konfigurálása, és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon." nevű tesztfelhasználó alapján BlueJeans

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó BlueJeans a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a BlueJeans közötti kapcsolat kapcsolatot kell létrehozni.

BlueJeans, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a BlueJeans tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[BlueJeans tesztfelhasználó létrehozása](#creating-a-bluejeans-test-user)**  - kell rendelkeznie a megfelelője a Britta Simon a BlueJeans, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az BlueJeans alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés BlueJeans, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **BlueJeans** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés konfigurálása](./media/bluejeans-tutorial/tutorial_bluejeans_samlbase.png)

3. Az a **BlueJeans tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/bluejeans-tutorial/tutorial_bluejeans_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://<companyname>.BlueJeans.com`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: `https://<companyname>.BlueJeans.com`

    > [!NOTE]
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Ügyfél [BlueJeans ügyfél-támogatási csoport](https://support.bluejeans.com/contact) beolvasni ezeket az értékeket.

4. Az a **SAML-aláíró tanúsítványa** kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/bluejeans-tutorial/tutorial_bluejeans_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/bluejeans-tutorial/tutorial_general_400.png)

6. A a **BlueJeans konfigurációs** kattintson **konfigurálása BlueJeans** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-cím, jelszó URL-cím módosítása és SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/bluejeans-tutorial/tutorial_bluejeans_configure.png) 

7. Egy másik webes böngészőablakban, jelentkezzen be a **BlueJeans** vállalati hely rendszergazdaként.

8. Ugrás a **ADMIN \> csoportbeállítások \> biztonsági**.

   ![Felügyeleti](./media/bluejeans-tutorial/IC785868.png "rendszergazda")

9. Az a **biztonsági** területen tegye a következőket:

   ![SAML-alapú egyszeri bejelentkezés](./media/bluejeans-tutorial/IC785869.png "SAML-alapú egyszeri bejelentkezés")

   a. Válassza ki **SAML-alapú egyszeri bejelentkezés**.

   b. Válassza ki **automatikus kiépítés engedélyezéséhez**.

10. Helyezze át, amely a következő lépéseket:

    ![Tanúsítvány-elérési út](./media/bluejeans-tutorial/IC785870.png "tanúsítvány elérési útja")

    a. Kattintson a **Choose File**, majd töltse fel a letöltött tanúsítvány.

    b. Beillesztés **SAML-alapú egyszeri bejelentkezési URL-címe** azokat a **bejelentkezési URL-cím** szövegmező.

    c. Beillesztés **jelszó URL-cím módosítása** azokat a **URL-cím jelszó módosítása** szövegmező.

    d. Beillesztés **Sign-Out URL-cím** azokat a **kijelentkezési URL-cím** szövegmező.

11. Helyezze át, amely a következő lépéseket:

    ![Módosítások mentése](./media/bluejeans-tutorial/IC785874.png "módosítások mentése")

    a. Az a **felhasználóazonosító** szövegmezőhöz típus `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    b. Az a **E-mail** szövegmezőhöz típus `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    c. Kattintson a **módosítások mentése**.

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/bluejeans-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/bluejeans-tutorial/create_aaduser_02.png)

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/bluejeans-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:

    ![Az Azure AD tesztfelhasználó létrehozása](./media/bluejeans-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="creating-a-bluejeans-test-user"></a>BlueJeans tesztfelhasználó létrehozása

Ez a szakasz célja BlueJeans Britta Simon nevű felhasználót létrehozni. BlueJeans támogatja az automatikus a felhasználók átadása, amely alapértelmezés szerint van engedélyezve. További részletek találhatók [Itt](bluejeans-provisioning-tutorial.md) automatikus felhasználólétesítés konfigurálásához.

**Ha szeretne létrehozni a felhasználót manuálisan, akkor hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a **BlueJeans** vállalati hely rendszergazdaként.

2. Ugrás a **ADMIN \> felhasználók kezelése \> felhasználó hozzáadása**.

   ![Felügyeleti](./media/bluejeans-tutorial/IC785877.png "rendszergazda")

   >[!IMPORTANT]
   >A **felhasználó hozzáadása** lap csak akkor használható, ha az a **Biztonság lap**, **automatikus kiépítés engedélyezéséhez** nincs bejelölve. 

3. Az a **felhasználó hozzáadása** területen tegye a következőket:

    ![Felhasználó hozzáadása](./media/bluejeans-tutorial/IC785886.png "felhasználó hozzáadása")

    a. Adjon meg egy **BlueJeans felhasználónév**, egy **E-mail cím**, egy **BlueJeans értekezlet azonosítója**, egy **moderátor PIN-kód**, egy **teljes nevét**, a **vállalati** szeretné azokat a kapcsolódó szövegmezők rendelkezés érvényes AAD-fiók.

    b. Kattintson a **felhasználó hozzáadása**.

>[!NOTE]
>Bármely más BlueJeans felhasználói fiók létrehozása eszközök vagy rendelkezés AAD felhasználói fiókokhoz BlueJeans által nyújtott API-k.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés BlueJeans Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200]

**Britta Simon hozzárendelése BlueJeans, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201]

2. Az alkalmazások listában válassza ki a **BlueJeans**.

    ![Egyszeri bejelentkezés konfigurálása](./media/bluejeans-tutorial/tutorial_bluejeans_app.png)

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.

### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen BlueJeans csempére kattint, BlueJeans alkalmazás bejelentkezési oldalán szerezheti be.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)
* [A felhasználók átadása konfigurálása](bluejeans-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/bluejeans-tutorial/tutorial_general_01.png
[2]: ./media/bluejeans-tutorial/tutorial_general_02.png
[3]: ./media/bluejeans-tutorial/tutorial_general_03.png
[4]: ./media/bluejeans-tutorial/tutorial_general_04.png

[100]: ./media/bluejeans-tutorial/tutorial_general_100.png

[200]: ./media/bluejeans-tutorial/tutorial_general_200.png
[201]: ./media/bluejeans-tutorial/tutorial_general_201.png
[202]: ./media/bluejeans-tutorial/tutorial_general_202.png
[203]: ./media/bluejeans-tutorial/tutorial_general_203.png

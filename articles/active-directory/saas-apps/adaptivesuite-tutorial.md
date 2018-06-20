---
title: 'Oktatóanyag: Azure Active Directory-integráció adaptív, amelyen |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és adaptív Insights között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/14/2018
ms.author: jeedes
ms.openlocfilehash: 307c3cf258a74d1ddfb409f0d5b22d9e1fd6bf4b
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36213821"
---
# <a name="tutorial-azure-active-directory-integration-with-adaptive-insights"></a>Oktatóanyag: Azure Active Directory-integráció adaptív, amelyen

Ebben az oktatóanyagban elsajátíthatja adaptív Insights integrálása az Azure Active Directory (Azure AD).

Adaptív Insights integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a adaptív kínáló rendelkező Azure AD-ben
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett adaptív insights (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Adaptív, amelyen az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy adaptív Insights egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Adaptív Insights hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-adaptive-insights-from-the-gallery"></a>Adaptív Insights hozzáadása a gyűjteményből
Adaptív betekintést az Azure AD integrálása konfigurálásához szüksége adaptív Insights hozzáadása a kezelt SaaS-alkalmazások listáját a gyűjteményből.

**Adaptív Insights hozzáadása a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **adaptív Insights**, jelölje be **adaptív Insights** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezést az úgynevezett "Britta Simon." tesztfelhasználó alapján adaptív insights szolgáltatással

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó adaptív insightsban a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó adaptív insightsban közötti kapcsolat kapcsolatot kell létrehozni.

Az Azure AD egyszeri bejelentkezést, amelyen adaptív tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Egy adaptív Insights tesztfelhasználó létrehozása](#creating-an-adaptive-insights-test-user)**  - való egy megfelelője a Britta Simon adaptív Insights, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az adaptív Insights alkalmazásban.

**Adaptív, amelyen konfigurálhatja az Azure AD egyszeri bejelentkezést, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **adaptív Insights** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés konfigurálása](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_samlbase.png)

3. Az a **adaptív Insights tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_url.png)

    a. Az a **azonosítója (entitás azonosítója)** szövegmező, adja meg a következő minta használatával URL-címe: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    b. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    >[!NOTE]
    > Azonosítója (entitás azonosítója) és a válasz URL-CÍMEN értékek lekérheti az adaptív Insights **SAML SSO beállítások** lap.

4. A a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_certificate.png)

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/adaptivesuite-tutorial/tutorial_general_400.png)

6. A a **adaptív Insights beállításait** kattintson **adaptív Insights konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító és SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_configure.png) 

7. Egy másik webes böngészőablakban jelentkezzen be a adaptív Insights vállalati webhely rendszergazdaként.

8. Ugrás a **Admin**.

    ![Felügyeleti](./media/adaptivesuite-tutorial/IC805644.png "rendszergazda")

9. Az a **felhasználók és szerepkörök** kattintson **SAML SSO beállítások kezelése**.

    ![SAML SSO beállításainak kezelése](./media/adaptivesuite-tutorial/IC805645.png "SAML SSO beállításainak kezelése")

10. Az a **SAML SSO beállítások** lapon, a következő lépésekkel:

    ![SAML SSO beállítások](./media/adaptivesuite-tutorial/IC805646.png "SAML SSO-beállítások")

    a. Az a **identitás szolgáltatónevet** szövegmező, adja meg a konfiguráció nevét.

    b. Beillesztés a **SAML Entitásazonosító** az Azure portálon átmásolja értéket a **identitásszolgáltató Entitásazonosító** szövegmező.

    c. Beillesztés a **SAML-alapú egyszeri bejelentkezési URL-címe** az Azure portálon átmásolja értéket a **identitásszolgáltató egyszeri bejelentkezési URL-cím** szövegmező.

    d. Beillesztés a **SAML-alapú egyszeri bejelentkezési URL-címe** az Azure portálon átmásolja értéket a **egyéni kijelentkezési URL-cím** szövegmező.

    e. A letöltött tanúsítvány feltöltése, kattintson a **fájl kiválasztása**.

    f. Válassza ki a következőket:

    * **SAML-alapú felhasználói azonosító**, jelölje be **adaptív Insights felhasználónevét**.

    * **SAML-alapú felhasználói azonosító hely**, jelölje be **felhasználóazonosító tárgyában csupa kisbetűvel NameID**.

    * **SAML NameID formátum**, jelölje be **E-mail cím**.

    * **SAML engedélyezése**, jelölje be **SAML SSO engedélyezése és a közvetlen adaptív Insights bejelentkezési**.

    g. Másolás **adaptív Insights egyszeri bejelentkezési URL-cím** és illessze be a **azonosítója (entitás azonosítója)** és **válasz URL-CÍMEN** a szövegmezők a **adaptív Insights tartomány és az URL-címek** szakaszban az Azure portálon.

    h. Kattintson a **Save** (Mentés) gombra.

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/adaptivesuite-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/adaptivesuite-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/adaptivesuite-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:

    ![Az Azure AD tesztfelhasználó létrehozása](./media/adaptivesuite-tutorial/create_aaduser_04.png)

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="creating-an-adaptive-insights-test-user"></a>Egy adaptív Insights tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók adaptív Insights bejelentkezni, akkor ki kell építenie az adaptív Insights. Adaptív Insights, ha egy kézi tevékenység.

**Adja meg a felhasználók átadása, hajtsa végre az alábbi lépéseket:** 

1. Jelentkezzen be a **adaptív Insights** vállalati hely rendszergazdaként.
2. Ugrás a **Admin**.

   ![Felügyeleti](./media/adaptivesuite-tutorial/IC805644.png "rendszergazda")
3. Az a **felhasználók és szerepkörök** kattintson **felhasználó hozzáadása**.

   ![Felhasználó hozzáadása](./media/adaptivesuite-tutorial/IC805648.png "felhasználó hozzáadása")
4. Az a **új felhasználó** területen tegye a következőket:

   ![Küldje el](./media/adaptivesuite-tutorial/IC805649.png "elküldése")

   a. Típus a **neve**, **bejelentkezési**, **E-mail**, **jelszó** egy érvényes Azure Active Directory-felhasználó szeretné azokat a kapcsolódó kiépítése szövegmezőből.

   b. Válassza ki a **szerepkör**.

   c. Kattintson a **nyújt**.

>[!NOTE]
>Bármely más adaptív Insights felhasználói fiók létrehozása eszközök vagy rendelkezés AAD felhasználói fiókokhoz adaptív Insights által nyújtott API-k.
>

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon Azure egyszeri bejelentkezés által biztosított hozzáférés adaptív Insights használatához.

![Felhasználó hozzárendelése][200]

**Adaptív Insights Britta Simon rendel, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201]

2. Az alkalmazások listában válassza ki a **adaptív Insights**.

    ![Egyszeri bejelentkezés konfigurálása](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_app.png)

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.

### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ez a szakasz célja a Microsoft Azure AD az egyszeri bejelentkezés konfiguráció teszteléséhez a hozzáférési panelen.

Ha a hozzáférési panelen adaptív Insights csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az adaptív Insights alkalmazására.

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/adaptivesuite-tutorial/tutorial_general_01.png
[2]: ./media/adaptivesuite-tutorial/tutorial_general_02.png
[3]: ./media/adaptivesuite-tutorial/tutorial_general_03.png
[4]: ./media/adaptivesuite-tutorial/tutorial_general_04.png

[100]: ./media/adaptivesuite-tutorial/tutorial_general_100.png

[200]: ./media/adaptivesuite-tutorial/tutorial_general_200.png
[201]: ./media/adaptivesuite-tutorial/tutorial_general_201.png
[202]: ./media/adaptivesuite-tutorial/tutorial_general_202.png
[203]: ./media/adaptivesuite-tutorial/tutorial_general_203.png

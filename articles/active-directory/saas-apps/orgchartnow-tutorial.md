---
title: 'Oktatóanyag: Azure Active Directory-integráció a szervezeti diagram most |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a szervezeti diagram most között.
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
ms.openlocfilehash: f68c5d6a022cccecde3b3eb272e51f75ae6bc50e
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36222061"
---
# <a name="tutorial-azure-active-directory-integration-with-orgchart-now"></a>Oktatóanyag: Azure Active Directory-integráció a szervezeti diagram most

Ebben az oktatóanyagban elsajátíthatja a szervezeti diagram most integrálása az Azure Active Directory (Azure AD).

Szervezeti diagram most integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér a szervezeti diagram most vezérelheti.
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni aláírt a szervezeti diagram most (egyszeri bejelentkezés).
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a szervezeti diagram most, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- A szervezeti diagram most egyszeri bejelentkezés engedélyezve van az előfizetésben

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Szervezeti diagram most hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-orgchart-now-from-the-gallery"></a>Szervezeti diagram most hozzáadása a gyűjteményből
Az Azure AD integrálása a szervezeti diagram most konfigurálásához kell hozzáadnia szervezeti diagram most már a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a szervezeti diagram most már a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **szervezeti diagram most**, jelölje be **szervezeti diagram most** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Szervezeti diagram most az eredménylistában](./media/orgchartnow-tutorial/tutorial_orgchartnow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban konfigurálása és tesztelése az Azure AD egyszeri bejelentkezéssel a szervezeti diagram most "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD számára a szervezeti diagram most a párjukhoz felhasználó egy felhasználó számára az Azure AD kell. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a szervezeti diagram most közötti kapcsolat kapcsolatot kell létrehozni.

Az Azure AD egyszeri bejelentkezést a szervezeti diagram most tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Hozzon létre egy szervezeti diagram most tesztfelhasználó](#create-an-orgchart-now-test-user)**  - való Britta Simon egy megfelelője a szervezeti diagram most, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és a szervezeti diagram most alkalmazásban egyszeri bejelentkezés konfigurálása.

**A szervezeti diagram most konfigurálása az Azure AD egyszeri bejelentkezést, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **szervezeti diagram most** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/orgchartnow-tutorial/tutorial_orgchartnow_samlbase.png)

3. Az a **szervezeti diagram most tartomány és az URL-címek** szakaszban, ha szeretne beállítani az alkalmazás **IDP** kezdeményezett mód:

    ![Szervezeti diagram most tartomány és az URL-címeket az egyszeri bejelentkezés információk](./media/orgchartnow-tutorial/tutorial_orgchartnow_url.png)

    Az a **azonosító** szövegmezőhöz URL-címet írja be: `https://sso2.orgchartnow.com`

4. Ellenőrizze **megjelenítése speciális URL-beállításainak** , és végezze el a következő lépés, ha szeretne beállítani az alkalmazás **SP** kezdeményezett mód:

    ![Szervezeti diagram most tartomány és az URL-címeket az egyszeri bejelentkezés információk](./media/orgchartnow-tutorial/tutorial_orgchartnow_url1.png)

    Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://sso2.orgchartnow.com/Shibboleth.sso/Login?entityID=<YourEntityID>&target=https://sso2.orgchartnow.com`
     
    > [!NOTE]
    > `<YourEntityID>` a SAML Entitásazonosító átmásolva oktatóanyag későbbi részében leírt rövid összefoglaló szakaszban.

5. Az a **SAML-aláíró tanúsítványa** területen kattintson **metaadatainak XML-kódja** és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/orgchartnow-tutorial/tutorial_orgchartnow_certificate.png) 

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/orgchartnow-tutorial/tutorial_general_400.png)
    
7. Az a **szervezeti diagram most konfigurációs** kattintson **konfigurálása most a szervezeti diagram** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító** a a **rövid összefoglaló szakasz** , és végezze el **bejelentkezési URL-cím** a **szervezeti diagram most tartomány és az URL-címek szakasz**.

    ![Szervezeti diagram most konfiguráció](./media/orgchartnow-tutorial/tutorial_orgchartnow_configure.png) 

8. Egyszeri bejelentkezés konfigurálása **szervezeti diagram most** oldalon kell küldeniük a letöltött **metaadatainak XML-kódja** való [szervezeti diagram most támogatási csoport](mailto:ocnsupport@officeworksoftware.com). Akkor állítsa be ezt a beállítást, hogy a SAML SSO kapcsolat mindkét oldalán megfelelően beállítva.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/orgchartnow-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/orgchartnow-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/orgchartnow-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/orgchartnow-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-an-orgchart-now-test-user"></a>Hozzon létre egy szervezeti diagram most tesztfelhasználó számára

Ahhoz, hogy a szervezeti diagram most már bejelentkezhet az Azure AD felhasználók, akkor ki kell építenie a szervezeti diagram most. 

1. Szervezeti diagram most már támogatja a just-in-time kiosztást, amely alapértelmezés szerint van engedélyezve. Új felhasználó jön létre, ha még nem létezik a szervezeti diagram most elérésére tett kísérlet során. A szolgáltatás létesítési just-in-time felhasználó csak létrehoz egy **írásvédett** kérelmet SSO felismert IDP származik, és az e-mailt a SAML-előfeltétel a nem találhatók meg a felhasználó. Az automatikus létesítési szolgáltatás egy című hozzáférési csoport létrehozására szeretne **általános** a szervezeti diagram most. Kérjük, kövesse a következő lépések végrehajtásával egy hozzáférési csoport létrehozására:

    a. Lépjen a **csoportok kezelése** kattintás után a beállítás a **áttételi** a felhasználói felület jobb felső sarkában található.

    ![Szervezeti diagram most csoportok](./media/orgchartnow-tutorial/tutorial_orgchartnow_manage.png)    

    b. Válassza ki a **Hozzáadás** ikonra, és a csoport nevét **általános** kattintson **OK**. 

    ![Szervezeti diagram most hozzáadása](./media/orgchartnow-tutorial/tutorial_orgchartnow_add.png)

    c. Az általános vagy csak olvasható felhasználók érhetik el kívánja Mappaválasztás:

    ![Szervezeti diagram most mappák](./media/orgchartnow-tutorial/tutorial_orgchartnow_chart.png)

    d. **Zárolási** a mappákat, hogy csak a rendszergazda felhasználók módosíthatják azokat. Nyomja le az **OK**.

    ![Szervezeti diagram most zárolása](./media/orgchartnow-tutorial/tutorial_orgchartnow_lock.png)

2. Létrehozásához **Admin** felhasználók és **olvasási/írási** felhasználók, manuálisan kell létrehoznia a felhasználó jogosultsági szintre SSO keresztül történő hozzáférés érdekében. Felhasználói fiók létrehozásához hajtsa végre az alábbi lépéseket:

    a. Jelentkezzen be szervezeti diagram most egy biztonsági-rendszergazdaként.

    b.  Kattintson a **beállítások** jobb felső sarokban, és navigáljon arra **felhasználók kezelése**.

    ![Szervezeti diagram most beállításai](./media/orgchartnow-tutorial/tutorial_orgchartnow_settings.png)

    c. Kattintson a **Hozzáadás** és hajtsa végre a következő lépéseket:

    ![Szervezeti diagram most kezelése](./media/orgchartnow-tutorial/tutorial_orgchartnow_manageusers.png)

    * Az a **Felhasználóazonosító** szövegmező, adja meg a Felhasználóazonosítót, például a **brittasimon@contoso.com**.

    * A **E-mail cím** szöveg mezőbe írja be például a felhasználó e-mail **brittasimon@contoso.com**.

    * Kattintson a **Hozzáadás** parancsra.
    
### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban Britta Simon hozzáférést biztosít a szervezeti diagram most által használandó Azure egyszeri bejelentkezés engedélyezése.

![A felhasználói szerepkör hozzárendelése][200] 

**Szervezeti diagram most Britta Simon rendel, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **szervezeti diagram most**.

    ![Az alkalmazások listáját a szervezeti diagram most hivatkozás](./media/orgchartnow-tutorial/tutorial_orgchartnow_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen a szervezeti diagram most csempére kattint, akkor kell beolvasása automatikusan bejelentkezett a szervezeti diagram most alkalmazásba.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)



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


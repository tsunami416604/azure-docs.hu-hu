---
title: 'Oktatóanyag: Azure Active Directoryval integrált Yodeck |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Yodeck között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b2c8dccb-eeb0-4f4d-a24d-8320631ce819
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: jeedes
ms.openlocfilehash: ba48c5d14775004e22dd67a2932c8969e6dfdd6d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34354180"
---
# <a name="tutorial-azure-active-directory-integration-with-yodeck"></a>Oktatóanyag: Azure Active Directoryval integrált Yodeck

Ebben az oktatóanyagban elsajátíthatja Yodeck integrálása az Azure Active Directory (Azure AD).

Yodeck integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér Yodeck szabályozhatja.
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett Yodeck (egyszeri bejelentkezés) számára a saját Azure AD-fiókok.
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs Yodeck, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Yodeck egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Yodeck hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-yodeck-from-the-gallery"></a>A gyűjteményből Yodeck hozzáadása
Az Azure AD integrálása a Yodeck konfigurálásához kell hozzáadnia Yodeck a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Yodeck hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **Yodeck**, jelölje be **Yodeck** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában Yodeck](./media/active-directory-saas-yodeck-tutorial/tutorial_yodeck_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján Yodeck.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Yodeck a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Yodeck közötti kapcsolat kapcsolatot kell létrehozni.

Az Azure AD egyszeri bejelentkezést a Yodeck tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Yodeck tesztfelhasználó létrehozása](#create-a-yodeck-test-user)**  - való Britta Simon valami Yodeck, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Yodeck alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés Yodeck, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Yodeck** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-yodeck-tutorial/tutorial_yodeck_samlbase.png)

3. Az a **Yodeck tartomány és az URL-címek** területen tegye a következőket, ha szeretne beállítani az alkalmazás **IDP** kezdeményezett mód:

    ![Az egyszeri bejelentkezés információk Yodeck tartomány és az URL-címek](./media/active-directory-saas-yodeck-tutorial/tutorial_yodeck_url.png)

    Az a **azonosítója (entitás azonosítója)** szövegmező, írja be az URL-cím: `https://app.yodeck.com/api/v1/account/metadata/`

4. Ellenőrizze **megjelenítése speciális URL-beállításainak** , és végezze el a következő lépés, ha szeretne beállítani az alkalmazás **SP** kezdeményezett mód:

    ![Az egyszeri bejelentkezés információk Yodeck tartomány és az URL-címek](./media/active-directory-saas-yodeck-tutorial/tutorial_yodeck_url1.png)

    Az a **bejelentkezési URL-cím** szövegmező, írja be az URL-cím: `https://app.yodeck.com/login`

5. Az a **SAML-aláíró tanúsítványa** területen kattintson a Másolás gombra másolása **alkalmazás összevonási metaadatainak URL-címe** és illessze be a Jegyzettömbbe.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-yodeck-tutorial/tutorial_yodeck_certificate.png)

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-yodeck-tutorial/tutorial_general_400.png)
    
7. Egy másik webes böngészőablakban jelentkezzen be a Yodeck vállalati webhely rendszergazdaként.

8. Kattintson a **felhasználói beállítások** lehetőséget a képernyő jobb felső sarokban a lapon, és válassza ki a **Fiókbeállítások**.

    ![Yodeck konfiguráció](./media/active-directory-saas-yodeck-tutorial/configure1.png)

9. Válassza ki **SAML** és hajtsa végre a következő lépéseket:

    ![Yodeck konfiguráció](./media/active-directory-saas-yodeck-tutorial/configure2.png)

    a. Válassza ki **importálás az URL-cím**.

    b. A a **URL-cím** szövegmező, illessze be a **alkalmazás összevonási metaadatainak URL-címe** értéket, amely a Azure-portálon, majd kattintson a másolt **importálási**.
    
    c. Importálás után **alkalmazás összevonási metaadatainak URL-címe**, a többi mező automatikusan feltöltéséhez.

    d. Kattintson a **Save** (Mentés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-yodeck-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-yodeck-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/active-directory-saas-yodeck-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-yodeck-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-yodeck-test-user"></a>Yodeck tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Yodeck bejelentkezni, akkor ki kell építenie a Yodeck.
Yodeck, ha egy kézi tevékenység.

**Felhasználói fiók létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a Yodeck vállalati webhely.

2. Kattintson a **felhasználói beállítások** lehetőséget a képernyő jobb felső sarokban a lapon, és válassza ki a **felhasználók**.

    ![Alkalmazott hozzáadása](./media/active-directory-saas-yodeck-tutorial/user1.png)

3. Kattintson a **+ felhasználói** megnyitásához a **felhasználó adatait** fülre.

    ![Alkalmazott hozzáadása](./media/active-directory-saas-yodeck-tutorial/user2.png)

4. Az a **felhasználó adatait** párbeszédpanel lapon, a következő lépésekkel:

    ![Alkalmazott hozzáadása](./media/active-directory-saas-yodeck-tutorial/user3.png)

    a. Az a **Utónév** szövegmezőhöz, például a felhasználó utónevét típusú **Britta**.

    b. Az a **Vezetéknév** szövegmező, a felhasználó vezetékneve típusát, például **Simon**.

    c. Az a **E-mail** szövegmező, a felhasználó e-mail címe típusát, például **brittasimon@contoso.com**.

    d. Válasszon megfelelő **fiókjához hozzárendelt jogosultságoktól** lehetőség szerint a szervezeti követelmény.
    
    e. Kattintson a **Save** (Mentés) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Yodeck Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200]

**Britta Simon hozzárendelése Yodeck, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201]

2. Az alkalmazások listában válassza ki a **Yodeck**.

    ![Az alkalmazások listáját a Yodeck hivatkozás](./media/active-directory-saas-yodeck-tutorial/tutorial_yodeck_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Yodeck csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az Yodeck alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-yodeck-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-yodeck-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-yodeck-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-yodeck-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-yodeck-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-yodeck-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-yodeck-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-yodeck-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-yodeck-tutorial/tutorial_general_203.png


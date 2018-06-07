---
title: 'Oktatóanyag: Azure Active Directoryval integrált állomány |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és állomány között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7b2c3ac5-17f1-49a0-8961-c541b258d4b1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: jeedes
ms.openlocfilehash: 30bdc71c9403a8de50773cee68c86f3f49bd359a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655013"
---
# <a name="tutorial-azure-active-directory-integration-with-flock"></a>Oktatóanyag: Azure Active Directoryval integrált állomány

Ebben az oktatóanyagban elsajátíthatja állomány integrálása az Azure Active Directory (Azure AD).

Az Azure ad-val integráló állomány a következő előnyöket biztosítja:

- Az Azure AD-állomány hozzáféréssel rendelkező szabályozhatja.
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett állomány (egyszeri bejelentkezés) számára a saját Azure AD-fiókok.
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs állományt, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy állomány egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből hozzáadását állomány
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-flock-from-the-gallery"></a>A gyűjteményből hozzáadását állomány
Az Azure AD-be állomány integráció konfigurálásához szüksége hozzáadása a gyűjteményből állományt a kezelt SaaS-alkalmazások listáját.

**A gyűjteményből állomány hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **állomány**, jelölje be **állomány** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában állomány](./media/active-directory-saas-flock-tutorial/tutorial_flock_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján állomány.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó állomány a felhasználó Azure AD-ben. Ez azt jelenti egy Azure AD-felhasználó és a kapcsolódó felhasználó a állomány közötti kapcsolat kapcsolatot kell létrehozni.

Az Azure AD egyszeri bejelentkezést a állomány tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Állomány tesztfelhasználó létrehozása](#create-a-flock-test-user)**  - való egy megfelelője a Britta Simon állományt, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az állomány alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés állományt, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **állomány** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-flock-tutorial/tutorial_flock_samlbase.png)

3. Az a **állomány tartomány és az URL-címek** területen tegye a következőket:

    ![Az egyszeri bejelentkezés információt állomány tartomány és az URL-címek](./media/active-directory-saas-flock-tutorial/tutorial_flock_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://<subdomain>.flock.com/`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: `https://<subdomain>.flock.com/`

    > [!NOTE]
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Ügyfél [állomány ügyfél-támogatási csoport](mailto:support@flock.com) beolvasni ezeket az értékeket.

4. Az a **SAML-aláíró tanúsítványa** kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-flock-tutorial/tutorial_flock_certificate.png)

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-flock-tutorial/tutorial_general_400.png)

6. A a **állomány konfigurációs** kattintson **konfigurálása állomány** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító és SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Állomány konfiguráció](./media/active-directory-saas-flock-tutorial/tutorial_flock_configure.png) 

7. Egy másik webes böngészőablakban jelentkezzen be a állomány vállalati webhely rendszergazdaként.

8. Válassza ki **hitelesítési** fülre a bal oldali navigációs panelen, majd jelölje **SAML-alapú hitelesítés**.

    ![Állomány konfiguráció](./media/active-directory-saas-flock-tutorial/configure1.png)

9. Az a **SAML-alapú hitelesítés** területen tegye a következőket:

    ![Állomány konfiguráció](./media/active-directory-saas-flock-tutorial/configure2.png)

    a. Az a **SAML 2.0 Endpoint(HTTP)** szövegmezőhöz Beillesztés **SAML-alapú egyszeri bejelentkezési URL-címe** érték, amely az Azure portálról másolta.

    b. Az a **Identity Provider kibocsátó** szövegmezőhöz Beillesztés **SAML Entitásazonosító** érték, amely az Azure portálról másolta.

    c. Nyissa meg a letöltött **Certificate(Base64)** a Jegyzettömbben az Azure portálról illesztheti be a **nyilvános tanúsítvány** szövegmező.

    d. Kattintson a **Save** (Mentés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-flock-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-flock-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/active-directory-saas-flock-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-flock-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="create-a-flock-test-user"></a>Állomány tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók állomány bejelentkezni, akkor ki kell építenie állomány be. Állományt, ha egy kézi tevékenység.

**Felhasználói fiók létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a állomány vállalati webhely.

2. Kattintson a **kezelése Team** a bal oldali navigációs panelről.

    ![Alkalmazott hozzáadása](./media/active-directory-saas-flock-tutorial/user1.png)

3. Kattintson a **tag hozzáadása** fülre, majd jelölje **csapattagok**.

    ![Alkalmazott hozzáadása](./media/active-directory-saas-flock-tutorial/user2.png)

4. Írja be például a felhasználó e-mail címe **Brittasimon@contoso.com** majd **felhasználó hozzáadása**.

    ![Alkalmazott hozzáadása](./media/active-directory-saas-flock-tutorial/user3.png)

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés állomány Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200]

**Britta Simon hozzárendelése állományt, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201]

2. Az alkalmazások listában válassza ki a **állomány**.

    ![Az alkalmazások listáját a állomány hivatkozás](./media/active-directory-saas-flock-tutorial/tutorial_flock_app.png)

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen állomány csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az állomány alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-flock-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-flock-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-flock-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-flock-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-flock-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-flock-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-flock-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-flock-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-flock-tutorial/tutorial_general_203.png

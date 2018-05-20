---
title: 'Oktatóanyag: Azure Active Directoryval integrált LaunchDarkly |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és LaunchDarkly között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0e9cb37e-16bf-493b-bfc8-8d9840545a1e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: jeedes
ms.openlocfilehash: f6611009ae10bf7a0d5c7f20f3ad26e9d41e456e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-launchdarkly"></a>Oktatóanyag: Azure Active Directoryval integrált LaunchDarkly

Ebben az oktatóanyagban elsajátíthatja LaunchDarkly integrálása az Azure Active Directory (Azure AD).

LaunchDarkly integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér LaunchDarkly szabályozhatja.
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett LaunchDarkly (egyszeri bejelentkezés) számára a saját Azure AD-fiókok.
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs LaunchDarkly, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy LaunchDarkly egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből LaunchDarkly hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-launchdarkly-from-the-gallery"></a>A gyűjteményből LaunchDarkly hozzáadása
Az Azure AD integrálása a LaunchDarkly konfigurálásához kell hozzáadnia LaunchDarkly a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből LaunchDarkly hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **LaunchDarkly**, jelölje be **LaunchDarkly** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában LaunchDarkly](./media/active-directory-saas-launchdarkly-tutorial/tutorial_launchdarkly_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján LaunchDarkly.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó LaunchDarkly a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a LaunchDarkly közötti kapcsolat kapcsolatot kell létrehozni.

Az Azure AD egyszeri bejelentkezést a LaunchDarkly tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[LaunchDarkly tesztfelhasználó létrehozása](#create-a-launchdarkly-test-user)**  - való Britta Simon valami LaunchDarkly, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az LaunchDarkly alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés LaunchDarkly, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **LaunchDarkly** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-launchdarkly-tutorial/tutorial_launchdarkly_samlbase.png)

3. Az a **LaunchDarkly tartomány és az URL-címek** területen tegye a következőket, ha szeretne beállítani az alkalmazás **IDP** kezdeményezett mód:

    ![Az egyszeri bejelentkezés információk LaunchDarkly tartomány és az URL-címek](./media/active-directory-saas-launchdarkly-tutorial/tutorial_launchdarkly_url.png)

    a. Az a **azonosítója (entitás azonosítója)** szövegmező, írja be az URL-cím: `app.launchdarkly.com`

    b. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe: `https://app.launchdarkly.com/trust/saml2/acs/<customers-unique-id>`
    
    > [!NOTE]
    > A válasz URL-cím értéke nincs valós. Az érték a tényleges válasz URL-címet, az oktatóanyag későbbi részében ismertetett frissíti.

4. Ellenőrizze **megjelenítése speciális URL-beállításainak** , és végezze el a következő lépés, ha szeretne beállítani az alkalmazás **SP** kezdeményezett mód:

    ![Az egyszeri bejelentkezés információk LaunchDarkly tartomány és az URL-címek](./media/active-directory-saas-launchdarkly-tutorial/tutorial_launchdarkly_url1.png)

    Az a **bejelentkezési URL-cím** szövegmező, írja be az URL-cím: `https://app.launchdarkly.com`

5. A a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-launchdarkly-tutorial/tutorial_launchdarkly_certificate.png) 

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-launchdarkly-tutorial/tutorial_general_400.png)
    
7. A a **LaunchDarkly konfigurációs** kattintson **konfigurálása LaunchDarkly** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![LaunchDarkly konfiguráció](./media/active-directory-saas-launchdarkly-tutorial/tutorial_launchdarkly_configure.png)

8. Egy másik webes böngészőablakban jelentkezzen be a LaunchDarkly vállalati webhely rendszergazdaként.

9. Válassza ki **Fiókbeállítások** a bal oldali navigációs panelről.

    ![LaunchDarkly konfiguráció](./media/active-directory-saas-launchdarkly-tutorial/configure1.png)

10. Kattintson a **biztonsági** fülre.

    ![LaunchDarkly konfiguráció](./media/active-directory-saas-launchdarkly-tutorial/configure2.png)

11. Kattintson a **engedélyezése egyszeri Bejelentkezéses** , majd **SZERKESZTÉS SAML-alapú konfigurációs**.

    ![LaunchDarkly konfiguráció](./media/active-directory-saas-launchdarkly-tutorial/configure3.png)

12. Az a **a SAML-konfigurációjának a szerkesztésével** területen tegye a következőket:

    ![LaunchDarkly konfiguráció](./media/active-directory-saas-launchdarkly-tutorial/configure4.png)

    a. Másolás a **SAML-ügyfél szolgáltatás URL-címe** a példány és illessze be a válasz URL-CÍMEN szövegmezőjének **LaunchDarkly tartomány és az URL-címek** Azure-portál szakaszban.

    b. Az a **bejelentkezési URL-cím** szövegmező, illessze be a **egyszeri bejelentkezési URL-címe** értéket, amely az Azure portálról másolta.

    c. Nyissa meg a letöltött tanúsítvány az Azure-portálon a Jegyzettömbbe. másolja a tartalmat, majd illessze be azt a **X.509 tanúsítvány** mezőbe, vagy Ön is közvetlenül feltölthet a tanúsítványt a **feltöltése egy**.

    d. Kattintson a **Mentés** gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-launchdarkly-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-launchdarkly-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/active-directory-saas-launchdarkly-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-launchdarkly-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="create-a-launchdarkly-test-user"></a>LaunchDarkly tesztfelhasználó létrehozása

Ez a szakasz célja LaunchDarkly Britta Simon nevű felhasználót létrehozni. LaunchDarkly támogatja just-in-time kiosztást, amely alapértelmezés szerint van engedélyezve. Nincs ebben a szakaszban az Ön művelet elem. Új felhasználó jön létre az LaunchDarkly elérésére, ha még nem létezik tett kísérlet során.
>[!Note]
>Ha manuálisan hozzon létre egy felhasználó van szüksége, forduljon a [LaunchDarkly ügyfél-támogatási csoport](mailto:support@launchdarkly.com).

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés LaunchDarkly Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése LaunchDarkly, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **LaunchDarkly**.

    ![Az alkalmazások listáját a LaunchDarkly hivatkozás](./media/active-directory-saas-launchdarkly-tutorial/tutorial_launchdarkly_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen LaunchDarkly csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az LaunchDarkly alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-launchdarkly-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-launchdarkly-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-launchdarkly-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-launchdarkly-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-launchdarkly-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-launchdarkly-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-launchdarkly-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-launchdarkly-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-launchdarkly-tutorial/tutorial_general_203.png


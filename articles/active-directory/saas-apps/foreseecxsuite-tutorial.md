---
title: 'Oktatóanyag: Azure Active Directory-integráció várható CX Suite |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a várható CX Suite között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 5f4b7830-6186-4d17-b77b-504d4192bfde
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2018
ms.author: jeedes
ms.openlocfilehash: e27bc853db6236165d77f2fb3418bab1170400b2
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36227703"
---
# <a name="tutorial-azure-active-directory-integration-with-foresee-cx-suite"></a>Oktatóanyag: Azure Active Directory-integráció várható CX csomaggal

Ebben az oktatóanyagban elsajátíthatja várható CX Suite integrálása az Azure Active Directory (Azure AD).

Várható CX Suite integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Szabályozhatja, hogy CX Suite hozzáféréssel rendelkező Azure AD-ben.
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni aláírt a CX Suite várható (egyszeri bejelentkezés).
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása várható CX csomaggal, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- A várható CX Suite egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben.
Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Várható CX csomag hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-foresee-cx-suite-from-the-gallery"></a>Várható CX csomag hozzáadása a gyűjteményből
Az Azure AD integrálása a várható CX Suite konfigurálásához kell hozzáadnia CX Suite várható a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből várható CX Suite hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **várható CX Suite**, jelölje be **várható CX Suite** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában CX Suite várható](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD az egyszeri bejelentkezés várható CX Suite "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, a partner felhasználó CX Suite várható Újdonságok egy felhasználó számára az Azure ad-ben. Ez azt jelenti egy Azure AD-felhasználó és a kapcsolódó felhasználó a várható CX Suite közötti kapcsolat kapcsolatot kell létrehozni.

Az Azure AD az egyszeri bejelentkezés várható CX Suite tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Várható CX Suite tesztfelhasználó létrehozása](#create-a-foresee-cx-suite-test-user)**  - kell rendelkeznie a megfelelője a Britta Simon várható CX csomagban található, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az várható CX Suite alkalmazásban.

**Konfigurálja az Azure AD az egyszeri bejelentkezés várható CX csomaggal, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **várható CX Suite** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés párbeszédpanel](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_samlbase.png)

3. Az a **várható CX Suite tartomány és az URL-címek** szakaszban, ha **szolgáltató metaadatfájl**, hajtsa végre a következő lépéseket:

    ![Várható CX Suite tartomány és az URL-címek egyetlen bejelentkezés információk](./media/foreseecxsuite-tutorial/upload.png)

    egy. Kattintson a **feltöltés metaadatfájl**.

    ![Várható CX Suite tartomány és az URL-címek egyetlen bejelentkezés információk](./media/foreseecxsuite-tutorial/tutorial_foreseen_uploadconfig.png)

    b. Kattintson a **mappa embléma** válassza ki a metaadat-fájlt, és kattintson a **feltöltése**.

    c. Feltöltés sikeres befejezése után **szolgáltató metaadatfájl** a **azonosító** üres érték get automatikus **várható CX Suite tartomány és az URL-címek** szakasz Szövegmező alább látható módon:

    ![Várható CX Suite tartomány és az URL-címek egyetlen bejelentkezés információk](./media/foreseecxsuite-tutorial/urlupload.png)

4. Ha nem rendelkezik **szolgáltató metaadatfájl**, hajtsa végre a következő lépéseket:

    ![Várható CX Suite tartomány és az URL-címek egyetlen bejelentkezés információk](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_url.png)

    egy. Az a **bejelentkezési URL-cím** szövegmező, írja be az URL-cím: `https://cxsuite.foresee.com/`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: `https://www.okta.com/saml2/service-provider/<UniqueID>`

    > [!NOTE]
    > Az azonosító értéke nincs valós. Frissítse ezt az értéket a tényleges azonosítója. Ügyfél [várható CX Suite ügyfél-támogatási csoport](mailto:support@foresee.com) lekérni ezt az értéket.

5. Az a **SAML-aláíró tanúsítványa** területen kattintson **metaadatainak XML-kódja** és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_certificate.png)

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/foreseecxsuite-tutorial/tutorial_general_400.png)

7. Egyszeri bejelentkezés konfigurálása **várható CX Suite** oldalon kell küldeniük a letöltött **metaadatainak XML-kódja** való [várható CX Suite támogatási csoport](mailto:support@foresee.com). Akkor állítsa be ezt a beállítást, hogy a SAML SSO kapcsolat mindkét oldalán megfelelően beállítva.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/foreseecxsuite-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/foreseecxsuite-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/foreseecxsuite-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/foreseecxsuite-tutorial/create_aaduser_04.png)

    egy. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **létrehozása**.

### <a name="create-a-foresee-cx-suite-test-user"></a>Várható CX Suite tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon várható CX Suite nevű felhasználót hoz létre. Együttműködve [várható CX Suite támogatási csoport](mailto:support@foresee.com) a felhasználókat vagy a tartománnyal, amely ahhoz szükséges, hogy a várható CX Suite platform szerepel az engedélyezési listán kell hozzáadni. Ha a tartomány a csapata által hozzáadott, felhasználók fogja beolvasni automatikusan hozzáférést kapnak a várható CX Suite platform. Felhasználók kell létrehoznia és aktiválni az egyszeri bejelentkezés használata előtt.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés várható CX Suite Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200]

**Britta Simon hozzárendelése CX Suite várható, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201]

2. Az alkalmazások listában válassza ki a **várható CX Suite**.

    ![Az alkalmazások listáját a várható CX Suite hivatkozás](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_app.png)

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.

### <a name="test-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

A hozzáférési panelen várható CX Suite csempére kattintva, meg kell beolvasni automatikusan bejelentkezett a várható CX Suite alkalmazásba.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/foreseecxsuite-tutorial/tutorial_general_01.png
[2]: ./media/foreseecxsuite-tutorial/tutorial_general_02.png
[3]: ./media/foreseecxsuite-tutorial/tutorial_general_03.png
[4]: ./media/foreseecxsuite-tutorial/tutorial_general_04.png

[100]: ./media/foreseecxsuite-tutorial/tutorial_general_100.png

[200]: ./media/foreseecxsuite-tutorial/tutorial_general_200.png
[201]: ./media/foreseecxsuite-tutorial/tutorial_general_201.png
[202]: ./media/foreseecxsuite-tutorial/tutorial_general_202.png
[203]: ./media/foreseecxsuite-tutorial/tutorial_general_203.png


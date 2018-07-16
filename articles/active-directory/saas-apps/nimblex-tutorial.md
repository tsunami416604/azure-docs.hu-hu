---
title: 'Oktatóanyag: Azure Active Directory-integráció az Nimblex |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Nimblex között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d3e165a5-f062-4b50-ac0b-b400838e99cd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2018
ms.author: jeedes
ms.openlocfilehash: 7b5dc6d892741f63596589a48ad5d45891b14c21
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39040405"
---
# <a name="tutorial-azure-active-directory-integration-with-nimblex"></a>Oktatóanyag: Azure Active Directory-integráció az Nimblex

Ebben az oktatóanyagban elsajátíthatja, hogyan Nimblex integrálása az Azure Active Directory (Azure AD).

Nimblex integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá Nimblex Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Nimblex (egyszeri bejelentkezés), az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Nimblex az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy Nimblex egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Nimblex hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-nimblex-from-the-gallery"></a>Nimblex hozzáadása a katalógusból
Az Azure AD integrálása a Nimblex konfigurálásához hozzá kell Nimblex a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Nimblex hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]

1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **Nimblex**válassza **Nimblex** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában Nimblex](./media/nimblex-tutorial/tutorial_nimblex_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Nimblex a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Nimblex mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Nimblex hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az Nimblex tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Hozzon létre egy Nimblex tesztfelhasználót](#create-a-nimblex-test-user)**  – egy megfelelője a Britta Simon Nimblex, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Nimblex alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Nimblex, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Nimblex** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési párbeszédpanel](./media/nimblex-tutorial/tutorial_nimblex_samlbase.png)

3. Az a **Nimblex tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Nimblex tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/nimblex-tutorial/tutorial_nimblex_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<YOUR APPLICATION PATH>/Login.aspx`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<YOUR APPLICATION PATH>/`

    c. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<path-to-application>/SamlReply.aspx`

    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL-, azonosítóját és válasz URL-cím. Kapcsolattartó [Nimblex ügyfél-támogatási csapatának](mailto:support@ebms.com.au) beolvasni ezeket az értékeket.

4. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/nimblex-tutorial/tutorial_nimblex_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/nimblex-tutorial/tutorial_general_400.png)

6. Az a **Nimblex konfigurációs** területén kattintson **konfigurálása Nimblex** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Nimblex konfiguráció](./media/nimblex-tutorial/tutorial_nimblex_configure.png) 

7. Egy másik böngészőablakban jelentkezzen be a Nimblex egy biztonsági-rendszergazdaként.

9. Kattintson a lap felső jobb oldalán, **beállítások** embléma.

    ![Nimblex beállításai](./media/nimblex-tutorial/tutorial_nimblex_settings.png)

10. Az a **Vezérlőpult** lap **biztonsági** szakaszban kattintson **egyszeri bejelentkezés**.

    ![Nimblex beállításai](./media/nimblex-tutorial/tutorial_nimblex_single.png)

11. Az a **felügyelete egyszeri bejelentkezéssel** lapon válassza ki a példány nevét, és kattintson **szerkesztése**.

    ![Nimblex saml](./media/nimblex-tutorial/tutorial_nimblex_saml.png)

12. Az a **SSO-szolgáltató szerkesztése** lapon, a következő lépésekkel:

    ![Nimblex saml](./media/nimblex-tutorial/tutorial_nimblex_sso.png)

    a. Az a **leírás** szövegmezőbe írja be a példány nevét.

    b. A Jegyzettömbben, nyissa meg az Azure Portalról letöltött base-64 kódolású tanúsítványt, másolja a tartalmat, és illessze be azt a **tanúsítvány** mezőbe.

    c. A a **Identity Provider Sso cél URL-cím** szövegmezőjébe illessze be az értéket, **SAML egyszeri bejelentkezési szolgáltatás URL-cím**, az Azure Portalról másolt.

    d. Kattintson a **Save** (Mentés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/nimblex-tutorial/create_aaduser_01.png)

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/nimblex-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/nimblex-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/nimblex-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-nimblex-test-user"></a>Nimblex tesztfelhasználó létrehozása

Ez a szakasz célja Nimblex Britta Simon nevű felhasználó létrehozásához. Nimblex támogatja a just-in-time-kiépítés, amely alapértelmezésben engedélyezve van. Nincs meg ebben a szakaszban a művelet elem. Új felhasználó jön létre az Nimblex elérésére, ha még nem létezik tett kísérlet során.

>[!Note]
>Ha manuálisan hozzon létre egy felhasználót van szüksége, forduljon a [Nimblex ügyfél-támogatási csapatának](mailto:support@ebms.com.au).

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Nimblex Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel Nimblex, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **Nimblex**.

    ![Az alkalmazások listáját a Nimblex hivatkozásra](./media/nimblex-tutorial/tutorial_nimblex_app.png)  

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Nimblex csempére kattint, meg kell lekérése automatikusan bejelentkezett az Nimblex alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/nimblex-tutorial/tutorial_general_01.png
[2]: ./media/nimblex-tutorial/tutorial_general_02.png
[3]: ./media/nimblex-tutorial/tutorial_general_03.png
[4]: ./media/nimblex-tutorial/tutorial_general_04.png

[100]: ./media/nimblex-tutorial/tutorial_general_100.png

[200]: ./media/nimblex-tutorial/tutorial_general_200.png
[201]: ./media/nimblex-tutorial/tutorial_general_201.png
[202]: ./media/nimblex-tutorial/tutorial_general_202.png
[203]: ./media/nimblex-tutorial/tutorial_general_203.png


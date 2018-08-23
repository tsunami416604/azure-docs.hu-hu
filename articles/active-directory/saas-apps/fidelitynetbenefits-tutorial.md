---
title: 'Oktatóanyag: Azure Active Directory-integráció az hűség NetBenefits |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés hűség NetBenefits és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 77dc8a98-c0e7-4129-ab88-28e7643e432a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2018
ms.author: jeedes
ms.openlocfilehash: a1308035a8b758a9e2f824de3a78c03103c19931
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2018
ms.locfileid: "42054679"
---
# <a name="tutorial-azure-active-directory-integration-with-fidelity-netbenefits"></a>Oktatóanyag: Azure Active Directory-integráció az hűség NetBenefits

Ebben az oktatóanyagban elsajátíthatja, hogyan hűség NetBenefits integrálása az Azure Active Directory (Azure AD).

Pontosságú NetBenefits integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá hűség NetBenefits Azure AD-ben.
- A felhasználók automatikusan első bejelentkezett hűség NetBenefits (egyszeri bejelentkezés), engedélyezheti az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Pontosságú NetBenefits konfigurálni az Azure AD-integráció, a következőkre van szükség:

- Azure AD-előfizetés
- Egy hűség NetBenefits egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.
Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Pontosságú NetBenefits hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-fidelity-netbenefits-from-the-gallery"></a>Pontosságú NetBenefits hozzáadása a katalógusból

Konfigurálása az Azure AD integrálása a pontosságú NetBenefits, hozzá kell hűség NetBenefits a galériából a felügyelt SaaS-alkalmazások listájára.

**Pontosságú NetBenefits hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **hűség NetBenefits**, jelölje be **hűség NetBenefits** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában hűség NetBenefits](./media/fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés hűség NetBenefits a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználói hűség NetBenefits a mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználói hűség NetBenefits a hivatkozás kapcsolatát kell létrehozni.

A pontosságú NetBenefits **felhasználói** leképezést kell elvégezni a **az Azure AD-felhasználó** hivatkozás viszony.

Az Azure AD egyszeri bejelentkezés az hűség NetBenefits tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Hozzon létre egy hűség NetBenefits tesztfelhasználót](#create-a-fidelity-netbenefits-test-user)**  – egy megfelelője a Britta Simon hűség NetBenefits, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és hűség NetBenefits alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés hűség NetBenefits, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **hűség NetBenefits** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési párbeszédpanel](./media/fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_samlbase.png)

3. Az a **hűség NetBenefits tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Pontosságú NetBenefits tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_url.png)

    a. Az a **azonosító** szövegmezőbe írja be egy URL-címe:

    Tesztelési környezetben:  `urn:sp:fidelity:geninbndnbparts20:uat:xq1`

    Éles környezetben:  `urn:sp:fidelity:geninbndnbparts20`

    b. Az a **válasz URL-cím** szövegmezőben adjon meg egy URL-címet, adja meg pontosságú végrehajtási idő, vagy forduljon a hozzárendelt pontosságú ügyfél Service Manager.

4. Pontosságú NetBenefits alkalmazás a SAML helyességi feltételek vár egy megadott formátumban. Mi van leképezve a **felhasználói azonosító** együtt a **user.userprincipalname**. Leképezheti a **employeeid** vagy alkalmazható a szervezet számára, mint minden más követelés **felhasználóazonosító**. A következő képernyőképen a csak egy példa látható.

    ![Pontosságú NetBenefits attribútum](./media/fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_attribute.png)

    >[!Note]
    >Pontosságú NetBenefits statikus és dinamikus összevonás támogatja. A statikus azt jelenti, hogy nem fogja használni a SAML-alapú csak az idő felhasználó kiépítési és dinamikus azt jelenti, hogy csak az idő felhasználókiépítés támogatja. A igény szerinti alapján kiépítési ügyfelek kell néhány további jogcímek hozzáadása az Azure ad-ben például a felhasználó születési dátum, stb. Ezek az adatok által biztosított a a hozzárendelt **pontosságú ügyfél Service Manager** kell a példány a dinamikus összevonás engedélyezése.

5. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_certificate.png)

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/fidelitynetbenefits-tutorial/tutorial_general_400.png)

7. Az a **hűség NetBenefits konfigurációs** területén kattintson **hűség NetBenefits konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Pontosságú NetBenefits konfiguráció](./media/fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_configure.png)

8. Az egyszeri bejelentkezés konfigurálása **hűség NetBenefits** oldalon kell küldenie a letöltött **metaadatainak XML**, **SAML egyszeri bejelentkezési szolgáltatás URL-cím** és  **SAML-Entitásazonosító** való **pontosságú ügyfél a hozzárendelt Service Manager**. Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/fidelitynetbenefits-tutorial/create_aaduser_01.png)

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/fidelitynetbenefits-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/fidelitynetbenefits-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/fidelitynetbenefits-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
  
### <a name="create-a-fidelity-netbenefits-test-user"></a>Pontosságú NetBenefits tesztfelhasználó létrehozása

Ebben a szakaszban egy felhasználói hűség NetBenefits Britta Simon nevű hoz létre. Statikus összevonás létrehozásakor együttműködve a hozzárendelt **pontosságú ügyfél Service Manager** hűség NetBenefits platform a felhasználók létrehozásához. Ezeket a felhasználókat kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva.

A dinamikus az összevonáshoz felhasználók használatával jön létre igény szerinti felhasználók átadása. A igény szerinti alapján kiépítési ügyfelek kell néhány további jogcímek hozzáadása az Azure ad-ben például a felhasználó születési dátum, stb. Ezek az adatok által biztosított a a hozzárendelt **pontosságú ügyfél Service Manager** kell a példány a dinamikus összevonás engedélyezése.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés hűség NetBenefits Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200]

**Britta Simon rendel hűség NetBenefits, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201]

2. Az alkalmazások listájában jelölje ki a **hűség NetBenefits**.

    ![Az alkalmazások listáját a pontosságú NetBenefits hivatkozás](./media/fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_app.png)  

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a pontosságú NetBenefits csempére kattint, kell lekérése automatikusan bejelentkezett a pontosságú NetBenefits alkalmazásba.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/fidelitynetbenefits-tutorial/tutorial_general_01.png
[2]: ./media/fidelitynetbenefits-tutorial/tutorial_general_02.png
[3]: ./media/fidelitynetbenefits-tutorial/tutorial_general_03.png
[4]: ./media/fidelitynetbenefits-tutorial/tutorial_general_04.png

[100]: ./media/fidelitynetbenefits-tutorial/tutorial_general_100.png

[200]: ./media/fidelitynetbenefits-tutorial/tutorial_general_200.png
[201]: ./media/fidelitynetbenefits-tutorial/tutorial_general_201.png
[202]: ./media/fidelitynetbenefits-tutorial/tutorial_general_202.png
[203]: ./media/fidelitynetbenefits-tutorial/tutorial_general_203.png
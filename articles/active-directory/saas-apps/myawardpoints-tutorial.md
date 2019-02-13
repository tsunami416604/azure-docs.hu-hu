---
title: 'Oktatóanyag: A díjjal járó pontok felső Sub/felső csapatával az Azure Active Directory-integráció |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a díjjal járó pontokat felső Sub/felső csoport között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a7a08eed-7a6b-4a83-8f8e-0add6d2fb8cf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 19ed1c12768eed0265f65388984a5859b8f0630b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56210730"
---
# <a name="tutorial-azure-active-directory-integration-with-my-award-points-top-subtop-team"></a>Oktatóanyag: Az Azure Active Directory-integráció, a díjjal járó pontok felső Sub/felső csapatával

Ebben az oktatóanyagban megismerheti, hogyan integrálható a saját díj pontok felső Sub/felső csapata az Azure Active Directory (Azure AD).

Saját díj pontok felső Sub/felső csapata integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá a saját díj pontok felső Sub/felső csapata az Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a díjjal járó pontok felső Sub/felső csapatának (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a díjjal járó pontok felső Sub/felső csapatával, a következőkre van szükség:

- Azure AD-előfizetés
- Egy saját díj pontok felső Sub/felső csapata egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.
Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Saját díj pontok felső Sub/felső csapata hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-my-award-points-top-subtop-team-from-the-gallery"></a>Saját díj pontok felső Sub/felső csapata hozzáadása a katalógusból

Az Azure AD integrálása a díjjal járó pontokat felső Sub/felső csoport konfigurálásához, hozzá kell saját díj pontok felső Sub/felső csapata a galériából a felügyelt SaaS-alkalmazások listájára.

**Saját díj pontok felső Sub/felső csapata hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **saját díj pontok felső Sub/felső csapata**, jelölje be **saját díj pontok felső Sub/felső csapata** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![A díj pontok felső Sub/felső csapata a találatok listájában](./media/myawardpoints-tutorial/tutorial_myawardpoints_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés saját díj pontok felső Sub/felső csapata egy "Britta Simon" nevű tesztelési felhasználó alapján.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó a saját díj pontok felső Sub/felső csapata mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó a saját díj pontok felső Sub/felső csapata hivatkozás kapcsolatát kell létrehozni.

Az Azure AD egyszeri bejelentkezés saját díjjal járó pontokat felső Sub/felső csapatával tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Hozzon létre egy saját díj pontok felső Sub/felső csapata tesztfelhasználót](#create-a-my-award-points-top-subtop-team-test-user)**  - a-megfelelője a Britta Simon van a díjjal járó pontokat felső Sub/felső csoport, amely kapcsolódik az Azure AD felhasználói ábrázolása.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és saját díj pontok felső Sub/felső csapata alkalmazását az egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálása saját díjjal járó pontokat felső Sub/felső csapatával, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **saját díj pontok felső Sub/felső csapata** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési párbeszédpanel](./media/myawardpoints-tutorial/tutorial_myawardpoints_samlbase.png)

3. Az a **saját díjjal járó pontokat felső Sub/felső csapat tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![A díjjal járó pontokat felső Sub/felső csapat tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/myawardpoints-tutorial/tutorial_myawardpoints_url.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://microsoftrr.performnet.com/biwv1auth/Shibboleth.sso/Login?providerId=<SAMLENTITYID>`

    > [!NOTE]
    > Megjelenik a `<SAMLENTITYID>` érték ebben az oktatóanyagban a későbbi lépésekben.

4. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/myawardpoints-tutorial/tutorial_myawardpoints_certificate.png)

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/myawardpoints-tutorial/tutorial_general_400.png)

6. Az a **saját díjjal járó pontokat felső Sub/felső Team konfigurációs** szakaszban jelölje be **konfigurálása saját díj pontok felső Sub/felső csapata** a konfigurálás bejelentkezési ablak megnyitásához. Az SAML-entitás azonosítója a másolja a **rövid összefoglaló** szakaszt, és fűzze hozzá a SAML Entitásazonosító értékét a bejelentkezési URL-cím helyett, `<SAMLENTITYID>` a a **saját díjjal járó pontokat felső Sub/felső csapat tartomány és URL-címek** a szakasz az Azure Portalon.

7. Az egyszeri bejelentkezés konfigurálása **saját díj pontok felső Sub/felső csapata** oldalon kell küldenie a letöltött **metaadatainak XML** való [saját díj pontok felső Sub/felső csapata támogatási csapatának](mailto:myawardpoints@biworldwide.com). Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/myawardpoints-tutorial/create_aaduser_01.png)

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/myawardpoints-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/myawardpoints-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/myawardpoints-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="create-a-my-award-points-top-subtop-team-test-user"></a>Saját díj pontok felső Sub/felső csapata tesztfelhasználó létrehozása

Ebben a szakaszban egy saját díjjal járó pontokat felső Sub/felső csapat Britta Simon nevű felhasználó hoz létre. Együttműködve [saját díj pontok felső Sub/felső csapata támogatási csapatának](mailto:myawardpoints@biworldwide.com) a felhasználók hozzáadása a saját díj pontok felső Sub/felső csapata platformon. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon hozzáférést biztosít a saját díj pontok felső Sub/felső csapata által használandó Azure egyszeri bejelentkezést.

![A felhasználói szerepkör hozzárendelése][200]

**Britta Simon hozzárendelése saját díj pontok felső Sub/felső csapata, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201]

2. Az alkalmazások listájában jelölje ki a **saját díj pontok felső Sub/felső csapata**.

    ![Az alkalmazások listáját a saját díj pontok felső Sub/felső csapata hivatkozás](./media/myawardpoints-tutorial/tutorial_myawardpoints_app.png)  

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a saját díj pontok felső Sub/felső csapata csempére kattint, akkor kell lekérése automatikusan bejelentkezett a saját díj pontok felső Sub/felső csapata alkalmazásba.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/myawardpoints-tutorial/tutorial_general_01.png
[2]: ./media/myawardpoints-tutorial/tutorial_general_02.png
[3]: ./media/myawardpoints-tutorial/tutorial_general_03.png
[4]: ./media/myawardpoints-tutorial/tutorial_general_04.png

[100]: ./media/myawardpoints-tutorial/tutorial_general_100.png

[200]: ./media/myawardpoints-tutorial/tutorial_general_200.png
[201]: ./media/myawardpoints-tutorial/tutorial_general_201.png
[202]: ./media/myawardpoints-tutorial/tutorial_general_202.png
[203]: ./media/myawardpoints-tutorial/tutorial_general_203.png

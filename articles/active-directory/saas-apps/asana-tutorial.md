---
title: 'Oktatóanyag: Azure Active Directory-integráció az Asanában |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és az Asanában között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 837e38fe-8f55-475c-87f4-6394dc1fee2b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: jeedes
ms.openlocfilehash: f5ea7a330891d4befeb6388bbe7f37b2a4aa848f
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39438208"
---
# <a name="tutorial-azure-active-directory-integration-with-asana"></a>Oktatóanyag: Azure Active Directory-integráció az Asanában

Ebben az oktatóanyagban elsajátíthatja, hogyan Asana integrálása az Azure Active Directory (Azure AD).

Asana integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá az Asanában Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett az Asanában (egyszeri bejelentkezés) az Azure AD-fiókjukat
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása az Asanában, a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy Asana egyszeri bejelentkezéses engedélyezett előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.
Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Asana hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-asana-from-the-gallery"></a>Asana hozzáadása a katalógusból
Az Azure AD-be Asana-integráció konfigurálásához, hozzá kell Asana a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Asana hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]

1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **Asana**válassza **Asana** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/asana-tutorial/tutorial_asana_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az Asanában egy "Britta Simon." nevű tesztelési felhasználó alapján

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó az Asanában mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó az Asanában hivatkozás kapcsolata kell létrehozni.

Az Asanában, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az Asanában tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy Asana tesztfelhasználót](#create-an-asana-test-user)**  - a-megfelelője a Britta Simon rendelkezik az Asanában, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és az Asanában alkalmazás egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálása az Asanában, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Asana** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési párbeszédpanel](./media/asana-tutorial/tutorial_asana_samlbase.png)

1. Az a **Asana-tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Asana-tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/asana-tutorial/tutorial_asana_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe típus URL-címe: `https://app.asana.com/`

    b. Az a **azonosító** szövegmező, érték: `https://app.asana.com/`

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/asana-tutorial/tutorial_asana_certificate.png)

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/asana-tutorial/tutorial_general_400.png)

1. Az a **Asana-konfiguráció** területén kattintson **konfigurálása Asana** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Asana-konfiguráció](./media/asana-tutorial/tutorial_asana_configure.png)

1. Egy másik böngészőablakban bejelentkezés Asana-alkalmazását. Egyszeri bejelentkezés konfigurálása az Asanában, a munkaterület nevére a képernyő jobb felső sarkában lévő kattintva férhet hozzá a munkaterület beállításait. Ezután kattintson a  **\<a munkaterület neve\> beállítások**.

    ![Asana egyszeri bejelentkezési beállításainak](./media/asana-tutorial/tutorial_asana_09.png)

1. Az a **szervezeti beállítások** ablakban kattintson a **felügyeleti**. Kattintson a **SAML használatával be kell jelentkeznie a tagok** engedélyezése az egyszeri bejelentkezés konfigurálása. Hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés szervezeti beállítások konfigurálása](./media/asana-tutorial/tutorial_asana_10.png)  

     a. Az a **lap URL-címe be** szövegmezőjébe illessze be a **SAML egyszeri bejelentkezési szolgáltatás URL-cím**.

     b. Az Azure-portálról letöltött tanúsítvány kattintson a jobb gombbal, majd nyissa meg a tanúsítvány-fájlt a Jegyzettömb vagy az előnyben részesített szövegszerkesztőben. Másolja az a kezdő és záró tanúsítvány címe között, és illessze be a **X.509-tanúsítvány** szövegmezőbe.

1. Kattintson a **Save** (Mentés) gombra. Lépjen a [Asana-útmutató az egyszeri bejelentkezés beállítása](https://asana.com/guide/help/premium/authentication#gl-saml) Ha segítségre van szüksége további.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](./media/asana-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/asana-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/asana-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:

    ![A Hozzáadás gombra.](./media/asana-tutorial/create_aaduser_04.png)

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="create-an-asana-test-user"></a>Hozzon létre egy Asana tesztfelhasználó számára

Ez a szakasz célja az Asanában Britta Simon nevű felhasználó létrehozásához. Asana támogatja a felhasználók automatikus átadása, amely alapértelmezés szerint van engedélyezve. További részleteket talál [Itt](asana-provisioning-tutorial.md) konfigurálásának a felhasználók automatikus átadása.

**Ha szeretné manuálisan létrehozni a felhasználói, hajtson végre a következő lépéseket:**

Ebben a szakaszban egy asana Britta Simon nevű felhasználó létrehozásához.

1. A **Asana**, nyissa meg a **csapatok** szakaszban a bal oldali panelen. Kattintson a pluszjel gombra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/asana-tutorial/tutorial_asana_12.png)

1. Írja be az e-mailt britta.simon@contoso.com a szövegmezőbe, és válassza ki a **meghívása**.

1. Kattintson a **meghívó küldése**. Az új felhasználó kap egy e-mailt saját e-mail-fiókba. Marcela kell létrehozni és megerősíteni a fiókot.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Asana Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200]

**Britta Simon rendel az Asanában, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201]

1. Az alkalmazások listájában jelölje ki a **Asana**.

    ![Az alkalmazások listáját az Asanában hivatkozásra](./media/asana-tutorial/tutorial_asana_app.png)

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ez a szakasz célja az Azure AD egyszeri bejelentkezés teszteléséhez.

Nyissa meg az Asanában bejelentkezési oldalára. Szúrja be az E-mail cím szövegmezőbe az e-mail-cím britta.simon@contoso.com. A jelszó szövegmező hagyja üresen, és kattintson a **bejelentkezés**. Az Azure AD bejelentkezési oldalára irányítja. Végezze el az Azure AD hitelesítő adatait. Most már van bejelentkezve Asanában.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Felhasználók átadásának konfigurálása](asana-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/asana-tutorial/tutorial_general_01.png
[2]: ./media/asana-tutorial/tutorial_general_02.png
[3]: ./media/asana-tutorial/tutorial_general_03.png
[4]: ./media/asana-tutorial/tutorial_general_04.png

[100]: ./media/asana-tutorial/tutorial_general_100.png

[200]: ./media/asana-tutorial/tutorial_general_200.png
[201]: ./media/asana-tutorial/tutorial_general_201.png
[202]: ./media/asana-tutorial/tutorial_general_202.png
[203]: ./media/asana-tutorial/tutorial_general_203.png
[10]: ./media/asana-tutorial/tutorial_general_060.png
[11]: ./media/asana-tutorial/tutorial_general_070.png
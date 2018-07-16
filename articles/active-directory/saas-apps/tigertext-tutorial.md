---
title: 'Oktatóanyag: Azure Active Directory-integráció az TigerText biztonságos Messenger |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés TigerText biztonságos Messenger és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 03f1e128-5bcb-4e49-b6a3-fe22eedc6d5e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: jeedes
ms.openlocfilehash: 09f04ab4d289eb7fb6664d192404f719f8604e1e
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39041347"
---
# <a name="tutorial-azure-active-directory-integration-with-tigertext-secure-messenger"></a>Oktatóanyag: Azure Active Directory-integráció az TigerText biztonságos Messenger

Ebben az oktatóanyagban elsajátíthatja, hogyan TigerText biztonságos Messenger integrálása az Azure Active Directory (Azure AD).

TigerText biztonságos Messenger integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá TigerText biztonságos Messenger Azure AD-ben
- Az Azure AD-fiókjukat engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett TigerText biztonságos Messenger (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása TigerText biztonságos Messenger, a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy biztonságos Messenger TigerText egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. TigerText biztonságos Messenger hozzáadása a katalógusból
2. Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

## <a name="add-tigertext-secure-messenger-from-the-gallery"></a>TigerText biztonságos Messenger hozzáadása a katalógusból
Az Azure AD-be TigerText biztonságos Messenger-integráció konfigurálásához, hozzá kell TigerText biztonságos Messenger a galériából a felügyelt SaaS-alkalmazások listájára.

**TigerText biztonságos Messenger hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

4. A Keresés mezőbe írja be a **TigerText biztonságos Messenger**, jelölje be **TigerText biztonságos Messenger** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Katalógus hozzáadása](./media/tigertext-tutorial/tutorial_tigertext_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés TigerText biztonságos Messenger a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó TigerText biztonságos Messenger mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó TigerText biztonságos Messenger hivatkozás kapcsolata kell létrehozni.

TigerText biztonságos Messenger, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés TigerText biztonságos Messenger tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Hozzon létre egy biztonságos Messenger TigerText tesztfelhasználót](#create-a-tigertext-secure-messenger-test-user)**  – egy megfelelője a Britta Simon TigerText biztonságos Messenger, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és az TigerText biztonságos Messenger alkalmazás egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés TigerText biztonságos Messenger, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **TigerText biztonságos Messenger** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![SAML-alapú bejelentkezés](./media/tigertext-tutorial/tutorial_tigertext_samlbase.png)

3. Az a **TigerText biztonságos Messenger tartománya és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![A szakasz TigerText biztonságos Messenger tartománya és URL-címek](./media/tigertext-tutorial/tutorial_tigertext_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be URL-cím: `https://home.tigertext.com`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://saml-lb.tigertext.me/v1/organization/<instance Id>`

    > [!NOTE] 
    > Az érték nem valódi. Ez az érték frissítse a tényleges azonosítója. Kapcsolattartó [TigerText biztonságos Messenger ügyfél-támogatási csapatának](mailTo:prosupport@tigertext.com) lekérni ezt az értéket. 
 
4. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![SAML-aláíró tanúsítvány szakaszban](./media/tigertext-tutorial/tutorial_tigertext_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Mentés gomb](./media/tigertext-tutorial/tutorial_general_400.png)

6. Egyszeri bejelentkezés az alkalmazáshoz konfigurált beszerzéséhez forduljon [TigerText biztonságos Messenger támogatási csapatának](mailTo:prosupport@tigertext.com) , és adja meg azokat a **letöltött metaadatok**.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/tigertext-tutorial/create_aaduser_01.png) 

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Felhasználók és csoportok -> minden felhasználó](./media/tigertext-tutorial/create_aaduser_02.png) 

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Hozzáadás gomb](./media/tigertext-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Felhasználói párbeszédpanel](./media/tigertext-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-tigertext-secure-messenger-test-user"></a>Biztonságos Messenger TigerText tesztfelhasználó létrehozása

Ebben a szakaszban egy felhasználói Britta Simon nevű TigerText hoz létre. Vegye fel a kapcsolatot [TigerText biztonságos Messenger ügyfél-támogatási csapatának](mailTo:prosupport@tigertext.com) a felhasználók hozzáadása az TigerText platformon.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon a hozzáférés biztosításával TigerText biztonságos Messenger Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel TigerText biztonságos Messenger, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **TigerText biztonságos Messenger**.

    ![Biztonságos Messenger TigerText Alkalmazáslista](./media/tigertext-tutorial/tutorial_tigertext_app.png) 

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a TigerText csempére kattint, meg kell lekérése automatikusan bejelentkezett az TigerText alkalmazáshoz. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/tigertext-tutorial/tutorial_general_01.png
[2]: ./media/tigertext-tutorial/tutorial_general_02.png
[3]: ./media/tigertext-tutorial/tutorial_general_03.png
[4]: ./media/tigertext-tutorial/tutorial_general_04.png

[100]: ./media/tigertext-tutorial/tutorial_general_100.png

[200]: ./media/tigertext-tutorial/tutorial_general_200.png
[201]: ./media/tigertext-tutorial/tutorial_general_201.png
[202]: ./media/tigertext-tutorial/tutorial_general_202.png
[203]: ./media/tigertext-tutorial/tutorial_general_203.png


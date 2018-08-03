---
title: 'Oktatóanyag: Azure Active Directory-integráció a üzemeltetőjének jelszókezelő & digitális Vault |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés üzemeltetőjének jelszókezelő & digitális Vault és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: e1a98f6a-2dae-4734-bdbf-4fba742a61d2
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.openlocfilehash: dee9b81b6830244dec6860da0618d20c7f062ac2
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39430355"
---
# <a name="tutorial-azure-active-directory-integration-with-keeper-password-manager--digital-vault"></a>Oktatóanyag: Azure Active Directory-integráció a üzemeltetőjének jelszókezelő & digitális tároló

Ebben az oktatóanyagban elsajátíthatja, hogyan üzemeltetőjének jelszókezelő & digitális Vault integrálható az Azure Active Directory (Azure AD).

Jelszókezelő üzemeltetőjének & digitális tároló integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá üzemeltetőjének jelszókezelő & digitális Vault az Azure AD-ben
- Az Azure AD-fiókjukat engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett üzemeltetőjének jelszókezelő & digitális tároló (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a üzemeltetőjének jelszókezelő & digitális tároló, a következőkre van szükség:

- Az Azure AD-előfizetéshez
- A jelszókezelő üzemeltetőjének & digitális Vault egyszeri bejelentkezéses engedélyezett előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Jelszókezelő üzemeltetőjének & digitális tároló hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-keeper-password-manager--digital-vault-from-the-gallery"></a>Jelszókezelő üzemeltetőjének & digitális tároló hozzáadása a katalógusból
Az Azure AD integrálása a üzemeltetőjének jelszókezelő & digitális Vault konfigurálásához kell üzemeltetőjének jelszókezelő és digitális tároló hozzáadása a katalógusból a felügyelt SaaS-alkalmazások listájában.

**Jelszókezelő üzemeltetőjének & digitális tároló hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **üzemeltetőjének jelszókezelő & digitális tároló**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/keeperpasswordmanager-tutorial/tutorial_keeper_search.png)

1. Az eredmények panelen válassza ki a **üzemeltetőjének jelszókezelő & digitális tároló**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/keeperpasswordmanager-tutorial/tutorial_keeper_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a üzemeltetőjének jelszókezelő & digitális tároló egy "Britta Simon." nevű tesztelési felhasználó alapján

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó üzemeltetőjének jelszókezelő & digitális tároló mi egy felhasználó számára az Azure ad-ben. Más szóval Azure AD-felhasználót és a kapcsolódó felhasználó üzemeltetőjének jelszókezelő & digitális tároló közötti kapcsolat kapcsolatot kell létrehozni.

& Jelszókezelő üzemeltetőjének digitális tároló, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** hivatkozás viszony.

Az Azure AD egyszeri bejelentkezés a üzemeltetőjének jelszókezelő & digitális tároló tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Jelszókezelő üzemeltetőjének & digitális Vault tesztfelhasználó létrehozása](#creating-a-keeperpasswordmanager-test-user)**  – szeretné, hogy egy megfelelője a Britta Simon üzemeltetőjének jelszókezelő & digitális tároló, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és az üzemeltetőjének jelszókezelő & digitális tároló alkalmazás egyszeri bejelentkezés konfigurálása.

**A jelszókezelő üzemeltetőjének & digitális tároló konfigurálása az Azure AD egyszeri bejelentkezés, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **üzemeltetőjének jelszókezelő & digitális tároló** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/keeperpasswordmanager-tutorial/tutorial_keeper_samlbase.png)

1. Az a **üzemeltetőjének jelszókezelő & digitális Vault tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/keeperpasswordmanager-tutorial/tutorial_keeper_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://{SSO CONNECT SERVER}/sso-connect/saml/login`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://{SSO CONNECT SERVER}/sso-connect/saml/sso`

    c. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://{SSO CONNECT SERVER}/sso-connect`

    > [!NOTE] 
    > Ezek a értékei nem valódi. Frissítse a tényleges válasz URL-címet, és a bejelentkezési URL-ezeket az értékeket. Kapcsolattartó [üzemeltetőjének jelszókezelő & digitális tároló ügyfél-támogatási csoportjának](https://keepersecurity.com/contact.html) beolvasni ezeket az értékeket. 

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/keeperpasswordmanager-tutorial/tutorial_keeper_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/keeperpasswordmanager-tutorial/tutorial_general_400.png)
    
1. Az a **üzemeltetőjének jelszókezelő & digitális tároló konfigurációja** területén kattintson **konfigurálása üzemeltetőjének jelszókezelő & digitális tároló** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/keeperpasswordmanager-tutorial/tutorial_keeper_configure.png) 

1. Az egyszeri bejelentkezés konfigurálása **üzemeltetőjének jelszókezelő & digitális tároló konfigurációja** oldalán, kövesse a megadott [üzemeltetőjének támogatási útmutató](https://keepersecurity.com/assets/pdf/KeeperSSOConnect_v11.pdf).

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/keeperpasswordmanager-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/keeperpasswordmanager-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/keeperpasswordmanager-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/keeperpasswordmanager-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-keeper-password-manager--digital-vault-test-user"></a>Jelszókezelő üzemeltetőjének & digitális Vault tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók üzemeltetőjének jelszókezelő & digitális tároló jelentkezzen be, akkor ki kell építeni üzemeltetőjének jelszókezelő & digitális tároló. Alkalmazás támogatja a csak az idő felhasználókiépítés, miután a felhasználók hitelesítésére, az alkalmazás automatikusan létrejön. Felveheti a kapcsolatot [üzemeltetőjének támogatási](https://keepersecurity.com/contact.html), ha szeretné telepíteni a felhasználók manuálisan.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon üzemeltetőjének jelszókezelő & digitális tárolóhoz a hozzáférés biztosításával Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel üzemeltetőjének jelszókezelő & digitális tároló, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **üzemeltetőjének jelszókezelő & digitális tároló**.

    ![Egyszeri bejelentkezés konfigurálása](./media/keeperpasswordmanager-tutorial/tutorial_keeper_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a üzemeltetőjének jelszókezelő & digitális tároló csempére kattint, üzemeltetőjének jelszókezelő & digitális tároló alkalmazás bejelentkezési oldalának szerezheti be. Sikeres hitelesítést követően szerezheti be az alkalmazásba. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/keeperpasswordmanager-tutorial/tutorial_general_01.png
[2]: ./media/keeperpasswordmanager-tutorial/tutorial_general_02.png
[3]: ./media/keeperpasswordmanager-tutorial/tutorial_general_03.png
[4]: ./media/keeperpasswordmanager-tutorial/tutorial_general_04.png

[100]: ./media/keeperpasswordmanager-tutorial/tutorial_general_100.png

[200]: ./media/keeperpasswordmanager-tutorial/tutorial_general_200.png
[201]: ./media/keeperpasswordmanager-tutorial/tutorial_general_201.png
[202]: ./media/keeperpasswordmanager-tutorial/tutorial_general_202.png
[203]: ./media/keeperpasswordmanager-tutorial/tutorial_general_203.png


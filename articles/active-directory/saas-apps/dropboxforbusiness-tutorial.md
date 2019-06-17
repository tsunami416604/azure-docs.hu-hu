---
title: 'Oktatóanyag: Az Azure Active Directory-integráció, a Dropbox Business |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Dropbox Business és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 63502412-758b-4b46-a580-0e8e130791a1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 591d8d28be5fc9322de8cf4886dc5924f53b1deb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67103785"
---
# <a name="tutorial-integrate-dropbox-for-business-with-azure-active-directory"></a>Oktatóanyag: Az Azure Active Directory integrálása Dropbox

Ebben az oktatóanyagban elsajátíthatja a Dropbox Business integrálása az Azure Active Directory (Azure AD) lesz. Dropbox Business és az Azure AD integrálása, akkor a következőket teheti:

* Szabályozza, ki férhet hozzá Dropbox Business az Azure AD-ben.
* Engedélyezze a felhasználókat, hogy a rendszer automatikusan bejelentkezve Dropbox Business az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Előfeltételek

Első lépésként szüksége van a következő elemek:

* Az Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, beszerezheti a egyhónapos ingyenes próbaidőszakot [Itt](https://azure.microsoft.com/pricing/free-trial/).
* Dropbox Business egyszeri bejelentkezés (SSO) az előfizetés engedélyezve van.

## <a name="scenario-description"></a>Forgatókönyv leírása

* Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD SSO-t egy tesztkörnyezetben. Üzleti támogatja a Dropbox **SP** által kezdeményezett egyszeri bejelentkezés

* Üzleti támogatja a Dropbox **igény szerinti** felhasználók átadása

## <a name="adding-dropbox-for-business-from-the-gallery"></a>Dropbox Business hozzáadása a katalógusból

A Dropbox, az Azure AD-be a vállalati integráció konfigurálásához, hozzá kell Dropbox Business a galériából a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs ablaktáblán válassza ki a **Azure Active Directory** szolgáltatás.
1. Navigáljon a **vállalati alkalmazások** majd **minden alkalmazás**.
1. Új alkalmazás hozzáadásához válassza **új alkalmazás**.
1. Az a **Hozzáadás a katalógusból** területén írja be a **Dropbox Business** kifejezést a keresőmezőbe.
1. Válassza ki **Dropbox Business** az eredmények panelen, és vegye fel az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőn.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Konfigurálás és tesztelés az Azure AD SSO Dropbox Business nevű tesztfelhasználó használata **Britta Simon**. Egyszeri bejelentkezés működjön, a Dropbox Business az Azure AD-felhasználót és a kapcsolódó felhasználó közötti hivatkozás kapcsolatot hozhat létre kell.

Az Azure AD SSO Dropbox Business tesztelése és konfigurálása, hajtsa végre a következő építőelemeket:

1. **[Az Azure AD SSO konfigurálása](#configure-azure-ad-sso)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Dropbox Business egyszeri bejelentkezés konfigurálása](#configure-dropbox-for-business-sso)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre Dropbox Business tesztfelhasználó](#create-dropbox-for-business-test-user)**  – egy megfelelője a Britta Simon Dropbox Business, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
6. **[Egyszeri bejelentkezés tesztelése](#test-sso)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD egyszeri bejelentkezés engedélyezése az Azure Portalon.

1. Az a [az Azure portal](https://portal.azure.com/), a a **Dropbox Business** alkalmazás integráció lapon keresse meg a **kezelés** szakaszt, és válassza **egyszeri bejelentkezés**.
1. Az a **egyszeri bejelentkezési módszer** lapra, jelölje be **SAML**.
1. Az a **állítsa be egyszeri bejelentkezést az SAML** lap, kattintson a Szerkesztés/toll ikonra a **alapszintű SAML-konfigurációja** beállításait módosíthatja.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az a **alapszintű SAML-konfigurációja** lap, adja meg az értékeket a következő mezőket:

    a. Az a **bejelentkezési URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://www.dropbox.com/sso/<id>`

    b. Az a **azonosító (entityid)** szövegmezőbe írjon be egy értéket: `Dropbox`

    > [!NOTE]
    > A fenti bejelentkezési URL-érték nem valódi értéket. A tényleges bejelentkezési URL-címet, az oktatóanyag későbbi részében ismertetett frissíteni az értéket.

1. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

1. Az a **állítsa be a Dropbox Business** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-dropbox-for-business-sso"></a>Dropbox üzleti egyszeri bejelentkezés konfigurálása

1. Automatizálhatja a Dropbox Business konfigurációra, telepítenie kell **saját alkalmazások biztonságos bejelentkezési böngészőbővítmény** kattintva **a bővítmény telepítése**.

    ![Saját alkalmazások kiterjesztése](common/install-myappssecure-extension.png)

2. A felvett bővítmény a böngészőre, kattintson a **telepítő Dropbox Business** fogja irányítani, a Dropbox, az üzleti alkalmazás. Itt adja meg a rendszergazdai hitelesítő adatait, jelentkezzen be a Dropbox Business. A webböngésző-bővítmény automatikusan konfigurálja az alkalmazást, és 3 – 8. lépések automatizálásához.

    ![Konfiguráció beállítása](common/setup-sso.png)

3. Ha szeretné manuálisan beállítani a Dropbox Business, nyisson meg egy új böngészőablakban, és keresse fel a Dropbox Business bérlő a, és jelentkezzen be a Dropboxba üzleti bérlő. és hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/dropboxforbusiness-tutorial/ic769509.png "egyszeri bejelentkezés konfigurálása")

4. Kattintson a a **felhasználói ikon** válassza **beállítások** fülre.

    ![Egyszeri bejelentkezés konfigurálása](./media/dropboxforbusiness-tutorial/configure1.png "egyszeri bejelentkezés konfigurálása")

5. A bal oldali navigációs panelén kattintson **felügyeleti konzol**.

    ![Egyszeri bejelentkezés konfigurálása](./media/dropboxforbusiness-tutorial/configure2.png "egyszeri bejelentkezés konfigurálása")

6. Az a **felügyeleti konzol**, kattintson a **beállítások** a bal oldali navigációs ablaktáblán.

    ![Egyszeri bejelentkezés konfigurálása](./media/dropboxforbusiness-tutorial/configure3.png "egyszeri bejelentkezés konfigurálása")

7. Válassza ki **egyszeri bejelentkezési** lehetőség a **hitelesítési** szakaszban.

    ![Egyszeri bejelentkezés konfigurálása](./media/dropboxforbusiness-tutorial/configure4.png "egyszeri bejelentkezés konfigurálása")

8. Az a **egyszeri bejelentkezési** szakaszban, hajtsa végre az alábbi lépéseket:  

    ![Egyszeri bejelentkezés konfigurálása](./media/dropboxforbusiness-tutorial/configure5.png "egyszeri bejelentkezés konfigurálása")

    a. Válassza ki **szükséges** beállítás lehetőséget a legördülő menüből a **egyszeri bejelentkezési**.

    b. Kattintson a **bejelentkezési URL-hozzáadása** és a a **identitásszolgáltató bejelentkezési URL-címe szolgáltató** szövegmezőjébe illessze be a **bejelentkezési URL-cím** érték, amely az Azure Portalon, és ezután válassza másolta **Kész**.

    ![Egyszeri bejelentkezés konfigurálása](./media/dropboxforbusiness-tutorial/configure6.png "egyszeri bejelentkezés konfigurálása")

    c. Kattintson a **tanúsítvány feltöltése**, és keresse meg a **Base64-kódolású tanúsítványfájl** , amely az Azure Portalról letöltött.

    d. Kattintson a **hivatkozás másolása** , és illessze be a másolt érték a **bejelentkezési URL-** a szövegmező **Dropbox Business tartomány és URL-címek** szakaszban az Azure Portalon.

    e. Kattintson a **Save** (Mentés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban az Azure Portalon Britta Simon nevű tesztfelhasználó fog létrehozni.

1. Az Azure Portal bal oldali panelén válassza **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.
1. Válassza ki **új felhasználó** a képernyő tetején.
1. Az a **felhasználói** tulajdonságok, kövesse az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `Britta Simon`.  
   1. Az a **felhasználónév** mezőbe írja be a username@companydomain.extension. Például: `BrittaSimon@contoso.com`.
   1. Válassza ki a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.
   1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban Britta Simon által biztosított hozzáférés Dropbox Business Azure egyszeri bejelentkezés használatához engedélyeznie kell.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.
1. Az alkalmazások listájában jelölje ki a **Dropbox Business**.
1. Az alkalmazás áttekintése lapon keresse meg a **kezelés** szakaszt, és válassza **felhasználók és csoportok**.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza ki **felhasználó hozzáadása**, majd **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. Az a **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listájából, majd kattintson a **kiválasztása** gombra a képernyő alján.
1. Ha a SAML helyességi feltétel, a szerepkör értéket vár a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználóhoz a listából, és kattintson a **kiválasztása** gombra a képernyő alján.
1. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-dropbox-for-business-test-user"></a>Hozzon létre Dropbox Business tesztfelhasználó számára

Ebben a szakaszban egy Britta Simon nevű felhasználó Dropbox Business jön létre. Dropbox Business támogatja a just-in-time-felhasználók létrehozásának, amely alapértelmezés szerint engedélyezve van. Nincs meg ebben a szakaszban a művelet elem. Ha a felhasználó még nem létezik a Dropboxban vállalati, a hitelesítés után egy új jön létre.

>[!Note]
>Ha manuálisan hozzon létre egy felhasználót, lépjen kapcsolatba kell [üzleti ügyfél támogatási csapata Dropbox](https://www.dropbox.com/business/contact)

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

A Dropbox Business csempe kiválasztásakor a hozzáférési panelen azt kell automatikusan megtörténik a, a Dropbox Business, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
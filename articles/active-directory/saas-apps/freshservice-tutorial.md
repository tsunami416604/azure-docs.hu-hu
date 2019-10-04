---
title: 'Oktatóanyag: Az Azure Active Directory-integráció a Freshservice |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Freshservice között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 3dd22b1f-445d-45c6-8eda-30207eb9a1a8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8fbf69ba814b99434e933ed700fb0c8c842c3312
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67101844"
---
# <a name="tutorial-integrate-freshservice-with-azure-active-directory"></a>Oktatóanyag: Freshservice integrálása az Azure Active Directoryval

Ebben az oktatóanyagban elsajátíthatja a Freshservice integrálása az Azure Active Directory (Azure AD) lesz. Freshservice integrálása az Azure ad-vel, akkor a következőket teheti:

* Szabályozhatja az Azure AD ki férhet hozzá a Freshservice.
* Engedélyezze a felhasználóknak, hogy lehet automatikusan bejelentkezve Freshservice az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Előfeltételek

Első lépésként szüksége van a következő elemek:

* Az Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, beszerezheti a egyhónapos ingyenes próbaidőszakot [Itt](https://azure.microsoft.com/pricing/free-trial/).
* Freshservice egyszeri bejelentkezés (SSO) engedélyezve van az előfizetésben.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD SSO-t egy tesztkörnyezetben. Támogatja a Freshservice **SP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-freshservice-from-the-gallery"></a>Freshservice hozzáadása a katalógusból

Az Azure AD integrálása a Freshservice konfigurálásához hozzá kell Freshservice a galériából a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs ablaktáblán válassza ki a **Azure Active Directory** szolgáltatás.
1. Navigáljon a **vállalati alkalmazások** majd **minden alkalmazás**.
1. Új alkalmazás hozzáadásához válassza **új alkalmazás**.
1. Az a **Hozzáadás a katalógusból** területén írja be a **Freshservice** kifejezést a keresőmezőbe.
1. Válassza ki **Freshservice** az eredmények panelen, és vegye fel az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőn.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Konfigurálás és tesztelés az Azure AD SSO nevű tesztfelhasználó használata Freshservice **Britta Simon**. Az SSO működjön szüksége egy Azure AD-felhasználót és a kapcsolódó felhasználó közötti kapcsolat kapcsolat létrehozására a Freshservice.

Az Azure AD SSO Freshservice tesztelése és konfigurálása, hajtsa végre a következő építőelemeket:

1. **[Az Azure AD SSO konfigurálása](#configure-azure-ad-sso)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Freshservice egyszeri bejelentkezést](#configure-freshservice-sso)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre Freshservice tesztfelhasználót](#create-freshservice-test-user)**  - a-megfelelője a Britta Simon szerepel, amely kapcsolódik az Azure AD felhasználói ábrázolása Freshservice.
6. **[Egyszeri bejelentkezés tesztelése](#test-sso)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD egyszeri bejelentkezés engedélyezése az Azure Portalon.

1. Az a [az Azure portal](https://portal.azure.com/), a a **Freshservice** alkalmazás integráció lapon keresse meg a **kezelése** szakaszt, és válassza **egyszeri bejelentkezési**.
1. Az a **egyszeri bejelentkezési módszer** lapra, jelölje be **SAML**.
1. Az a **állítsa be egyszeri bejelentkezést az SAML** lap, kattintson a Szerkesztés/toll ikonra a **alapszintű SAML-konfigurációja** beállításait módosíthatja.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az a **alapszintű SAML-konfigurációja** lap, adja meg az értékeket a következő mezőket:

    a. Az a **bejelentkezési URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<democompany>.freshservice.com`

    b. Az a **azonosító (entityid)** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<democompany>.freshservice.com`

    > [!NOTE]
    > Ezek a értékei nem valódi. Frissítse a tényleges bejelentkezési URL-címet és azonosító ezeket az értékeket. Kapcsolattartó [Freshservice ügyfél-támogatási csapatának](https://support.freshservice.com/) beolvasni ezeket az értékeket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **szerkesztése** gombra kattintva nyissa meg a **SAML-aláíró tanúsítvány** párbeszédpanel.

    ![SAML-aláíró tanúsítvány szerkesztése](common/edit-certificate.png)

1. Az a **SAML-aláíró tanúsítvány** területén másolja a **ujjlenyomat** , és mentse a számítógépre.

    ![Másolja ki az ujjlenyomat értéket](common/copy-thumbprint.png)

1. Az a **állítsa be a Freshservice** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-freshservice-sso"></a>Freshservice egyszeri bejelentkezés konfigurálása

1. Automatizálhatja a Freshservice konfigurációra, telepítenie kell **saját alkalmazások biztonságos bejelentkezési böngészőbővítmény** kattintva **a bővítmény telepítése**.

    ![Saját alkalmazások kiterjesztése](common/install-myappssecure-extension.png)

2. A felvett bővítmény a böngészőre, kattintson a **telepítő Freshservice** fog irányítja át a Freshservice alkalmazás. Itt adja meg a rendszergazdai hitelesítő adatait, jelentkezzen be a Freshservice. A webböngésző-bővítmény automatikusan konfigurálja az alkalmazást, és 3 – 6. lépések automatizálásához.

    ![Konfiguráció beállítása](common/setup-sso.png)

3. Ha szeretné manuálisan beállítani a Freshservice, nyisson meg egy új böngészőablakban, és jelentkezzen be rendszergazdaként vállalati Freshservice webhelyét, és hajtsa végre az alábbi lépéseket:

4. A felső menüben kattintson **rendszergazdai**.

    ![Rendszergazdai](./media/freshservice-tutorial/ic790814.png "rendszergazda")

5. Az a **Ügyfélportál**, kattintson a **biztonsági**.

    ![Biztonsági](./media/freshservice-tutorial/ic790815.png "biztonsági")

6. Az a **biztonsági** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés](./media/freshservice-tutorial/ic790816.png "egyszeri bejelentkezés")

    a. Kapcsoló **egyszeri bejelentkezés**.

    b. Válassza ki **SAML SSO**.

    c. Az a **SAML bejelentkezési URL-cím** szövegmezőbe, illessze be az értéket a **bejelentkezési URL-cím**, az Azure Portalról másolt.

    d. Az a **kijelentkezési URL-címe** szövegmezőjébe illessze be az értéket, **kijelentkezési URL-címe**, az Azure Portalról másolt.

    e. A **biztonsági tanúsítvány-ujjlenyomat** szövegmezőjébe illessze be a **UJJLENYOMAT** tanúsítványt, amely az Azure Portalról másolt értékét.

    f. Kattintson a **Save** (Mentés) gombra

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

Ebben a szakaszban Britta Simon által biztosított hozzáférés Freshservice Azure egyszeri bejelentkezés használatához engedélyeznie kell.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.
1. Az alkalmazások listájában jelölje ki a **Freshservice**.
1. Az alkalmazás áttekintése lapon keresse meg a **kezelés** szakaszt, és válassza **felhasználók és csoportok**.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza ki **felhasználó hozzáadása**, majd **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. Az a **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listájából, majd kattintson a **kiválasztása** gombra a képernyő alján.
1. Ha a SAML helyességi feltétel, a szerepkör értéket vár a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználóhoz a listából, és kattintson a **kiválasztása** gombra a képernyő alján.
1. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-freshservice-test-user"></a>Freshservice tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók jelentkezzen be a FreshService, akkor ki kell építeni FreshService be. Esetén a FreshService kiépítés a manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **FreshService** rendszergazdaként a vállalati webhely.

2. A felső menüben kattintson **rendszergazdai**.

    ![Rendszergazdai](./media/freshservice-tutorial/ic790814.png "rendszergazda")

3. Az a **felhasználókezelés** területén kattintson **igénylők**.

    ![Igénylők](./media/freshservice-tutorial/ic790818.png "igénylők")

4. Kattintson a **új kérelmező**.

    ![Új igénylők](./media/freshservice-tutorial/ic790819.png "új igénylők")

5. Az a **új kérelmező** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Új kérelmező](./media/freshservice-tutorial/ic790820.png "új kérelmező")  

    a. Adja meg a **Utónév** és **E-mail** attribútumok egy érvényes Azure Active Directory-fióknevet, amelyet a kapcsolódó szövegmezőkben létrehozásához.

    b. Kattintson a **Save** (Mentés) gombra.

    > [!NOTE]
    > Az Azure Active Directory fióktulajdonos kap egy e-mailt és a telepítőre mutató mielőtt aktívvá válik, győződjön meg arról, hogy a fiók
    >  

> [!NOTE]
> Bármely más FreshService felhasználói fiók létrehozása eszközöket használhatja, vagy az aad-ben a felhasználói fiókok kiépítését FreshService által biztosított API-k.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

A Freshservice csempe kiválasztásakor a hozzáférési panelen, érdemes lehet automatikusan bejelentkezett a Freshservice, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Replicon |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Replicon között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 02a62f15-917c-417c-8d80-fe685e3fd601
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b27615b0c76b5c23bbc79788431b0e909b8bf22a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67092768"
---
# <a name="tutorial-integrate-replicon-with-azure-active-directory"></a>Oktatóanyag: Replicon integrálása az Azure Active Directoryval

Ebben az oktatóanyagban elsajátíthatja a Replicon integrálása az Azure Active Directory (Azure AD) lesz. Replicon integrálása az Azure ad-vel, akkor a következőket teheti:

* Szabályozza, ki férhet hozzá Replicon Azure AD-ben.
* Engedélyezze a felhasználóknak, hogy lehet automatikusan bejelentkezve Replicon az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Előfeltételek

Első lépésként szüksége van a következő elemek:

* Az Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, beszerezheti a egyhónapos ingyenes próbaidőszakot [Itt](https://azure.microsoft.com/pricing/free-trial/).
* Replicon egyszeri bejelentkezés (SSO) engedélyezve van az előfizetésben.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD SSO-t egy tesztkörnyezetben. Támogatja a replicon **SP** által kezdeményezett egyszeri bejelentkezés.

## <a name="adding-replicon-from-the-gallery"></a>Replicon hozzáadása a katalógusból

Az Azure AD integrálása a Replicon konfigurálásához hozzá kell Replicon a katalógusból a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs ablaktáblán válassza ki a **Azure Active Directory** szolgáltatás.
1. Navigáljon a **vállalati alkalmazások** majd **minden alkalmazás**.
1. Új alkalmazás hozzáadásához válassza **új alkalmazás**.
1. Az a **Hozzáadás a katalógusból** területén írja be a **Replicon** kifejezést a keresőmezőbe.
1. Válassza ki **Replicon** az eredmények panelen, és vegye fel az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőn.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Konfigurálás és tesztelés az Azure AD SSO nevű tesztfelhasználó használata Replicon **B.Simon**. Az SSO működjön kell Replicon az Azure AD-felhasználót és a kapcsolódó felhasználó közötti hivatkozás kapcsolatot hozhat létre.

Az Azure AD SSO Replicon tesztelése és konfigurálása, hajtsa végre a következő építőelemeket:

1. **[Az Azure AD SSO konfigurálása](#configure-azure-ad-sso)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Replicon egyszeri bejelentkezést](#configure-replicon-sso)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az B.Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – B.Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre Replicon tesztfelhasználót](#create-replicon-test-user)**  - a-megfelelője a B.Simon szerepel, amely kapcsolódik az Azure AD felhasználói ábrázolása Replicon.
6. **[Egyszeri bejelentkezés tesztelése](#test-sso)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD egyszeri bejelentkezés engedélyezése az Azure Portalon.

1. Az a [az Azure portal](https://portal.azure.com/), a a **Replicon** alkalmazás integráció lapon keresse meg a **kezelése** szakaszt, és válassza **egyszeri bejelentkezési**.
1. Az a **egyszeri bejelentkezési módszer** lapra, jelölje be **SAML**.
1. Az a **állítsa be egyszeri bejelentkezést az SAML** lap, kattintson a Szerkesztés/toll ikonra a **alapszintű SAML-konfigurációja** beállításait módosíthatja.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az a **alapszintű SAML-konfigurációja** lap, adja meg az értékeket a következő mezőket:

    1. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím: `https://global.replicon.com/!/saml2/<client name>/sp-sso/post`

    1. Az a **azonosító** mezőbe írja be a következő minta használatával URL-cím: `https://global.replicon.com/!/saml2/<client name>`

    1. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://global.replicon.com/!/saml2/<client name>/sso/post`

    > [!NOTE]
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL-, azonosítóját és válasz URL-cím. Kapcsolattartó [Replicon ügyfél-támogatási csapatának](https://www.replicon.com/customerzone/contact-support) beolvasni ezeket az értékeket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

1. Kattintson a Szerkesztés/toll ikonra a **SAML-aláíró tanúsítvány** beállításait módosíthatja.

    ![Aláíró algoritmus](common/signing-algorithm.png)

    1. Válassza ki **bejelentkezési SAML helyességi feltétel** , a **aláírási beállítás**.

    1. Válassza ki **SHA-256 algoritmust** , a **aláíró algoritmus**.

1. A a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén található **összevonási metaadatainak XML** válassza **letöltése** töltse le a tanúsítványt, és menti azt a számítógépet.

   ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

### <a name="configure-replicon-sso"></a>Replicon egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Replicon vállalati hely rendszergazdaként.

2. Adja meg az SAML 2.0, hajtsa végre az alábbi lépéseket:

    ![SAML-hitelesítés engedélyezése](./media/replicon-tutorial/ic777805.png "engedélyezése SAML-hitelesítés")

    a. A megjelenítendő a **EnableSAML Authentication2** párbeszédpanelen fűzze hozzá a következő az URL-címre, a vállalat kulcs után: `/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

    * Az alábbiakban látható a teljes URL-cím sémája: `https://na2.replicon.com/\<YourCompanyKey\>/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

   b. Kattintson a **+** bontsa ki a **v20Configuration** szakaszban.

   c. Kattintson a **+** bontsa ki a **metaDataConfiguration** szakaszban.

   d. Válassza ki **SHA256** xmlSignatureAlgorithm számára

   e. Kattintson a **fájl kiválasztása**ki az identitás szolgáltató metaadatainak XML-fájlt, majd kattintson a **küldés**.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban az Azure Portalon B.Simon nevű tesztfelhasználó fog létrehozni.

1. Az Azure Portal bal oldali panelén válassza **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.
1. Válassza ki **új felhasználó** a képernyő tetején.
1. Az a **felhasználói** tulajdonságok, kövesse az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. Az a **felhasználónév** mezőbe írja be a username@companydomain.extension. Például: `BrittaSimon@contoso.com`.
   1. Válassza ki a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.
   1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban B.Simon által biztosított hozzáférés Replicon Azure egyszeri bejelentkezés használatához engedélyeznie kell.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.
1. Az alkalmazások listájában jelölje ki a **Replicon**.
1. Az alkalmazás áttekintése lapon keresse meg a **kezelés** szakaszt, és válassza **felhasználók és csoportok**.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza ki **felhasználó hozzáadása**, majd **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. Az a **felhasználók és csoportok** párbeszédablakban válassza **B.Simon** a felhasználók listájából, majd kattintson a **kiválasztása** gombra a képernyő alján.
1. Ha a SAML helyességi feltétel, a szerepkör értéket vár a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználóhoz a listából, és kattintson a **kiválasztása** gombra a képernyő alján.
1. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-replicon-test-user"></a>Replicon tesztfelhasználó létrehozása

Ez a szakasz célja Replicon B.Simon nevű felhasználó létrehozásához.

**Hozza létre a felhasználó manuálisan kell, ha hajtsa végre a következő lépéseket:**

1. Egy böngészőablakban jelentkezzen be a Replicon vállalati hely rendszergazdaként.

2. Lépjen a **felügyeleti \> felhasználók**.

    ![Felhasználók](./media/replicon-tutorial/ic777806.png "felhasználók")

3. Kattintson a **+ felhasználó hozzáadása**.

    ![Felhasználó hozzáadása](./media/replicon-tutorial/ic777807.png "felhasználó hozzáadása")

4. Az a **felhasználói profil** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Felhasználói profil](./media/replicon-tutorial/ic777808.png "felhasználói profil")

    a. Az a **bejelentkezési név** szövegmezőbe írja be az Azure AD e-mail címét, az Azure AD-felhasználót kíván létrehozni, például `B.Simon@contoso.com`.

    > [!NOTE]
    > Bejelentkezési nevet meg kell felelnie a felhasználó e-mail címét az Azure ad-ben

    b. Mint **hitelesítési típus**válassza **SSO**.

    c. Hitelesítési azonosító értéke megegyezik a bejelentkezési nevet (az Azure ad-ben felhasználó e-mail címét a) beállítása

    d. Az a **részleg** szövegmezőbe írja be a felhasználó részlege.

    e. Mint **alkalmazott típusa**válassza **rendszergazda**.

    f. Kattintson a **felhasználói profil mentése**.

> [!NOTE]
> Eszközt is használhat bármilyen más Replicon felhasználói fiók létrehozása, vagy az Azure AD-felhasználói fiókok kiépítése Replicon által biztosított API-k.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

A Replicon csempe kiválasztásakor a hozzáférési panelen, kell lennie automatikusan bejelentkezett a Replicon, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
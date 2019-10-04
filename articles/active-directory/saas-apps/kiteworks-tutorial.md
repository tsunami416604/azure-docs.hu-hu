---
title: 'Oktatóanyag: Az Azure Active Directory-integráció a Kiteworks |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Kiteworks között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f7984aaf-ab1f-4a85-9646-a9523f5275d9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: jeedes
ms.openlocfilehash: 52b113d92fa83795e94d5179ea47ed5d9d9e9a26
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68248920"
---
# <a name="tutorial-integrate-kiteworks-with-azure-active-directory"></a>Oktatóanyag: Kiteworks integrálása az Azure Active Directoryval

Ebben az oktatóanyagban elsajátíthatja a Kiteworks integrálása az Azure Active Directory (Azure AD) lesz. Kiteworks integrálása az Azure ad-vel, akkor a következőket teheti:

* Szabályozza, ki férhet hozzá a Kiteworks Azure AD-ben.
* Engedélyezze a felhasználóknak, hogy lehet automatikusan bejelentkezett a Kiteworks az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Előfeltételek

Első lépésként szüksége van a következő elemek:

* Az Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, beszerezheti a egyhónapos ingyenes próbaidőszakot [Itt](https://azure.microsoft.com/pricing/free-trial/).
* Kiteworks egyszeri bejelentkezés (SSO) engedélyezve van az előfizetésben.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD SSO-t egy tesztkörnyezetben.

* Támogatja a Kiteworks **SP** által kezdeményezett egyszeri bejelentkezés
* Támogatja a Kiteworks **igény szerinti** felhasználók átadása

## <a name="adding-kiteworks-from-the-gallery"></a>Kiteworks hozzáadása a katalógusból

Az Azure AD integrálása a Kiteworks konfigurálásához szüksége Kiteworks hozzáadása a felügyelt SaaS-alkalmazások listájában a katalógusból.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs ablaktáblán válassza ki a **Azure Active Directory** szolgáltatás.
1. Navigáljon a **vállalati alkalmazások** majd **minden alkalmazás**.
1. Új alkalmazás hozzáadásához válassza **új alkalmazás**.
1. Az a **Hozzáadás a katalógusból** területén írja be a **Kiteworks** kifejezést a keresőmezőbe.
1. Válassza ki **Kiteworks** az eredmények panelen, és vegye fel az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőn.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Konfigurálás és tesztelés az Azure AD SSO a Kiteworks nevű tesztfelhasználó használata **B.Simon**. Működik az egyszeri bejelentkezés Azure AD-felhasználót és a kapcsolódó felhasználó közötti kapcsolat kapcsolat létrehozására a Kiteworks szüksége.

Az Azure AD SSO a Kiteworks tesztelése és konfigurálása, hajtsa végre a következő építőelemeket:

1. **[Az Azure AD SSO konfigurálása](#configure-azure-ad-sso)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Kiteworks egyszeri bejelentkezést](#configure-kiteworks-sso)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre Kiteworks tesztfelhasználót](#create-kiteworks-test-user)**  – egy megfelelője a Britta Simon Kiteworks, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
6. **[Egyszeri bejelentkezés tesztelése](#test-sso)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD egyszeri bejelentkezés engedélyezése az Azure Portalon.

1. Az a [az Azure portal](https://portal.azure.com/), a a **Kiteworks** alkalmazás integráció lapon keresse meg a **kezelése** szakaszt, és válassza **egyszeri bejelentkezési**.
1. Az a **egyszeri bejelentkezési módszer** lapra, jelölje be **SAML**.
1. Az a **állítsa be egyszeri bejelentkezést az SAML** lap, kattintson a Szerkesztés/toll ikonra a **alapszintű SAML-konfigurációja** beállításait módosíthatja.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az a **alapszintű SAML-konfigurációja** területén adja meg az értékeket a következő mezőket:

    a. Az a **bejelentkezési URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<kiteworksURL>.kiteworks.com`

    b. Az a **azonosító (entityid)** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<kiteworksURL>/sp/module.php/saml/sp/saml2-acs.php/sp-sso`

    > [!NOTE]
    > Ezek a értékei nem valódi. Frissítse a tényleges bejelentkezési URL-címet és azonosító ezeket az értékeket. Kapcsolattartó [Kiteworks ügyfél-támogatási csapatának](https://accellion.com/support) beolvasni ezeket az értékeket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

1. A a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén található **tanúsítvány (Base64)** válassza **letöltése** töltse le a tanúsítványt, és menti azt a számítógépet.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

1. Az a **állítsa be a Kiteworks** területén másolja a megfelelő URL-címe szerint.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

### <a name="configure-kiteworks-sso"></a>Kiteworks egyszeri bejelentkezés konfigurálása

1. Jelentkezzen be rendszergazdaként a Kiteworks vállalati webhely.

1. A felső eszköztáron kattintson **beállítások**.

    ![Egyszeri bejelentkezés konfigurálása](./media/kiteworks-tutorial/tutorial_kiteworks_06.png)

1. Az a **hitelesítési és engedélyezési** területén kattintson **egyszeri bejelentkezés beállítása**.

    ![Egyszeri bejelentkezés konfigurálása](./media/kiteworks-tutorial/tutorial_kiteworks_07.png)

1. Az egyszeri bejelentkezés beállítása lapon hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/kiteworks-tutorial/tutorial_kiteworks_09.png)

    a. Válassza ki **SSO-on keresztül hitelesítendő**.

    b. Válassza ki **AuthnRequest kezdeményezése**.

    c. Az a **Identitásszolgáltató Entitásazonosító** szövegmezőbe, illessze be az értéket a **az Azure AD-azonosító**, az Azure Portalról másolt.

    d. Az a **egyszeri bejelentkezési szolgáltatás URL-cím** szövegmezőbe, illessze be az értéket a **bejelentkezési URL-cím**, az Azure Portalról másolt.

    e. Az a **egyszeri kijelentkezési szolgáltatás URL-cím** szövegmező, illessze be az értéket a **kijelentkezési URL-címe**, az Azure Portalról másolt.

    f. Nyissa meg a letöltött tanúsítvány a Jegyzettömbben, másolja a tartalmat, és illessze be azt a **RSA nyilvános kulcs tanúsítvány** szövegmezőbe.

    g. Kattintson a **Save** (Mentés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban az Azure Portalon B.Simon nevű tesztfelhasználó fog létrehozni.

1. Az Azure Portal bal oldali panelén válassza **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.
1. Válassza ki **új felhasználó** a képernyő tetején.
1. Az a **felhasználói** tulajdonságok, kövesse az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.
   1. Az a **felhasználónév** mezőbe írja be a username@companydomain.extension. Például: `B.Simon@contoso.com`.
   1. Válassza ki a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.
   1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban B.Simon által biztosított hozzáférés Kiteworks Azure egyszeri bejelentkezés használatához engedélyeznie kell.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.
1. Az alkalmazások listájában jelölje ki a **Kiteworks**.
1. Az alkalmazás áttekintése lapon keresse meg a **kezelés** szakaszt, és válassza **felhasználók és csoportok**.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza ki **felhasználó hozzáadása**, majd **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. Az a **felhasználók és csoportok** párbeszédablakban válassza **B.Simon** a felhasználók listájából, majd kattintson a **kiválasztása** gombra a képernyő alján.
1. Ha a SAML helyességi feltétel, a szerepkör értéket vár a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználóhoz a listából, és kattintson a **kiválasztása** gombra a képernyő alján.
1. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-kiteworks-test-user"></a>Kiteworks tesztfelhasználó létrehozása

Ez a szakasz célja Britta Simon Kiteworks nevű felhasználó létrehozásához.

Kiteworks támogatja a just-in-time-kiépítés, amely alapértelmezésben engedélyezve van. Nincs meg ebben a szakaszban a művelet elem. Új felhasználó próbál hozzáférni a Kiteworks, ha még nem létezik jön létre.

> [!NOTE]
> Hozzon létre egy felhasználót manuálisan kell, ha kapcsolódni kell a [Kiteworks támogatási csoportjának](https://accellion.com/support).

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Kiteworks csempére kattint, meg kell lehet automatikusan bejelentkezett a Kiteworks, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

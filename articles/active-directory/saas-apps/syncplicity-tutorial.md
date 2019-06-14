---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Syncplicity |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Syncplicity között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 896a3211-f368-46d7-95b8-e4768c23be08
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/10/2019
ms.author: jeedes
ms.openlocfilehash: c823b1b5d009d836ba3f134623a67cab0d38c180
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67050324"
---
# <a name="tutorial-integrate-syncplicity-with-azure-active-directory"></a>Oktatóanyag: Syncplicity integrálása az Azure Active Directoryval

Ebben az oktatóanyagban elsajátíthatja a Syncplicity integrálása az Azure Active Directory (Azure AD) lesz. Syncplicity integrálása az Azure ad-vel, akkor a következőket teheti:

* Szabályozza, ki férhet hozzá Syncplicity Azure AD-ben.
* Engedélyezze a felhasználóknak, hogy lehet automatikusan bejelentkezve Syncplicity az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Előfeltételek

Első lépésként szüksége van a következő elemek:

* Az Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, beszerezheti a egyhónapos ingyenes próbaidőszakot [Itt](https://azure.microsoft.com/pricing/free-trial/).
* Syncplicity egyszeri bejelentkezés (SSO) engedélyezve van az előfizetésben.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD SSO-t egy tesztkörnyezetben. Támogatja a Syncplicity **SP** által kezdeményezett egyszeri bejelentkezés.

## <a name="adding-syncplicity-from-the-gallery"></a>Syncplicity hozzáadása a katalógusból

Az Azure AD integrálása a Syncplicity konfigurálásához hozzá kell Syncplicity a katalógusból a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs ablaktáblán válassza ki a **Azure Active Directory** szolgáltatás.
1. Navigáljon a **vállalati alkalmazások** majd **minden alkalmazás**.
1. Új alkalmazás hozzáadásához válassza **új alkalmazás**.
1. Az a **Hozzáadás a katalógusból** területén írja be a **Syncplicity** kifejezést a keresőmezőbe.
1. Válassza ki **Syncplicity** az eredmények panelen, és vegye fel az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőn.

## <a name="configure-and-test-azure-ad-sso"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Konfigurálás és tesztelés az Azure AD SSO nevű tesztfelhasználó használata Syncplicity **B.Simon**. Az SSO működjön kell Syncplicity az Azure AD-felhasználót és a kapcsolódó felhasználó közötti hivatkozás kapcsolatot hozhat létre.

Az Azure AD SSO Syncplicity tesztelése és konfigurálása, hajtsa végre a következő építőelemeket:

1. **[Az Azure AD SSO konfigurálása](#configure-azure-ad-sso)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Syncplicity egyszeri bejelentkezést](#configure-syncplicity-sso)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az B.Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – B.Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre Syncplicity tesztfelhasználót](#create-syncplicity-test-user)**  - a-megfelelője a B.Simon szerepel, amely kapcsolódik az Azure AD felhasználói ábrázolása Syncplicity.
6. **[Egyszeri bejelentkezés tesztelése](#test-sso)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD egyszeri bejelentkezés engedélyezése az Azure Portalon.

1. Az a [az Azure portal](https://portal.azure.com/), a a **Syncplicity** alkalmazás integráció lapon keresse meg a **kezelése** szakaszt, és válassza **egyszeri bejelentkezési**.
1. Az a **egyszeri bejelentkezési módszer** lapra, jelölje be **SAML**.
1. Az a **állítsa be egyszeri bejelentkezést az SAML** lap, kattintson a Szerkesztés/toll ikonra a **alapszintű SAML-konfigurációja** beállításait módosíthatja.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az a **alapszintű SAML-konfigurációja** lap, adja meg az értékeket a következő mezőket:

    a. Az a **bejelentkezési URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<companyname>.syncplicity.com`

    b. Az a **azonosító (entityid)** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<companyname>.syncplicity.com/sp`

    > [!NOTE]
    > Ezek a értékei nem valódi. Frissítse a tényleges bejelentkezési URL-címet és azonosító ezeket az értékeket. Kapcsolattartó [Syncplicity ügyfél-támogatási csapatának](https://www.syncplicity.com/contact-us) beolvasni ezeket az értékeket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

1. A a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén található **tanúsítvány (Base64)** válassza **letöltése** töltse le a tanúsítványt, és menti azt a számítógépet.

   ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

1. Az a **Syncplicity beállítása** területén másolja a megfelelő URL-címe szerint.

   ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

### <a name="configure-syncplicity-sso"></a>Syncplicity egyszeri bejelentkezés konfigurálása

1. Jelentkezzen be a **Syncplicity** bérlő.

1. A felső menüben kattintson **rendszergazdai**válassza **beállítások**, és kattintson a **egyéni tartomány és az egyszeri bejelentkezés**.

    ![Syncplicity](./media/syncplicity-tutorial/ic769545.png "Syncplicity")

1. Az a **egyszeri bejelentkezéses (SSO)** párbeszédpanel lapon, a következő lépésekkel:

    ![Egyszeri bejelentkezés \(egyszeri bejelentkezés\)](./media/syncplicity-tutorial/ic769550.png "Single Sign-On \\\(SSO\\\)")

    a. Az a **Custom Domain** szövegmezőbe írja be annak a tartománynak a nevét.
  
    b. Válassza ki **engedélyezve** , **egyszeri bejelentkezés állapotát**.

    c. Az a **entitásazonosító** szövegmezőjébe illessze be a **azonosító (entityid)** érték, amely már használtak a **alapszintű SAML-konfigurációja** az Azure Portalon.

    d. Az a **lap URL-címe be** szövegmezőjébe illessze be a **bejelentkezési URL-cím** Azure Portalról másolt.

    e. Az a **kijelentkezési URL-címe** szövegmezőjébe illessze be a **kijelentkezési URL-címe** Azure Portalról másolt.

    f. A **szolgáltató Identitástanúsítványt**, kattintson a **fájl kiválasztása**, majd töltse fel a tanúsítványt, amely az Azure Portalról letöltött.

    g. Kattintson a **MENTSE a MÓDOSÍTÁSOKAT**.

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

Ebben a szakaszban B.Simon által biztosított hozzáférés Syncplicity Azure egyszeri bejelentkezés használatához engedélyeznie kell.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.
1. Az alkalmazások listájában jelölje ki a **Syncplicity**.
1. Az alkalmazás áttekintése lapon keresse meg a **kezelés** szakaszt, és válassza **felhasználók és csoportok**.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza ki **felhasználó hozzáadása**, majd **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. Az a **felhasználók és csoportok** párbeszédablakban válassza **B.Simon** a felhasználók listájából, majd kattintson a **kiválasztása** gombra a képernyő alján.
1. Ha a SAML helyességi feltétel, a szerepkör értéket vár a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználóhoz a listából, és kattintson a **kiválasztása** gombra a képernyő alján.
1. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-syncplicity-test-user"></a>Syncplicity tesztfelhasználó létrehozása

AAD-felhasználók számára jelentkezhetnek be akkor ki kell építeni Syncplicity alkalmazáshoz. Ez a szakasz ismerteti a Syncplicity az AAD felhasználói fiókokat hozhat létre.

**Üzembe helyez egy felhasználói fiókot Syncplicity, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **Syncplicity** bérlő (például: `https://company.Syncplicity.com`).

1. Kattintson a **rendszergazdai** , és válassza ki **felhasználói fiókok** majd **A felhasználó hozzáadása**.

    ![Felhasználók kezelése](./media/syncplicity-tutorial/ic769764.png "felhasználók kezelése")

1. Írja be a **E-mail-címek** egy kíván üzembe helyezni az Azure AD-fiók, jelölje be a **felhasználói** , **szerepkör**, és kattintson a **tovább**.

    ![Fiókadatok](./media/syncplicity-tutorial/ic769765.png "fiókadatok")

    > [!NOTE]
    > Az AAD fióktulajdonos beolvasása egy e-mailt és a telepítőre mutató erősítse meg, és a fiók aktiválásához.

1. Válasszon ki egy csoportot, amely az új felhasználót kell tagjává válik, és kattintson a vállalata **tovább**.

    ![Csoporttagság](./media/syncplicity-tutorial/ic769772.png "csoporttagság")

    > [!NOTE]
    > Ha nincsenek felsorolva csoportok, kattintson a **tovább**.

1. Válassza ki a felhasználó számítógépén Syncplicity a vezérlő alá helyezni, és kattintson a kívánt mappákat **tovább**.

    ![Syncplicity mappák](./media/syncplicity-tutorial/ic769773.png "Syncplicity mappák")

> [!NOTE]
> Bármely más Syncplicity felhasználói fiók létrehozása eszközöket használhatja, vagy az aad-ben a felhasználói fiókok kiépítését Syncplicity által biztosított API-k.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

A Syncplicity csempe kiválasztásakor a hozzáférési panelen, kell lennie automatikusan bejelentkezett a Syncplicity, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
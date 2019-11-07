---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Freshservice | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Freshservice között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3dd22b1f-445d-45c6-8eda-30207eb9a1a8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3ba2f59799629873553fe261abebf59529ad94d
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2019
ms.locfileid: "73570617"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-freshservice"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Freshservice

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Freshservice a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az Freshservice-t az Azure AD-vel, a következőket teheti:

* A Freshservice-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Freshservice az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Freshservice egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Freshservice támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-freshservice-from-the-gallery"></a>Freshservice hozzáadása a gyűjteményből

A Freshservice Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Freshservice a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Freshservice** kifejezést a keresőmezőbe.
1. Válassza ki a **Freshservice** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-freshservice"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a Freshservice

Konfigurálja és tesztelje az Azure AD SSO-t a Freshservice a **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Freshservice-ben.

Az Azure AD SSO és a Freshservice konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[FRESHSERVICE SSO konfigurálása](#configure-freshservice-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre Freshservice-teszt felhasználót](#create-freshservice-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-Freshservice rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **Freshservice** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<democompany>.freshservice.com`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<democompany>.freshservice.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. Az értékek lekéréséhez forduljon a Freshservice ügyfélszolgálati [csapatához](https://support.freshservice.com/) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A Freshservice az SHA-256 ujjlenyomatot igényli az SSO működésének beolvasásához. Az SHA-256 ujjlenyomat beszerzéséhez hajtsa végre a következő lépéseket:

    ![Ujjlenyomat](./media/freshservice-tutorial/ic790821.png "ujjlenyomata")

    1. Nyissa meg a [hivatkozást](https://www.samltool.com/fingerprint.php) egy másik böngészőben.

    1. Nyissa meg a letöltött tanúsítvány (Base64) fájlt a Jegyzettömbben, és illessze be a tartalmat az **X. 509 tanúsítvány** szövegmezőbe.

    1. Az algoritmus esetében válassza a **sha256** lehetőséget a legördülő listából.

    1. Kattintson az **ujjlenyomat kiszámítása**elemre.

    1. A másolás ikonra kattintva másolja a generált **ujjlenyomatot** , és mentse a számítógépére.

1. A Azure Portal **Freshservice beállítása** szakaszban másolja ki amegfelelő URL-címeket a követelmény alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőbe írja be a username@companydomain.extension. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** elemre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Freshservice.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **Freshservice**lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-freshservice-sso"></a>Freshservice SSO konfigurálása

1. A Freshservice belüli konfiguráció automatizálásához telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése**lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **telepítés Freshservice** gombra a Freshservice alkalmazáshoz. Itt adja meg a rendszergazdai hitelesítő adatokat a Freshservice való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-6-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a Freshservice, nyisson meg egy új böngészőablakot, és jelentkezzen be a Freshservice vállalati webhelyére rendszergazdaként, és hajtsa végre a következő lépéseket:

4. A felső menüben kattintson a **rendszergazda**elemre.

    ![Rendszergazdai](./media/freshservice-tutorial/ic790814.png "rendszergazda")

5. A **Customer Portalon**kattintson a **Biztonság**elemre.

    ![Biztonsági](./media/freshservice-tutorial/ic790815.png "Biztonság")

6. A **Biztonság** szakaszban hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés](./media/freshservice-tutorial/ic790816.png "egyszeri") bejelentkezéssel

    a. **Egyszeri bejelentkezés**váltása.

    b. Válassza ki az **SAML SSO**elemet.

    c. Az **SAML bejelentkezési URL** szövegmezőbe illessze be a **bejelentkezési URL-címet**, amelyet a Azure Portalból másolt.

    d. A **kijelentkezési URL** szövegmezőbe illessze be a **KIJELENTKEZÉSI URL-cím**értékét, amelyet a Azure Portalból másolt.

    e. A **biztonsági tanúsítvány ujjlenyomata** szövegmezőbe illessze be a korábban létrehozott **ujjlenyomat** -értéket.

    f. Kattintson a **Mentés** gombra.

### <a name="create-freshservice-test-user"></a>Freshservice-tesztelési felhasználó létrehozása

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a FreshService, a FreshService kell kiépíteni őket. FreshService esetén a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a **FreshService** vállalati webhelyre rendszergazdaként.

2. A felső menüben kattintson a **rendszergazda**elemre.

    ![Rendszergazdai](./media/freshservice-tutorial/ic790814.png "rendszergazda")

3. A **felhasználói kezelés** szakaszban kattintson a **kérelmezők**elemre.

    ![Kérelmező](./media/freshservice-tutorial/ic790818.png "kérelmek")

4. Kattintson az **új kérelmező**elemre.

    ![Új]kérők új(./media/freshservice-tutorial/ic790819.png "kérők")

5. Az **új kérelmező** szakaszban hajtsa végre a következő lépéseket:

    ![Új]kérelmező új(./media/freshservice-tutorial/ic790820.png "kérelmezője")  

    a. Adja meg egy érvényes Azure Active Directory- **fiók vezetéknevét és** **e-mail-** attribútumait, amelyet szeretne a kapcsolódó szövegmezőbe beépíteni.

    b. Kattintson a **Save** (Mentés) gombra.

    > [!NOTE]
    > A Azure Active Directory fiók tulajdonosa egy e-mailt kap, amely tartalmazza a fiók megerősítését, mielőtt az aktívvá válna.
    >  

> [!NOTE]
> A FreshService által biztosított egyéb FreshService-létrehozási eszközöket vagy API-kat a HRE felhasználói fiókjainak kiépítésére használhatja.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Freshservice csempére kattint, automatikusan be kell jelentkeznie arra a Freshservice, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [A Freshservice kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)
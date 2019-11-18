---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a screencast-O-Matic szolgáltatással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a screencast-O-Matic között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 525ad47d-5488-40e2-aeaf-ae6183745682
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc887e95b6fa6f8b17fbbb3dbaae5105385a07fa
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132139"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-screencast-o-matic"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a screencast-O-Matictel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a screencast-O-Matict Azure Active Directory (Azure AD) használatával. A screencast-O-Matic és az Azure AD integrálásával a következőket teheti:

* A screencast-O-Matic eléréséhez hozzáférő Azure AD-vezérlő.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a screencast-O-Maticbe az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Screencast-O-Matic egyszeri bejelentkezés (SSO) engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* Screencast-O-Matic támogatja az **SP** által kezdeményezett SSO-t
* A screencast-O-Matic **csak időben támogatja a** felhasználók üzembe helyezését

## <a name="adding-screencast-o-matic-from-the-gallery"></a>Screencast-O-Matic hozzáadása a katalógusból

A screencast-O-Matic Azure AD-be való integrálásának konfigurálásához a gyűjteményből fel kell vennie a screencast-O-Matic elemet a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a keresőmezőbe a **screencast-O-Matic** kifejezést.
1. Válassza ki a **screencast-O-Matic** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-screencast-o-matic"></a>Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a screencast-O-Matic számára

Konfigurálja és tesztelje az Azure AD SSO-t a screencast-O-Matic használatával egy **B. Simon**nevű tesztelési felhasználó segítségével. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között a screencast-O-Matic használatával.

Az Azure AD SSO és a screencast-O-Matic konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[screencast-O-Matic SSO konfigurálása](#configure-screencast-o-matic-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    * **[Hozzon létre screencast-O-Matic teszt felhasználót](#create-screencast-o-matic-test-user)** – ha a felhasználó Azure ad-beli képviseletéhez csatolt B. Simon-partneri jogosultsággal rendelkezik a screencast-o-maticben.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **screencast-O-Matic** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    A **bejelentkezési URL** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://screencast-o-matic.com/<InstanceName>`

    > [!NOTE]
    > Az érték nem valódi. Frissítse az értéket a tényleges bejelentkezési URL-címmel. Az érték beszerzéséhez vegye fel a kapcsolatot a [screencast-O-Matic ügyfél-támogatási csapattal](mailto:support@screencast-o-matic.com) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

1. A **screencast-O-Matic beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza ki **új felhasználó** a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőbe írja be a username@companydomain.extension. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** elemre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést a screencast-O-Matic elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **screencast-O-Matic**elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-screencast-o-matic-sso"></a>A screencast-O-Matic SSO konfigurálása

1. A képernyőfelvétel-O-Matic konfigurációjának automatizálásához telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése**lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

1. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **screencast-o-matic beállítása** gombra a screencast-o-matic alkalmazáshoz. Itt adja meg a szükséges rendszergazdai hitelesítő adatokat a screencast-O-Maticbe való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-11-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

1. Ha a screencast-O-Matic-t manuálisan szeretné beállítani, nyisson meg egy új böngészőablakot, és jelentkezzen be a screencast-O-Matic vállalati webhelyre rendszergazdaként, és hajtsa végre a következő lépéseket:

1. Kattintson az **előfizetés**elemre.

    ![Az előfizetés](./media/screencast-tutorial/tutorial_screencast_sub.png)

1. A **hozzáférés lap** szakaszban kattintson a **telepítés**elemre.

    ![A hozzáférés](./media/screencast-tutorial/tutorial_screencast_setup.png)

1. A **telepítési hozzáférés oldalon**hajtsa végre a következő lépéseket.

1. A **hozzáférési URL** szakaszban adja meg a példánynév értéket a megadott szövegmezőben.

    ![A hozzáférés](./media/screencast-tutorial/tutorial_screencast_access.png)

1. Jelölje be a **tartományi felhasználó megkövetelése** az **SAML felhasználói korlátozás (nem kötelező)** szakaszban.

1. A **identitásszolgáltató metaadatait tartalmazó XML-fájl feltöltése**területen kattintson a **fájl kiválasztása** lehetőségre a Azure Portal letöltött metaadatok feltöltéséhez.

1. Kattintson az **OK** gombra.

    ![A hozzáférés](./media/screencast-tutorial/tutorial_screencast_save.png)

### <a name="create-screencast-o-matic-test-user"></a>Screencast-O-Matic tesztelési felhasználó létrehozása

Ebben a szakaszban a Britta Simon nevű felhasználó a screencast-O-Matic-ben jön létre. A screencast-O-Matic támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha a felhasználó még nem létezik a screencast-O-Maticben, a hitelesítés után létrejön egy új. Ha manuálisan kell létrehoznia egy felhasználót, forduljon a [screencast-O-Matic ügyfél-támogatási csapathoz](mailto:support@screencast-o-matic.com).

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Amikor a hozzáférési panelen a screencast-O-Matic csempére kattint, automatikusan be kell jelentkeznie a screencast-O-Matic-be, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [A screencast-O-Matic kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)
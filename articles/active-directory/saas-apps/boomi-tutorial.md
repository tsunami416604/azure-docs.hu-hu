---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Boomi | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Boomi között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 40d034ff-7394-4713-923d-1f8f2ed8bf36
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1044a78d9bf1b52a7b0dd9e47d7db6d98b45299c
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74081969"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-boomi"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Boomi

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Boomi a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az Boomi-t az Azure AD-vel, a következőket teheti:

* A Boomi-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Boomi az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Boomi egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Boomi támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést

## <a name="adding-boomi-from-the-gallery"></a>Boomi hozzáadása a gyűjteményből

A Boomi Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Boomi a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Boomi** kifejezést a keresőmezőbe.
1. Válassza ki a **Boomi** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-single-sign-on-for-boomi"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a Boomi

Konfigurálja és tesztelje az Azure AD SSO-t a Boomi a **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Boomi-ben.

Az Azure AD SSO és a Boomi konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[BOOMI SSO konfigurálása](#configure-boomi-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    * **[Hozzon létre Boomi-teszt felhasználót](#create-boomi-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-Boomi rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **Boomi** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be a következő URL-címet: `https://platform.boomi.com/`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://platform.boomi.com/sso/<boomi-tenant>/saml`

    > [!NOTE]
    > A válasz URL-cím értéke nem valódi. Frissítse az értéket a tényleges válasz URL-címével. Az érték beszerzéséhez lépjen kapcsolatba a [Boomi](https://boomi.com/company/contact/) ügyfélszolgálatával. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. A Boomi alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentiek mellett a Boomi alkalmazás néhány további attribútumot vár az SAML-válaszban, amelyek alább láthatók. Ezek az attribútumok előre fel vannak töltve, de a követelményeinek megfelelően áttekintheti őket.

    | Name (Név) |  Forrás attribútum|
    | ---------------|  --------- |
    | FEDERATION_ID | user.mail |

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

1. A **Boomi beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Boomi.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **Boomi**lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-boomi-sso"></a>Boomi SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Boomi vállalati webhelyre rendszergazdaként.

1. Lépjen a **vállalat nevére** , és válassza a **beállítás**lehetőséget.

1. Kattintson az **egyszeri bejelentkezés beállításai** lapra, és hajtsa végre az alábbi lépéseket.

    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalán](./media/boomi-tutorial/tutorial_boomi_11.png)

    a. Jelölje be **az SAML egyszeri bejelentkezés engedélyezése** jelölőnégyzetet.

    b. Az **Importálás** gombra kattintva töltse fel a letöltött tanúsítványt az Azure ad-ből az **identitás-szolgáltatói tanúsítványba**.

    c. Az **Identity Provider bejelentkezési URL-címe** szövegmezőbe helyezze a **bejelentkezési URL-cím** értéket az Azure ad-alkalmazás konfigurációs ablakából.

    d. Az **összevonási azonosító helye**területen válassza az **összevonási azonosító FEDERATION_ID attribútum elem** választógombot.

    e. Kattintson a **mentése** gombra.

### <a name="create-boomi-test-user"></a>Boomi-tesztelési felhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezzenek a Boomi, a Boomi-ben kell kiépíteni őket. Boomi esetén a kiépítés manuális feladat.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:

1. Jelentkezzen be a Boomi vállalati webhelyre rendszergazdaként.

1. Bejelentkezés után navigáljon a felhasználók **felügyeletéhez** , és nyissa meg a **felhasználókat**.

    ![Felhasználók](./media/boomi-tutorial/tutorial_boomi_001.png "Felhasználók")

1. Kattintson **+** ikonra, és megnyílik a **felhasználói szerepkörök hozzáadása/karbantartása** párbeszédpanel.

    ![Felhasználók](./media/boomi-tutorial/tutorial_boomi_002.png "Felhasználók")

    ![Felhasználók](./media/boomi-tutorial/tutorial_boomi_003.png "Felhasználók")

    a. A **felhasználó e-mail címe** szövegmezőbe írja be a (z) B.Simon@contoso.comfelhasználó e-mail-címét.

    b. Az **Utónév** szövegmezőbe írja be a (z) "B" nevű felhasználó utónevét.

    c. A **vezetékneve** szövegmezőbe írja be a felhasználó vezetéknevét, például Simon nevet.

    d. Adja meg a felhasználó **ÖSSZEVONÁSI azonosítóját**. Minden felhasználónak rendelkeznie kell egy olyan összevonási AZONOSÍTÓval, amely egyedileg azonosítja a felhasználót a fiókon belül.

    e. Rendelje hozzá az **általános jogú felhasználói** szerepkört a felhasználóhoz. Ne rendelje hozzá a rendszergazdai szerepkört, mert az lehetővé tenné számukra a normál környezet elérését, valamint az egyszeri bejelentkezéshez való hozzáférést.

    f. Kattintson az **OK** gombra.

    > [!NOTE]
    > A felhasználó nem kap olyan üdvözlő értesítő e-mailt, amely tartalmaz egy jelszót, amely a AtomSphere-fiókba való bejelentkezéshez használható, mert a jelszót az identitás-szolgáltató kezeli. A Boomi által biztosított egyéb Boomi-létrehozási eszközöket vagy API-kat a HRE felhasználói fiókjainak kiépítéséhez használhatja.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Boomi csempére kattint, automatikusan be kell jelentkeznie arra a Boomi, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [A Boomi kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)
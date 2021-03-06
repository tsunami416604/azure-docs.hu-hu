---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Boomi | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Boomi között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/07/2020
ms.author: jeedes
ms.openlocfilehash: e14ef0c039fdf07d50c09fe57dc3cac222be524d
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2020
ms.locfileid: "92456874"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-boomi"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Boomi

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Boomi a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az Boomi-t az Azure AD-vel, a következőket teheti:

* A Boomi-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Boomi az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Boomi egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Boomi támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést
* A Boomi konfigurálása után kikényszerítheti a munkamenet-vezérlőket, amelyek valós időben védik a szervezet bizalmas adatainak kiszűrése és beszivárgását. A munkamenet-vezérlőelemek kiterjeszthetők a feltételes hozzáférésből. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-boomi-from-the-gallery"></a>Boomi hozzáadása a gyűjteményből

A Boomi Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Boomi a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
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

1. Az **alapszintű SAML-konfiguráció** szakaszban, ha **szolgáltatói metaadatokat tartalmazó fájllal** rendelkezik, és **identitásszolgáltató** kezdeményezett módban kívánja konfigurálni, hajtsa végre a következő lépéseket:

    a. Kattintson a **metaadat-fájl feltöltése**elemre.

    ![Metaadat-fájl feltöltése](common/upload-metadata.png)

    b. Kattintson a **mappa emblémára** a metaadat-fájl kiválasztásához, majd kattintson a **feltöltés**elemre.

    ![metaadat-fájl kiválasztása](common/browse-upload-metadata.png)

    c. A metaadat-fájl feltöltése után a rendszer az alapszintű SAML-konfiguráció szakaszban automatikusan feltölti az **azonosítót** és a **Válasz URL-** értékeket.

    ![A képernyőfelvételen az alapszintű SAML-konfiguráció látható, ahol az azonosító és a válasz U R L érték jelenik meg.](common/idp-intiated.png)

    d. Adja meg a **bejelentkezési URL-címet**, például: `https://platform.boomi.com/AtomSphere.html#build;accountId={your-accountId}` .

    > [!Note]
    > A **szolgáltatói metaadat-fájlt** a **Boomi SSO konfigurálása** szakaszban találja, amelyet az oktatóanyag későbbi részében ismertetünk. Ha az **azonosító** és a **Válasz URL-címe** nem kap automatikus polulated, akkor a követelménynek megfelelően adja meg manuálisan az értékeket.

1. A Boomi alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![Képernyőfelvétel: felhasználói attribútumok & alapértelmezett értékekkel rendelkező jogcímek, például givenName user. givenName és EmailAddress user. mail.](common/default-attributes.png)

1. A fentiek mellett a Boomi alkalmazás néhány további attribútumot vár az SAML-válaszban, amelyek alább láthatók. Ezek az attribútumok előre fel vannak töltve, de a követelményeinek megfelelően áttekintheti őket.

    | Name |  Forrás attribútum|
    | ---------------|  --------- |
    | FEDERATION_ID | User. mail |

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Boomi beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Boomi.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **Boomi**lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-boomi-sso"></a>Boomi SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Boomi vállalati webhelyre rendszergazdaként.

1. Lépjen a **vállalat nevére** , és válassza a **beállítás**lehetőséget.

1. Kattintson az **egyszeri bejelentkezés beállításai** lapra, és hajtsa végre az alábbi lépéseket.

    ![Egyetlen Sign-On konfigurálása az alkalmazás oldalán](./media/boomi-tutorial/tutorial_boomi_11.png)

    a. Jelölje be **az SAML egyszeri bejelentkezés engedélyezése** jelölőnégyzetet.

    b. Az **Importálás** gombra kattintva töltse fel a letöltött tanúsítványt az Azure ad-ből az **identitás-szolgáltatói tanúsítványba**.

    c. Az **Identity Provider bejelentkezési URL-címe** szövegmezőbe helyezze a **bejelentkezési URL-cím** értéket az Azure ad-alkalmazás konfigurációs ablakából.

    d. Az **összevonási azonosító helye**területen jelölje be az **összevonási azonosító a FEDERATION_ID attribútum elem** választógombot.

    e. Másolja a **AtomSphere metaadat URL-címét**, lépjen a **metaadatok URL-címére** az Ön által választott böngészőn keresztül, és mentse a kimenetet egy fájlba. Töltse fel a **metaadatok URL-címét** a Azure Portal **ALAPszintű SAML-konfiguráció** szakaszában.

    f. Kattintson a **Save (Mentés** ) gombra.

### <a name="create-boomi-test-user"></a>Boomi-tesztelési felhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezzenek a Boomi, a Boomi-ben kell kiépíteni őket. Boomi esetén a kiépítés manuális feladat.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:

1. Jelentkezzen be a Boomi vállalati webhelyre rendszergazdaként.

1. Bejelentkezés után navigáljon a felhasználók **felügyeletéhez** , és nyissa meg a **felhasználókat**.

    ![Képernyőfelvétel: a felhasználó-felügyeleti oldal, amelyen a felhasználók ki vannak választva.](./media/boomi-tutorial/tutorial_boomi_001.png "Felhasználók")

1. Kattintson **+**  a ikon gombra, és megnyílik a **felhasználói szerepkörök hozzáadása/karbantartása** párbeszédpanel.

    ![Képernyőfelvétel: a kiválasztott + ikon.](./media/boomi-tutorial/tutorial_boomi_002.png "Felhasználók")

    ![A képernyőfelvételen a felhasználói szerepkörök hozzáadása és karbantartása látható, ahol a felhasználó konfigurálható.](./media/boomi-tutorial/tutorial_boomi_003.png "Felhasználók")

    a. A **felhasználó e-mail címe** szövegmezőbe írja be a felhasználóhoz hasonló e-mail címet B.Simon@contoso.com .

    b. Az **Utónév** szövegmezőbe írja be a (z) "B" nevű felhasználó utónevét.

    c. A **vezetékneve** szövegmezőbe írja be a felhasználó vezetéknevét, például Simon nevet.

    d. Adja meg a felhasználó **ÖSSZEVONÁSI azonosítóját**. Minden felhasználónak rendelkeznie kell egy olyan összevonási AZONOSÍTÓval, amely egyedileg azonosítja a felhasználót a fiókon belül.

    e. Rendelje hozzá az **általános jogú felhasználói** szerepkört a felhasználóhoz. Ne rendelje hozzá a rendszergazdai szerepkört, mert az lehetővé tenné számukra a normál környezet elérését, valamint az egyszeri bejelentkezéshez való hozzáférést.

    f. Kattintson az **OK** gombra.

    > [!NOTE]
    > A felhasználó nem kap olyan üdvözlő értesítő e-mailt, amely tartalmaz egy jelszót, amely a AtomSphere-fiókba való bejelentkezéshez használható, mert a jelszót az identitás-szolgáltató kezeli. A Boomi által biztosított egyéb Boomi-létrehozási eszközöket vagy API-kat a HRE felhasználói fiókjainak kiépítéséhez használhatja.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Boomi csempére kattint, automatikusan be kell jelentkeznie arra a Boomi, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](/cloud-app-security/proxy-intro-aad)

- [A Boomi kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)
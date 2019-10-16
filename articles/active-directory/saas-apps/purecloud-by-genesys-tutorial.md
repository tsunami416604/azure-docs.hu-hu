---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a PureCloud-mel a Genesys használatával | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést az Azure Active Directory és a PureCloud között a Genesys használatával.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e16a46db-5de2-4681-b7e0-94c670e3e54e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfaa5a4ebb8bc633dc49db08698aec31de9436d4
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72373119"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-purecloud-by-genesys"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a PureCloud-mel a Genesys használatával

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a PureCloud a Genesys segítségével a Azure Active Directory (Azure AD) használatával. Ha a Genesys és az Azure AD segítségével integrálja a PureCloud-et, a következőket teheti:

* Vezérlés az Azure AD-ben, aki a Genesys által PureCloud fér hozzá.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a PureCloud az Azure AD-fiókjával való Genesys.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* PureCloud az Genesys egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Genesys által támogatott PureCloud az **SP és a identitásszolgáltató** által kezdeményezett SSO-t támogatja

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egyetlen bérlőn.

## <a name="adding-purecloud-by-genesys-from-the-gallery"></a>PureCloud hozzáadása a Genesys-ből a katalógusból

Ahhoz, hogy az Azure AD-be Genesys az PureCloud-integrációt, hozzá kell adnia a PureCloud a Genesys a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **PureCloud by Genesys** kifejezést a keresőmezőbe.
1. Válassza ki a **PureCloud az Genesys** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-purecloud-by-genesys"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a Genesys-PureCloud

Konfigurálja és tesztelje az Azure AD SSO-t a PureCloud-mel a **B. Simon**nevű Genesys használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a PureCloud által a Genesys-ben.

Az Azure AD SSO konfigurálásához és a Genesys-mel történő PureCloud való teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[PureCloud konfigurálása a GENESYS SSO használatával](#configure-purecloud-by-genesys-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre PureCloud a Genesys test User használatával](#create-purecloud-by-genesys-test-user)** – ha a PureCloud által használt "B. Simon" jogosultsággal rendelkezik a felhasználó Azure ad-képviseletéhez kapcsolódó Genesys.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **PureCloud a Genesys** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az **ALAPszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be a régiójának megfelelő URL-címet:

    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.au/saml` |

    b. A **Válasz URL-címe** szövegmezőbe írja be a régiójának megfelelő URL-címet:

    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.com.au/saml`|

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be a régiójának megfelelő URL-címet:
    
    | |
    |--|
    | `https://login.mypurecloud.com` |
    | `https://login.mypurecloud.de` |
    | `https://login.mypurecloud.jp` |
    | `https://login.mypurecloud.ie` |
    | `https://login.mypurecloud.com.au` |

1. A Genesys-alkalmazás által használt PureCloud egy adott formátumban várja az SAML-jogcímeket, így egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentiek mellett a Genesys alkalmazás PureCloud néhány további attribútumot is vár az SAML-válaszokban, amelyek alább láthatók. Ezek az attribútumok előre fel vannak töltve, de a követelményeinek megfelelően áttekintheti őket.

    | Név | Forrás attribútum|
    | ---------------| --------------- |
    | E-mail cím | User. userprinicipalname |
    | Szervezetnév | `Your organization name` |

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **PureCloud által Genesys beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőbe írja be a username@companydomain.extension értéket. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a  **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a PureCloud számára a Genesys.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **PureCloud a Genesys alapján**lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-purecloud-by-genesys-sso"></a>PureCloud konfigurálása Genesys SSO-val

1. Egy másik böngészőablakban jelentkezzen be a PureCloud Genesys rendszergazdaként.

1. Kattintson a **rendszergazda** elemre a felső részen, és az **integrációk**területen navigáljon az **egyszeri bejelentkezés** lehetőségre.

    ![Egyszeri bejelentkezés konfigurálása](./media/purecloud-by-genesys-tutorial/configure01.png)

1. Váltson az **ADFS/Azure ad (prémium)** lapra, és hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/purecloud-by-genesys-tutorial/configure02.png)

    a. Kattintson a **Tallózás** gombra, és töltse fel a Azure Portalból letöltött base-64 kódolt tanúsítványt az **ADFS-tanúsítványba**.

    b. Az **ADFS kiállítói URI** szövegmezőbe illessze be a Azure Portalból másolt **Azure ad-azonosító** értékét.

    c. A **cél URI** szövegmezőbe illessze be a **bejelentkezési URL-cím** értékét, amelyet a Azure Portal másolt.

    d. **Függő entitás azonosító** értékének megkezdéséhez lépjen a Azure Portal **PureCloud az Genesys** alkalmazás-integráció lapján, kattintson a **Tulajdonságok** fülre, és másolja az **alkalmazás-azonosító** értékét. Illessze be a **függő entitás azonosítójának** szövegmezőbe. 

    ![Egyszeri bejelentkezés konfigurálása](./media/purecloud-by-genesys-tutorial/configure06.png)

    e. Kattintson a **Mentés** gombra.

### <a name="create-purecloud-by-genesys-test-user"></a>PureCloud létrehozása a Genesys test User használatával

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a PureCloud a Genesys-be, azokat a Genesys PureCloud kell kiépíteni. A Genesys által PureCloud a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a PureCloud a Genesys rendszergazdaként.

1. Kattintson a felső **rendszergazda** lehetőségre, és navigáljon a személyek **& engedélyek** **területen.**

    ![Egyszeri bejelentkezés konfigurálása](./media/purecloud-by-genesys-tutorial/configure03.png)

1. A személyek lapon kattintson a **személy felvétele**elemre.

    ![Egyszeri bejelentkezés konfigurálása](./media/purecloud-by-genesys-tutorial/configure04.png)

1. A **személyek hozzáadása a szervezethez** előugró ablakban végezze el a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/purecloud-by-genesys-tutorial/configure05.png)

    a. A **teljes név** szövegmezőbe írja be a felhasználó nevét, például: **B. Simon**.

    b. Az **e-mail** szövege szövegmezőbe írja be a **b.Simon\@contoso.com**(például a következőt) felhasználó e-mail címét.

    c. Kattintson a  **Create** (Létrehozás) gombra.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a PureCloud by Genesys csempe elemre kattint, automatikusan be kell jelentkeznie a PureCloud olyan Genesys, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a Genesys által PureCloud az Azure AD-vel](https://aad.portal.azure.com/)
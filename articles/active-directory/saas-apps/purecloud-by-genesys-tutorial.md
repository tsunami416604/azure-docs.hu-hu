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
ms.openlocfilehash: 779328f4c21afb4392663e6f8840749ea505c529
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2019
ms.locfileid: "73242440"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-purecloud-by-genesys"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a PureCloud-mel a Genesys használatával

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a PureCloud a Genesys segítségével a Azure Active Directory (Azure AD) használatával. Ezt követően a következőket teheti:

* Az Azure AD segítségével szabályozhatja, hogy mely felhasználók férhetnek hozzá a PureCloud a Genesys.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a PureCloud az Azure AD-fiókjával való Genesys.
* A fiókokat egyetlen központi helyen kezelheti: a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik ilyennel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* PureCloud Genesys egyszeri bejelentkezéssel (SSO) – engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Genesys által támogatott PureCloud az **SP és a identitásszolgáltató**– kezdeményezett SSO-t támogatja.

> [!NOTE]
> Mivel az alkalmazás azonosítója rögzített sztring értékű, csak egy példány konfigurálható egyetlen bérlőn.

## <a name="adding-purecloud-by-genesys-from-the-gallery"></a>PureCloud hozzáadása a Genesys-ből a katalógusból

Ahhoz, hogy az Azure AD-be Genesys az PureCloud-integrációt, hozzá kell adnia a PureCloud a Genesys a katalógusból a felügyelt SaaS-alkalmazások listájához. Ehhez kövesse az alábbi lépéseket:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **PureCloud by Genesys** kifejezést a keresőmezőbe.
1. Az eredmények panelen válassza a **PureCloud Genesys alapján** lehetőséget, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-purecloud-by-genesys"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a Genesys-PureCloud

Konfigurálja és tesztelje az Azure AD SSO-t a PureCloud-mel a Genesys használatával egy **B. Simon**nevű teszt felhasználó segítségével. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között a PureCloud által a Genesys.

Az Azure AD SSO konfigurálásához és a Genesys-mel történő PureCloud való teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure ad SSO](#configure-azure-ad-sso)** -t, hogy a felhasználók használhatják ezt a funkciót.
    1. **[Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user)** az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** , hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[Konfigurálja a PureCloud a GENESYS SSO használatával](#configure-purecloud-by-genesys-sso)** az alkalmazás oldalán lévő egyszeri bejelentkezés beállításainak konfigurálásához.
    1. **[Hozzon létre egy PureCloud a Genesys-teszt felhasználója](#create-purecloud-by-genesys-test-user)** számára, hogy az a felhasználó Azure ad-beli képviseletéhez kapcsolódó, B. Simon PureCloud.
1. Ellenőrizze az **[SSO](#test-sso)** -t annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Ha engedélyezni szeretné az Azure AD SSO használatát a Azure Portalban, kövesse az alábbi lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **PureCloud a Genesys** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon válassza az **alapszintű SAML-konfigurációhoz** tartozó toll ikont a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha az **alapszintű SAML-konfiguráció** szakaszban szeretné konfigurálni az alkalmazást **identitásszolgáltató**módban, adja meg a következő mezők értékeit:

    a. Az **azonosító** mezőben adja meg a régiójának megfelelő URL-címet:

    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.au/saml` |

    b. A **Válasz URL-címe** mezőbe írja be a régiójának megfelelő URL-címet:

    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.com.au/saml`|

1. Válassza a **további URL-címek beállítása** lehetőséget, majd a következő lépéssel konfigurálja az alkalmazást **SP** -ben kezdeményezett módban:

    A **bejelentkezési URL-cím** mezőbe írja be a régiójának megfelelő URL-címet:
    
    | |
    |--|
    | `https://login.mypurecloud.com` |
    | `https://login.mypurecloud.de` |
    | `https://login.mypurecloud.jp` |
    | `https://login.mypurecloud.ie` |
    | `https://login.mypurecloud.com.au` |

1. A Genesys-alkalmazás által használt PureCloud egy adott formátumban várja az SAML-jogcímeket, így egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőfelvétel az alapértelmezett attribútumok listáját jeleníti meg:

    ![image](common/default-attributes.png)

1. Emellett a Genesys alkalmazás által készített PureCloud néhány további attribútumot vár az SAML-válaszban, ahogy az a következő táblázatban látható. Ezek az attribútumok előre is fel vannak töltve, de szükség szerint áttekinthetők.

    | Név | Forrás attribútum|
    | ---------------| --------------- |
    | E-mail cím | User. userprinicipalname |
    | Szervezetnév | `Your organization name` |

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **PureCloud által Genesys beállítása** szakaszban másolja a megfelelő URL-címet (vagy URL-címeket) a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy B. Simon nevű teszt felhasználót hoz létre a Azure Portalban:

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőbe írja be a felhasználónevet a következő formátumban: username@companydomain.extension. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd jegyezze fel a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon-t fogja beállítani az Azure egyszeri bejelentkezés használatára azáltal, hogy hozzáférést biztosít a PureCloud számára a Genesys.

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

1. Válassza a felső **rendszergazda** lehetőséget, majd az **integrációk**területen lépjen az **egyszeri bejelentkezés** lehetőségre.

    ![Egyszeri bejelentkezés konfigurálása](./media/purecloud-by-genesys-tutorial/configure01.png)

1. Váltson az **ADFS/Azure ad (prémium)** lapra, majd kövesse az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/purecloud-by-genesys-tutorial/configure02.png)

    a. A **Tallózás** gombra kattintva töltse fel a Azure Portalból letöltött base-64 kódolt tanúsítványt az **ADFS-tanúsítványba**.

    b. Az **ADFS kiállítói URI-ja** mezőben illessze be a Azure Portalból másolt **Azure ad-azonosító** értékét.

    c. A **cél URI** mezőben illessze be a Azure Portalból másolt **bejelentkezési URL-cím** értékét.

    d. A **függő entitás azonosítójának** értékeként nyissa meg a Azure Portal, majd az **PureCloud by Genesys** Application Integration lapon válassza a **Tulajdonságok** lapot, és másolja az **alkalmazás-azonosító** értékét. Illessze be a **függő entitás azonosító** mezőjébe.

    ![Egyszeri bejelentkezés konfigurálása](./media/purecloud-by-genesys-tutorial/configure06.png)

    e. Kattintson a **Mentés** gombra.

### <a name="create-purecloud-by-genesys-test-user"></a>PureCloud létrehozása a Genesys test User használatával

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a PureCloud a Genesys-be, azokat a Genesys PureCloud kell kiépíteni. A Genesys által PureCloud a kiépítés manuális feladat.

**Felhasználói fiók kiépítéséhez kövesse az alábbi lépéseket:**

1. Jelentkezzen be a PureCloud a Genesys rendszergazdaként.

1. Válassza a **rendszergazda** lehetőséget a felső részen, és lépjen a személyek személyek **& engedélyek** **területen.**

    ![Egyszeri bejelentkezés konfigurálása](./media/purecloud-by-genesys-tutorial/configure03.png)

1. A **személyek** lapon válassza a **személy felvétele**lehetőséget.

    ![Egyszeri bejelentkezés konfigurálása](./media/purecloud-by-genesys-tutorial/configure04.png)

1. A **személyek hozzáadása a szervezethez** párbeszédpanelen kövesse az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/purecloud-by-genesys-tutorial/configure05.png)

    a. A **teljes név** mezőben adja meg a felhasználó nevét. Például: **B. Simon**.

    b. Az **e-mail** mezőbe írja be a felhasználó e-mail-címét. Például: **b. simon\@contoso.com**.

    c. Kattintson a **Létrehozás** gombra.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen kiválasztja a **PureCloud by Genesys** csempét, akkor automatikusan be kell jelentkeznie a PureCloud Genesys-fiókkal, amelyet az egyszeri bejelentkezés beállításához állított be. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure AD-vel való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure AD-vel?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure AD-ben?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a Genesys által PureCloud az Azure AD-vel](https://aad.portal.azure.com/)
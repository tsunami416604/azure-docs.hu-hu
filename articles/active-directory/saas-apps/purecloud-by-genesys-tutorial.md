---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a PureClouddal a Genesys által | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Genesys PureCloud szolgáltatása között.
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
ms.openlocfilehash: dac8e0f2e10906f2cc56ecf86e0cc70947cb7e85
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78897784"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-purecloud-by-genesys"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a PureClouddal a Genesys által

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a PureCloud by Genesys alkalmazást az Azure Active Directoryval (Azure AD). Ezt követően a következőket teheti:

* Az Azure AD használatával szabályozhatja, hogy mely felhasználók férhetnek hozzá a Genesys purecloudjához.
* Lehetővé teszi, hogy a genesys automatikusan bejelentkezhet a PureCloudba az Azure AD-fiókjaikkal.
* Kezelje fiókjait egyetlen központi helyen: az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik ilyen, akkor kap egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* PureCloud by Genesys egyszeri bejelentkezés (SSO)–kompatibilis előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* A PureCloud by Genesys támogatja az **SP-t és az IDP-t**–kezdeményezett sso.PureCloud by Genesys supports SP and IDP-initiated SSO.

> [!NOTE]
> Mivel az alkalmazás azonosítója rögzített karakterláncú érték, csak egy példány konfigurálható egy bérlőben.

## <a name="adding-purecloud-by-genesys-from-the-gallery"></a>PureCloud hozzáadása a Genesys-től a galériából

A PureCloud Genesys által az Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a PureCloud by Genesys-t a katalógusból a felügyelt SaaS-alkalmazások listájához. Ehhez kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások lehetőséget,** és válassza **az Összes alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **katalógus hozzáadásszakaszában** írja be a **PureCloud by Genesys kifejezést** a keresőmezőbe.
1. Válassza ki a **PureCloud by Genesys elemet** az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-purecloud-by-genesys"></a>Az Azure AD egyszeri bejelentkezésének konfigurálása és tesztelése a Genesys által a PureCloud számára

Konfigurálja és tesztelje az Azure AD SSO-t a Genesys PureCloud szolgáltatásával egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó a PureCloud a Genesys.

Az Azure AD SSO konfigurálásához és teszteléséhez a Genesys PureCloud szolgáltatásával hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD SSO-t,](#configure-azure-ad-sso)** hogy a felhasználók használhassák ezt a funkciót.
    1. **[Hozzon létre egy Azure AD-tesztfelhasználót](#create-an-azure-ad-test-user)** az Azure AD egyszeri bejelentkezésének teszteléséhez B.Simon nal.
    1. **[Rendelje hozzá az Azure AD tesztfelhasználót,](#assign-the-azure-ad-test-user)** hogy b.Simon az Azure AD egyszeri bejelentkezést használhasson.
1. **[Konfigurálja a PureCloud by Genesys Egyszeri bejelentkezést](#configure-purecloud-by-genesys-sso)** az alkalmazás oldalon az egyszeri bejelentkezési beállítások konfigurálásához.
    1. **[Hozzon létre egy PureCloud a Genesys teszt felhasználó,](#create-purecloud-by-genesys-test-user)** hogy egy megfelelője B.Simon a PureCloud a Genesys, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[Tesztelje az SSO-t,](#test-sso)** hogy ellenőrizze, működik-e a konfiguráció.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az Azure AD-sSO engedélyezéséhez az Azure Portalon kövesse az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **PureCloud by Genesys alkalmazásintegrációs** lapon keresse meg a **Kezelés szakaszt,** és válassza ki **az egyszeri bejelentkezést.**
1. Az **Egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon válassza az **egyszerű SAML-konfiguráció** tollikonját a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban, ha az alkalmazást **IDP**által kezdeményezett módban szeretné konfigurálni, adja meg a következő mezők értékeit:

    a. Az **Azonosító** mezőbe írjon be egy, a régiónak megfelelő URL-címet:

    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.au/saml` |

    b. A **Válasz URL-címe** mezőbe írjon be egy, a régiónak megfelelő URL-címet:

    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.com.au/saml`|

1. Válassza **a További URL-címek beállítása** lehetőséget, és tegye a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban szeretné konfigurálni:

    A **Bejelentkezési URL-cím** mezőbe írjon be egy, a régiónak megfelelő URL-címet:
    
    | |
    |--|
    | `https://login.mypurecloud.com` |
    | `https://login.mypurecloud.de` |
    | `https://login.mypurecloud.jp` |
    | `https://login.mypurecloud.ie` |
    | `https://login.mypurecloud.com.au` |

1. A PureCloud by Genesys alkalmazás az SAML-állításokat egy adott formátumban várja, amely megköveteli, hogy egyéni attribútumleképezéseket adjon hozzá az SAML token attribútumok konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható:

    ![image](common/default-attributes.png)

1. Emellett a PureCloud by Genesys alkalmazás azt várja, hogy még néhány attribútum ot visszaadjon az SAML válaszban, ahogy az az alábbi táblázatban látható. Ezek az attribútumok is előre kitöltött, de szükség szerint áttekintheti őket.

    | Név | Forrás attribútum|
    | ---------------| --------------- |
    | E-mail | user.userprincipalname |
    | OrganizationName | `Your organization name` |

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon keresse meg az **SAML aláíró tanúsítvány szakaszát,** keresse meg a **Tanúsítvány (Base64)** lehetőséget, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **PureCloud beállítása a Genesys** által szakaszban másolja a megfelelő URL-t (vagy URL-eket) az Ön igényei nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy B.Simon nevű tesztfelhasználót hoz létre az Azure Portalon:

1. Az Azure Portal bal oldali ablaktáblájában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd az **Összes felhasználó**lehetőséget.
1. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.
1. A **Felhasználói** tulajdonságok csoportban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőbe írja be a felhasználónevet username@companydomain.extensiona következő formátumban: . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet, majd jegyezze fel a **Jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban beállítja a B.Simon t az Azure egyszeri bejelentkezéshez, a Genesys által a PureCloudhoz való hozzáférést biztosítva.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **PureCloud by Genesys lehetőséget.**
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** **párbeszédpanelen** a Felhasználók és csoportok lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a Felhasználók listában a **B.Simon** elemet, majd a képernyő alján található **Kijelölés** gomb lehetőséget.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-purecloud-by-genesys-sso"></a>A PureCloud konfigurálása a Genesys SSO-val

1. Egy másik böngészőablakban jelentkezzen be a Genesys PureCloud szolgáltatásába rendszergazdaként.

1. Válassza a **Rendszergazda** lehetőséget a lap tetején, majd az **Integrációk**csoportban **válassza az Egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezés konfigurálása](./media/purecloud-by-genesys-tutorial/configure01.png)

1. Váltson az **ADFS/Azure AD(Premium)** fülre, majd kövesse az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/purecloud-by-genesys-tutorial/configure02.png)

    a. Válassza a **Tallózás** gombot az Azure Portalról letöltött base-64 kódolású tanúsítvány **ADFS-tanúsítványba**való feltöltéséhez.

    b. Az **ADFS-kiállító URI-mezőjébe** illessze be az Azure Portalról másolt **Azure AD-azonosító** értékét.

    c. A **Cél URI** mezőbe illessze be az Azure Portalról másolt **bejelentkezési URL-cím** értékét.

    d. A **függő entitásazonosító** értékért nyissa meg az Azure Portalt, majd a **PureCloud by Genesys alkalmazásintegrációs** lapon válassza a **Tulajdonságok** lapot, és másolja az **alkalmazásazonosító** értékét. Illessze be a **Függő entitás azonosító mezőjébe.**

    ![Egyszeri bejelentkezés konfigurálása](./media/purecloud-by-genesys-tutorial/configure06.png)

    e. Kattintson a **Mentés** gombra.

### <a name="create-purecloud-by-genesys-test-user"></a>PureCloud létrehozása a Genesys tesztfelhasználójával

Ahhoz, hogy az Azure AD-felhasználók bejelentkezhessenek a Genesys purecloudba, a Genesys-nek ki kell építenie őket a PureCloudba. A Genesys PureCloud ban a kiépítés manuális feladat.

**Felhasználói fiók kiépítéséhez kövesse az alábbi lépéseket:**

1. Jelentkezzen be a PureCloudba a Genesys által rendszergazdaként.

1. Válassza a **Rendszergazda** lehetőséget a lap tetején, és nyissa meg a **Személyek** **& engedélyek csoportjában.**

    ![Egyszeri bejelentkezés konfigurálása](./media/purecloud-by-genesys-tutorial/configure03.png)

1. A **Személyek** lapon válassza a **Személy hozzáadása lehetőséget.**

    ![Egyszeri bejelentkezés konfigurálása](./media/purecloud-by-genesys-tutorial/configure04.png)

1. A **Személyek hozzáadása a szervezethez** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/purecloud-by-genesys-tutorial/configure05.png)

    a. A **Teljes név** mezőbe írja be a felhasználó nevét. Például: **B.simon**.

    b. Az **E-mail** mezőbe írja be a felhasználó e-mail címét. Például: **b.simon\@contoso.com**.

    c. Kattintson a **Létrehozás** gombra.

## <a name="test-sso"></a>SSO tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelhasználatával teszteli.

Amikor kiválasztja a **PureCloud by Genesys csempét** a Hozzáférési panelen, akkor automatikusan be kell jelentkeznie a PureCloudba a Genesys-fiókban, amelyhez sso-t állított be. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure AD-vel való integrálásáról szóló oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure AD-vel?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure AD-ben?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a PureCloud by Genesys-t az Azure AD-vel](https://aad.portal.azure.com/)

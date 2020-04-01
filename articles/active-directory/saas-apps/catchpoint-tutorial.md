---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Catchpointtal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Catchpoint között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ab3eead7-8eb2-4c12-bb3a-0e46ec899d37
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c56d34a331821ffbc3d0d4f2cf5e9b033f4011ff
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78968492"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-catchpoint"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Catchpointtal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Catchpointot az Azure Active Directoryval (Azure AD). Ha integrálja a Catchpointot az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Catchpointhoz.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve catchpoint az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Catchpoint egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* A Catchpoint támogatja az **SP és az IDP** által kezdeményezett sso-t
* A Catchpoint támogatja **a Just In Time** felhasználói kiépítést
* A Catchpoint konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlő a feltételes hozzáféréstől terjed. [Megtudhatja, hogy miként kényszerítheti ki a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)

## <a name="adding-catchpoint-from-the-gallery"></a>Catchpoint hozzáadása a gyűjteményből

A Catchpoint Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Catchpoint-ot a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **Hozzáadás a gyűjteményből szakaszban** írja be a **Catchpoint** kifejezést a keresőmezőbe.
1. Válassza **a Catchpoint** elemet az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-catchpoint"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése a Catchpoint ban

Konfigurálja és tesztelje az Azure AD SSO-t a Catchpoint segítségével egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó catchpoint.

Az Azure AD SSO catchpoint-tal való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    * **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    * **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Catchpoint Egyszeri bejelentkezés konfigurálása](#configure-catchpoint-sso)** – az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
    * **[Catchpoint tesztfelhasználó létrehozása](#create-catchpoint-test-user)** – b.Simon megfelelőjének a Catchpointban, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Catchpoint** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A beállítások szerkesztéséhez kattintson az **SAML-konfigurációval** rendelkező egyszeri bejelentkezés beállítása lapon az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha az alkalmazást **IDP** által kezdeményezett módban kívánja konfigurálni, adja meg a következő mezők értékeit:

    a. Az **Azonosító** mezőbe írja be az URL-címet:`https://portal.catchpoint.com/SAML2`

    b. A **Válasz URL-cím** mezőjébe írja be az URL-címet:`https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`

1. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    A **Bejelentkezési URL-cím** mezőbe írja be az URL-címet:`https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`

1. Catchpoint alkalmazás elvárja, hogy az SAML állításokat egy adott formátumban, amely megköveteli, hogy egyéni attribútum-hozzárendelések hozzáadása az SAML token attribútumok konfigurációját. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentieken kívül a Catchpoint alkalmazás azt várja, hogy néhány további attribútumot kell visszaadni az SAML válaszban, amelyek az alábbiakban láthatók. Ezek az attribútumok is előre ki vannak töltve, de áttekintheti őket a követelmények nek megfelelően.

    | Név | Forrás attribútuma|
    | ------------ | --------- |
    | névtér | user.assignedrole |

    > [!NOTE]
    > a névtérjogcímeket le kell képezni a fióknévvel. Ezt a fióknevet az Azure AD szerepköreiként kell beállítani, amelyek az SAML-válaszban kerülnek vissza. Kérjük, olvassa el ezt a [cikket,](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management) hogy megtanulják, hogyan kell beállítani a szerepeket

1. A **Telepítés egyszeri bejelentkezés saml-lel** lapon az **SAML aláíró tanúsítvány** szakaszában keresse meg a **Tanúsítvány (Base64)** lehetőséget, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Catchpoint beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy tesztfelhasználót hoz létre az Azure Portalon B.Simon néven.

1. Az Azure Portal bal oldali ablaktáblájában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd válassza az **Összes felhasználó**lehetőséget.
1. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.
1. A **Felhasználói** tulajdonságok csoportban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőbe írja username@companydomain.extensionbe a mezőt. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet, majd írja le a **Jelszó** mezőben megjelenő értéket.
   1. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t a Catchpoint hozzáférést biztosítva.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Catchpoint lehetőséget.**
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-catchpoint-sso"></a>Catchpoint sso konfigurálása

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként a Catchpoint alkalmazásba.

1. Kattintson a **Beállítások** ikonra, és válassza **az SSO identitásszolgáltató lehetőséget.**

    ![Catchpoint konfigurációja](./media/catchpoint-tutorial/configuration1.png)

1. Az **Egyszeri bejelentkezés** lapon hajtsa végre a következő lépéseket:

    ![Catchpoint konfigurációja](./media/catchpoint-tutorial/configuration2.png)

    1. A **Névtér** mezőbe írjon be egy érvényes névtérértéket.

    1. Az **identitásszolgáltató kiállítószövege** mezőbe írja be az **Azure AD-azonosító** értékét, amelyet az Azure Portalról másolt.

    1. Az **egyszeri bejelentkezésurl-cím** mezőbe írja be a **bejelentkezési URL-címet,** amelyet az Azure Portalról másolt.

    1. Nyissa meg a letöltött **tanúsítványfájlt (Base64)** a Jegyzettömbbe, másolja a tanúsítványfájl tartalmát, és illessze be **a Tanúsítvány** szövegmezőbe.

    1. Az **összevonási metaadat-XML-t** a **Metaadatok feltöltése** beállításra kattintva is feltöltheti.

    1. Kattintson a **Mentés** gombra.

### <a name="create-catchpoint-test-user"></a>Catchpoint tesztfelhasználó létrehozása

In this section, a user called Britta Simon is created in Catchpoint. Catchpoint támogatja a just-in-time felhasználói kiépítés, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nincs műveletelem. Ha a felhasználó már nem létezik a Catchpoint, egy új jön létre a hitelesítés után.

## <a name="test-sso"></a>SSO tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a Catchpoint csempére kattint, automatikusan be kell jelentkeznie arra a catchpoint-ba, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

> [!NOTE]
> Amikor a bejelentkezési lapon keresztül jelentkezik be a Catchpoint alkalmazásba, a **Catchpoint hitelesítő adatok**megadása után adja meg az érvényes **névtérértéket** a **Vállalati hitelesítő adatok (SSO)** mezőbe, és kattintson a **Bejelentkezés gombra.**

![Catchpoint konfigurációja](./media/catchpoint-tutorial/loginimage.png)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a Catchpointot az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlés a Microsoft Cloud App Security alkalmazásban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
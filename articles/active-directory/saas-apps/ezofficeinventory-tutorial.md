---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az EZOfficeInventory szolgáltatással | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az EZOfficeInventory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e8594f7c-dc2f-446f-9c25-676fe49ff3af
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/12/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5bd277eb3588743e7fb864445d4c6fc8397507b4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77370460"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ezofficeinventory"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az EZOfficeInventory szolgáltatással

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az EZOfficeInventory-ot az Azure Active Directoryval (Azure AD). Ha integrálja az EZOfficeInventory-ot az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér az EZOfficeInventory szolgáltatáshoz.
* Engedélyezze, hogy a felhasználók automatikusan bejelentkezve az EZOfficeInventory-ba az Azure AD-fiókjukkal.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* EZOfficeInventory egyszeri bejelentkezés (SSO) engedélyezve van előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* Az EZOfficeInventory támogatja az **SP** által kezdeményezett sso-t
* Az EZOfficeInventory támogatja **a Just In Time** felhasználói kiépítést
* Az EZOfficeInventory konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlő a feltételes hozzáféréstől terjed. [Megtudhatja, hogy miként kényszerítheti ki a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egy bérlőben.

## <a name="adding-ezofficeinventory-from-the-gallery"></a>AZ EZOfficeInventory hozzáadása a galériából

Az EZOfficeInventory azure AD-be való integrálásának konfigurálásához hozzá kell adnia az EZOfficeInventory-ot a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből szakaszban** írja be **az EZOfficeInventory** kifejezést a keresőmezőbe.
1. Válassza **az EZOfficeInventory** elemet az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ezofficeinventory"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése az EZOfficeInventory szolgáltatáshoz

Konfigurálja és tesztelje az Azure AD SSO-t az EZOfficeInventory segítségével egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó az EZOfficeInventory-ban.

Az Azure AD SSO konfigurálásához és teszteléséhez az EZOfficeInventory szolgáltatással hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    1. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja AZEZOfficeInventory SSO](#configure-ezofficeinventory-sso)** -konfigurálni az egyszeri bejelentkezési beállításokat az alkalmazás oldalán.
    1. **[Hozzon létre EZOfficeInventory teszt felhasználó](#create-ezofficeinventory-test-user)** - egy megfelelője B.Simon az EZOfficeInventory, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)az **EZOfficeInventory** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<SUBDOMAIN>.ezofficeinventory.com/users/sign_in`

    > [!NOTE]
    > Az érték nem valós. Frissítse az értéket a tényleges bejelentkezési URL-címmel. Lépjen kapcsolatba [az EZOfficeInventory ügyféltámogatási csapatával](mailto:support@ezofficeinventory.com) az érték leéséhez. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. Az EZOfficeInventory alkalmazás az SAML-állításokat egy adott formátumban várja, amely megköveteli, hogy egyéni attribútumleképezéseket adjon hozzá az SAML token attribútumok konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentieken kívül az EZOfficeInventory alkalmazás azt várja, hogy néhány további attribútumot kell visszaadni az SAML válaszban, amelyek az alábbiakban láthatók. Ezek az attribútumok is előre ki vannak töltve, de áttekintheti őket a követelmény nek megfelelően.

    | Név | Forrás attribútuma|
    | ---------------| --------------- |
    | first_name | user.givenname |
    | last_name | user.vezetéknév |
    | e-mail | user.mail |

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában keresse meg a **Tanúsítvány (Base64)** lehetőséget, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. Az **EZOfficeInventory beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés használatával az EZOfficeInventory hozzáférést biztosít.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza az **EZOfficeInventory lehetőséget.**
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-ezofficeinventory-sso"></a>AZ EZOfficeInventory-sSO konfigurálása

1. Az EZOfficeInventory konfigurációjának automatizálásához telepítenie kell a **My Apps Secure Sign-in böngészőbővítményt** **a Bővítmény telepítése**gombra kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

1. Hozzáadása után kiterjesztés a böngésző, kattintson a **Beállítása EZOfficeInventory** irányítja, hogy a EZOfficeInventory alkalmazás. Itt adja meg a rendszergazdai hitelesítő adatokat az EZOfficeInventory-ba való bejelentkezéshez. A böngésző bővítmény automatikusan konfigurálja az alkalmazást, és automatizálja a 3-5.

    ![Beállítási konfiguráció](common/setup-sso.png)

1. Ha manuálisan szeretné beállítani az EZOfficeInventory-ot, nyisson meg egy új böngészőablakot, és jelentkezzen be rendszergazdaként az EZOfficeInventory vállalati webhelyére, és hajtsa végre a következő lépéseket:

1. A lap jobb felső sarkában kattintson a **Profil** elemre, majd keresse meg a **Beállítások** > **hozzáadása ekkor**lehetőséget.

    ![EZOfficeInventory konfiguráció](./media/ezofficeinventory-tutorial/configure01.png)

1. Görgessen le az **SAML-integráció** szakaszig, és hajtsa végre a következő lépéseket:

    ![EZOfficeInventory konfiguráció](./media/ezofficeinventory-tutorial/configure02.png)

    a. Jelölje be az **Engedélyezve jelölőnégyzetet.**

    b. Az **Identitásszolgáltató URL-címe** mezőbe illessze be a **bejelentkezési URL-értéket,** amelyet az Azure Portalról másolt.

    c. Nyissa meg a Base64 kódolású tanúsítványt a jegyzettömbben, másolja annak tartalmát, és illessze be az **Identitásszolgáltató tanúsítványa** mezőbe.

    d. A **Bejelentkezési gomb szövegmezőjébe** írja be a bejelentkezési gomb szövegét.

    e. A **Keresztnév** mezőbe írja be **first_name.**

    f. A **Vezetéknév** mezőbe írja be **last_name.**

    g. Az **E-mail** mezőbe írja be az **e-mailt.**

    h. Válassza ki a szerepkört a követelmény szerint az **EZOfficeInventory szerepkör alapértelmezés szerint** beállításból.

    i. Kattintson a **Frissítés** parancsra.

### <a name="create-ezofficeinventory-test-user"></a>EZOfficeInventory tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre az EZOfficeInventory-ban. EZOfficeInventory támogatja a just-in-time felhasználói kiépítés, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nincs műveletelem. Ha a felhasználó már nem létezik az EZOfficeInventory, egy új jön létre a hitelesítés után.

## <a name="test-sso"></a>SSO tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a hozzáférési panelen az EZOfficeInventory csempére kattint, automatikusan be kell jelentkeznie az EZOfficeInventory-ba, amelyhez beállította az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Mi a munkamenet-vezérlés a Microsoft Cloud App Security alkalmazásban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Próbálja ki az EZOfficeInventory szolgáltatást az Azure AD-vel](https://aad.portal.azure.com/)
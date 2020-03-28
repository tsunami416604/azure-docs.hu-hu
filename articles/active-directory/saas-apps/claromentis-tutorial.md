---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Claromentis-szel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Claromentis között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7980e0c5-e4d8-4678-afa2-7ec219432114
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c6eaf23950f83592a33709574be464f4499f0ab
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74823220"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-claromentis"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Claromentis-szel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Claromentis-t az Azure Active Directoryval (Azure AD). Ha integrálja a Claromentis-t az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Claromentis.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve a Claromentis az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Claromentis egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* A Claromentis támogatja az **SP-t és az IDP** által kezdeményezett sso-t
* A Claromentis támogatja a **Just In Time** felhasználói kiépítést

## <a name="adding-claromentis-from-the-gallery"></a>Claromentis hozzáadása a galériából

A Claromentis Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Claromentis-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **Hozzáadás a gyűjteményből szakaszban** írja be a **Claromentis** kifejezést a keresőmezőbe.
1. Válassza a **Claromentis** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-claromentis"></a>Konfigurálja és tesztelje az Azure AD egyszeri bejelentkezését a Claromentis-hez

Konfigurálja és tesztelje az Azure AD-sSO-t a Claromentis-szel egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a claromentis kapcsolódó felhasználója között.

Az Azure AD SSO Claromentis-szel való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    * **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    * **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja a Claromentis SSO-t](#configure-claromentis-sso)** - az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalán.
    * **[Hozzon létre Claromentis teszt felhasználó](#create-claromentis-test-user)** - egy megfelelője B.Simon a Claromentis, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Claromentis** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza ki **az egyszeri bejelentkezést.**
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban, ha az alkalmazást **IDP** által kezdeményezett módban szeretné konfigurálni, adja meg a következő mezők értékeit:

    a. Az **Azonosító** mezőbe írja be az azonosító értékét a szervezeti követelménynek megfelelően.

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://<customer_site_url>/custom/loginhandler/simplesaml/www/module.php/saml/sp/saml2-acs.php/claromentis`

1. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:

    | | |
    |-|-|
    | `https://<customer_site_url>/login`|
    | `https://<customer_site_url>/login?no_auto=0`|

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító, válasz URL-cím és bejelentkezési URL-cím, amely később a turorial.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az SAML aláíró tanúsítvány szakaszban keresse meg az **összevonási** **metaadatok XML-jét,** és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A **Claromentis beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t a Claromentis hozzáférést biztosítva.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Claromentis**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-claromentis-sso"></a>Claromentis SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Claromentis webhelyére rendszergazdaként.

1. Kattintson az **alkalmazások ikonra,** és válassza **a Rendszergazda**lehetőséget.

    ![Claromentis konfiguráció](./media/claromentis-tutorial/config1.png)

1. Válassza **az Egyéni bejelentkezéskezelő** lapot.

    ![Claromentis konfiguráció](./media/claromentis-tutorial/config2.png)

1. Válassza a **SAML Config lehetőséget.**

    ![Claromentis konfiguráció](./media/claromentis-tutorial/config3.png)

1. Az **SAML Config** lapon görgessen le a **Config** szakaszig, és hajtsa végre az alábbi lépéseket:

    ![Claromentis konfiguráció](./media/claromentis-tutorial/config4.png)

    a. A **Műszaki kapcsolattartó neve** mezőbe írja be a műszaki kapcsolattartó nevét.

    b. A **Technikai kapcsolat e-mail** szövegmezőbe írja be a technikai kapcsolattartó személy e-mail címét.

    c. Adja meg a jelszót az **Auth Admin Password** szövegmezőben.

1. Görgessen le **az Auth Sources oldalra,** és hajtsa végre a következő lépéseket:

    ![Claromentis konfiguráció](./media/claromentis-tutorial/config5.png)

    a. Az **IDP-szövegmezőbe** írja be az **Azure AD-azonosító** értékét, amelyet az Azure Portalról másolt.

    b. Az **Entitásazonosító** mezőbe írja be az Entitásazonosító értékét.

    c. Töltse fel az **összevonási metaadat-XML-fájlt,** amelyet az Azure Portalról töltött le.

    d. Kattintson a **Mentés** gombra.

1. Most észre fogja venni, hogy az összes URL-cím az **SAML konfigurációs** szakasz **identitásszolgáltató** javunkra van kitöltve.

    ![Claromentis konfiguráció](./media/claromentis-tutorial/config6.png)

    a. Copy **Identifier (entity ID)** érték, illessze be ezt az értéket az **Azonosító** szövegmezőbe az Azure Portal **Alapszintű SAML konfigurációs** szakaszában.

    b. Másolja **a válasz URL-értékét,** illessze be ezt az értéket az Azure Portal **Alapszintű SAML-konfiguráció** **szakaszának Válasz URL-szövegmezőjébe.**

    c. Sign **On URL-érték** másolása, illessze be ezt az értéket a **Bejelentkezési URL-cím** szövegmezőbe az Azure Portal **Alapszintű SAML-konfiguráció** szakaszán.

### <a name="create-claromentis-test-user"></a>Claromentis tesztfelhasználó létrehozása

In this section, a user called B.Simon is created in Claromentis. A Claromentis támogatja a just-in-time felhasználói kiépítést, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nincs műveletelem. Ha a felhasználó már nem létezik a Claromentis, egy új jön létre a hitelesítés után.

## <a name="test-sso"></a>SSO tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a Claromentis csempére kattint, automatikusan be kell jelentkeznie arra a Claromentisbe, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a Claromentis-t az Azure AD-vel](https://aad.portal.azure.com/)
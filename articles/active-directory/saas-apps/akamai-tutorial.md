---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az Akamai-val | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az Akamai között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1b7e0d7a-e78f-43a5-af93-b626186e2376
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 042dd242285081001ca48c9f17e4d42c2294c0ff
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74979594"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-akamai"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az Akamai-val

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az Akamai-t az Azure Active Directoryval (Azure AD). Ha integrálja az Akamai-t az Azure AD-vel, a következőket teheti:

* Az Azure AD- ban, aki hozzáfér az Akamai.Control in Azure AD who has access to Akamai.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve Akamai az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Akamai egyszeri bejelentkezés (SSO) engedélyezve van előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

- A Tartalékidő támogatja az IDP által kezdeményezett sso-t

## <a name="adding-akamai-from-the-gallery"></a>Akamai hozzáadása a galériából

Az Akamai Azure AD-be való integrációjának konfigurálásához hozzá kell adnia az Akamai-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből szakaszban** írja be az **Akamai** kifejezést a keresőmezőbe.
1. Válassza az **Akamai** elemet az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-akamai"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése az Akamai számára

Konfigurálja és tesztelje az Azure AD SSO-t az Akamai segítségével egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó akamai ban.

Az Azure AD SSO konfigurálásához és teszteléséhez az Akamai segítségével hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    * **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    * **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja az Akamai SSO-t](#configure-akamai-sso)** - az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
    * **[Hozzon létre Akamai teszt felhasználó](#create-akamai-test-user)** - egy megfelelője B.Simon a akamai, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)az **Akamai** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza ki **az egyszeri bejelentkezést.**
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban, ha az alkalmazást **IDP** által kezdeményezett módban szeretné konfigurálni, adja meg a következő mezők értékeit:

    a. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<Yourapp>.login.go.akamai-access.com/sp/response`

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https:// <Yourapp>.login.go.akamai-access.com/sp/response`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító és válasz URL-címével. Lépjen kapcsolatba [az Akamai ügyféltámogatási csapatával,](https://www.akamai.com/us/en/contact-us/) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az SAML aláíró tanúsítvány szakaszban keresse meg az **összevonási** **metaadatok XML-jét,** és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. Az **Akamai beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés használatával hozzáférést biztosít az Akamai.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza az **Akamai**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-akamai-sso"></a>Akamai SSO konfigurálása

### <a name="setting-up-idp"></a>Az IDP beállítása

1. Jelentkezzen be az **Akamai Enterprise Application Access** konzolra.
1. Az **Akamai EAA konzolon**válassza az > **Identitásazonosító-szolgáltatók lehetőséget.** **Identity**

    ![Akamai konfigurálása](./media/header-akamai-tutorial/configure01.png)

1. Kattintson **az Identitásszolgáltató hozzáadása gombra.**

    ![Akamai konfigurálása](./media/header-akamai-tutorial/configure02.png)

    a. Adja meg az **Egyedi nevet**.
    
    b. Válassza **a Harmadik fél SAML (Harmadik fél SAML)** lehetőséget, majd kattintson az **Identitásszolgáltató létrehozása és a Konfigurálás parancsra.**

### <a name="general-settings"></a>Általános beállítások

1. **Identity Intercept** – Adja meg a nevét a (SP alap URL-fogja használni az Azure AD konfiguráció)

    > [!NOTE]
    > Választhat, hogy saját egyéni tartománya van (DNS-bejegyzésre és tanúsítványra lesz szükség). Ebben a példában fogjuk használni az Akamai domain.

1. **Akamai Cloud Zone** - Válassza ki a megfelelő felhőzónát.
1. **Tanúsítványérvényesítés** - Ellenőrizze az Akamai dokumentációt (nem kötelező)

    ![Akamai konfigurálása](./media/header-akamai-tutorial/configure03.png)

### <a name="authentication-configuration"></a>Hitelesítés konfigurációja

1. URL – Adja meg az URL-t ugyanaz, mint az identitás elfogása (ez az, ahol a felhasználók átirányítás hitelesítés után).
2. Kijelentkezési URL: Frissítse a kijelentkezés URL-címét.
3. Saml-kérelem aláírása: az alapértelmezett nincs bejelölve.
4. Az IDP metaadat-fájl, adja hozzá az alkalmazást az Azure AD console.For the IDP Metadata File, add hozzá az alkalmazást az Azure AD Console.For the IDP Metadata File, add a Application in the Azure AD Console.

    ![Akamai konfigurálása](./media/header-akamai-tutorial/configure04.png)

### <a name="header-based-authentication"></a>Fejlécalapú hitelesítés

Akamai fejlécalapú hitelesítés

1. Válassza az **Egyéni HTTP-űrlap** lehetőséget, az Alkalmazások hozzáadása varázslót.

    ![Akamai konfigurálása](./media/header-akamai-tutorial/configure05.png)

    ![Akamai konfigurálása](./media/header-akamai-tutorial/configure06.png)

    ![Akamai konfigurálása](./media/header-akamai-tutorial/configure07.png)

    ![Akamai konfigurálása](./media/header-akamai-tutorial/configure08.png)

#### <a name="authentication"></a>Hitelesítés

![Akamai konfigurálása](./media/header-akamai-tutorial/configure09.png)

![Akamai konfigurálása](./media/header-akamai-tutorial/configure10.png)

#### <a name="services"></a>Szolgáltatások

1. Kattintson a Mentés és ugrás a hitelesítésre gombra.

![Akamai konfigurálása](./media/header-akamai-tutorial/configure11.png)

#### <a name="advanced-settings"></a>Speciális beállítások

1. A **VevőHTTP-fejlécek**csoportban adja meg a **CustomerHeader** és **az SAML attribútumot.**

    ![Akamai konfigurálása](./media/header-akamai-tutorial/configure12.png)

1. Kattintson a **Mentés gombra, és lépjen a Telepítés** gombra.

    ![Akamai konfigurálása](./media/header-akamai-tutorial/configure13.png)

#### <a name="deploy-the-application"></a>Az alkalmazás telepítése

1. Kattintson **az Alkalmazás telepítése** gombra.

    ![Akamai konfigurálása](./media/header-akamai-tutorial/configure14.png)

1. Ellenőrizze, hogy az alkalmazás telepítése sikeresen megtörtént-e.

    ![Akamai konfigurálása](./media/header-akamai-tutorial/configure15.png)

### <a name="kerberos-authentication"></a>Kerberos-hitelesítés

#### <a name="remote-desktop"></a>Távoli asztal

1. Válassza az **RDP** lehetőséget az ADD Applications varázslóból.

    ![Akamai konfigurálása](./media/header-akamai-tutorial/configure16.png)

    ![Akamai konfigurálása](./media/header-akamai-tutorial/configure17.png)

    ![Akamai konfigurálása](./media/header-akamai-tutorial/configure18.png)

1. Adja meg azt az összekötőt, amely ezt a kiszolgálót fogja elszolgálni.

    ![Akamai konfigurálása](./media/header-akamai-tutorial/configure19.png)

#### <a name="authentication"></a>Hitelesítés

Kattintson a **Mentés gombra, és nyissa meg a Szolgáltatásokat**.

![Akamai konfigurálása](./media/header-akamai-tutorial/configure20.png)

#### <a name="services"></a>Szolgáltatások

Kattintson a **Mentés gombra, és lépjen a Speciális beállítások gombra.**

![Akamai konfigurálása](./media/header-akamai-tutorial/configure21.png)

#### <a name="advanced-settings"></a>Speciális beállítások

Kattintson a **Mentés gombra, és nyissa meg a Telepítés menüt.**

![Akamai konfigurálása](./media/header-akamai-tutorial/configure22.png)

![Akamai konfigurálása](./media/header-akamai-tutorial/configure23.png)

![Akamai konfigurálása](./media/header-akamai-tutorial/configure24.png)

### <a name="deployment"></a>Környezet

#### <a name="ssh"></a>SSH

1. Nyissa meg az Alkalmazások hozzáadása lehetőséget, válassza az **SSH**lehetőséget.

    ![Akamai konfigurálása](./media/header-akamai-tutorial/configure25.png)

    ![Akamai konfigurálása](./media/header-akamai-tutorial/configure26.png)

1. Alkalmazásidentitás konfigurálása.

    ![Akamai konfigurálása](./media/header-akamai-tutorial/configure27.png)

    a. Adja meg a nevet / leírást.

    b. Adja meg az alkalmazáskiszolgáló IP/FQDN azonosítóját és portját az SSH-hoz.

    c. Adja meg az SSH felhasználónevet / jelszót *Ellenőrizze az Akamai EAA-t.

    d. Adja meg a külső állomás nevét.

    e. Adja meg az összekötő helyét, és válassza ki az összekötőt.

#### <a name="authentication"></a>Hitelesítés

Kattintson a **Mentés gombra, és lépjen a Szolgáltatások menügombra.**

![Akamai konfigurálása](./media/header-akamai-tutorial/configure28.png)

#### <a name="services"></a>Szolgáltatások

Kattintson a **Mentés gombra, és lépjen a Speciális beállítások gombra.**

![Akamai konfigurálása](./media/header-akamai-tutorial/configure29.png)

#### <a name="advanced-settings"></a>Speciális beállítások

Kattintson a Mentés és ugrás telepítésre

![Akamai konfigurálása](./media/header-akamai-tutorial/configure30.png)

![Akamai konfigurálása](./media/header-akamai-tutorial/configure31.png)

#### <a name="deployment"></a>Környezet

Kattintson **az Alkalmazás telepítése gombra.**

![Akamai konfigurálása](./media/header-akamai-tutorial/configure32.png)

### <a name="kerberos-applications"></a>Kerberos-alkalmazások

#### <a name="adding-directory"></a>Könyvtár hozzáadása

![Akamai konfigurálása](./media/header-akamai-tutorial/configure33.png)

![Akamai konfigurálása](./media/header-akamai-tutorial/configure34.png)

![Akamai konfigurálása](./media/header-akamai-tutorial/configure35.png)

![Akamai konfigurálása](./media/header-akamai-tutorial/configure36.png)

### <a name="create-akamai-test-user"></a>Akamai tesztfelhasználó létrehozása

Ebben a szakaszban egy B.Simon nevű felhasználót hoz létre az Akamai ban. Együttműködve [Akamai ügyfél támogatási csapat](https://www.akamai.com/us/en/contact-us/) a felhasználók hozzáadása az Akamai platformon. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat. 

## <a name="test-sso"></a>SSO tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a hozzáférési panelen az Akamai csempére kattint, automatikusan be kell jelentkeznie arra az Akamai-ba, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki az Akamai-t az Azure AD-vel](https://aad.portal.azure.com/)

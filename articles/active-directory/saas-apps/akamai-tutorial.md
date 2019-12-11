---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Akamai | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Akamai között.
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
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74979594"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-akamai"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Akamai

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Akamai a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az Akamai-t az Azure AD-vel, a következőket teheti:

* A Akamai-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Akamai az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Akamai egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

- A Slack támogatja a IDENTITÁSSZOLGÁLTATÓ által kezdeményezett egyszeri bejelentkezést

## <a name="adding-akamai-from-the-gallery"></a>Akamai hozzáadása a gyűjteményből

A Akamai Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Akamai a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Akamai** kifejezést a keresőmezőbe.
1. Válassza ki a **Akamai** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-akamai"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a Akamai

Konfigurálja és tesztelje az Azure AD SSO-t a Akamai a **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Akamai-ben.

Az Azure AD SSO és a Akamai konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[Akamai SSO konfigurálása](#configure-akamai-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    * **[Hozzon létre Akamai-teszt felhasználót](#create-akamai-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-Akamai rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **Akamai** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az **ALAPszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<Yourapp>.login.go.akamai-access.com/sp/response`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https:// <Yourapp>.login.go.akamai-access.com/sp/response`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító és válasz URL-címmel. Az értékek lekéréséhez forduljon a Akamai ügyfélszolgálati [csapatához](https://www.akamai.com/us/en/contact-us/) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A **Akamai beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőbe írja be a username@companydomain.extension. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a  **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Akamai.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **Akamai**lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-akamai-sso"></a>Akamai SSO konfigurálása

### <a name="setting-up-idp"></a>IDENTITÁSSZOLGÁLTATÓ beállítása

1. Bejelentkezési a **Akamai Enterprise Application Access** -konzolra.
1. Az **Akamai EAA konzolon**válassza az **Identity** > **Identity Providers**elemet.

    ![Akamai konfigurálása](./media/header-akamai-tutorial/configure01.png)

1. Kattintson a **személyazonosság-szolgáltató hozzáadása**elemre.

    ![Akamai konfigurálása](./media/header-akamai-tutorial/configure02.png)

    a. Adja meg az **egyedi nevet**.
    
    b. Válasszon **harmadik féltől származó SAML** -t, és kattintson az **Identity Provider létrehozása és konfigurálása**elemre.

### <a name="general-settings"></a>Általános beállítások

1. **Identitás feltartóztatása** – adja meg az Azure ad-konfigurációhoz használandó (SP Base URL-cím) nevét.

    > [!NOTE]
    > Dönthet úgy is, hogy saját egyéni tartományt használ (DNS-bejegyzést és tanúsítványt igényel). Ebben a példában a Akamai tartományt fogjuk használni.

1. **Akamai Cloud Zone** – válassza ki a megfelelő felhő zónát.
1. **Tanúsítvány érvényesítése** – a Akamai dokumentációjának ellenőrzése (nem kötelező)

    ![Akamai konfigurálása](./media/header-akamai-tutorial/configure03.png)

### <a name="authentication-configuration"></a>Hitelesítési konfiguráció

1. URL-cím – megegyező URL-cím megadása az identitás feltartóztatva (ez az a hely, ahol a felhasználók a hitelesítés után átirányíthatók).
2. Kijelentkezési URL: frissítse a kijelentkezési URL-címet.
3. SAML-kérelem aláírása: az alapértelmezett beállítás nincs bejelölve.
4. A IDENTITÁSSZOLGÁLTATÓ metaadat-fájljához adja hozzá az alkalmazást az Azure AD-konzolon.

    ![Akamai konfigurálása](./media/header-akamai-tutorial/configure04.png)

### <a name="header-based-authentication"></a>Fejléc alapú hitelesítés

Akamai-fejléc alapú hitelesítés

1. Válassza az **Egyéni http** -űrlap az alkalmazások hozzáadása varázslót.

    ![Akamai konfigurálása](./media/header-akamai-tutorial/configure05.png)

    ![Akamai konfigurálása](./media/header-akamai-tutorial/configure06.png)

    ![Akamai konfigurálása](./media/header-akamai-tutorial/configure07.png)

    ![Akamai konfigurálása](./media/header-akamai-tutorial/configure08.png)

#### <a name="authentication"></a>Hitelesítés

![Akamai konfigurálása](./media/header-akamai-tutorial/configure09.png)

![Akamai konfigurálása](./media/header-akamai-tutorial/configure10.png)

#### <a name="services"></a>Szolgáltatások

1. Kattintson a Mentés gombra, és válassza a hitelesítés lehetőséget.

![Akamai konfigurálása](./media/header-akamai-tutorial/configure11.png)

#### <a name="advanced-settings"></a>Speciális beállítások

1. Az **ügyfél http-fejlécei**területen a **CustomerHeader** és az **SAML attribútumot**kell megadni.

    ![Akamai konfigurálása](./media/header-akamai-tutorial/configure12.png)

1. Kattintson **a Save (Mentés) gombra, és válassza a telepítés** gombot.

    ![Akamai konfigurálása](./media/header-akamai-tutorial/configure13.png)

#### <a name="deploy-the-application"></a>Az alkalmazás üzembe helyezése

1. Kattintson az **alkalmazás központi telepítése** gombra.

    ![Akamai konfigurálása](./media/header-akamai-tutorial/configure14.png)

1. Ellenőrizze, hogy az alkalmazás telepítése sikeresen megtörtént-e.

    ![Akamai konfigurálása](./media/header-akamai-tutorial/configure15.png)

### <a name="kerberos-authentication"></a>Kerberos-hitelesítés

#### <a name="remote-desktop"></a>Távoli asztal

1. Az alkalmazások hozzáadása varázslóban válassza az **RDP** elemet.

    ![Akamai konfigurálása](./media/header-akamai-tutorial/configure16.png)

    ![Akamai konfigurálása](./media/header-akamai-tutorial/configure17.png)

    ![Akamai konfigurálása](./media/header-akamai-tutorial/configure18.png)

1. Itt adhatja meg a karbantartáshoz használandó összekötőt.

    ![Akamai konfigurálása](./media/header-akamai-tutorial/configure19.png)

#### <a name="authentication"></a>Hitelesítés

Kattintson **a Mentés gombra, és válassza a szolgáltatások**lehetőséget.

![Akamai konfigurálása](./media/header-akamai-tutorial/configure20.png)

#### <a name="services"></a>Szolgáltatások

Kattintson **a Save (Mentés) gombra, és válassza a speciális beállítások lehetőséget**.

![Akamai konfigurálása](./media/header-akamai-tutorial/configure21.png)

#### <a name="advanced-settings"></a>Speciális beállítások

Kattintson **a Mentés gombra, és válassza a telepítés**lehetőséget.

![Akamai konfigurálása](./media/header-akamai-tutorial/configure22.png)

![Akamai konfigurálása](./media/header-akamai-tutorial/configure23.png)

![Akamai konfigurálása](./media/header-akamai-tutorial/configure24.png)

### <a name="deployment"></a>Üzembe helyezés

#### <a name="ssh"></a>SSH

1. Lépjen az alkalmazások hozzáadása elemre, és válassza az **SSH**lehetőséget.

    ![Akamai konfigurálása](./media/header-akamai-tutorial/configure25.png)

    ![Akamai konfigurálása](./media/header-akamai-tutorial/configure26.png)

1. Adja meg az alkalmazás identitását.

    ![Akamai konfigurálása](./media/header-akamai-tutorial/configure27.png)

    a. Adja meg a nevet és a leírást.

    b. Az alkalmazáskiszolgáló IP/FQDN és portjának megadása az SSH-hoz.

    c. A Akamai EAA megadása az SSH-Felhasználónév/jelszó beállításnál.

    d. Adja meg a külső állomásnév nevét.

    e. Adja meg az összekötő helyét, és válassza ki az összekötőt.

#### <a name="authentication"></a>Hitelesítés

Kattintson a **Save (Mentés**) gombra, és válassza a szolgáltatások lehetőséget.

![Akamai konfigurálása](./media/header-akamai-tutorial/configure28.png)

#### <a name="services"></a>Szolgáltatások

Kattintson **a Save (Mentés) gombra, és válassza a speciális beállítások lehetőséget**.

![Akamai konfigurálása](./media/header-akamai-tutorial/configure29.png)

#### <a name="advanced-settings"></a>Speciális beállítások

Kattintson a Mentés gombra, és lépjen a központi telepítésre

![Akamai konfigurálása](./media/header-akamai-tutorial/configure30.png)

![Akamai konfigurálása](./media/header-akamai-tutorial/configure31.png)

#### <a name="deployment"></a>Üzembe helyezés

Kattintson az **alkalmazás központi telepítése**elemre.

![Akamai konfigurálása](./media/header-akamai-tutorial/configure32.png)

### <a name="kerberos-applications"></a>Kerberos-alkalmazások

#### <a name="adding-directory"></a>Könyvtár hozzáadása

![Akamai konfigurálása](./media/header-akamai-tutorial/configure33.png)

![Akamai konfigurálása](./media/header-akamai-tutorial/configure34.png)

![Akamai konfigurálása](./media/header-akamai-tutorial/configure35.png)

![Akamai konfigurálása](./media/header-akamai-tutorial/configure36.png)

### <a name="create-akamai-test-user"></a>Akamai-tesztelési felhasználó létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználót hoz létre a Akamai-ben. A [Akamai ügyfél-támogatási csapattal](https://www.akamai.com/us/en/contact-us/) együttműködve veheti fel a felhasználókat a Akamai-platformba. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat. 

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Akamai csempére kattint, automatikusan be kell jelentkeznie arra a Akamai, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [A Akamai kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

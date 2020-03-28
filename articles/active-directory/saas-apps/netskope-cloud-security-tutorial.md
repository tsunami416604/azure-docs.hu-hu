---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Netskope felügyeleti konzollal | Microsoft dokumentumok'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Netskope felügyeleti konzol között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e286adec-8d19-4d41-9afa-a2e39d7a5983
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/31/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dec8f8065114b89dfa7bcaceee3f26855953dde2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74081766"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netskope-administrator-console"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Netskope felügyeleti konzollal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Netskope felügyeleti konzolt az Azure Active Directoryval (Azure AD). Ha integrálja a Netskope Felügyeleti konzolt az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Netskope felügyeleti konzolhoz.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve a Netskope felügyeleti konzol az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Netskope Administrator Console egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* A Netskope Administrator Console támogatja az **SP és az IDP** által kezdeményezett sso-t

## <a name="adding-netskope-administrator-console-from-the-gallery"></a>Netskope felügyeleti konzol hozzáadása a galériából

A Netskope felügyeleti konzol Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Netskope felügyeleti konzolt a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **katalógus hozzáadásszakaszában** írja be a **Netskope felügyeleti konzolkifejezést** a keresőmezőbe.
1. Válassza a **Netskope administrator console** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-netskope-administrator-console"></a>Konfigurálja és tesztelje az Azure AD egyszeri bejelentkezését a Netskope felügyeleti konzolhoz

Konfigurálja és tesztelje az Azure AD SSO szolgáltatást a Netskope felügyeleti konzollal egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Netskope felügyeleti konzolon.

Az Azure AD SSO konfigurálásához és teszteléséhez a Netskope felügyeleti konzollal hajtsa végre az alábbi építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    * **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    * **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja a Netskope administrator console egyszeri bejelentkezési kiszolgálót](#configure-netskope-administrator-console-sso)** – az alkalmazás oldalon az egyszeri bejelentkezési beállítások konfigurálásához.
    * **[Hozzon létre Netskope administrator console teszt felhasználó](#create-netskope-administrator-console-test-user)** - egy megfelelője B.Simon a Netskope felügyeleti konzol, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Netskope administrator console** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza **az egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban, ha az alkalmazást **IDP** által kezdeményezett módban szeretné konfigurálni, adja meg a következő mezők értékeit:

    a. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`Netskope_<OrgKey>`

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://<tenant_host_name>/saml/acs`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító és válasz URL-címével. Ezeket az értékeket később az oktatóanyagban ismertetheti.

1. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<tenantname>.goskope.com`

    > [!NOTE]
    > A bejelentkezési URL-értékek nem valósak. A bejelentkezési URL-érték frissítése a tényleges bejelentkezési URL-címmel. Lépjen kapcsolatba [a Netskope administrator console ügyféltámogatási csapatával](mailto:support@netskope.com) a bejelentkezési URL-érték lefelvételéhez. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. A Netskope Administrator Console alkalmazás az SAML-állításokat egy adott formátumban várja, amely megköveteli, hogy egyéni attribútumleképezéseket adjon hozzá az SAML token attribútumok konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentieken kívül a Netskope Administrator Console alkalmazás azt várja, hogy néhány további attribútum ot kell visszaadni az SAML válaszban, amelyek az alábbiakban láthatók. Ezek az attribútumok is előre ki vannak töltve, de áttekintheti őket a követelmények nek megfelelően.

    | Név |  Forrás attribútuma|
    | ---------| --------- |
    | rendszergazdai szerepkör | user.assignedroles |

    > [!NOTE]
    > Kattintson [ide,](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management) hogy megtudja, hogyan hozhat létre szerepköröket az Azure AD-ben.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában keresse meg a **Tanúsítvány (Base64)** lehetőséget, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Netskope felügyeleti konzol beállítása** csoportban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t a Netskope felügyeleti konzol hoz való hozzáférés biztosításával.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Netskope felügyeleti konzol lehetőséget.**
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-netskope-administrator-console-sso"></a>Netskope administrator konzol sso konfigurálása

1. Nyisson meg egy új lapot a böngészőben, és jelentkezzen be a Netskope Administrator Console vállalati webhelyére rendszergazdaként.

1. Kattintson a bal oldali navigációs ablak **Beállítások** fülére.

    ![Netskope felügyeleti konzol konfigurációja](./media/netskope-cloud-security-tutorial/config-settings.png)

1. Kattintson **a Felügyelet** fülre.

    ![Netskope felügyeleti konzol konfigurációja](./media/netskope-cloud-security-tutorial/config-administration.png)

1. Kattintson **az SSO** fülre.

    ![Netskope felügyeleti konzol konfigurációja](./media/netskope-cloud-security-tutorial/config-sso.png)

1. A **Hálózati beállítások** csoportban hajtsa végre az alábbi lépéseket:
    
    ![Netskope felügyeleti konzol konfigurációja](./media/netskope-cloud-security-tutorial/config-pasteurls.png)

    a. Másolja **a helyességi feltétel fogyasztói szolgáltatás URL-értékét,** és illessze be a **Válasz URL-cím** szövegmezőbe az Azure Portal **alapszintű SAML-konfiguráció** szakaszában.

    b. Másolja **a szolgáltató entitásazonosító** értékét, és illessze be az Azure Portal **Alapszintű SAML-konfiguráció** szakaszában található **Azonosító** szövegmezőbe.

1. Kattintson az **SSO/SLO Beállítások** szakasz **SZERKESZTÉSi beállításai** ra.

    ![Netskope felügyeleti konzol konfigurációja](./media/netskope-cloud-security-tutorial/config-editsettings.png)

1. A **Beállítások** előugró ablakban hajtsa végre a következő lépéseket;

    ![Netskope felügyeleti konzol konfigurációja](./media/netskope-cloud-security-tutorial/configuration.png)

    a. Válassza az **SSO engedélyezése**lehetőséget.

    b. Az **IDP URL-cím** szövegdobozába illessze be a **bejelentkezési URL-értéket,** amelyet az Azure Portalról másolt.

    c. Az **IDP-ENTITÁS azonosító** szövegdobozába illessze be az **Azure AD-azonosító** értékét, amelyet az Azure Portalról másolt.

    d. Nyissa meg a letöltött Base64 kódolású tanúsítványt a jegyzettömbben, másolja annak tartalmát a vágólapra, majd illessze be az **IDP CERTIFICATE** szövegdobozába.

    e. Válassza az **SSO engedélyezése**lehetőséget.

    f. Az **IDP SLO URL-cím** szövegdobozába illessze be a **kijelentkezési URL-cím** értékét, amelyet az Azure Portalról másolt.

    g. Kattintson **a KÜLDÉS gombra.**

### <a name="create-netskope-administrator-console-test-user"></a>Netskope administrator konzolteszt-felhasználó létrehozása

1. Nyisson meg egy új lapot a böngészőben, és jelentkezzen be a Netskope Administrator Console vállalati webhelyére rendszergazdaként.

1. Kattintson a bal oldali navigációs ablak **Beállítások** fülére.

    ![Netskope rendszergazdai konzol felhasználóinak létrehozása](./media/netskope-cloud-security-tutorial/config-settings.png)

1. Kattintson **az Aktív platform** fülre.

    ![Netskope rendszergazdai konzol felhasználóinak létrehozása](./media/netskope-cloud-security-tutorial/user1.png)

1. Kattintson **a Felhasználók** fülre.

    ![Netskope rendszergazdai konzol felhasználóinak létrehozása](./media/netskope-cloud-security-tutorial/add-user.png)

1. Kattintson **a Felhasználók hozzáadása gombra.**

    ![Netskope rendszergazdai konzol felhasználóinak létrehozása](./media/netskope-cloud-security-tutorial/user-add.png)

1. Adja meg a hozzáadni kívánt felhasználó e-mail címét, majd kattintson az **ADD gombra.**

    ![Netskope rendszergazdai konzol felhasználóinak létrehozása](./media/netskope-cloud-security-tutorial/add-user-popup.png)

## <a name="test-sso"></a>SSO tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a Netskope administrator console csempére kattint, automatikusan be kell jelentkeznie a Netskope felügyeleti konzolra, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a Netskope felügyeleti konzolt az Azure AD-vel](https://aad.portal.azure.com/)
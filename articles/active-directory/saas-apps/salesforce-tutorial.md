---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Salesforce-szal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Salesforce között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d2d7d420-dc91-41b8-a6b3-59579e043b35
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/17/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a875cee7e6796a2c865bde4a62f2f0463eb12130
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78967734"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-salesforce"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Salesforce-szal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Salesforce-ot az Azure Active Directoryval (Azure AD). Ha integrálja a Salesforce-ot az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, hogy ki férhet hozzá a Salesforce-hoz.
* Lehetővé teszi, hogy a felhasználók automatikusan bejelentkezve a Salesforce az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* A Salesforce egyszeri bejelentkezés (SSO) kompatibilis előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* A Salesforce támogatja az **SP** által kezdeményezett sso-t

* A Salesforce támogatja [ **az automatikus** felhasználói kiépítést és a megszüntetést](salesforce-provisioning-tutorial.md) (ajánlott)

* A Salesforce támogatja **a Just In Time** felhasználói kiépítést

* A Salesforce Mobile alkalmazás most már konfigurálható az Azure AD-vel az SSO engedélyezéséhez. Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.
* A Salesforce konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlés a feltételes hozzáférésből terjed. [Megtudhatja, hogy miként kényszerítheti ki a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-salesforce-from-the-gallery"></a>Salesforce hozzáadása a gyűjteményből

A Salesforce Azure AD-be való integrációjának konfigurálásához hozzá kell adnia a Salesforce-ot a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből szakaszban** írja be a **Salesforce** kifejezést a keresőmezőbe.
1. Válassza a **Salesforce lehetőséget** az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-salesforce"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése a Salesforce-hoz

Konfigurálja és tesztelje az Azure AD SSO-t a Salesforce segítségével egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a salesforce kapcsolódó felhasználója között.

Az Azure AD SSO konfigurálásához és teszteléséhez a Salesforce segítségével hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    * **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    * **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja a Salesforce Egyszeri bejelentkezést](#configure-salesforce-sso)** – az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
    * **[Hozzon létre Salesforce tesztfelhasználót](#create-salesforce-test-user)** – hogy b.Simon megfelelője legyen a Salesforce-ban, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Salesforce** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza **az egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A **Bejelentkezési URL-cím** mezőbe írja be az értéket a következő minta használatával:

    Vállalati fiók:`https://<subdomain>.my.salesforce.com`

    Fejlesztői fiók:`https://<subdomain>-dev-ed.my.salesforce.com`

    b. Az **Azonosító** mezőbe írja be az értéket a következő minta használatával:

    Vállalati fiók:`https://<subdomain>.my.salesforce.com`

    Fejlesztői fiók:`https://<subdomain>-dev-ed.my.salesforce.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. Lépjen kapcsolatba [a Salesforce ügyféltámogatási csapatával,](https://help.salesforce.com/support) hogy megkapja ezeket az értékeket.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az SAML aláíró tanúsítvány szakaszban keresse meg az **összevonási** **metaadatok XML-jét,** és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A **Salesforce beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t a Salesforce-hoz való hozzáférés biztosításával.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Salesforce**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-salesforce-sso"></a>Salesforce-sso konfigurálása

1. A Salesforce konfigurációjának automatizálásához telepítenie kell a **My Apps Secure Sign-in böngészőbővítményt** **a Bővítmény telepítése**gombra kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

1. Miután hozzátette a bővítményt a böngészőhöz, kattintson a **Salesforce beállítása** gombra, amely a Salesforce Single Sign-On alkalmazáshoz irányítja. Itt adja meg a rendszergazdai hitelesítő adatokat a Salesforce Single Sign-On-ba való bejelentkezéshez. A böngészőbővítmény automatikusan konfigurálja az alkalmazást, és automatizálja a 3-13.

    ![Beállítási konfiguráció](common/setup-sso.png)

1. Ha manuálisan szeretné beállítani a Salesforce-ot, nyisson meg egy új böngészőablakot, és jelentkezzen be a Salesforce vállalati webhelyére rendszergazdaként, és hajtsa végre az alábbi lépéseket:

1. Kattintson a **beállítás** gombra a lap jobb felső sarkában található **Beállítások ikonra.**

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-tutorial/configure1.png)

1. Görgessen le a navigációs ablak **BEÁLLÍTÁSOK eleméhez,** és kattintson az **Identitás gombra** a kapcsolódó szakasz kibontásához. Ezután kattintson **az Egyszeri bejelentkezési beállítások gombra.**

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-tutorial/sf-admin-sso.png)

1. Az **Egyszeri bejelentkezés beállításai** lapon kattintson a **Szerkesztés** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-tutorial/sf-admin-sso-edit.png)

    > [!NOTE]
    > Ha nem tudja engedélyezni az egyszeri bejelentkezés beállításait a Salesforce-fiókjához, lehet, hogy kapcsolatba kell [lépnie a Salesforce ügyféltámogatási csapatával.](https://help.salesforce.com/support)

1. Válassza **az SAML Enabled**lehetőséget, majd kattintson a Mentés **gombra.**

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-tutorial/sf-enable-saml.png)

1. Az SAML egyszeri bejelentkezési beállításainak konfigurálásához kattintson az **Új a metaadatfájlból gombra.**

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-tutorial/sf-admin-sso-new.png)

1. Kattintson a **Fájl kiválasztása gombra** az Azure portalról letöltött METAadat-XML-fájl feltöltéséhez, majd kattintson a **Létrehozás gombra.**

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-tutorial/xmlchoose.png)

1. Az **SAML Egyszeri bejelentkezési beállítások** lapon a mezők automatikusan feljönnek, válassza a **Felhasználói kiépítés engedélyezve** lehetőséget, majd kattintson a **Mentés gombra.**

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-tutorial/salesforcexml.png)

1. A Salesforce bal oldali navigációs ablaktábláján kattintson a **Vállalati beállítások** elemre a kapcsolódó szakasz kibontásához, majd kattintson a **Saját tartomány**parancsra.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-tutorial/sf-my-domain.png)

1. Görgessen le a **Hitelesítési konfiguráció** szakaszhoz, és kattintson a **Szerkesztés** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-tutorial/sf-edit-auth-config.png)

1. A **Hitelesítési konfiguráció** szakaszban ellenőrizze az **AzureSSO** **hitelesítési szolgáltatásként** az SAML SSO-konfiguráció, majd kattintson a **Mentés gombra.**

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-tutorial/sf-auth-config.png)

    > [!NOTE]
    > Ha egynél több hitelesítési szolgáltatás van kiválasztva, a rendszer megkérdezi a felhasználókat, hogy melyik hitelesítési szolgáltatással szeretnének bejelentkezni, miközben egyszeri bejelentkezést szeretne a Salesforce-környezetbe. Ha nem szeretné, hogy megtörténjen, akkor **hagyja az összes többi hitelesítési szolgáltatást bejelölve.**

### <a name="create-salesforce-test-user"></a>Salesforce tesztfelhasználó létrehozása

Ebben a szakaszban egy B.Simon nevű felhasználó jön létre a Salesforce-ban. A Salesforce támogatja a just-in-time kiépítést, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nincs műveletelem. Ha a felhasználó még nem létezik a Salesforce-ban, akkor egy új jön létre, amikor megpróbál hozzáférni a Salesforce-hoz. A Salesforce támogatja az automatikus felhasználói kiépítést is, itt további [részleteket](salesforce-provisioning-tutorial.md) talál az automatikus felhasználói kiépítés konfigurálásáról.

## <a name="test-sso"></a>SSO tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelEn a Salesforce csempére kattint, automatikusan be kell jelentkeznie arra a Salesforce-ba, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="test-sso-for-salesforce-mobile"></a>SSO tesztelése salesforce (mobil)

1. Nyissa meg a Salesforce mobilalkalmazást. A bejelentkezési lapon kattintson az **Egyéni tartomány használata gombra.**

    ![Salesforce mobilalkalmazás](media/salesforce-tutorial/mobile-app1.png)

1. Az **Egyéni tartomány** mezőbe írja be a regisztrált egyéni tartománynevet, és kattintson a **Folytatás**gombra.

    ![Salesforce mobilalkalmazás](media/salesforce-tutorial/mobile-app2.png)

1. Adja meg az Azure AD hitelesítő adatait a Salesforce alkalmazásba való bejelentkezéshez, majd kattintson a **Tovább**gombra.

    ![Salesforce mobilalkalmazás](media/salesforce-tutorial/mobile-app3.png)

1. Az **Access engedélyezése** lapon az alábbi módon kattintson az **Engedélyezés** gombra a Salesforce alkalmazás elérésének engedélyezéséhez elemre.

    ![Salesforce mobilalkalmazás](media/salesforce-tutorial/mobile-app4.png)

1. Végül a sikeres bejelentkezés után megjelenik az alkalmazás kezdőlapja.

    ![Salesforce mobilalkalmazás,](media/salesforce-tutorial/mobile-app5.png) ![A Salesforce mobilalkalmazás](media/salesforce-tutorial/mobile-app6.png)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Felhasználói kiépítés konfigurálása](salesforce-provisioning-tutorial.md)

- [Próbálja ki a Salesforce-ot az Azure AD-vel](https://aad.portal.azure.com)

- [Mi a munkamenet-vezérlés a Microsoft Cloud App Security alkalmazásban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [A Salesforce védelme fejlett láthatósággal és vezérlőkkel](https://docs.microsoft.com/cloud-app-security/protect-salesforce)
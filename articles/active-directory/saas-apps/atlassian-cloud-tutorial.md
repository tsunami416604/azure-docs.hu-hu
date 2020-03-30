---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az Atlassian Cloudtal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az Atlassian Cloud között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: celested
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/27/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46c48d6f7d751e6d767921fad21e6213c6b1203b
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384024"
---
# <a name="tutorial-integrate-atlassian-cloud-with-azure-active-directory"></a>Oktatóanyag: Az Atlassian Cloud integrálása az Azure Active Directoryval

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az Atlassian Cloudot az Azure Active Directoryval (Azure AD). Ha integrálja az Atlassian Cloudot az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér az Atlassian Cloud szolgáltatáshoz.
* Lehetővé teszi, hogy a felhasználók automatikusan bejelentkezve az Atlassian Cloud az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [itt](https://azure.microsoft.com/pricing/free-trial/)egy hónapos ingyenes próbaverziót kaphat.
* Az Atlassian Cloud egyszeri bejelentkezéssel (SSO) engedélyezett előfizetéssel rendelkezik.
* Az Atlassian Cloud-termékek saml-alapú bejelentkezésének engedélyezéséhez be kell állítania az Atlassian Access alkalmazást. További információ az [Atlassian Access programról.]( https://www.atlassian.com/enterprise/cloud/identity-manager)

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben. 

* Az Atlassian Cloud támogatja az **SP és az IDP** által kezdeményezett sso-t
* Az Atlassian Cloud támogatja a [felhasználó automatikus kiépítését és a megszüntetést](atlassian-cloud-provisioning-tutorial.md)
* Az Atlassian Cloud konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből terjed. [Megtudhatja, hogy miként kényszerítheti ki a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)
## <a name="adding-atlassian-cloud-from-the-gallery"></a>Atlassian Cloud hozzáadása a galériából

Az Atlassian Cloud Azure AD-be való integrációjának konfigurálásához hozzá kell adnia az Atlassian Cloud-ot a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből szakaszban** írja be az **Atlassian Cloud** kifejezést a keresőmezőbe.
1. Válassza az **Atlassian Cloud** elemet az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD SSO-t az Atlassian Cloud segítségével egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó az Atlassian Cloudban.

Az Azure AD SSO atlaszfelhővel való konfigurálásához és teszteléséhez hajtsa végre az alábbi építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    * **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    * **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja az Atlassian Cloud Egyszeri bejelentkezést](#configure-atlassian-cloud-sso)** – az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
    * **[Hozzon létre Atlassian Cloud tesztfelhasználót](#create-atlassian-cloud-test-user)** – hogy b.Simon megfelelője legyen az Atlassian Cloudban, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)az **Atlassian Cloud** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza **az Egyszeri bejelentkezés**lehetőséget.
1. Az **Egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban, ha az alkalmazást **IDP** által kezdeményezett módban szeretné konfigurálni, adja meg a következő mezők értékeit:

    a. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`https://auth.atlassian.com/saml/<unique ID>`

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://auth.atlassian.com/login/callback?connection=saml-<unique ID>`

    c. Kattintson **a További URL-címek beállítása gombra.**

    d. A **Továbbítási állapot** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<instancename>.atlassian.net`

    > [!NOTE]
    > Az előző értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval és a válasz URL-címével. Ezeket a valós értékeket az **Atlassian Cloud SAML konfigurációs** képernyőről kapja, amelyet később az **Atlassian Cloud SSO konfigurálása** az oktatóanyagban ismertetett 7.

1. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<instancename>.atlassian.net`

    > [!NOTE]
    > A Bejelentkezés url-cím értéke nem valós. Illessze be az értéket abból a példányból, amelyet az Atlassian Cloud felügyeleti portálra való bejelentkezéshez használ.

    ![Egyszeri bejelentkezés konfigurálása](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-10.png)

1. Az Atlassian Cloud-alkalmazás elvárja az SAML-állításokat egy adott formátumban, amely megköveteli, hogy egyéni attribútum-hozzárendeléseket adjon hozzá az SAML token attribútumok konfigurációjához. A következő képernyőképen az alapértelmezett attribútumok listája látható, ahol a **nameidentifier** a **user.userprincipalname**. Az Atlassian Cloud alkalmazás elvárja, hogy a **névazonosító** le legyen képezve **a user.mail alkalmazással,** ezért az attribútumleképezést a **Szerkesztés** ikonra kattintva kell módosítania, és módosítania kell az attribútumleképezést.

    ![image](common/edit-attribute.png)

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon keresse meg az **SAML aláíró tanúsítvány szakaszát,** keresse meg a **Tanúsítvány (Base64)** lehetőséget, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. Az **Atlassian Cloud beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

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

Ebben a szakaszban lehetővé teszi b.Simon azure egyszeri bejelentkezés t az Atlassian Cloud hozzáférést biztosít.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza az **Atlassian Cloud**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-atlassian-cloud-sso"></a>Atlassian Cloud SSO konfigurálása

1. Az Atlassian Cloud konfigurációjának automatizálásához telepítenie kell a **My Apps Secure Sign-in böngészőbővítményt** **a Bővítmény telepítése**gombra kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

1. Miután hozzáadja a bővítményt a böngészőhöz, kattintson a **Setup Atlassian Cloud** gombra, amely az Atlassian Cloud alkalmazáshoz irányítja. Innen adja meg a rendszergazdai hitelesítő adatokat az Atlassian Cloud szolgáltatásba való bejelentkezéshez. A böngésző bővítmény automatikusan konfigurálja az alkalmazást, és automatizálja a 3-7.

    ![Beállítási konfiguráció](common/setup-sso.png)

1. Ha manuálisan szeretné beállítani az Atlassian Cloud-ot, nyisson meg egy új böngészőablakot, és jelentkezzen be rendszergazdaként az Atlassian Cloud vállalati webhelyére, és hajtsa végre az alábbi lépéseket:

1. Az egyszeri bejelentkezés konfigurálása előtt ellenőriznie kell a tartományt. További információ: [Atlassian domain ellenőrzési](https://confluence.atlassian.com/cloud/domain-verification-873871234.html) dokumentum.

1. A bal oldali ablaktáblában válassza a Security**SAML single sign-on** **(Biztonsági** > SAML egyszeri bejelentkezés) lehetőséget. Ha még nem tette meg, iratkozzon fel az Atlassian Identity Manager szolgáltatásra.

    ![Egyszeri bejelentkezés konfigurálása](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-11.png)

1. Az **SAML konfiguráció hozzáadása** ablakban tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-12.png)

    a. Az **identitásszolgáltató entitásazonosítója** mezőbe illessze be az **Azure AD-azonosítót,** amelyet az Azure Portalról másolt.

    b. Az **identitásszolgáltató sso URL-címe** mezőbe illessze be az Azure Portalról másolt **bejelentkezési URL-címet.**

    c. Nyissa meg a letöltött tanúsítványt az Azure Portalról egy .txt fájlban, másolja az értéket (a *Tanúsítvány kezdete* és a tanúsítvány *befejezése* sorok nélkül), majd illessze be a **Nyilvános X509 tanúsítvány** mezőbe.

    d. Kattintson **a Konfiguráció mentése gombra.**

1. Annak érdekében, hogy a megfelelő URL-eket állítsa be, frissítse az Azure AD-beállításokat az alábbi módon:

    ![Egyszeri bejelentkezés konfigurálása](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-13.png)

    a. Az SAML ablakban másolja az **SP-identitásazonosítót,** majd az Azure Portalon az Atlassian Cloud **Basic SAML konfiguráció ja)** aláilleszti az **azonosító** mezőbe.

    b. Az SAML ablakban másolja az **SP helyességi feltétel fogyasztói szolgáltatás URL-címét,** majd az Azure Portalon az Atlassian Cloud **Basic SAML konfiguráció alatt**illessze be a Válasz **URL-cím** mezőjébe. A bejelentkezési URL az Atlassian Cloud bérlői URL-címe.

    > [!NOTE]
    > Ha Ön már meglévő ügyfél, miután frissítette az **SP identity-azonosító** és az **SP-helyességi feltétel szolgáltatás URL-értékeit** az Azure Portalon, válassza az **Igen lehetőséget, a konfiguráció frissítése**lehetőséget. Ha Ön új ügyfél, kihagyhatja ezt a lépést.

### <a name="create-atlassian-cloud-test-user"></a>Atlassian Cloud tesztfelhasználó létrehozása

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezhessenek az Atlassian Cloud szolgáltatásba, manuálisan kell kiépíteni a felhasználói fiókokat az Atlassian Cloud szolgáltatásban az alábbi módon:

1. A **Felügyelet** ablaktáblán válassza a **Felhasználók**lehetőséget.

    ![Az Atlassian Cloud Users hivatkozása](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-14.png)

1. Ha felhasználót szeretne létrehozni az Atlassian Cloud szolgáltatásban, válassza **a Felhasználó meghívása**lehetőséget.

    ![Atlassian Cloud-felhasználó létrehozása](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-15.png)

1. Az **E-mail cím** mezőbe írja be a felhasználó e-mail címét, majd rendelje hozzá az alkalmazáshoz való hozzáférést.

    ![Atlassian Cloud-felhasználó létrehozása](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-16.png)

1. Ha e-mailben szeretne meghívót küldeni a felhasználónak, válassza **a Felhasználók meghívása**lehetőséget. A rendszer e-mailben meghívót küld a felhasználónak, és a meghívás elfogadása után a felhasználó aktív a rendszerben.

> [!NOTE]
> A felhasználók tömeges létrehozásához jelölje be a Felhasználók szakasz **Tömeges létrehozás** **gombját.**

### <a name="test-sso"></a>SSO tesztelése

Amikor kiválasztja az Atlassian Cloud csempét a Hozzáférési panelen, automatikusan be kell jelentkeznie az Atlassian Cloud-ba, amelyhez beállítja az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki az Atlassian Cloud szolgáltatást az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlés a Microsoft Cloud App Security alkalmazásban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Az Atlassian Cloud védelme fejlett láthatósággal és vezérléssel](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
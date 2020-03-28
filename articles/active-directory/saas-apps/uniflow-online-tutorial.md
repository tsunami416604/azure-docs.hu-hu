---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az uniFLOW Online szolgáltatással | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az uniFLOW Online között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28313d27-638c-4d50-92ad-d093f2ae9ecf
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f26af813fcd4032aabce2305ac8845307d1fca65
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76262129"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-uniflow-online"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az uniFLOW Online-nal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az uniFLOW Online szolgáltatást az Azure Active Directoryval (Azure AD). Ha integrálja az uniFLOW Online-t az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér az uniFLOW Online-hoz.
* Engedélyezze a felhasználók nak, hogy az UniFLOW Online-ba jelentkezzenek be az Azure AD-fiókjukkal.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* uniFLOW Online bérlő.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* uniFLOW Online támogatja **sp** kezdeményezett SSO

## <a name="adding-uniflow-online-from-the-gallery"></a>UniFLOW Online hozzáadása a galériából

Az uniFLOW Online Azure AD-be való integrációjának konfigurálásához hozzá kell adnia az uniFLOW Online-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **Hozzáadás a gyűjteményből szakaszban** írja be az **uniFLOW Online** kifejezést a keresőmezőbe.
1. Válassza **az uniFLOW Online** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-uniflow-online"></a>Az Azure AD egyszeri bejelentkezésének konfigurálása és tesztelése az uniFLOW Online-hoz

Konfigurálja és tesztelje az Azure AD SSO-t az uniFLOW Online-nal egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó uniFLOW Online között.

Az Azure AD SSO uniFLOW Online szolgáltatással való konfigurálásához és teszteléséhez hajtsa végre az alábbi építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
   1. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
   1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja uniFLOW Online Egyszeri bejelentkezés](#configure-uniflow-online-sso)** - konfigurálni az egyszeri bejelentkezési beállításokat az alkalmazás oldalán.
    * **[Jelentkezzen be az uniFLOW Online-ba a létrehozott tesztfelhasználó használatával](#sign-in-to-uniflow-online-using-the-created-test-user)** - a felhasználói bejelentkezés teszteléséhez az alkalmazás oldalán.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)az **uniFLOW Online alkalmazásintegrációs** lapon keresse meg a **Kezelés szakaszt,** és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A Bejelentkezés az **URL-cím** mezőbe írja be az URL-címet a következő minta használatával:

    | | |
    |-|-|
    | `https://<tenant_domain_name>.eu.uniflowonline.com`|
    | `https://<tenant_domain_name>.us.uniflowonline.com`|
    | `https://<tenant_domain_name>.sg.uniflowonline.com`|
    | `https://<tenant_domain_name>.jp.uniflowonline.com`|
    | `https://<tenant_domain_name>.au.uniflowonline.com`|

    b. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő minta használatával:

    | | |
    |-|-|
    | `https://<tenant_domain_name>.eu.uniflowonline.com`|
    | `https://<tenant_domain_name>.us.uniflowonline.com`|
    | `https://<tenant_domain_name>.sg.uniflowonline.com`|
    | `https://<tenant_domain_name>.jp.uniflowonline.com`|
    | `https://<tenant_domain_name>.au.uniflowonline.com`|

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Bejelentkezési URL-címmel és azonosítóval. Lépjen kapcsolatba [az uniFLOW Online ügyféltámogatási csapatával,](mailto:support@nt-ware.com) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákra is hivatkozhat, vagy az uniFLOW Online-bérlőben megjelenő válasz URL-címére hivatkozhat.

1. UniFLOW Online alkalmazás elvárja az SAML állításokat egy adott formátumban, amely megköveteli, hogy egyéni attribútum leképezések az SAML token attribútumok konfigurációját. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentieken kívül az uniFLOW Online alkalmazás arra számít, hogy néhány további attribútum ot kell visszaadni az SAML válaszban, amelyek az alábbiakban láthatók. Ezek az attribútumok is előre ki vannak töltve, de áttekintheti őket a követelmények nek megfelelően.

    | Név |  Forrás attribútuma|
    | -----------| --------------- |
    | Displayname | user.displayname |
    | Felhasználónév | user.onpremisessamaccountname |

   > [!NOTE]
   > Az `user.onpremisessamaccountname` attribútum csak akkor tartalmaz értéket, ha az Azure AD-felhasználók szinkronizálása a helyi Windows Active Directoryból történik.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány szakaszában** kattintson a Másolás gombra az **Alkalmazásösszevonás metaadat-címének** másolásához és mentéséhez a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t uniFLOW Online hozzáférést biztosít.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza az **uniFLOW Online**lehetőséget.
1. Az alkalmazás áttekintése lapon nyissa meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

   ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

> [!NOTE]
> Ha azt szeretné, hogy minden felhasználó manuális hozzárendelés nélkül férhessen hozzá az alkalmazáshoz, nyissa meg a **Kezelés szakaszt,** és válassza a **Tulajdonságok lehetőséget.** Ezután módosítsa a **Felhasználó hozzárendelésszükséges** paramétert **NEM -re.**

## <a name="configure-uniflow-online-sso"></a>UniFLOW Online sso konfigurálása

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként az uniFLOW Online webhelyére.

1. A bal oldali navigációs panelen válassza a **Felhasználó** fület.

    ![uniFLOW online konfiguráció](./media/uniflow-online-tutorial/configure1.png)

1. Kattintson **az Identitásszolgáltatók gombra.**

    ![uniFLOW online konfiguráció](./media/uniflow-online-tutorial/configure2.png)

1. Kattintson az **Identitásszolgáltató hozzáadása gombra.**

    ![uniFLOW online konfiguráció](./media/uniflow-online-tutorial/configure3.png)

1. Az **ADD IDENTITY PROVIDER (IDENTITÁSSZOLGÁLTATÓ HOZZÁADÁSA)** szakaszban hajtsa végre az alábbi lépéseket:


    ![uniFLOW online konfiguráció](./media/uniflow-online-tutorial/configure4.png)

    a. Írja be az Ex: *AzureAD SSO*megjelenítendő nevet.

    b. A **Szolgáltató típusa mezőben**válassza a **WS-Fed** lehetőséget a legördülő menüből.

    c. A **WS-Fed típusú,** válassza ki az **Azure Active Directory** lehetőséget a legördülő menüből.

    d. Kattintson a **Mentés** gombra.

1. Az **Általános** lapon hajtsa végre az alábbi lépéseket:

    ![uniFLOW online konfiguráció](./media/uniflow-online-tutorial/configure5.png)

    a. Írja be az Ex: *AzureAD SSO*megjelenítendő nevet.

    b. Válassza a **From URL** lehetőséget az **ADFS összevonási metaadatokhoz.**

    c. Az **összevonási metaadatok url** szövegdobozába illessze be az **App Federation metaadat-url-címét,** amelyet az Azure Portalról másolt.

    d. Válassza **az Identitásszolgáltatót** **engedélyezve ként.**

    e. Válassza **az Automatikus felhasználói regisztráció** **aktiválásként**lehetőséget.

    f. Kattintson a **Mentés** gombra.

### <a name="sign-in-to-uniflow-online-using-the-created-test-user"></a>Bejelentkezés az uniflow online szolgáltatásba a létrehozott tesztfelhasználóhasználatával

1. Egy másik böngészőablakban nyissa meg a bérlő uniFLOW Online URL-címét.

1. Válassza ki a korábban létrehozott identitásszolgáltatót az Azure AD-példányon keresztül történő bejelentkezéshez.

1. Jelentkezzen be a tesztfelhasználóval.

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki az uniFLOW Online szolgáltatást az Azure AD-vel](https://aad.portal.azure.com/)

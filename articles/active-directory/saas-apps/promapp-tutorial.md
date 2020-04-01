---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Nintex Promappalkalmazással | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Nintex Promapp között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 418d0601-6e7a-4997-a683-73fa30a2cfb5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/30/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 581c850801c153996031378cbf470457264cad3d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76984456"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-nintex-promapp"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Nintex Promapp alkalmazással

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Nintex Promappot az Azure Active Directoryval (Azure AD). Ha integrálja a Nintex Promappot az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Nintex Promapp.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve nintex Promapp az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Nintex Promapp egyszeri bejelentkezés (SSO) engedélyezve van előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* A Nintex Promapp támogatja az **SP és az IDP** által kezdeményezett SSO-t
* A Nintex Promapp támogatja a **Just In Time** felhasználói kiépítést

## <a name="adding-nintex-promapp-from-the-gallery"></a>Nintex Promapp hozzáadása a galériából

A Nintex Promapp azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Nintex Promapp-ot a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből szakaszban** írja be a **Nintex Promapp** kifejezést a keresőmezőbe.
1. Válassza a **Nintex Promapp** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-nintex-promapp"></a>Konfigurálja és tesztelje az Azure AD egyszeri bejelentkezését a Nintex Promapphoz

Konfigurálja és tesztelje az Azure AD SSO-t a Nintex Promapp segítségével egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a nintex promapp kapcsolódó felhasználója között.

Az Azure AD SSO konfigurálásához és teszteléséhez a Nintex Promapp alkalmazásával hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    * **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    * **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja a Nintex Promapp SSO-t](#configure-nintex-promapp-sso)** - az alkalmazás oldalon az egyszeri bejelentkezési beállítások konfigurálásához.
    * **[Hozzon létre Nintex Promapp teszt felhasználó](#create-nintex-promapp-test-user)** - egy megfelelője B.Simon a Nintex Promapp, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Nintex Promapp** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza ki **az egyszeri bejelentkezést.**
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban, ha az alkalmazást **IDP** által kezdeményezett módban szeretné konfigurálni, adja meg a következő mezők értékeit:

    1. Az **Azonosító** mezőbe írjon be egy URL-címet a következő mintába:

        | |
        |--|
        | `https://go.promapp.com/TENANTNAME/`|
        | `https://au.promapp.com/TENANTNAME/`|
        | `https://us.promapp.com/TENANTNAME/`|
        | `https://eu.promapp.com/TENANTNAME/`|
        | `https://ca.promapp.com/TENANTNAME/`|
        |   |

       > [!NOTE]
       > Az Azure AD-integráció a Nintex Promapp jelenleg csak a szolgáltatás által kezdeményezett hitelesítéshez van konfigurálva. (Ez azt, hogy megy a Nintex Promapp URL kezdeményezi a hitelesítési folyamatot.) A **Válasz URL-címe** mező azonban kötelező mező.

    1. A **Válasz URL-címe** mezőbe írjon be egy URL-címet a következő mintába:

       `https://<DOMAINNAME>.promapp.com/TENANTNAME/saml/authenticate.aspx`

1. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő mintába:`https://<DOMAINNAME>.promapp.com/TENANTNAME/saml/authenticate`

    > [!NOTE]
    > Ezek az értékek helyőrzők. A tényleges azonosítót, a válasz URL-címét és a bejelentkezési URL-címet kell használnia. Lépjen kapcsolatba a [Nintex Promapp támogatási csapatával](https://www.promapp.com/about-us/contact-us/) az értékek értéséhez. Az Azure Portal **alapszintű SAML-konfiguráció** párbeszédpanelén látható mintákra is hivatkozhat.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában keresse meg a **Tanúsítvány (Base64)** lehetőséget, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Nintex Promapp beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t a Nintex Promapp használatával.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Nintex Promapp**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-nintex-promapp-sso"></a>Nintex Promapp SSO konfigurálása

1. Jelentkezzen be a Nintex Promapp cég webhelyére rendszergazdaként.

2. Az ablak tetején található menüben válassza a **Rendszergazda**lehetőséget:

    ![Rendszergazda kiválasztása][12]

3. Válassza **a Konfigurálás**lehetőséget:

    ![Konfigurálás kiválasztása][13]

4. A **Biztonság** párbeszédpanelen tegye a következő lépéseket.

    ![Biztonság párbeszédpanel][14]

    1. Illessze be az Azure Portalról másolt **bejelentkezési URL-címet** az **Egyszeri bejelentkezés URL-címmezőjébe.**

    1. Az **Egyszeri bejelentkezés - Egyszeri bejelentkezési mód** listában válassza a **Választható**lehetőséget. Kattintson a **Mentés** gombra.

       > [!NOTE]
       > Az opcionális mód csak tesztelésre szolgál. Miután elégedett a konfigurációval, válassza **az Egyszeri** bejelentkezés – Egyszeri bejelentkezési mód listában a Kötelező lehetőséget, hogy minden felhasználót az Azure AD-vel való hitelesítésre kényszerítsen. **SSO - Single Sign-on Mode**

    1. A Jegyzettömbben nyissa meg az előző szakaszban letöltött tanúsítványt. Másolja a tanúsítvány tartalmát az első sor (**-----BEGIN CERTIFICATE-----)** vagy az utolsó sor (**-----END CERTIFICATE-----**) nélkül. Illessze be a tanúsítvány tartalmát az **SSO-x.509 tanúsítvány** mezőbe, majd válassza a **Mentés gombot.**

### <a name="create-nintex-promapp-test-user"></a>Nintex Promapp tesztfelhasználó létrehozása

Ebben a szakaszban egy B.Simon nevű felhasználó jön létre a Nintex Promapp. A Nintex Promapp támogatja a just-in-time felhasználói kiépítést, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nincs műveletelem. Ha a felhasználó már nem létezik a Nintex Promapp, egy új jön létre a hitelesítés után.

## <a name="test-sso"></a>SSO tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési paneln a Nintex Promapp csempére kattint, automatikusan be kell jelentkeznie a Nintex Promappba, amelyhez beállítja az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a Nintex Promappot az Azure AD-vel](https://aad.portal.azure.com/)

<!--Image references-->

[12]: ./media/promapp-tutorial/tutorial_promapp_05.png
[13]: ./media/promapp-tutorial/tutorial_promapp_06.png
[14]: ./media/promapp-tutorial/tutorial_promapp_07.png
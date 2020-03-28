---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a GitHubbal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a GitHub között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 8761f5ca-c57c-4a7e-bf14-ac0421bd3b5e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e812e1b03637a3ecd7a45f02664c4e3547f1aef1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79138977"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-github"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a GitHubbal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a GitHubot az Azure Active Directoryval (Azure AD). Ha integrálja a GitHubot az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a GitHub Enterprise Cloud Organization.Control in Azure AD who has access to your GitHub Enterprise Cloud Organization.
* A GitHub Enterprise Cloud Organization egyetlen központi helyen – az Azure Portalon – való hozzáférés kezelése.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a GitHubbal a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* A [GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise)szolgáltatásban létrehozott GitHub-szervezet, amelyhez a [GitHub Enterprise számlázási csomagszükséges](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A GitHub támogatja az **SP** által kezdeményezett SSO-t

* A GitHub támogatja [ **az automatikus** felhasználói kiépítést (szervezeti meghívók)](github-provisioning-tutorial.md)
* A GitHub konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből terjed. [Megtudhatja, hogy miként kényszerítheti ki a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-github-from-the-gallery"></a>GitHub hozzáadása a galériából

A GitHub Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a GitHubot a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a katalógusból szakaszban** írja be a **GitHub** a keresőmezőbe.
1. Válassza ki a **GitHub** az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.


## <a name="configure-and-test-azure-ad-single-sign-on-for-github"></a>Konfigurálja és tesztelje az Azure AD egyszeri bejelentkezését a GitHubhoz

Konfigurálja és tesztelje az Azure AD SSO-t a GitHubbal egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a GitHub kapcsolódó felhasználója között.

Az Azure AD SSO githubbal való konfigurálásához és teszteléséhez hajtsa végre az alábbi építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    * **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    * **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja a GitHub SSO-t](#configure-github-sso)** – az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
    * **[Hozzon létre GitHub-tesztfelhasználót](#create-github-test-user)** – b.Simon megfelelőjével a GitHubon, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **GitHub-alkalmazásintegrációs** lapon keresse meg a **Kezelés szakaszt,** és válassza ki **az egyszeri bejelentkezést.**
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

   a. A Bejelentkezés az **URL-cím** mezőbe írja be az URL-címet a következő minta használatával:`https://github.com/orgs/<entity-id>/sso`

    b. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő minta használatával:`https://github.com/orgs/<entity-id>`

    > [!NOTE]
    > Kérjük, vegye figyelembe, hogy ezek nem a valós értékek. Ezeket az értékeket a tényleges Bejelentkezési URL-címmel és azonosítóval kell frissítenie. Itt azt javasoljuk, hogy használja a karakterlánc egyedi értékét az azonosítóban. Nyissa meg a GitHub Felügyeleti szakaszt, és olvassa el ezeket az értékeket.

5. A GitHub-alkalmazás elvárja az SAML-állításokat egy adott formátumban, amely megköveteli, hogy egyéni attribútum-hozzárendelések hozzáadása az SAML token attribútumok konfigurációjához. A következő képernyőképen az alapértelmezett attribútumok listája látható, ahol a **nameidentifier** a **user.userprincipalname**. A GitHub-alkalmazás elvárja, hogy a **névazonosító** le legyen képezve **a user.mail alkalmazással,** ezért az attribútumleképezést a **Szerkesztés** ikonra kattintva kell módosítania, és módosítania kell az attribútumleképezést.

    ![image](common/edit-attribute.png)

6. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

7. A **GitHub beállítása** szakaszban másolja a megfelelő URL-cím(ek) a követelmény szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure-hirdetésazonosító

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés használatával a GitHub hoz való hozzáférés biztosításával.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **GitHub**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-github-sso"></a>A GitHub SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a GitHub-szervezeti webhelyére rendszergazdaként.

2. Keresse meg a **Beállítások lapot,** és kattintson a Biztonság gombra. **Security**

    ![Beállítások](./media/github-tutorial/tutorial_github_config_github_03.png)

3. Jelölje be az **SAML-hitelesítés engedélyezése** jelölőnégyzetet, és felfedje az egyszeri bejelentkezés konfigurációs mezőket. Ezután használja az egyszeri bejelentkezési URL-cím érték az egyszeri bejelentkezési URL-cím az Azure AD-konfigurációban.

    ![Beállítások](./media/github-tutorial/tutorial_github_config_github_13.png)

4. Konfigurálja a következő mezőket:

    ![Beállítások](./media/github-tutorial/tutorial_github_config_github_051.png)

    a. A **Bejelentkezés url-cím** szövegmezőbe illessze be **a bejelentkezési URL-cím,** amely et az Azure Portalról másolt.

    b. A **Kiállító** szövegmezőbe illessze be az **Azure AD-azonosító** értékét, amelyet az Azure Portalról másolt.

    c. Nyissa meg a letöltött tanúsítványt az Azure Portalról a jegyzettömbben, illessze be a tartalmat a **Nyilvános tanúsítvány** szövegmezőbe.

    d. Kattintson a **Szerkesztés** ikonra az **aláírási módszer** és a **kivonatolási módszer** **RSA-SHA1** és **SHA1-ről** **RSA-SHA256** és **SHA256-ra** történő szerkesztéséhez az alábbiak szerint.
    
    e. Frissítse a **helyességi feltétel fogyasztói szolgáltatás URL-címét (Válasz URL-cím)** az alapértelmezett URL-címről, hogy a Github URL-címe megegyezik az Azure-alkalmazás regisztrációjában lévő URL-címmel.

    ![image](./media/github-tutorial/tutorial_github_sha.png)

5. Kattintson az **SAML-konfiguráció tesztelése** elemre annak ellenőrzéséhez, hogy az SSO során nem történt-e érvényesítési hiba vagy hiba.

    ![Beállítások](./media/github-tutorial/tutorial_github_config_github_06.png)

6. Kattintson a **Mentés gombra**

> [!NOTE]
> Egyszeri bejelentkezés a GitHubon hitelesíti magát egy adott szervezet a GitHubon, és nem helyettesíti a GitHub hitelesítését. Ezért ha a felhasználó github.com munkamenete lejárt, előfordulhat, hogy a rendszer a GitHub azonosítójával/jelszavával kell hitelesítenie magát az egyszeri bejelentkezési folyamat során.

### <a name="create-github-test-user"></a>GitHub-tesztfelhasználó létrehozása

Ez a szakasz célja, hogy hozzon létre egy felhasználó nevű Britta Simon a GitHubon. A GitHub támogatja az automatikus felhasználói kiépítést, amely alapértelmezés szerint engedélyezve van. Az automatikus felhasználói [here](github-provisioning-tutorial.md) kiépítés konfigurálásáról itt olvashat bővebben.

**Ha manuálisan kell létrehoznia a felhasználót, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a GitHub vállalati webhelyére rendszergazdaként.

2. Kattintson **a Személyek gombra.**

    ![People](./media/github-tutorial/tutorial_github_config_github_08.png "People")

3. Kattintson **a Tag meghívása gombra.**

    ![Felhasználók meghívása](./media/github-tutorial/tutorial_github_config_github_09.png "Felhasználók meghívása")

4. A **Tag meghívása** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    a. Az **E-mail** mezőbe írja be Britta Simon-fiók e-mail címét.

    ![Személyek meghívása](./media/github-tutorial/tutorial_github_config_github_10.png "Személyek meghívása")

    b. Kattintson **a Meghívó küldése gombra.**

    ![Személyek meghívása](./media/github-tutorial/tutorial_github_config_github_11.png "Személyek meghívása")

    > [!NOTE]
    > Az Azure Active Directory-fiók tulajdonosa kap egy e-mailt, és kövesse a hivatkozást, hogy erősítse meg a fiók, mielőtt aktívvá válik.

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a GitHub csempére kattint, automatikusan be kell jelentkeznie arra a GitHubra, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a GitHubot az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlés a Microsoft Cloud App Security alkalmazásban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

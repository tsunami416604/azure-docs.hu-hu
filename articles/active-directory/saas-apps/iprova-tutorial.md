---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az iProvával | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az iProva között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1eaeef9b-4479-4a9f-b1b2-bc13b857c75c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 98458f8be162d0903f5ea0d1f7d4651d46f78e8e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80048437"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-iprova"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az iProvával

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az iProva-t az Azure Active Directoryval (Azure AD). Ha integrálja az iProva-t az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér az iProva.Control in Azure AD who has access to iProva.
* Engedélyezze, hogy a felhasználók automatikusan bejelentkezve legyenek az iProva-ba az Azure AD-fiókjukkal.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* iProva egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* az iProva támogatja az **SP** által kezdeményezett SSO-t

* Az iProva konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből terjed. [Megtudhatja, hogy miként kényszerítheti ki a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)

## <a name="adding-iprova-from-the-gallery"></a>Az iProva hozzáadása a galériából

Az iProva azure AD-be való integrációjának konfigurálásához hozzá kell adnia az iProva-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből szakaszban** írja be az **iProva** értéket a keresőmezőbe.
1. Válassza ki **az iProva** elemet az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-iprova"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése az iProva számára

Konfigurálja és tesztelje az Azure AD SSO-t az iProvával egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó iProva.

Az Azure AD SSO konfigurálásához és teszteléséhez az iProvával hajtsa végre a következő építőelemeket:

1. **[A konfigurációs információk lekérése az iProva-ból](#retrieve-configuration-information-from-iprova)** - a következő lépések előkészítéseként.
1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
1. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Hozzon létre iProva teszt felhasználó](#create-iprova-test-user)** - egy megfelelője B.Simon az iProva, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[Konfigurálja az iProva SSO-t](#configure-iprova-sso)** - az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="retrieve-configuration-information-from-iprova"></a>Konfigurációs adatok lekérése az iProva-ból

Ebben a szakaszban az adatokat az iProva az Azure AD egyszeri bejelentkezés konfigurálásához.

1. Nyisson meg egy webböngészőt, és lépjen az **iProva SAML2 információs** oldalára a következő URL-minta használatával:

    | | |
    |-|-|
    | `https://SUBDOMAIN.iprova.nl/saml2info`|
    | `https://SUBDOMAIN.iprova.be/saml2info`|
    | | |

    ![Az iProva SAML2 információs oldal megtekintése](media/iprova-tutorial/iprova-saml2-info.png)

1. A böngészőfül nyitva hagyja, miközben egy másik böngészőlapon folytatja a következő lépéseket.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)az **iProva** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza ki **az egyszeri bejelentkezést.**
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    a. Töltse ki a **Bejelentkezési URL-címet** az **iProva SAML2 információs** oldalon a címke **bejelentkezési URL-címe** mögött megjelenő értékkel. Ez a lap továbbra is meg van nyitva a másik böngészőlapon.

    b. Töltse ki az **azonosító** mezőt az **entitásazonosító** mögött az **iProva SAML2 információs** oldalon megjelenő értékkel. Ez a lap továbbra is meg van nyitva a másik böngészőlapon.

    c. Töltse ki a **Válasz URL-t** mezőben az **iProva SAML2 információs** oldalon a **címke válasz URL-címe** mögött megjelenő értékkel. Ez a lap továbbra is meg van nyitva a másik böngészőlapon.

1. Az iProva alkalmazás az SAML-állításokat egy adott formátumban várja, amely megköveteli, hogy egyéni attribútumleképezéseket adjon hozzá az SAML token attribútumok konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. Amellett, hogy a fenti, iProva alkalmazás elvárja, hogy néhány további attribútumok at kell átadni vissza SAML válasz, amely az alábbiakban látható. Ezek az attribútumok is előre ki vannak töltve, de áttekintheti őket a követelmények nek megfelelően.

    | Név | Forrás attribútuma| Névtér  |
    | ---------------| -------- | -----|
    | `samaccountname` | `user.onpremisessamaccountname`| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány szakaszában** kattintson a Másolás gombra az **Alkalmazásösszevonás metaadat-címének** másolásához és mentéséhez a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)

## <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy tesztfelhasználót hoz létre az Azure Portalon B.Simon néven.

1. Az Azure Portal bal oldali ablaktáblájában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd válassza az **Összes felhasználó**lehetőséget.
1. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.
1. A **Felhasználói** tulajdonságok csoportban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőbe írja username@companydomain.extensionbe a mezőt. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet, majd írja le a **Jelszó** mezőben megjelenő értéket.
   1. Kattintson **a Létrehozás gombra.**

## <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés iProva hozzáférés biztosításával.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza az **iProva**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="create-iprova-test-user"></a>IProva tesztfelhasználó létrehozása

1. Jelentkezzen be az iProva-ba a **Rendszergazda** fiókkal.

2. Nyissa meg az **Ugrás a menüt.**

3. Válassza **az Alkalmazáskezelés**lehetőséget.

4. Válassza a **Felhasználók és felhasználói csoportok panelEn** a **Felhasználók** és felhasználói csoportok lehetőséget.

5. Válassza a **Hozzáadás** lehetőséget.

6. A **Felhasználónév** mezőbe írja be a `B.Simon@contoso.com`felhasználó felhasználónevét, például .

7. A **Teljes név** mezőbe írja be a felhasználó teljes nevét, például **B.Simon**.

8. Válassza a **Nincs jelszó (egyszeri bejelentkezés használata)** lehetőséget.

9. Az **E-mail cím** mezőbe írja be a `B.Simon@contoso.com`felhasználó e-mail címét, például .

10. Görgessen le a lap végéig, és válassza a **Befejezés gombot.**

## <a name="configure-iprova-sso"></a>Az iProva SSO konfigurálása

1. Jelentkezzen be az iProva-ba a **Rendszergazda** fiókkal.

2. Nyissa meg az **Ugrás a menüt.**

3. Válassza **az Alkalmazáskezelés**lehetőséget.

4. A **Rendszerbeállítások** panelen válassza az **Általános** lehetőséget.

5. Válassza a **Szerkesztés** elemet.

6. Görgessen le a **Hozzáférés-vezérléshez**.

    ![iProva hozzáférés-vezérlési beállítások](media/iprova-tutorial/iprova-accesscontrol.png)

7. Keresse meg azt a **beállítást, amelyet a felhasználók automatikusan bejelentkeznek a hálózati fiókjukkal,** és változtassa azt **Igen, SAML-hitelesítésre.** Most további lehetőségek jelennek meg.

8. Válassza a **Beállítás** lehetőséget.

9. Válassza a **Tovább lehetőséget.**

10. Az iProva megkérdezi, hogy le szeretné-e tölteni az összevonási adatokat egy URL-ről, vagy feltölteni egy fájlból. Válassza a **From URL** lehetőséget.

    ![Az Azure AD metaadatainak letöltése](media/iprova-tutorial/iprova-download-metadata.png)

11. Illessze be a metaadat-URL-címet mentett az utolsó lépésben az "Azure AD egyszeri bejelentkezés" szakasz utolsó lépésében.

12. Válassza ki a nyíl alakú gombot a metaadatok azure AD-ből való letöltéséhez.

13. Amikor a letöltés befejeződött, megjelenik az **Érvényes összevonási adatfájl érvényességi** üzenete.

14. Válassza a **Tovább lehetőséget.**

15. Most hagyja ki a **Bejelentkezés tesztelése** beállítást, és válassza a **Tovább**gombot.

16. A **Jogcím** a használat hoz mezőben válassza a **Windowsaccountname lehetőséget.**

17. Válassza a **Finish** (Befejezés) elemet.

18. Most visszatér az **Általános beállítások szerkesztése** képernyőre. Görgessen le a lap aljára, és a konfiguráció mentéséhez válassza az **OK gombot.**

## <a name="test-sso"></a>SSO tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a hozzáférési panelen az iProva csempére kattint, automatikusan be kell jelentkeznie az iProva-ba, amelyhez beállította az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki az iProva-t az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlés a Microsoft Cloud App Security alkalmazásban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Hogyan védheti az iProva-t a fejlett láthatóságés vezérlők segítségével?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Saba TalentSpace szolgáltatással | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Saba TalentSpace között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2ca2298d-9a0c-4f14-925c-fa23f2659d28
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/20/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76c13a649e1c1888a11e02b83d969255615cdc67
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77561338"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-saba-talentspace"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Saba TalentSpace szolgáltatással

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Saba TalentSpace-t az Azure Active Directoryval (Azure AD). Ha integrálja a Saba TalentSpace-t az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Saba TalentSpace.Control in Azure AD who has access to Saba TalentSpace.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve a Saba TalentSpace az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Saba TalentSpace egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* A Saba TalentSpace támogatja az **SP** által kezdeményezett SSO-t
* A Saba TalentSpace konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből terjed. [Megtudhatja, hogy miként kényszerítheti ki a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)

## <a name="adding-saba-talentspace-from-the-gallery"></a>Saba TalentSpace hozzáadása a galériából

A Saba TalentSpace Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Saba TalentSpace-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből szakaszban** írja be a **Saba TalentSpace** kifejezést a keresőmezőbe.
1. Válassza a **Saba TalentSpace** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-saba-talentspace"></a>Konfigurálja és tesztelje az Azure AD egyszeri bejelentkezését a Saba TalentSpace-hez

Konfigurálja és tesztelje az Azure AD SSO-t a Saba TalentSpace segítségével egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó a Saba TalentSpace.For SSO to work, you need to establish a link relationship between a Azure AD user and the related user in Saba TalentSpace.

Az Azure AD SSO saba TalentSpace szolgáltatással való konfigurálásához és teszteléséhez hajtsa végre az alábbi építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    * **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    * **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja a Saba TalentSpace Egyszeri bejelentkezést](#configure-saba-talentspace-sso)** – az alkalmazás oldalon az egyszeri bejelentkezési beállítások konfigurálásához.
    * **[Hozzon létre Saba TalentSpace teszt felhasználó](#create-saba-talentspace-test-user)** - egy megfelelője B.Simon a Saba TalentSpace, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Saba TalentSpace** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    a. A Bejelentkezés az **URL-cím** mezőbe írja be az URL-címet a következő minta használatával:`https://global.hgncloud.com/[companyname]/saml/login`

    b. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő minta használatával:`https://global.hgncloud.com/[companyname]/saml/metadata`

    c. A **Válasz URL -címe (Helyességi feltétel fogyasztói szolgáltatás URL-címe)** mezőbe írjon be egy URL-címet a következő minta használatával:`https://global.hgncloud.com/[companyname]/saml/SSO`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Bejelentkezési URL-címmel és azonosítóval. Lépjen kapcsolatba [a Saba TalentSpace ügyféltámogatási csapatával,](https://support.saba.com/) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány csoportjában** kattintson a **Letöltés** gombra, ha letöltheti az **összevonási metaadat-XML-t** a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A **Saba TalentSpace beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

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

Ebben a szakaszban lehetővé teszi b.Simon azure egyszeri bejelentkezés t a Saba TalentSpace hozzáférést biztosít.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Saba TalentSpace**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-saba-talentspace-sso"></a>Saba TalentSpace SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként a **Saba TalentSpace** alkalmazásba.

2. Kattintson a **Beállítások** fülre.
  
    ![Mi az az Azure AD Connect?](./media/halogen-software-tutorial/tutorial-halogen-12.png)

3. A bal oldali navigációs ablakban kattintson az **SAML-konfiguráció gombra.**
  
    ![Mi az az Azure AD Connect?](./media/halogen-software-tutorial/tutorial-halogen-13.png)

4. Az **SAML konfigurációja** lapon hajtsa végre az alábbi lépéseket:

    ![Mi az az Azure AD Connect?](./media/halogen-software-tutorial/tutorial-halogen-14.png)

    a. **Egyedi azonosítóként**válassza a **NameID lehetőséget.**

    b. Az **egyedi azonosító leképezéseként**válassza a **Felhasználónév**lehetőséget.
  
    c. A letöltött metaadatfájl feltöltéséhez kattintson a **Tallózás** gombra a fájl kijelöléséhez, majd **a Fájl feltöltése gombra.**

    d. A konfiguráció teszteléséhez kattintson a **Teszt futtatása**gombra.

    > [!NOTE]
    > Meg kell várnia a következő üzenetet:*"Az SAML teszt befejeződött. Kérjük, zárja be ezt az ablakot*". Ezután zárja be a megnyitott böngészőablakot. Az **SAML engedélyezése** jelölőnégyzet csak akkor engedélyezett, ha a teszt befejeződött.

    e. Válassza **az SAML engedélyezése**lehetőséget.

    f. Kattintson a **Save Changes** (Módosítások mentése) gombra.

### <a name="create-saba-talentspace-test-user"></a>Saba TalentSpace tesztfelhasználó létrehozása

A cél ebben a szakaszban, hogy hozzon létre egy felhasználó nevű Britta Simon a Saba TalentSpace.

**Britta Simon nevű felhasználó létrehozásához a Saba TalentSpace-ben hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be **a Saba TalentSpace** alkalmazásba rendszergazdaként.

2. Kattintson a **Felhasználóközpont** fülre, majd a **Felhasználó létrehozása gombra.**

    ![Mi az az Azure AD Connect?](./media/halogen-software-tutorial/tutorial-halogen-300.png)  

3. Az **Új felhasználó** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![Mi az az Azure AD Connect?](./media/halogen-software-tutorial/tutorial-halogen-301.png)

    a. Az **Utónév** mezőbe írja be a felhasználó keresztnevét, például **B**.

    b. A **Vezetéknév** mezőbe írja be a felhasználó vezetéknevét, például **Simon**.

    c. A **Felhasználónév** mezőbe írja be a **B.Simon**nevet, a felhasználónevet az Azure Portalon.

    d. A **Jelszó** mezőbe írja be b.Simon jelszavát.

    e. Kattintson a **Mentés** gombra.

## <a name="test-sso"></a>SSO tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a Saba TalentSpace csempére kattint, automatikusan be kell jelentkeznie a Saba TalentSpace-be, amelyhez beállítja az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a Saba TalentSpace-t az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlés a Microsoft Cloud App Security alkalmazásban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
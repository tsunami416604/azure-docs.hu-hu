---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Polgári Platformmal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Civic Platform között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1d790454-143e-40ac-b3cb-5a256977b4db
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/25/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ccf124c5a4160715df4e685e405dcd591c49ae7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "68496825"
---
# <a name="tutorial-integrate-civic-platform-with-azure-active-directory"></a>Oktatóanyag: A polgári platform integrálása az Azure Active Directoryval

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Civic Platformot az Azure Active Directoryval (Azure AD). Ha integrálja a Civic Platformot az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Polgári platformhoz.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve a Civic Platform az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [itt](https://azure.microsoft.com/pricing/free-trial/)egy hónapos ingyenes próbaverziót kaphat.
* A Civic Platform egyszeri bejelentkezéssel (SSO) engedélyezett előfizetéssel rendelkezik.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* A Polgári Platform támogatja az **SP** által kezdeményezett sso-t





## <a name="adding-civic-platform-from-the-gallery"></a>Civic Platform hozzáadása a galériából

A Civic Platform Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Civic Platformot a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **Hozzáadás a gyűjteményből szakaszban** írja be a **Polgári platform** kifejezést a keresőmezőbe.
1. Válassza a **Polgári platform** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD SSO-t a Civic Platform segítségével egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó a Civic Platform.

Az Azure AD SSO konfigurálásához és teszteléséhez a Polgári platformon hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a Polgári Platform Egyszeri Bejelentkezés](#configure-civic-platform-sso)** - konfigurálni az egyszeri bejelentkezési beállításokat az alkalmazás oldalán.
3. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre Civic Platform teszt felhasználó](#create-civic-platform-test-user)** - egy megfelelője B.Simon a Polgári platform, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Polgári platform** alkalmazásintegrációs lapján keresse meg a **Kezelés szakaszt,** és válassza **az Egyszeri bejelentkezés**lehetőséget.
1. Az **Egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A Bejelentkezés az **URL-cím** mezőbe írja be az URL-címet a következő minta használatával:`https://<SUBDOMAIN>.accela.com`

    b. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet:`civicplatform.accela.com`

    > [!NOTE]
    > A Bejelentkezés url-cím értéke nem valós. Frissítse ezt az értéket a tényleges Bejelentkezési URL-címmel. Lépjen kapcsolatba [a Civic Platform ügyféltámogatási csapatával,](mailto:skale@accela.com) hogy megkapja ezt az értéket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány szakaszában** kattintson a Másolás gombra az **Alkalmazásösszevonás metaadat-címének** másolásához és mentéséhez a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)

1. Keresse meg az **Azure Active Directory** > **alkalmazás regisztrációit** az Azure AD-ben, válassza ki az alkalmazást.

1. Másolja a **címtár (bérlői) azonosítót,** és tárolja a Jegyzettömbbe.

    ![A könyvtár másolása (bérlőazonosító) és tárolása az alkalmazáskódban](media/civic-platform-tutorial/directoryid.png)

1. Másolja az **alkalmazásazonosítót,** és tárolja a Jegyzettömbbe.

   ![Az alkalmazás (ügyfél) azonosítójának másolása](media/civic-platform-tutorial/applicationid.png)

1. Keresse meg az **Azure Active Directory** > **alkalmazás regisztrációit** az Azure AD-ben, válassza ki az alkalmazást. Válassza **a Tanúsítványok & titkos kulcsok lehetőséget.**

1. Válassza **ki az ügyféltitkokat -> Új ügyféltitok**.

1. Adja meg a titkos kulcsot és az időtartamot. Ha végzett, válassza **a Hozzáadás**lehetőséget.

   > [!NOTE]
   > Az ügyféltitkos lista mentése után megjelenik az ügyféltitkos lista értéke. Másolja ezt az értéket, mert később nem tudja beolvasni a kulcsot.

   ![A titkos érték másolása, mert később nem lehet beolvasni](media/civic-platform-tutorial/secretkey.png)

### <a name="configure-civic-platform-sso"></a>A Polgári platform sso-jának konfigurálása

1. Nyisson meg egy új böngészőablakot, és jelentkezzen be az Atlassian Cloud vállalati webhelyére rendszergazdaként.

1. Kattintson a **Szokásos választási lehetőségek gombra.**

    ![A tanúsítvány letöltési hivatkozása](media/civic-platform-tutorial/standard-choices.png)

1. Hozzon létre egy szabványos választási **ssoconfig**.

1. Keresés **a ssoconfig** és küldje el.

    ![A tanúsítvány letöltési hivatkozása](media/civic-platform-tutorial/sso-config.png)

1. Bontsa ki a SSOCONFIG elemet a piros ra kattintva.

    ![A tanúsítvány letöltési hivatkozása](media/civic-platform-tutorial/sso-config01.png)

1. Adja meg az SSO-val kapcsolatos konfigurációs információkat a következő lépésben:

    ![A tanúsítvány letöltési hivatkozása](media/civic-platform-tutorial/sso-config02.png)

    1. A **applicationid** mezőben adja meg az **Alkalmazásazonosító** értékét, amelyet az Azure Portalról másolt.

    1. Az **ügyféltitkos mezőben** adja meg a **Titkos** értéket, amelyet az Azure Portalról másolt.

    1. A **címtárazonosító** mezőbe írja be a **címtár (bérlői) azonosító** értékét, amelyet az Azure Portalról másolt.

    1. Adja meg az idpName értéket. Pl.: `Azure`- .

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

Ebben a szakaszban lehetővé teszi b.Simon azure egyszeri bejelentkezés t a Polgári platformhoz való hozzáférés biztosításával.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Civic Platform**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-civic-platform-test-user"></a>Civic Platform tesztfelhasználó létrehozása

Ebben a szakaszban hozzon létre egy B.Simon nevű felhasználót a Polgári platformban. Együttműködve civic platform támogatási csapat ával, hogy hozzáadja a felhasználókat a [Civic Platform Ügyfél támogatási csapatához.](mailto:skale@accela.com) Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

### <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a Polgári platform csempére kattint, automatikusan be kell jelentkeznie arra a polgári platformra, amelyhez beállította az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


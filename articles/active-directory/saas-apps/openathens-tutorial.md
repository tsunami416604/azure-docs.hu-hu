---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a OpenAthens | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és OpenAthens között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.openlocfilehash: fc60552fadf10dde88c0e6d8c6f46bd25c68cf2f
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543941"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-openathens"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a OpenAthens

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a OpenAthens a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az OpenAthens-t az Azure AD-vel, a következőket teheti:

* A OpenAthens-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a OpenAthens az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* OpenAthens egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A OpenAthens támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést
* A OpenAthens **csak időben támogatja a** felhasználók kiépítési folyamatát

## <a name="adding-openathens-from-the-gallery"></a>OpenAthens hozzáadása a gyűjteményből

A OpenAthens Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a OpenAthens a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **OpenAthens** kifejezést a keresőmezőbe.
1. Válassza ki a **OpenAthens** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-openathens"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a OpenAthens

Konfigurálja és tesztelje az Azure AD SSO-t a OpenAthens a **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a OpenAthens-ben.

Az Azure AD SSO és a OpenAthens konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[OPENATHENS SSO konfigurálása](#configure-openathens-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    * **[Hozzon létre OpenAthens-teszt felhasználót](#create-openathens-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-OpenAthens rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **OpenAthens** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban töltse fel a **szolgáltatói metaadatokat tartalmazó fájlt**, amely az oktatóanyag későbbi részében említett lépéseket ismerteti.

    a. Kattintson a **metaadat-fájl feltöltése**elemre.

    ![openathens feltöltése metaadatok](common/upload-metadata.png)

    b. Kattintson a **mappa emblémára** a metaadat-fájl kiválasztásához, majd kattintson a **feltöltés**elemre.

    ![Openathens tallózási metaadatok feltöltése](common/browse-upload-metadata.png)

    c. A metaadat-fájl feltöltése után az **azonosító** érték automatikusan kitöltődik az **SAML alapszintű konfigurációs** szakasz szövegmezőben:

    ![OpenAthens tartomány és URL-címek egyszeri bejelentkezési adatai](common/idp-identifier.png)

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A **OpenAthens beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a OpenAthens.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **OpenAthens**lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-openathens-sso"></a>OpenAthens SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a OpenAthens vállalati webhelyre rendszergazdaként.

1. Válassza a **kapcsolatok** lehetőséget a listából a **kezelés** lapon.

    ![Egyszeri bejelentkezés konfigurálása](./media/openathens-tutorial/tutorial_openathens_application1.png)

1. Válassza ki az **SAML 1.1/2.0**elemet, majd kattintson a **Konfigurálás** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/openathens-tutorial/tutorial_openathens_application2.png)

1. A konfiguráció hozzáadásához kattintson a **Tallózás** gombra a Azure Portal letöltött metadata. xml fájl feltöltéséhez, majd válassza a **Hozzáadás**lehetőséget.

    ![Egyszeri bejelentkezés konfigurálása](./media/openathens-tutorial/tutorial_openathens_application3.png)

1. Hajtsa végre a következő lépéseket a **részletek** lapon.

    ![Egyszeri bejelentkezés konfigurálása](./media/openathens-tutorial/tutorial_openathens_application4.png)

    a. A **megjelenítendő név leképezése**lapon válassza az **attribútum használata**elemet.

    b. A **megjelenítendő név attribútum** szövegmezőbe írja be az értéket `http://schema.microsoft.com/identity/claims/displayname` .

    c. Az **egyedi felhasználók leképezése**területen válassza az **attribútum használata**lehetőséget.

    d. Az **egyedi felhasználói attribútum** szövegmezőbe írja be az értéket `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` .

    e. Az **állapot**területen jelölje be mind a három jelölőnégyzetet.

    f. A **helyi fiókok létrehozása**területen válassza az **automatikus**lehetőséget.

    : Válassza a **Módosítások mentése** lehetőséget.

    h. A **</> függő entitás** lapon másolja a **metaadatok URL-címét** , és nyissa meg ezt a böngészőben az **SP-metaadatok XML-** fájljának letöltéséhez. Töltse fel ezt az SP metaadat-fájlt az Azure AD **alapszintű SAML-konfiguráció** szakaszában.

    ![Egyszeri bejelentkezés konfigurálása](./media/openathens-tutorial/tutorial_openathens_application5.png)

### <a name="create-openathens-test-user"></a>OpenAthens-tesztelési felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a OpenAthens-ben. A OpenAthens támogatja az **igény szerinti felhasználói üzembe**helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik a OpenAthens-ben, a rendszer egy újat hoz létre a hitelesítés után.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a OpenAthens csempére kattint, automatikusan be kell jelentkeznie arra a OpenAthens, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [A OpenAthens kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)
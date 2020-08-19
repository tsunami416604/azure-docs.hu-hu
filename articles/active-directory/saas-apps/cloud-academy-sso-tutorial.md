---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Cloud Academy-vel – SSO | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a Cloud Academy-SSO között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/16/2020
ms.author: jeedes
ms.openlocfilehash: 88e626f9b3069b3b43d525914c017caf763a9047
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88551761"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cloud-academy---sso"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Cloud Academy-vel – SSO

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Cloud Academy-SSO-t Azure Active Directory (Azure AD) használatával. Ha integrálja a Cloud Academy-SSO-t az Azure AD-val, a következőket teheti:

* Vezérlés az Azure AD-ben, aki hozzáfér a Cloud Academy-SSO-hoz.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Cloud Academy-SSO-ba az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Cloud Academy – SSO egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* Cloud Academy – SSO támogatja az **SP** által kezdeményezett SSO-t

* Miután konfigurálta a Cloud Academy-SSO-t, kényszerítheti a munkamenet-vezérlést, amely valós időben biztosítja a szervezet bizalmas adatainak kiszűrése és beszivárgását. A munkamenet-vezérlő kiterjeszthető a feltételes hozzáférésből. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-cloud-academy---sso-from-the-gallery"></a>Cloud Academy – SSO hozzáadása a katalógusból

A Cloud Academy-SSO Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Cloud Academy-SSO-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Cloud Academy-SSO** kifejezést a keresőmezőbe.
1. Válassza ki a **Cloud Academy-SSO** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-sso-for-cloud-academy---sso"></a>Azure AD SSO konfigurálása és tesztelése a Cloud Academy-hez – SSO

Konfigurálja és tesztelje az Azure AD SSO-t a Cloud Academy-SSO segítségével egy **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Cloud Academy-SSO-ben.

Az Azure AD SSO és a Cloud Academy-SSO konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[Cloud Academy konfigurálása – egyszeri](#configure-cloud-academy-sso-sso)** BEJELENTKEZÉSes egyszeri bejelentkezés – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Cloud Academy-egyszeri bejelentkezéses tesztelési felhasználó létrehozása](#create-cloud-academy-sso-test-user)** – ha a "B. Simon" a Cloud Academy-ben található, a felhasználó Azure ad-képviseletéhez társított SSO-t.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **Cloud Academy-SSO alkalmazás-** integráció lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet:  `https://cloudacademy.com/login/enterprise/`

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a Másolás gombra az **alkalmazás-összevonási metaadatok URL-címének** másolásához és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát a Cloud Academy-SSO elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza ki a **Cloud Academy-SSO**elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-cloud-academy-sso-sso"></a>A Cloud Academy konfigurálása – SSO SSO

1. Egy másik böngészőablakban jelentkezzen be a Cloud Academy-SSO céges webhelyre rendszergazdaként.

1. Kattintson a vállalat nevére, és válassza a **beállítások & integrációk** lehetőséget a menüből.

    ![Konfiguráció ](./media/cloud-academy-sso-tutorial/config-1.PNG)

1. A **beállítások & integrációk** lapon lépjen az **integrációk** lapra, és kattintson az **egyszeri bejelentkezés** kártyára.

    ![Konfiguráció ](./media/cloud-academy-sso-tutorial/config-2.PNG)

1. Hajtsa végre a következő lépéseket a következő oldalon:

    ![Konfiguráció ](./media/cloud-academy-sso-tutorial/config-3.PNG)

    a. Az **entitás-azonosító URL-címe** szövegmezőbe illessze be azt az **entitás-azonosító** értéket, amelyet a Azure Portal másolt.

    b. Az **egyszeri bejelentkezés URL-címe** szövegmezőbe illessze be azt a **bejelentkezési URL-címet** , amelyet a Azure Portal másolt.

    c. Nyissa meg a letöltött **tanúsítványt (Base64)** a Azure Portal a Jegyzettömbben, és illessze be a tartalmat a **tanúsítvány** szövegmezőbe.

    d. A **név azonosítójának formázása** szövegmezőben az alapértelmezett érték: `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`

1. Kattintson a **Save (Mentés** ) gombra.

    > [!NOTE]
    > A Cloud Academy-SSO konfigurálásával kapcsolatos további információkért tekintse meg a [támogatási cikket](https://support.cloudacademy.com/hc/articles/360043908452-Setting-Up-Single-Sign-On).

### <a name="create-cloud-academy-sso-test-user"></a>Cloud Academy létrehozása – SSO-teszt felhasználó

1. Jelentkezzen be a **Cloud Academy-SSO-** ba.

1. Kattintson a vállalat nevére, és válassza a **tagok** lehetőséget a menüből.

    ![ Tesztelési felhasználó létrehozása ](./media/cloud-academy-sso-tutorial/create-user.PNG)

1. Kattintson a **tagok meghívása** elemre, és válassza **az egyetlen tag meghívása**lehetőséget.

    ![ Tesztelési felhasználó létrehozása ](./media/cloud-academy-sso-tutorial/create-user-1.PNG)

1. Adja meg a kötelező mezőket, és kattintson a **meghívás**elemre.

    ![ Tesztelési felhasználó létrehozása ](./media/cloud-academy-sso-tutorial/create-user-2.PNG)

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Cloud Academy-egyszeri bejelentkezés csempére kattint, automatikusan be kell jelentkeznie a Cloud Academy-SSO-ba, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a Cloud Academy-SSO-t az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [A Cloud Academy – SSO védelem speciális láthatósággal és vezérlőkkel](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
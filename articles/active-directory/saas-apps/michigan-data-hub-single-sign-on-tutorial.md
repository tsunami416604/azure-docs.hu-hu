---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Michigan adatközpont-alapú egyszeri bejelentkezéssel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a Michigan adatközpont egyszeri bejelentkezés között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/23/2020
ms.author: jeedes
ms.openlocfilehash: 031190bee2dc81398ee0c4ac23ad9ec62469333a
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88528369"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-michigan-data-hub-single-sign-on"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Michigan adatközponti egyszeri bejelentkezéssel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Michigan adatközpont-alapú egyszeri bejelentkezést Azure Active Directory (Azure AD) használatával. Ha a Michigan adatközpont egyszeri bejelentkezését az Azure AD-be integrálja, a következőket teheti:

* Vezérlés az Azure AD-ben, aki hozzáfér a Michigan adatközponti egyszeri bejelentkezéshez.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Michigan adatközpontba való egyszeri bejelentkezésre az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Michigan adatközpont egyszeri bejelentkezéses egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Michigan adatközpont egyszeri bejelentkezés támogatja az **SP** által kezdeményezett SSO-t
* Miután konfigurálta a Michigan adatközpont egyszeri bejelentkezését, kikényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-michigan-data-hub-single-sign-on-from-the-gallery"></a>A Michigan adatközpont egyszeri bejelentkezésének hozzáadása a katalógusból

A Michigan adatközpont egyszeri bejelentkezésének az Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Michigan adatközpont egyszeri bejelentkezést a katalógusból a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Michigan adatközpont egyszeri bejelentkezés** elemet a keresőmezőbe.
1. Válassza a **Michigan adatközpont egyszeri bejelentkezés** az eredmények panelen lehetőséget, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-sso-for-michigan-data-hub-single-sign-on"></a>Azure AD SSO konfigurálása és tesztelése a Michigan adatközponti egyszeri bejelentkezéshez

Konfigurálja és tesztelje az Azure AD SSO-t a Michigan adatközpont egyszeri bejelentkezéssel egy **B. Simon**nevű tesztelési felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között a Michigan adatközpont egyszeri bejelentkezéssel.

Az Azure AD SSO és a Michigan adatközpont egyszeri bejelentkezésének konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[Michigan adatközpont egyszeri bejelentkezésének beállítása](#configure-michigan-data-hub-single-sign-on-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. A **[Michigan adatközpont egyszeri bejelentkezéses tesztelési felhasználójának létrehozása](#create-michigan-data-hub-single-sign-on-test-user)** – hogy a B. Simon a Michigan adatközpont egyszeri bejelentkezéssel rendelkezzen, amely a felhasználó Azure ad-képviseletéhez van társítva.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **Michigan adatközpont egyszeri bejelentkezési** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet:  `https://launchpad.midatahub.org`

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát a Michigan adatközponti egyszeri bejelentkezéshez való hozzáférés biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **Michigan adatközpont egyszeri bejelentkezés**lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-michigan-data-hub-single-sign-on-sso"></a>A Michigan adatközpont egyszeri bejelentkezéses egyszeri bejelentkezésének konfigurálása

Ha az egyszeri bejelentkezést a **Michigan adatközpont egyszeri bejelentkezési** oldalán szeretné beállítani, el kell küldenie az **alkalmazás-összevonási metaadatok URL-címét** a [Michigan adatközpont egyszeri bejelentkezés támogatási csapatának](mailto:support@midatahub.org). Ezt a beállítást úgy állították be, hogy az SAML SSO-kapcsolatok mindkét oldalon helyesen legyenek beállítva.

### <a name="create-michigan-data-hub-single-sign-on-test-user"></a>A Michigan adatközpont egyszeri bejelentkezési teszt felhasználójának létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználót hoz létre a Michigan adatközpont-alapú egyszeri bejelentkezésben. A [Michigan adatközpont egyszeri bejelentkezést támogató csapatával](mailto:support@midatahub.org) felveheti a felhasználókat a Michigan adatközpont egyszeri bejelentkezési platformján. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Michigan adatközpont egyszeri bejelentkezés csempére kattint, automatikusan be kell jelentkeznie a Michigan adatközpont egyszeri bejelentkezésre, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [A Michigan adatközpont egyszeri bejelentkezésének kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [A Michigan adatközpont-alapú egyszeri bejelentkezés védelme speciális láthatósággal és ellenőrzésekkel](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
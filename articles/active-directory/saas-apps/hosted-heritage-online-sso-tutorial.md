---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az üzemeltetett örökség online SSO-val | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és az üzemeltetett örökség online egyszeri bejelentkezése között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6ff82b40-76de-4eed-8ab7-92a416cd83cb
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1b133d28986f31ed396d1f499940d99e64f6fbc
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71174574"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-hosted-heritage-online-sso"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az üzemeltetett örökség online SSO-vel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az üzemeltetett örökség online SSO-t Azure Active Directory (Azure AD) használatával. Ha a üzemeltetett örökség online SSO-t az Azure AD-be integrálja, a következőket teheti:

* Vezérlés az Azure AD-ben, aki hozzáfér az üzemeltetett örökség online SSO-hoz.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek az üzemeltetett örökség online bejelentkezési adataiba az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Üzemeltetett örökség online egyszeri bejelentkezéses egyszeri bejelentkezési (SSO) előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* Az üzemeltetett örökség online SSO támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-hosted-heritage-online-sso-from-the-gallery"></a>Üzemeltetett örökség online SSO hozzáadása a katalógusból

Az üzemeltetett örökség online egyszeri bejelentkezés az Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az üzemeltetett örökség online SSO-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a " **Hosted Heritage online SSO** " kifejezést a keresőmezőbe.
1. Válassza az **üzemeltetett örökség online egyszeri bejelentkezés** az eredmények panelen lehetőséget, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-hosted-heritage-online-sso"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése az üzemeltetett örökség online SSO-hoz

Konfigurálja és tesztelje az Azure AD SSO-t az üzemeltetett örökség online SSO használatával egy **B. Simon**nevű tesztelési felhasználó segítségével. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között az üzemeltetett örökség online SSO-ban.

Az Azure AD SSO az üzemeltetett örökség online SSO-val való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[Tárolt örökség online egyszeri](#configure-hosted-heritage-online-sso-sso)** BEJELENTKEZÉSes egyszeri bejelentkezésének konfigurálása – az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalán.
    1. Az **[üzemeltetett örökség online SSO-tesztelési felhasználójának létrehozása](#create-hosted-heritage-online-sso-test-user)** – a felhasználó Azure ad-beli képviseletéhez kapcsolódó B. Simon-beli, üzemeltetett örökség online egyszeri bejelentkezéshez
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)az **üzemeltetett örökség online SSO** -alkalmazás integrációja oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az alapszintű **SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<SUBDOMAIN>.cirqahosting.com/Shibboleth.sso/Login`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<SUBDOMAIN>.cirqahosting.com/shibboleth`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. Vegye fel a kapcsolatot az [üzemeltetett örökség online SSO-ügyfél támogatási csapatával](mailto:support@isoxford.com) az értékek beszerzéséhez. Az Azure Portal alapszintű **SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a Másolás gombra az **alkalmazás-összevonási metaadatok URL-címének** másolásához és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozás](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza ki **új felhasználó** a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőben adja meg a username@companydomain.extensionnevet. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát az üzemeltetett örökség online SSO-hoz való hozzáférés biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza az **üzemeltetett örökség online egyszeri bejelentkezés**lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-hosted-heritage-online-sso-sso"></a>Üzemeltetett örökség online egyszeri bejelentkezéses SSO konfigurálása

Ha be szeretné állítani az egyszeri bejelentkezést az **üzemeltetett örökség online SSO** -oldalán, el kell küldenie az **alkalmazás-összevonási metaadatok URL-címét** az [üzemeltetett örökség online SSO támogatási csapatának](mailto:support@isoxford.com). Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

### <a name="create-hosted-heritage-online-sso-test-user"></a>Üzemeltetett örökség online SSO-tesztelési felhasználójának létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználót hoz létre az üzemeltetett örökség online SSO-ban. Az üzemeltetett [örökség online egyszeri bejelentkezéses támogatási csapatának](mailto:support@isoxford.com) használata a felhasználók hozzáadásához az üzemeltetett örökség online egyszeri bejelentkezés platformján. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen az üzemeltetett örökség online egyszeri bejelentkezés csempére kattint, automatikusan be kell jelentkeznie az üzemeltetett örökség online SSO-ra, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki az üzemeltetett örökség online SSO-t az Azure AD-vel](https://aad.portal.azure.com/)


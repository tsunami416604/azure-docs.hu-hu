---
title: 'Oktatóanyag: Azure Active Directory SSO-integráció a Cloud Academy-vel – SSO'
description: Ebből az oktatóanyagból megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a Cloud Academy-SSO között.
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
ms.openlocfilehash: 37ed9bb09b6b15af0c32f489cbc3c02ec27c2827
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89461952"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-cloud-academy---sso"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses integráció a Cloud Academy-vel – SSO

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Cloud Academy-SSO-t Azure Active Directory (Azure AD) használatával. Ha integrálja a Cloud Academy-SSO-t az Azure AD-val, a következőket teheti:

* Az Azure AD segítségével szabályozhatja, hogy ki férhet hozzá a Cloud Academy-SSO-hoz.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Cloud Academy-SSO-ba az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti: a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az egyszeri bejelentkezés?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Egy felhőalapú Akadémia – egyszeri bejelentkezést (SSO) használó egyszeri bejelentkezéses előfizetés.

## <a name="tutorial-description"></a>Oktatóanyag leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését tesztkörnyezetben végezheti el.

A Cloud Academy – SSO támogatja az SP által kezdeményezett egyszeri bejelentkezést.

A Cloud Academy-SSO konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlőelemek kiterjeszthetők a feltételes hozzáférésből. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-cloud-academy---sso-from-the-gallery"></a>Cloud Academy-SSO hozzáadása a katalógusból

A Cloud Academy-SSO Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Cloud Academy-SSO-t a katalógusból a felügyelt SaaS-alkalmazások listájához:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók.
1. A bal oldali panelen válassza az **Azure Active Directory** lehetőséget.
1. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban adja meg a **Cloud Academy-SSO** kifejezést a keresőmezőbe.
1. Válassza ki a **Cloud Academy-SSO** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-sso-for-cloud-academy---sso"></a>Azure AD SSO konfigurálása és tesztelése a Cloud Academy-hez – SSO

Az Azure AD SSO konfigurálását és tesztelését a Cloud Academy-SSO használatával végezheti el egy **B. Simon**nevű teszt felhasználó segítségével. Az SSO működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a hozzá tartozó felhasználó között a Cloud Academy-SSO-ban.

Az Azure AD SSO és a Cloud Academy-SSO konfigurálásához és teszteléséhez hajtsa végre a következő magas szintű lépéseket:

1. **[Konfigurálja az Azure ad SSO](#configure-azure-ad-sso)** -t annak engedélyezéséhez, hogy a felhasználók használják a szolgáltatást.
    1. **[Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user)** az Azure ad egyszeri bejelentkezés teszteléséhez.
    1. **[Hozzáférés biztosítása a tesztelési felhasználónak](#grant-access-to-the-test-user)** , hogy lehetővé váljon a felhasználó számára az Azure ad egyszeri bejelentkezés használata.
1. Az **[egyszeri bejelentkezés konfigurálása a Cloud Academy-hez – SSO](#configure-single-sign-on-for-cloud-academy)** az alkalmazás oldalán.
    1. **[Hozzon létre egy Cloud Academy-SSO teszt felhasználót](#create-a-cloud-academy-test-user)** a felhasználó Azure ad-beli képviseletének megfelelő felhasználóként.
1. Ellenőrizze az **[SSO](#test-sso)** -t a konfiguráció működésének ellenőrzéséhez.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO-t a Azure Portalban:

1. A [Azure Portal](https://portal.azure.com/)a **Cloud Academy-SSO** Application Integration oldalon, a **kezelés** szakaszban válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri Sign-On beállítása az SAML-vel** lapon válassza az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza gombot a beállítások szerkesztéséhez:

   ![Képernyőkép, amely az alapszintű SAML-konfiguráció szerkesztésére szolgáló ceruza gombot mutatja.](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakasz **bejelentkezési URL-címe** mezőjébe írja be a következőt: `https://cloudacademy.com/login/enterprise/` .

1. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban válassza a másolás gombot az **alkalmazás-összevonási metaadatok URL-címének**másolásához. Mentse az URL-címet.

    ![Képernyőkép, amely az alkalmazás-összevonási metaadatok URL-címéhez tartozó másolás gombot jeleníti meg.](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy B. Simon nevű teszt felhasználót hoz létre a Azure Portal.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory**lehetőséget. Válassza a **felhasználók**lehetőséget, majd válassza **a minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre a következő lépéseket:
   1. A név mezőbe írja be a **B. Simon** **nevet** .  
   1. A **Felhasználónév** mezőbe írja be a nevet \<username> @ \<companydomain> . \<extension> Például: `B.Simon@contoso.com`.
   1. Válassza a **jelszó megjelenítése**lehetőséget, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** gombra.

### <a name="grant-access-to-the-test-user"></a>Hozzáférés biztosítása a tesztelési felhasználónak

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy a felhasználó hozzáférést biztosít a Cloud Academy-SSO-hoz.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza ki a **Cloud Academy-SSO**elemet.
1. Az alkalmazás Áttekintés lapjának **kezelés** szakaszában válassza a **felhasználók és csoportok**lehetőséget:

   ![A felhasználók és csoportok lehetőséget megjelenítő képernyőkép.](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget:

    ![A felhasználó hozzáadása gombot megjelenítő képernyőkép.](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza az **B. Simon** elemet a **felhasználók** listán, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-kijelentésben bármelyik szerepkör értékét várta, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a listáról a felhasználó számára. Kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelés**lehetőséget.

## <a name="configure-single-sign-on-for-cloud-academy"></a>Egyszeri bejelentkezés konfigurálása a Cloud Academy-hez

1. Egy másik böngészőablakban jelentkezzen be a Cloud Academy-SSO céges webhelyre rendszergazdaként.

1. Válassza ki a vállalat nevét, majd válassza a **beállítások & integrációk** lehetőséget a megjelenő menüben:

    ![Képernyőkép, amely a beállítások & integrációk lehetőséget mutatja.](./media/cloud-academy-sso-tutorial/config-1.PNG)

1. A **beállítások & integrációk** lap **integrációk** lapján válassza az **SSO** -kártyát:

    ![Képernyőkép, amely az SSO-kártyát mutatja az integrációk lapon.](./media/cloud-academy-sso-tutorial/config-2.PNG)

1. Hajtsa végre a következő lépéseket ezen az oldalon:

    ![A Inegrations > SSO-lapot megjelenítő képernyőkép.](./media/cloud-academy-sso-tutorial/config-3.PNG)

    a. Az **entitás-azonosító URL-címe** mezőbe írja be a Azure Portalból másolt entitás-azonosító értékét.

    b. Az **SSO URL-cím** mezőben illessze be a Azure Portalból másolt bejelentkezési URL-értéket.

    c. Nyissa meg a letöltött Base64-tanúsítványt a Azure Portal a Jegyzettömbben. Illessze be a tartalmát a **tanúsítvány** mezőbe.

    d. A **név azonosítójának formátuma** mezőben tartsa meg az alapértelmezett értéket: `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` .

1. Kattintson a **Mentés** gombra.

    > [!NOTE]
    > A Cloud Academy-SSO konfigurálásával kapcsolatos további információkért lásd: az [egyszeri bejelentkezés beállítása](https://support.cloudacademy.com/hc/articles/360043908452-Setting-Up-Single-Sign-On).

### <a name="create-a-cloud-academy-test-user"></a>Felhőalapú Akadémia tesztelési felhasználójának létrehozása

1. Jelentkezzen be a Cloud Academy-SSO-ba.

1. Válassza ki a vállalat nevét, majd a megjelenő menüben válassza a **tagok** elemet:

    ![A tagok lehetőséget megjelenítő képernyőkép.](./media/cloud-academy-sso-tutorial/create-user.PNG)

1. Válassza a **tagok meghívása** , majd **az egyetlen tag meghívása**lehetőséget:

    ![Képernyőkép, amely az egyetlen tag meghívása lehetőséget mutatja.](./media/cloud-academy-sso-tutorial/create-user-1.PNG)

1. Adja meg az értékeket a kötelező mezőkben, majd válassza a **meghívás**elemet:

    ![Képernyőkép, amely megjeleníti a tag meghívása párbeszédpanelt.](./media/cloud-academy-sso-tutorial/create-user-2.PNG)

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Most tesztelheti az Azure AD SSO-konfigurációját a hozzáférési panel használatával.

Amikor kiválasztja a Cloud Academy-SSO csempét a hozzáférési panelen, automatikusan be kell jelentkeznie a Cloud Academy-SSO-példányba, amelyhez be kell állítania az egyszeri bejelentkezést. További információ: [Bevezetés a hozzáférési panelbe](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Útmutatók az SaaS-alkalmazások Azure Active Directory-nal való integrálásához](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a Cloud Academy-SSO-t az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [A Cloud Academy – SSO védelem speciális láthatóság és vezérlők használatával](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
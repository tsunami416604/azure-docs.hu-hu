---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a SSOGEN-vel – Azure AD SSO Gateway for Oracle E-Business Suite-EBS, PeopleSoft és JDE | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és SSOGEN között – Azure AD SSO Gateway for Oracle E-Business Suite-EBS, PeopleSoft és JDE.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f4311031-5a4b-468e-be58-324d06220869
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74b56e30eb8851913f91cbee377204a575311375
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "76293674"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a SSOGEN-vel – Azure AD SSO Gateway for Oracle E-Business Suite-EBS, PeopleSoft és JDE

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a SSOGEN-Azure AD SSO-átjárót az Oracle E-Business Suite-EBS, a PeopleSoft és a JDE Azure Active Directory (Azure AD) használatával. Ha integrálja a SSOGEN – Azure AD SSO Gateway for Oracle E-Business Suite-EBS, PeopleSoft és JDE az Azure AD-vel, a következőket teheti:

* Vezérlés az Azure AD-ben, aki hozzáfér a SSOGEN-hez – Azure AD SSO Gateway for Oracle E-Business Suite-EBS, PeopleSoft és JDE.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a SSOGEN – Azure AD SSO Gateway for Oracle E-Business Suite-EBS, PeopleSoft és JDE Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* SSOGEN – Azure AD SSO Gateway for Oracle E-Business Suite-EBS, PeopleSoft és JDE egyszeri bejelentkezés (SSO) engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* SSOGEN – Azure AD SSO Gateway for Oracle E-Business Suite – az EBS, a PeopleSoft és a JDE támogatja az **SP és a identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést.
* Miután konfigurálta a SSOGEN – Azure AD SSO-átjárót az Oracle E-Business Suite-EBS, a PeopleSoft és a JDE számára, kikényszerítheti a munkamenet-vezérlést, amely a szervezet bizalmas adatainak kiszűrése és beszivárgását valós időben teszi elérhetővé. A munkamenet-vezérlőelemek kiterjeszthetők a feltételes hozzáférésből. [Ismerje meg, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egyetlen bérlőn.

## <a name="adding-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-from-the-gallery"></a>SSOGEN hozzáadása – Azure AD SSO Gateway az Oracle E-Business Suite-EBS, PeopleSoft és JDE a katalógusból

A SSOGEN-Azure AD SSO-átjáró az Oracle E-Business Suite-EBS, a PeopleSoft és a JDE Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a SSOGEN-Azure AD SSO Gateway for Oracle E-Business Suite-EBS, PeopleSoft és JDE a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **SSOGEN-Azure ad SSO Gateway for Oracle E-Business Suite-EBS, PeopleSoft és JDE** kifejezést a keresőmezőbe.
1. Válassza a **SSOGEN – Azure ad SSO Gateway for Oracle E-Business Suite-EBS, PeopleSoft és JDE** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a SSOGEN-hez – Azure AD SSO Gateway for Oracle E-Business Suite-EBS, PeopleSoft és JDE

Az Azure AD SSO konfigurálása és tesztelése a SSOGEN – Azure AD SSO Gateway for Oracle E-Business Suite-EBS, PeopleSoft és JDE egy **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a SSOGEN-ben – Azure AD SSO Gateway az Oracle E-Business Suite-EBS, PeopleSoft és JDE esetében.

Az Azure AD SSO konfigurálásához és teszteléséhez az SSOGEN-Azure AD SSO Gateway for Oracle E-Business Suite-EBS, PeopleSoft és JDE esetében hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[SSOGEN Azure ad SSO-átjáró konfigurálása az Oracle E Business Suite EBS, a PeopleSoft és a JDE egyszeri](#configure-ssogen-azure-ad-sso-gateway-for-oracle-e-business-suite-ebs-peoplesoft-and-jde-sso)** bejelentkezéshez – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    * **[SSOGEN Azure ad SSO-átjáró létrehozása az Oracle E Business Suite EBS-hez, a PeopleSoft-hoz és a JDE-teszthez](#create-ssogen-azure-ad-sso-gateway-for-oracle-e-business-suite-ebs-peoplesoft-and-jde-test-user)** – az Azure ad SSO-ÁTJÁRÓ a SSOGEN-hez című részében található, a felhasználó Azure ad-beli képviseletéhez kapcsolódó Oracle e-Business Suite-EBS, PEOPLESOFT és JDE.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **SSOGEN – Azure ad SSO-átjáró az Oracle E-Business Suite-EBS, a PEOPLESOFT és a JDE alkalmazás-** integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az **ALAPszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<customer_name>.ssogen.com/ssogen/login?client_name=<customer_name>`

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<customer_name>.ssogen.com/ssogen/login?client_name=<customer_name>`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges válasz URL-címmel és a bejelentkezési URL-címmel. Vegye fel a kapcsolatot a [SSOGEN-vel – Azure ad SSO-átjáró az Oracle E-Business Suite-EBS, PeopleSoft és JDE ügyfél-támogatási csapatának](mailto:support@ssogen.com) az értékek beszerzéséhez. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. A SSOGEN-Azure AD SSO Gateway for Oracle E-Business Suite-EBS, PeopleSoft és JDE alkalmazás meghatározott formátumban várja az SAML-jogcímeket, amelyhez egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőfelvételen az alapértelmezett attribútumok listája látható, ahol a **NameIdentifier** a **User. userPrincipalName**leképezéssel van leképezve. SSOGEN – az Azure AD SSO Gateway for Oracle E-Business Suite-EBS, a PeopleSoft és a JDE alkalmazás a **NameIdentifier** -t a **User. onpremisessamaccountname**-vel rendeli hozzá, ezért az attribútum-hozzárendelés szerkesztéséhez kattintson a **Szerkesztés** ikonra, és módosítsa az attribútumok leképezését.

    ![image](common/edit-attribute.png)

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a Másolás gombra az **alkalmazás-összevonási metaadatok URL-címének** másolásához és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőben adja meg a username@companydomain.extensionnevet. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás**gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a SSOGEN – Azure AD SSO Gateway for Oracle E-Business Suite-EBS, PeopleSoft és JDE.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **SSOGEN-Azure ad SSO Gateway for Oracle E-Business Suite-EBS, PeopleSoft és JDE**elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-ssogen-azure-ad-sso-gateway-for-oracle-e-business-suite-ebs-peoplesoft-and-jde-sso"></a>Azure AD SSO-átjáró konfigurálása az Oracle E Business Suite EBS, a PeopleSoft és a JDE SSO SSOGEN

Az egyszeri bejelentkezés konfigurálása a **SSOGEN-ben – Azure ad SSO-átjáró az Oracle E-Business Suite-EBS, a PeopleSoft és a JDE oldalon az** alkalmazás-specifikus SSO regisztrációs dokumentációjában olvasható

* Oracle EBS – Azure AD SSO-integráció:[https://www.ssogen.com/oracle-ebs-sso-ldap/](https://www.ssogen.com/oracle-ebs-sso-ldap/)
* PeopleSoft – Azure AD SSO-integráció:[https://www.ssogen.com/peoplesoft-sso/](https://www.ssogen.com/peoplesoft-sso/)
* JD Edwards – Azure AD SSO-integráció:[https://www.ssogen.com/oracle-jde-sso/](https://www.ssogen.com/oracle-jde-sso/)
* Apache – Azure AD SSO-integráció:[https://www.ssogen.com/apache-sso-authentication/](https://www.ssogen.com/apache-sso-authentication/)

### <a name="create-ssogen-azure-ad-sso-gateway-for-oracle-e-business-suite-ebs-peoplesoft-and-jde-test-user"></a>SSOGEN Azure AD SSO-átjáró létrehozása az Oracle E Business Suite EBS, a PeopleSoft és a JDE test User számára

Az Azure AD a hitelesítés sikeres elvégzése után egyedi felhasználói azonosítót (Name ID) küld a felhasználói alkalmazásnak.  Győződjön meg arról, hogy az egyedi felhasználói azonosító (Name ID) megfelel az alkalmazásban lévő felhasználói rekordnak, FND_USER. USER_NAME például az Oracle EBS-ben.

Forduljon a [info@ssogen.com](mailto:info@ssogen.com) támogatási [support@ssogen.com](mailto:support@ssogen.com) szolgálathoz, és kérjen segítséget.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha rákattint a SSOGEN – Azure AD SSO Gateway for Oracle E-Business Suite-EBS, PeopleSoft és JDE csempe a hozzáférési panelen, automatikusan be kell jelentkeznie a SSOGEN-Azure AD SSO Gateway for Oracle E-Business Suite-EBS, PeopleSoft és JDE, amelyhez be van állítva az egyszeri bejelentkezés. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További háttéranyagok

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a SSOGEN – Azure AD SSO Gateway for Oracle E-Business Suite-EBS, PeopleSoft és JDE az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [SSOGEN – Azure AD SSO-átjáró az Oracle E-Business Suite-EBS, PeopleSoft és JDE számára speciális láthatósággal és ellenőrzésekkel](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

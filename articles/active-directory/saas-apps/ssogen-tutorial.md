---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az SSOGEN-nel – Azure AD SSO Gateway for Oracle E-Business Suite – EBS, PeopleSoft és JDE | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az SSOGEN – Azure AD SSO Gateway for Oracle E-Business Suite – EBS, PeopleSoft és JDE között.
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76293674"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az SSOGEN-nel – Azure AD SSO Gateway oracle E-Business Suite - EBS, PeopleSoft és JDE

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az SSOGEN – Azure AD SSO Gateway for Oracle E-Business Suite – EBS, PeopleSoft és JDE szolgáltatást az Azure Active Directoryval (Azure AD). Ha integrálja a SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft és JDE átjárót az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-t, aki hozzáfér az SSOGEN - Azure AD SSO Gateway oracle E-Business Suite - EBS, PeopleSoft és JDE.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve ssogen - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft és JDE az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* SSOGEN – Azure AD SSO Gateway for Oracle E-Business Suite – EBS, PeopleSoft és JDE egyszeri bejelentkezés (SSO) előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite – EBS, PeopleSoft és JDE támogatja **az SP-t és az IDP** által kezdeményezett egyszeri szolgáltatást.
* Miután konfigurálta az SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft és JDE konfigurálását, kényszerítheti a munkamenet-vezérlőket, amelyek valós időben védik a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlők a feltételes hozzáféréstől származnak. [Megtudhatja, hogy miként kényszerítheti ki a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egy bérlőben.

## <a name="adding-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-from-the-gallery"></a>SSOGEN hozzáadása - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft és JDE a galériából

Az SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft és JDE azure AD integrációjának konfigurálásához hozzá kell adnia az SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft és JDE átjárót a gyűjteményből a felügyelt SaaS alkalmazások.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a katalógusból** szakaszban írja be az **SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft és JDE** kifejezést a keresőmezőbe.
1. Válassza a **SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite – EBS, PeopleSoft és JDE lehetőséget** az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde"></a>Az Azure AD egyszeri bejelentkezésének konfigurálása és tesztelése az SSOGEN-hez – Azure AD SSO Gateway for Oracle E-Business Suite – EBS, PeopleSoft és JDE

Konfigurálja és tesztelje az Azure AD SSO-t ssogen - Azure AD SSO Gateway for Oracle E-Business Suite – EBS, PeopleSoft és JDE segítségével egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az Egyszeri bejelentkezés működjön, létre kell hoznia egy kapcsolatkapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között az SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft és JDE.

Az Azure AD SSO ssogen - Azure AD SSO Gateway for Oracle E-Business Suite – EBS, PeopleSoft és JDE beállításához és teszteléséhez hajtsa végre az alábbi építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    1. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja az SSOGEN Azure AD SSO Gateway-t az Oracle E Business Suite EBS, a PeopleSoft és a JDE SSO-hoz](#configure-ssogen-azure-ad-sso-gateway-for-oracle-e-business-suite-ebs-peoplesoft-and-jde-sso)** – az alkalmazás oldalon az egyszeri bejelentkezési beállítások konfigurálásához.
    * **[SSOGEN Azure AD SSO Gateway létrehozása Oracle E Business Suite EBS, PeopleSoft és JDE tesztfelhasználó számára](#create-ssogen-azure-ad-sso-gateway-for-oracle-e-business-suite-ebs-peoplesoft-and-jde-test-user)** – b.Simon megfelelője az SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft és JDE, amely az Azure AD felhasználói ábrázolásához kapcsolódik.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)az **SSOGEN – Azure AD SSO Gateway for Oracle E-Business Suite – EBS, PeopleSoft és JDE** alkalmazásintegrációs lapon keresse meg a **Manage szakaszt,** és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban, ha az alkalmazást **IDP** által kezdeményezett módban szeretné konfigurálni, adja meg a következő mezők értékeit:

    A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://<customer_name>.ssogen.com/ssogen/login?client_name=<customer_name>`

1. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<customer_name>.ssogen.com/ssogen/login?client_name=<customer_name>`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges válasz URL-címmel és bejelentkezési URL-címmel. Lépjen kapcsolatba [a SSOGEN – Azure AD SSO Gateway for Oracle E-Business Suite – EBS, PeopleSoft és JDE ügyféltámogatási csapattal,](mailto:support@ssogen.com) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. Az SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite – EBS, PeopleSoft és JDE alkalmazás az SAML-állításokat egy adott formátumban várja, amely megköveteli, hogy egyéni attribútum-hozzárendeléseket adjon hozzá az SAML tokenattribútum-konfigurációjához. A következő képernyőképen az alapértelmezett attribútumok listája látható, ahol a **nameidentifier** a **user.userprincipalname**. SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite – EBS, PeopleSoft és JDE alkalmazás elvárja, hogy **a névazonosító** le legyen képezve **a user.onpremisessamaccountname-nal,** ezért az attribútumleképezést a **Szerkesztés** ikonra kattintva kell módosítania, és módosítania kell az attribútumleképezést.

    ![image](common/edit-attribute.png)

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány szakaszában** kattintson a Másolás gombra az **Alkalmazásösszevonás metaadat-címének** másolásához és mentéséhez a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)

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

Ebben a szakaszban lehetővé teszi b.Simon azure egyszeri bejelentkezés t azáltal, hogy hozzáférést biztosít a SSOGEN - Azure AD SSO Gateway oracle E-Business Suite - EBS, PeopleSoft és JDE.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza az **SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite – EBS, PeopleSoft és JDE**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-ssogen-azure-ad-sso-gateway-for-oracle-e-business-suite-ebs-peoplesoft-and-jde-sso"></a>SSOGEN Azure AD SSO gateway konfigurálása Oracle E Business Suite EBS, PeopleSoft és JDE SSO-hoz

Az egyszeri bejelentkezés konfigurálásához az **SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft és JDE** oldalon, kérjük, keresse meg az alkalmazásspecifikus egyszeri bejelentkezés regisztrációs dokumentációját az alábbi módon:

* Oracle EBS – Azure AD Egyszeri szolgáltatás integrációja:[https://www.ssogen.com/oracle-ebs-sso-ldap/](https://www.ssogen.com/oracle-ebs-sso-ldap/)
* PeopleSoft - Azure AD Egyszeri szolgáltatás integrációja:[https://www.ssogen.com/peoplesoft-sso/](https://www.ssogen.com/peoplesoft-sso/)
* JD Edwards – Azure AD Egyszeri szolgáltatás integrációja:[https://www.ssogen.com/oracle-jde-sso/](https://www.ssogen.com/oracle-jde-sso/)
* Apache – Azure AD Egyszeri szolgáltatás integrációja:[https://www.ssogen.com/apache-sso-authentication/](https://www.ssogen.com/apache-sso-authentication/)

### <a name="create-ssogen-azure-ad-sso-gateway-for-oracle-e-business-suite-ebs-peoplesoft-and-jde-test-user"></a>SSOGEN Azure AD SSO gateway létrehozása Oracle E Business Suite EBS, PeopleSoft és JDE tesztfelhasználó számára

Az Azure AD egyedi felhasználói azonosítót (névazonosítót) küld a felhasználói alkalmazásnak, miután a hitelesítés sikeres volt.  Győződjön meg arról, hogy az egyedi felhasználói azonosító (névazonosító) megegyezik az alkalmazáson FND_USER lévő felhasználói bejegyzésével. USER_NAME például az Oracle EBS-ben.

Kérjük, forduljon, [info@ssogen.com](mailto:info@ssogen.com) és [support@ssogen.com](mailto:support@ssogen.com) minden támogatást.

## <a name="test-sso"></a>SSO tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha az Oracle E-Business Suite SSOGEN - Azure AD SSO Gateway – EBS, PeopleSoft és JDE csempére kattint a hozzáférési panelen, automatikusan be kell jelentkeznie az SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft és JDE rendszerbe, amelyhez Ön sso-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a SSOGEN -Azure AD SSO Gateway for Oracle E-Business Suite – EBS, PeopleSoft és JDE azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlés a Microsoft Cloud App Security alkalmazásban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Az SSOGEN védelme – Azure AD SSO Gateway for Oracle E-Business Suite – EBS, PeopleSoft és JDE fejlett láthatósággal és vezérlőkkel](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

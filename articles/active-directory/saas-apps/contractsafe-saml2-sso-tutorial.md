---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a ContractSafe Saml2 Egyszeri bejelentkezéssel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a ContractSafe Saml2 egyszeri bejelentkezés között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9d8c9eba-6a90-4c8f-b387-a6ead4af00af
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 12/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ab2dc71f39164988e0d229fc994548a00447986
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77185619"
---
# <a name="tutorial-integrate-azure-active-directory-single-sign-on-sso-with-contractsafe-saml2-sso"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezésének (SSO) integrálása a ContractSafe Saml2 egyszeri bejelentkezéssel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a ContractSafe Saml2 SSO-t az Azure Active Directoryval (Azure AD). Ha integrálja a ContractSafe Saml2 SSO-t az Azure AD-vel, a következőket teheti:

* Szabályozhatja, hogy ki férhet hozzá a ContractSafe Saml2 SSO-hoz az Azure AD-ben.
* Lehetővé teszi a felhasználók számára, hogy automatikusan jelentkezzen be a ContractSafe Saml2 SSO az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen: az Azure Portalon.

Ha többet szeretne megtudni a szoftver szolgáltatásként (SaaS) alkalmazás integráció az Azure [AD,Lásd: Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következők szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* ContractSafe Saml2 SSO-előfizetés, amelyen az SSO engedélyezve van.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben. A ContractSafe Saml2 Egyszeri szolgáltató támogatja az **IDP**által kezdeményezett egyszeri szolgáltatót.

## <a name="add-contractsafe-saml2-sso-from-the-gallery"></a>ContractSafe Saml2 SSO hozzáadása a galériából

A ContractSafe Saml2 SSO azure AD-be való integrációjának konfigurálásához hozzá kell adnia a ContractSafe Saml2 SSO-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a Minden **alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **katalógus hozzáadásszakaszában** írja be a **ContractSafe Saml2 SSO** kifejezést a keresőmezőbe.
1. Válassza a **ContractSafe Saml2 SSO** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-sso-for-contractsafe-saml2-sso"></a>Az Azure AD-sSO konfigurálása és tesztelése a ContractSafe Saml2 SSO-hoz

Konfigurálja és tesztelje az Azure AD SSO-t a ContractSafe Saml2 SSO-val egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a contractsafe Saml2 sso kapcsolódó felhasználója között.

Az Azure AD SSO konfigurálásához és teszteléséhez a ContractSafe Saml2 SSO-val hajtsa végre a következő építőelemeket:

1. [Konfigurálja az Azure AD SSO-t,](#configure-azure-ad-sso) hogy a felhasználók használhassák ezt a funkciót.
   * [Hozzon létre egy Azure AD-tesztfelhasználót](#create-an-azure-ad-test-user) az Azure AD SSO teszteléséhez a **B.Simon-fiók** használatával.
   * [Rendelje hozzá az Azure AD tesztfelhasználót,](#assign-the-azure-ad-test-user) hogy **b.Simon** azure AD sso használatát.

1. [Konfigurálja a ContractSafe Saml2 SSO-t](#configure-contractsafe-saml2-sso) az sso-beállítások alkalmazásoldali konfigurálásához.
   * [Hozzon létre egy ContractSafe Saml2 SSO teszt felhasználó,](#create-a-contractsafe-saml2-sso-test-user) hogy egy megfelelője **B.Simon** a ContractSafe Saml2 SSO, amely kapcsolódik a felhasználó Azure AD-ábrázolása.
2. [Tesztelje az SSO-t,](#test-sso) hogy ellenőrizze, működik-e a konfiguráció.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az Azure AD SSO engedélyezéséhez kövesse az alábbi lépéseket az Azure Portalon:

1. Az [Azure Portalon](https://portal.azure.com/)a **ContractSafe Saml2 egyszeri** bejelentkezés alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások egy saml-lel** lapon válassza az **egyszerű SAML-konfiguráció** szerkesztési (toll) ikonját a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon adja meg a következő értékeket a megfelelő mezőkben:

    a. Az **Azonosító** mezőbe írjon be egy URL-címet a következő formátumban:`https://app.contractsafe.com/saml2_auth/<UNIQUEID>/acs/`

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő formátumban:`https://app.contractsafe.com/saml2_auth/<UNIQUEID>/acs/`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval és a válasz URL-címével. Lépjen kapcsolatba a [ContractSafe Saml2 SSO ügyfél támogatási csapatával,](mailto:support@contractsafe.com) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható formátumokat is megjelenítheti.

1. A ContractSafe Saml2 Egyszeri bejelentkezés egy adott formátumban várja az SAML-állításokat, amely megköveteli, hogy egyéni attribútumleképezéseket adjon hozzá az SAML tokenattribútum-konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![Gyakori alapértelmezett attribútumok](common/default-attributes.png)

1. Az alapértelmezett attribútumok mellett a ContractSafe Saml2 SSO alkalmazás azt várja, hogy még néhány attribútum ot visszaadjon az SAML-válaszban. Ezek az attribútumok előre ki vannak töltve, de áttekintheti őket az igényeinek megfelelően. Az alábbi lista a további attribútumokat tartalmazza.

    | Név | Forrás attribútum|
    | ---------------| --------------- |
    | e-mailnév | user.userprincipalname |
    | e-mail | user.onpremisesuserprincipalname |

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány szakaszban** keresse meg az **összevonási metaadat-XML című területet.** A Tanúsítvány letöltéséhez válassza a **Letöltés** lehetőséget, majd mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A **ContractSafe Saml2 SSO beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

## <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy **B.Simon**nevű tesztfelhasználót hoz létre az Azure Portalon.

1. Az Azure Portal bal oldali ablaktáblájában válassza az **Azure Active Directory**lehetőséget. Válassza a **Felhasználók**lehetőséget, majd a **Minden felhasználó lehetőséget.**
1. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.
1. A **Felhasználói** tulajdonságok csoportban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőbe írjon be `username@companydomain.extension` egy e-mail címet a formátumban. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet, majd írja le a **Jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** gombra.

## <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi a **B.Simon** számára az Azure SSO használatát a ContractSafe Saml2 SSO-hoz való hozzáférés biztosításával.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **ContractSafe Saml2 SSO**lehetőséget.
1. Az alkalmazás áttekintése lapon keresse meg a **Kezelés szakaszt,** majd válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** **párbeszédpanelen** a Felhasználók és csoportok lehetőséget.

   ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a **Felhasználók** listából. Ezután válassza a **Kijelölés** gombot a képernyő alján.
1. Ha az SAML-feltételben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából. Ezután válassza a **Kijelölés** gombot a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-contractsafe-saml2-sso"></a>A ContractSafe Saml2 sso konfigurálása

Az SSO konfigurálásához a **ContractSafe Saml2 SSO** oldalon el kell küldenie a letöltött **összevonási metaadat-XML-t** és a megfelelő másolt URL-címeket az Azure Portalról a [ContractSafe Saml2 SSO támogatási csapatának.](mailto:support@contractsafe.com) A csapat felelős az SAML SSO-kapcsolat mindkét oldalon megfelelő beállításáért.

## <a name="create-a-contractsafe-saml2-sso-test-user"></a>ContractSafe Saml2 SSO-tesztfelhasználó létrehozása

Hozzon létre egy B.Simon nevű felhasználót a ContractSafe Saml2 SSO-ban. Együttműködve a [ContractSafe Saml2 SSO támogatási csapatával](mailto:support@contractsafe.com) a felhasználók hozzáadása a ContractSafe Saml2 SSO platformon. A felhasználókat az SSO használata előtt létre kell hozni és aktiválni kell.

## <a name="test-sso"></a>SSO tesztelése

Tesztelje az Azure AD SSO-konfigurációt a hozzáférési panel használatával. Ha a Hozzáférési panelen kiválasztja a ContractSafe Saml2 SSO csempét, automatikusan be kell jelentkeznie a ContractSafe Saml2 sso-ba, amelyhez beállítja az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directoryval való integrálásáról szóló oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a ContractSafe Saml2 SSO-t az Azure AD-vel](https://aad.portal.azure.com/)

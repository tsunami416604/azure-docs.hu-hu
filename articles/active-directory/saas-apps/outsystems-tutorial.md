---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az OutSystems Azure AD-vel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az OutSystems Azure AD között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: cf6f99b7-0604-4db2-a72e-0d1a1d643a08
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/18/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f631a641ab63d33d27d1e4520a000b4b01132be
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "68478572"
---
# <a name="tutorial-integrate-outsystems-azure-ad-with-azure-active-directory"></a>Oktatóanyag: Az OutSystems Azure AD integrálása az Azure Active Directoryval

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az OutSystems Azure AD-t az Azure Active Directoryval (Azure AD). Ha integrálja az OutSystems Azure AD-t az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér az OutSystems Azure AD-hez.
* Lehetővé teszi, hogy a felhasználók automatikusan bejelentkezve OutSystems Azure AD az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* OutSystems Azure AD egyszeri bejelentkezés (SSO) engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben. Az OutSystems Azure AD támogatja **az SP és az IDP** által kezdeményezett egyszeri bejelentkezést, és támogatja a Just In **Time** felhasználói kiépítést.

## <a name="adding-outsystems-azure-ad-from-the-gallery"></a>OutSystems Azure AD hozzáadása a gyűjteményből

Az OutSystems Azure AD azure AD-be való integrálásának konfigurálásához hozzá kell adnia az OutSystems Azure AD-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a katalógusból** szakaszban írja **be outSystems Azure AD** a keresőmezőbe.
1. Válassza **az OutSystems Azure AD** az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD-s-t az OutSystems Azure AD-vel egy **B. Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó outsystems Azure AD-ben.

Az Azure AD SSO konfigurálásához és teszteléséhez az OutSystems Azure AD-vel hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD SSO-t,](#configure-azure-ad-sso)** hogy a felhasználók használhassák ezt a funkciót.
2. **[Állítsa be az OutSystems Azure AD-t](#configure-outsystems-azure-ad)** az sso-beállítások alkalmazásoldali konfigurálásához.
3. **[Hozzon létre egy Azure AD-tesztfelhasználót](#create-an-azure-ad-test-user)** az Azure AD egyszeri bejelentkezésének teszteléséhez B. Simonnal.
4. **[Rendelje hozzá az Azure AD tesztfelhasználót,](#assign-the-azure-ad-test-user)** hogy b. Simon az Azure AD egyszeri bejelentkezést.
5. **[Hozzon létre OutSystems Azure AD tesztfelhasználó,](#create-outsystems-azure-ad-test-user)** hogy egy megfelelője B. Simon outSystems Azure AD, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Tesztelje az SSO-t,](#test-sso)** hogy ellenőrizze, működik-e a konfiguráció.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)az **OutSystems Azure AD-alkalmazások** integrációja lapon keresse meg a **Kezelés szakaszt,** és válassza az **Egyszeri bejelentkezés**lehetőséget.
1. Az **Egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban, ha az alkalmazást **IDP** által kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    a. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`http://<YOURBASEURL>/IdP`

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://<YOURBASEURL>/IdP/SSO.aspx`

1. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<YOURBASEURL>`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Lépjen kapcsolatba [az OutSystems ügyféltámogatási csapatával,](mailto:support@outsystems.com) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az SAML aláíró tanúsítvány szakaszban keresse meg az **összevonási** **metaadatok XML-jét,** és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

   ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. Az **OutSystems Beállítása Az Azure AD** szakaszban másolja a megfelelő URL-cím(ek) a követelmény alapján.

   ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-outsystems-azure-ad"></a>OutSystems Azure AD konfigurálása

Az OutSystems oldalon való egyszeri bejelentkezés konfigurálásához le kell töltenie az [IdP forge](https://www.outsystems.com/forge/component-overview/599/idp) összetevőt, konfigurálnia kell az [utasításokat.](https://success.outsystems.com/Documentation/Development_FAQs/How_to_configure_OutSystems_to_use_identity_providers_using_SAML#Configure_your_application_to_use_IdP_connector) Az összetevő telepítése és a szükséges kódmódosítások elvégzése után konfigurálja az Azure AD-t az Összevonási metaadat-XML azure-ból való letöltésével, majd töltse fel az OutSystems IdP-összetevőre az alábbi [utasításoknak](https://success.outsystems.com/Documentation/Development_FAQs/How_to_configure_OutSystems_to_use_identity_providers_using_SAML#Azure_AD_.2F_ADFS)megfelelően.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy tesztfelhasználót hoz létre az Azure Portalon B. Simon néven.

1. Az Azure Portal bal oldali ablaktáblájában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd válassza az **Összes felhasználó**lehetőséget.
1. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.
1. A **Felhasználói** tulajdonságok csoportban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B. Simon`.  
   1. A **Felhasználónév** mezőbe írja username@companydomain.extensionbe a mezőt. Például: `BrittaSimon@contoso.com`.
   1. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet, majd írja le a **Jelszó** mezőben megjelenő értéket.
   1. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi b. Simon azure egyszeri bejelentkezés t az OutSystems Azure AD-hez való hozzáférés biztosításával.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza **az OutSystems Azure AD lehetőséget.**
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B. Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-outsystems-azure-ad-test-user"></a>OutSystems Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy B.Simon nevű felhasználó jön létre az OutSystems rendszerben. OutSystems támogatja a just-in-time felhasználói kiépítés, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nincs műveletelem. Ha a felhasználó már nem létezik az OutSystems rendszerben, a hitelesítés után egy új jön létre.

### <a name="test-sso"></a>SSO tesztelése

Amikor kiválasztja az OutSystems Azure AD csempét a hozzáférési panelen, automatikusan be kell jelentkeznie az OutSystems Azure AD-be, amelyhez beállítja az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a 8x8-as sal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a 8x8 között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b34a6edf-e745-4aec-b0b2-7337473d64c5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/20/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c598222978a1c831be6f5e9db9eb87b2d6b6b96
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78968642"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-8x8"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a 8x8-as sal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a 8x8-as t az Azure Active Directoryval (Azure AD). Ha a 8x8-as t integrálja az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a 8x8-as.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve a 8x8-as azure-beli AD-fiókokkal.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* 8x8-as előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* A 8x8 támogatja az **SP és az IDP** által kezdeményezett sso-t

* A 8x8-as beállítás után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből terjed. [Megtudhatja, hogy miként kényszerítheti ki a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egy bérlőben.

## <a name="adding-8x8-from-the-gallery"></a>8x8 hozzáadása a galériából

A 8x8-as Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a 8x8-at a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **Hozzáadás a gyűjteményből szakaszban** írja be a **8x8** értéket a keresőmezőbe.
1. Válassza a **8x8** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-8x8"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése a 8x8-as csomaghoz

Konfigurálja és tesztelje az Azure AD SSO-t a 8x8-as sal egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között 8x8-ban.

Az Azure AD SSO 8x8-as beállításához és teszteléséhez hajtsa végre az alábbi építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    1. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Állítsa be a 8x8 Egyszeri bejelentkezést](#configure-8x8-sso)** - az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
    1. **[Hozzon létre 8x8-as tesztfelhasználót](#create-8x8-test-user)** – a B.Simon 8x8-as megfelelőjével, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **8x8-as** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    a. Az **Azonosító** mezőbe írjon be egy URL-címet:`https://sso.8x8.com/saml2`

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet:`https://sso.8x8.com/saml2`

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában keresse meg a **Tanúsítvány (Base64)** lehetőséget, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez. A tanúsítványt később az oktatóanyag ban a **konfigurálása 8x8 SSO** szakaszban.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **8x8 beállítása** szakaszban másolja az URL-cím(eke)t, és ezeket az URL-értékeket az oktatóanyag későbbi részében fogja használni.

    ![Konfigurációs URL-címek másolása](./media/8x8virtualoffice-tutorial/copy-configuration-urls.png)

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t a 8x8-as hozzáférés biztosításával.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **8x8 lehetőséget.**
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-8x8-sso"></a>8x8 SSO konfigurálása

A következő rész a bemutató attól függ, hogy milyen előfizetés van a 8x8.

* A Configuration Manager t használó 8x8-as és X sorozatú ügyfelek esetében a Configuration Manager [konfigurálása a Configuration Manager programban.](#configure-8x8-configuration-manager)
* A Felügyeleti fiókkezelőt használó virtuális office-ügyfelek esetében olvassa el [a 8x8-as fiókkezelő konfigurálása című.](#configure-8x8-account-manager)

### <a name="configure-8x8-configuration-manager"></a>A 8x8-as konfigurációkezelő konfigurálása

1. Jelentkezzen be a 8x8 [Configuration Manager bevezetőbe.](https://vo-cm.8x8.com/)

1. A kezdőlapon kattintson az **Identitáskezelés**elemre.

    ![8x8-as konfigurációkezelő](./media/8x8virtualoffice-tutorial/configure1.png)

1. Jelölje be **az Egyszeri bejelentkezés (SSO) jelölőnégyzetet,** majd válassza a **Microsoft Azure AD**lehetőséget.

    ![8x8-as konfigurációkezelő](./media/8x8virtualoffice-tutorial/configure2.png)

1. Másolja a három URL-t és az aláírási tanúsítványt az Egyetlen **bejelentkezés beállítása saml-lel** lapról az Azure AD-ben a 8x8 Configuration Manager **Microsoft Azure AD SAML settings** szakaszába.

    ![8x8-as konfigurációkezelő](./media/8x8virtualoffice-tutorial/configure3.png)

    a. A **bejelentkezési URL másolása** az **IDP bejelentkezési URL-címére.**

    b. Másolja **az Azure AD-azonosítót** az **IDP-kibocsátó URL-címére/URL-címére.**

    c. **Jelentkezzen URL-cím** másolása az **IDP-kijelentkezés URL-címére.**

    d. Töltse le **a tanúsítványt (Base64)** és töltse fel a **tanúsítványba.**

    e. Kattintson a **Mentés** gombra.

### <a name="configure-8x8-account-manager"></a>8x8-as fiókkezelő konfigurálása

1. Jelentkezzen be a 8x8-as virtuális office-bérlőbe rendszergazdaként.

1. Válassza a **Virtuális irodai fiók Mgr** elem lehetőséget az alkalmazáspanelen.

    ![Konfigurálás az alkalmazás oldalon](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_001.png)

1. Válassza a kezelni kívánt **Üzleti** fiók lehetőséget, és kattintson **a Bejelentkezés** gombra.

    ![Konfigurálás az alkalmazás oldalon](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_002.png)

1. Kattintson a menülista **FIÓKOK** fülére.

    ![Konfigurálás az alkalmazás oldalon](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_003.png)

1. Kattintson **az Egyszeri bejelentkezés elemre** a fiókok listájában.

    ![Konfigurálás az alkalmazás oldalon](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_004.png)

1. A Hitelesítési módszerek csoportban válassza **az Egyszeri bejelentkezés** lehetőséget, és kattintson **az SAML gombra.**

    ![Konfigurálás az alkalmazás oldalon](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_005.png)

1. Az **SAML Single Sign on (SAML Single Sign on) szakaszban** hajtsa végre az alábbi lépéseket:

    ![Konfigurálás az alkalmazás oldalon](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_006.png)

    a. A **Bejelentkezési URL-cím** szövegmezőbe illessze be **a bejelentkezési URL-cím** értékét, amelyet az Azure Portalról másolt.

    b. A **Kijelentkezés URL-cím** beírt mezőbe illessze be **a kijelentkezési URL-cím** értékét, amelyet az Azure Portalról másolt.

    c. A **Kiállító URL-címmezőjébe** illessze be az **Azure AD-azonosító** értékét, amelyet az Azure Portalról másolt.

    d. Kattintson a **Tallózás** gombra az Azure Portalról letöltött tanúsítvány feltöltéséhez.

    e. Kattintson a **Mentés** gombra.

### <a name="create-8x8-test-user"></a>8x8-as tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a 8x8-ban. A [8x8 támogatási csapatával](https://www.8x8.com/about-us/contact-us) együttműködve adja hozzá a felhasználókat a 8x8-as platformon. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>SSO tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a hozzáférési panelen a 8x8-as csempére kattint, automatikusan be kell jelentkeznie arra a 8x8-asba, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a 8x8-as ta-](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlés a Microsoft Cloud App Security alkalmazásban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [A 8x8 védelme fejlett láthatósággal és vezérlőkkel](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
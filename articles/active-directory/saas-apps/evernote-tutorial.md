---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az Evernote-val | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és az Evernote között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55216e9eb4bd5cda2635564480751459f05e8309
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "71121629"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-evernote"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az Evernote-val

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az Evernote-t Azure Active Directory (Azure AD-val). Ha az Azure AD-val integrálja az Evernote-t, a következőket teheti:

* Az Evernote-hoz hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek az Evernote-ba az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Evernote egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* Az Evernote támogatja **az SP-t és a identitásszolgáltató** KEZDEMÉNYEZett SSO

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egyetlen bérlőn.

## <a name="adding-evernote-from-the-gallery"></a>Evernote hozzáadása a gyűjteményből

A Evernote Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az Evernote-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a " **Evernote** " kifejezést a keresőmezőbe.
1. Válassza ki az **Evernote** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-evernote"></a>Azure AD-beli egyszeri bejelentkezés konfigurálása és tesztelése az Evernote-hoz

Konfigurálja és tesztelje az Azure AD SSO-t az Evernote-val egy **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó számára az Evernote-ban.

Az Azure AD SSO az Evernote-vel való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. Az **[EVERNOTE SSO konfigurálása](#configure-evernote-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre Evernote-teszt felhasználót](#create-evernote-test-user)** – a felhasználó Azure ad-beli képviseletéhez kapcsolódó B. Simon-partneri kapcsolattal.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)az **Evernote** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az **ALAPszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    Az **azonosító** szövegmezőbe írja be az URL-címet:`https://www.evernote.com/saml2`

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL** szövegmezőbe írja be a következő URL-címet:`https://www.evernote.com/Login.action`

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

7. Az **aláírási** beállítások módosításához kattintson a **Szerkesztés** gombra az **SAML-aláíró tanúsítvány** párbeszédpanel megnyitásához.

    ![image](common/edit-certificate.png) 

    ![image](./media/evernote-tutorial/samlassertion.png)

    a. Jelölje be az **SAML-válasz aláírása és az érvényesítési** beállítás az **aláíráshoz lehetőséget**.

    b. Kattintson a **Mentés** gombra

1. Az **Evernote beállítása** szakaszban másolja ki a megfelelő URL-címet (ka) t a követelmény alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést az Evernote hozzáférésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza az **Evernote**elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-evernote-sso"></a>Az Evernote SSO konfigurálása

1. Ahhoz, hogy automatizálja a konfigurációt az Evernote-n belül, telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése**lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **Setup Evernote** lehetőségre, majd az Evernote-alkalmazásra irányítja. Itt adja meg a rendszergazdai hitelesítő adatokat az Evernote-ba való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-6-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani az Evernote-t, nyisson meg egy új böngészőablakot, és jelentkezzen be a Evernote vállalati webhelyére rendszergazdaként, és hajtsa végre a következő lépéseket:

4. Ugrás a **"felügyeleti konzolra"**

    ![Rendszergazda – konzol](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

5. A **"felügyeleti konzol"** lapon lépjen a **"biztonság"** elemre, és válassza az **"egyszeri bejelentkezés"** lehetőséget.

    ![Egyszeri bejelentkezés – beállítás](./media/evernote-tutorial/tutorial_evernote_sso.png)

6. Konfigurálja a következő értékeket:

    ![Tanúsítvány beállítása](./media/evernote-tutorial/tutorial_evernote_certx.png)
    
    a.  **Egyszeri bejelentkezés engedélyezése:** Az egyszeri bejelentkezés alapértelmezés szerint engedélyezve van (kattintson az **egyszeri bejelentkezés letiltása** lehetőségre az egyszeri bejelentkezésre vonatkozó követelmény eltávolításához)

    b. Illessze be a **bejelentkezési URL** értékét, amelyet a Azure Portal az **SAML HTTP-kérelem URL-címe** szövegmezőbe másolt.

    c. Nyissa meg a letöltött tanúsítványt a Jegyzettömbben az Azure AD-ben, és másolja a tartalmat a "BEGIN CERTIFICATe" és a "END CERTIFICATe" kifejezésre, és illessze be az **X. 509 tanúsítvány** szövegmezőbe. 

    d. kattintson a **módosítások mentése** gombra.

### <a name="create-evernote-test-user"></a>Evernote-teszt felhasználó létrehozása

Ahhoz, hogy lehetővé váljon az Azure AD-felhasználók számára az Evernote-ba való bejelentkezés, az Evernote-ban kell kiépíteni őket.  
Az Evernote esetében a kiépítés manuális feladat.

**Felhasználói fiókok kiépítéséhez hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a Evernote vállalati webhelyre rendszergazdaként.

2. Kattintson a **"felügyeleti konzol"** elemre.

    ![Rendszergazda – konzol](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

3. A **"felügyeleti konzol"** területen lépjen a **"felhasználók hozzáadása"** elemre.

    ![Add-tesztfelhasználó](./media/evernote-tutorial/create_aaduser_0001.png)

4. **Adja hozzá a csoporttagokat** az **e-mail** szövegmezőbe, írja be a felhasználói fiók e-mail-címét, és kattintson a meghívás elemre **.**

    ![Add-tesztfelhasználó](./media/evernote-tutorial/create_aaduser_0002.png)
    
5. A meghívás elküldése után a Azure Active Directory fiók tulajdonosa kap egy e-mailt, amely elfogadja a meghívást.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Amikor a hozzáférési panelen a Evernote csempére kattint, automatikusan be kell jelentkeznie az Evernote-ba, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További háttéranyagok

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Az Evernote kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)


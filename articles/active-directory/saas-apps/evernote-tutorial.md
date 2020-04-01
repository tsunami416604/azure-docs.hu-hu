---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az Evernote-tal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az Evernote között.
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "71121629"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-evernote"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az Evernote-tal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az Evernote-ot az Azure Active Directoryval (Azure AD). Ha integrálja az Evernote-ot az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér az Evernote-hoz.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve evernote az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Evernote egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* Az Evernote támogatja az **SP és az IDP** által kezdeményezett SSO-t

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egy bérlőben.

## <a name="adding-evernote-from-the-gallery"></a>Evernote hozzáadása a galériából

Az Evernote Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az Evernote-ot a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **gyűjtemény Hozzáadás szakaszába** írja be az **Evernote** kifejezést a keresőmezőbe.
1. Válassza az **Evernote** elemet az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-evernote"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése az Evernote-hoz

Konfigurálja és tesztelje az Azure AD SSO-t az Evernote-tal egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között az Evernote-ban.

Az Azure AD SSO evernote-tal való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    1. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja evernote SSO](#configure-evernote-sso)** -konfigurálása az egyszeri bejelentkezési beállításokat az alkalmazás oldalán.
    1. **[Hozzon létre Evernote-tesztfelhasználót](#create-evernote-test-user)** – hogy b.Simon megfelelője legyen az Evernote-ban, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)az **Evernote** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza **az egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban, ha az alkalmazást **IDP** által kezdeményezett módban szeretné konfigurálni, adja meg a következő mezők értékeit:

    Az **Azonosító** mezőbe írjon be egy URL-címet:`https://www.evernote.com/saml2`

1. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet:`https://www.evernote.com/Login.action`

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában keresse meg a **Tanúsítvány (Base64)** lehetőséget, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

7. Az **aláírási** beállítások módosításához kattintson a **Szerkesztés** gombra az **SAML aláíró tanúsítvány** párbeszédpanel megnyitásához.

    ![image](common/edit-certificate.png) 

    ![image](./media/evernote-tutorial/samlassertion.png)

    a. Válassza az **Aláírás iMT-válasz és a helyességi feltétel** beállítás **átállítási lehetőséget az Aláírási beállításhoz.**

    b. Kattintson a **Mentés gombra**

1. Az **Evernote beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

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

Ebben a szakaszban lehetővé teszi b.Simon azure egyszeri bejelentkezés használatával hozzáférést biztosít az Evernote.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza az **Evernote lehetőséget.**
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-evernote-sso"></a>Evernote SSO konfigurálása

1. Az Evernote konfigurációjának automatizálásához telepítenie kell a **My Apps Secure Sign-in böngészőbővítményt** **a Bővítmény telepítése**gombra kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Hozzáadása után kiterjesztés a böngésző, kattintson a **beállítás Evernote** irányítja, hogy az Evernote alkalmazás. Innen adja meg a rendszergazdai hitelesítő adatokat az Evernote-ba való bejelentkezéshez. A böngésző bővítmény automatikusan konfigurálja az alkalmazást, és automatizálja a 3-6.

    ![Beállítási konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani az Evernote-ot, nyisson meg egy új böngészőablakot, és jelentkezzen be az Evernote vállalati webhelyére rendszergazdaként, és hajtsa végre a következő lépéseket:

4. Ugrás az **"Admin console" (Felügyeleti konzol'**

    ![Rendszergazda-konzol](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

5. Az **"Admin Console" (Felügyeleti konzol) oldalon**válassza a **"Biztonság"** lehetőséget, és válassza **az "Egyszeri bejelentkezés" lehetőséget.**

    ![Egyszeri felállítás](./media/evernote-tutorial/tutorial_evernote_sso.png)

6. Állítsa be a következő értékeket:

    ![Tanúsítvány beállítása](./media/evernote-tutorial/tutorial_evernote_certx.png)
    
    a.  **SSO engedélyezése:** Az Egyszeri bejelentkezés alapértelmezés szerint engedélyezve van (az **Egyszeri bejelentkezés letiltása gombra** kattintva el szeretné távolítani az Egyszeri bejelentkezés követelményt)

    b. **Illessze be a bejelentkezési URL-értéket,** amelyet az Azure Portalról másolt az **SAML HTTP-kérelem URL-címének** szövegmezőjébe.

    c. Nyissa meg az Azure AD letöltött tanúsítványát egy jegyzettömbben, és másolja a tartalmat, beleértve a "BEGIN CERTIFICATE" és az "END CERTIFICATE" (END CERTIFICATE) szöveget, és illessze be az **X.509 tanúsítvány** szövegmezőbe. 

    d.Kattintson **a Módosítások mentése gombra**

### <a name="create-evernote-test-user"></a>Evernote-tesztfelhasználó létrehozása

Annak érdekében, hogy az Azure AD-felhasználók bejelentkezhessenek az Evernote-ba, ki kell építeni őket az Evernote-ba.  
Evernote esetén kiépítése manuális feladat.

**Felhasználói fiókok létesítéséhez hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be az Evernote vállalati webhelyére rendszergazdaként.

2. Kattintson a **"Felügyeleti konzol" gombra.**

    ![Rendszergazda-konzol](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

3. Az **"Admin Console" területen**nyissa meg a **"Felhasználók hozzáadása"** lehetőséget.

    ![Add-testUser](./media/evernote-tutorial/create_aaduser_0001.png)

4. Vegye fel a **csapattagokat** az **E-mail** mezőbe, írja be a felhasználói fiók e-mail címét, és kattintson a **Meghívás gombra.**

    ![Add-testUser](./media/evernote-tutorial/create_aaduser_0002.png)
    
5. A meghívás elküldése után az Azure Active Directory-fiók tulajdonosa e-mailt kap a meghívás elfogadásához.

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a hozzáférési panelen az Evernote csempére kattint, automatikusan be kell jelentkeznie arra az Evernote-ba, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki az Evernote-ot az Azure AD-vel](https://aad.portal.azure.com/)


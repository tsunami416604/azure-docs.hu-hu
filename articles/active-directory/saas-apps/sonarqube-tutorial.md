---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Sonarqube-be | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Sonarqube között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b01665e7-c3d0-4393-9286-d5bcf8cf6add
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/18/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 571d8849fd7cae5c872a56182858848dbb43ef42
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "72026686"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sonarqube"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Sonarqube-val

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Sonarqube-t az Azure Active Directoryval (Azure AD). Ha integrálja a Sonarqube-t az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Sonarqube.Control in Azure AD who has access to Sonarqube.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve sonarqube az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Szonárkabeegyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* A Sonarqube támogatja az **SP** által kezdeményezett SSO-t

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egy bérlőben.

## <a name="adding-sonarqube-from-the-gallery"></a>Sonarqube hozzáadása a galériából

A Sonarqube azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Sonarqube-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből szakaszban** írja be a **Szonárbe be** kifejezést a keresőmezőbe.
1. Válassza a **Szonárka lehetőséget** az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sonarqube"></a>Konfigurálja és tesztelje az Azure AD egyszeri bejelentkezését a Sonarqube-hoz

Konfigurálja és tesztelje az Azure AD-sSO-t a Sonarqube segítségével egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó a Sonarqube-ban.

Az Azure AD SSO sonarqube-vel való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    1. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja a Sonarqube Egyszeri bejelentkezés egyszeri](#configure-sonarqube-sso)** bejelentkezési beállításait az alkalmazás oldalon.
    1. **[Create Sonarqube teszt felhasználó](#create-sonarqube-test-user)** - a B.Simon a Sonarqube, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Sonarqube** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza ki **az egyszeri bejelentkezést.**
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet:

    * **Éles környezethez**

        `https://servicessonar.corp.microsoft.com/`

    * **Fejlesztési környezethez**

        `https://servicescode-dev.westus.cloudapp.azure.com`

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában keresse meg a **Tanúsítvány (Base64)** lehetőséget, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Sonarqube beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés használatával hozzáférést biztosít a Sonarqube.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Sonarqube**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-sonarqube-sso"></a>Sonarqube SSO konfigurálása

1. Nyisson meg egy új böngészőablakot, és jelentkezzen be rendszergazdaként a Sonarqube vállalati webhelyére.

2. Telepítse az SAML plugint a sonarqube piacról.

3. A lap bal felső részén kattintson az **ADMIN** gombra, majd keresse meg az **SAML -t.**

4. Az **SAML** lapon hajtsa végre az alábbi lépéseket:

    ![Szonárkakonfiguráció](./media/sonarqube-tutorial/config01.png)

    a. Az **Engedélyezve** beállítás bekapcsolása **igen**értékre.

    b. Az **Alkalmazásazonosító** mezőbe írja be a nevet, mint a **szonár.**

    c. A **Szolgáltató neve** mezőbe írja be a nevet, például **SAML**.

    d. A **Szolgáltatóazonosító** szövegmezőbe illessze be az **Azure AD-azonosító**értékét, amelyet az Azure Portalról másolt.

    e. Az **SAML bejelentkezési url-cím** mezőjébe illessze be a **bejelentkezési URL-cím**értékét, amelyet az Azure Portalról másolt.

    f. Nyissa meg a Base64 kódolású tanúsítványt a jegyzettömbben, másolja annak tartalmát, és illessze be a **Szolgáltatótanúsítvány** szövegmezőbe.

    g. Az **SAML felhasználói bejelentkezési attribútum** szövegmezőjébe írja be az értéket. `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`

    h. Az **SAML felhasználónév attribútum** mezőbe írja `http://schemas.microsoft.com/identity/claims/displayname`be az értéket.

    i. Az **SAML felhasználói e-mail attribútum** `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`szövegmezőjébe írja be az értéket.

    j. Kattintson a **Mentés** gombra.

### <a name="create-sonarqube-test-user"></a>Sonarqube tesztfelhasználó létrehozása

Ebben a szakaszban hozzon létre egy B.Simon nevű felhasználót a Sonarqube-ban. Együttműködve [Sonarqube ügyfél támogatási csapat](https://www.sonarsource.com/support/) a felhasználók hozzáadása a Sonarqube platformon. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat. 

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a Sonarqube csempére kattint, automatikusan be kell jelentkeznie a sonarqube-be, amelyhez beállította az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a Sonarqube-t az Azure AD-vel](https://aad.portal.azure.com/)


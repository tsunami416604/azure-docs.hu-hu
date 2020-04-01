---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a PurelyHR-sel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a PurelyHR között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 86a9c454-596d-4902-829a-fe126708f739
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56049e1b1253cd749a8e16061957c6b5b8786e3c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "72594535"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-purelyhr"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a PurelyHR-sel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a PurelyHR-t az Azure Active Directoryval (Azure AD). Ha integrálja a PurelyHR-t az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a PurelyHR.Control in Azure AD who has access to PurelyHR.
* Lehetővé teszi, hogy a felhasználók automatikusan bejelentkezve purelyHR az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* A PurelyHR egyszeri bejelentkezés (SSO) engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* A PurelyHR támogatja az **SP és az IDP** által kezdeményezett SSO-t
* A PurelyHR támogatja **a Just In Time** felhasználói kiépítést

## <a name="adding-purelyhr-from-the-gallery"></a>A PurelyHR hozzáadása a galériából

A PurelyHR azure AD-be való integrálásának konfigurálásához hozzá kell adnia a PurelyHR-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből szakaszban** írja be a **TisztánHR** kifejezést a keresőmezőbe.
1. Válassza **a Tisztán HR** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-purelyhr"></a>Az Azure AD egyszeri bejelentkezésének konfigurálása és tesztelése a PurelyHR-hez

Konfigurálja és tesztelje az Azure AD SSO-t a PurelyHR-rel egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó a PurelyHR-ben.

Az Azure AD SSO konfigurálásához és teszteléséhez a PurelyHR segítségével hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    * **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    * **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja a PurelyHR Egyszeri bejelentkezést](#configure-purelyhr-sso)** – az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
    * **[Hozzon létre purelyHR teszt felhasználó](#create-purelyhr-test-user)** - a B.Simon a PurelyHR, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **PurelyHR-alkalmazásintegrációs** lapon keresse meg a **Kezelés szakaszt,** és válassza ki **az egyszeri bejelentkezést.**
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban, ha az alkalmazást **IDP** által kezdeményezett módban szeretné konfigurálni, adja meg a következő mezők értékeit:

    A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://<companyID>.purelyhr.com/sso-consume`

1. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<companyID>.purelyhr.com/sso-initiate`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges válasz URL-címmel és bejelentkezési URL-címmel. Lépjen kapcsolatba [a PurelyHR ügyféltámogatási csapatával,](https://support.purelyhr.com/) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában keresse meg a **Tanúsítvány (Base64)** lehetőséget, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Beállítás i TisztánHR** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t a PurelyHR-hozzáférés biztosításával.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **PurelyHR**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-purelyhr-sso"></a>A PurelyHR SSO konfigurálása

1. A PurelyHR konfigurációjának automatizálásához telepítenie kell a **My Apps Secure Sign-in böngészőbővítményt** **a Bővítmény telepítése**gombra kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

1. Hozzáadása után kiterjesztés a böngésző, kattintson a **Beállítása PurelyHR** irányítja, hogy a PurelyHR alkalmazás. Innen adja meg a rendszergazdai hitelesítő adatokat a PurelyHR-be való bejelentkezéshez. A böngésző bővítmény automatikusan konfigurálja az alkalmazást, és automatizálja a 3-5.

    ![Beállítási konfiguráció](common/setup-sso.png)

1. Ha manuálisan szeretné beállítani a PurelyHR-t, nyisson meg egy új böngészőablakot, és jelentkezzen be a PurelyHR vállalati webhelyére rendszergazdaként, és hajtsa végre a következő lépéseket:

1. Nyissa meg az **irányítópultot** az eszköztár beállításai között, és kattintson az **SSO-beállítások gombra**.

1. Illessze be az értékeket az alábbi mezőkbe.

    ![Egyszeri bejelentkezés konfigurálása](./media/purelyhr-tutorial/purelyhr-dashboard-sso-settings.png)  

    a. Nyissa meg az Azure Portalról letöltött **Tanúsítvány(Bas64)** a jegyzettömbben, és másolja a tanúsítvány értékét. Illessze be a másolt értéket az **X.509 Tanúsítvány** mezőbe.

    b. Az **Idp-kiállító URL-címe** mezőbe illessze be az **Azure AD-azonosítót** az Azure Portalról másolt.

    c. Az **Idp-végpont URL-címe** mezőbe illessze be az Azure Portalról másolt **bejelentkezési URL-címet.** 

    d. Jelölje be a **Felhasználók automatikus létrehozása** jelölőnégyzetet, ha engedélyezni szeretné a felhasználók automatikus kiépítését a PurelyHR-ben.

    e. A beállítások mentéséhez kattintson a **Módosítások mentése** gombra.

### <a name="create-purelyhr-test-user"></a>PurelyHR tesztfelhasználó létrehozása

Ez a lépés általában nem szükséges, mivel az alkalmazás támogatja a közvetlenül az időben a felhasználó kiépítése. Ha az automatikus felhasználói kiépítés nincs engedélyezve, akkor a manuális felhasználólétrehozása az alábbiak szerint végezhető el.

Jelentkezzen be rendszergazdaként a Velpic SAML vállalati webhelyére, és hajtsa végre a következő lépéseket:

1. Kattintson a Kezelés fülre, és lépjen a Felhasználók szakaszra, majd az Új gombra a felhasználók hozzáadásához.

    ![felhasználó hozzáadása](./media/velpicsaml-tutorial/velpic_7.png)

2. Az **"Új felhasználó létrehozása"** párbeszédpanelen hajtsa végre a következő lépéseket.

    ![felhasználó!](./media/velpicsaml-tutorial/velpic_8.png)

    a. Az **Utónév** mezőbe írja be a B keresztnevét.

    b. A **Vezetéknév** mezőbe írja be a Simon vezetéknevét.

    c. A **Felhasználónév** mezőbe írja be a B.Simon felhasználónevét.

    d. Az **E-mail** mezőbe írja be B.Simon@contoso.com a fiók e-mail címét.

    e. A többi információ nem kötelező, szükség esetén kitöltheti.

    f. Kattintson **a MENTÉS gombra.**

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a PurelyHR csempére kattint, automatikusan be kell jelentkeznie arra a PurelyHR-be, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a PurelyHR-t az Azure AD-vel](https://aad.portal.azure.com/)

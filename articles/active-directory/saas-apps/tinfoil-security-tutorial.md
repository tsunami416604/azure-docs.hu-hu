---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a TINFOIL SECURITY-vel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a TINFOIL SECURITY között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: da02da92-e3b0-4c09-ad6c-180882b0f9f8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71d3078b553843922cd51e4e0f43ea84b6dcde16
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74170765"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tinfoil-security"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a TINFOIL SECURITY-szel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a TINFOIL SECURITY-t az Azure Active Directoryval (Azure AD). Ha integrálja a TINFOIL SECURITY-t az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a TINFOIL SECURITY szolgáltatáshoz.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve tinfoil security az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* A TINFOIL SECURITY egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* A TINFOIL SECURITY támogatja az **IDP** által kezdeményezett SSO-t

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egy bérlőben.

## <a name="adding-tinfoil-security-from-the-gallery"></a>A TINFOIL SECURITY hozzáadása a galériából

A TINFOIL SECURITY Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a TINFOIL SECURITY-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **Hozzáadás a gyűjteményből szakaszban** írja be a **TINFOIL SECURITY kifejezést** a keresőmezőbe.
1. Válassza a **TINFOIL SECURITY lehetőséget** az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-tinfoil-security"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése a TINFOIL SECURITY számára

Konfigurálja és tesztelje az Azure AD SSO-t a TINFOIL SECURITY szolgáltatással egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a TINFOIL SECURITY-ben.

Az Azure AD SSO konfigurálásához és teszteléséhez a TINFOIL SECURITY segítségével hajtsa végre az alábbi építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    * **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    * **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja a TINFOIL SECURITY SSO-t](#configure-tinfoil-security-sso)** - az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalán.
    * **[Hozzon létre TINFOIL SECURITY teszt felhasználó](#create-tinfoil-security-test-user)** - hogy egy megfelelője B.Simon a TINFOIL SECURITY, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **TINFOIL SECURITY** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció szakaszban** az alkalmazás előre konfigurált, és a szükséges URL-címek már előre kitöltött az Azure-ban. A felhasználónak mentenie kell a konfigurációt a **Mentés** gombra kattintva.

1. A Visitly alkalmazás egy adott formátumban várja az SAML-állításokat, amely megköveteli, hogy egyéni attribútumleképezéseket adjon hozzá az SAML token attribútumok konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentieken kívül a Visitly alkalmazás azt várja, hogy néhány további attribútumot kell visszaadni az SAML válaszban, amelyek az alábbiakban láthatók. Ezek az attribútumok is előre ki vannak töltve, de áttekintheti őket a követelmények nek megfelelően.

    | Név | Forrás attribútuma |
    | ------------------- | -------------|
    | accountid | UXXXXXXXXXXXXX |

    > [!NOTE]
    > Kapsz a beszámoló értékét kifejtette később a tutorial.

1. Az **SAML aláíró tanúsítvány csoportban** kattintson a **Szerkesztés** gombra az **SAML aláíró tanúsítvány** párbeszédpanel megnyitásához.

    ![SAML aláíró tanúsítvány szerkesztése](common/edit-certificate.png)

1. Az **SAML aláíró tanúsítvány szakaszban** másolja a **hüvelykujj értékét,** és mentse a számítógépre.

    ![Ujjlenyomat másolása érték](common/copy-thumbprint.png)

1. A **TINFOIL SECURITY beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés tinfoil security hozzáférést biztosít.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **TINFOIL SECURITY lehetőséget.**
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-tinfoil-security-sso"></a>Alufólia biztonsági sso konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a TINFOIL SECURITY cég webhelyére rendszergazdaként.

1. A felső eszköztáron kattintson a **Saját fiók gombra.**

    ![Irányítópult](./media/tinfoil-security-tutorial/ic798971.png "Irányítópult")

1. Kattintson a **Biztonság gombra.**

    ![Biztonság](./media/tinfoil-security-tutorial/ic798972.png "Biztonság")

1. Az **Egyszeri bejelentkezés** konfigurációs lapon hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés](./media/tinfoil-security-tutorial/ic798973.png "Egyszeri bejelentkezés")

    a. Válassza **az SAML engedélyezése**lehetőséget.

    b. Kattintson **a Kézi konfiguráció gombra.**

    c. Az **SAML Közzététel URL-címmezőjébe** illessze be az Azure Portalról másolt **bejelentkezési URL-cím** értékét

    d. Az **SAML tanúsítvány ujjlenyomat** szövegdobozában illessze be az **SAML aláíró tanúsítvány** szakaszból másolt **ujjlenyomat** értékét.
  
    e. Másolja **a fiókazonosító** értékét, és illessze be az értéket a **Forrás attribútum** szövegmezőjébe az Azure Portal Felhasználói **attribútumok & jogcímek** szakaszában.

    f. Kattintson a **Mentés** gombra.

### <a name="create-tinfoil-security-test-user"></a>A TINFOIL SECURITY tesztfelhasználólétrehozása

Annak érdekében, hogy az Azure AD-felhasználók bejelentkezhessenek a TINFOIL SECURITY szolgáltatásba, ki kell építeni őket a TINFOIL SECURITY-be. A TINFOIL SECURITY esetében a kiépítés manuális feladat.

**A felhasználó kiépítéséhez hajtsa végre az alábbi lépéseket:**

1. Ha a felhasználó egy vállalati fiók része, a felhasználói fiók létrehozásához kapcsolatba kell [lépnie a TINFOIL BIZTONSÁGI támogatási csapatával.](https://www.tinfoilsecurity.com/contact)

1. Ha a felhasználó rendszeres TINFOIL SECURITY SaaS-felhasználó, akkor a felhasználó a felhasználó bármely webhelyéhez hozzáadhat egy munkatársat. Ez elindítja azt a folyamatot, amelynek során meghívót küldanek a megadott e-mailnek egy új TINFOIL SECURITY felhasználói fiók létrehozására.

> [!NOTE]
> A TINFOIL SECURITY felhasználói fiók létrehozási eszközeinek vagy API-inak a TINFOIL SECURITY által biztosított bármely más használatával azure AD felhasználói fiókokat hozhat létre.

## <a name="test-sso"></a>SSO tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a TINFOIL SECURITY csempére kattint, automatikusan be kell jelentkeznie a TINFOIL SECURITY-be, amelyhez beállította az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a TINFOIL SECURITY-t az Azure AD-vel](https://aad.portal.azure.com/)
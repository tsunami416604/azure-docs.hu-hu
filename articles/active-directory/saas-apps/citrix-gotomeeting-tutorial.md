---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a GoToMeeting programmal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a GoToMeeting között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: bcaf19f2-5809-4e1c-acbc-21a8d3498ccf
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2835fda7b709ded9fac5503d4ba0bf4b8bdd5ec
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76290665"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-gotomeeting"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a GoToMeeting programmal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a GoToMeeting szolgáltatást az Azure Active Directoryval (Azure AD). Ha integrálja a GoToMeeting szolgáltatást az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a GoToMeeting.
* Engedélyezze, hogy a felhasználók automatikusan bejelentkezve legyenek a GoToMeeting beírásába az Azure AD-fiókjukkal.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* GoToMeeting egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* A GoToMeeting támogatja az **IDP** által kezdeményezett sso-t.
* A GoToMeeting konfigurálása után kényszerítheti a munkamenet-vezérlőket, amelyek valós időben védik a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlők a feltételes hozzáféréstől származnak. [Megtudhatja, hogy miként kényszerítheti ki a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-gotomeeting-from-the-gallery"></a>A GoToMeeting hozzáadása a galériából

A GoToMeeting azure AD-be való integrációjának konfigurálásához hozzá kell adnia a GoToMeeting-et a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **Gyűjtemény hozzáadás szakaszába** írja be a Keresőmezőbe a **GoToMeeting** kifejezést.
1. Válassza a **GoToMeeting** elemet az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.


## <a name="configure-and-test-azure-ad-single-sign-on-for-gotomeeting"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése a GoToMeeting szolgáltatáshoz

Konfigurálja és tesztelje az Azure AD SSO-t a GoToMeeting segítségével egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a GoToMeeting.For SSO to work, you need to establish a link relationship between a Azure AD user and the related user in GoToMeeting.

Az Azure AD SSO konfigurálásához és teszteléséhez a GoToMeeting segítségével hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    * **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    * **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja a GoToMeeting SSO-t](#configure-gotomeeting-sso)** - az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
    * **[Hozzon létre GoToMeeting teszt felhasználó](#create-gotomeeting-test-user)** - egy megfelelője B.Simon a GoToMeeting, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **GoToMeeting** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`https://authentication.logmeininc.com/saml/sp`

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://authentication.logmeininc.com/saml/acs`

    c. Kattintson **a további URL-ek beállítása** és az alábbi URL-címek konfigurálása elemre

    d. **Bejelentkezési URL-cím** (ezt ne hagyja üresen)

    e. A **RelayState** mezőbe írjon be egy URL-címet a következő minta használatával:

   - A GoToMeeting alkalmazáshoz használja a`https://global.gotomeeting.com`

   - A GoToTraining`https://global.gototraining.com`

   - A GoToWebinar`https://global.gotowebinar.com` 

   - GoToAssist esetén használja a`https://app.gotoassist.com`

     > [!NOTE]
     > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító és válasz URL-címével. Lépjen kapcsolatba [a GoToMeeting ügyfél támogatási csapatával](https://go.microsoft.com/fwlink/?linkid=845985) az értékek lehívásához. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **GoToMeeting beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure-hirdetésazonosító

    c. Kijelentkezés URL-címe


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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t a GoToMeeting hozzáférést biztosítva.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **GoToMeeting**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-gotomeeting-sso"></a>GoToMeeting sso konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a [GoToMeeting Szervezeti központba.](https://organization.logmeininc.com/) A rendszer kéri, hogy ellenőrizze, hogy az IdP frissítve lett-e.

2. Engedélyezze a "Saját identitásszolgáltató frissítve lett az új tartománnyal" jelölőnégyzet. Ha végzett, kattintson a **Kész** gombra.

### <a name="create-gotomeeting-test-user"></a>GoToMeeting tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a GoToMeeting alkalmazásban. A GoToMeeting támogatja a just-in-time kiépítést, amely alapértelmezés szerint engedélyezve van.

Ebben a szakaszban nincs műveletelem. Ha a felhasználó még nem létezik a GoToMeeting programban, a GoToMeeting elérésekor új felhasználó jön létre.

> [!NOTE]
> Ha manuálisan kell létrehoznia egy felhasználót, forduljon a [GoToMeeting támogatási csapatához.](https://support.logmeininc.com/gotomeeting)

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelEn a GoToMeeting csempére kattint, a rendszer automatikusan bejelentkezik arra a GoToMeeting be, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a GoToMeeting szolgáltatást az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlés a Microsoft Cloud App Security alkalmazásban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [A GoToMeeting védelme fejlett láthatósággal és vezérlőkkel](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a LinkedIn Elevate-val | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a LinkedIn-elevate között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2ad9941b-c574-42c3-bd0f-5d6ec68537ef
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 03efae5f9dec904f141a6776766850aa1f328892
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74892124"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-elevate"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a LinkedIn Elevate-tal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a LinkedIn-elválaszt az Azure Active Directoryval (Azure AD). Ha integrálja a LinkedIn-elválaszt az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a LinkedIn-elevate.Control in Azure AD who has access to LinkedIn Elevate.
* Engedélyezze, hogy a felhasználók automatikusan bejelentkezve legyenek a LinkedIn-fiókba az Azure AD-fiókjukkal.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* LinkedIn elévül egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.



* A LinkedIn Elevate támogatja az **SP-t és az IDP** által kezdeményezett sso-t
* A LinkedIn Elevate támogatja a **Just In Time** felhasználói kiépítést
* A LinkedIn-elévülés támogatja az [ **automatikus** felhasználói kiépítést](linkedinelevate-provisioning-tutorial.md)

## <a name="adding-linkedin-elevate-from-the-gallery"></a>LinkedIn-elévülve hozzáadása a gyűjteményből

A LinkedIn-elevate Azure AD-be való integrációjának konfigurálásához hozzá kell adnia a LinkedIn-elevate-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **Hozzáadás a gyűjteményből szakaszban** írja be a **LinkedIn Elevate kifejezést** a keresőmezőbe.
1. Válassza a **LinkedIn Elevate** elemet az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.


## <a name="configure-and-test-azure-ad-single-sign-on-for-linkedin-elevate"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése a LinkedIn-eloldalon

Konfigurálja és tesztelje az Azure AD SSO-t a LinkedIn Elevate segítségével egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a linkedin-elevate kapcsolódó felhasználója között.

Az Azure AD SSO konfigurálásához és teszteléséhez a LinkedIn Elevate segítségével hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    1. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja a LinkedIn Elevate SSO-t](#configure-linkedin-elevate-sso)** - az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
    1. **[Create LinkedIn Elevate teszt felhasználó](#create-linkedin-elevate-test-user)** - egy megfelelője B.Simon a LinkedIn Elevate, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **LinkedIn-integrációs** lapon keresse meg a **Kezelés szakaszt,** és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban, ha az alkalmazást **IDP** által kezdeményezett módban szeretné konfigurálni, adja meg a következő mezők értékeit:

    a. Az **Azonosító** mezőbe írja be az **entitásazonosító** értékét, és az oktatóanyag későbbi részében ismertetett Linkedin-portál entitásazonosító-értékét másolja.

    b. A **Válasz URL-cím** mezőjébe írja be a **helyességi feltétel fogyasztói hozzáférési (ACS) url-címét,** és az oktatóanyag későbbi részében ismertetett Linkedin-portálurl-címét másolja a Helyességi feltétel fogyasztói hozzáférési (ACS) URL-címének értékére.

5. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=elevate&applicationInstanceId=<InstanceId>`

1. A LinkedIn-elévülési alkalmazás az SAML-állításokat egy adott formátumban várja, amely megköveteli, hogy egyéni attribútumleképezéseket adjon hozzá az SAML token attribútumok konfigurációjához. A következő képernyőképen az alapértelmezett attribútumok listája látható, ahol a **nameidentifier** a **user.userprincipalname**. A LinkedIn Elévülés alkalmazás azt várja, hogy a névazonosító le legyen képezve **a user.mail programmal,** ezért az attribútumleképezést a Szerkesztés ikonra kattintva és az attribútumleképezés módosításával kell módosítania.

    ![image](common/edit-attribute.png)

1. A fentieken kívül a LinkedIn Elevate alkalmazás arra számít, hogy néhány további attribútum ot kell visszaadni az SAML válaszban, amelyek az alábbiakban láthatók. Ezek az attribútumok is előre ki vannak töltve, de áttekintheti őket a követelmény nek megfelelően.

    | Név | Forrás attribútuma|
    | -------| -------------|
    | Részleg | user.department (felhasználó.részleg) |

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az SAML aláíró tanúsítvány szakaszban keresse meg az **összevonási** **metaadatok XML-jét,** és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A **LinkedIn-elévülés beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t a LinkedIn-elevate hozzáférés biztosításával.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **LinkedIn Elevate**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-linkedin-elevate-sso"></a>LinkedIn-elévülése sso beállítása

1. Egy másik böngészőablakban jelentkezzen be a LinkedIn-bérlőhöz rendszergazdaként.

1. A **Fiókközpontban**kattintson a **Globális beállítások** elemre a Beállítások **csoportban.** A legördülő listából válassza az **Elevate - Elevate - Elevate AAD teszt** lehetőséget is.

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_01.png)

1. Kattintson **a VAGY ide gombra az egyes mezők betöltéséhez és másolásához az űrlapról,** és hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_03.png)

    a. Másolja **az entitásazonosítót,** és illessze be az Azure Portal **alapszintű SAML-konfigurációjának** **azonosító** szövegmezőjébe.

    b. Másolja **az egycímfelhasználói hozzáférési (ACS) URL-címét,** és illessze be a **Válasz URL-cím** mezőjébe az Azure Portal **alapszintű SAML-konfigurációjában.**

1. Nyissa meg a **LinkedIn felügyeleti beállítások szakaszát.** Töltse fel az Azure Portalról letöltött XML-fájlt az XML-fájl feltöltése beállításra kattintva.

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinelevate-tutorial/tutorial_linkedin_metadata_03.png)

1. Kattintson **a Be gombra** az SSO engedélyezéséhez. Az SSO állapota **nem csatlakozik** kapcsolatról **csatlakoztatottra változik**

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_05.png)

### <a name="create-linkedin-elevate-test-user"></a>LinkedIn-elévülve tesztfelhasználó létrehozása

LinkedIn Elevate alkalmazás támogatja a Just in time felhasználói kiépítés és hitelesítés után a felhasználók jönnek létre az alkalmazásban automatikusan. A LinkedIn Elevate portál rendszergazdai beállítások lapján megkell fordítania a **kapcsolót, amely automatikusan hozzárendeli** a licenceket az aktív Just in time provisioning-hoz, és ez licencet is hozzárendel a felhasználóhoz. A LinkedIn-elevate támogatja az automatikus felhasználói kiépítést is, itt további [részleteket](linkedinelevate-provisioning-tutorial.md) talál az automatikus felhasználói kiépítés konfigurálásáról.

   ![Azure AD-tesztfelhasználó létrehozása](./media/linkedinelevate-tutorial/LinkedinUserprovswitch.png)

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a LinkedIn-elítás csempére kattint, automatikusan be kell jelentkeznie arra a LinkedIn-elágazásba, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a LinkedIn-t az Azure AD-vel](https://aad.portal.azure.com/)


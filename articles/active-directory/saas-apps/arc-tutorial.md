---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az Arc Publishing - SSO | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az Arc Publishing - SSO között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ae609583-f875-4cb8-b68e-1b0b7938e9a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 19acb111cc672b0e044142b3f8ccdc7fb7572559
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74893280"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-arc-publishing---sso"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az Arc Publishing - SSO-val

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az Arc Publishing - SSO szolgáltatást az Azure Active Directoryval (Azure AD). Ha integrálja az Arc Publishing - Egyszeri szolgáltatást az Azure AD-vel, a következőket teheti:

* Az Azure AD-ben, aki hozzáfér az Arc Publishing - SSO.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve arc közzététel – egyszeri bejelentkezés az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Ívközzététel – Egyszeri bejelentkezés egyszeri bejelentkezés (SSO) engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.



* Arc Publishing - Az Egyszeri szolgáltatás támogatja az **SP-t és az IDP** által kezdeményezett egyszeri szolgáltatót
* Ívközzététel - Az egyszeri bejelentkezés támogatja **a Just In Time** felhasználói kiépítést


## <a name="adding-arc-publishing---sso-from-the-gallery"></a>Ívközzététel hozzáadása - Egyszeri felár a galériából

Az Arc Publishing - SSO integrálásának konfigurálásához hozzá kell adnia az Arc Publishing - SSO-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **katalógus hozzáadásszakaszában** írja be az **Arc Publishing - Egyszeri bejelentkezés** kifejezést a keresőmezőbe.
1. Válassza **az Arc Publishing - SSO** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.


## <a name="configure-and-test-azure-ad-single-sign-on-for-arc-publishing---sso"></a>Az Azure AD egyszeri bejelentkezésének konfigurálása és tesztelése az Arc Publishing - Egyszeri bejelentkezéshez

Konfigurálja és tesztelje az Azure AD SSO-t az Arc Publishing - Egyszeri szolgáltatás sal egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az Egyszeri bejelentkezés működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között az Arc Publishing - SSO-ban.

Az Azure AD SSO konfigurálásához és teszteléséhez az Arc Publishing - SSO szolgáltatással hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    1. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja az Arc Publishing - SSO Egyszeri bejelentkezést](#configure-arc-publishing---sso-sso)** – az alkalmazás oldali egyszeri bejelentkezési beállítások konfigurálásához.
    1. **[Hozzon létre ívközzétételi – egyszeri bejelentkezést tesztfelhasználó](#create-arc-publishing---sso-test-user)** – b.Simon megfelelője az Arc Publishing - Egyszeri bejelentkezés, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)az **Arc Publishing - SSO** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban, ha az alkalmazást **IDP** által kezdeményezett módban szeretné konfigurálni, adja meg a következő mezők értékeit:

    a. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`https://www.okta.com/saml2/service-provider/<Unique ID>`

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://arcpublishing-<Customer>.okta.com/sso/saml2/<Unique ID>`

1. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://arcpublishing-<Customer>.okta.com/sso/saml2/<Unique ID>`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Lépjen kapcsolatba [az Arc Publishing – SSO ügyféltámogatási csapatával,](mailto:inf@washpost.com) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. Ívközzététel – Az Egyszeri bejelentkezés alkalmazás az SAML-állításokat egy adott formátumban várja, amely megköveteli, hogy egyéni attribútumleképezéseket adjon hozzá az SAML token attribútumok konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/edit-attribute.png)

1. A fentieken kívül az Arc Publishing - SSO alkalmazás azt várja, hogy néhány további attribútumot kell visszaadni az SAML válaszban, amelyek az alábbiakban láthatók. Ezek az attribútumok is előre ki vannak töltve, de áttekintheti őket a követelmény nek megfelelően.


    | Név | Forrás attribútuma|
    | ---------------| --------------- |    
    | firstName | user.givenname |
    | lastName | user.vezetéknév |
    | e-mail | user.mail |
    | csoportok | user.assignedroles |

    > [!NOTE]
    > Itt a **groups** attribútum a **user.assignedroles-szal**van leképezve. Ezek az Azure AD-ben létrehozott egyéni szerepkörök a csoportnevek leképezéséhez az alkalmazásban. További [útmutatást](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) itt talál az egyéni szerepkörök létrehozásához az Azure AD-ben

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában keresse meg a **Tanúsítvány (Base64)** lehetőséget, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. Az **Arc-közzététel beállítása - Egyszeri szolgáltatások** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés használatával az Arc Publishing - SSO hozzáférést biztosít.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza az **Arc Publishing - SSO**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-arc-publishing---sso-sso"></a>Ívközzététel konfigurálása - Egyszeri egyszeri szolgáltatások

Az **Arc Publishing – SSO** oldalon történő egyszeri bejelentkezés konfigurálásához el kell küldenie a letöltött **tanúsítványt (Base64)** és a megfelelő másolt URL-címeket az Azure Portalról az [Arc Publishing - SSO támogatási csapatnak.](mailto:inf@washpost.com) Úgy állították be ezt a beállítást, hogy az SAML SSO-kapcsolat mindkét oldalon megfelelően legyen beállítva.

### <a name="create-arc-publishing---sso-test-user"></a>Ívközzététel létrehozása – Egyszeri felhasználó egyszeri felhasználója

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre az Arc Publishing - SSO-ban. Ívközzététel – Az egyszeri bejelentkezés támogatja a just-in-time felhasználói kiépítést, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nincs műveletelem. Ha a felhasználó már nem létezik az Arc Publishing - Egyszeri bejelentkezés, egy új jön létre a hitelesítés után.

> [!Note]
> Ha manuálisan kell létrehoznia egy felhasználót, forduljon [az Arc Publishing - SSO támogatási csapatához.](mailto:inf@washpost.com)

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha a hozzáférési panelen az Arc Publishing - Egyszeri bejelentkezés csempére kattint, automatikusan be kell jelentkeznie az Arc Publishing - Egyszeri bejelentkezésbe, amelyhez be állítja az Egyszeri bejelentkezést. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki az Arc Publishing - SSO szolgáltatást az Azure AD-vel](https://aad.portal.azure.com/)


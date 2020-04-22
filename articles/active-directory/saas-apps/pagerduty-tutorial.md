---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a PagerDuty programmal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a PagerDuty között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0410456a-76f7-42a7-9bb5-f767de75a0e0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 99d9988a02b046562c2517df8a81b8ef5c778ef2
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683504"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pagerduty"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a PagerDuty-ral

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a PagerDuty-t az Azure Active Directoryval (Azure AD). Ha integrálja a PagerDuty-t az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a PagerDuty.Control in Azure AD who has access to PagerDuty.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkeznek a PagerDuty-ba az Azure AD-fiókjukkal.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* PagerDuty egyszeri bejelentkezés (SSO) engedélyezve van előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* A PagerDuty támogatja az **SP** által kezdeményezett SSO-t
* A PagerDuty konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből terjed. [Megtudhatja, hogy miként kényszerítheti ki a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)

## <a name="adding-pagerduty-from-the-gallery"></a>PagerDuty hozzáadása a galériából

A PagerDuty azure AD-be való integrálásának konfigurálásához hozzá kell adnia a PagerDuty-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből szakaszban** írja be a **PagerDuty** kifejezést a keresőmezőbe.
1. Válassza a **PagerDuty** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-pagerduty"></a>Az Azure AD egyszeri bejelentkezésének konfigurálása és tesztelése a PagerDuty számára

Konfigurálja és tesztelje az Azure AD SSO-t a PagerDuty használatával egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó pagerduty.

Az Azure AD SSO beállítása és tesztelése a PagerDuty szolgáltatással hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    1. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja pagerduty sso](#configure-pagerduty-sso)** -konfigurálása az egyszeri bejelentkezési beállításokat az alkalmazás oldalán.
    1. **[Hozzon létre PagerDuty teszt felhasználó](#create-pagerduty-test-user)** - egy megfelelője B.Simon a PagerDuty, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **PagerDuty** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    a. A Bejelentkezés az **URL-cím** mezőbe írja be az URL-címet a következő minta használatával:`https://<tenant-name>.pagerduty.com`

    b. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<tenant-name>.pagerduty.com`

    c. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://<tenant-name>.pagerduty.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Bejelentkezési URL-címmel, az azonosítóval és a válasz URL-címmel. Lépjen kapcsolatba [a PagerDuty ügyféltámogatási csapatával,](https://www.pagerduty.com/support/) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában keresse meg a **Tanúsítvány (Base64)** lehetőséget, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **PagerDuty beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t a PagerDuty elérésének biztosításával.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **PagerDuty**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-pagerduty-sso"></a>PagerDuty sso konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Pagerduty vállalati webhelyére rendszergazdaként.

2. A felső menüben kattintson a **Fiókbeállítások parancsra.**

    ![Fiókbeállítások](./media/pagerduty-tutorial/ic778535.png "Fiókbeállítások")

3. Kattintson **az Egyszeri bejelentkezés gombra.**

    ![Egyszeri bejelentkezés](./media/pagerduty-tutorial/ic778536.png "Egyszeri bejelentkezés")

4. Az **Egyszeri bejelentkezés (SSO) engedélyezése lapon hajtsa** végre a következő lépéseket:

    ![Egyszeri bejelentkezés engedélyezése](./media/pagerduty-tutorial/ic778537.png "Egyszeri bejelentkezés engedélyezése")

    a. Nyissa meg az Azure Portalról letöltött base-64 kódolású tanúsítványt a jegyzettömbben, másolja annak tartalmát a vágólapra, majd illessze be az **X.509 tanúsítvány** szövegdobozába
  
    b. A **Bejelentkezési URL-cím** mezőbe illessze be **a bejelentkezési URL-címet,** amelyet az Azure Portalról másolt.
  
    c. A **Kijelentkezés URL-címmezőjébe** illessze be **a kijelentkezési URL-címet,** amelyet az Azure Portalról másolt.

    d. Válassza **a Felhasználónév/jelszó bejelentkezésengedélyezése**lehetőséget.

    e. Jelölje be **a Pontos hitelesítési környezet összehasonlításának megkövetelése** jelölőnégyzetet.

    f. Kattintson a **Save Changes** (Módosítások mentése) gombra.

### <a name="create-pagerduty-test-user"></a>PagerDuty tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezhessenek a PagerDuty-ba, ki kell építeni őket a PagerDuty-ba. A PagerDuty esetében a kiépítés manuális feladat.

> [!NOTE]
> A Pagerduty által biztosított bármely más Pagerduty felhasználói fiók-létrehozási eszköz vagy API-k használatával azure Active Directory felhasználói fiókokat hozhat létre.

**Felhasználói fiók kiépítéséhez hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **Pagerduty** bérlő.

2. A felső menüben kattintson a **Felhasználók parancsra.**

3. Kattintson **a Felhasználók hozzáadása gombra.**
   
    ![Felhasználók hozzáadása](./media/pagerduty-tutorial/ic778539.png "Felhasználók hozzáadása")

4.  A **Csapat meghívása** párbeszédpanelen hajtsa végre az alábbi lépéseket:
   
    ![Hívja meg csapatát](./media/pagerduty-tutorial/ic778540.png "Hívja meg csapatát")

    a. Írja be a felhasználó **vezeték- és utónevét,** például **B.Simon**. 
   
    b. Adja meg a felhasználó **e-mail** címét, például **\@b.simon contoso.com**.
   
    c. Kattintson **a Hozzáadás**gombra, majd **a Meghívók küldése parancsra.**
   
    > [!NOTE]
    > Minden hozzáadott felhasználó kap egy meghívást, hogy hozzon létre egy PagerDuty fiókot.

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a PagerDuty csempére kattint, automatikusan be kell jelentkeznie arra a PagerDuty-ba, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a PagerDuty szolgáltatást az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlés a Microsoft Cloud App Security alkalmazásban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [A PagerDuty védelme fejlett láthatósággal és vezérlőkkel](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)


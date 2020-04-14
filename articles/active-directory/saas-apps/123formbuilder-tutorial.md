---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a 123FormBuilder SSO-val | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a 123FormBuilder egyszeri bejelentkezés között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5211910a-ab96-4709-959a-524c4d57c43e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/14/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 947a9d632089b18f6b950c5eecbcb74d061f32eb
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81274213"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-123formbuilder-sso"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a 123FormBuilder egyszeri bejelentkezési rendszerrel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a 123FormBuilder SSO-t az Azure Active Directoryval (Azure AD). Ha integrálja a 123FormBuilder SSO-t az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a 123FormBuilder SSO.Control in Azure AD who has access to 123FormBuilder SSO.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkeznek a 123FormBuilder SSO-ba az Azure AD-fiókjukkal.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* 123FormBuilder Egyszeri bejelentkezés (SSO) engedélyezve van az előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* A 123FormBuilder SSO támogatja az **SP-t, az IDP** pedig kezdeményezte az SSO-t.
* 123FormBuilder Egyszeri bejelentkezés támogatja **a Just In Time** felhasználói kiépítést.
* A 123FormBuilder Egyszeri bejelentkezés konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlő a feltételes hozzáféréstől terjed. [Megtudhatja, hogy miként kényszerítheti ki a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)

## <a name="adding-123formbuilder-sso-from-the-gallery"></a>123FormBuilder SSO hozzáadása a galériából

A 123FormBuilder SSO Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a 123FormBuilder SSO-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből szakaszban** írja be a **123FormBuilder SSO** kifejezést a keresőmezőbe.
1. Válassza ki a **123FormBuilder SSO** elemet az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-123formbuilder-sso"></a>Konfigurálja és tesztelje az Azure AD egyszeri bejelentkezését a 123FormBuilder egyszeri bejelentkezéshez

Konfigurálja és tesztelje az Azure AD SSO-t a 123FormBuilder SSO-val egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a 123FormBuilder SSO-ban.

Az Azure AD SSO 123FormBuilder SSO-val történő konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    * **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    * **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja a 123FormBuilder SSO-t](#configure-123formbuilder-sso)** - az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
    * **[Hozzon létre 123FormBuilder SSO teszt felhasználó](#create-123formbuilder-sso-test-user)** - egy megfelelője B.Simon 123FormBuilder SSO, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **123FormBuilder Egyszeri** bejelentkezés alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban Ha az alkalmazást **IDP** által kezdeményezett módban kívánja konfigurálni, hajtsa végre a következő lépéseket:

    a. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`https://www.123formbuilder.com/saml/azure_ad/<tenant_id>/metadata`


    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://www.123formbuilder.com/saml/azure_ad/<tenant_id>/acs`

5. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://www.123formbuilder.com/saml/azure_ad/<tenant_id>/sso`

    > [!NOTE]
    > Ezek az értékek nem valósak. Ezeket az értékeket a tényleges URL-címekből és azonosítóból kell frissítenie, amelyet az oktatóanyag későbbi részében ismertetünk.

1. A **Setup single sign-on with SAML (Egyszeri bejelentkezés SAML)-bejelentkezés** lapon az **SAML aláíró tanúsítvány szakaszban** keresse meg az **összevonási metaadat-XML-t,** és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A **Set up 123FormBuilder SSO (Beállítás: 123FormBuilder SSO)** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t a 123FormBuilder egyszeri egyszeri bejelentkezés engedélyezésével.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **123FormBuilder SSO**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-123formbuilder-sso"></a>123Formbuilder sso konfigurálása

1. Az egyszeri bejelentkezés konfigurálásához a **123FormBuilder Egyszeri bejelentkezési** oldalon, folytassa [https://www.123formbuilder.com/form-2709121/](https://www.123formbuilder.com/form-2709121/) a következő lépésekkel:

    ![Egyszeri bejelentkezés konfigurálása](./media/123formbuilder-tutorial/submit.png) 

    a. Az **E-mail** mezőbe írja be a `B.Simon@Contoso.com`felhasználó e-mail címét a programhoz hasonlóan.

    b. Kattintson **a Töltse fel** és böngésszen a letöltött metaadat-XML-fájlban, amelyet az Azure Portalról töltött le.

    c. Kattintson **a ŰRLAP küldése gombra.**

2. A **Microsoft Azure AD – Egyszeri bejelentkezés – Az alkalmazásbeállítások konfigurálása hajtsa** végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/123formbuilder-tutorial/url3.png)

    a. Ha az alkalmazást **IDP által kezdeményezett módban**szeretné konfigurálni, másolja a példány **azonosító** értékét, és illessze be az Azure Portal **Alapszintű SAML-konfiguráció** szakaszában az **Azonosító** szövegmezőbe.

    b. Ha az alkalmazást **IDP által kezdeményezett módban**szeretné konfigurálni, másolja a **példány válasz URL-értékének** másolatát, és illessze be a **Válasz URL-szövegmezőbe** az Azure Portal **Alapszintű SAML-konfiguráció** szakaszában.

    c. Ha az alkalmazást SP **által kezdeményezett módban**szeretné konfigurálni, másolja a példány **SIGN ON URL-értékét,** és illessze be **a Bejelentkezési URL-cím** beírt szövegmezőbe az Azure Portal **alapszintű SAML-konfigurációs** szakaszában.

### <a name="create-123formbuilder-sso-test-user"></a>123FormBuilder SSO-tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a 123FormBuilder SSO-ban. 123FormBuilder egyszeri bejelentkezés támogatja a just-in-time felhasználói kiépítés, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nincs műveletelem. Ha a felhasználó már nem létezik a 123FormBuilder SSO, egy új jön létre a hitelesítés után.

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha a hozzáférési panelen a 123FormBuilder SSO csempére kattint, automatikusan be kell jelentkeznie a 123FormBuilder sso-ba, amelyhez beállította az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a 123FormBuilder SSO-t az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlés a Microsoft Cloud App Security alkalmazásban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

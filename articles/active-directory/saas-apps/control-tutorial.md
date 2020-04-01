---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a vezérlővel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Folytonosság-vezérlés között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1cb7f505-0d06-44b0-95b1-65b470e97092
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d5a3383c6c79f3cde901c244eafb39bb0f479176
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67164912"
---
# <a name="tutorial-integrate-continuity-control-with-azure-active-directory"></a>Oktatóanyag: Folytonosság-vezérlés integrálása az Azure Active Directoryval

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Folytonosság-vezérlést (Control) az Azure Active Directoryval (Azure AD). Ha integrálja a vezérlést az Azure AD-vel, a következőket teheti:

* Kezelheti az Azure AD-ben, aki hozzáfér a Control.
* Lehetővé teszi, hogy a felhasználók automatikusan bejelentkezve a control az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [itt](https://azure.microsoft.com/pricing/free-trial/)egy hónapos ingyenes próbaverziót kaphat.
* A control egyszeri bejelentkezés (SSO) engedélyezve van előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben. A vezérlő támogatja az **SP** által kezdeményezett sso-t.

## <a name="adding-control-from-the-gallery"></a>Vezérlő hozzáadása a galériából

A Control azure AD-be való integrálásának konfigurálásához hozzá kell adnia a vezérlőt a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **Gyűjtemény hozzáadás szakaszába** írja be a **Vezérlő kifejezést** a keresőmezőbe.
1. Válassza a **Vezérlés** elemet az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD SSO-t a Vezérlővel egy **Britta Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó a vezérlőben.

Az Azure AD SSO vezérléssel való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Állítsa be az egyszeri bejelentkezést](#configure-control-sso)** – az egyszeri bejelentkezés i beállításainak konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre egy vezérlő teszt felhasználó](#create-control-test-user)** - egy megfelelője Britta Simon a vezérlő, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)keresse meg a **Kezelés** **szakaszt,** és válassza az **Egyszeri bejelentkezés**lehetőséget.
1. Az **Egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** lapon adja meg a következő mező értékeit:

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<SUBDOMAIN>.continuity.net/auth/saml`

    > [!Note]
    > Az érték nem valós. Frissítse az értéket a megfelelő altartománnyal. Az SSO altartomány a [vezérléshitelesítési stratégiákon](https://control.continuity.net/settings/account_profile#tab/security)konfigurálható. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. Az **SAML aláíró tanúsítvány csoportban** kattintson a **Szerkesztés** gombra az **SAML aláíró tanúsítvány** párbeszédpanel megnyitásához.

    ![SAML aláíró tanúsítvány szerkesztése](common/edit-certificate.png)

1. Az **SAML aláíró tanúsítvány szakaszban** másolja az **ujjlenyomatot,** és mentse a számítógépre.

    ![Ujjlenyomat másolása érték](common/copy-thumbprint.png)

1. A **Vezérlés beállítása csoportban** másolja a bejelentkezési URL-címet, és mentse a számítógépre.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-control-sso"></a>Vezérlő sso konfigurálása

Az egyszeri bejelentkezés a **vezérlési** oldalon való konfigurálásához frissítenie kell az egyszeri bejelentkezési hitelesítési beállításokat a [Control Authentication Strategies (Vezérléshitelesítési stratégiák)](https://control.continuity.net/settings/account_profile#tab/security)oldalon. Frissítse **az SAML SSO URL-címét** a **bejelentkezési URL-címmel** és a **tanúsítványujjlenyomattal** az Azure Portal **ujjlenyomatértékével.**

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy tesztfelhasználót hoz létre az Azure Portalon Britta Simon néven.

1. Az Azure Portal bal oldali ablaktáblájában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd válassza az **Összes felhasználó**lehetőséget.
1. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.
1. A **Felhasználói** tulajdonságok csoportban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `Britta Simon`.  
   1. A **Felhasználónév** mezőbe írja username@companydomain.extensionbe a mezőt. Például: `BrittaSimon@contoso.com`.
   1. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet, majd írja le a **Jelszó** mezőben megjelenő értéket.
   1. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés t azáltal, hogy hozzáférést biztosít a control.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Vezérlés**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a Felhasználó lista **Britta Simon** elemet, majd kattintson a képernyő alján található **Kijelölés** gombra.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-control-test-user"></a>Vezérlőteszt-felhasználó létrehozása

Ebben a szakaszban hozzon létre egy felhasználó nevű Britta Simon a control. Együttműködve [a Control támogatási csapatával](mailto:help@continuity.net) a felhasználók hozzáadása a Vezérlő platformon. Britta Simon Azure **AD-felhasználónevével** feltöltheti **identitásszolgáltatójának felhasználói azonosítóját** a controlban. A felhasználókat létre kell hozni, és **az identitásszolgáltató felhasználói azonosítóját** be kell állítani a Vezérlés ben, mielőtt használhatnák az egyszeri bejelentkezést.

### <a name="test-sso"></a>SSO tesztelése

Amikor a Vezérlőpanelt választja a Hozzáférési panelen, a rendszer automatikusan bejelentkezik arra a vezérlőbe, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

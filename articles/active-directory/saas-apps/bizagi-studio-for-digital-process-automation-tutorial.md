---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Bizagi Studio for Digital Process Automation programmal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Bizagi Studio for Digital Process Automation között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: af3d4613-c3fb-485c-b7b9-c385713e6f8f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 85f7dd96bc2767b98174bee2cadaa93a6bfa1459
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78207511"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-bizagi-studio-for-digital-process-automation"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Bizagi Studio for Digital Process Automation szolgáltatással

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Bizagi Studio for Digital Process Automation alkalmazást az Azure Active Directoryval (Azure AD). Ha integrálja a Bizagi Studio for Digital Process Automation alkalmazást az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-t, aki hozzáfér a Bizagi Studio for Digital Process Automation szolgáltatáshoz.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve bizagi Studio for Digital Process Automation az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Bizagi Studio for Digital Process Automation egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* Bizagi Studio for Digital Process Automation támogatja **SP** kezdeményezett SSO
* Miután konfigurálta a Bizagi Studio for Digital Process Automation alkalmazást, kényszerítheti a munkamenet-vezérlőket, amelyek valós időben védik a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből terjed. [Megtudhatja, hogy miként kényszerítheti ki a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)

## <a name="adding-bizagi-studio-for-digital-process-automation-from-the-gallery"></a>Bizagi Studio digitális folyamatautomatizáláshoz hozzáadása a galériából

A Bizagi Studio for Digital Process Automation azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Bizagi Studio for Digital Process Automation alkalmazást a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből szakaszban** írja be a **Bizagi Studio for Digital Process Automation** kifejezést a keresőmezőbe.
1. Válassza a **Bizagi Studio for Digital Process Automation (Az** eredmények panelen) lehetőséget, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-bizagi-studio-for-digital-process-automation"></a>Az Azure AD egyszeri bejelentkezésének konfigurálása és tesztelése a Bizagi Studio for Digital Process Automation számára

Konfigurálja és tesztelje az Azure AD SSO-t a Bizagi Studio for Digital Process Automation segítségével egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Bizagi Studio for Digital Process Automation-ben.

Az Azure AD SSO konfigurálásához és teszteléséhez a Bizagi Studio for Digital Process Automation szolgáltatással hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    1. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja a Bizagi Studio for Digital Process Automation Egyszeri Bejelentkezés](#configure-bizagi-studio-for-digital-process-automation-sso)** beállítást – az alkalmazás oldalon az egyszeri bejelentkezési beállítások konfigurálásához.
    1. **[Hozzon létre Bizagi Studio for Digital Process Automation teszt felhasználó](#create-bizagi-studio-for-digital-process-automation-test-user)** - hogy egy megfelelője B.Simon bizagi Studio for Digital Process Automation, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Bizagi Studio for Digital Process Automation** alkalmazásintegrációs lapon keresse meg a Kezelés **szakaszt,** és válassza **az egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A **Bejelentkezés az URL-cím** mezőbe írjon be egy URL-címet:`https://<COMPANYNAME>.bizagi.com/<PROJECTNAME>`

    b. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet:`https://<COMPANYNAME>.bizagi.com/<PROJECTNAME>`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. Lépjen kapcsolatba [a Bizagi Studio for Digital Process Automation támogatási csapatával,](mailto:jarvein.rivera@bizagi.com) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány szakaszában** kattintson a Másolás gombra az **Alkalmazásösszevonás metaadat-címének** másolásához és mentéséhez a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t, hozzáférést biztosítva a Bizagi Studio digitális folyamatautomatizálási hozzáférést biztosít.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Bizagi Studio for Digital Process Automation**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-bizagi-studio-for-digital-process-automation-sso"></a>A Bizagi Studio konfigurálása digitális folyamatautomatizálási sso-hoz

A **Bizagi Studio for Digital Process Automation** oldalán való egyszeri bejelentkezés konfigurálásához el kell küldenie az App Federation **metaadat-címét** a [Bizagi Studio for Digital Process Automation támogatási csapatának.](mailto:jarvein.rivera@bizagi.com) Úgy állították be ezt a beállítást, hogy az SAML SSO-kapcsolat mindkét oldalon megfelelően legyen beállítva.

### <a name="create-bizagi-studio-for-digital-process-automation-test-user"></a>Bizagi Studio for Digital Process Automation tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a Bizagi Studio for Digital Process Automation alkalmazásban. Együttműködve [Bizagi Studio for Digital Process Automation támogatási csapatával](mailto:jarvein.rivera@bizagi.com) a felhasználók hozzáadásához a Bizagi Studio for Digital Process Automation platformon. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>SSO tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha a Hozzáférési panelen a Bizagi Studio for Digital Process Automation csempére kattint, automatikusan be kell jelentkeznie a Bizagi Digitális folyamatautomatizálási stúdióba, amelyhez beállította az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a Bizagi Studio for Digital Process Automation alkalmazást az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlés a Microsoft Cloud App Security alkalmazásban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
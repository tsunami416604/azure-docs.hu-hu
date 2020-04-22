---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Nitro Productivity Suite csomaggal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Nitro Productivity Suite között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 98c96c9f-18a6-4a20-919b-74fb113ee465
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/02/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ca675e43f4d20898d9f97c175da71f8d5c59e4f
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676927"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-nitro-productivity-suite"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Nitro Productivity Suite csomaggal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Nitro Productivity Suite-ot az Azure Active Directoryval (Azure AD). Ha integrálja a Nitro Productivity Suite-ot az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-t, aki hozzáfér a Nitro Productivity Suite-hoz.
* Lehetővé teszi, hogy a felhasználók automatikusan bejelentkeznek a Nitro Productivity Suite-ba az Azure AD-fiókjukkal.
* Kezelje fiókjait egyetlen központi helyen: az Azure Portalon.

Ha többet szeretne megtudni a szoftverszolgáltatásként (SaaS) alkalmazásintegrációról az Azure AD-vel, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következők szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Nitro Productivity Suite [Enterprise előfizetés.](https://www.gonitro.com/pricing)

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* A Nitro Productivity Suite támogatja az **SP** és **az IDP** által kezdeményezett sso-t.
* A Nitro Productivity Suite támogatja **a Just In Time** felhasználói kiépítést.
* A Nitro Productivity Suite konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlő a feltételes hozzáféréstől nyújt ki. További információt a [Szakadát ismerésből megtudhatja, hogy miként kényszerítheti a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)

## <a name="add-nitro-productivity-suite-from-the-gallery"></a>Nitro Productivity Suite hozzáadása a galériából

A Nitro Productivity Suite Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Nitro Productivity Suite-ot a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget.
1. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a Minden **alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **katalógus hozzáadásszakaszában** írja be a **Nitro Productivity Suite** kifejezést a keresőmezőbe.
1. Válassza ki **a Nitro Productivity Suite** az eredmények közül, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.


## <a name="configure-and-test-azure-ad-single-sign-on-for-nitro-productivity-suite"></a>Konfigurálja és tesztelje az Azure AD egyszeri bejelentkezését a Nitro Productivity Suite számára

Konfigurálja és tesztelje az Azure AD SSO-t a Nitro Productivity Suite segítségével egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy összekapcsolt kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Nitro Productivity Suite-ban.

Az Azure AD SSO nitro termelékenységi csomaggal való konfigurálásához és teszteléséhez hajtsa végre az alábbi építőelemeket:

1. [Konfigurálja az Azure AD SSO-t,](#configure-azure-ad-sso) hogy a felhasználók használhassák ezt a funkciót.
    1. [Hozzon létre egy Azure AD-tesztfelhasználót](#create-an-azure-ad-test-user) az Azure AD egyszeri bejelentkezésének teszteléséhez B.Simon nal.
    1. [Rendelje hozzá az Azure AD tesztfelhasználót,](#assign-the-azure-ad-test-user) hogy b.Simon az Azure AD egyszeri bejelentkezést használhasson.
1. [Konfigurálja a Nitro Productivity Suite Egyszeri bejelentkezési](#configure-nitro-productivity-suite-sso) beállításokat az alkalmazás oldalon.
    1. [Hozzon létre egy Nitro Productivity Suite tesztfelhasználót,](#create-a-nitro-productivity-suite-test-user) hogy b.Simon megfelelője legyen a Nitro Productivity Suite-ban, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
1. [Tesztelje az SSO-t,](#test-sso) hogy ellenőrizze, működik-e a konfiguráció.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Nitro Productivity Suite** alkalmazásintegrációs lapon keresse meg a **Kezelés** szakaszt. Válassza **ki az egyszeri bejelentkezést**.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **SAML aláíró tanúsítvány szakaszban** keresse meg **a tanúsítványt (Base64)**. A **Letöltés gombra** a tanúsítvány letöltéséhez és a számítógépre való mentéséhez válassza a Letöltés lehetőséget.

    ![Képernyőkép az SAML aláíró tanúsítvány szakaszról, kiemelve a Letöltés hivatkozással](common/certificatebase64.png)
    
1. A **Nitro Productivity Suite beállítása** csoportban válassza a Másolás ikont a **Bejelentkezési URL mellett.**
    
    ![Képernyőkép: A Nitro Productivity Suite beállítása szakasz, kiemelt URL-címekkel és másolási ikonokkal](common/copy-configuration-urls.png)
    
1. A [Nitro Admin portálon](https://admin.gonitro.com/)a **Vállalati beállítások** lapon keresse meg az **Egyszeri bejelentkezés szakaszt.** Válassza **a Setup SAML SSO beállítását.**

    a. Illessze be a **bejelentkezési URL-címet** az előző lépésből a **Bejelentkezési URL-cím** mezőbe.
    
    b. Töltse fel a **tanúsítványt (Base64)** az **X509 aláíró tanúsítvány** mező korábbi lépéséből.
    
    c. Válassza a **Küldés** lehetőséget.
    
    d. Válassza **az Egyszeri bejelentkezés engedélyezése**lehetőséget.


1. Térjen vissza az [Azure Portalra.](https://portal.azure.com/) Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon válassza az **egyszerű SAML-konfiguráció** ceruza ikonját a beállítások szerkesztéséhez.

   ![Az Egyszeri bejelentkezés beállítása SAML-lel lap képernyőképe, kiemelve a ceruza ikon](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban, ha az alkalmazást **IDP** által kezdeményezett módban szeretné konfigurálni, adja meg a következő mezők értékeit:

    a. Az **Azonosító** szövegmezőben másolja és illessze be az **SAML entitásazonosító** mezőt a [Nitro Admin portálról.](https://admin.gonitro.com/) Meg kell a következő mintát:`urn:auth0:gonitro-prod:<ENVIRONMENT>`

    b. A **Válasz URL szövegmezőjében** másolja és illessze be az **ACS URL-mezőjét** a [Nitro Admin portálról.](https://admin.gonitro.com/) Meg kell a következő mintát:`https://gonitro-prod.eu.auth0.com/login/callback?connection=<ENVIRONMENT>`

1. Válassza **a További URL-ek beállítása**lehetőséget, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban szeretné konfigurálni:

    A **Bejelentkezési URL-cím** mezőbe írja be az URL-címet:`https://sso.gonitro.com/login`

1. Kattintson a **Mentés** gombra.

1. A Nitro Productivity Suite alkalmazás elvárja, hogy az SAML-állítások egy adott formátumban legyenek, amely megköveteli, hogy egyéni attribútumleképezéseket adjon hozzá az SAML token attribútumok konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![Alapértelmezett attribútumok képernyőképe](common/default-attributes.png)

1. Az előző attribútumok mellett a Nitro Productivity Suite alkalmazás azt várja, hogy még néhány attribútumot vissza kell adni az SAML-válaszban. Ezek az attribútumok előre ki vannak töltve, de áttekintheti őket a követelmények szerint.
    
    | Name (Név)  |  Forrás attribútum|
    | ---------------| --------------- |
    | alkalmazottszáma |  user.objectid |


### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban hozzon létre egy tesztfelhasználót az Azure Portalon B.Simon nevű.

1. Az Azure Portal bal oldali ablaktáblájában válassza az **Azure Active Directory** > **felhasználói** > **minden felhasználó lehetőséget.**
1. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.
1. A **Felhasználói** tulajdonságok csoportban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőbe írja username@companydomain.extensionbe a mezőt. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszót.
   1. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban lehetővé teszi b.Simon azure egyszeri bejelentkezés trórózni a termelékenység suite használatával.

1. Az Azure Portalon válassza a **Vállalati alkalmazások** > **minden alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Nitro Productivity Suite lehetőséget.**
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![Képernyőkép a Kezelés szakaszról, kiemelve a Felhasználók és csoportok](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget. Ezután a **Hozzárendelés hozzáadása** párbeszédpanelen válassza a Felhasználók és **csoportok**lehetőséget.

    ![Képernyőkép: Felhasználók és csoportok lap, kiemelt Felhasználó hozzáadása](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a Felhasználók listájából a **B.Simon** elemet. Ezután válassza a **Kijelölés** lehetőséget a képernyő alján.
1. Ha az SAML-feltételben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából. Ezután válassza a **Kijelölés** lehetőséget a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen válassza a **Hozzárendelés lehetőséget.**

## <a name="configure-nitro-productivity-suite-sso"></a>Nitro Productivity Suite SSO konfigurálása

Az egyszeri bejelentkezés konfigurálásához a Nitro Productivity Suite oldalán küldje el a letöltött **tanúsítványt (Base64)** és a megfelelő másolt URL-címeket az Azure Portalról a [Nitro Productivity Suite támogatási csapatának.](https://www.gonitro.com/support) A támogatási csapat biztosítja, hogy az SAML SSO-kapcsolat mindkét oldalon megfelelően legyen beállítva.

### <a name="create-a-nitro-productivity-suite-test-user"></a>Nitro Productivity Suite tesztfelhasználó létrehozása

A Nitro Productivity Suite támogatja a just-in-time felhasználói kiépítést, amely alapértelmezés szerint engedélyezve van. Nincs további teendőd. Ha a felhasználó már nem létezik a Nitro Productivity Suite alkalmazásban, a hitelesítés után egy új jön létre.

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a Hozzáférési panel használatával teszteli.

Amikor kiválasztja a Nitro Productivity Suite csempét a Hozzáférési panelen, automatikusan bejelentkezik a Nitro Productivity Suite programba, amelyhez beállítja az SSO-t. További információt a [Bejelentkezés és alkalmazások indítása a Saját alkalmazások portálról című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Oktatóanyagok SaaS-alkalmazások az Azure Active Directoryval való integrálásához](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a Nitro Productivity Suite-ot az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlés a Microsoft Cloud App Security alkalmazásban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Védje meg a Nitro Productivity Suite-ot a fejlett láthatósággal és vezérléssel](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)


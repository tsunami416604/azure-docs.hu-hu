---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a nitro hatékonyságnövelő csomaggal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a nitro hatékonyságnövelő csomag között.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81676927"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-nitro-productivity-suite"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a nitro hatékonyságnövelő csomaggal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a nitro Productivity Suite-t Azure Active Directory (Azure AD) használatával. A nitro hatékonyságnövelő csomag Azure AD-val való integrálásakor a következőket teheti:

* Szabályozza az Azure AD-t, aki hozzáfér a nitro hatékonyságnövelő csomaghoz.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a nitro hatékonyságnövelő csomagba az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti: a Azure Portal.

Ha többet szeretne megtudni a szolgáltatott szoftver (SaaS) alkalmazás Azure AD-integrációval kapcsolatban, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)használatával című témakört.

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következők szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Egy nitro termelékenységi csomag [nagyvállalati előfizetése](https://www.gonitro.com/pricing).

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A nitro hatékonyságnövelő csomag támogatja az **SP** és a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést.
* A nitro Productivity Suite **csak időben támogatja a** felhasználók üzembe helyezését.
* A nitro hatékonyságnövelő csomag konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. További információ: a [munkamenet-vezérlés kényszerített érvényesítése Microsoft Cloud app Security használatával](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-nitro-productivity-suite-from-the-gallery"></a>A nitro hatékonyságnövelő csomag hozzáadása a katalógusból

A nitro hatékonyságnövelő csomag Azure AD-integrációjának konfigurálásához a katalógusból a felügyelt SaaS-alkalmazások listájához hozzá kell adnia a nitro hatékonyságnövelő csomagot.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali ablaktáblán válassza a **Azure Active Directory**lehetőséget.
1. Lépjen a **vállalati alkalmazások**elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **nitro hatékonyságnövelő csomag** kifejezést a keresőmezőbe.
1. Válassza a **nitro hatékonyságnövelő csomag** lehetőséget az eredmények közül, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-single-sign-on-for-nitro-productivity-suite"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a nitro hatékonyságnövelő csomaghoz

Konfigurálja és tesztelje az Azure AD SSO-t a nitro hatékonyságnövelő csomaggal, egy **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy összekapcsolt kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a nitro Productivity Suite-ban.

Az Azure AD SSO és a nitro hatékonyságnövelő csomag konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. [Konfigurálja az Azure ad SSO](#configure-azure-ad-sso) -t, hogy a felhasználók használhatják ezt a funkciót.
    1. [Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user) az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. [Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user) , hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. [Konfigurálja a nitro termelékenységi csomag egyszeri](#configure-nitro-productivity-suite-sso) bejelentkezési beállításait az alkalmazás oldalának egyszeri bejelentkezési beállításainak konfigurálásához.
    1. [Hozzon létre egy nitro termelékenységi csomag tesztelésére szolgáló felhasználót](#create-a-nitro-productivity-suite-test-user) , hogy az a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-ügyféllel rendelkezzen.
1. Ellenőrizze az [SSO](#test-sso) -t annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **nitro hatékonyságnövelő csomag** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt. Válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** szakaszt. A **Letöltés** gombra kattintva letöltheti a tanúsítványt, és mentheti a számítógépre.

    ![Képernyőfelvétel az SAML-aláíró tanúsítványról szakasz, a letöltési hivatkozás kiemelve](common/certificatebase64.png)
    
1. A **nitro hatékonyságnövelő csomag beállítása** szakaszban válassza a másolás ikont a **bejelentkezési URL-cím**mellett.
    
    ![Képernyőfelvétel a nitro hatékonyságnövelő csomag beállításáról, URL-címekkel és a másolási ikonok kiemelésével](common/copy-configuration-urls.png)
    
1. A [nitro felügyeleti portál](https://admin.gonitro.com/) **Vállalati beállítások** lapján keresse meg az **egyszeri bejelentkezés** szakaszt. Válassza az **SAML SSO beállítása**lehetőséget.

    a. Illessze be a **bejelentkezési URL-címet** az előző lépésben a **bejelentkezési URL-cím** mezőbe.
    
    b. Töltse fel a **tanúsítványt (Base64)** a **X509 aláíró tanúsítványa** mező korábbi lépésében.
    
    c. Válassza a **Küldés** lehetőséget.
    
    d. Válassza az **egyszeri bejelentkezés engedélyezése**lehetőséget.


1. Térjen vissza a [Azure Portal](https://portal.azure.com/). Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon válassza az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikont a beállítások szerkesztéséhez.

   ![Képernyőfelvétel: egyszeri bejelentkezés beállítása SAML-oldallal – kiemelve a ceruza ikonnal](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe másolja és illessze be az **SAML-entitás azonosítója** mezőt a [nitro felügyeleti portálról](https://admin.gonitro.com/). A következő mintát kell tartalmaznia:`urn:auth0:gonitro-prod:<ENVIRONMENT>`

    b. A **Válasz URL-címe** szövegmezőbe másolja és illessze be az **ACS URL-címét** a [nitro felügyeleti portálról](https://admin.gonitro.com/). A következő mintát kell tartalmaznia:`https://gonitro-prod.eu.auth0.com/login/callback?connection=<ENVIRONMENT>`

1. Válassza a **további URL-címek beállítása**lehetőséget, majd hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet:`https://sso.gonitro.com/login`

1. Kattintson a **Mentés** gombra.

1. A nitro Productivity Suite-alkalmazás azt várja, hogy az SAML-állítások megadott formátumban legyenek, így egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![Az alapértelmezett attribútumok képernyőképe](common/default-attributes.png)

1. Az előző attribútumok mellett a nitro Productivity Suite alkalmazás néhány további attribútumot vár, amelyeket vissza kell adni az SAML-válaszban. Ezek az attribútumok előre ki vannak töltve, de a követelmények szerint áttekinthetők.
    
    | Name (Név)  |  Forrás attribútum|
    | ---------------| --------------- |
    | employeeNumber |  User. ObjectId |


### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory** > **felhasználók** > **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőben adja meg a username@companydomain.extensionnevet. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszót.
   1. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban engedélyezi a B. Simon számára az Azure egyszeri bejelentkezés használatát a nitro hatékonyságnövelő csomag elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** > **minden alkalmazás**lehetőséget.
1. Válassza az alkalmazások lista **nitro hatékonyságnövelő csomag**elemét.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![Képernyőfelvétel a kezelés szakaszról, Kiemelt felhasználók és csoportok](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**elemet. Ezután a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok**lehetőséget.

    ![Képernyőkép a felhasználók és csoportok oldalról, a felhasználó hozzáadása elem kiemelésével](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza ki a felhasználók listájából a **B. Simon** elemet. Ezután válassza a **kijelölés** lehetőséget a képernyő alján.
1. Ha az SAML-kijelentésben bármelyik szerepkör értékét várta, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a listáról a felhasználó számára. Ezután válassza a **kijelölés** lehetőséget a képernyő alján.
1. A **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelés**lehetőséget.

## <a name="configure-nitro-productivity-suite-sso"></a>A nitro hatékonyságnövelő csomag egyszeri bejelentkezésének konfigurálása

Ha be szeretné állítani az egyszeri bejelentkezést a nitro hatékonyságnövelő csomag oldalán, küldje el a letöltött **tanúsítványt (Base64)** és a megfelelő másolt URL-címeket a Azure Portal a [nitro hatékonyságnövelő csomag támogatási csapatának](https://www.gonitro.com/support). A támogatási csapat biztosítja, hogy az SAML SSO-kapcsolatok mindkét oldalon megfelelően legyenek beállítva.

### <a name="create-a-nitro-productivity-suite-test-user"></a>Egy nitro termelékenységi csomag tesztelési felhasználójának létrehozása

A nitro hatékonyságnövelő csomag támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Nincs szükség további műveletre. Ha egy felhasználó még nem létezik a nitro hatékonyságnövelő csomagban, akkor a hitelesítés után létrejön egy újat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panel használatával tesztelheti.

Amikor kiválasztja a nitro hatékonyságnövelő csomag csempét a hozzáférési panelen, automatikusan bejelentkezik a nitro hatékonyságnövelő csomagba, amelyhez be van állítva az egyszeri bejelentkezés. További információ: [Bejelentkezés és alkalmazások indítása a saját alkalmazások portálján](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További háttéranyagok

- [Oktatóanyagok SaaS-alkalmazások az Azure Active Directoryval való integrálásához](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [A nitro hatékonyságnövelő csomag kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [A nitro hatékonyságnövelő csomag a fejlett láthatósággal és ellenőrzésekkel biztosítható](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)


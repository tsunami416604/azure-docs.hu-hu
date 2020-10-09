---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a EasySSO for BitBucket | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a EasySSO között a BitBucket számára.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/18/2020
ms.author: jeedes
ms.openlocfilehash: bd15798e9b642f3abc617c0fa224c530d90b5dd6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88555564"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-easysso-for-bitbucket"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az EasySSO for BitBucket

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a EasySSO a BitBucket-be a Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja a BitBucket EasySSO-t, a következőket teheti:

* Vezérlés az Azure AD-ben, aki hozzáfér a BitBucket EasySSO.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a BitBucket EasySSO az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti: a Azure Portal.

Ha többet szeretne megtudni a szolgáltatott szoftver (SaaS) alkalmazás Azure AD-integrációval kapcsolatban, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)használatával című témakört.

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következőkre lesz szüksége:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Előfizetés az egyszeri bejelentkezéshez (SSO) engedélyezett BitBucket-EasySSO.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A EasySSO for BitBucket támogatja az SP által kezdeményezett és a identitásszolgáltató által kezdeményezett egyszeri bejelentkezést.
* A EasySSO for BitBucket támogatja az "igény szerinti" felhasználói üzembe helyezést.
* A BitBucket EasySSO konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-easysso-for-bitbucket-from-the-gallery"></a>EasySSO hozzáadása a BitBucket a gyűjteményből

A BitBucket-EasySSO az Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a EasySSO a BitBucket a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Lépjen a **vállalati alkalmazások**elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **EasySSO a BitBucket** mezőbe a keresőmezőbe.
1. Az eredmények közül válassza ki a **BitBucket EasySSO** , majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-single-sign-on-for-easysso-for-bitbucket"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a BitBucket-hez készült EasySSO

Konfigurálja és tesztelje az Azure AD SSO-t az EasySSO for BitBucket használatával egy **B. Simon**nevű tesztelési felhasználó segítségével. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy összekapcsolt kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a EasySSO for BitBucket-ben.

Az Azure AD SSO konfigurálásához és a BitBucket EasySSO való teszteléséhez hajtsa végre a következő lépéseket:

1. [Konfigurálja az Azure ad SSO](#configure-azure-ad-sso) -t, hogy a felhasználók használhatják ezt a funkciót.
    1. [Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user) az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. [Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user) , hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. [Konfigurálja a BITBUCKET SSO EasySSO](#configure-easysso-for-bitbucket-sso) az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalán.
    1. [Hozzon létre egy EasySSO a BitBucket tesztelési felhasználó](#create-an-easysso-for-bitbucket-test-user) számára, hogy a EasySSO-hez tartozó B. Simon párja legyen a BitBucket, amely a felhasználó Azure ad-képviseletéhez kapcsolódik.
1. Ellenőrizze az [SSO](#test-sso) -t annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **EasySSO a BitBucket** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt. Válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon válassza az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikont a beállítások szerkesztéséhez.

   ![Képernyőfelvétel az egyszeri Sign-On az SAML-oldallal való beállításáról, a ceruza ikon kiemelve](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be a következő mintát használó URL-címet: `https://<server-base-url>/plugins/servlet/easysso/saml`

    b. A **Válasz URL-cím** szövegmezőbe írja be a következő mintát használó URL-címet: `https://<server-base-url>/plugins/servlet/easysso/saml`

1. Válassza a **további URL-címek beállítása**lehetőséget, majd hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    - A **bejelentkezési URL** szövegmezőbe írja be a következő mintát használó URL-címet: `https://<server-base-url>/login.jsp`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Ha kétségei vannak, lépjen kapcsolatba a [EasySSO támogatási csapatával](mailto:support@techtime.co.nz) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. A BitBucket alkalmazás EasySSO egy adott formátumban várja az SAML-állításokat, így egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![Az alapértelmezett attribútumok képernyőképe](common/default-attributes.png)

1. A BitBucket alkalmazás EasySSO Emellett néhány további attribútumot is vár az SAML-válaszban. A következő táblázat ezeket mutatja be. Ezek az attribútumok előre fel vannak töltve, de a követelmények szerint áttekinthetők.
    
    | Név | Forrás attribútum|
    | ---------------| --------- |
    | urn: OID: 0.9.2342.19200300.100.1.1 | User. userPrincipalName |
    | urn: OID: 0.9.2342.19200300.100.1.3 | User. mail |
    | urn: OID: 2.16.840.1.113730.3.1.241 | felhasználó. DisplayName |
    | urn: OID: 2.5.4.4 | felhasználó. vezetéknév |
    | urn: OID: 2.5.4.42 | User. givenName |
    
    Ha az Azure AD-felhasználók **sAMAccountName** vannak konfigurálva, le kell képeznie az **urn: OID: 0.9.2342.19200300.100.1.1** alakzatot a **sAMAccountName** attribútumra.
    
1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban válassza ki a **tanúsítvány (Base64)** vagy az **összevonási metaadatok XML-** beállításainak letöltési hivatkozásait. Mentse vagy mindkettőt a számítógépre. Később szüksége lesz rá a BitBucket-EasySSO konfigurálásához.

    ![Képernyőkép az SAML aláíró tanúsítványról, a letöltési hivatkozások kiemelésével](./media/easysso-for-bitbucket-tutorial/certificate.png)
    
    Ha manuálisan szeretné konfigurálni a BitBucket EasySSO a tanúsítvánnyal, a **bejelentkezési URL-címet** és az **Azure ad-azonosítót**is át kell másolnia, és mentenie kell a számítógépére.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót (B. Simon) hoz létre a Azure Portal.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**  >  **felhasználók**  >  **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **név**mezőbe írja be a következőt: `B.Simon` .  
   1. A **Felhasználónév**mezőbe írja be a következőt: username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszót.
   1. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a EasySSO a BitBucket.

1. A Azure Portal válassza a **vállalati alkalmazások**  >  **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a EasySSO lehetőséget a **BitBucket**elemnél.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![Képernyőfelvétel a kezelés szakaszról, Kiemelt felhasználók és csoportok](common/users-groups-blade.png)

1. Válassza a **Felhasználó hozzáadása** elemet. A **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok**lehetőséget.

    ![Képernyőkép a felhasználók és csoportok oldalról, a felhasználó hozzáadása elem kiemelésével](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza ki az **B. Simon** elemet a **felhasználók** listából, majd a képernyő alján válassza a **kiválasztás** lehetőséget.
1. Ha az SAML-kijelentésben bármelyik szerepkör értékét várta, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a listáról a felhasználó számára. Ezután válassza a **kijelölés** lehetőséget a képernyő alján.
1. A **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelés**lehetőséget.

## <a name="configure-easysso-for-bitbucket-sso"></a>EasySSO konfigurálása a BitBucket SSO-hoz

1. Jelentkezzen be a Atlassian BitBucket-példányba rendszergazdai jogosultságokkal, és lépjen az **Adminisztráció** szakaszhoz. 

    ![Képernyőkép a BitBucket-példányról, a fogaskerék ikon kiemelve](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-1.png)
1. Keresse meg és válassza ki a **EasySSO**.

    ![Képernyőkép az egyszerű SSO lehetőségről](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-2.png)

1. Válassza az **SAML**lehetőséget. Ekkor megjelenik az SAML konfigurációs szakasza.

    ![Képernyőkép a EasySSO-felügyeleti oldalról, az SAML kiemelve](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-3.png)

1. Válassza a **tanúsítványok** fület, és a következő képernyő jelenik meg:

    ![Képernyőfelvétel a tanúsítványok lapról, különböző lehetőségekkel kiemelve](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-4.png)

1. Keresse meg az oktatóanyag előző szakaszában mentett **tanúsítványt (Base64)** vagy **metaadat-fájlt** . A következő módszerek egyikét folytathatja:

    - Használja a letöltött alkalmazás-összevonási **metaadat-fájlt** a számítógép helyi fájljába. Válassza a Radio **feltöltése** gombot, és kövesse az operációs rendszerének megfelelő elérési utat.

    - Nyissa meg az alkalmazás-összevonási **metaadatokat tartalmazó fájlt** , és tekintse meg a fájl tartalmát bármely egyszerű szövegszerkesztőben. Másolja a vágólapra. Válassza a **bevitel**lehetőséget, majd illessze be a vágólap tartalmát a szövegmezőbe.
 
    - Végezzen el egy teljesen manuális konfigurációt. Nyissa meg az alkalmazás-összevonási **tanúsítványt (Base64)** a fájl tartalmának megtekintéséhez bármilyen egyszerű szövegszerkesztőben. Másolja a vágólapra, majd illessze be az **identitásszolgáltató jogkivonat-aláíró tanúsítványok** szövegmezőbe. Ezután nyissa meg az **általános** lapot, és adja meg a **post kötési URL-címet** és az entitás- **azonosító** mezőket a korábban mentett **bejelentkezési URL-cím** és az **Azure ad-azonosító** megfelelő értékeivel.
 
1. Kattintson a lap alján található **Mentés** gombra. Látni fogja, hogy a metaadatok vagy tanúsítványfájl-fájlok tartalma a konfigurációs mezőkbe van-e elemezve. A BitBucket-konfiguráció EasySSO befejeződött.

1. A konfiguráció teszteléséhez lépjen a **Look & Feel (nézet** megtekintése) lapra, és válassza az **SAML-bejelentkezés gombot**. Ez lehetővé teszi egy külön gomb használatát a BitBucket bejelentkezési képernyőjén, különösen az Azure AD SAML-integráció végpontok közötti teszteléséhez. Ezt a gombot bekapcsolhatja, és beállíthatja az elhelyezést, a színt és a fordítást az éles üzemmódra is.

    ![Képernyőfelvétel az SAML-oldalról & Feel (az SAML bejelentkező gomb kiemelése) lapon](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-5.png)
    > [!NOTE]
    >Ha bármilyen problémája van, lépjen kapcsolatba a [EasySSO támogatási csapatával](mailto:support@techtime.co.nz).

### <a name="create-an-easysso-for-bitbucket-test-user"></a>EasySSO létrehozása BitBucket-teszt felhasználó számára

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a BitBucket-ben. A EasySSO for BitBucket támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint le van tiltva. Az engedélyezéshez explicit módon ellenőriznie kell, hogy **sikeresen bejelentkezett** -e a felhasználó létrehozása a EasySSO beépülő modul konfigurációjának **általános** szakaszában. Ha egy felhasználó még nem létezik a BitBucket-ben, a rendszer egy újat hoz létre a hitelesítés után.

Ha azonban nem szeretné engedélyezni az automatikus felhasználói kiépítés használatát, amikor a felhasználó először jelentkezik be, a felhasználóknak olyan felhasználói címtárakban kell lenniük, amelyekben a BitBucket-példány használja. Ez a könyvtár lehet például LDAP vagy Atlassian-tömeg.

![Képernyőkép az EasySSO beépülő modul konfigurálásának általános szakaszáról, a felhasználó létrehozása a sikeres bejelentkezéshez kiemelve](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-6.png)

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

### <a name="idp-initiated-workflow"></a>Identitásszolgáltató által kezdeményezett munkafolyamat

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panel használatával tesztelheti.

Amikor kiválasztja a BitBucket csempéhez tartozó EasySSO, automatikusan be kell jelentkeznie arra a BitBucket-példányra, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ: [Bejelentkezés és alkalmazások indítása a saját alkalmazások portálján](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

### <a name="sp-initiated-workflow"></a>Az SP által kezdeményezett munkafolyamat

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a BitBucket **SAML-bejelentkezés** gomb segítségével tesztelheti.

![Képernyőkép a bejelentkezés képernyőről, az SAML-bejelentkezés kiemelve](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-7.png)

Ez a forgatókönyv feltételezi, hogy engedélyezte az **SAML-bejelentkezést** a BitBucket EasySSO konfigurációs lapjának **Look & Feel (megjelenés** ) lapján. A meglévő munkamenetek zavarásának elkerülése érdekében nyissa meg a BitBucket bejelentkezési URL-címét a böngésző inkognitóban módjában. Válassza az **SAML-bejelentkezés**lehetőséget, és a rendszer átirányítja az Azure ad felhasználói hitelesítési folyamatára. A sikeres befejezést követően a rendszer átirányítja a BitBucket-példányra az SAML-n keresztül hitelesített felhasználóként.

Az Azure AD-ből való átirányítást követően a következő képernyőt tapasztalhatja:

![Képernyőkép a EasySSO-meghibásodási képernyőről, Kiemelt hivatkozási számmal](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-8.png)

Ha ezt teszi, kövesse az [ezen az oldalon található utasításokat](https://techtime.co.nz/display/TECHTIME/EasySSO+How+to+get+the+logs#EasySSOHowtogetthelogs-RETRIEVINGTHELOGS) a **Atlassian-bitbucket. log** fájl eléréséhez. A hiba részleteit a EasySSO-hiba oldalon található hivatkozási azonosító fogja tartalmazni.

Ha bármilyen problémája van, lépjen kapcsolatba a [EasySSO támogatási csapatával](mailto:support@techtime.co.nz).

## <a name="additional-resources"></a>További források

- [Oktatóanyagok SaaS-alkalmazások az Azure Active Directoryval való integrálásához](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [A BitBucket EasySSO kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [A speciális láthatóságot és vezérlést biztosító BitBucket EasySSO elleni védelem](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

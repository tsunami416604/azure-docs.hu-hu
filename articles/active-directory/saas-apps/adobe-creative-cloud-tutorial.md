---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Adobe Creative Cloudtal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Adobe Creative Cloud között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/14/2020
ms.author: jeedes
ms.openlocfilehash: 48009246dd491ca3f4080cf85036c66605a73272
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/16/2020
ms.locfileid: "90707004"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adobe-creative-cloud"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció Adobe Creative Cloud

> [!NOTE]
> Ez a cikk az Adobe felügyeleti konzol egyéni SAML-alapú telepítését ismerteti Azure Active Directory (Azure AD). A vadonatúj konfigurációk esetében javasoljuk, hogy használja az [Azure ad-összekötőt](https://helpx.adobe.com/enterprise/using/sso-setup-azure.html). Az Azure AD Connector percek alatt beállítható, és lerövidíti a tartományi jogcímek, az egyszeri bejelentkezések és a felhasználói szinkronizálás folyamatát.

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Adobe Creative Cloudt Azure Active Directory (Azure AD) használatával. A Adobe Creative Cloud az Azure AD-vel való integrálásakor a következőket teheti:

* A Adobe Creative Cloudhoz hozzáféréssel rendelkező Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek az Azure AD-fiókjával Adobe Creative Cloudba.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Adobe Creative Cloud egyszeri bejelentkezést (SSO) engedélyező előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* Adobe Creative Cloud támogatja az **SP** által KEZDEMÉNYEZett SSO-t
* A Adobe Creative Cloud konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben biztosítja a szervezet bizalmas adatainak kiszűrése és beszivárgását. A munkamenet-vezérlő kiterjeszthető a feltételes hozzáférésből. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-adobe-creative-cloud-from-the-gallery"></a>Adobe Creative Cloud hozzáadása a gyűjteményből

Adobe Creative Cloud az Azure AD-be való integrálásának konfigurálásához hozzá kell adnia Adobe Creative Cloud a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Adobe Creative Cloud** kifejezést a keresőmezőbe.
1. Válassza ki **Adobe Creative Cloud** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-sso-for-adobe-creative-cloud"></a>Azure AD SSO konfigurálása és tesztelése Adobe Creative Cloud

Az Azure AD SSO konfigurálása és tesztelése Adobe Creative Cloud egy **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között Adobe Creative Cloudban.

Az Azure AD SSO és a Adobe Creative Cloud közötti konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[Adobe Creative Cloud SSO konfigurálása](#configure-adobe-creative-cloud-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre Adobe Creative Cloud tesztelési felhasználót](#create-adobe-creative-cloud-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-beli partnert Adobe Creative Cloud.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **Adobe Creative Cloud** alkalmazás-integráció lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet: `https://adobe.com`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://www.okta.com/saml2/service-provider/<token>`

    > [!NOTE]
    > Az azonosító értéke nem valódi. Kövesse az **Adobe Cloud SSO konfigurálása** szakasz 4. lépésének útmutatását. Az alkalmazásban megnyithatja az **összevonási metaadatok XML-fájlját** , és lekérheti az entitás-azonosító értékét, és az Azure ad-konfigurációban azonosító értékként helyezheti el. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Adobe Creative Cloud alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/edit-attribute.png)

1. A fentiek mellett Adobe Creative Cloud alkalmazás néhány további attribútumot vár az SAML-válaszban, amelyek alább láthatók. Ezek az attribútumok előre is fel vannak töltve, de a követelménynek megfelelően áttekintheti őket.

    | Name | Forrás attribútum|
    |----- | --------- |
    | FirstName | User. givenName |
    | LastName | felhasználó. vezetéknév |
    | E-mail | User. mail |

    > [!NOTE]
    > A felhasználóknak érvényes Microsoft 365 ExO-licenccel kell rendelkezniük az e-mail-jogcím értékének az SAML-válaszban való feltöltéséhez.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási adatok XML-** fájlját, majd válassza a **Letöltés** lehetőséget, hogy letöltse az XML-metaadatokat, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Adobe Creative Cloud beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát, ha hozzáférést biztosít a Adobe Creative Cloudhoz.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **Adobe Creative Cloud**lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-adobe-creative-cloud-sso"></a>Adobe Creative Cloud SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be az [Adobe felügyeleti konzolra](https://adminconsole.adobe.com) rendszergazdaként.

1. A felső navigációs sávon kattintson a **Beállítások** elemre, majd válassza az **Identity (identitás**) lehetőséget. Megnyílik a címtárak listája. Válassza ki a kívánt összevont könyvtárat.

1. A **címtár részletei** lapon válassza a **Konfigurálás**lehetőséget.

1. Másolja az entitás-azonosítót és az ACS URL-címet (a fogyasztói szolgáltatás URL-címére vagy a válasz URL-címére). Adja meg az URL-címeket a Azure Portal megfelelő mezőiben.

    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalán](./media/adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. Használja az Adobe-beli entitás-azonosító értéket, amelyet az **alkalmazás beállításainak konfigurálása** párbeszédpanelen az **azonosítóhoz** megadott.

    b. A **Válasz URL** -címének **megadása az Alkalmazásbeállítások konfigurálása** párbeszédpanelen az ACS URL-cím (a fogyasztói szolgáltatás URL-címe) értékének az Adobe által megadott értéke.

1. Az oldal alján töltse fel a Azure Portalból letöltött **összevonási adatok XML-** fájlját. 

    ![Összevonási adatxml-fájl](https://helpx.adobe.com/content/dam/help/en/enterprise/kb/configure-microsoft-azure-with-adobe-sso/jcr_content/main-pars/procedure/proc_par/step_228106403/step_par/image_copy/saml_signinig_certificate.png "Identitásszolgáltató-metaadatok XML-fájlja")

1. Kattintson a **Mentés** gombra.


### <a name="create-adobe-creative-cloud-test-user"></a>Adobe Creative Cloud tesztelési felhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezzenek a Adobe Creative Cloudba, Adobe Creative Cloud kell kiépíteni őket. Adobe Creative Cloud esetén a kiépítés manuális feladat.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Felhasználói fiókok kiépítéséhez hajtsa végre a következő lépéseket:

1. Jelentkezzen be rendszergazdaként az [Adobe felügyeleti konzol](https://adminconsole.adobe.com) webhelyére.

2. Vegye fel a felhasználót az Adobe konzolján összevont AZONOSÍTÓként, és rendelje hozzá egy termék profiljához. A felhasználók hozzáadásával kapcsolatos részletes információkért lásd: [felhasználók hozzáadása az Adobe felügyeleti konzolon](https://helpx.adobe.com/enterprise/using/users.html#Addusers) . 

3. Ezen a ponton írja be az e-mail-címet/UPN-t az Adobe bejelentkezési űrlapba, nyomja le a TAB billentyűt, és az Azure AD-be való összevonáshoz:
   * Webes elérés: www \. adobe.com > bejelentkezés
   * Az asztali alkalmazás segédprogram > bejelentkezés
   * Az alkalmazáson belül > Súgó > bejelentkezéshez

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Adobe Creative Cloud csempére kattint, automatikusan be kell jelentkeznie arra a Adobe Creative Cloudra, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Adobe Creative Cloud kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

- [Identitás beállítása (adobe.com)](https://helpx.adobe.com/enterprise/using/set-up-identity.html)
  
- [Az Azure konfigurálása az Adobe SSO (adobe.com) szolgáltatással való használatra](https://helpx.adobe.com/enterprise/kb/configure-microsoft-azure-with-adobe-sso.html)

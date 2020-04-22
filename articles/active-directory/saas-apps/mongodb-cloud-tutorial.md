---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a MongoDB-felhővel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a MongoDB Cloud között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ef392044-235b-4d80-8a33-eeba9b142849
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/03/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02cc78f7e786e97062cce6d402c972e66fa56860
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81688114"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mongodb-cloud"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a MongoDB-felhővel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a MongoDB-felhőt az Azure Active Directoryval (Azure AD). Ha integrálja a MongoDB-felhőt az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-t, aki hozzáfér a MongoDB Cloud, a MongoDB Atlas, a MongoDB közösség, a MongoDB Egyetem és a MongoDB-támogatáshoz.
* Lehetővé teszi, hogy a felhasználók automatikusan bejelentkeznek a MongoDB Cloud-ba az Azure AD-fiókjukkal.
* Kezelje fiókjait egyetlen központi helyen: az Azure Portalon.

Ha többet szeretne megtudni a szoftverszolgáltatásként (SaaS) alkalmazásintegrációról az Azure AD-vel, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következők szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Egy MongoDB Cloud-előfizetés, amely engedélyezve van az egyszeri bejelentkezéshez (SSO).

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* A MongoDB Cloud támogatja az **SP** és **az IDP** által kezdeményezett sso-t.
* A MongoDB Cloud támogatja **a Just In Time** felhasználói kiépítést.
* A MongoDB-felhő konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlő a feltételes hozzáféréstől nyújt ki. További információt a [Szakadát ismerésből megtudhatja, hogy miként kényszerítheti a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)

## <a name="add-mongodb-cloud-from-the-gallery"></a>MongoDB Cloud hozzáadása a galériából

A MongoDB Cloud Azure AD-be való integrációjának konfigurálásához hozzá kell adnia a MongoDB-felhőt a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget.
1. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a Minden **alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből szakaszban** írja be a **MongoDB Cloud** kifejezést a keresőmezőbe.
1. Válassza ki a **MongoDB Cloud** elemet az eredmények közül, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.


## <a name="configure-and-test-azure-ad-single-sign-on-for-mongodb-cloud"></a>Konfigurálja és tesztelje az Azure AD egyszeri bejelentkezését a MongoDB Cloud szolgáltatáshoz

Konfigurálja és tesztelje az Azure AD SSO-t a MongoDB Cloud szolgáltatással egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy összekapcsolt kapcsolatot egy Azure AD-felhasználó és a mongoDB-felhőben a kapcsolódó felhasználó között.

Az Azure AD SSO mongoDB-felhővel való konfigurálásához és teszteléséhez hajtsa végre az alábbi építőelemeket:

1. [Konfigurálja az Azure AD SSO-t,](#configure-azure-ad-sso) hogy a felhasználók használhassák ezt a funkciót.
    1. [Hozzon létre egy Azure AD-tesztfelhasználót](#create-an-azure-ad-test-user) az Azure AD egyszeri bejelentkezésének teszteléséhez B.Simon nal.
    1. [Rendelje hozzá az Azure AD tesztfelhasználót,](#assign-the-azure-ad-test-user) hogy b.Simon az Azure AD egyszeri bejelentkezést használhasson.
1. [Konfigurálja a MongoDB Cloud Egyszeri](#configure-mongodb-cloud-sso) bejelentkezéskonfigurálási konfigurálása az alkalmazás oldalon az egyszeri bejelentkezési beállítások konfigurálásához.
    1. [Hozzon létre egy MongoDB-tesztfelhasználót,](#create-a-mongodb-cloud-test-user) hogy b.Simon megfelelője legyen a MongoDB-felhőben, amely a felhasználó Azure AD-ábrázolásához kapcsolódik.
1. [Tesztelje az SSO-t,](#test-sso) hogy ellenőrizze, működik-e a konfiguráció.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **MongoDB Felhőalkalmazás-integrációlapon** keresse meg a **Kezelés szakaszt.** Válassza **ki az egyszeri bejelentkezést**.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon válassza az **egyszerű SAML-konfiguráció** ceruza ikonját a beállítások szerkesztéséhez.

   ![Az Egyszeri bejelentkezés beállítása SAML-lel lap képernyőképe, kiemelve a ceruza ikon](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban, ha az alkalmazást **IDP** által kezdeményezett módban szeretné konfigurálni, adja meg a következő mezők értékeit:

    a. Az **Azonosító** mezőbe írjon be egy URL-címet, amely a következő mintát használja:`https://www.okta.com/saml2/service-provider/<Customer_Unique>`

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet, amely a következő mintát használja:`https://auth.mongodb.com/sso/saml2/<Customer_Unique>`

1. Válassza **a További URL-ek beállítása**lehetőséget, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban szeretné konfigurálni:

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet, amely a következő mintát használja:`https://cloud.mongodb.com/sso/<Customer_Unique>`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Ezeknek az értékeknek a beszerezéséhez lépjen kapcsolatba a [MongoDB Cloud Client támogatási csapatával.](https://support.mongodb.com/) Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. A MongoDB Cloud alkalmazás elvárja, hogy az SAML-állítások egy adott formátumban legyenek, amely megköveteli, hogy egyéni attribútumleképezéseket adjon hozzá az SAML token attribútumok konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![Alapértelmezett attribútumok képernyőképe](common/default-attributes.png)

1. Az előző attribútumok mellett a MongoDB Cloud alkalmazás elvárja, hogy még néhány attribútumot vissza kell adni az SAML válaszban. Ezek az attribútumok is előre kivannak töltve, de áttekintheti őket a követelmények szerint.
    
    | Name (Név) | |  Forrás attribútum|
    | ---------------| --------------- | --------- |
    | e-mail | | user.userprincipalname |
    | firstName | | user.givenname |
    | lastName | | user.vezetéknév |

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány szakaszban** keresse meg az **összevonási metaadat-XML című területet.** A **Letöltés gombra** a tanúsítvány letöltéséhez és a számítógépre való mentéséhez válassza a Letöltés lehetőséget.

    ![Képernyőkép az SAML aláíró tanúsítvány szakaszról, kiemelve a Letöltés hivatkozással](common/metadataxml.png)

1. A **MongoDB-felhő beállítása** szakaszban másolja a megfelelő URL-címeket a követelmény alapján.

    ![Képernyőkép: A Mongo DB Felhő beállítása szakasz, kiemelt URL-címekkel](common/copy-configuration-urls.png)
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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t a MongoDB Cloud hozzáférést biztosít.

1. Az Azure Portalon válassza a **Vállalati alkalmazások** > **minden alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **MongoDB Cloud**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![Képernyőkép a Kezelés szakaszról, kiemelve a Felhasználók és csoportok](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget. Ezután a **Hozzárendelés hozzáadása** párbeszédpanelen válassza a Felhasználók és **csoportok**lehetőséget.

    ![Képernyőkép: Felhasználók és csoportok lap, kiemelt Felhasználó hozzáadása](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a Felhasználók listájából a **B.Simon** elemet. Ezután válassza a **Kijelölés** lehetőséget a képernyő alján.
1. Ha az SAML-feltételben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából. Ezután válassza a **Kijelölés** lehetőséget a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen válassza a **Hozzárendelés lehetőséget.**

## <a name="configure-mongodb-cloud-sso"></a>A MongoDB felhőalapú sso konfigurálása

Az egyszeri bejelentkezés konfigurálásához a MongoDB felhőoldalán, az Azure Portalról másolt megfelelő URL-címekre van szükség. A MongoDB Cloud Organization összevonási alkalmazást is konfigurálnia kell. Kövesse a [MongoDB Cloud dokumentációjában](https://docs.atlas.mongodb.com/security/federated-authentication/index.html)található utasításokat. Ha problémája van, forduljon a [MongoDB Cloud támogatási csapatához.](https://support.mongodb.com/)

### <a name="create-a-mongodb-cloud-test-user"></a>MongoDB Cloud tesztfelhasználó létrehozása

A MongoDB Cloud támogatja a just-in-time felhasználói kiépítést, amely alapértelmezés szerint engedélyezve van. Nincs további teendőd. Ha a felhasználó még nem létezik a MongoDB-felhőben, a hitelesítés után egy új jön létre.

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a Hozzáférési panel használatával teszteli.

Amikor kiválasztja a MongoDB Felhő csempét a Hozzáférési panelen, automatikusan bejelentkezik a MongoDB-felhőbe, amelyhez beállítja az SSO-t. További információt a [Bejelentkezés és alkalmazások indítása a Saját alkalmazások portálról című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Oktatóanyagok SaaS-alkalmazások az Azure Active Directoryval való integrálásához](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Regisztráció a MongoDB Atlas szolgáltatásra az Azure-ban](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/mongodb.mongodb_atlas_azure_08082019?tab=Overview)

- [Próbálja ki a MongoDB Cloud szolgáltatást az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlés a Microsoft Cloud App Security alkalmazásban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [A MongoDB Cloud védelme fejlett láthatósággal és vezérléssel](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)


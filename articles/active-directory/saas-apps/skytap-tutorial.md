---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Skytap egyszeri bejelentkezésével | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Skytap egyszeri bejelentkezése között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d6cb7ab2-da1a-4015-8e6f-c0c47bb6210f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/13/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 33a8035f16f531dbb17177d1c2f4d5cd344e5a28
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77565774"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-single-sign-on-for-skytap"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Skytap egyszeri bejelentkezésével

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Skytap egyszeri bejelentkezését az Azure Active Directoryval (Azure AD). Ha integrálja az Egyszeri bejelentkezést a Skytaphoz az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Skytap egyszeri bejelentkezéséhez.
* Lehetővé teszi, hogy a felhasználók automatikusan bejelentkeznek az Egyszeri bejelentkezés be a Skytap az Azure AD-fiókok.
* Egyetlen központi helyen, az Azure Portalon kezelheti fiókjait.

Ha többet szeretne megtudni a szoftverszolgáltatásként (SaaS) alkalmazásintegrációról az Azure AD-vel, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Egyszeri bejelentkezés a Skytap egyszeri bejelentkezéssel (SSO) engedélyezett előfizetéséhez.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* A Skytap egyszeri bejelentkezése támogatja az SP-t, az IDP pedig kezdeményezte az egyszeri bejelentkezést.
* Miután konfigurálta az egyszeri bejelentkezést a Skytaphoz, kényszerítheti a munkamenet-vezérlést. Ez valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlő a feltételes hozzáféréstől nyújt ki. [Megtudhatja, hogy miként kényszerítheti ki a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)

## <a name="add-single-sign-on-for-skytap-from-the-gallery"></a>Egyszeri bejelentkezés hozzáadása a Skytaphoz a galériából

A Skytap egyszeri bejelentkezésének konfigurálásához hozzá kell adnia a Skytap egyszeri bejelentkezését a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások lehetőséget,** és válassza **az Összes alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **katalógus hozzáadásszakaszában** írja be **a Skytap egyszeri bejelentkezését** a keresőmezőbe.
1. Válassza az Egységes bejelentkezés lehetőséget a **Skytap hoz** az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-single-sign-on-for-skytap"></a>Konfigurálja és tesztelje az Azure AD egyszeri bejelentkezését a Skytap egyszeri bejelentkezéséhez

Konfigurálja és tesztelje az Azure AD egyszeri bejelentkezést a Skytap hoz egy **B.Simon**nevű tesztfelhasználó használatával. Az egyszeri bejelentkezés működéséhez hozzon létre egy összekapcsolt kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Skytap egyszeri bejelentkezésében.

Az alábbiakban az Azure AD Egyszeri bejelentkezés egyszeri bejelentkezéssel való konfigurálásának és tesztelésének általános lépéseit olvashatja:

1. **[Konfigurálja az Azure AD SSO-t,](#configure-azure-ad-sso)** hogy a felhasználók használhassák ezt a funkciót.

    a. **[Hozzon létre egy Azure AD-tesztfelhasználót](#create-an-azure-ad-test-user)** az Azure AD egyszeri bejelentkezésének teszteléséhez B.Simon nal.

    b. **[Rendelje hozzá az Azure AD tesztfelhasználót,](#assign-the-azure-ad-test-user)** hogy b.Simon az Azure AD egyszeri bejelentkezést használhasson.
1. **[Konfigurálja az egyszeri bejelentkezést a Skytap Egyszeri bejelentkezéshez](#configure-single-sign-on-for-skytap-sso)** az alkalmazás oldalon az egyszeri bejelentkezési beállítások konfigurálásához.

    a. **[Hozzon létre egy egyszeri bejelentkezést a Skytap tesztfelhasználója számára,](#create-single-sign-on-for-skytap-test-user)** hogy b.Simon megfelelője legyen a Skytap egyszeri bejelentkezésében. Ez a megfelelő kapcsolódik a felhasználó Azure AD-ábrázolása.
1. **[Tesztelje az SSO-t,](#test-sso)** hogy ellenőrizze, működik-e a konfiguráció.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)az Egyszeri bejelentkezés a **Skytap alkalmazásintegrációs** lapon keresse meg a **Kezelés** szakaszt. Válassza **ki az egyszeri bejelentkezést**.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon válassza az **egyszerű SAML-konfiguráció** ceruza ikonját a beállítások szerkesztéséhez.

   ![Képernyőkép: Egyszeri bejelentkezés beállítása SAML-oldallal, kiemelve a ceruza ikonnal](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban, ha az alkalmazást **IDP** által kezdeményezett módban szeretné konfigurálni, adja meg a következő mezők értékeit:

    a. Az **Azonosító** mezőbe írjon be egy URL-címet, amely a következő mintát használja:`http://pingone.com/<custom EntityID>`

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet, amely a következő mintát használja:`https://sso.connect.pingidentity.com/sso/sp/ACS.saml2`

1. Válassza **a További URL-ek beállítása**lehetőséget, és hajtsa végre a következő lépéseket, ha az alkalmazást **SP** által kezdeményezett módban szeretné konfigurálni:

    a. A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet, amely a következő mintát használja:`https://sso.connect.pingidentity.com/sso/sp/initsso?saasid=<saasid>&idpid=<idpid>`

    
    b. A **Továbbítási állapot** mezőbe írjon be egy URL-címet, amely a következő mintát használja:`https://pingone.com/1.0/<custom ID>`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével, a bejelentkezési URL-címmel és a továbbítási állapottal. Lépjen kapcsolatba a [Skytap ügyféltámogatási csapatának egyszeri bejelentkezési kapcsolatával,](mailto:support@skytap.com) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány szakaszban** keresse meg az **összevonási metaadat-XML című területet.** A **Letöltés** gombra a metaadatfájl letöltéséhez és a számítógépre való mentéséhez válassza a Letöltés lehetőséget.

    ![Képernyőkép a tanúsítvány letöltési hivatkozásáról](common/metadataxml.png)

1. Az **Egyszeri bejelentkezés beállítása a Skytaphoz** szakaszban másolja a megfelelő URL-t vagy URL-eket a követelmény alapján.

    ![Képernyőkép a másoláskonfiguráció URL-címeiről](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban hozzon létre egy tesztfelhasználót az Azure Portalon B.Simon nevű.

1. Az Azure Portal bal oldali ablaktáblájában válassza az **Azure Active Directory** > **felhasználói** > **minden felhasználó lehetőséget.**
1. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.
1. A **Felhasználói** tulajdonságok csoportban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőbe írja username@companydomain.extensionbe a mezőt. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet, majd írja le a **Jelszó** mezőben látható értéket.
   1. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t azáltal, hogy hozzáférést biztosít a Skytap egyszeri bejelentkezéshez.

1. Az Azure Portalon válassza a **Vállalati alkalmazások** > **minden alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza az Egyszeri bejelentkezés lehetőséget **a Skytap hoz.**
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![Képernyőkép a Kezelés szakaszról, kiemelve a Felhasználók és csoportok](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget. A **Hozzárendelés hozzáadása** párbeszédpanelen válassza a **Felhasználók és csoportok**lehetőséget.

    ![Képernyőkép: Felhasználók és csoportok lap, kiemelt Felhasználó hozzáadása](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a felhasználók listájából. Ezután válassza a **Kijelölés** gombot a képernyő alján.
1. Ha az SAML-feltételben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából. Ezután válassza a **Kijelölés** gombot a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen válassza a **Hozzárendelés lehetőséget.**

## <a name="configure-single-sign-on-for-skytap-sso"></a>Egyszeri bejelentkezés konfigurálása a Skytap Egyszeri bejelentkezéshez

A Skytap-oldalon az egyszeri bejelentkezés konfigurálásához el kell küldenie a letöltött **összevonási metaadat-XML-t**és a megfelelő másolt URL-címeket az Azure Portalról a [Skytap-ügyfél támogatási csapatának egyszeri bejelentkezési oldalára.](mailto:support@skytap.com) Úgy konfigurálják ezt a beállítást, hogy az SAML SSO-kapcsolat mindkét oldalon megfelelően legyen beállítva.


### <a name="create-single-sign-on-for-skytap-test-user"></a>Egyszeri bejelentkezés létrehozása a Skytap tesztfelhasználója számára

Ebben a szakaszban hozzon létre egy B.Simon nevű felhasználót a Skytap egyszeri bejelentkezésében. Együttműködve a [Skytap-ügyfél támogatási csapatának egyszeri bejelentkezési szolgáltatásával,](mailto:support@skytap.com) hogy hozzáadja a felhasználókat a Skytap platformegyszeri bejelentkezési webhelyén. Az egyszeri bejelentkezés nem használható, amíg létre nem hozza és nem aktiválja a felhasználókat.

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a Hozzáférési panel használatával teszteli.

Ha a Hozzáférési panelen kiválasztja a Skytap csempéjének egyszeri bejelentkezését, automatikusan be kell jelentkeznie a Skytap egyszeri bejelentkezési bejelentkezésbe, amelyhez beállítja az Egyszeri bejelentkezést. További információt a [Hozzáférési panel – bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Oktatóanyagok SaaS-alkalmazások az Azure Active Directoryval való integrálásához](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a Slack szolgáltatást az Azure AD-vel](https://aad.portal.azure.com/)


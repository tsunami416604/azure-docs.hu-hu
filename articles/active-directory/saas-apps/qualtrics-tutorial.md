---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az SAP Qualtrics- szel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az SAP Qualtrics között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4df889ab-2685-4d15-a163-1ba26567eeda
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/03/2020
ms.author: jeedes
ms.openlocfilehash: 2e27d020bd25f234d084fba770e234bdccb40a99
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682377"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-qualtrics"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az SAP Qualtrics-szel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az SAP Qualtrics-t az Azure Active Directoryval (Azure AD). Ha integrálja az SAP Qualtrics-t az Azure AD-vel, a következőket teheti:

* Az Azure AD- ban, aki hozzáfér az SAP Qualtrics.
* Lehetővé teszi, hogy a felhasználók automatikusan bejelentkeznek az SAP Qualtrics az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen: az Azure Portalon.

Ha többet szeretne megtudni a szoftverszolgáltatásként (SaaS) alkalmazásintegrációról az Azure AD-vel, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következők szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Az SAP Qualtrics-előfizetés engedélyezve van az egyszeri bejelentkezéshez (SSO).

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* Az SAP Qualtrics támogatja az **SP** és **az IDP** által kezdeményezett sso-t.
* Az SAP Qualtrics támogatja **a Just In Time** felhasználói kiépítést.
* Az SAP Qualtrics konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlő a feltételes hozzáféréstől nyújt ki. További információt a [Szakadát ismerésből megtudhatja, hogy miként kényszerítheti a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)

## <a name="add-sap-qualtrics-from-the-gallery"></a>SAP Qualtrics hozzáadása a galériából

Az SAP Qualtrics azure AD-be való integrálásának konfigurálásához hozzá kell adnia az SAP Qualtrics-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget.
1. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a Minden **alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből szakaszban** írja be az **SAP Qualtrics** kifejezést a keresőmezőbe.
1. Válassza ki **az SAP Qualtrics** az eredmények közül, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-qualtrics"></a>Az Azure AD egyszeri bejelentkezésének konfigurálása és tesztelése az SAP Qualtrics-hez

Konfigurálja és tesztelje az Azure AD SSO-t az SAP Qualtrics-szel egy **B.Simon**nevű tesztfelhasználó használatával. Az SSO működéséhez létre kell hoznia egy összekapcsolt kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó az SAP Qualtrics-ben.

Az Azure AD SSO SAP Qualtrics-szel való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. [Konfigurálja az Azure AD SSO-t,](#configure-azure-ad-sso) hogy a felhasználók használhassák ezt a funkciót.
    1. [Hozzon létre egy Azure AD-tesztfelhasználót](#create-an-azure-ad-test-user) az Azure AD egyszeri bejelentkezésének teszteléséhez B.Simon nal.
    1. [Rendelje hozzá az Azure AD tesztfelhasználót,](#assign-the-azure-ad-test-user) hogy b.Simon az Azure AD egyszeri bejelentkezést használhasson.
1. [Konfigurálja az SAP Qualtrics egyszeri](#configure-sap-qualtrics-sso) bejelentkezési beállításait az alkalmazás oldalon.
    1. [Hozzon létre egy SAP Qualtrics teszt felhasználó,](#create-sap-qualtrics-test-user) hogy egy megfelelője B.Simon az SAP Qualtrics, a felhasználó Azure AD-reprezentációjához kapcsolva.
1. [Tesztelje az SSO-t,](#test-sso) hogy ellenőrizze, működik-e a konfiguráció.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)az **SAP Qualtrics alkalmazásintegrációs** lapon keresse meg a **Kezelés** szakaszt. Válassza **ki az egyszeri bejelentkezést**.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon válassza az **egyszerű SAML-konfiguráció** ceruza ikonját a beállítások szerkesztéséhez.

   ![Az Egyszeri bejelentkezés beállítása SAML-lel lap képernyőképe, kiemelve a ceruza ikon](common/edit-urls.png)

1. Ha az alkalmazást **IDP** által kezdeményezett módban szeretné konfigurálni, adja **meg** az alábbi mezők értékeit:
    
    a. Az **Azonosító** mezőbe írjon be egy URL-címet, amely a következő mintát használja:

    `https://< DATACENTER >.qualtrics.com`
   
    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet, amely a következő mintát használja:

    `https://< DATACENTER >.qualtrics.com/login/v1/sso/saml2/default-sp`

    c. A **Továbbítási állapot** mezőbe írjon be egy URL-címet, amely a következő mintát használja:

    `https://< brandID >.< DATACENTER >.qualtrics.com`

1. Válassza **a További URL-ek beállítása**lehetőséget, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban szeretné konfigurálni:

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet, amely a következő mintát használja:

    `https://< brandID >.< DATACENTER >.qualtrics.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel, azonosítóval, válasz URL-címmel és továbbítási állapottal. Ezeknek az értékeknek a beszerezéséhez forduljon a [Qualtrics ügyféltámogatási csapatához.](https://www.qualtrics.com/support/) Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány csoportban** jelölje ki a másolásikont az **Alkalmazásösszevonás metaadat-címének** másolásához és mentéséhez a számítógépre.

    ![Képernyőkép az SAML aláíró tanúsítványról, kiemelve a másolás ikonnal](common/copy-metadataurl.png)

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés az SAP Qualtrics használatával.

1. Az Azure Portalon válassza a **Vállalati alkalmazások** > **minden alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza az **SAP Qualtrics**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![Képernyőkép a Kezelés szakaszról, kiemelve a Felhasználók és csoportok](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget. Ezután a **Hozzárendelés hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok**lehetőséget.

    ![Képernyőkép: Felhasználók és csoportok lap, kiemelt Felhasználó hozzáadása](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a Felhasználók listájából a **B.Simon** elemet. Ezután válassza a **Kijelölés** lehetőséget a képernyő alján.
1. Ha az SAML-feltételben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából. Ezután válassza a **Kijelölés** lehetőséget a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen válassza a **Hozzárendelés lehetőséget.**

## <a name="configure-sap-qualtrics-sso"></a>SAP Qualtrics sso konfigurálása

Az SAP Qualtrics oldalán az egyszeri bejelentkezés konfigurálásához küldje el a másolt **App Federation metaadat-URL-címét** az Azure Portalról az [SAP Qualtrics támogatási csapatának.](https://www.qualtrics.com/support/) A támogatási csapat biztosítja, hogy az SAML SSO-kapcsolat mindkét oldalon megfelelően legyen beállítva.

### <a name="create-sap-qualtrics-test-user"></a>SAP Qualtrics tesztfelhasználó létrehozása

Az SAP Qualtrics támogatja a just-in-time felhasználói kiépítést, amely alapértelmezés szerint engedélyezve van. Nincs további teendőd. Ha a felhasználó már nem létezik az SAP Qualtrics, egy új jön létre a hitelesítés után.

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a Hozzáférési panel használatával teszteli.

Amikor kiválasztja az SAP Qualtrics csempét a Hozzáférési panelen, automatikusan bejelentkezik az SAP Qualtrics-be, amelyhez beállítja az SSO-t. További információt a [Bejelentkezés és alkalmazások indítása a Saját alkalmazások portálról című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Oktatóanyagok SaaS-alkalmazások az Azure Active Directoryval való integrálásához](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki az SAP Qualtrics-t az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlés a Microsoft Cloud App Security alkalmazásban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Védje az SAP Qualtrics-t a fejlett láthatósággal és vezérléssel](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)


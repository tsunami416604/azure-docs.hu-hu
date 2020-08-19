---
title: 'Oktatóanyag: Azure Active Directory integráció az SAP Qualtrics | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és az SAP-Qualtrics között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/03/2020
ms.author: jeedes
ms.openlocfilehash: 170997099f1194bbc75d6d61a7c29fc1d18b462a
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88552216"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-qualtrics"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az SAP Qualtrics

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az SAP-Qualtrics Azure Active Directory (Azure AD) használatával. Az SAP-Qualtrics az Azure AD-vel való integrálásakor a következőket teheti:

* Az SAP-Qualtrics hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek az SAP Qualtrics az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti: a Azure Portal.

Ha többet szeretne megtudni a szolgáltatott szoftver (SaaS) alkalmazás Azure AD-integrációval kapcsolatban, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)használatával című témakört.

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következők szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Az egyszeri bejelentkezéshez (SSO) engedélyezett SAP Qualtrics-előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* Az SAP-Qualtrics az **SP** és a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést támogatja.
* **Az SAP-Qualtrics csak időben támogatja a** felhasználók üzembe helyezését.
* Az SAP-Qualtrics konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. További információ: a [munkamenet-vezérlés kényszerített érvényesítése Microsoft Cloud app Security használatával](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-sap-qualtrics-from-the-gallery"></a>SAP-Qualtrics hozzáadása a katalógusból

Az SAP-Qualtrics az Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az SAP-Qualtrics a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali ablaktáblán válassza a **Azure Active Directory**lehetőséget.
1. Lépjen a **vállalati alkalmazások**elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be az **SAP Qualtrics** kifejezést a keresőmezőbe.
1. Válassza az **SAP-Qualtrics** az eredmények közül, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-qualtrics"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése az SAP Qualtrics

Konfigurálja és tesztelje az Azure AD SSO-t az SAP Qualtrics egy **B. Simon**nevű tesztelési felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy összekapcsolt kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között az SAP Qualtrics.

Az Azure AD SSO SAP Qualtrics való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. [Konfigurálja az Azure ad SSO](#configure-azure-ad-sso) -t, hogy a felhasználók használhatják ezt a funkciót.
    1. [Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user) az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. [Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user) , hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. [Konfigurálja az SAP QUALTRICS SSO](#configure-sap-qualtrics-sso) -t az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalán.
    1. [Hozzon létre egy SAP Qualtrics-teszt felhasználót](#create-sap-qualtrics-test-user) , hogy az a felhasználó Azure ad-beli képviseletéhez kapcsolódó B. Simon-ügyféllel RENDELKEZZEN az SAP-Qualtrics.
1. Ellenőrizze az [SSO](#test-sso) -t annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **SAP Qualtrics** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt. Válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon válassza az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikont a beállítások szerkesztéséhez.

   ![Képernyőfelvétel: egyszeri bejelentkezés beállítása SAML-oldallal – kiemelve a ceruza ikonnal](common/edit-urls.png)

1. Ha az **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, akkor adja meg a következő mezők értékeit az **egyszeri bejelentkezés az SAML-vel** lapon:
    
    a. Az **azonosító** szövegmezőbe írja be a következő mintát használó URL-címet:

    `https://< DATACENTER >.qualtrics.com`
   
    b. A **Válasz URL-cím** szövegmezőbe írja be a következő mintát használó URL-címet:

    `https://< DATACENTER >.qualtrics.com/login/v1/sso/saml2/default-sp`

    c. A **továbbítási állapot** szövegmezőbe írja be a következő mintát használó URL-címet:

    `https://< brandID >.< DATACENTER >.qualtrics.com`

1. Válassza a **további URL-címek beállítása**lehetőséget, majd hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL** szövegmezőbe írja be a következő mintát használó URL-címet:

    `https://< brandID >.< DATACENTER >.qualtrics.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel, azonosítóval, válasz URL-címmel és továbbítási állapottal. Ezeknek az értékeknek a beszerzéséhez forduljon a [Qualtrics ügyfél-támogatási csapatához](https://www.qualtrics.com/support/). Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban válassza a másolás ikont az **alkalmazás-összevonási metaadatok URL-címének** másolásához és a számítógépre mentéséhez.

    ![Képernyőfelvétel az SAML-aláíró tanúsítványról, a másolás ikon kiemelve](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**  >  **felhasználók**  >  **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszót.
   1. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban engedélyezi, hogy a B. Simon az Azure egyszeri bejelentkezést használja az SAP-Qualtrics való hozzáférés biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**  >  **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza az **SAP Qualtrics**elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![Képernyőfelvétel a kezelés szakaszról, Kiemelt felhasználók és csoportok](common/users-groups-blade.png)

1. Válassza a **Felhasználó hozzáadása** elemet. Ezután a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok**lehetőséget.

    ![Képernyőkép a felhasználók és csoportok oldalról, a felhasználó hozzáadása elem kiemelésével](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza ki a felhasználók listájából a **B. Simon** elemet. Ezután válassza a **kijelölés** lehetőséget a képernyő alján.
1. Ha az SAML-kijelentésben bármelyik szerepkör értékét várta, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a listáról a felhasználó számára. Ezután válassza a **kijelölés** lehetőséget a képernyő alján.
1. A **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelés**lehetőséget.

## <a name="configure-sap-qualtrics-sso"></a>Az SAP Qualtrics SSO konfigurálása

Az egyszeri bejelentkezés az SAP Qualtrics oldalon való konfigurálásához küldje el az alkalmazás- **összevonási metaadatokat** a Azure Portal az [SAP Qualtrics támogatási csapatának](https://www.qualtrics.com/support/). A támogatási csapat biztosítja, hogy az SAML SSO-kapcsolatok mindkét oldalon megfelelően legyenek beállítva.

### <a name="create-sap-qualtrics-test-user"></a>SAP Qualtrics-teszt felhasználó létrehozása

Az SAP Qualtrics támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Nincs szükség további műveletre. Ha egy felhasználó még nem létezik az SAP-Qualtrics, a hitelesítés után létrejön egy újat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panel használatával tesztelheti.

Amikor kiválasztja az SAP Qualtrics csempe a hozzáférési panelen, automatikusan bejelentkezik az SAP-Qualtrics, amelyhez be van állítva az egyszeri bejelentkezés. További információ: [Bejelentkezés és alkalmazások indítása a saját alkalmazások portálján](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Oktatóanyagok SaaS-alkalmazások az Azure Active Directoryval való integrálásához](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Az SAP Qualtrics kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Az SAP-Qualtrics speciális láthatósággal és vezérlőkkel való ellátása](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)


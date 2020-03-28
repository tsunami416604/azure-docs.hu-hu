---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a HubSpottal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a HubSpot között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 57343ccd-53ea-4e62-9e54-dee2a9562ed5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c4b235426a7029abb9bb79ba56e582cccc3b14a6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "68944449"
---
# <a name="tutorial-azure-active-directory-integration-with-hubspot"></a>Oktatóanyag: Az Azure Active Directory integrációja a HubSpottal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a HubSpotot az Azure Active Directoryval (Azure AD).

A HubSpot integrálása az Azure AD-vel a következő előnyökkel jár:

* Az Azure AD segítségével szabályozhatja, hogy ki férhet hozzá a HubSpothoz.
* A felhasználók automatikusan bejelentkezhetnek a HubSpotba az Azure AD-fiókjukkal (egyszeri bejelentkezés).
* A fiókokat egy központi helyen, az Azure Portalon kezelheti.

A szoftverszolgáltatásként (SaaS) alkalmazásintegrációaz Azure AD-vel való integrációjáról az [Azure Active Directory ban lévő alkalmazásokegyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakörben talál további információt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a HubSpottal a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.
* HubSpot-előfizetés egyszeri bejelentkezéssel.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését egy tesztkörnyezetben, és integrálhatja a HubSpotot az Azure AD-vel.

A HubSpot a következő szolgáltatásokat támogatja:

* **Sp által kezdeményezett egyszeri bejelentkezés**
* **IDP által kezdeményezett egyszeri bejelentkezés**

## <a name="add-hubspot-in-the-azure-portal"></a>HubSpot hozzáadása az Azure portalon

A HubSpot azure AD-vel való integrálásához hozzá kell adnia a HubSpotot a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

1. A bal oldali menüben válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory beállítás](common/select-azuread.png)

1. Válassza **a Vállalati alkalmazások** > **minden alkalmazás lehetőséget.**

    ![A Vállalati alkalmazások ablaktábla](common/enterprise-applications.png)

1. Alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.

    ![Az Új alkalmazás opció](common/add-new-app.png)

1. A keresőmezőbe írja be a **HubSpot**kifejezést. A keresési eredmények között válassza a **HubSpot**lehetőséget, majd a **Hozzáadás**lehetőséget.

    ![HubSpot az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a HubSpottal egy **Britta Simon**nevű tesztfelhasználó alapján. Egyszeri bejelentkezés működéséhez létre kell hoznia egy összekapcsolt kapcsolatot egy Azure AD-felhasználó és a hubspot kapcsolódó felhasználó között.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a HubSpot szolgáltatással a következő építőelemeket kell végrehajtania:

| Tevékenység | Leírás |
| --- | --- |
| **[Az Azure AD egyszeri bejelentkezéskonfigurálása](#configure-azure-ad-single-sign-on)** | Lehetővé teszi a felhasználók számára a szolgáltatás használatát. |
| **[HubSpot egyszeri bejelentkezéskonfigurálása](#configure-hubspot-single-sign-on)** | Az alkalmazás egyszeri bejelentkezési beállításainak konfigurálása. |
| **[Azure AD-tesztfelhasználó létrehozása](#create-an-azure-ad-test-user)** | Teszteli az Azure AD egyszeri bejelentkezést egy Britta Simon nevű felhasználó számára. |
| **[Az Azure AD-tesztfelhasználó hozzárendelése](#assign-the-azure-ad-test-user)** | Lehetővé teszi Britta Simon számára az Azure AD egyszeri bejelentkezését. |
| **[HubSpot tesztfelhasználó létrehozása](#create-a-hubspot-test-user)** | Britta Simon megfelelőjét hozza létre a HubSpotban, amely a felhasználó Azure AD-ábrázolásához kapcsolódik. |
| **[Az egyszeri bejelentkezés tesztelése](#test-single-sign-on)** | Ellenőrzi, hogy a konfiguráció működik-e. |

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban konfigurálhatja az Azure AD egyszeri bejelentkezést a HubSpot tal az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **HubSpot** alkalmazásintegrációs ablaktáblán válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési beállítás konfigurálása](common/select-sso.png)

1. Az **Egyszeri bejelentkezési módszer kiválasztása** ablaktáblában válassza **az SAML** vagy az **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** ablaktáblán válassza a **Szerkesztés** (a ceruza ikon) lehetőséget az **egyszerű SAML-konfiguráció** ablaktábla megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** ablaktáblában az *IDP által kezdeményezett mód*konfigurálásához hajtsa végre az alábbi lépéseket:

    1. Az **Azonosító** mezőbe írjon be egy URL-címet, amelya következő mintát tartalmazza: https:\//api.hubspot.com/login-api/v1/saml/login?portalId=\<CUSTOMER ID\>.

    1. A **Válasz URL-címe** mezőbe írjon be egy\/URL-címet, amely a következő mintát követi: https: /api.hubspot.com/login-api/v1/saml/acs?portalId=\<CUSTOMER ID\>.

    ![HubSpot tartomány és URL-címek egyszeri bejelentkezési információi](common/idp-intiated.png)

    > [!NOTE]
    > Az URL-címek formázásához tekintse meg az Azure Portal **alapszintű SAML-konfigurációablakában** látható mintákat is.

1. Az alkalmazás *sp-kezdeményezésű módban történő konfigurálása:*

    1. Válassza **a További URL-címek beállítása**lehetőséget.

    1. A **Bejelentkezési URL-cím** mezőbe írja be a **\/https: /app.hubspot.com/login**.

    ![A További URL-címek beállítása beállítás](common/metadata-upload-additional-signon.png)

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** ablaktáblán az **SAML aláíró tanúsítvány** szakaszában válassza a **Letöltés** lehetőséget a **Tanúsítvány (Base64)** mellett. Válasszon egy letöltési lehetőséget az igényei nek megfelelően. Mentse a tanúsítványt a számítógépre.

    ![A Tanúsítvány (Base64) letöltési lehetőség](common/certificatebase64.png)

1. A **HubSpot beállítása** szakaszban másolja a következő URL-címeket az Ön igényei nek megfelelően:

    * Bejelentkezési URL
    * Azure Hirdetés-azonosító
    * Kijelentkezés URL-címe

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-hubspot-single-sign-on"></a>HubSpot egyszeri bejelentkezéskonfigurálása

1. Nyisson meg egy új lapot a böngészőben, és jelentkezzen be a HubSpot rendszergazdai fiókjába.

1. Válassza a lap jobb felső sarkában található **Beállítások** ikont.

    ![A HubSpot Beállítások ikonja](./media/hubspot-tutorial/config1.png)

1. Válassza **a Fiók alapértelmezéseit**lehetőséget.

    ![A Fiók alapértelmezései beállítás a HubSpotban](./media/hubspot-tutorial/config2.png)

1. Görgessen le a **Biztonság** szakaszhoz, és válassza **a Beállítás**lehetőséget.

    ![A Beállítás beállítás a HubSpotban](./media/hubspot-tutorial/config3.png)

1. Az **Egyszeri bejelentkezés beállítása csoportban** hajtsa végre az alábbi lépéseket:

    1. A **Közönség azonosító (Service Provider Entity ID)** mezőben válassza a **Másolás** lehetőséget az érték másolásához. Az Azure Portalon az **alapszintű SAML konfigurációs** ablaktáblán illessze be az értéket az **azonosító** mezőbe.

    1. Az **URL, ACS, Címzett vagy Átirányítás bejelentkezése** mezőben válassza a **Másolás** lehetőséget az érték másolásához. Az Azure Portalon az **alapszintű SAML konfigurációs** ablaktáblán illessze be az értéket a **Válasz URL-cím** mezőbe.

    1. A HubSpot, az **identitásszolgáltató azonosító vagy kiállító URL-címmezőjébe** illessze be az **Azure AD-azonosító,** amely az Azure Portalon másolt érték.

    1. A HubSpot, az **identitásszolgáltató egyszeri bejelentkezési URL-cím,** illessze be az azure portalon másolt **bejelentkezési URL-cím** értékét.

    1. A Windows Jegyzettömbben nyissa meg a letöltött Tanúsítvány(Alap64) fájlt. Jelölje ki és másolja a fájl tartalmát. Ezután a HubSpot alkalmazásban illessze be az **X.509 Tanúsítvány** mezőbe.

    1. Válassza az **Ellenőrzés** lehetőséget.

        ![Az Egyszeri bejelentkezés beállítása szakasz a HubSpotban](./media/hubspot-tutorial/config4.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban hozzon létre egy britta Simon nevű tesztfelhasználót az Azure Portalon.

1. Az Azure Portalon válassza az **Azure Active Directory** > **felhasználói** > **minden felhasználó lehetőséget.**

    ![A Felhasználók és az Összes felhasználó beállítás](common/users.png)

1. Válassza az **Új felhasználó**lehetőséget.

    ![Az Új felhasználó beállítás](common/new-user.png)

1. A **Felhasználó** ablaktáblán hajtsa végre az alábbi lépéseket:

    1. A **Név** mezőbe írja be **a BrittaSimon**értéket.
  
    1. A **Felhasználónév** mezőbe írja be **a\@\<brittasimon\< your-company-domain>. kiterjesztés.\>** Például **a\@brittasimon contoso.com**.

    1. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet. Írja le a **Jelszó** mezőben megjelenő értéket.

    1. Kattintson a **Létrehozás** gombra.

    ![A Felhasználó ablaktábla](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban hozzáférést biztosít Britta Simonnak a HubSpothoz, hogy használhassa az Azure egyszeri bejelentkezést.

1. Az Azure Portalon válassza a **Vállalati alkalmazások** > **Minden alkalmazás** > **HubSpot**lehetőséget.

    ![A Vállalati alkalmazások ablaktábla](common/enterprise-applications.png)

1. Az alkalmazások listájában válassza a **HubSpot**lehetőséget.

    ![HubSpot az alkalmazások listájában](common/all-applications.png)

1. A menüben válassza a **Felhasználók és csoportok lehetőséget.**

    ![A Felhasználók és csoportok beállítás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget. Ezután a **Hozzárendelés hozzáadása** ablaktáblán válassza a Felhasználók és **csoportok**lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

1. A **Felhasználók és csoportok** ablaktáblán válassza a Felhasználók listájában a **Britta Simon** elemet. Válassza a **Kiválasztás** lehetőséget

1. Ha az SAML-feltételben szerepkörértéket vár, a **Szerepkör kiválasztása** ablaktáblában válassza ki a megfelelő szerepkört a felhasználó számára a listából. Válassza a **Kiválasztás** lehetőséget

1. A **Hozzárendelés hozzáadása** ablaktáblán válassza a **Hozzárendelés lehetőséget.**

### <a name="create-a-hubspot-test-user"></a>HubSpot tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD egy felhasználó jelentkezzen be hubspot, a felhasználó ki kell építeni a HubSpot. A HubSpot ban a kiépítés manuális feladat.

Felhasználói fiók kiépítése a HubSpot ban:

1. Jelentkezzen be a HubSpot vállalati webhelyére rendszergazdaként.

1. Válassza a lap jobb felső sarkában található **Beállítások** ikont.

    ![A HubSpot Beállítások ikonja](./media/hubspot-tutorial/config1.png)

1. Válassza a **Felhasználók & a Csapatok lehetőséget.**

    ![A Felhasználók & csapatok beállítás a HubSpotban](./media/hubspot-tutorial/user1.png)

1. Válassza **a Felhasználó létrehozása**lehetőséget.

    ![A Felhasználó létrehozása beállítás a HubSpotban](./media/hubspot-tutorial/user2.png)

1. Az **E-mail hozzáadása hozzáadása mezőben** adja meg a felhasználó e-mail címét\@brittasimon contoso.com formátumban, majd válassza a **Tovább**gombot.

    ![A HubSpot Felhasználók létrehozása szakaszában található E-mail cím(ek) hozzáadása mező](./media/hubspot-tutorial/user3.png)

1. A **Felhasználók létrehozása** csoportban jelölje ki az egyes lapokat. Minden lapon adja meg a felhasználó számára megfelelő beállításokat és engedélyeket. Ezután válassza a **Tovább gombot.**

    ![Lapok a Felhasználók létrehozása szakaszban a HubSpotban](./media/hubspot-tutorial/user4.png)

1. Ha el szeretné küldeni a meghívót a felhasználónak, válassza a **Küldés**lehetőséget.

    ![A Küldés lehetőség a HubSpotban](./media/hubspot-tutorial/user5.png)

    > [!NOTE]
    > A felhasználó aktiválódik, miután a felhasználó elfogadja a meghívást.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a Saját alkalmazások portálhasználatával teszteli.

Miután beállította az egyszeri bejelentkezést, amikor a **HubSpot** lehetőséget választja a Saját alkalmazások portálon, automatikusan bejelentkezik a HubSpotba. A Saját alkalmazások portálról további információt az [Access és az alkalmazások használata a Saját alkalmazások portálon](../user-help/my-apps-portal-end-user-access.md)talál.

## <a name="next-steps"></a>További lépések

További információért tekintse át az alábbi cikkeket:

- [A SaaS-alkalmazások Azure Active Directoryval való integrálására szolgáló oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Egyszeri bejelentkezés az alkalmazásokba az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

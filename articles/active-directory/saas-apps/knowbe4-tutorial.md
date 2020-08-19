---
title: 'Oktatóanyag: Azure Active Directory integráció a KnowBe4 Security tudatosságnövelő képzéssel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a KnowBe4 biztonságának megismeréséhez.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.openlocfilehash: c6e70268dcc252964a0b046488c399f474660ca5
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88540077"
---
# <a name="tutorial-azure-active-directory-integration-with-knowbe4-security-awareness-training"></a>Oktatóanyag: Azure Active Directory integráció a KnowBe4 biztonsági felvilágosító képzéssel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a KnowBe4 biztonsági ismereteinek betanítását Azure Active Directory (Azure AD) használatával.
Az Azure AD-vel való KnowBe4 biztonsági ismeretek betanítása a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a KnowBe4 biztonsági ismereteinek betanításához.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek, hogy az Azure AD-fiókjával KnowBe4 a biztonsági ismeretek betanítását (egyszeri bejelentkezés).
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Ha az Azure AD-integrációt a KnowBe4 biztonságával szeretné beállítani, a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* KnowBe4 biztonsági tájékoztató egyszeri bejelentkezésre alkalmas előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A KnowBe4 Security tudatosságnövelő szolgáltatás támogatja az **SP** által KEZDEMÉNYEZett SSO-t

* A KnowBe4 biztonsági ismereteinek betanítása a felhasználói üzembe helyezést **csak időben** támogatja

## <a name="adding-knowbe4-security-awareness-training-from-the-gallery"></a>KnowBe4 biztonsági tudatosságnövelő képzés felvétele a katalógusból

A KnowBe4 biztonsági tudatosságnövelő Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a KnowBe4 biztonsági tudatosságnövelő képzését a katalógusból a felügyelt SaaS-alkalmazások listájára.

**A KnowBe4 biztonsági ismereteinek a katalógusból való hozzáadásához hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a következőt: **KnowBe4 Security betanítása**, válassza az **KnowBe4 biztonsági betanítása** az eredmények paneljéről lehetőséget, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![A KnowBe4 biztonsági ismereteinek betanítása az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálhatja és tesztelheti a KnowBe4 biztonsági ismereteinek betanításával a **Britta Simon**nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a kapcsolódó felhasználó közötti kapcsolati kapcsolatra van szükség a KnowBe4 biztonsági tudatosságnövelő képzésben.

Az Azure AD egyszeri bejelentkezés konfigurálásához és teszteléséhez a KnowBe4 biztonsági ismereteinek betanításához a következő építőelemeket kell elvégeznie:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. Az **[KnowBe4 biztonsági tájékoztatójának konfigurálása egyszeri bejelentkezéssel](#configure-knowbe4-security-awareness-training-single-sign-on)** – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[KnowBe4 biztonsági tudaton alapuló betanítási teszt felhasználó](#create-knowbe4-security-awareness-training-test-user)** – a Britta Simon partnere, amely az Azure ad-képviselethez kapcsolódik a KnowBe4 biztonsági ismereteinek betanításához.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés a KnowBe4 biztonsági betanítással való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)a **KnowBe4 Security tudatosságnövelő betanítása** alkalmazás-integráció lapon válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![A KnowBe4 biztonsági ismereteinek betanítása és az URL-címek egyszeri bejelentkezési adatai](common/sp-identifier.png)

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<companyname>.KnowBe4.com/auth/saml/<instancename>`

    > [!NOTE]
    > A bejelentkezési URL-cím értéke nem valós. Frissítse ezt az értéket a tényleges bejelentkezési URL-címmel. Az érték beszerzéséhez forduljon a [KnowBe4 Security tudatosságnövelő](mailto:support@KnowBe4.com) ügyfélszolgálatához. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatot is megtekintheti.

    b. Az **azonosító (entitás azonosítója)** szövegmezőbe írja be a karakterlánc értékét: `KnowBe4`

    > [!NOTE]
    > Ez megkülönbözteti a kis-és nagybetűket.

5. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (RAW)** letöltéséhez a megadott beállítások alapján, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificateraw.png)

6. A **KnowBe4 biztonságának beállítása** című szakaszban a követelmények szerint másolja a megfelelő URL-címeket (ka) t.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure ad-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-knowbe4-security-awareness-training-single-sign-on"></a>A KnowBe4 biztonsági tájékoztatójának konfigurálása az egyszeri bejelentkezéshez

Ha az **KnowBe4 biztonsági ismereteinek** bevezetéséhez az egyszeri bejelentkezést szeretné beállítani, el kell küldenie a letöltött **tanúsítványt (RAW)** és a megfelelő másolt url-címeket a Azure Portalről, hogy [KnowBe4 a biztonsági tudatosságnövelő képzési csapatot](mailto:support@KnowBe4.com). Ezt a beállítást úgy állították be, hogy az SAML SSO-kapcsolatok mindkét oldalon helyesen legyenek beállítva.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságainál végezze el a következő lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A **Felhasználónév** mezőbe írja be a **brittasimon \@ yourcompanydomain. Extension** nevet  
    Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít a KnowBe4 biztonsági ismereteinek betanításához.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **KnowBe4 biztonsági betanítása**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listáról válassza a **KnowBe4 biztonsági tájékoztatási képzés**lehetőséget.

    ![A KnowBe4 biztonsággal kapcsolatos ismeretek betanítása hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-knowbe4-security-awareness-training-test-user"></a>KnowBe4 biztonsági tudatosságnövelő tanfolyam létrehozása

Ennek a szakasznak a célja, hogy egy Britta Simon nevű felhasználót hozzon létre a KnowBe4 Security tudatosságnövelő képzésben. A KnowBe4 biztonsági betanítási szolgáltatás az igény szerinti üzembe helyezést is támogatja, amely alapértelmezés szerint engedélyezve van.

Ez a szakasz nem tartalmaz műveleti elemeket. A rendszer egy új felhasználót hoz létre a KnowBe4 biztonsági ismereteinek elérésére tett kísérlet során, ha még nem létezik.

> [!NOTE]
> Ha manuálisan kell létrehoznia egy felhasználót, lépjen kapcsolatba a [KnowBe4 Security tudatosságnövelő oktatási csapatával](mailto:support@KnowBe4.com).

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a KnowBe4 biztonsági tájékoztatási képzés csempére kattint, automatikusan be kell jelentkeznie a KnowBe4 biztonsági ismereteinek betanításához, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

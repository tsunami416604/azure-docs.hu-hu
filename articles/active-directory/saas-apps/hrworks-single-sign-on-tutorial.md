---
title: 'Oktatóanyag: Azure Active Directory integráció az HRworks-alapú egyszeri bejelentkezéssel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és HRworks egyszeri bejelentkezés között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c4c5d434-3f8a-411e-83a5-c3d5276ddc0a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: a36266c14531f935779266829402392dc4a03411
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74706000"
---
# <a name="tutorial-azure-active-directory-integration-with-hrworks-single-sign-on"></a>Oktatóanyag: Azure Active Directory integráció az HRworks-alapú egyszeri bejelentkezéssel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a HRworks-alapú egyszeri bejelentkezést Azure Active Directory (Azure AD) használatával.
Az HRworks egyszeri bejelentkezés az Azure AD-vel való integrálása a következő előnyöket biztosítja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá az HRworks egyszeri bejelentkezéshez.
* Engedélyezheti a felhasználók számára, hogy automatikusan bejelentkezzenek az HRworks egyszeri bejelentkezésre (egyszeri bejelentkezésre) az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció HRworks-alapú egyszeri bejelentkezéssel való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* HRworks egyszeri bejelentkezéses egyszeri bejelentkezésre alkalmas előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* Az HRworks egyszeri bejelentkezés támogatja az **SP** által kezdeményezett SSO-t

## <a name="adding-hrworks-single-sign-on-from-the-gallery"></a>HRworks-alapú egyszeri bejelentkezés hozzáadása a gyűjteményből

Az HRworks egyszeri bejelentkezés Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a HRworks egyszeri bejelentkezést a katalógusból a felügyelt SaaS-alkalmazások listájára.

**A HRworks egyszeri bejelentkezés a katalógusból való hozzáadásához hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **HRworks egyszeri bejelentkezés**kifejezést, válassza az **HRworks egyszeri bejelentkezés** lehetőséget az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![HRworks egyszeri bejelentkezés az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálhatja és tesztelheti HRworks egyszeri bejelentkezéssel a **Britta Simon**nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a kapcsolódó felhasználó közötti HRworks egyszeri bejelentkezéssel kell létrehozni.

Az Azure AD egyszeri bejelentkezés HRworks egyszeri bejelentkezéssel való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[HRworks egyszeri](#configure-hrworks-single-sign-on-single-sign-on)** bejelentkezéses egyszeri bejelentkezés konfigurálása – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[HRworks-alapú egyszeri bejelentkezéses tesztelési felhasználó létrehozása](#create-hrworks-single-sign-on-test-user)** – ha a felhasználó Azure ad-Britta összekapcsolt, HRworks-alapú egyszeri bejelentkezéshez is hozzáfér.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés HRworks egyszeri bejelentkezéssel való konfigurálásához hajtsa végre a következő lépéseket:

1. Az [Azure Portal](https://portal.azure.com/) **HRworks egyszeri bejelentkezési** alkalmazás integrációja lapon válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![HRworks egyszeri bejelentkezési tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-signonurl.png)

    A **bejelentkezési URL** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://login.hrworks.de/?companyId=<companyId>&directssologin=true`

    > [!NOTE]
    > Az érték nem valódi. Frissítse az értéket a tényleges bejelentkezési URL-címmel. Az érték beszerzéséhez vegye fel a kapcsolatot az [HRworks egyszeri bejelentkezést támogató](https://www.hrworks.de/dienstleistungen/support/) ügyfélszolgálatával. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. A **HRworks egyszeri bejelentkezés beállítása** szakaszban másolja a megfelelő URL-címet (ka) t a követelmény szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL-cím

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-hrworks-single-sign-on-single-sign-on"></a>HRworks egyszeri bejelentkezéses egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a HRworks egyszeri bejelentkezésre rendszergazdaként.

2. Kattintson a **rendszergazda** > **alapjai** > **biztonsági** > **egyszeri bejelentkezés** lehetőségre a menüsáv bal oldalán, és hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/hrworks-single-sign-on-tutorial/configure01.png)

    a. Jelölje be az **egyszeri bejelentkezés használata** jelölőnégyzetet.

    b. Adja meg az **XML-metaadatokat** **meta adatbeviteli módszerként**.

    c. Válassza az **Egyéni NameID azonosító** **értékeként a NameID értéket**.

    d. Nyissa meg a Jegyzettömbben a Azure Portal letöltött metaadatok XML-fájlját, másolja a tartalmát, majd illessze be a **metaadatok** szövegmezőbe.

    e. Kattintson a **Save** (Mentés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása 

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságainál végezze el a következő lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A **Felhasználónév** mezőbe írja be a (z) BrittaSimon@contoso.comnevet.

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a  **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít a HRworks egyszeri bejelentkezéshez.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza az **HRworks egyszeri bejelentkezés**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza az **HRworks egyszeri bejelentkezés**lehetőséget.

    ![Az HRworks egyszeri bejelentkezési hivatkozása az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-hrworks-single-sign-on-test-user"></a>HRworks egyszeri bejelentkezési teszt felhasználó létrehozása

Az Azure AD-felhasználók engedélyezéséhez jelentkezzen be az HRworks egyszeri bejelentkezésbe, az HRworks egyszeri bejelentkezéssel kell kiépíteni őket. HRworks egyszeri bejelentkezés esetén a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a HRworks-alapú egyszeri bejelentkezésre rendszergazdaként.

2. Kattintson a **rendszergazda** elemre ** > személyeket > személyeket** ** > a** menüsáv bal oldalán lévő **új személyt** .

     ![Egyszeri bejelentkezés konfigurálása](./media/hrworks-single-sign-on-tutorial/configure02.png)

3. Az előugró ablakban kattintson a **tovább**gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/hrworks-single-sign-on-tutorial/configure03.png)

4. Az **új személy létrehozása országba jogi feltételek** előugró ablakban adja meg a megfelelő adatokat, például az **Utónév**és a **vezetéknév nevet** , majd kattintson a **Létrehozás**gombra.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/hrworks-single-sign-on-tutorial/configure04.png)

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a HRworks egyszeri bejelentkezés csempére kattint, automatikusan be kell jelentkeznie a HRworks egyszeri bejelentkezésre, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


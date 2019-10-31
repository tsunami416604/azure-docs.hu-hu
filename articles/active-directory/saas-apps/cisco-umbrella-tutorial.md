---
title: 'Oktatóanyag: Azure Active Directory integráció a Cisco Umbrella-nal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a Cisco Umbrella között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 675dca98-f119-4463-8350-d6a45d5601e3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee91ca53e32bfdc387dc20054493d02d506a75da
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73158643"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-umbrella"></a>Oktatóanyag: Azure Active Directory integráció a Cisco Umbrella-nal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Cisco esernyőt Azure Active Directory (Azure AD) használatával.
A Cisco Umbrella és az Azure AD integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a Cisco Esernyőhöz.
* Engedélyezheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Cisco Umbrella-be (egyszeri bejelentkezés) az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció Cisco Umbrella-nal való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* Cisco Umbrella egyszeri bejelentkezésre alkalmas előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Cisco Umbrella támogatja **az SP és a identitásszolgáltató** által kezdeményezett SSO-t

## <a name="adding-cisco-umbrella-from-the-gallery"></a>A Cisco Umbrella hozzáadása a katalógusból

A Cisco Umbrella Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Cisco esernyőt a katalógusból a felügyelt SaaS-alkalmazások listájához.

**A Cisco Umbrella katalógusból való hozzáadásához hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Cisco Umbrella**kifejezést, válassza a **Cisco Umbrella** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![A Cisco Umbrella az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálja és teszteli az [alkalmazásnév] névvel a **Britta Simon**nevű tesztelési felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és az [alkalmazásnév] kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés az [alkalmazásnév] használatával történő konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. A **[Cisco Umbrella egyszeri bejelentkezés konfigurálása](#configure-cisco-umbrella-single-sign-on)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Cisco Umbrella-teszt felhasználó létrehozása](#create-cisco-umbrella-test-user)** – hogy a Britta Simon a Cisco Umbrella-beli partnere legyen, amely a felhasználó Azure ad-képviseletéhez van társítva.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés az [alkalmazásnév] használatával történő konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)a **Cisco Umbrella** Application Integration oldalon válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban a felhasználónak nem kell végrehajtania egy lépést, mivel az alkalmazás már előre integrálva van az Azure-ban.

    ![Cisco Umbrella-tartomány és URL-címek egyszeri bejelentkezési adatai](common/both-preintegrated-signon.png)

    a. Ha az alkalmazást **SP** intiated módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    b. Kattintson a **további URL-címek beállítása**elemre.

    c. A **bejelentkezési URL** szövegmezőbe írja be a következő URL-címet: `https://login.umbrella.com/sso`

5. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **metaadatok XML** -fájljának a megadott beállítások alapján történő letöltéséhez, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. A **Cisco Umbrella beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL-cím

    b. Azure ad-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-cisco-umbrella-single-sign-on"></a>A Cisco esernyő egyszeri bejelentkezésének konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Cisco Umbrella vállalati webhelyre rendszergazdaként.

2. A menü bal oldalán kattintson a **rendszergazda** elemre, és keresse meg a **hitelesítést** , majd kattintson az **SAML**elemre.

    ![A rendszergazda](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_admin.png)

3. Válassza a **továbbiak** lehetőséget, majd kattintson a **tovább**gombra.

    ![A másik](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_other.png)

4. A **Cisco Umbrella-metaadatok**lapon kattintson a **tovább**gombra.

    ![A metaadatok](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_metadata.png)

5. Ha a **metaadatok feltöltése** lapon előre konfigurált SAML-t állított be, válassza a **kattintson ide a módosításhoz** lehetőségre, és kövesse az alábbi lépéseket.

    ![A következő](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_next.png)

6. Az **a: fájl feltöltése XML-fájlban**töltse fel a Azure Portal letöltött **összevonási metaadatok XML-** fájlját, és a metaadatok feltöltése után az alábbi értékek automatikusan feltöltve értéket kapnak, majd kattintson a **tovább**gombra.

    ![A choosefile](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_choosefile.png)

7. Az **SAML-konfiguráció ellenőrzése** szakaszban kattintson **az SAML-konfiguráció tesztelése**elemre.

    ![A teszt](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_test.png)

8. Kattintson a **SAVE** (Mentés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságainál végezze el a következő lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A **Felhasználónév** mezőbe írja be a következőt: **brittasimon\@yourcompanydomain. Extension**  
    Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a  **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést a Cisco Umbrella elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **Cisco Umbrella**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában írja be és válassza a **Cisco Umbrella**elemet.

    ![A Cisco Umbrella-hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-cisco-umbrella-test-user"></a>Cisco Umbrella-teszt felhasználó létrehozása

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a Cisco Umbrella-ba, ki kell építeni a Cisco Umbrella-ba.  
A Cisco Umbrella esetében a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Egy másik böngészőablakban jelentkezzen be a Cisco Umbrella vállalati webhelyre rendszergazdaként.

2. A menü bal oldalán kattintson a **rendszergazda** lehetőségre, és navigáljon a **fiókok**elemre.

    ![A fiók](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_account.png)

3. A **fiókok** lapon kattintson az oldal jobb felső sarkában található **Hozzáadás** gombra, és végezze el a következő lépéseket.

    ![A felhasználó](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_createuser.png)

    a. Az Utónév mezőben adja meg a vezetéknév (például **Britta**) **nevet** .

    b. A **vezetékneve** mezőben adja meg a (z) **Simon**nevet.

    c. A **delegált rendszergazdai szerepkör kiválasztása**lapon válassza ki a szerepkört.
  
    d. Az **e-mail-cím** mezőbe írja be a felhasználó, például a **brittasimon\@contoso.com**.

    e. A **jelszó** mezőbe írja be a jelszavát.

    f. A **Jelszó megerősítése** mezőbe írja be újra a jelszót.

    g. Kattintson a **Létrehozás**gombra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Cisco Umbrella csempére kattint, automatikusan be kell jelentkeznie a Cisco Umbrella-be, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

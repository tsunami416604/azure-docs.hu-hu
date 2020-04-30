---
title: 'Oktatóanyag: Azure Active Directory integráció az autotask munkahellyel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és az autotask munkahely között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a9a7ff71-c389-4169-aafd-d7a505244797
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97a68fd74449ea34438348cbc4c9d09c06d46d71
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "73157808"
---
# <a name="tutorial-azure-active-directory-integration-with-autotask-workplace"></a>Oktatóanyag: Azure Active Directory integráció az autotask munkahellyel

Ez az oktatóanyag azt ismerteti, hogyan integrálható az autotask munkahely a Azure Active Directory (Azure AD) szolgáltatással.
Az autotask munkahely az Azure AD-vel való integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá az autotask munkahelyhez.
* Engedélyezheti a felhasználók számára, hogy automatikusan bejelentkezzenek az automatikus feladat munkahelyre (egyszeri bejelentkezés) az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció az autotask munkahellyel való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* A munkahelyi egyszeri bejelentkezéshez engedélyezett előfizetés
* Egy autotask munkahelyi egyszeri bejelentkezést engedélyező előfizetés
* A munkahelyen rendszergazdának vagy felügyelői rendszergazdának kell lennie.
* Rendszergazdai fiókkal kell rendelkeznie az Azure AD-ben.
* A szolgáltatást használó felhasználóknak a munkahelyen és az Azure AD-ban is szerepelniük kell a fiókoknak, és az e-mail-címeiknek is egyezniük kell.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* Az autotask munkahely támogatja **az SP-t és a identitásszolgáltató** kezdeményezett SSO-t

## <a name="adding-autotask-workplace-from-the-gallery"></a>Autotask munkahely hozzáadása a katalógusból

Az autotask munkahely az Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az autotask munkaterületet a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha az autotask munkaterületet szeretné hozzáadni a katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **autotask munkahely**kifejezést, válassza az **autotask munkahely** lehetőséget az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Az autotask munkaterülete az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az autotask munkaterülettel konfigurálja és teszteli a **Britta Simon**nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és az automatikus feladat-munkaterületen lévő kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés autotask munkahellyel való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. Az **[autotask munkahelyi egyszeri bejelentkezés konfigurálása](#configure-autotask-workplace-single-sign-on)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. Az **[autotask munkahely-tesztelési felhasználó létrehozása](#create-autotask-workplace-test-user)** – ha a Britta Simon-t az autotask munkaterületen szeretné létrehozni, amely a felhasználó Azure ad-képviseletéhez van társítva.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés autotask munkahellyel való konfigurálásához hajtsa végre a következő lépéseket:

1. Az [Azure Portal](https://portal.azure.com/)az **autotask munkahely** -integráció lapon válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    ![Az autotask munkahely-tartomány és az URL-címek egyszeri bejelentkezési adatai](common/idp-intiated.png)

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<subdomain>.awp.autotask.net/singlesignon/saml/metadata`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<subdomain>.awp.autotask.net/singlesignon/saml/SSO`

5. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    ![Az autotask munkahely-tartomány és az URL-címek egyszeri bejelentkezési adatai](common/metadata-upload-additional-signon.png)

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<subdomain>.awp.autotask.net/loginsso`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Az értékek megszerzéséhez forduljon az [autotask munkahelyi](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) ügyfélszolgálati csoporthoz. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

6. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

7. Az **autotask munkahely beállítása** szakaszban adja meg a megfelelő URL (eke) t a követelménynek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure ad-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-autotask-workplace-single-sign-on"></a>Az autotask munkahely egyszeri bejelentkezésének konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a munkahelyi Online szolgáltatásba a rendszergazdai hitelesítő adatokkal.

    > [!Note]
    > A identitásszolgáltató konfigurálásakor meg kell adni egy altartományt. A megfelelő altartomány megerősítéséhez jelentkezzen be a munkahelyi online webhelyre. Ha bejelentkezett, jegyezze fel az altartományt az URL-címben. Az altartomány a "https://" és a ". awp.autotask.net/" közé tartozik, és a következőnek kell lennie: USA, EU, CA vagy au.

2. Lépjen a **konfiguráció** > **egyszeri bejelentkezéshez** , és hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezési konfiguráció automatikus feladatainak konfigurálása](./media/autotaskworkplace-tutorial/tutorial_autotaskssoconfig1.png)

    a. Válassza ki az **XML-metaadatok fájlját** , majd töltse fel a letöltött **összevonási metaadatok XML** -t a Azure Portalból.

    b. Kattintson az **egyszeri bejelentkezés engedélyezése**lehetőségre.

    ![Automatikus feladat egyszeri bejelentkezésének jóváhagyása](./media/autotaskworkplace-tutorial/tutorial_autotaskssoconfig2.png)

    c. Jelölje be a **megerősítem ezt az információt jelölőnégyzetet, és bízom benne, hogy ez a identitásszolgáltató** .

    d. Kattintson a **jóváhagyás**gombra.

> [!Note]
> Ha segítségre van szüksége az autotask munkahely konfigurálásához, tekintse meg [ezt a lapot](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) , és kérjen segítséget a munkahelyi fiókjával.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságainál végezze el a következő lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A Felhasználónév mezőbe írja be a **brittasimon\@yourcompanydomain. Extension** **nevet**  
    Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Létrehozás**gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést az autotask munkahely elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza az **autotask munkahely**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza az **autotask munkahely**elemet.

    ![Az automatikus feladat munkaterületre mutató hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-autotask-workplace-test-user"></a>Az autotask munkahelyi tesztelési felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre az autotask munkahelyen. Az autotask munkahely- [támogatási csapattal](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) együttműködve vegye fel a felhasználókat az autotask munkahelyi platformba.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Amikor a hozzáférési panelen rákattint az automatikus feladat munkaterületre, automatikusan be kell jelentkeznie az automatikus feladat munkaterületre, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

---
title: 'Oktatóanyag: Azure Active Directory-integráció a NetSuite-nal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a NetSuite között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/17/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e462197bac854004aaf2d2f0f96e121ed081581a
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967242"
---
# <a name="tutorial-azure-active-directory-integration-with-netsuite"></a>Oktatóanyag: Azure Active Directory-integráció a NetSuite-nal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a NetSuite-t a Azure Active Directory (Azure AD) szolgáltatásba.
A NetSuite és az Azure AD integrálása a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a NetSuite-hoz.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a NetSuite-ba (egyszeri bejelentkezés) az Azure AD-fiókkal.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció a NetSuite-nal való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* NetSuite egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A NetSuite támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést
* A NetSuite **a** felhasználók üzembe helyezésének időpontját is támogatja
* A NetSuite támogatja a [felhasználók automatikus kiépítési](NetSuite-provisioning-tutorial.md) felállítását

## <a name="adding-netsuite-from-the-gallery"></a>A NetSuite hozzáadása a katalógusból

A NetSuite Azure AD-ba való integrálásának konfigurálásához hozzá kell adnia a NetSuite-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

**A következő lépések végrehajtásával veheti fel a NetSuite-t a katalógusból:**

1. Az a **[az Azure portal](https://portal.azure.com)** , kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **NetSuite**kifejezést, válassza a **NetSuite** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![A NetSuite az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálja és teszteli a NetSuite-vel a **Britta Simon**nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a NetSuite kapcsolódó felhasználója közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés a NetSuite-vel való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. A **[NetSuite egyszeri bejelentkezés konfigurálása](#configure-netsuite-single-sign-on)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. A **[NetSuite-teszt felhasználójának létrehozása](#create-netsuite-test-user)** – hogy a Britta Simon in NetSuite partnere legyen, amely a felhasználó Azure ad-képviseletéhez van társítva.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés a NetSuite-vel való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)a **NetSuite** Application Integration oldalon válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az alapszintű **SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![A NetSuite tartomány és az URL-címek egyszeri bejelentkezési adatai](common/idp-reply.png)

    A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:

    `https://<tenant-name>.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na1.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na2.NetSuite.com/saml2/acs`

    `https://<tenant-name>.sandbox.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na1.sandbox.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na2.sandbox.NetSuite.com/saml2/acs`

    > [!NOTE]
    > Az érték nem valódi. Frissítse az értéket a tényleges válasz URL-címével. Az érték beszerzéséhez forduljon a [NetSuite ügyfél-támogatási csapathoz](https://www.netsuite.com/portal/services/support-services/suitesupport.shtml) . Az Azure Portal alapszintű **SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. A NetSuite alkalmazás meghatározott formátumban várja az SAML-kijelentéseket. Konfigurálja a következő jogcímeket ehhez az alkalmazáshoz. Az attribútumok értékeit az alkalmazás-integráció lapon, a **felhasználói attribútumok** szakaszban kezelheti. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** gombra a **felhasználói attribútumok** párbeszédpanel megnyitásához.

    ![image](common/edit-attribute.png)

6. A **felhasználó attribútumai** párbeszédpanel **felhasználói** jogcímek szakaszában szerkessze a jogcímeket a **Szerkesztés ikon** használatával, vagy adja hozzá a jogcímeket az **új jogcím hozzáadása** paranccsal az SAML-token attribútum konfigurálásához a fenti képen látható módon, és hajtsa végre a következő lépéseket:
    
    | Name (Név) | Forrás attribútum | 
    | ---------------| --------------- |
    | account  | `account id` |

    a. Kattintson az **új jogcím hozzáadása** elemre a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. A **név** szövegmezőbe írja be az adott sorhoz megjelenített attribútum nevét.

    c. Hagyja üresen a **névteret** .

    d. Válassza a forrás **attribútumként**lehetőséget.

    e. A **forrás attribútum** listáról írja be az adott sorhoz megjelenő attribútum értékét.

    f. Kattintson a **Ok**

    g. Kattintson a **Save** (Mentés) gombra.

    >[!NOTE]
    >A Account attribútum értéke nem valódi. Ezt az értéket frissíteni fogja, amelyet az oktatóanyag későbbi részében ismertetünk.

4. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **metaadatok XML** -fájljának a megadott beállítások alapján történő letöltéséhez, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

6. A a a **NetSuite beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmény szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure ad-azonosító

    c. Kijelentkezési URL

### <a name="configure-netsuite-single-sign-on"></a>A NetSuite egyszeri bejelentkezés konfigurálása

1. Nyisson meg egy új fület a böngészőben, és jelentkezzen be a NetSuite vállalati webhelyre rendszergazdaként.

2. A lap tetején található eszköztáron kattintson a **beállítás**elemre, majd keresse meg a **vállalatot** , és kattintson a **szolgáltatások engedélyezése**lehetőségre.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-setupsaml.png)

3. Az oldal közepén található eszköztáron kattintson a **SuiteCloud**elemre.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-suitecloud.png)

4. A **hitelesítés kezelése** szakaszban válassza az **SAML egyszeri bejelentkezés** lehetőséget az SAML egyszeri bejelentkezés beállításának engedélyezéséhez a NetSuite-ban.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-ticksaml.png)

5. A lap tetején található eszköztáron kattintson a **telepítés**elemre.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-setup.png)

6. A **telepítési feladatok** listában kattintson az **integráció**elemre.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-integration.png)

7. A **hitelesítés kezelése** szakaszban kattintson az **SAML egyszeri bejelentkezés**elemre.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-saml.png)

8. Az **SAML beállítása** lapon a NetSuite- **konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a. Válassza ki az **elsődleges hitelesítési módszert**.

    b. Az SAMLV2-SZOLGÁLTATÓi **metaadatokat**tartalmazó mezőnél válassza a **identitásszolgáltató-metaadatok feltöltése**lehetőséget. Ezután kattintson a **Tallózás** gombra a Azure Portal letöltött metaadat-fájl feltöltéséhez.

    c. Kattintson a **Submit** (Küldés) gombra.

9. A NetSuite-ban kattintson a **telepítés** elemre, majd keresse meg a **vállalatot** , és kattintson a felső navigációs menü **vállalati adatok** elemére.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-com.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-account-id.png)

    b. A jobb oldali oszlop **Vállalati információk** lapján másolja a **fiók azonosítóját**.

    c. Illessze be azt a **fiókazonosító** -fiókot, amelyet a NetSuite-fiókból másolt az Azure ad **attribútum érték** mezőjébe. 

10. Ahhoz, hogy a felhasználók egyszeri bejelentkezést tudjanak végezni a NetSuite-ba, először hozzá kell rendelni a megfelelő engedélyeket a NetSuite-ban. Az engedélyek hozzárendeléséhez kövesse az alábbi utasításokat.

    a. A felső navigációs menüben kattintson a **telepítés**elemre.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-setup.png)

    b. A bal oldali navigációs menüben válassza a **felhasználók/szerepkörök**, majd a **Szerepkörök kezelése**lehetőséget.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-manage-roles.png)

    c. Kattintson az **Új szerepkör**elemre.

    d. Adja meg az új szerepkör **nevét** .

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-new-role.png)

    e. Kattintson a **Save** (Mentés) gombra.

    f. A felső menüben kattintson az **engedélyek**elemre. Ezután kattintson a **telepítés**elemre.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-sso.png)

    g. Válassza az **SAML egyszeri bejelentkezés**lehetőséget, majd kattintson a **Hozzáadás**gombra.

    h. Kattintson a **Save** (Mentés) gombra.

    i. A felső navigációs menüben kattintson a **telepítő**, majd a **telepítéskezelő**elemre.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-setup.png)

    j. A bal oldali navigációs menüben válassza a **felhasználók/szerepkörök**, majd a **felhasználók kezelése**lehetőséget.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-manage-users.png)

    k. Válasszon ki egy teszt felhasználót. Ezután kattintson a **Szerkesztés** gombra, és keresse meg a **hozzáférés** lapot.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-edit-user.png)

    l. A szerepkörök párbeszédpanelen rendelje hozzá a létrehozott megfelelő szerepkört.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-add-role.png)

    m. Kattintson a **Save** (Mentés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A Felhasználónév mezőbe írja be a **brittasimon\@yourcompanydomain. Extension** **nevet**  
    Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést a NetSuite elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **NetSuite**elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában írja be a következőt: és válassza a **NetSuite**elemet.

    ![A NetSuite hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-netsuite-test-user"></a>NetSuite-teszt felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a NetSuite-ban. A NetSuite az igény szerinti felhasználói üzembe helyezést is támogatja, ami alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik a NetSuite-ban, a rendszer egy újat hoz létre a hitelesítés után.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Amikor a hozzáférési panelen a NetSuite csempére kattint, automatikusan be kell jelentkeznie a NetSuite-ba, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [A felhasználók üzembe helyezésének konfigurálása](NetSuite-provisioning-tutorial.md)


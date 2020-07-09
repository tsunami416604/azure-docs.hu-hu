---
title: 'Oktatóanyag: Azure Active Directory integráció a Zscaler internet-hozzáférés rendszergazdájával | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a Zscaler internet-hozzáférés rendszergazdája között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: ea555097-bf62-45dd-9b45-b75c50324a69
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7623373be1aded29cb022f1d5080ed94d96b719
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2020
ms.locfileid: "86170088"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-internet-access-administrator"></a>Oktatóanyag: Azure Active Directory integráció a Zscaler Internet-hozzáférési rendszergazdájával

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Zscaler Internet-hozzáférési rendszergazdáját Azure Active Directory (Azure AD) használatával.
A Zscaler internet-hozzáférés rendszergazdájának az Azure AD-vel való integrálása a következő előnyöket biztosítja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a Zscaler internet-hozzáférés rendszergazdája számára.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek az Internet-hozzáférés Zscaler (egyszeri bejelentkezés) az Azure AD-fiókokkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció Zscaler való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* Zscaler internet-hozzáférés rendszergazdai előfizetése

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Zscaler internet-hozzáférés rendszergazdája támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést

## <a name="adding-zscaler-internet-access-administrator-from-the-gallery"></a>A Zscaler internet-hozzáférés rendszergazdájának hozzáadása a katalógusból

A Zscaler Internet-hozzáférési rendszergazdája Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Zscaler internet-hozzáférés rendszergazdáját a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha a katalógusból szeretné hozzáadni a Zscaler Internet-hozzáférési rendszergazdát, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Zscaler internet-hozzáférés rendszergazdája**kifejezést, válassza a **Zscaler internet-hozzáférés rendszergazdája** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Zscaler az Internet-hozzáférés rendszergazdája az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálhatja és tesztelheti a Zscaler Internet-hozzáférési rendszergazdájával a **Britta Simon**nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a Zscaler kapcsolódó felhasználó közötti kapcsolat létesítése szükséges.

Az Azure AD egyszeri bejelentkezés konfigurálásához és teszteléséhez a Zscaler Internet-hozzáférési rendszergazdájával a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. Az **[Zscaler internet-hozzáférés rendszergazdai egyszeri bejelentkezésének konfigurálása](#configure-zscaler-internet-access-administrator-single-sign-on)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Zscaler-alapú internet-hozzáférés rendszergazdai tesztelési felhasználó létrehozása](#create-zscaler-internet-access-administrator-test-user)** – a Zscaler Internet-hozzáférési rendszergazdájához tartozó Britta, amely a felhasználó Azure ad-képviseletéhez van társítva.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés az Zscaler internet-hozzáférés-rendszergazdával való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)a **Zscaler internet-hozzáférés rendszergazdai** alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** gombra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Zscaler az Internet-hozzáférés rendszergazdai tartománya és az URL-címek egyszeri bejelentkezési adatai](common/idp-intiated.png)

    a. Az **azonosító** szövegmezőbe írja be a követelménynek megfelelő URL-címet:

    - `https://admin.zscaler.net`
    - `https://admin.zscalerone.net`
    - `https://admin.zscalertwo.net`
    - `https://admin.zscalerthree.net`
    - `https://admin.zscloud.net`
    - `https://admin.zscalerbeta.net`

    b. A **Válasz URL-címe** szövegmezőbe írja be a követelménynek megfelelő URL-címet:

    - `https://admin.zscaler.net/adminsso.do`
    - `https://admin.zscalerone.net/adminsso.do`
    - `https://admin.zscalertwo.net/adminsso.do`
    - `https://admin.zscalerthree.net/adminsso.do`
    - `https://admin.zscloud.net/adminsso.do`
    - `https://admin.zscalerbeta.net/adminsso.do`

5. A Zscaler internet-hozzáférés rendszergazdai alkalmazása egy adott formátumban várja az SAML-kijelentéseket. Konfigurálja a következő jogcímeket ehhez az alkalmazáshoz. Ezen attribútumok értékeit az alkalmazás-integráció lapon lévő **felhasználói attribútumok & jogcímek** szakaszban kezelheti. Az **egyszeri bejelentkezés SAML-vel való beállítása lapon**kattintson a **Szerkesztés** gombra a **felhasználói attribútumok & jogcímek** párbeszédpanel megnyitásához.

    ![Az attribútum hivatkozása](./media/zscaler-internet-access-administrator-tutorial/tutorial_zscaler-internet_attribute.png)

6. A **felhasználó attribútumai** párbeszédpanel **felhasználói jogcímek** szakaszában konfigurálja az SAML-jogkivonat attribútumot a fenti képen látható módon, és hajtsa végre a következő lépéseket:

    | Name  | Forrás attribútum  |
    | ---------| ------------ |
    | Szerepkör     | User. assignedroles |

    a. Kattintson az **új jogcím hozzáadása** elemre a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    ![image](./common/new-save-attribute.png)
    
    ![image](./common/new-attribute-details.png)

    b. A **forrás attribútum** listáról selelct az attribútum értékét.

    c. Kattintson az **OK** gombra.

    d. Kattintson a **Mentés** gombra.

    > [!NOTE]
    > [Ide kattintva](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) megtudhatja, hogyan konfigurálhatja a szerepkört az Azure ad-ben

7. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

8. Az **Zscaler internet-hozzáférésének beállítása** című szakaszban adja meg a megfelelő URL (eke) t a követelménynek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure ad-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-zscaler-internet-access-administrator-single-sign-on"></a>Az Zscaler internet-hozzáférés rendszergazdai egyszeri bejelentkezésének konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Zscaler internet-hozzáférés rendszergazdai felhasználói felületére.

2. Nyissa meg az **adminisztráció > rendszergazdai felügyelet** eszközt, és hajtsa végre a következő lépéseket, majd kattintson a Mentés gombra:

    ![Felügyelet](./media/zscaler-internet-access-administrator-tutorial/AdminSSO.png "Felügyelet")

    a. Jelölje be az **SAML-hitelesítés engedélyezése**címűt.

    b. Kattintson a **feltöltés**gombra, és töltse fel a **nyilvános SSL-tanúsítványban**Azure Portal letöltött Azure SAML-aláíró tanúsítványt.

    c. Ha szükséges, a további biztonság érdekében adja hozzá a **kibocsátó** adatait az SAML-válasz kiállítójának ellenőrzéséhez.

3. A rendszergazdai felhasználói felületen hajtsa végre a következő lépéseket:

    ![Felügyelet](./media/zscaler-internet-access-administrator-tutorial/ic800207.png)

    a. Vigye az egérmutatót a bal alsó sarokban található **aktiválási** menü fölé.

    b. Kattintson az **aktiválás**gombra.

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

    d. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a Zscaler Internet-hozzáférési rendszergazdájához.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza az **Zscaler internet-hozzáférés rendszergazdája**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában írja be és válassza a **Zscaler internet-hozzáférés rendszergazdája**elemet.

    ![A Zscaler internet-hozzáférés rendszergazdája hivatkozás az alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-zscaler-internet-access-administrator-test-user"></a>Zscaler internet-hozzáférés rendszergazdai tesztelési felhasználójának létrehozása

Ennek a szakasznak a célja, hogy létrehozzon egy Britta Simon nevű felhasználót a Zscaler internet-hozzáférés rendszergazdájánál. A Zscaler internet-hozzáférés nem támogatja az igény szerinti üzembe helyezést a rendszergazdai egyszeri bejelentkezéshez. Manuálisan kell létrehoznia egy rendszergazdai fiókot.
A rendszergazdai fiókok létrehozásával kapcsolatos lépésekért tekintse meg a Zscaler dokumentációját:

https://help.zscaler.com/zia/adding-admins

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Zscaler internet-hozzáférés rendszergazdája csempére kattint, automatikusan be kell jelentkeznie a Zscaler internet-hozzáférés felügyeleti felhasználói felületére, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

---
title: 'Oktatóanyag: Az Alibaba Felhőszolgáltatás (Role-based SSO) az Azure Active Directory-integráció |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és az Alibaba Felhőszolgáltatás (Role-based SSO) között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3667841e-acfc-4490-acf5-80d9ca3e71e8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/05/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a79ff869ac57a089664bbd7277ad4ddc9ffea08e
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59287377"
---
# <a name="tutorial-azure-active-directory-integration-with-alibaba-cloud-service-role-based-sso"></a>Oktatóanyag: Az Alibaba Felhőszolgáltatás (Role-based SSO) az Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálhatja az Alibaba Felhőszolgáltatás (Role-based SSO) az Azure Active Directoryval (Azure AD).
Az Alibaba Felhőszolgáltatás (Role-based SSO) integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá az Alibaba Felhőszolgáltatás (Role-based SSO) az Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezett az Alibaba felhőszolgáltatáshoz (Role-based SSO) (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása az Alibaba felhőszolgáltatás (Role-based SSO), a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a [ingyenes fiókkal](https://azure.microsoft.com/free/)
* Az Alibaba Felhőszolgáltatás (Role-based SSO) az egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Az Alibaba Felhőszolgáltatás (Role-based SSO) támogatja a **Identitásszolgáltató** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-alibaba-cloud-service-role-based-sso-from-the-gallery"></a>Az Alibaba Felhőszolgáltatás (Role-based SSO) hozzáadása a katalógusból

Az Alibaba felhőszolgáltatás (Role-based SSO) az Azure AD-be integráció konfigurálásához, hozzá kell Alibaba Felhőszolgáltatás (Role-based SSO) a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Az Alibaba Felhőszolgáltatás (Role-based SSO) hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Alibaba Felhőszolgáltatás (Role-based SSO)**, jelölje be **Alibaba Felhőszolgáltatás (Role-based SSO)** eredmény panelen kattintson a **Hozzáadás** gombra kattintva adhat hozzá a az alkalmazás.

    ![Az Alibaba Felhőszolgáltatás (Role-based SSO) a találatok listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az Alibaba felhőszolgáltatás (Role-based SSO) nevű tesztfelhasználó alapján **Britta Simon**.
Egyszeri bejelentkezés működjön, az Azure AD-felhasználót és a kapcsolódó felhasználó Alibaba felhőszolgáltatásban (Role-based SSO) közötti kapcsolat kapcsolatot kell hozható létre.

Az Azure AD egyszeri bejelentkezés az Alibaba felhőszolgáltatás (Role-based SSO) tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az Alibaba Felhőszolgáltatás (Role-based SSO) egyszeri bejelentkezés konfigurálása](#configure-alibaba-cloud-service-role-based-sso-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Az Alibaba Felhőszolgáltatás (Role-based SSO) tesztfelhasználó létrehozása](#create-alibaba-cloud-service-role-based-sso-test-user)**  – egy megfelelője a Britta Simon van az Alibaba Cloud Service (Role-based SSO), amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Az Azure AD egyszeri bejelentkezés konfigurálása az Alibaba felhőszolgáltatás (Role-based SSO), hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **Alibaba Felhőszolgáltatás (Role-based SSO)** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **állítsa be egyszeri bejelentkezést az SAML** lapon, a következő lépésekkel:

    ![Az Alibaba Felhőszolgáltatás (Role-based SSO) tartomány és URL-címeket egyetlen bejelentkezési adatait](common/idp-intiated.png)

    a. Az a **azonosító** szöveg írja be az URL-cím bármelyikét:
    
    | |
    |--|
    | `urn:alibaba:cloudcomputing` |
    | `urn:alibaba:cloudcomputing:international` |

    b. Az a **válasz URL-cím** szöveg írja be az URL-cím bármelyikét:

    | |
    |--|
    | `https://signin.aliyun.com/saml-role/SSO` |
    | `https://signin.alibabacloud.com/saml-role/SSO` |

5. Az Alibaba Felhőszolgáltatás (Role-based SSO) alkalmazás a SAML helyességi feltételek vár egy megadott formátumban, amely megköveteli, hogy egyéni attribútum-leképezéshez az SAML-jogkivonat attribútumai konfigurációja. Az alábbi képernyőképen az alapértelmezett attribútumok listáját jeleníti meg. Kattintson a **szerkesztése** ikonra kattintva nyissa meg a **felhasználói attribútumok** párbeszédpanel.

    ![image](common/edit-attribute.png)

6. Emellett a fent az Alibaba Felhőszolgáltatás (Role-based SSO) alkalmazás vár néhány további attribútumok vissza SAML-válasz átadni. A a **felhasználói jogcímek** szakaszában a **felhasználói attribútumok** párbeszédpanelen a következő lépésekkel adja hozzá a SAML-jogkivonat attribútumot, ahogyan az alábbi táblázatban:

    | Name (Név) | Adatforrás-attribútum|
    | ---------------| --------------- |
    | Szerepkör | user.assignedroles |
    | RoleSessionName | user.mail |

    > [!NOTE]
    > Kattintson a [Itt](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management) konfigurálása tudni **szerepkör** az Azure ad-ben

    a. Kattintson a **hozzáadása új jogcímet** megnyitásához a **kezelheti a felhasználói jogcímek** párbeszédpanel.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    c. Hagyja a **Namespace** üres.

    d. Válassza ki a forrás, **attribútum**.

    e. Az a **forrásattribútum** list, írja be az adott sorhoz feltüntetett attribútumot értéket.

    f. Kattintson a **Save** (Mentés) gombra.

7. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **összevonási metaadatainak XML**  a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

8. Az a **Alibaba Felhőszolgáltatás (Role-based SSO) beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-alibaba-cloud-service-role-based-sso-single-sign-on"></a>Az Alibaba Cloud Service (Role-based SSO) egyszeri bejelentkezés konfigurálása

Az egyszeri bejelentkezés konfigurálása **Alibaba Felhőszolgáltatás (Role-based SSO)** oldalon kell küldenie a letöltött **összevonási metaadatainak XML** és a megfelelő másolt URL-címek az Azure Portalról [ Az Alibaba Felhőszolgáltatás (Role-based SSO) támogatási csoportjának](https://www.aliyun.com/service/). Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be `brittasimon@yourcompanydomain.extension`. Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon Azure egyszeri bejelentkezést a hozzáférés biztosításával az Alibaba felhőszolgáltatáshoz (Role-based SSO) használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Alibaba Felhőszolgáltatás (Role-based SSO)**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Alibaba Felhőszolgáltatás (Role-based SSO)**.

    ![Az Alibaba Felhőszolgáltatás (szerepkör-alapú egyszeri bejelentkezés) hivatkozásra az alkalmazások listáját](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-alibaba-cloud-service-role-based-sso-test-user"></a>Az Alibaba Felhőszolgáltatás (Role-based SSO) tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű Alibaba felhőszolgáltatásban (Role-based SSO) felhasználói hoz létre. Együttműködve [Alibaba Felhőszolgáltatás (Role-based SSO) támogatási csoportjának](https://www.aliyun.com/service/) a felhasználók hozzáadása az Alibaba Felhőszolgáltatás (Role-based SSO) platform. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen az Alibaba Felhőszolgáltatás (Role-based SSO) csempére kattint, akkor kell is automatikusan megtörténik a, amelynek beállítása egyszeri bejelentkezés az Alibaba felhőszolgáltatáshoz (Role-based SSO). A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


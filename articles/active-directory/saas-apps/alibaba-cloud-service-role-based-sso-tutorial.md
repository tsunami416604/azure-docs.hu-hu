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
ms.date: 05/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d4b154b8ce55c381f1398c696bc439067dccfab
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65785760"
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

5. Az a **Alibaba Felhőszolgáltatás (Role-based SSO)** kattintson **tulajdonságok** a bal oldali navigációs panelen, és másolja a **objektumazonosító:** és menti azt a számítógépet későbbi használatra.

    ![Tulajdonságok konfigurálása](./media/alibaba-cloud-service-role-based-sso-tutorial/Properties.png)
    
## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az Alibaba felhőszolgáltatás (Role-based SSO) nevű tesztfelhasználó alapján **Britta Simon**.
Egyszeri bejelentkezés működjön, az Azure AD-felhasználót és a kapcsolódó felhasználó Alibaba felhőszolgáltatásban (Role-based SSO) közötti kapcsolat kapcsolatot kell hozható létre.

Az Azure AD egyszeri bejelentkezés az Alibaba felhőszolgáltatás (Role-based SSO) tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az Alibaba Cloud Service-ben a szerepkör-alapú egyszeri bejelentkezés konfigurálása](#configure-role-based-single-sign-on-in-alibaba-cloud-service)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
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

4. Az a **alapszintű SAML-konfigurációja** szakaszt, ha rendelkezik **szolgáltató metaadatait tartalmazó fájl**, hajtsa végre az alábbi lépéseket:

    >[!NOTE]
    >Ebből a Service Provider metaadatok kap [URL-címe](https://signin.alibabacloud.com/saml-role/sp-metadata.xml)

    a. Kattintson a **metaadatfájl feltöltése**.

    ![image](common/upload-metadata.png)

    b. Kattintson a **mappa embléma** válassza ki a metaadat-fájlt, és kattintson a **feltöltése**.

    ![image](common/browse-upload-metadata.png)

    c. A metaadatfájl sikeres feltöltését követően a **azonosító** és **válasz URL-cím** értékek lekérése az Alibaba Felhőszolgáltatás (Role-based SSO) szakasz szövegmezőben automatikusan:

    ![image](common/idp-intiated.png)

    > [!Note]
    > Ha a **azonosító** és **válasz URL-cím** értékek nem automatikusan lekérése, majd adja meg az értékeket manuálisan a követelmény alapján.

5. Az Alibaba Felhőszolgáltatás (Role-based SSO) alkalmazás a SAML helyességi feltételek vár egy megadott formátumban, amely megköveteli, hogy egyéni attribútum-leképezéshez az SAML-jogkivonat attribútumai konfigurációja. Az alábbi képernyőképen az alapértelmezett attribútumok listáját jeleníti meg. Kattintson a **szerkesztése** ikonra kattintva nyissa meg a **felhasználói attribútumok** párbeszédpanel.

    ![image](common/edit-attribute.png)

6. Emellett a fent az Alibaba Felhőszolgáltatás (Role-based SSO) alkalmazás vár néhány további attribútumok vissza SAML-válasz átadni. A a **felhasználói jogcímek** szakaszában a **felhasználói attribútumok** párbeszédpanelen a következő lépésekkel adja hozzá a SAML-jogkivonat attribútumot, ahogyan az alábbi táblázatban:

    | Name (Név) | Névtér | Adatforrás-attribútum|
    | ---------------| ------------| --------------- |
    | Szerepkör | https:\//www.aliyun.com/SAML-Role/Attribute | user.assignedroles |
    | RoleSessionName | https:\//www.aliyun.com/SAML-Role/Attribute | user.userprincipalname |

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

### <a name="configure-role-based-single-sign-on-in-alibaba-cloud-service"></a>Az Alibaba Cloud Service-ben a szerepkör-alapú egyszeri bejelentkezés konfigurálása

1. Jelentkezzen be az Alibaba Felhőbeli [RAM konzol](https://account.alibabacloud.com/login/login.htm?oauth_callback=https%3A%2F%2Fram.console.aliyun.com%2F%3Fspm%3Da2c63.p38356.879954.8.7d904e167h6Yg9) Partner1 használatával.

2. A bal oldali navigációs panelen válassza ki **SSO**.

3. Az a **szerepkör-alapú egyszeri bejelentkezés** lapra, majd **identitásszolgáltató létrehozása**.

4. A megjelenített lap, adja meg `AAD` identitásszolgáltató név mezőben adja meg egy leírást a **Megjegyzés** mezőben kattintson **feltöltése** , és kattintson a Töltsefelazelőzőlegletöltöttösszevonásimetaadataittartalmazófájl **OK**.

5. Az identitásszolgáltató sikeres létrehozása után kattintson a **RAM-szerepkör létrehozása**.

6. Az a **RAM szerepkörnév** mezőbe írja be `AADrole`válassza `AAD` a a **identitásszolgáltató kiválasztása** legördülő listában, és kattintson az OK gombra.

    >[!NOTE]
    >Igény szerint is biztosítson engedélyt a szerepkörhöz. Miután létrehozta az identitásszolgáltató és a megfelelő szerepkör, azt javasoljuk, hogy a ARNs az identitásszolgáltató és a szerepkör későbbi használatra menti. Az IdP információkat tartalmazó oldal és a szerepkör információkat tartalmazó oldal a ARNs szerezheti be.

7. Az Alibaba felhőalapú RAM szerepkör (AADrole) társítása az Azure AD-felhasználó (u2): A RAM-MAL szerepkör társítása az Azure AD-felhasználó, létre kell hoznia egy szerepkörhöz az Azure ad-ben az alábbi lépéseket:

    a. Jelentkezzen be a [az Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer?spm=a2c63.p38356.879954.9.7d904e167h6Yg9).

    b. Kattintson a **engedélyek módosítása** szerepkör létrehozásához szükséges engedélyek beszerzése.

    ![Graph-konfiguráció](./media/alibaba-cloud-service-role-based-sso-tutorial/graph01.png)

    c. Válassza ki a következő engedélyeket a listából, és kattintson a **módosítási hozzáférést**, ahogy az alábbi ábrán látható.

    ![Graph-konfiguráció](./media/alibaba-cloud-service-role-based-sso-tutorial/graph02.png)

    >[!NOTE]
    >Engedélyek után jelentkezzen be a Graph Explorer újra.

    d. A Graph Explorer lapon válassza ki a **első** első legördülő listájából és **béta** a második legördülő listából. Majd adja meg `https://graph.microsoft.com/beta/servicePrincipals` melletti legördülő listák, majd kattintson a mező **lekérdezés futtatása**.

    ![Graph-konfiguráció](./media/alibaba-cloud-service-role-based-sso-tutorial/graph03.png)

    >[!NOTE]
    >Ha több címtárat használ, akkor megadhatja `https://graph.microsoft.com/beta/contoso.com/servicePrincipals` a lekérdezés a mezőben.

    e. Az a **válasz előzetes** területén bontsa ki a appRoles tulajdonság az a szolgáltatásnév későbbi használatra.

    ![Graph-konfiguráció](./media/alibaba-cloud-service-role-based-sso-tutorial/graph05.png)

    >[!NOTE]
    >A appRoles tulajdonság megadásával keresheti `https://graph.microsoft.com/beta/servicePrincipals/<objectID>` a lekérdezés a mezőben. Vegye figyelembe, hogy a `objectID` az objektumazonosító, az Azure AD-ből másolta **tulajdonságok** lapot.

    f. Lépjen vissza a Graph Explorer, a módszert a **első** való **javítására**, illessze be a következő tartalmat a **kérelem törzse** szakaszt, és kattintson a **lekérdezés futtatása** :
    ```
    { 
    "appRoles": [
        { 
        "allowedMemberTypes":[
            "User"
        ],
        "description": "msiam_access",
        "displayName": "msiam_access",
        "id": "41be2db8-48d9-4277-8e86-f6d22d35****",
        "isEnabled": true,
        "origin": "Application",
        "value": null
        },
        { "allowedMemberTypes": [
            "User"
        ],
        "description": "Admin,AzureADProd",
        "displayName": "Admin,AzureADProd",
        "id": "68adae10-8b6b-47e6-9142-6476078cdbce",
        "isEnabled": true,
        "origin": "ServicePrincipal",
        "value": "acs:ram::187125022722****:role/aadrole,acs:ram::187125022722****:saml-provider/AAD"
        }
    ]
    }
    ```
    > [!NOTE]
    > A `value` az identitásszolgáltató és a RAM-konzolon létrehozott szerepkört a ARNs van. Itt szükség szerint több szerepkört is hozzáadhat. Az Azure AD elküldi ezeket a szerepköröket értékét az SAML-válasz a jogcím értéke. Azonban csak akkor adhat hozzá új szerepkörök után a `msiam_access` részben a patch művelethez. A létrehozási folyamat zökkenőmentes, azt javasoljuk, hogy használja-e egy azonosító generátor GUID-generátort, például valós időben azonosítókat létrehozni.

    g. Miután a szolgáltatásnév a szükséges szerepkör-javított tartományvezérlőről érkezett, rendelje hozzá a szerepkört az Azure AD-felhasználóval (u2) lépéseit követve **hozzárendelése az Azure ad-ben tesztfelhasználó** az oktatóanyag szakaszában.

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

    a. Az a **neve** írja be a következőt **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be a `brittasimon@yourcompanydomain.extension`. Például: BrittaSimon@contoso.com

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

5. Az a **felhasználók és csoportok** lapra, válassza ki a u2 a felhasználó listából, majd kattintson **kiválasztása**. Kattintson a **hozzárendelése**.

    ![Teszt konfigurálása](./media/alibaba-cloud-service-role-based-sso-tutorial/test01.png)

6. A hozzárendelt szerepkör megtekintéséhez, és tesztelje az Alibaba Felhőszolgáltatás (Role-based SSO).

    ![Teszt konfigurálása](./media/alibaba-cloud-service-role-based-sso-tutorial/test02.png)

    >[!NOTE]
    >Miután a felhasználó (u2), a létrehozott szerepkör automatikusan csatlakozik a felhasználó. Ha több szerepkör létrehozott, rendelje hozzá a megfelelő szerepkört a felhasználó igény szerint szeretné. Ha azt szeretné, szerepkör-alapú egyszeri bejelentkezés az Azure ad-ből több Alibaba Felhőbeli fiókok megvalósításához, ismételje meg a fenti lépéseket.

### <a name="create-alibaba-cloud-service-role-based-sso-test-user"></a>Az Alibaba Felhőszolgáltatás (Role-based SSO) tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű Alibaba felhőszolgáltatásban (Role-based SSO) felhasználói hoz létre. Együttműködve [Alibaba Felhőszolgáltatás (Role-based SSO) támogatási csoportjának](https://www.aliyun.com/service/) a felhasználók hozzáadása az Alibaba Felhőszolgáltatás (Role-based SSO) platform. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata 

A fenti konfigurációk elvégzése után tesztelje az Alibaba Felhőszolgáltatás (Role-based SSO) az alábbi lépéseket:

1. Az Azure Portalon nyissa meg a **Alibaba Felhőszolgáltatás (Role-based SSO)** lapon válassza **egyszeri bejelentkezési**, és kattintson a **teszt**.

    ![Teszt konfigurálása](./media/alibaba-cloud-service-role-based-sso-tutorial/test03.png)

2. Kattintson a **Bejelentkezés az aktuális felhasználóként** elemre.

    ![Teszt konfigurálása](./media/alibaba-cloud-service-role-based-sso-tutorial/test04.png)

3. A fiók kiválasztása lapon válassza ki a u2.

    ![Teszt konfigurálása](./media/alibaba-cloud-service-role-based-sso-tutorial/test05.png)

4. A következő oldal jelenik meg, jelezve, hogy a szerepkör-alapú egyszeri bejelentkezés sikeres.

    ![Teszt konfigurálása](./media/alibaba-cloud-service-role-based-sso-tutorial/test06.png)

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


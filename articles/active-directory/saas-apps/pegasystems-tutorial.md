---
title: 'Oktatóanyag: Az Azure Active Directory-integráció Pega rendszerekkel |} A Microsoft Docs'
description: Ebben az oktatóanyagban elsajátíthatja fog konfigurálása egyszeri bejelentkezéshez az Azure Active Directory és Pega rendszerek között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 31acf80f-1f4b-41f1-956f-a9fbae77ee69
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 195e7bf21fe1f6017705883f2ec692c182f15375
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/13/2019
ms.locfileid: "65560597"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>Oktatóanyag: Az Azure Active Directory-integráció Pega rendszerekkel

Ebben az oktatóanyagban megismerheti, hogyan Pega rendszerek integrálását az Azure Active Directory (Azure AD) lesz.

Ez az integráció ezeket az előnyöket biztosítja:

* Az Azure AD-szabályozza, ki férhet hozzá Pega rendszerek is használhatja.
* Engedélyezheti a felhasználóknak kell automatikus bejelentkezésre Pega rendszerekhez (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen kezelheti: az Azure Portalon.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [egyszeri bejelentkezés alkalmazásokhoz az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása Pega rendszerekkel, szüksége lesz:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, akkor regisztráljon egy [egy havi próbalehetőség](https://azure.microsoft.com/pricing/free-trial/).
* Pega rendszerek előfizetés, amely egyszeri bejelentkezés engedélyezve van.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban fogja konfigurálni, és egy tesztelési környezetben az Azure AD egyszeri bejelentkezés tesztelése.

* Pega Systems supports SP-initiated and IdP-initiated SSO.

## <a name="add-pega-systems-from-the-gallery"></a>Pega rendszerek hozzáadása a katalógusból

Pega rendszerek integrálását az Azure AD beállításához, hozzá kell Pega rendszerek a galériából a felügyelt SaaS-alkalmazások listájára.

1. Az a [az Azure portal](https://portal.azure.com), a bal oldali panelen válassza ki a **Azure Active Directory**:

    ![Válassza az Azure Active Directory elemet.](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások** > **minden alkalmazás**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Egy alkalmazás hozzáadásához válassza **új alkalmazás** az ablak tetején:

    ![Válassza ki az új alkalmazás](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Pega rendszerek**. Válassza ki **Pega rendszerek** a keresési eredmények, és válassza ki a **Hozzáadás**.

     ![Keresési eredmények](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban fog konfigurálása és tesztelése az Azure AD egyszeri bejelentkezés Pega rendszerekkel Britta Simon nevű tesztfelhasználó használatával.
Egyszeri bejelentkezés engedélyezéséhez szüksége Pega rendszerekben egy Azure AD-felhasználót és a megfelelő felhasználó közötti kapcsolat létrehozásához.

Konfigurálja és az Azure AD egyszeri bejelentkezés Pega rendszerekkel való teszteléséhez szüksége a lépések elvégzéséhez:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  a felhasználók számára a funkció engedélyezéséhez.
2. **[Pega rendszerek egyszeri bejelentkezés konfigurálása](#configure-pega-systems-single-sign-on)**  az alkalmazás oldalán.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  az Azure AD egyszeri bejelentkezés teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  ahhoz, hogy az Azure AD egyszeri bejelentkezés a felhasználó számára.
5. **[Hozzon létre egy Pega rendszerek tesztfelhasználót](#create-a-pega-systems-test-user)**  , amely kapcsolódik a felhasználó Azure ad-ben ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  , hogy működik-e a konfiguráció ellenőrzéséhez.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Az Azure AD egyszeri bejelentkezés konfigurálása Pega rendszerekkel, hajtsa végre ezeket a lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **Pega rendszerek** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**:

    ![Válassza ki az egyszeri bejelentkezés](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válasszon **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése:

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** lapon válassza ki a **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel:

    ![Szerkesztés ikon](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** párbeszédpanel, ha azt szeretné, konfigurálja az alkalmazás identitásszolgáltató által kezdeményezett módban, a következő lépéseket.

    ![Alapszintű SAML-konfigurációja párbeszédpanel](common/idp-intiated.png)

    1. Az a **azonosító** mezőbe írjon be egy URL-címe ebben a mintában:

       `https://<customername>.pegacloud.io:443/prweb/sp/<instanceID>`

    1. Az a **válasz URL-cím** mezőbe írjon be egy URL-címe ebben a mintában:

       `https://<customername>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

5. Ha az alkalmazás konfigurálása a Szolgáltató által kezdeményezett módban szeretne, válassza ki a **további URL-címet beállítani** , és kövesse az alábbi lépéseket.

    ![Pega rendszerek tartomány és URL-címeket egyetlen bejelentkezési adatait](common/both-advanced-urls.png)

    1. Az a **bejelentkezési URL-cím** mezőbe írja be a bejelentkezési URL-Címének értékét.

    1. Az a **továbbítási állapot** mezőbe írjon be egy URL-címe ebben a mintában: `https://<customername>.pegacloud.io/prweb/sso`

    > [!NOTE]
    > Az itt megadott értékek helyőrzőket. Szeretne használni a tényleges azonosítója, válasz URL-címe, jelentkezzen be, és továbbítási állapot URL-címe. Az azonosító lekérése, és a válasz URL-címértékekre Pega-alkalmazásból, az oktatóanyag későbbi részében leírtak. A relay állapotérték lekéréséhez lépjen kapcsolatba a [Pega rendszerek támogatási csoportjának](https://www.pega.com/contact-us). Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

6. A Pega rendszerek alkalmazásnak kell lennie egy meghatározott formátumban a SAML helyességi feltételek. A megfelelő formátumban használhassa őket, hozzá kell egyéni attribútum-leképezéshez SAML-jogkivonat attribútumai konfigurációján. Az alábbi képernyőfelvételen az alapértelmezett attribútumok. Válassza ki a **szerkesztése** ikonra kattintva nyissa meg a **felhasználói attribútumok** párbeszédpanel:

    ![Felhasználói attribútumok](common/edit-attribute.png)

7. Az attribútumok az előző képernyőképen látható, felül Pega rendszerekhez szükséges az alkalmazás számára néhány további attribútumok vissza a SAML-válasz átadni. Az a **felhasználói jogcímek** szakaszában a **felhasználói attribútumok** párbeszédpanel mezőbe a SAML-jogkivonat attribútumai hozzáadása a következő lépéseket:

    
   - `uid`
   - `cn`
   - `mail`
   - `accessgroup`  
   - `organization`  
   - `orgdivision`
   - `orgunit`
   - `workgroup`  
   - `Phone`

    > [!NOTE]
    > Ezeket az értékeket a szervezet jellemzőek. Adja meg a megfelelő értékekre.

    1. Válassza ki **hozzáadása új jogcímet** megnyitásához a **kezelheti a felhasználói jogcímek** párbeszédpanel:

    ![Válassza ki az új jogcímet hozzáadása](common/new-save-attribute.png)

    ![Felhasználói jogcímek párbeszédpanel kezelése](common/new-attribute-details.png)

    1. Az a **neve** mezőben megadhatja azon attribútum nevét, a sorhoz látható.

    1. Hagyja a **Namespace** mező üres.

    1. Az a **forrás**válassza **attribútum**.

    1. Az a **forrásattribútum** listájához, válassza ki az adott sorhoz feltüntetett attribútum értéke.

    1. Kattintson az **OK** gombra.

    1. Kattintson a **Mentés** gombra.

8. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** szakaszban jelölje be a **letöltése** mellett kapcsolni **összevonási metaadatainak XML** , a igényeknek, és mentse el a tanúsítványt a számítógépen:

    ![Tanúsítvány letöltésére szolgáló hivatkozásra.](common/metadataxml.png)

9. Az a **Pega rendszerek beállítása** területén másolja a megfelelő URL-címeket, a követelmények alapján.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    1. **Bejelentkezési URL-cím**.

    1. **Az Azure AD-azonosító**.

    1. **Kijelentkezési URL-címe**.

### <a name="configure-pega-systems-single-sign-on"></a>Pega rendszerek egyszeri bejelentkezés konfigurálása

1. Az egyszeri bejelentkezés konfigurálásához a **Pega rendszerek** oldalán, a rendszergazdai fiók egy másik böngészőablakban jelentkezzen be a Pega portálra.

2. Válassza ki **létrehozása** > **SysAdmin (rendszergazda)** > **hitelesítési szolgáltatás**:

    ![Válassza ki a hitelesítési szolgáltatás](./media/pegasystems-tutorial/tutorial_pegasystems_admin.png)
    
3. A következő lépéseket a **hitelesítési szolgáltatás-létrehozás** képernyő.

    ![Hitelesítési szolgáltatás képernyő létrehozása](./media/pegasystems-tutorial/tutorial_pegasystems_admin1.png)

    1. Az a **típus** listáról válassza ki **SAML 2.0**.

    1. Az a **neve** mezőbe írjon be egy tetszőleges nevet (például **Azure AD SSO**).

    1. Az a **rövid leírás** mezőbe írja be egy leírást.  

    1. Válassza ki **létrehozása és a nyílt**.
    
4. Az a **identitásszolgáltató (IdP) információt** szakaszban jelölje be **importálás IdP-metaadatok** , és keresse meg az Azure-portálról letöltött metaadatait tartalmazó fájl. Kattintson a **küldés** a metaadatok betöltése:

    ![Identity Provider (IdP) szakasza](./media/pegasystems-tutorial/tutorial_pegasystems_admin2.png)
    
    Az importálás az IdP-adat fel itt látható módon:

    ![Imported IdP data](./media/pegasystems-tutorial/tutorial_pegasystems_admin3.png)
    
6. Befejeződött a következő lépéseket a **Service Provider (SP) beállítások** szakaszban.

    ![Szolgáltatás-szolgáltató beállításai](./media/pegasystems-tutorial/tutorial_pegasystems_admin4.png)

    1. Másolás a **entitás azonosító** értékét, és illessze be azt a **azonosító** párbeszédpanel a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

    1. Másolás a **helyességi feltétel fogyasztói Service (ACS) helyen** értékét, és illessze be azt a **válasz URL-cím** párbeszédpanel a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

    1. Válassza ki **tiltsa le a kérelem-aláírás**.

7. Kattintson a **Mentés** gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban az Azure Portalon Britta Simon nevű tesztfelhasználó fog létrehozni.

1. Az Azure Portalon válassza ki a **Azure Active Directory** a bal oldali panelen válassza ki a **felhasználók**, majd válassza ki **minden felhasználó**:

    ![Válassza ki az összes felhasználó](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején:

    ![Válassza ki az új felhasználó](common/new-user.png)

3. Az a **felhasználói** párbeszédpanelen töltse ki az alábbi lépéseket.

    ![Felhasználói párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be **brittasimon @\<vállalati_tartomány >.\< bővítmény >**. (Például BrittaSimon@contoso.com.)

    c. Válassza ki **Show jelszó**, és jegyezze fel az értéket, amely szerepel a **jelszó** mezőbe.

    d. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban Britta Simon Pega rendszerek számára a hozzáférés biztosításával az Azure egyszeri bejelentkezés használatához engedélyeznie kell.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd válassza ki **Pega rendszerek**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Pega rendszerek**.

    ![Alkalmazások listája](common/all-applications.png)

3. A bal oldali panelen válassza ki a **felhasználók és csoportok**:

    ![Felhasználók és csoportok kiválasztása](common/users-groups-blade.png)

4. Válassza ki **felhasználó hozzáadása**, majd válassza ki **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel bezárásához.

    ![Felhasználó hozzáadása kiválasztása](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza ki **Britta Simon** a felhasználók listában, és kattintson a **válassza** gombra a képernyő alján.

6. Ha a SAML-előfeltétel szerepkör értéket a várt a **Szerepkörválasztás** párbeszédpanelen jelölje ki a megfelelő szerepkört a felhasználóhoz a listából. Kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen jelölje ki **hozzárendelése**.

### <a name="create-a-pega-systems-test-user"></a>Pega rendszerek tesztfelhasználó létrehozása

Ezután szüksége Britta Simon Pega rendszerekben nevű felhasználó létrehozásához. Együttműködik a [Pega rendszerek támogatási csoportjának](https://www.pega.com/contact-us) a felhasználók létrehozásához.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Most szüksége az Azure AD egyszeri bejelentkezési konfigurációjának tesztelése a hozzáférési Panel használatával.

A Pega rendszerek csempe kiválasztásakor a hozzáférési panelen azt kell automatikusan megtörténik a a Pega rendszerek példányhoz, amelyhez beállítani az egyszeri bejelentkezés Konfigurálásához. További információkért lásd: [alkalmazások használatának és elérésének a saját alkalmazások portál](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory számára oktatóanyagokkal](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
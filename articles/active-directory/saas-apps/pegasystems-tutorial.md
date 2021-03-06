---
title: 'Oktatóanyag: Azure Active Directory integráció a Pega-rendszerekkel | Microsoft Docs'
description: Ebből az oktatóanyagból megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Pega rendszerek között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 4fb117b7f7b9a0c7a6a67e2714380a01cd53a4e0
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2020
ms.locfileid: "92515642"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>Oktatóanyag: Azure Active Directory integráció Pega rendszerekkel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Pega rendszereket Azure Active Directory (Azure AD) használatával.

Ez az integráció az alábbi előnyöket biztosítja:

* Az Azure AD segítségével szabályozhatja, hogy ki férhet hozzá a Pega rendszerekhez.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Pega rendszerekre (egyszeri bejelentkezés) az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti: a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg az [egyszeri bejelentkezést a Azure Active Directory alkalmazásaihoz](../manage-apps/what-is-single-sign-on.md)című témakört.

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció Pega-rendszerekkel való konfigurálásához a következők szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, regisztrálhat egy [hónapos próbaverzióra](https://azure.microsoft.com/pricing/free-trial/).
* Az egyszeri bejelentkezést engedélyező Pega-rendszerek előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Pega rendszerek támogatják az SP által kezdeményezett és a identitásszolgáltató által kezdeményezett egyszeri bejelentkezést.

## <a name="add-pega-systems-from-the-gallery"></a>Pega-rendszerek hozzáadása a gyűjteményből

A Pega rendszerek Azure AD-be való integrálásának beállításához hozzá kell adnia az Pega-rendszereket a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. A [Azure Portal](https://portal.azure.com)a bal oldali ablaktáblán válassza a **Azure Active Directory**:

    ![Válassza az Azure Active Directory elemet.](common/select-azuread.png)

2. Nyissa meg a **vállalati alkalmazások**  >  **minden alkalmazás**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

3. Alkalmazás hozzáadásához válassza az ablak tetején található **új alkalmazás** elemet:

    ![Új alkalmazás kiválasztása](common/add-new-app.png)

4. A keresőmezőbe írja be a **Pega rendszerek**kifejezést. Válassza ki a **Pega rendszereket** a keresési eredmények között, majd válassza a **Hozzáadás**lehetőséget.

     ![Keresési eredmények](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálhatja és tesztelheti a Pega-rendszerekkel egy Britta Simon nevű teszt felhasználó használatával.
Az egyszeri bejelentkezés engedélyezéséhez létre kell hoznia egy kapcsolatot az Azure AD-felhasználó és a megfelelő felhasználó között a Pega-rendszerekben.

Az Azure AD egyszeri bejelentkezés Pega-rendszerekkel való konfigurálásához és teszteléséhez a következő lépéseket kell elvégeznie:

1. **[Konfigurálja az Azure ad egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** , hogy engedélyezze a szolgáltatást a felhasználók számára.
2. **[Konfigurálja a Pega rendszerek egyszeri bejelentkezését](#configure-pega-systems-single-sign-on)** az alkalmazás oldalán.
3. **[Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user)** az Azure ad egyszeri bejelentkezés teszteléséhez.
4. **[Az Azure ad-teszt felhasználójának hozzárendelésével](#assign-the-azure-ad-test-user)** engedélyezheti az Azure ad egyszeri bejelentkezést a felhasználó számára.
5. **[Hozzon létre egy Pega-Rendszerteszt felhasználót](#create-a-pega-systems-test-user)** , amely a felhasználó Azure ad-képviseletéhez van társítva.
6. Az **[egyszeri bejelentkezés tesztelésével](#test-single-sign-on)** ellenőrizheti, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezést fogja engedélyezni a Azure Portal.

Az Azure AD egyszeri bejelentkezés Pega-rendszerekkel való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)a **Pega Systems** Application Integration lapon válassza az **egyszeri bejelentkezés**lehetőséget:

    ![Egyszeri bejelentkezés kiválasztása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez:

    ![Egyszeri bejelentkezési módszer kiválasztása](common/select-saml-option.png)

3. Az **egyszeres Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához:

    ![Szerkesztés ikon](common/edit-urls.png)

4. Ha az **alapszintű SAML-konfiguráció** párbeszédpanelen szeretné konfigurálni az alkalmazást identitásszolgáltató módban, hajtsa végre az alábbi lépéseket.

    ![Alapszintű SAML-konfiguráció párbeszédpanel](common/idp-intiated.png)

    1. Az **azonosító** mezőbe írjon be egy URL-címet ebben a mintában:

       `https://<customername>.pegacloud.io:443/prweb/sp/<instanceID>`

    1. A **Válasz URL-címe** mezőbe írjon be egy URL-címet ebben a mintában:

       `https://<customername>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

5. Ha az alkalmazást SP-kezdeményezésű módban szeretné konfigurálni, válassza a **további URL-címek beállítása** lehetőséget, és végezze el a következő lépéseket.

    ![Pega rendszerek tartomány és URL-címek egyszeri bejelentkezési adatai](common/both-advanced-urls.png)

    1. A **bejelentkezési URL-cím** mezőbe írja be a bejelentkezési URL-cím értékét.

    1. A **továbbítási állapot** mezőben adja meg az URL-címet ebben a mintában: `https://<customername>.pegacloud.io/prweb/sso`

    > [!NOTE]
    > Az itt megadott értékek helyőrzők. A tényleges azonosítót, a válasz URL-címét, a bejelentkezési URL-címet és a továbbítási állapot URL-címét kell használnia. Az Pega alkalmazásban az azonosító és a válasz URL-értékei az oktatóanyag későbbi részében leírtak szerint olvashatók be. A továbbítási állapot értékének lekéréséhez forduljon a [Pega Systems támogatási csapatához](https://www.pega.com/contact-us). Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

6. A Pega rendszerbeli alkalmazásnak az SAML-kijelentéseknek adott formátumban kell lennie. A megfelelő formátumban való lekéréshez egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőfelvételen az alapértelmezett attribútumok láthatók. Kattintson a **Szerkesztés** ikonra a **felhasználói attribútumok** párbeszédpanel megnyitásához:

    ![Felhasználói attribútumok](common/edit-attribute.png)

7. Az előző képernyőképen látható attribútumok mellett a Pega Systems alkalmazásnak néhány további attribútumot is meg kell adni az SAML-válaszban. A **felhasználói attribútumok** párbeszédpanel **felhasználói jogcímek** szakaszában adja meg az alábbi lépéseket az SAML-jogkivonat attribútumainak hozzáadásához:

    
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
    > Ezek az értékek a szervezetre jellemzőek. Adja meg a megfelelő értékeket.

    1. Válassza az **új jogcím hozzáadása** elemet a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához:

    ![Válassza az új jogcím hozzáadása lehetőséget](common/new-save-attribute.png)

    ![Felhasználói jogcímek kezelése párbeszédpanel](common/new-attribute-details.png)

    1. A **név** mezőbe írja be az adott sorhoz megjelenített attribútum nevét.

    1. Hagyja üresen a **névtér** mezőt.

    1. A **forrás**mezőben válassza az **attribútum**lehetőséget.

    1. A **forrás attribútum** listáról válassza ki az adott sorhoz megjelenő attribútum értékét.

    1. Kattintson az **OK** gombra.

    1. Válassza a **Mentés** lehetőséget.

8. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban válassza ki a **letöltési** hivatkozást az **összevonási metaadatok XML-fájlja**mellett, a követelmények szerint, és mentse a tanúsítványt a számítógépen:

    ![Tanúsítvány letöltési hivatkozása](common/metadataxml.png)

9. A **Pega rendszerek beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

    ![A konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    1. **Bejelentkezési URL-cím**.

    1. **Azure ad-azonosító**.

    1. **Kijelentkezési URL-cím**.

### <a name="configure-pega-systems-single-sign-on"></a>Pega rendszerek egyszeri bejelentkezésének konfigurálása

1. Ha az egyszeri bejelentkezést a Pega- **rendszerek** oldalon szeretné konfigurálni, jelentkezzen be a Pega-portálra egy másik böngészőablakban található rendszergazdai fiókkal.

2. Válassza **Create**a  >  **sysadmin**  >  **Authentication szolgáltatás**létrehozása lehetőséget:

    ![Hitelesítési szolgáltatás kiválasztása](./media/pegasystems-tutorial/tutorial_pegasystems_admin.png)
    
3. Hajtsa végre a következő lépéseket a **hitelesítési szolgáltatás létrehozása** képernyőn.

    ![Hitelesítési szolgáltatás létrehozása képernyő](./media/pegasystems-tutorial/tutorial_pegasystems_admin1.png)

    1. A **típus** listában válassza az **SAML 2,0**elemet.

    1. A **név** mezőbe írja be a tetszőleges nevet (például: **Azure ad SSO**).

    1. A **rövid leírás** mezőbe írja be a leírást.  

    1. Válassza **a létrehozás és Megnyitás**elemet.
    
4. Az **Identity Provider (identitásszolgáltató) adatai** szakaszban válassza a **identitásszolgáltató-Metaadatok importálása** lehetőséget, és keresse meg a Azure Portal letöltött metaadat-fájlt. A metaadatok betöltéséhez kattintson a **Submit (Küldés** ) gombra:

    ![Identitáskezelés (identitásszolgáltató) – információ szakasz](./media/pegasystems-tutorial/tutorial_pegasystems_admin2.png)
    
    Az Importálás az itt látható módon tölti fel a identitásszolgáltató-adatokat:

    ![Importált identitásszolgáltató-adatértékek](./media/pegasystems-tutorial/tutorial_pegasystems_admin3.png)
    
6. Hajtsa végre a következő lépéseket a **szolgáltatói (SP) beállítások** szakaszban.

    ![Szolgáltatói beállítások](./media/pegasystems-tutorial/tutorial_pegasystems_admin4.png)

    1. Másolja az **entitás-azonosító** értéket, és illessze be a Azure Portal **ALAPszintű SAML-konfiguráció** szakaszának **azonosító** mezőjébe.

    1. Másolja ki az **állítási szolgáltatás (ACS) helyének** értékét, és illessze be a **Válasz URL-cím** mezőbe a Azure Portal **alapszintű SAML-konfiguráció** szakaszában.

    1. Jelölje be a **kérelmek aláírásának letiltása**jelölőnégyzetet.

7. Válassza a **Mentés** lehetőséget.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy Britta Simon nevű teszt felhasználót hoz létre a Azure Portal.

1. A Azure Portal a bal oldali ablaktáblán válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**lehetőséget, majd válassza a **minden felhasználó**lehetőséget:

    ![Válassza a Minden felhasználó lehetőséget](common/users.png)

2. Válassza ki a képernyő felső részén található **új felhasználó** elemet:

    ![Új felhasználó kiválasztása](common/new-user.png)

3. A **felhasználó** párbeszédpanelen hajtsa végre az alábbi lépéseket.

    ![Felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A **Felhasználónév** mezőbe írja be a **brittasimon@ \<yourcompanydomain> . \<extension> **. (Például: BrittaSimon@contoso.com .)

    c. Válassza a **jelszó megjelenítése**lehetőséget, majd írja le a **jelszó** mezőben található értéket.

    d. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít a Pega rendszerekhez.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **Pega rendszerek**elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Pega rendszerek**elemet.

    ![Alkalmazások listája](common/all-applications.png)

3. A bal oldali ablaktáblán válassza a **felhasználók és csoportok**lehetőséget:

    ![Felhasználók és csoportok kiválasztása](common/users-groups-blade.png)

4. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![Felhasználó hozzáadása kiválasztása](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-állításban a szerepkör értéke várható, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából. Kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelés**lehetőséget.

### <a name="create-a-pega-systems-test-user"></a>Pega-rendszerek tesztelésére szolgáló felhasználó létrehozása

Ezután létre kell hoznia egy Britta Simon nevű felhasználót a Pega Systems-ben. A felhasználók létrehozásához működjön együtt a [Pega Systems támogatási csapatával](https://www.pega.com/contact-us) .

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Most az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panel használatával kell tesztelni.

Amikor kiválasztja a Pega-rendszerek csempét a hozzáférési panelen, automatikusan be kell jelentkeznie a Pega rendszer-példányba, amelyhez be kell állítania az egyszeri bejelentkezést. További információ: [alkalmazások elérése és használata a saját alkalmazások portálon](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Oktatóanyagok SaaS-alkalmazások az Azure Active Directoryval való integrálásához](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)
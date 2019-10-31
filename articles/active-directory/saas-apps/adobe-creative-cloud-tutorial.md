---
title: 'Oktatóanyag: Azure Active Directory integráció a Adobe Creative Cloudtal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Adobe Creative Cloud között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: c199073f-02ce-45c2-b515-8285d4bbbca2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f3fdd1aa4fa655cfffb9e9a2f29d551133613a8
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73154012"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-creative-cloud"></a>Oktatóanyag: Azure Active Directory integráció a Adobe Creative Cloud

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Adobe Creative Cloudt Azure Active Directory (Azure AD) használatával.
A Adobe Creative Cloud az Azure AD-vel való integrálása a következő előnyöket biztosítja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá Adobe Creative Cloudhoz.
* Engedélyezheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Adobe Creative Cloudba (egyszeri bejelentkezés) az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció Adobe Creative Cloudval való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* Adobe Creative Cloud egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* Adobe Creative Cloud támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-adobe-creative-cloud-from-the-gallery"></a>Adobe Creative Cloud hozzáadása a gyűjteményből

Adobe Creative Cloud az Azure AD-be való integrálásának konfigurálásához hozzá kell adnia Adobe Creative Cloud a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha Adobe Creative Cloud szeretne hozzáadni a katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Adobe Creative Cloud**kifejezést, válassza a **Adobe Creative Cloud** az eredmények paneljén, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Adobe Creative Cloud az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés konfigurálását és tesztelését Adobe Creative Cloud a **Britta Simon**nevű tesztelési felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a Adobe Creative Cloud kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés Adobe Creative Cloud használatával történő konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Adobe Creative Cloud egyszeri bejelentkezés konfigurálása](#configure-adobe-creative-cloud-single-sign-on)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Hozzon létre Adobe Creative Cloud test User](#create-adobe-creative-cloud-test-user)** -t, hogy a Britta Simon partnere legyen a Adobe Creative Cloud, amely a felhasználó Azure ad-képviseletéhez van csatolva.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés Adobe Creative Cloud használatával történő konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **Adobe Creative Cloud** alkalmazás-integráció lapon válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![A tartomány és az URL-címek egyszeri bejelentkezési adatainak Adobe Creative Cloud](common/sp-identifier.png)

    a. A **bejelentkezési URL** szövegmezőbe írja be az értéket a következőre: `https://adobe.com`.

    b. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://www.okta.com/saml2/service-provider/<token>`

    > [!NOTE]
    > Az azonosító értéke nem valódi. Frissítse ezt az értéket a tényleges azonosítóval. Az érték beszerzéséhez vegye fel a kapcsolatot [Adobe Creative Cloud ügyfél-támogatási csapattal](https://www.adobe.com/au/creativecloud/business/teams/plans.html) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Adobe Creative Cloud alkalmazás megadott formátumban várja az SAML-kijelentéseket. Konfigurálja a következő jogcímeket ehhez az alkalmazáshoz. Az attribútumok értékeit az alkalmazás-integráció lapon, a **felhasználói attribútumok** szakaszban kezelheti. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** gombra a **felhasználói attribútumok** párbeszédpanel megnyitásához.

    ![image](common/edit-attribute.png)

6. A **felhasználó attribútumai** párbeszédpanel **felhasználói jogcímek** szakaszában konfigurálja az SAML-jogkivonat attribútumot a fenti képen látható módon, és hajtsa végre a következő lépéseket:

    | Név | Forrás attribútum|
    |----- | --------- |
    | firstName | User. givenName |
    | lastName | felhasználó. vezetéknév |
    | E-mail cím | User. mail

    a. Kattintson az **új jogcím hozzáadása** elemre a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. A **név** szövegmezőbe írja be az adott sorhoz megjelenített attribútum nevét.

    c. Hagyja üresen a **névteret** .

    d. Válassza a forrás **attribútumként**lehetőséget.

    e. A **forrás attribútum** listáról írja be az adott sorhoz megjelenő attribútum értékét.

    f. Kattintson **az OK** gombra

    g. Kattintson a **Save** (Mentés) gombra.

    > [!NOTE]
    > A felhasználóknak érvényes Office 365 ExO-licenccel kell rendelkezniük az SAML-válaszban az e-mail-jogcím értékének kitöltéséhez.

7. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

8. A **Adobe Creative Cloud beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL-cím

    b. Azure ad-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-adobe-creative-cloud-single-sign-on"></a>Adobe Creative Cloud egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként az [Adobe felügyeleti konzolra](https://adminconsole.adobe.com) .

2. A felső navigációs sávon kattintson a **Beállítások** elemre, majd válassza az **identitás**elemet. Megnyílik a tartományok listája. Kattintson a kapcsolat **konfigurálása** a tartományhoz lehetőségre. Ezután hajtsa végre az alábbi lépéseket az **egyszeri bejelentkezés konfigurálásához szükséges** szakaszban. További információ: [tartomány beállítása](https://helpx.adobe.com/enterprise/using/set-up-domain.html)

    ![Beállítások](https://helpx.adobe.com/content/dam/help/en/enterprise/using/configure-microsoft-azure-with-adobe-sso/_jcr_content/main-pars/procedure_719391630/proc_par/step_3/step_par/image/edit-sso-configuration.png "Beállítások")

    a. Kattintson a **Tallózás** gombra a letöltött tanúsítvány az Azure ad-ből **identitásszolgáltató-tanúsítványba**való feltöltéséhez.

    b. A **identitásszolgáltató kiállító** szövegmezőbe írja be az **SAML-entitás azonosítójának** értékét, amelyet a Azure Portal-ben a **Bejelentkezés konfigurálása** szakaszban másolt.

    c. A **identitásszolgáltató bejelentkezési URL-címe** szövegmezőbe írja be az **SAML SSO szolgáltatás URL-címének** értékét, amelyet a Azure Portal-ben a **Bejelentkezés konfigurálása** szakaszban másolt.

    d. Válassza a **http-átirányítás identitásszolgáltató-** **Kötésként**lehetőséget.

    e. Válassza ki az **E-mail címet** **felhasználói bejelentkezési beállításként**.

    f. Kattintson a **Save (Mentés** ) gombra.

3. Az irányítópult mostantól a **"metaadatok letöltése"** fájlt fogja bemutatni. Az Adobe EntityDescriptor URL-címét és AssertionConsumerService URL-címét tartalmazza. Nyissa meg a fájlt, és konfigurálja őket az Azure AD-alkalmazásban.

    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalán](./media/adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. Használja az EntityDescriptor értéket az Adobe által megadott **azonosítóhoz** az **Alkalmazásbeállítások konfigurálása** párbeszédpanelen.

    b. Használja az AssertionConsumerService értéket az Adobe által megadott **Válasz URL-címére** az **Alkalmazásbeállítások konfigurálása** párbeszédpanelen.

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

Ebben a szakaszban a Britta Simon használatával engedélyezi az Azure egyszeri bejelentkezést az Adobe Creative Cloud elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **Adobe Creative Cloud**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Adobe Creative Cloud**lehetőséget.

    ![Az alkalmazások lista Adobe Creative Cloud hivatkozása](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-adobe-creative-cloud-test-user"></a>Adobe Creative Cloud tesztelési felhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezzenek a Adobe Creative Cloudba, Adobe Creative Cloud kell kiépíteni őket. Adobe Creative Cloud esetén a kiépítés manuális feladat.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Felhasználói fiókok kiépítéséhez hajtsa végre a következő lépéseket:

1. Jelentkezzen be rendszergazdaként az [Adobe felügyeleti konzol](https://adminconsole.adobe.com) webhelyére.

2. Vegye fel a felhasználót az Adobe konzolján összevont AZONOSÍTÓként, és rendelje hozzá egy termék profiljához. A felhasználók hozzáadásával kapcsolatos részletes információkért lásd: [felhasználók hozzáadása az Adobe felügyeleti konzolon](https://helpx.adobe.com/enterprise/using/users.html#Addusers) . 

3. Ezen a ponton írja be az e-mail-címét/UPN-t az Adobe bejelentkezési űrlapba, nyomja le a TAB billentyűt, és az Azure AD-be való összevonáshoz:
   * Webes elérés: www\.adobe.com > bejelentkezés
   * Az asztali alkalmazás segédprogram > bejelentkezés
   * Az alkalmazáson belül > Súgó > bejelentkezéshez

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Adobe Creative Cloud csempére kattint, automatikusan be kell jelentkeznie arra a Adobe Creative Cloudra, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
  
- [Tartomány beállítása (adobe.com)](https://helpx.adobe.com/enterprise/using/set-up-domain.html)
  
- [Az Azure konfigurálása az Adobe SSO (adobe.com) szolgáltatással való használatra](https://helpx.adobe.com/enterprise/kb/configure-microsoft-azure-with-adobe-sso.html)

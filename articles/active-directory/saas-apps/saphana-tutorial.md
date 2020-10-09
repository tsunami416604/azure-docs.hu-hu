---
title: 'Oktatóanyag: Azure Active Directory integráció a SAP HANAtal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és SAP HANA között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.openlocfilehash: fc3b5b096e339d0c0f4bca7afc92d7315246fcd4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88552288"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>Oktatóanyag: Azure Active Directory integráció a SAP HANA

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a SAP HANAt Azure Active Directory (Azure AD) használatával.
A SAP HANA az Azure AD-vel való integrálása a következő előnyöket biztosítja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá SAP HANAhoz.
* Engedélyezheti a felhasználók számára, hogy automatikusan bejelentkezzenek a SAP HANAba (egyszeri bejelentkezés) az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció SAP HANAval való konfigurálásához a következő elemek szükségesek:

- Azure AD-előfizetés
- Az egyszeri bejelentkezést (SSO) engedélyező SAP HANA előfizetés
- Olyan HANA-példány, amely nyilvános IaaS, helyszíni, Azure-beli virtuális gépen vagy nagy méretű SAP-példányokon fut az Azure-ban
- A XSA felügyelet webes felülete, valamint a Hana-példányra telepített HANA Studio

> [!NOTE]
> Az oktatóanyag lépéseinek teszteléséhez nem ajánlott SAP HANA éles környezetét használni. Először tesztelje az integrációt az alkalmazás fejlesztési vagy átmeneti környezetében, majd használja az éles környezetet.

Az oktatóanyag lépéseinek teszteléséhez kövesse az alábbi javaslatokat:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* SAP HANA egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* SAP HANA támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést
* SAP HANA támogatja **az** igény szerinti felhasználói üzembe helyezést

## <a name="adding-sap-hana-from-the-gallery"></a>SAP HANA hozzáadása a gyűjteményből

SAP HANA az Azure AD-be való integrálásának konfigurálásához hozzá kell adnia SAP HANA a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha SAP HANA szeretne hozzáadni a katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **SAP HANA**kifejezést, válassza a **SAP HANA** az eredmények paneljén, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![SAP HANA az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés konfigurálását és tesztelését SAP HANA a **Britta Simon**nevű tesztelési felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a SAP HANA kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés SAP HANA használatával történő konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[SAP HANA egyszeri bejelentkezés konfigurálása](#configure-sap-hana-single-sign-on)** – az egyes Sign-On beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Hozzon létre SAP HANA test User](#create-sap-hana-test-user)** -t, hogy a Britta Simon partnere legyen a SAP HANA, amely a felhasználó Azure ad-képviseletéhez van csatolva.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés SAP HANA használatával történő konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **SAP HANA** alkalmazás-integráció lapon válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **egyszeri Sign-On beállítása az SAML-vel** lapon hajtsa végre a következő lépéseket:

    ![A tartomány és az URL-címek egyszeri bejelentkezési adatainak SAP HANA](common/idp-intiated.png)

    a. Az **azonosító** szövegmezőbe írja be a következőt: `HA100`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító és válasz URL-címmel. Ezekhez az értékekhez vegye fel a kapcsolatot SAP HANA ügyfélszolgálati [csapatával](https://cloudplatform.sap.com/contact.html) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. SAP HANA alkalmazás megadott formátumban várja az SAML-kijelentéseket. Konfigurálja a következő jogcímeket ehhez az alkalmazáshoz. Az attribútumok értékeit az alkalmazás-integráció lapon, a **felhasználói attribútumok** szakaszban kezelheti. Az **egyszeres Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** gombra a **felhasználói attribútumok** párbeszédpanel megnyitásához.

    ![image](common/edit-attribute.png)

6. A felhasználói **attribútumok** **& a jogcímek** párbeszédpanelen hajtsa végre a következő lépéseket:
 
    a. Kattintson a **Szerkesztés ikonra** a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    ![image](./media/saphana-tutorial/tutorial_usermail.png)

    ![image](./media/saphana-tutorial/tutorial_usermailedit.png)

    b. Az **átalakítás** listából válassza a **ExtractMailPrefix ()** elemet.

    c. Az **1. paraméter** listából válassza a **User. mail**elemet.

    d. Kattintson a **Mentés** gombra.

7. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

### <a name="configure-sap-hana-single-sign-on"></a>SAP HANA egyetlen Sign-On konfigurálása

1. Ha az egyszeri bejelentkezést az SAP HANA oldalon szeretné konfigurálni, jelentkezzen be a **HANA XSA Webkonzolra**  a megfelelő HTTPS-végponton.

    > [!NOTE]
    > Az alapértelmezett konfigurációban az URL-cím átirányítja a kérést egy bejelentkezési képernyőre, amelyhez hitelesített SAP HANA adatbázis-felhasználó hitelesítő adatai szükségesek. A bejelentkező felhasználónak rendelkeznie kell az SAML felügyeleti feladatok végrehajtásához szükséges engedélyekkel.

2. A XSA webes felületén nyissa meg az **SAML-identitás szolgáltatóját**. Innen válassza a **+** képernyő alján található gombot az **Identity Provider információinak hozzáadása** panel megjelenítéséhez. Ezután hajtsa végre a következő lépéseket:

    ![Identitás-szolgáltató hozzáadása](./media/saphana-tutorial/sap1.png)

    a. Az **Identity Provider információinak hozzáadása** panelen illessze be a metaadatok XML-fájljának tartalmát (amelyet a Azure Portalból töltött le) a **metaadatok** mezőbe.

    ![Identitás-szolgáltató beállításainak megadása](./media/saphana-tutorial/sap2.png)

    b. Ha az XML-dokumentum tartalma érvényes, az elemzési folyamat kibontja az **általános** adatképernyő területén a **tulajdonos, az entitás-azonosító és a kiállító** mezőkhöz szükséges adatokat. Emellett kigyűjti a **célként** megadott képernyő URL mezőihez szükséges adatokat, például az **alap URL-címet és a SingleSignon URL-címét (*)** .

    ![Identitás-szolgáltató beállításainak megadása](./media/saphana-tutorial/sap3.png)

    c. Az **általános** adatképernyő terület **név** mezőjébe írja be az új SAML SSO-azonosító nevét.

    > [!NOTE]
    > Az SAML-IDENTITÁSSZOLGÁLTATÓ neve kötelező, és egyedinek kell lennie. Megjelenik a rendelkezésre álló SAML-IDP listájában, amely akkor jelenik meg, ha a SAP HANA XS-alkalmazások használatára vonatkozó hitelesítési módszerként kiválasztja az SAML-t. Ezt például az XS-összetevő felügyeleti eszközének **hitelesítési** képernyő területén teheti meg.

3. A **Save (Mentés** ) gombra kattintva mentheti a SAML-identitás szolgáltatójának adatait, és hozzáadhatja az új SAML-identitásszolgáltató az ismert SAML-IDP listájához.

    ![Mentés gomb](./media/saphana-tutorial/sap4.png)

4. A HANA Studióban, a **konfiguráció** lap rendszertulajdonságai között, az **SAML**alapján szűrheti a beállításokat. Ezután módosítsa a **assertion_timeout** **10 másodperc** és **120 másodperc**közötti értékre.

    ![assertion_timeout beállítás](./media/saphana-tutorial/sap7.png)

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

    d. Kattintson a **Létrehozás** elemre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezi az Azure egyszeri bejelentkezést az SAP HANA elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **SAP HANA**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában írja be és válassza a **SAP HANA**lehetőséget.

    ![Az alkalmazások lista SAP HANA hivatkozása](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-sap-hana-test-user"></a>SAP HANA tesztelési felhasználó létrehozása

Ha engedélyezni szeretné, hogy az Azure AD-felhasználók bejelentkezzenek a SAP HANAba, be kell építenie azokat a SAP HANAban.
SAP HANA támogatja az igény szerinti **üzembe**helyezést, amely alapértelmezés szerint engedélyezve van.

Ha manuálisan kell létrehoznia egy felhasználót, hajtsa végre a következő lépéseket:

>[!NOTE]
>Módosíthatja a felhasználó által használt külső hitelesítést. Külső rendszerekkel, például Kerberos-hitelesítéssel is hitelesíthetők. A külső identitásokkal kapcsolatos részletes információkért forduljon a [tartományi rendszergazdához](https://cloudplatform.sap.com/contact.html).

1. Nyissa meg a [SAP HANA studiót](https://help.sap.com/viewer/a2a49126a5c546a9864aae22c05c3d0e/2.0.01/en-us) rendszergazdaként, majd engedélyezze a DB-User SAML SSO-hoz.

    ![Felhasználó létrehozása](./media/saphana-tutorial/sap5.png)

2. Jelölje be a láthatatlan jelölőnégyzetet az **SAML**bal oldalán, majd válassza a **Konfigurálás** hivatkozást.

3. Válassza a **Hozzáadás** lehetőséget az SAML-identitásszolgáltató hozzáadásához.  Válassza ki a megfelelő SAML-IDENTITÁSSZOLGÁLTATÓ, majd kattintson **az OK gombra**.

4. Adja hozzá a **külső identitást** (ebben az esetben BrittaSimon), **vagy válasszon egyet**. Ezután válassza az **OK** gombot.

   > [!Note]
   > Ha a **bármely** jelölőnégyzet nincs bejelölve, akkor a HANA-beli felhasználónévnek pontosan egyeznie kell a felhasználó nevével az UPN-ben a tartomány utótagja előtt. (Például BrittaSimon@contoso.com BrittaSimon válik a HANA-ban.)

5. Tesztelési célból rendeljen hozzá minden **XS** -szerepkört a felhasználóhoz.

    ![Szerepkörök kiosztása](./media/saphana-tutorial/sap6.png)

    > [!TIP]
    > Olyan engedélyeket kell megadni, amelyek csak a használati esetekben megfelelőek.

6. Mentse a felhasználót.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a SAP HANA csempére kattint, automatikusan be kell jelentkeznie arra a SAP HANAra, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


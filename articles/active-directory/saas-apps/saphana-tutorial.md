---
title: 'Oktatóanyag: Az Azure Active Directory-integráció az SAP HANA |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezést az SAP HANA és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: cef4a146-f4b0-4e94-82de-f5227a4b462c
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 017d693f0d71692abfb432216ca0645dee80d7df
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56865229"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>Oktatóanyag: Az Azure Active Directory-integráció az SAP HANA

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálható a SAP HANA az Azure Active Directory (Azure AD).
SAP HANA integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá az SAP HANA az Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezve (egyszeri bejelentkezés) SAP Hana-hoz az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az SAP HANA az Azure AD-integráció konfigurálásához lesz szüksége a következő elemek:

- Azure AD-előfizetés
- Egy SAP HANA-előfizetést, amely egyszeri bejelentkezés (SSO) engedélyezve van
- Bármely nyilvános IaaS-on futó HANA-példány helyszíni, Azure virtuális Gépen vagy SAP nagyméretű példányok az Azure-ban
- A XSA felügyeleti webes felület, valamint a HANA Studio telepítve van a HANA-példányon

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez az SAP Hana éles környezetben használatát nem javasoljuk. Az integráció először tesztelje a fejlesztési vagy az alkalmazás átmeneti környezetben, és az éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez hajtsa végre ezeket a javaslatokat:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* SAP HANA – egyszeri bejelentkezéshez engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja az SAP HANA **Identitásszolgáltató** által kezdeményezett egyszeri bejelentkezés
* Támogatja az SAP HANA **just-in-time** felhasználók átadása

## <a name="adding-sap-hana-from-the-gallery"></a>SAP HANA hozzáadása a katalógusból

Konfigurálja az integráció az SAP Hana az Azure AD-be, szüksége a katalógus az SAP HANA hozzáadása a felügyelt SaaS-alkalmazások listájában.

**SAP HANA hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **SAP HANA**, jelölje be **SAP HANA** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![SAP HANA, az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az SAP HANA nevű tesztfelhasználó alapján **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó, az SAP Hana-beli hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az SAP HANA tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az SAP HANA egyszeri bejelentkezés konfigurálása](#configure-sap-hana-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre az SAP HANA tesztfelhasználót](#create-sap-hana-test-user)**  - a-megfelelője a Britta Simon rendelkezik, amely kapcsolódik az Azure AD felhasználói ábrázolása SAP HANA-ban.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Konfigurálja az Azure AD egyszeri bejelentkezés az SAP HANA, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **SAP HANA** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **állítsa be egyszeri bejelentkezést az SAML** lapon, a következő lépésekkel:

    ![Az SAP HANA-tartomány és URL-címek egyszeri bejelentkezési adatait](common/idp-intiated.png)

    a. Az a **azonosító** szöveg mezőbe írja be a következőt: `HA100`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc`

    > [!NOTE]
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges azonosítóját és a válasz URL-cím. Kapcsolattartó [SAP HANA-ügyfél-támogatási csapatának](https://cloudplatform.sap.com/contact.html) beolvasni ezeket az értékeket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

5. SAP HANA-alkalmazás a SAML helyességi feltételek vár egy megadott formátumban. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a **felhasználói attribútumok** szakasz alkalmazás integráció lapján. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** gombra kattintva nyissa meg a **felhasználói attribútumok** párbeszédpanel.

    ![image](common/edit-attribute.png)

6. Az a **felhasználói attribútumok** szakaszában a **felhasználói attribútumok & jogcímek** párbeszédpanelen hajtsa végre az alábbi lépéseket:
 
    a. Kattintson a **Szerkesztés ikon** megnyitásához a **kezelheti a felhasználói jogcímek** párbeszédpanel.

    ![image](./media/saphana-tutorial/tutorial_usermail.png)

    ![image](./media/saphana-tutorial/tutorial_usermailedit.png)

    b. Az a **átalakítási** listáról válassza ki **ExtractMailPrefix()**.

    c. Az a **paraméter 1** listáról válassza ki **user.mail**.

    d. Kattintson a **Save** (Mentés) gombra.

7. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **összevonási metaadatainak XML**  a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

### <a name="configure-sap-hana-single-sign-on"></a>Az SAP HANA egyszeri bejelentkezés konfigurálása

1. Az SAP HANA oldalán konfigurálása egyszeri bejelentkezéshez, jelentkezzen be a **HANA XSA Webkonzol** nyissa meg a megfelelő HTTPS-végpontokat.

    > [!NOTE]
    > Az alapértelmezett beállítás az URL-címet átirányítja a kérést egy bejelentkezési képernyő, amely egy SAP HANA-adatbázis hitelesített felhasználó hitelesítő adatait igényli. A felhasználó, aki bejelentkezik jogosultnak kell lennie az SAML-felügyeleti feladatok végrehajtásához.

2. Lépjen a XSA Web-felületén **identitásszolgáltató SAML**. Itt válassza ki a **+** gombra a megjelenítendő képernyő alján a **identitás kiadói információk hozzáadása** ablaktáblán. Ezután az alábbi lépéseket:

    ![Add Identity Provider](./media/saphana-tutorial/sap1.png)

    a. Az a **identitás kiadói információk hozzáadása** panelen illessze be a metaadatainak XML (amely az Azure Portalról letöltött) tartalmát a **metaadatok** mezőbe.

    ![Identitásszolgáltató-beállítások hozzáadása](./media/saphana-tutorial/sap2.png)

    b. Az XML-dokumentum tartalmát érvényesek, ha az elemzési folyamat kinyeri a számára szükséges információkat a **tárgy, entitás azonosítója és a kiállító** mezőt a **általános adatok** képernyőterület. Az URL-címet a mezőket, a szükséges információkat is bontja ki a **cél** képernyőterület, például a **alap URL-cím és az egyszeri bejelentkezés URL-cím (*)** mezőket.

    ![Identitásszolgáltató-beállítások hozzáadása](./media/saphana-tutorial/sap3.png)

    c. Az a **neve** mezőjében a **általános adatok** képernyőterület, adja meg az SAML SSO új identitásszolgáltató nevét.

    > [!NOTE]
    > Az SAML-Identitásszolgáltató nevét kötelező megadni, és egyedinek kell lennie. A rendelkezésre álló SAML identitásszolgáltató SAML az SAP HANA XS alkalmazásokkal való használatra hitelesítési módszer kiválasztásakor megjelenő listájában jelenik meg. Például ezt megteheti a **hitelesítési** képernyőn az XS összetevő eszköz területéhez.

3. Válassza ki **mentése** részleteit az SAML-identitásszolgáltató mentése, és adja hozzá az új Identitásszolgáltató SAML ismert az SAML-identitásszolgáltató listájához.

    ![Mentés gomb](./media/saphana-tutorial/sap4.png)

4. A HANA Studio, a rendszer tulajdonságain belül a **konfigurációs** lapon, a beállítások szerint szűrheti **saml**. Majd szükség esetén módosítsa a **assertion_timeout** a **10 másodperc** való **120 másodperc**.

    ![assertion_timeout beállítás](./media/saphana-tutorial/sap7.png)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőtípus **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon a hozzáférés biztosításával az SAP Hana-hoz az Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **SAP HANA**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listáját, írja be, és válassza ki **SAP HANA**.

    ![Az alkalmazások listáját az SAP HANA hivatkozásra](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-sap-hana-test-user"></a>SAP HANA tesztfelhasználó létrehozása

Ahhoz, hogy jelentkezzen be az SAP HANA az Azure AD-felhasználók, kell építheti ki azokat az SAP HANA-ban.
Támogatja az SAP HANA **just-in-time-kiépítés**, ami által alapértelmezés szerint engedélyezve van.

Ha szeretné manuálisan hozzon létre egy felhasználót, tegye a következőket:

>[!NOTE]
>A külső hitelesítés a felhasználó által használt módosíthatja. Egy külső rendszer például a Kerberos-hitelesítéshez is. Külső identitások kapcsolatos részletes információkért lépjen kapcsolatba a [tartományi rendszergazda](https://cloudplatform.sap.com/contact.html).

1. Nyissa meg a [SAP HANA Studio](https://help.sap.com/viewer/a2a49126a5c546a9864aae22c05c3d0e/2.0.01/en-us) alkalmazást egy rendszergazdaként, majd az adatbázis-felhasználó engedélyezheti azt az SAML egyszeri bejelentkezéshez.

    ![Felhasználó létrehozása](./media/saphana-tutorial/sap5.png)

2. A nem látható jelölőnégyzet bejelölésével bal oldalán **SAML**, majd válassza ki a **konfigurálása** hivatkozásra.

3. Válassza ki **Hozzáadás** az SAML-Identitásszolgáltató hozzáadása.  Válassza ki a megfelelő Identitásszolgáltató SAML, majd a **OK**.

4. Adja hozzá a **külső azonosító** (ebben az esetben az BrittaSimon), vagy válasszon **bármely**. Ezután kattintson az **OK** gombra.

    >[!Note]
    >Ha a **bármely** jelölőnégyzet nincs bejelölve, akkor a felhasználó nevét, a HANA a felhasználói UPN-jét a tartományi utótag előtt neve pontosan egyeznie kell. (Például BrittaSimon@contoso.com Hana BrittaSimon válik.)

5. Tesztelési célokra az összes hozzárendelése **XS** a felhasználói szerepköröket.

    ![Szerepkörök hozzárendelése](./media/saphana-tutorial/sap6.png)

    > [!TIP]
    > Csak a használati esetek megfelelő engedélyeket kell adnia.

6. A felhasználó mentse.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a SAP HANA csempére kattint, akkor kell automatikusan megtörténik a az SAP Hana-hoz, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


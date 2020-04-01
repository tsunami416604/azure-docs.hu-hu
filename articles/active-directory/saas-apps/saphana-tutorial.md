---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az SAP HANA-val | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az SAP HANA között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: cef4a146-f4b0-4e94-82de-f5227a4b462c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 44a34fe5637e895ea69b6fc4c277b7722b306c97
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73161180"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>Oktatóanyag: Az Azure Active Directory integrációja az SAP HANA-val

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az SAP HANA-t az Azure Active Directoryval (Azure AD).
Az SAP HANA integrálása az Azure AD-vel a következő előnyöket biztosítja:

* Szabályozhatja az Azure AD-ben, aki hozzáfér az SAP HANA.You can control in Azure AD who has access to SAP HANA.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve az SAP HANA (Single Sign-On) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció sap HANA konfigurálásához a következő elemekre van szüksége:

- Azure AD-előfizetés
- Az SAP HANA-előfizetés, amely egyszeri bejelentkezés (SSO) engedélyezve van
- Hana-példány, amely bármely nyilvános IaaS-, helyszíni, Azure-beli virtuális gépen vagy SAP-példányon fut az Azure-ban
- Az XSA Administration webes felület, valamint a HANA-példányra telepített HANA Studio

> [!NOTE]
> Nem javasoljuk, hogy az SAP HANA éles környezetben tesztelje az oktatóanyag lépéseit. Tesztelje az integrációt először az alkalmazás fejlesztési vagy átmeneti környezetében, majd használja az éles környezetet.

Az oktatóanyag lépéseinek teszteléséhez kövesse az alábbi ajánlásokat:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* SAP HANA egyszeri bejelentkezéssel rendelkező előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* Az SAP HANA támogatja az **IDP** által kezdeményezett SSO-t
* Az SAP HANA támogatja **a just-in-time** felhasználói kiépítést

## <a name="adding-sap-hana-from-the-gallery"></a>SAP HANA hozzáadása a gyűjteményből

Az SAP HANA azure AD-be való integrálásának konfigurálásához hozzá kell adnia az SAP HANA-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Sap HANA hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **SAP HANA**, válassza **az SAP HANA** az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![SAP HANA az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést az SAP HANA-val egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés működéséhez létre kell hozni egy kapcsolat közötti kapcsolat egy Azure AD-felhasználó és a kapcsolódó felhasználó sap HANA létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez az SAP HANA-val a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja az SAP HANA Single Sign-On](#configure-sap-hana-single-sign-on)** -konfigurálásához az egyszeri bejelentkezési beállításokat az alkalmazás oldalán.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre SAP HANA tesztfelhasználó](#create-sap-hana-test-user)** – egy megfelelője Britta Simon az SAP HANA, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához az SAP HANA-val hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)az **SAP HANA** alkalmazásintegrációs lapon válassza **az Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon hajtsa végre az alábbi lépéseket:

    ![SAP HANA-tartomány és URL-címek egyszeri bejelentkezési információk](common/idp-intiated.png)

    a. Az **Azonosító** mezőbe írja be a következőt:`HA100`

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító és válasz URL-címével. Lépjen kapcsolatba [az SAP HANA ügyfél támogatási csapatával,](https://cloudplatform.sap.com/contact.html) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

5. AZ SAP HANA alkalmazás az SAML-állításokat egy adott formátumban várja. Konfigurálja az alkalmazás következő jogcímeket. Ezeknek az attribútumoknak az értékeit az alkalmazásintegrációs lap **Felhasználói attribútumok** szakaszában kezelheti. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** gombra a **Felhasználói attribútumok** párbeszédpanel megnyitásához.

    ![image](common/edit-attribute.png)

6. A **Felhasználói attribútumok & jogcímek** párbeszédpanel **Felhasználói attribútumok** & párbeszédpanelen hajtsa végre a következő lépéseket:
 
    a. Kattintson a **Szerkesztés ikonra** a **Felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    ![image](./media/saphana-tutorial/tutorial_usermail.png)

    ![image](./media/saphana-tutorial/tutorial_usermailedit.png)

    b. Az **Átalakítás** listában válassza **a ExtractMailPrefix() (ExtractMailPrefix() (ExtractMailPrefix() (ExtractMailPrefix() (ExtractMailPrefix() (Átalakítási**

    c. Az **1.** **user.mail**

    d. Kattintson a **Mentés** gombra.

7. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány csoportjában** kattintson a **Letöltés** gombra, ha letöltheti az **összevonási metaadat-XML-t** a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

### <a name="configure-sap-hana-single-sign-on"></a>SAP HANA egyszeri bejelentkezés konfigurálása

1. Az SAP HANA oldalán egyszeri bejelentkezés konfigurálásához jelentkezzen be a **HANA XSA webkonzolra** a megfelelő HTTPS-végpontra való megtérésével.

    > [!NOTE]
    > Az alapértelmezett konfigurációban az URL-cím átirányítja a kérelmet egy bejelentkezési képernyőre, amely egy hitelesített SAP HANA-adatbázis-felhasználó hitelesítő adatait igényli. A bejelentkező felhasználónak engedéllyel kell rendelkeznie az SAML felügyeleti feladatok elvégzéséhez.

2. Az XSA webes felületén nyissa meg az **SAML-identitásszolgáltató t.** Itt válassza ki **+** a képernyő alján lévő gombot az **Identitásszolgáltató adatainak hozzáadása** ablaktábla megjelenítéséhez. Ezután tegye a következő lépéseket:

    ![Identitásszolgáltató hozzáadása](./media/saphana-tutorial/sap1.png)

    a. Az **Identitásszolgáltató adatainak hozzáadása** ablaktáblán illessze be a metaadat-XML tartalmát (amelyet az Azure Portalról töltött le) a **Metaadatok** mezőbe.

    ![Identitásszolgáltató beállításainak hozzáadása](./media/saphana-tutorial/sap2.png)

    b. Ha az XML-dokumentum tartalma érvényes, az elemzési folyamat kinyeri a **Tárgy, az Entitásazonosító és** a Kiállító mezőhöz szükséges információkat az **Általános adatképernyő** területén. Azt is kibontja az url-mezők a **Cél** képernyő területén, például az **alap URL-cím és a SingleSignOn URL-cím (*)** mezők.

    ![Identitásszolgáltató beállításainak hozzáadása](./media/saphana-tutorial/sap3.png)

    c. Az **Általános adatok** képernyőterület **Név** mezőjében adja meg az új SAML SSO-identitásszolgáltató nevét.

    > [!NOTE]
    > Az SAML IDP neve kötelező, és egyedinek kell lennie. Úgy tűnik, a rendelkezésre álló SAML IDP-k listájában jelenik meg, amely akkor jelenik meg, ha az SAML-t választja az SAP HANA XS-alkalmazások használandó hitelesítési módszereként. Ezt például az XS-műtermék-felügyelet **eszköz Hitelesítési** képernyőjén teheti meg.

3. Válassza a **Mentés** lehetőséget az SAML-identitásszolgáltató részleteinek mentéséhez és az új SAML IDP hozzáadásához az ismert SAML idp-k listájához.

    ![Mentés gomb](./media/saphana-tutorial/sap4.png)

4. A HANA Studio rendszerben a **Konfiguráció** lap rendszertulajdonságain belül szűrje a beállításokat **saml**szerint. Ezután állítsa be a **assertion_timeout** **10 mp-ről** **120 mp-re.**

    ![assertion_timeout beállítás](./media/saphana-tutorial/sap7.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása 

Ez a szakasz célja, hogy hozzon létre egy tesztfelhasználót az Azure Portalon Britta Simon.

1. Az Azure Portalon a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd az **Összes felhasználó**lehetőséget.

    ![A "Felhasználók és csoportok" és a "Minden felhasználó" linkek](common/users.png)

2. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A Felhasználó tulajdonságokban hajtsa végre a következő lépéseket.

    ![A Felhasználó párbeszédpanel](common/user-properties.png)

    a. A **Név** mezőbe írja be **a BrittaSimon**értéket.
  
    b. A **Felhasználónév** mezőbe írja be **a\@brittasimon yourcompanydomain.extension típusú felhasználónév mezőt.**  
    Például: BrittaSimon@contoso.com

    c. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket.

    d. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés az SAP HANA használatával.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza az **SAP HANA**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában írja be és válassza az **SAP HANA parancsot.**

    ![Az SAP HANA hivatkozás az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-sap-hana-test-user"></a>SAP HANA-tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezzenek az SAP HANA-ba, ki kell építenie őket az SAP HANA-ban.
Az SAP HANA támogatja **a just-in-time kiépítése,** amely alapértelmezés szerint engedélyezve van.

Ha manuálisan kell létrehoznia egy felhasználót, tegye a következő lépéseket:

>[!NOTE]
>Módosíthatja a felhasználó által használt külső hitelesítést. Hitelesíthetik magukat egy külső rendszerrel, például a Kerberos-szal. A külső identitásokról a [tartományi rendszergazdától](https://cloudplatform.sap.com/contact.html)kaphat részletes információt.

1. Nyissa meg az [SAP HANA Studio](https://help.sap.com/viewer/a2a49126a5c546a9864aae22c05c3d0e/2.0.01/en-us) rendszergazdaként, és engedélyezze a DB-felhasználó SAML Egyszeri kiszolgáló.

    ![Felhasználó létrehozása](./media/saphana-tutorial/sap5.png)

2. Jelölje be az **SAML-től**balra lévő láthatatlan jelölőnégyzetet, majd a **Hivatkozás konfigurálása** lehetőséget.

3. Az SAML IDP hozzáadásához válassza a **Hozzáadás** lehetőséget.  Válassza ki a megfelelő SAML IDP-t, majd kattintson **az OK gombra.**

4. Adja hozzá a **külső identitást** (ebben az esetben BrittaSimon), vagy válassza **a Bármely**lehetőséget. Ezután kattintson az **OK** gombra.

   > [!Note]
   > Ha a **Minden** jelölőnégyzet nincs bejelölve, akkor a HANA-ban lévő felhasználónévnek pontosan meg kell egyeznie az upn-ban lévő felhasználó nevével a tartományutótag előtt. (Például, BrittaSimon@contoso.com lesz BrittaSimon a HANA.)

5. Tesztelési célokra rendelje hozzá az összes **XS-szerepkört** a felhasználóhoz.

    ![Szerepkörök hozzárendelése](./media/saphana-tutorial/sap6.png)

    > [!TIP]
    > Olyan engedélyeket kell megadnia, amelyek csak a használati eseteknek megfelelőek.

6. Mentse a felhasználót.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha az SAP HANA csempére kattint a hozzáférési panelen, automatikusan be kell jelentkeznie az SAP HANA-ba, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


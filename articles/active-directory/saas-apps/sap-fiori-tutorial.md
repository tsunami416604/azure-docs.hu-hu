---
title: 'Oktatóanyag: Az Azure Active Directory-integráció az SAP Fiori |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és az SAP Fiori között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 77ad13bf-e56b-4063-97d0-c82a19da9d56
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: jeedes
ms.openlocfilehash: 9e7993ee1cb439ebeaa9f64bee55429aa54f9cee
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2019
ms.locfileid: "65903946"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-fiori"></a>Oktatóanyag: Az Azure Active Directory-integráció az SAP Fiori

Ebben az oktatóanyagban elsajátíthatja, hogyan SAP Fiori integrálása az Azure Active Directory (Azure AD).

Az SAP Fiori integrálása az Azure AD kínál fel a következő előnyökkel jár:

* Az Azure AD, hogy ki férhet hozzá az SAP Fiori is használhatja.
* Felhasználók is automatikusan megtörténik a SAP Fiori-, a saját Azure AD-fiókok (egyszeri bejelentkezés).
* A fiókok egyetlen központi helyen, az Azure Portalon kezelheti.

Az Azure ad-vel szoftverként (saas biztosított) alkalmazás integrációja szoftverrel kapcsolatos további információkért lásd: [egyszeri bejelentkezés alkalmazásokhoz az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálja az Azure AD-integráció az SAP Fiori, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik Azure AD előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) megkezdése előtt.
* Egy SAP Fiori-előfizetés az egyszeri bejelentkezés engedélyezve van.
* 7.20 vagy újabb SAP Fiori megadása kötelező.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálni és a egy tesztkörnyezetben az Azure AD egyszeri bejelentkezés tesztelése és SAP Fiori integrálása az Azure ad-ben.

Az SAP Fiori támogatja a következő funkciókat:

* **SP által kezdeményezett egyszeri bejelentkezés**

## <a name="add-sap-fiori-in-the-azure-portal"></a>Az SAP Fiori hozzáadása az Azure Portalon

SAP Fiori integrálható az Azure ad-vel, hozzá kell adnia az SAP Fiori a felügyelt SaaS-alkalmazások listájában.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A bal oldali menüben válassza ki a **Azure Active Directory**.

    ![Az Azure Active Directoryval opciót.](common/select-azuread.png)

1. Válassza ki **vállalati alkalmazások** > **minden alkalmazás**.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

1. Egy alkalmazás hozzáadásához válassza **új alkalmazás**.

    ![Az új alkalmazás-beállítás](common/add-new-app.png)

1. A Keresés mezőbe írja be a **SAP Fiori**. A keresési eredmények között, válassza ki a **SAP Fiori**, majd válassza ki **Hozzáadás**.

    ![Az SAP Fiori a találatok listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az SAP Fiori nevű tesztfelhasználó alapján **Britta Simon**. Az egyszeri bejelentkezés működéséhez az SAP Fiori kell egy Azure AD-felhasználót és a kapcsolódó felhasználó között társított kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az SAP Fiori tesztelése és konfigurálása, a következő építőelemeit kell elvégeznie:

| Feladat | Leírás |
| --- | --- |
| **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)** | Lehetővé teszi a felhasználók a funkció használatához. |
| **[Az SAP Fiori egyszeri bejelentkezés konfigurálása](#configure-sap-fiori-single-sign-on)** | Az egyszeri bejelentkezési beállításainak konfigurálása az alkalmazásban. |
| **[Hozzon létre egy Azure ad-ben tesztfelhasználó számára](#create-an-azure-ad-test-user)** | Tesztek az Azure AD egyszeri bejelentkezés egy felhasználó nevű Britta Simon. |
| **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assign-the-azure-ad-test-user)** | Britta Simon az Azure AD egyszeri bejelentkezés használata lehetővé teszi. |
| **[Hozzon létre egy SAP Fiori-tesztfelhasználó számára](#create-an-sap-fiori-test-user)** | A felhasználó Azure ad-ben reprezentációja kapcsolódó SAP Fiori-megfelelője a Britta Simon hoz létre. |
| **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** | Ellenőrzi, hogy működik-e a konfiguráció. |

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban konfigurálja az Azure AD egyszeri bejelentkezés az SAP Fiori az Azure Portalon.

1. Nyisson meg egy új böngészőablakban, és jelentkezzen be rendszergazdaként a SAP Fiori-vállalati hely.

1. Győződjön meg arról, hogy **http** és **https** szolgáltatások aktívak, és a megfelelő portok vannak rendelve a tranzakció kódot **SMICM**.

1. Jelentkezzen be az SAP Business ügyfél SAP-rendszer **T01**, ahol az egyszeri bejelentkezés szükséges. HTTP biztonsági munkamenet-kezelés, majd aktiválja.

    1. Lépjen a tranzakció kódot **SICF_SESSIONS**. Az összes releváns profil paramétereinek aktuális értékekkel jelennek meg. Az alábbi példa néznek ki:

        ```
        login/create_sso2_ticket = 2
        login/accept_sso2_ticket = 1
        login/ticketcache_entries_max = 1000
        login/ticketcache_off = 0  login/ticket_only_by_https = 0 
        icf/set_HTTPonly_flag_on_cookies = 3
        icf/user_recheck = 0  http/security_session_timeout = 1800
        http/security_context_cache_size = 2500
        rdisp/plugin_auto_logout = 1800
        rdisp/autothtime = 60
        ```

        >[!NOTE]
        > Módosítsa a paramétereket a szervezeti követelmények alapján. A fenti paraméterek csak példaként kapnak.

    1. Ha szükséges, módosítsa a paramétereket a példány (alapértelmezés) profilban, az SAP-rendszer, és indítsa újra az SAP-rendszerhez.

    1. Kattintson duplán a megfelelő ügyfél ahhoz, hogy egy biztonsági HTTP-munkamenetben.

        ![Az aktuális értékek a megfelelő profil paramétereinek oldal az SAP-ben](./media/sapfiori-tutorial/tutorial-sapnetweaver-profileparameter.png)

    1. A következő SICF szolgáltatások aktiválása:

        ```
        /sap/public/bc/sec/saml2
        /sap/public/bc/sec/cdc_ext_service
        /sap/bc/webdynpro/sap/saml2
        /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
        ```

1. Lépjen a tranzakció kódot **egy SAML2** az SAP-rendszerhez üzleti ügyfél [**T01/122**]. A konfigurációs felhasználói felület egy új böngészőablakban nyílik meg. Ebben a példában a vállalati ügyfél SAP-rendszer 122 használjuk.

    ![Az SAP Fiori üzleti ügyfél bejelentkezési oldal](./media/sapfiori-tutorial/tutorial-sapnetweaver-sapbusinessclient.png)

1. Adja meg a felhasználónevét és jelszavát, és válassza ki **bejelentkezés**.

    ![Az SAP-ben az SAML 2.0 konfigurációs, ABAP rendszer T01/122 oldal](./media/sapfiori-tutorial/tutorial-sapnetweaver-userpwd.png)

1. Az a **szolgáltatónevet** mezőben cserélje le **T01122** a **http:\//T01122**, majd válassza ki **mentése**.

    > [!NOTE]
    > Alapértelmezés szerint a szolgáltató neve a következő formátumban van \<sid >\<ügyfél >. Az Azure AD nevét várja, a következő formátumban \<protokoll > ://\<neve >. Azt javasoljuk, hogy továbbra is a szolgáltató neve, mint a https\://\<sid >\<ügyfél >, hogy konfigurálhassa több SAP Fiori ABAP-motor az Azure ad-ben.

    ![Az SAP-ben az SAML 2.0 konfigurációs, ABAP rendszer T01/122 oldalon a frissített szolgáltatójának neve](./media/sapfiori-tutorial/tutorial-sapnetweaver-providername.png)

1. Válassza ki **helyi szolgáltató lap** > **metaadatok**.

1. Az a **SAML 2.0-metaadatokat** párbeszédpanelen, a generált metaadatainak XML-fájl letöltése és mentse azt a számítógépen.

    ![A metaadatok letöltése hivatkozásra az SAP SAML 2.0-metaadatokat párbeszédpanel](./media/sapfiori-tutorial/tutorial-sapnetweaver-generatesp.png)

1. Az a [az Azure portal](https://portal.azure.com/), a a **SAP Fiori** application integration ablaktáblában válassza **egyszeri bejelentkezési**.

    ![Az egyszeri bejelentkezési beállítás](common/select-sso.png)

1. Az a **egyszeri bejelentkezési módszer** ablaktáblán válassza ki az **SAML** vagy **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

1. Az a **állítsa be egyszeri bejelentkezést az SAML** ablaktáblán válassza **szerkesztése** (a ceruza ikon) nyissa meg a **alapszintű SAML-konfigurációja** ablaktáblán.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az a **alapszintű SAML-konfigurációja** területén kövesse az alábbi lépéseket:

    1. Válassza ki **metaadatfájl feltöltése**.

        ![Metaadatok feltöltés beállítást](common/upload-metadata.png)

   1. Válassza ki a metaadatait tartalmazó fájl, a mappa ikonra, és válassza **feltöltése**.

       ![Válassza ki a metaadat-fájlt, majd a feltöltés gombbal](common/browse-upload-metadata.png)

1. Ha sikeresen feltöltötte a metaadat-fájlt, a **azonosító** és **válasz URL-cím** értékeket automatikusan fel van töltve a a **alapszintű SAML-konfigurációja** ablaktáblán. Az a **bejelentkezési URL-cím** mezőbe írjon be egy URL-címet, amely rendelkezik a következő mintának: https:\//\<SAP Fiori-vállalati példányát\>.

    ![Az SAP Fiori-tartomány és URL-címek egyszeri bejelentkezési adatait](common/sp-identifier-reply.png)

    > [!NOTE]
    > Néhány ügyfelek jelentés kapcsolatos hibák helytelenül konfigurált **válasz URL-cím** értékeket. Ha ezt a hibát látja, a következő PowerShell-parancsfájl segítségével állítsa be a helyes válasz URL-cím a példány:
    >
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > 
    > Beállíthatja a `ServicePrincipal` objektumazonosító: saját maga a parancsfájl futtatása előtt, illetve itt adhat át.

1. Az SAP Fiori-alkalmazás a SAML helyességi feltételek egy meghatározott formátumban kell megadni. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezeket az attribútumértékeket kezelhet a **állítsa be egyszeri bejelentkezést az SAML** ablaktáblán válassza **szerkesztése**.

    ![A felhasználói attribútumok ablaktáblából.](common/edit-attribute.png)

1. Az a **felhasználói attribútumok & jogcímek** panelen konfigurálja az SAML-jogkivonat attribútumai, az előző képen látható módon. Ezután kövesse az alábbi lépéseket:

    1. Válassza ki **szerkesztése** megnyitásához a **kezelheti a felhasználói jogcímek** ablaktáblán.

    1. Az a **átalakítási** listáról válassza ki **ExtractMailPrefix()**.

    1. Az a **paraméter 1** listáról válassza ki **user.userprinicipalname**.

    1. Kattintson a **Mentés** gombra.

       ![A kezelés felhasználói jogcímek panel](./media/sapfiori-tutorial/nameidattribute.png)

       ![A kezelés felhasználói jogcímek panelen transzformációs szakaszából](./media/sapfiori-tutorial/nameidattribute1.png)


1. Az a **állítsa be egyszeri bejelentkezést az SAML** ablaktáblán, a a **SAML-aláíró tanúsítvány** szakaszban jelölje be **letöltése** melletti **összevonási metaadatainak XML**. Válassza ki a letöltési lehetőséget igényei alapján. Mentse a tanúsítványt a számítógépen.

    ![A tanúsítvány a letöltési lehetőséget](common/metadataxml.png)

1. Az a **SAP Fiori beállítása** területén másolja a következő URL-címek igényei alapján:

    * Bejelentkezési URL
    * Azure AD-azonosító
    * Kijelentkezési URL

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

### <a name="configure-sap-fiori-single-sign-on"></a>Az SAP Fiori egyszeri bejelentkezés konfigurálása

1. Jelentkezzen be az SAP-rendszerhez, és nyissa meg a tranzakció kódja **egy SAML2**. A SAML konfigurálása lapon megnyílik egy új böngészőablakban.

1. Végpontok konfigurálása egy megbízható identitásszolgáltatóra (Azure AD), válassza ki a **megbízható szolgáltatók** fülre.

    ![Az SAP megbízható szolgáltatók lapot](./media/sapfiori-tutorial/tutorial-sapnetweaver-samlconfig.png)

1. Válassza ki **Hozzáadás**, majd válassza ki **metaadatfájl feltöltése** a helyi menüből.

    ![A Hozzáadás és a metaadatfájl feltöltése beállítás, az SAP-ben](./media/sapfiori-tutorial/tutorial-sapnetweaver-uploadmetadata.png)

1. A letöltött az Azure Portalon metaadatfájl feltöltése. Kattintson a **Tovább** gombra.

    ![Válassza ki a metaadatfájl feltöltése az SAP-ben](./media/sapfiori-tutorial/tutorial-sapnetweaver-metadatafile.png)

1. A következő oldalon található a **Alias** aliasneve. Adja meg. Ha például **aadsts**. Kattintson a **Tovább** gombra.

    ![Az Alias mezőbe az SAP-ben](./media/sapfiori-tutorial/tutorial-sapnetweaver-aliasname.png)

1. Győződjön meg arról, hogy az értéket a **kivonatoló algoritmus** mező **SHA-256**. Kattintson a **Tovább** gombra.

    ![Ellenőrizze a kivonatoló algoritmus érték az SAP-ben](./media/sapfiori-tutorial/tutorial-sapnetweaver-identityprovider.png)

1. A **egyszeri bejelentkezési végpont**válassza **HTTP POST**, majd válassza ki **tovább**.

    ![Az SAP egyszeri bejelentkezési végpont beállításai](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect.png)

1. Alatt **egyszeri kijelentkezési végpont**válassza **HTTP-átirányítás**, majd válassza ki **tovább**.

    ![Az SAP egyszeri kijelentkezési végpont beállításai](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect1.png)

1. Alatt **összetevő végpontok**válassza **tovább** folytatásához.

    ![A SAP összetevő-végpontok beállításai](./media/sapfiori-tutorial/tutorial-sapnetweaver-artifactendpoint.png)

1. A **hitelesítési követelmények**válassza **Befejezés**.

    ![Hitelesítési követelményeknek lehetőségeket és a Befejezés beállítást, az SAP-ben](./media/sapfiori-tutorial/tutorial-sapnetweaver-authentication.png)

1. Válassza ki **szolgáltató megbízható** > **identitás-összevonási** (a lap alján). Válassza a **Szerkesztés** elemet.

    ![A megbízható szolgáltatót és az identitás-összevonási lapok az SAP-ben](./media/sapfiori-tutorial/tutorial-sapnetweaver-trustedprovider.png)

1. Válassza a **Hozzáadás** lehetőséget.

    ![Az identitás-összevonási lapon Hozzáadás pontját](./media/sapfiori-tutorial/tutorial-sapnetweaver-addidentityprovider.png)

1. Az a **támogatott NameID-formátumok** párbeszédpanelen jelölje ki **meghatározatlan**. Kattintson az **OK** gombra.

    ![A párbeszédpanel támogatott NameID-formátumok és a SAP-beállításai](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameid.png)

    A tartozó értékeket **felhasználói azonosító forrás** és **felhasználói azonosító leképezési módban** határozza meg az SAP-felhasználó és az Azure ad-ben jogcím közötti kapcsolat.  

    **1. forgatókönyv**: SAP-felhasználó számára az Azure AD-felhasználó-hozzárendelés

    1. Az SAP-ben a **részletei a NameID-formátum "nem meghatározott"**, vegye figyelembe a részletek:

        ![A részleteket a NameID-formátum "Ismeretlen" párbeszédpanel az SAP-ben](./media/sapfiori-tutorial/nameiddetails.png)

    1. Az Azure Portalon alatt **felhasználói attribútumok & jogcímek**, vegye figyelembe a szükséges jogcímek, az Azure ad-ből.

        ![A felhasználói attribútumok és a jogcímek párbeszédpanelen, az Azure Portalon](./media/sapfiori-tutorial/claimsaad1.png)

    **2. forgatókönyv**: Válassza ki az SAP felhasználói azonosító alapján SU01 a beállított e-mail-címét. Ebben az esetben az e-mail-Azonosítót kell konfigurálni a SU01 minden felhasználóhoz, aki az egyszeri bejelentkezés szükséges.

    1.  Az SAP-ben a **részletei a NameID-formátum "nem meghatározott"**, vegye figyelembe a részletek:

        ![A részleteket a NameID-formátum "Ismeretlen" párbeszédpanel az SAP-ben](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameiddetails1.png)

    1. Az Azure Portalon alatt **felhasználói attribútumok & jogcímek**, vegye figyelembe a szükséges jogcímek, az Azure ad-ből.

       ![A felhasználói attribútumok és a jogcímek párbeszédpanelen, az Azure Portalon](./media/sapfiori-tutorial/claimsaad2.png)

1. Válassza ki **mentése**, majd válassza ki **engedélyezése** az identitásszolgáltató engedélyezéséhez.

    ![A Mentés és engedélyezése a beállítások az SAP-ben](./media/sapfiori-tutorial/configuration1.png)

1. Válassza ki **OK** amikor a rendszer kéri.

    ![Az OK lehetőséget az SAP-ben az SAML 2.0 konfigurálása párbeszédpanel](./media/sapfiori-tutorial/configuration2.png)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban hozzon létre egy tesztfelhasználót Britta Simon nevű az Azure Portalon.

1. Az Azure Portalon válassza ki a **Azure Active Directory** > **felhasználók** > **minden felhasználó**.

    ![A felhasználók és az összes felhasználói beállítások](common/users.png)

1. Válassza ki **új felhasználó**.

    ![Az új felhasználói beállítás](common/new-user.png)

1. Az a **felhasználói** panelen a következő lépéseket:

    1. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    1. Az a **felhasználónév** mezőbe írja be **brittasimon\@\<a vállalati tartomány >.\< bővítmény >**. Ha például **brittasimon\@contoso.com**.

    1. Válassza ki a **Show jelszó** jelölőnégyzetet. Írja le az értéket, a megjelenő a **jelszó** mezőbe.

    1. Kattintson a **Létrehozás** gombra.

    ![A felhasználói panelen](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban hozzáférést adhat a Britta Simon SAP Fiori így ő Azure egyszeri bejelentkezéshez használható.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások** > **minden alkalmazás** > **SAP Fiori**.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

1. Az alkalmazások listájában jelölje ki a **SAP Fiori**.

    ![Az SAP Fiori-alkalmazásainak listájában](common/all-applications.png)

1. Válassza a menüben **felhasználók és csoportok**.

    ![A felhasználók és csoportok lehetőség](common/users-groups-blade.png)

1. Válassza ki **felhasználó hozzáadása**. Ezt követően a a **-hozzárendelés hozzáadása** ablaktáblán válassza előbb **felhasználók és csoportok**.

    ![A Hozzáadás hozzárendelési ablaktáblán](common/add-assign-user.png)

1. Az a **felhasználók és csoportok** ablaktáblán válassza **Britta Simon** felhasználók listájában. Válassza a **Kiválasztás** lehetőséget

1. Ha az a SAML-előfeltétel szerepkör értéket várt a **szerepkör kiválasztása** ablaktáblán válassza ki a megfelelő szerepkört a felhasználóhoz a listából. Válassza a **Kiválasztás** lehetőséget

1. Az a **hozzárendelés hozzáadása** ablaktáblán válassza előbb **hozzárendelése**.

### <a name="create-an-sap-fiori-test-user"></a>Hozzon létre egy SAP Fiori-tesztfelhasználó számára

Ebben a szakaszban az SAP Fiori Britta Simon nevű felhasználó létrehozásához. Az SAP csapat szakértők vagy a szervezet SAP partneri a felhasználó hozzáadása az SAP Fiori-platformon működik.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

1. Az identitásszolgáltató az Azure AD az SAP Fiori aktiválása után próbálja meg elérni az egyszeri bejelentkezés (nem ajánlott rákérdez a felhasználónév és jelszó) teszteléséhez a következő URL-címek valamelyikét:

    * https:\//\<sapurl\>/sap/bc/bsp/sap/it00/default.htm
    * https:\//\<sapurl\>/sap/bc/bsp/sap/it00/default.htm

    > [!NOTE]
    > Cserélje le *sapurl* SAP tényleges állomásnevét.

1. A tesztcélú URL-cím kell venniük, az alábbi tesztelési alkalmazás lapot az SAP-ben. Ha a lapon nyílik meg, az Azure AD egyszeri bejelentkezés sikeres beállítása.

    ![A standard SAP alkalmazások lap tesztelése](./media/sapfiori-tutorial/testingsso.png)

1. Ha felhasználónevet és jelszót kéri, engedélyezze a nyomkövetési a probléma diagnosztizálása érdekében. A nyomkövetés a következő URL-cím használata: https:\//\<sapurl\>/sap/bc/webdynpro/sap/sec_diag_tool? az sap-ügyfél 122 és sap-nyelv = = EN #.

## <a name="next-steps"></a>További lépések

További információkért tekintse át a következő cikkeket:

- [Az Azure Active Directory SaaS-alkalmazások integrálását ismertető oktatóanyagok listáját](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Egyszeri bejelentkezés az Azure Active Directory-alkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

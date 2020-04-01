---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az SAP Fiorival | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az SAP Fiori között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 77ad13bf-e56b-4063-97d0-c82a19da9d56
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/05/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 917ba9274276fec5d01a40bdf7219e8d4fee1395
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78897763"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-fiori"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az SAP Fiori-val

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az SAP Fiori-t az Azure Active Directoryval (Azure AD). Ha integrálja az SAP Fiori az Azure AD, a következőket teheti:

* Az Azure AD-ben, aki hozzáfér az SAP Fiori.
* Engedélyezze, hogy a felhasználók automatikusan bejelentkezve az SAP Fiori az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* SAP Fiori egyszeri bejelentkezés (SSO) engedélyezve van az előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* Az SAP Fiori támogatja az **SP** által kezdeményezett SSO-t

> [!NOTE]
> Az SAP Fiori által kezdeményezett iFrame-hitelesítés esetén azt javasoljuk, hogy az **ISPassive** paramétert használja az SAML AuthnRequest-ben a csendes hitelesítéshez. Az **IsPassive** paramétertovábbi részletekért tekintse meg az [Azure AD SAML egyszeri bejelentkezési](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-saml-protocol) adatait.

## <a name="adding-sap-fiori-from-the-gallery"></a>SAP Fiori hozzáadása a galériából

Az SAP Fiori azure AD-be való integrációjának konfigurálásához hozzá kell adnia az SAP Fiori-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a katalógusból szakaszban** írja be az **SAP Fiori** kifejezést a keresőmezőbe.
1. Válassza ki az **SAP Fiori** az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-fiori"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése az SAP Fiori számára

Konfigurálja és tesztelje az Azure AD SSO-t az SAP Fiori-val egy **B.Simon**nevű tesztfelhasználó használatával. Az SSO működéséhez létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó az SAP Fiori.

Az Azure AD SSO SAP Fiori-val való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    1. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja az SAP Fiori SSO-t](#configure-sap-fiori-sso)** – az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
    1. **[Hozzon létre SAP Fiori teszt felhasználó](#create-sap-fiori-test-user)** – a B.Simon megfelelője az SAP Fiori, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Nyisson meg egy új böngészőablakot, és jelentkezzen be az SAP Fiori vállalati webhelyére rendszergazdaként.

1. Győződjön meg arról, hogy **a http-** és **https-szolgáltatások** aktívak, és hogy a megfelelő portok az **SMICM**tranzakciókódhoz vannak rendelve.

1. Jelentkezzen be az SAP Business Client for SAP-rendszer **T01,** ahol egyszeri bejelentkezés szükséges. Ezután aktiválja a HTTP security session management.

    1. Nyissa meg a **tranzakciókódot SICF_SESSIONS.** Az aktuális értékekkel rendelkező összes releváns profilparaméter megjelenik. Úgy néznek ki, mint a következő példa:

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
        > Módosítsa a paramétereket a szervezeti követelmények alapján. Az előző paraméterek csak példaként vannak megadva.

    1. Ha szükséges, módosítsa a paramétereket az SAP-rendszer példány (alapértelmezett) profiljában, és indítsa újra az SAP-rendszert.

    1. Http-biztonsági munkamenet engedélyezéséhez kattintson duplán a megfelelő ügyfélre.

        ![A releváns profilparaméterek aktuális értékei az SAP-ban](./media/sapfiori-tutorial/tutorial-sapnetweaver-profileparameter.png)

    1. Aktiválja a következő SICF-szolgáltatásokat:

        ```
        /sap/public/bc/sec/saml2
        /sap/public/bc/sec/cdc_ext_service
        /sap/bc/webdynpro/sap/saml2
        /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
        ```

1. Ugrás az **SAML2** tranzakciókódra az SAP üzleti ügyfélszolgáltatásában [**T01/122**]. A konfigurációs felhasználói felület egy új böngészőablakban nyílik meg. Ebben a példában az SAP 122-es rendszerhez használt business client.

    ![Az SAP Fiori Business Client bejelentkezési lapja](./media/sapfiori-tutorial/tutorial-sapnetweaver-sapbusinessclient.png)

1. Írja be felhasználónevét és jelszavát, majd válassza **a Bejelentkezés**lehetőséget.

    ![Az ABAP System T01/122 lap SAML 2.0 konfigurációja az SAP-ban](./media/sapfiori-tutorial/tutorial-sapnetweaver-userpwd.png)

1. A **Szolgáltató neve** mezőben cserélje le a **T01122** kapcsolót **a következő re: http:\//T01122**, majd a Mentés **gombra.**

    > [!NOTE]
    > Alapértelmezés szerint a szolgáltató neve \<az \<ügyfél>>formátumban van. Az Azure AD a nevet \<a\<protokoll formátumában>://>. Azt javasoljuk, hogy a szolgáltató\://\<nevét \<https sid>ügyfél>, így konfigurálhatja több SAP Fiori ABAP motorok az Azure AD-ben.

    ![A frissített szolgáltató név az ABAP System T01/122 konfigurációja SAML 2.0 lapon az SAP-ban](./media/sapfiori-tutorial/tutorial-sapnetweaver-providername.png)

1. Válassza a **Helyi szolgáltató lap** > **Metaadatai**lehetőséget .

1. Az **SAML 2.0 Metaadatok** párbeszédpanelen töltse le a létrehozott metaadat-XML-fájlt, és mentse a számítógépre.

    ![A Metaadatok letöltése hivatkozás az SAP SAML 2.0 Metaadatok párbeszédpanelen](./media/sapfiori-tutorial/tutorial-sapnetweaver-generatesp.png)

1. Az [Azure Portalon](https://portal.azure.com/)az **SAP Fiori alkalmazásintegrációs** lapon keresse meg a **Kezelés szakaszt,** és válassza ki **az egyszeri bejelentkezést.**
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban, ha **a Szolgáltató metaadatfájlja**található, hajtsa végre az alábbi lépéseket:

    a. Kattintson **a Metaadatfájl feltöltése gombra.**

    ![Metaadatfájl feltöltése](common/upload-metadata.png)

    b. Kattintson a **mappaemblémára** a metaadatfájl kijelöléséhez, majd a **Feltöltés gombra.**

    ![metaadatfájl kiválasztása](common/browse-upload-metadata.png)

    c. A metaadatfájl sikeres feltöltésekén az **azonosító** és a **válasz URL-értékei** automatikusan kitöltődnek az **Egyszerű SAML konfiguráció** ablaktáblán. A **Bejelentkezési URL-cím** mezőbe írjon be `https:\//\<your company instance of SAP Fiori\>`egy URL-címet, amely a következő mintát követi: .

    > [!NOTE]
    > Néhány ügyfél a helytelenül konfigurált **Válasz URL-értékekkel** kapcsolatos hibákat jelent. Ha ez a hiba jelenik meg, a következő PowerShell-parancsfájl segítségével állíthatja be a megfelelő válasz URL-címet a példányhoz:
    >
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > 
    > Beállíthatja az `ServicePrincipal` objektum-azonosítót saját maga a parancsfájl futtatása előtt, vagy itt adhatja át.

1. Az SAP Fiori alkalmazás elvárja, hogy az SAML-állítások egy adott formátumban legyenek. Konfigurálja az alkalmazás következő jogcímeket. Ezeknek az attribútumértékeknek a kezeléséhez az **Egyszeri bejelentkezés beállítása SAML-lel** ablaktáblán válassza a **Szerkesztés**lehetőséget.

    ![A Felhasználói attribútumok ablaktábla](common/edit-attribute.png)

1. A **Felhasználói attribútumok & jogcímek** ablaktáblán konfigurálja az SAML token attribútumait az előző képen látható módon. Ezután hajtsa végre a következő lépéseket:

    1. A **Szerkesztés gombra** a **Felhasználói jogcímek kezelése** ablaktábla megnyitásához válassza a Szerkesztés lehetőséget.

    1. Az **Átalakítás** listában válassza **a ExtractMailPrefix() (ExtractMailPrefix() (ExtractMailPrefix() (ExtractMailPrefix() (ExtractMailPrefix() (Extract**

    1. Az **1.** **user.userprincipalname**

    1. Kattintson a **Mentés** gombra.

       ![A Felhasználói jogcímek kezelése ablaktábla](./media/sapfiori-tutorial/nameidattribute.png)

       ![A Felhasználói jogcímek kezelése ablaktábla Átalakítási szakasza](./media/sapfiori-tutorial/nameidattribute1.png)
    
1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az SAML aláíró tanúsítvány szakaszban keresse meg az **összevonási** **metaadatok XML-jét,** és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. Az **SAP Fiori beállítása** szakaszban másolja a megfelelő URL-cím(ek) a követelmény alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy tesztfelhasználót hoz létre az Azure Portalon B.Simon néven.

1. Az Azure Portal bal oldali ablaktáblájában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd válassza az **Összes felhasználó**lehetőséget.
1. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.
1. A **Felhasználói** tulajdonságok csoportban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőbe írja username@companydomain.extensionbe a mezőt. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet, majd írja le a **Jelszó** mezőben megjelenő értéket.
   1. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés az SAP Fiori használatával.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza az **SAP Fiori**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-sap-fiori-sso"></a>SAP Fiori SSO konfigurálása

1. Jelentkezzen be az SAP rendszerbe, és lépjen az **SAML2**tranzakciós kódra. Megnyílik egy új böngészőablak az SAML konfigurációs lappal.

1. Ha végpontokat szeretne konfigurálni egy megbízható identitásszolgáltatóhoz (Azure AD), válassza a **Megbízható szolgáltatók** lapot.

    ![A Megbízható szolgáltatók lap az SAP-ban](./media/sapfiori-tutorial/tutorial-sapnetweaver-samlconfig.png)

1. Válassza **a Hozzáadás**lehetőséget, majd a helyi menü **Metaadat-fájl feltöltése** parancsát.

    ![A Metaadat-fájl hozzáadása és feltöltése beállítások az SAP-ban](./media/sapfiori-tutorial/tutorial-sapnetweaver-uploadmetadata.png)

1. Töltse fel az Azure Portalon letöltött metaadatfájlt. Válassza a **Tovább lehetőséget.**

    ![Válassza ki az SAP-ban feltöltendő metaadatfájlt](./media/sapfiori-tutorial/tutorial-sapnetweaver-metadatafile.png)

1. A következő lapon az **Alias** mezőbe írja be az alias nevét. Például **aadsts**. Válassza a **Tovább lehetőséget.**

    ![Az Alias mező az SAP-ban](./media/sapfiori-tutorial/tutorial-sapnetweaver-aliasname.png)

1. Győződjön meg arról, hogy a **Kivonatoló algoritmus** mező értéke **SHA-256**. Válassza a **Tovább lehetőséget.**

    ![A Kivonatoló algoritmus érték ellenőrzése az SAP-ban](./media/sapfiori-tutorial/tutorial-sapnetweaver-identityprovider.png)

1. Az **Egyszeri bejelentkezési végpontok csoportban**válassza a **HTTP POST**lehetőséget, majd a **Tovább**lehetőséget.

    ![Egyszeri bejelentkezési végpontok beállításai az SAP-ban](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect.png)

1. Az **Egy kijelentkezési végpontok csoportban**válassza a **HTTP Átirányítás**lehetőséget, majd a **Tovább**lehetőséget.

    ![Egyetlen kijelentkezési végpontok beállításai az SAP-ban](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect1.png)

1. A **Műtermék végpontjai csoportban**válassza a **Tovább** lehetőséget a folytatáshoz.

    ![Műtermék-végpontok beállításai az SAP-ban](./media/sapfiori-tutorial/tutorial-sapnetweaver-artifactendpoint.png)

1. A **Hitelesítési követelmények csoportban**válassza a **Befejezés**lehetőséget.

    ![A hitelesítési követelmények beállításai és a Befejezés beállítás az SAP-ban](./media/sapfiori-tutorial/tutorial-sapnetweaver-authentication.png)

1. Válassza **a Megbízható szolgáltató** > **identitás-összevonását** (a lap alján). Válassza a **Szerkesztés** elemet.

    ![A megbízható szolgáltató és az identitás-összevonás lap az SAP-ban](./media/sapfiori-tutorial/tutorial-sapnetweaver-trustedprovider.png)

1. Válassza a **Hozzáadás** lehetőséget.

    ![Az Identitásösszevonás lap Hozzáadás i adja hozzá](./media/sapfiori-tutorial/tutorial-sapnetweaver-addidentityprovider.png)

1. A **Támogatott Névazonosító-formátumok** párbeszédpanelen válassza **a Meghatározatlan**lehetőséget. Válassza **az OK gombot.**

    ![A Támogatott NameID-formátumok párbeszédpanel és -beállítások az SAP-ban](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameid.png)

    A **felhasználói azonosító forrás** és a felhasználói azonosító **leképezési mód értékei** határozzák meg az SAP-felhasználó és az Azure AD-jogcím közötti kapcsolatot.  

    **1. forgatókönyv:** SAP-felhasználó az Azure AD-felhasználói leképezéshez

    1. Az SAP-ban **a "Meghatározatlan" NameID formátum részletei**csoportban vegye figyelembe a részleteket:

        ![A "Meghatározatlan" nameId formátum részletei párbeszédpanel az SAP-ban](./media/sapfiori-tutorial/nameiddetails.png)

    1. Az Azure Portalon a **felhasználói attribútumok & jogcímek**csoportban vegye figyelembe az Azure AD-ből szükséges jogcímeket.

        ![A Felhasználói attribútumok & jogcímek párbeszédpanel az Azure Portalon](./media/sapfiori-tutorial/claimsaad1.png)

    **2. forgatókönyv:** Válassza ki az SAP felhasználói azonosítót a SU01-ben beállított e-mail-cím alapján. Ebben az esetben az e-mail-azonosítót a SU01-ben kell konfigurálni minden olyan felhasználó számára, akinek sso-t kell igényelnie.

    1.  Az SAP-ban **a "Meghatározatlan" NameID formátum részletei**csoportban vegye figyelembe a részleteket:

        ![A "Meghatározatlan" nameId formátum részletei párbeszédpanel az SAP-ban](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameiddetails1.png)

    1. Az Azure Portalon a **felhasználói attribútumok & jogcímek**csoportban vegye figyelembe az Azure AD-ből szükséges jogcímeket.

       ![A Felhasználói attribútumok & jogcímek párbeszédpanel az Azure Portalon](./media/sapfiori-tutorial/claimsaad2.png)

1. Válassza a **Mentés**lehetőséget, majd az **Engedélyezés** lehetőséget az identitásszolgáltató engedélyezéséhez.

    ![A Mentés és az Engedélyezés beállítások az SAP-ban](./media/sapfiori-tutorial/configuration1.png)

1. Amikor a program **kéri,** válassza az OK gombot.

    ![Az OK beállítás az SAML 2.0 konfigurációpárbeszédpanelén az SAP-ban](./media/sapfiori-tutorial/configuration2.png)

### <a name="create-sap-fiori-test-user"></a>SAP Fiori tesztfelhasználó létrehozása

Ebben a szakaszban hozzon létre egy britta Simon nevű felhasználót az SAP Fiori-ban. Együttműködve a házon belüli SAP szakértői csapat vagy a szervezet SAP-partner a felhasználó hozzáadása az SAP Fiori platformon.

## <a name="test-sso"></a>SSO tesztelése

1. Miután az Identitásszolgáltató, az Azure AD aktiválva van az SAP Fiori programban, próbálja meg elérni az alábbi URL-címek egyikét az egyszeri bejelentkezés teszteléséhez (nem kell felhasználónevet és jelszót kérnie):

    * https:\//\<sapurl\>/sap/bc/bsp/sap/it00/default.htm
    * https:\//\<sapurl\>/sap/bc/bsp/sap/it00/default.htm

    > [!NOTE]
    > Cserélje le *a sapurl-t* a tényleges SAP állomásnévre.

1. A teszt URL-cím kell elvinnie a következő tesztalkalmazás-lap az SAP-ban. Ha a lap megnyílik, az Azure AD egyszeri bejelentkezés sikeresen be van állítva.

    ![A szabványos tesztalkalmazás-oldal az SAP-ban](./media/sapfiori-tutorial/testingsso.png)

1. Ha a rendszer felhasználónevet és jelszót kér, engedélyezze a nyomkövetést a probléma diagnosztizálása érdekében. Használja a következő URL-t\//\<a\>nyomkövetéshez: https: sapurl /sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN#.

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki az SAP Fiori-t az Azure AD-vel](https://aad.portal.azure.com/)

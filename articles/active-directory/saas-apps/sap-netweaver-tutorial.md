---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az SAP NetWeaverrel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az SAP NetWeaver között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 1b9e59e3-e7ae-4e74-b16c-8c1a7ccfdef3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 700f2ca4d46b3483531fa0784cb78699befb20ca
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78897737"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-netweaver"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az SAP NetWeaverrel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az SAP NetWeavert az Azure Active Directoryval (Azure AD). Ha integrálja az SAP NetWeavert az Azure AD-vel, a következőket teheti:

* Az Azure AD-ben, aki hozzáfér az SAP NetWeaver.
* Engedélyezze, hogy a felhasználók automatikusan bejelentkezve az SAP NetWeaver az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* SAP NetWeaver egyszeri bejelentkezés (SSO) engedélyezve van az előfizetés.
* SAP NetWeaver V7.20 szükséges atleast

## <a name="scenario-description"></a>Forgatókönyv leírása

Az SAP NetWeaver támogatja mind az **SAML** (**SP által kezdeményezett SSO)** és **az OAuth -ot.** Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben. 

> [!NOTE]
> Konfigurálja az alkalmazást SAML-ben vagy OAuth-ban a szervezeti követelményeknek megfelelően. 

## <a name="adding-sap-netweaver-from-the-gallery"></a>SAP NetWeaver hozzáadása a galériából

Az SAP NetWeaver Azure AD-be való integrációjának konfigurálásához hozzá kell adnia az SAP NetWeavert a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **katalógus hozzáadásszakaszában** írja be az **SAP NetWeaver** kifejezést a keresőmezőbe.
1. Válassza ki **az SAP NetWeaver** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-netweaver"></a>Konfigurálja és tesztelje az Azure AD egyszeri bejelentkezését az SAP NetWeaver számára

Konfigurálja és tesztelje az Azure AD SSO-t az SAP NetWeaver segítségével egy **B.Simon**nevű tesztfelhasználó használatával. Az SSO működéséhez létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó az SAP NetWeaver.

Az Azure AD SSO SAP NetWeaver rel való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD SSO-t,](#configure-azure-ad-sso)** hogy a felhasználók használhassák ezt a funkciót.
    1. **[Hozzon létre egy Azure AD-tesztfelhasználót](#create-an-azure-ad-test-user)** az Azure AD egyszeri bejelentkezésének teszteléséhez B.Simon nal.
    1. **[Rendelje hozzá az Azure AD tesztfelhasználót,](#assign-the-azure-ad-test-user)** hogy b.Simon az Azure AD egyszeri bejelentkezést használhasson.
1. **[Konfigurálja az SAP NetWeaver saml használatával](#configure-sap-netweaver-using-saml)** az SSO-beállítások konfigurálásához az alkalmazás oldalon.
    1. **[Hozzon létre SAP NetWeaver tesztfelhasználó,](#create-sap-netweaver-test-user)** hogy egy megfelelője B.Simon az SAP NetWeaver, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[Tesztelje az SSO-t,](#test-sso)** hogy ellenőrizze, működik-e a konfiguráció.
1. **[Konfigurálja az SAP NetWeaver oauth-ra](#configure-sap-netweaver-for-oauth)** konfigurálja az OAuth-beállításokat az alkalmazás oldalon.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához az SAP NetWeaver rel hajtsa végre a következő lépéseket:

1. Nyisson meg egy új böngészőablakot, és jelentkezzen be az SAP NetWeaver vállalati webhelyére rendszergazdaként

1. Győződjön meg arról, hogy **a http-** és **https-szolgáltatások** aktívak, és az **SMICM** T-code megfelelő portjai vannak hozzárendelve.

1. Jelentkezzen be az SAP System (T01) üzleti ügyfélszolgáltatásba, ahol az SSO-ra van szükség, és aktiválja a HTTP security session Management szolgáltatást.

    a. Nyissa meg a Tranzakciókód **SICF_SESSIONS.** Megjeleníti az összes releváns profilparamétert az aktuális értékekkel. Úgy néznek ki, mint az alábbiakban: -
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
    > Állítsa be a fenti paramétereket, mint egy a szervezeti követelmények, A fenti paraméterek itt megadott jelzésként csak.

    b. Ha szükséges, módosítsa a paramétereket, az SAP-rendszer példányban/alapértelmezett profiljában, és indítsa újra az SAP-rendszert.

    c. Kattintson duplán a megfelelő ügyfélre a HTTP biztonsági munkamenet engedélyezéséhez.

    ![A tanúsítvány letöltési hivatkozása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_profileparameter.png)

    d. Aktiválás a SICF-szolgáltatások alatt:
    ```
    /sap/public/bc/sec/saml2
    /sap/public/bc/sec/cdc_ext_service
    /sap/bc/webdynpro/sap/saml2
    /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
    ```
1. Nyissa meg az **SAML2** tranzakciókódot az SAP-rendszer üzleti kliensében [T01/122]. Megnyílik egy felhasználói felület a böngészőben. Ebben a példában azt feltételeztük, 122 SAP üzleti ügyfél.

    ![A tanúsítvány letöltési hivatkozása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_sapbusinessclient.png)

1. Adja meg felhasználónevét és jelszavát, hogy belépjen a felhasználói felületre, és kattintson **a Szerkesztés gombra.**

    ![A tanúsítvány letöltési hivatkozása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_userpwd.png)

1. Cserélje le **a szolgáltató nevét** T01122-ről `http://T01122` a Mentés **gombra.**

    > [!NOTE]
    > Alapértelmezés szerint a `<sid><client>` szolgáltató neve formátumként érkezik, de `<protocol>://<name>`az Azure AD `https://<sid><client>` a név rezisztiorát várja, és azt javasolja, hogy a szolgáltató neve maradjon fenn, hogy több SAP NetWeaver ABAP-motor konfigurálható az Azure AD-ben.

    ![A tanúsítvány letöltési hivatkozása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_providername.png)

1. **Generálása Szolgáltató Metaadatok**:- Miután elkészült a konfigurálása a **helyi szolgáltató** és **a megbízható szolgáltatók** beállításait SAML 2.0 felhasználói felület, a következő lépés az lenne, hogy létrehoz a szolgáltató metaadat-fájl (amely tartalmazza az összes beállítást, hitelesítési környezetek és egyéb konfigurációk SAP). A fájl létrehozása után fel kell töltenünk ezt az Azure AD-ben.

    ![A tanúsítvány letöltési hivatkozása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_generatesp.png)

    a. Nyissa meg a **Helyi szolgáltató lapot**.

    b. Kattintson a **Metadata gombra.**

    c. Mentse a létrehozott **metaadat-XML-fájlt** a számítógépre, és töltse fel az **Egyszerű SAML konfiguráció** szakaszban az **azonosító** és a **válasz URL-értékeinek** automatikus feltöltéséhez az Azure Portalon.

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)az **SAP NetWeaver** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza az **Egyszeri bejelentkezés**lehetőséget.
1. Az **Egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban, ha az alkalmazást **IDP** által kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépést:

    a. Kattintson **a Metaadatfájl feltöltése gombra** a korábban beszerzett **Szolgáltató metaadatfájljának**feltöltéséhez.

    b. Kattintson a **mappaemblémára** a metaadatfájl kijelöléséhez, majd a **Feltöltés gombra.**

    c. A metaadatfájl sikeres feltöltése után az **azonosító** és a **válasz URL-értéke** automatikusan feltöltődik az **Egyszerű SAML konfigurációs** szakasz szövegmezőjében az alábbi módon:

    d. A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<your company instance of SAP NetWeaver>`

    > [!NOTE]
    > Láttuk, hogy kevés ügyfél jelenti a példányhoz konfigurált helytelen válasz URL-címet. Ha ilyen hibát kap, a következő PowerShell-parancsfájl segítségével a megfelelő válasz URL-címet állíthatja be a példányhoz.:
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > ServicePrincipal object id kell beállítani magát először, vagy átadhatja, hogy itt is.

1. Az SAP NetWeaver alkalmazás az SAML-állításokat egy adott formátumban várja, amely megköveteli, hogy egyéni attribútumleképezéseket adjon hozzá az SAML token attribútumok konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Kattintson a **Szerkesztés** ikonra a Felhasználói attribútumok párbeszédpanel megnyitásához.

    ![image](common/edit-attribute.png)

1. A **Felhasználói attribútumok** párbeszédpanel **Felhasználói jogcímek** szakaszában konfigurálja az SAML token attribútumot a fenti képen látható módon, és hajtsa végre a következő lépéseket:

    a. Kattintson a **Szerkesztés ikonra** a **Felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    ![image](./media/sapnetweaver-tutorial/nameidattribute.png)

    ![image](./media/sapnetweaver-tutorial/nameidattribute1.png)

    b. Az **Átalakítás** listában válassza **a ExtractMailPrefix() (ExtractMailPrefix() (ExtractMailPrefix() (ExtractMailPrefix() (ExtractMailPrefix() (Átalakítási**

    c. Az **1.** **user.userprincipalname**

    d. Kattintson a **Mentés** gombra.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az SAML aláíró tanúsítvány szakaszban keresse meg az **összevonási** **metaadatok XML-jét,** és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

   ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. Az **SAP NetWeaver beállítása** szakaszban másolja a megfelelő URL-cím(ek) a követelmény alapján.

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés az SAP NetWeaver használatával.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza az **SAP NetWeaver**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-sap-netweaver-using-saml"></a>SAP NetWeaver konfigurálása SAML használatával

1. Jelentkezzen be az SAP-rendszerbe, és lépjen az SAML2 tranzakciós kódra. Új böngészőablakot nyit meg SAML konfigurációs képernyővel.

2. A végfelhasználói pontok konfigurálása a megbízható identitásszolgáltató (Azure AD) lapon **a Megbízható szolgáltatók** lapon.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_samlconfig.png)

3. Nyomja **le a Hozzáadás parancsot,** és válassza a helyi menü **Metaadatfájl feltöltése** parancsát.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_uploadmetadata.png)

4. Töltse fel a metaadatfájlt, amelyet az Azure Portalról töltött le.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_metadatafile.png)

5. A következő képernyőn írja be az Alias nevét. Például aadsts és nyomja meg a **Tovább** gombot a folytatáshoz.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_aliasname.png)

6. Győződjön meg arról, hogy a **Kivonatoló algoritmusnak** **SHA-256-nak** kell lennie, és nem igényel semmilyen módosítást, és nyomja meg a **Tovább**gombot.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_identityprovider.png)

7. Az **egyszeri bejelentkezési végpontokon**használja a **HTTP POST** protokollt, és a folytatáshoz kattintson a **Tovább** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect.png)

8. Az **Egykijelentkezési végpontok on** válassza a **HTTPRedirect** lehetőséget, és a **folytatáshoz** kattintson a Tovább gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect1.png)

9. A **műtermék végpontjain**nyomja le a **Tovább** billentyűt a folytatáshoz.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_artifactendpoint.png)

10. A **hitelesítési követelmények en**kattintson a Befejezés **gombra.**

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_authentication.png)

11. Ugrás a **Megbízható szolgáltató** > **identitásösszevonása** lapra (a képernyő aljáról). Kattintson a **Szerkesztés** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_trustedprovider.png)

12. Kattintson a **Hozzáadás** gombra az **Identitásösszevonás** lap (alsó ablak) alatt.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_addidentityprovider.png)

13. Az előugró ablakban válassza a **Meghatározatlan** lehetőséget a **Támogatott névazonosító-formátumok** közül, és kattintson az OK gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameid.png)

14. Vegye figyelembe, hogy **a felhasználói azonosító forrás-** és **felhasználói azonosító leképezési mód értékei** határozzák meg az SAP-felhasználó és az Azure AD-jogcím közötti kapcsolatot.  

    #### <a name="scenario-sap-user-to-azure-ad-user-mapping"></a>Forgatókönyv: SAP-felhasználó az Azure AD felhasználói leképezés.

    a. NameID részletek screenshot az SAP-tól.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/nameiddetails.png)

    b. Képernyőkép az Azure AD-ből szükséges jogcímekről.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/claimsaad1.png)

    #### <a name="scenario-select-sap-user-id-based-on-configured-email-address-in-su01-in-this-case-email-id-should-be-configured-in-su01-for-each-user-who-requires-sso"></a>Eset: Válassza ki az SAP felhasználói azonosítót az SU01-ben beállított e-mail-cím alapján. Ebben az esetben az e-mail-azonosítót minden olyan felhasználó számára konfigurálni kell a su01-ben, akinek sso-t kell igényelnie.

    a.  NameID részletek screenshot az SAP-tól.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameiddetails1.png)

    b. képernyőkép, amely az Azure AD-től szükséges jogcímeket említi.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/claimsaad2.png)

15. Kattintson a **Mentés,** majd **az Engedélyezés** gombra az identitásszolgáltató engedélyezéséhez.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/configuration1.png)

16. A rákérdezés után kattintson **az OK** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/configuration2.png)

    ### <a name="create-sap-netweaver-test-user"></a>SAP NetWeaver tesztfelhasználó létrehozása

    Ebben a szakaszban hozzon létre egy B.simon nevű felhasználót az SAP NetWeaver alkalmazásban. Kérjük, működjön együtt a házon belül SAP szakértői csapat, vagy a szervezet SAP-partnerrel együttműködve adja hozzá a felhasználókat az SAP NetWeaver platformon.

## <a name="test-sso"></a>SSO tesztelése

1. Az Azure AD identitásszolgáltató aktiválása után próbálja meg elérni az URL-cím alatt az Egyszeri bejelentkezést (nem lesz rákérdezés a felhasználónév& jelszó)

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    (vagy) használja az alábbi URL-t

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    > [!NOTE]
    > Cserélje le a sapurl-t a tényleges SAP állomásnévre.

2. A fenti URL-t kell, hogy az alábbi képernyőn. Ha képes elérni az alábbi oldalon, az Azure AD SSO beállítás sikeresen befejeződött.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/testingsso.png)

3. Ha felhasználónév & jelszókérése, diagnosztizálja a problémát az alábbi URL-cím használatával engedélyezze a nyomkövetést.

    `https://<sapurl>/sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN#`

## <a name="configure-sap-netweaver-for-oauth"></a>SAP NetWeaver konfigurálása OAuth-hoz

1. Az SAP által dokumentált folyamat elérhető a következő helyen: [NetWeaver átjárószolgáltatás engedélyezése és OAuth 2.0 hatókör létrehozása](https://wiki.scn.sap.com/wiki/display/Security/NetWeaver+Gateway+Service+Enabling+and+OAuth+2.0+Scope+Creation)

2. Nyissa meg a SPRO-t, és keresse meg **a Szolgáltatások aktiválása és karbantartása című.**

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/oauth01.png)

3. Ebben a példában szeretnénk csatlakozni `DAAG_MNGGRP` az OData szolgáltatás: az OAuth az Azure AD SSO. Használja a műszaki szolgáltatás nevét `DAAG_MNGGRP` keressen a szolgáltatásra, és `green` aktiválja, ha még nem aktív, már (az állapot keresése az ICF-csomópontok lapon). Győződjön meg arról, hogy a rendszeralias (a csatlakoztatott háttérrendszer, ahol a szolgáltatás ténylegesen fut) helyes.Ensure if system alias (the connected backend system, where the service actually running) is correct.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/oauth02.png)

    * Ezután kattintson a nyomógombra **OAuth** a felső gomb sávés hozzárendelése `scope` (tartsa alapértelmezett nevét ajánlott).

4. Példánkban a `DAAG_MNGGRP_001`hatókör , a szolgáltatás nevéből jön létre egy szám automatikus hozzáadásával. A `/IWFND/R_OAUTH_SCOPES` jelentés segítségével módosíthatja a hatókör nevét, vagy manuálisan hozhat létre.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/oauth03.png)

    > [!NOTE]
    > Üzenet `soft state status is not supported` – lehet figyelmen kívül hagyni, mivel nem probléma. További részletekért [itt](https://help.sap.com/doc/saphelp_nw74/7.4.16/1e/c60c33be784846aad62716b4a1df39/content.htm?no_cache=true) olvashat.

### <a name="create-a-service-user-for-the-oauth-20-client"></a>Szolgáltatásfelhasználó létrehozása az OAuth 2.0 ügyfélhez

1. Az OAuth2 `service ID` egy segítségével kapja meg a hozzáférési jogkivonatot a végfelhasználó nevében. Fontos korlátozás az OAuth `OAuth 2.0 Client ID` design szerint: `username` a rendszernek meg kell egyeznie az OAuth 2.0 ügyfél bejelentkezési jogkivonat kérésekor használt ügyféllel. Ezért példánkban egy ÜGYFÉL1 nevű OAuth 2.0-s ügyfelet fogunk regisztrálni, és előfeltételként az SAP-rendszerben azonos nevű felhasználónak (CLIENT1) kell léteznie, és ezt a felhasználót úgy fogjuk konfigurálni, hogy az említett alkalmazás használja. 

2. Az OAuth ügyfél regisztrálásakor a `SAML Bearer Grant type`.

    >[!NOTE]
    >További részletekért lásd az OAuth 2.0 ügyfélregisztrációt az SAML bearer grant típushoz [itt](https://wiki.scn.sap.com/wiki/display/Security/OAuth+2.0+Client+Registration+for+the+SAML+Bearer+Grant+Type)

3. tcod: SU01 / hozzon `System type` létre felhasználó CLIENT1, és jelszót rendelni, mentse, mint kell, hogy a hitelesítő adatokat az API programozó, aki éget azt a felhasználónevet a hívó kódot. Nem kell profilt vagy szerepkört hozzárendelni.

### <a name="register-the-new-oauth-20-client-id-with-the-creation-wizard"></a>Az új OAuth 2.0 ügyfélazonosító regisztrálása a létrehozási varázslóval

1. Új **OAuth 2.0 ügyfél** indítási **tranzakciós SOAUTH2 regisztrálása**. A tranzakció áttekintést jelenít meg a már regisztrált OAuth 2.0 ügyfelekről. Válassza a **Létrehozás gombot** az ügyfél1 névvel ellátott új OAuth-ügyfél varázslójának elindításához ebben a példában.

2. Nyissa meg a T-kód: **SOAUTH2** és adja meg a leírást, majd kattintson a **következő gombra**.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/oauth04.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/oauth05.png)

3. Válassza ki a már hozzáadott **SAML2 IdP – Azure AD** a legördülő listából, és mentse.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/oauth06.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/oauth07.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/oauth08.png)

4. A korábban létrehozott hatókör hozzáadásához kattintson a **Hatókör-hozzárendelés** hozzáadása elemre:`DAAG_MNGGRP_001`

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/oauth09.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/oauth10.png)

5. Kattintson **a befejezés gombra.**

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki az SAP NetWeaver t az Azure AD-vel](https://aad.portal.azure.com/)
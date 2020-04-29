---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az SAP Fiori szolgáltatással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és az SAP Fiori között.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "78897763"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-fiori"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az SAP Fiorital

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az SAP Fioriot Azure Active Directory (Azure AD) használatával. Az SAP Fiori Azure AD-val való integrálásakor a következőket teheti:

* Az SAP Fiorihoz hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek az SAP Fioriba az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Az SAP Fiori egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* Az SAP Fiori támogatja az **SP** által KEZDEMÉNYEZett SSO-t

> [!NOTE]
> Az SAP Fiori által kezdeményezett iFrame-hitelesítéshez ajánlott a **IsPassive** paraméter használata az SAML-AuthnRequest a csendes hitelesítéshez. A **IsPassive** paraméterrel kapcsolatos további részletekért tekintse meg az [Azure ad SAML egyszeri bejelentkezésre](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-saml-protocol) vonatkozó információkat.

## <a name="adding-sap-fiori-from-the-gallery"></a>SAP Fiori hozzáadása a katalógusból

Az SAP Fiori Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az SAP Fiori szolgáltatást a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be az **SAP Fiori** kifejezést a keresőmezőbe.
1. Válassza az **SAP Fiori** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-fiori"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése az SAP Fiori szolgáltatáshoz

Konfigurálja és tesztelje az Azure AD SSO-t az SAP Fiori segítségével egy **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között az SAP Fioriban.

Az Azure AD SSO SAP Fiori használatával történő konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[SAP Fiori SSO konfigurálása](#configure-sap-fiori-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[SAP Fiori-teszt felhasználó létrehozása](#create-sap-fiori-test-user)** – ha az SAP Fioriban található, a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-ügyféllel rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. Nyisson meg egy új böngészőablakot, és jelentkezzen be az SAP Fiori vállalati webhelyre rendszergazdaként.

1. Győződjön meg arról, hogy a **http** -és a **https** -szolgáltatások aktívak, és hogy a kapcsolódó portok hozzá vannak rendelve a tranzakciós kód **SMICM**.

1. Jelentkezzen be az SAP Business Client for SAP System **T01**, ahol egyszeri bejelentkezés szükséges. Ezután aktiválja a HTTP biztonsági munkamenetek felügyeletét.

    1. Nyissa meg a tranzakciós kódot **SICF_SESSIONS**. Az aktuális értékekkel rendelkező összes releváns profil-paraméter megjelenik. A következő példához hasonlóan néznek ki:

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
        > Állítsa be a paramétereket a szervezeti követelmények alapján. Az előző paramétereket csak példaként adja meg.

    1. Ha szükséges, módosítsa a paramétereket az SAP-rendszer példány (alapértelmezett) profiljába, és indítsa újra az SAP-rendszert.

    1. A HTTP biztonsági munkamenet engedélyezéséhez kattintson duplán a megfelelő ügyfélre.

        ![A megfelelő profil-paraméterek aktuális értékei az SAP-ben](./media/sapfiori-tutorial/tutorial-sapnetweaver-profileparameter.png)

    1. Aktiválja a következő SICF-szolgáltatásokat:

        ```
        /sap/public/bc/sec/saml2
        /sap/public/bc/sec/cdc_ext_service
        /sap/bc/webdynpro/sap/saml2
        /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
        ```

1. Lépjen az SAP System [**T01/122**] üzleti ügyfél **egy saml2** . Megnyílik a konfigurációs felhasználói felület egy új böngészőablakban. Ebben a példában a 122-es SAP-rendszerhez az üzleti ügyfélprogramot használjuk.

    ![Az SAP Fiori üzleti ügyfél bejelentkezési lapja](./media/sapfiori-tutorial/tutorial-sapnetweaver-sapbusinessclient.png)

1. Adja meg felhasználónevét és jelszavát, majd válassza **a bejelentkezés**lehetőséget.

    ![A ABAP System T01/122 oldal SAML 2,0-konfigurációja az SAP-ben](./media/sapfiori-tutorial/tutorial-sapnetweaver-userpwd.png)

1. A **szolgáltató neve** mezőben cserélje le a **T01122** **http\/:/T01122**, majd válassza a **Mentés**lehetőséget.

    > [!NOTE]
    > Alapértelmezés szerint a szolgáltató neve a SID>\< \<Client> formátumban van. Az Azure AD a nevet a Format \<protokoll>://\<Name> néven várja. Javasoljuk, hogy a szolgáltató\://\<nevét HTTPS-SID-ként>\<ügyfél->ként tartsa karban, így több SAP Fiori ABAP motort konfigurálhat az Azure ad-ben.

    ![A frissített szolgáltató neve az SAML 2,0-konfigurációban a ABAP System T01/122 oldalon az SAP-ben](./media/sapfiori-tutorial/tutorial-sapnetweaver-providername.png)

1. Válassza a **helyi szolgáltató lap** > **metaadatok**elemet.

1. A **SAML 2,0 metaadatok** párbeszédpanelen töltse le a generált metaadatok XML-fájlját, és mentse a számítógépre.

    ![A metaadatok letöltése hivatkozásra az SAP SAML 2,0 metaadatainak párbeszédpanelen](./media/sapfiori-tutorial/tutorial-sapnetweaver-generatesp.png)

1. A [Azure Portal](https://portal.azure.com/)az **SAP Fiori** -alkalmazás integrációja lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban, ha **szolgáltatói metaadatokat tartalmazó fájllal**rendelkezik, hajtsa végre a következő lépéseket:

    a. Kattintson a **metaadat-fájl feltöltése**elemre.

    ![Metaadat-fájl feltöltése](common/upload-metadata.png)

    b. Kattintson a **mappa emblémára** a metaadat-fájl kiválasztásához, majd kattintson a **feltöltés**elemre.

    ![metaadat-fájl kiválasztása](common/browse-upload-metadata.png)

    c. A metaadat-fájl feltöltése után a rendszer automatikusan kitölti az **azonosítót** és a **Válasz URL-címeket** az **alapszintű SAML-konfiguráció** ablaktáblán. A **bejelentkezési URL-cím** mezőbe írjon be egy URL-címet, amely a következő `https:\//\<your company instance of SAP Fiori\>`mintával rendelkezik:.

    > [!NOTE]
    > Néhány ügyfél a helytelenül konfigurált **Válasz URL-** értékekkel kapcsolatos hibákat jelez. Ha ezt a hibát látja, a következő PowerShell-parancsfájl használatával állíthatja be a példány helyes válaszának URL-címét:
    >
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > 
    > A parancsfájl futtatása előtt `ServicePrincipal` állíthatja be a objektumazonosítót, vagy itt adhatja át.

1. Az SAP Fiori-alkalmazás azt várja, hogy az SAML-kijelentések meghatározott formátumban legyenek. Konfigurálja a következő jogcímeket ehhez az alkalmazáshoz. Az attribútumérték kezeléséhez az **egyszeri bejelentkezés beállítása SAML-vel** panelen válassza a **Szerkesztés**lehetőséget.

    ![A felhasználói attribútumok panel](common/edit-attribute.png)

1. A **felhasználói attribútumok & jogcímek** ablaktáblán KONFIGURÁLJA az SAML-jogkivonat attribútumait az előző képen látható módon. Ezután hajtsa végre a következő lépéseket:

    1. Válassza a **Szerkesztés** lehetőséget a **felhasználói jogcímek kezelése** ablaktábla megnyitásához.

    1. Az **átalakítás** listában válassza a **ExtractMailPrefix ()** elemet.

    1. Az **1. paraméter** listából válassza a **User. userPrincipalName**elemet.

    1. Kattintson a **Mentés** gombra.

       ![A felhasználói jogcímek kezelése panel](./media/sapfiori-tutorial/nameidattribute.png)

       ![A felhasználói jogcímek kezelése ablaktábla átalakítás szakasza](./media/sapfiori-tutorial/nameidattribute1.png)
    
1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. Az **SAP Fiori beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőben adja meg a username@companydomain.extensionnevet. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás**gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést az SAP Fiori elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza az **SAP Fiori**elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-sap-fiori-sso"></a>SAP Fiori SSO konfigurálása

1. Jelentkezzen be az SAP-szolgáltatásba, és lépjen a tranzakciós kód **egy saml2**. Megnyílik egy új böngészőablak az SAML-konfiguráció oldalával.

1. Egy megbízható identitás-szolgáltató (Azure AD) végpontjának konfigurálásához válassza a **megbízható szolgáltatók** fület.

    ![A megbízható szolgáltatók lap az SAP-ben](./media/sapfiori-tutorial/tutorial-sapnetweaver-samlconfig.png)

1. Válassza a **Hozzáadás**lehetőséget, majd válassza a **metaadatok feltöltése** elemet a helyi menüből.

    ![A metaadatok hozzáadása és feltöltése beállítások az SAP-ben](./media/sapfiori-tutorial/tutorial-sapnetweaver-uploadmetadata.png)

1. Töltse fel a Azure Portal letöltött metaadat-fájlt. Kattintson a **Tovább** gombra.

    ![Válassza ki az SAP-ban feltölteni kívánt metaadat-fájlt](./media/sapfiori-tutorial/tutorial-sapnetweaver-metadatafile.png)

1. A következő lap **alias** mezőjébe írja be az alias nevét. Például: **aadsts**. Kattintson a **Tovább** gombra.

    ![Az SAP alias mezője](./media/sapfiori-tutorial/tutorial-sapnetweaver-aliasname.png)

1. Győződjön meg arról, hogy a **kivonatoló algoritmus** mezőben az **SHA-256**érték szerepel. Kattintson a **Tovább** gombra.

    ![A kivonatoló algoritmus értékének ellenőrzése az SAP-ben](./media/sapfiori-tutorial/tutorial-sapnetweaver-identityprovider.png)

1. Az **egyszeri bejelentkezési végpontok**területen válassza a **http post**lehetőséget, majd kattintson a **tovább**gombra.

    ![Az egyszeri bejelentkezési végpontok beállításai az SAP-ben](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect.png)

1. Az **egyszeri kijelentkezési végpontok**területen válassza a **http-átirányítás**lehetőséget, majd kattintson a **tovább**gombra.

    ![Egyszeri kijelentkezési végpontok beállításai az SAP-ben](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect1.png)

1. Az összetevő- **végpontok**területen **válassza a** tovább lehetőséget a folytatáshoz.

    ![Összetevő-végpontok beállításai az SAP-ben](./media/sapfiori-tutorial/tutorial-sapnetweaver-artifactendpoint.png)

1. A **hitelesítési követelmények**területen válassza a **Befejezés**lehetőséget.

    ![A hitelesítési követelmények beállításai és a befejezési beállítás az SAP-ben](./media/sapfiori-tutorial/tutorial-sapnetweaver-authentication.png)

1. Válassza a **megbízható szolgáltató** > **identitásának összevonása** lehetőséget (a lap alján). Válassza a **Szerkesztés** elemet.

    ![A megbízható szolgáltató és az identitás összevonásának lapja az SAP-ban](./media/sapfiori-tutorial/tutorial-sapnetweaver-trustedprovider.png)

1. Válassza a **Hozzáadás** lehetőséget.

    ![A Hozzáadás lehetőség az identitás-összevonás lapon](./media/sapfiori-tutorial/tutorial-sapnetweaver-addidentityprovider.png)

1. A **támogatott NameID-formátumok** párbeszédpanelen válassza a **meghatározatlan**lehetőséget. Kattintson az **OK** gombra.

    ![A támogatott NameID-formátumok párbeszédpanel és az SAP beállításai](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameid.png)

    A **felhasználói azonosító forrása** és a **felhasználói azonosító megfeleltetési mód** értékei határozzák meg az SAP-felhasználó és az Azure ad-jogcím közötti kapcsolatot.  

    **1. forgatókönyv**: az SAP-felhasználó és az Azure ad felhasználói leképezése

    1. Az SAP-ben a **"meghatározatlan" NameID-formátum részletei**alatt jegyezze fel a részleteket:

        ![A NameID "meghatározatlan" párbeszédpanelének részletei az SAP-ben](./media/sapfiori-tutorial/nameiddetails.png)

    1. A Azure Portal **felhasználói attribútumok & jogcímek**területen jegyezze fel az Azure ad által igényelt jogcímeket.

        ![A felhasználói attribútumok & jogcímek párbeszédpanel a Azure Portal](./media/sapfiori-tutorial/claimsaad1.png)

    **2. forgatókönyv**: válassza ki az SAP felhasználói azonosítóját a SU01-ben konfigurált e-mail-cím alapján. Ebben az esetben az e-mail-azonosítót a SU01-ben kell konfigurálni minden olyan felhasználó számára, aki egyszeri bejelentkezést igényel.

    1.  Az SAP-ben a **"meghatározatlan" NameID-formátum részletei**alatt jegyezze fel a részleteket:

        ![A NameID "meghatározatlan" párbeszédpanelének részletei az SAP-ben](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameiddetails1.png)

    1. A Azure Portal **felhasználói attribútumok & jogcímek**területen jegyezze fel az Azure ad által igényelt jogcímeket.

       ![A felhasználói attribútumok & jogcímek párbeszédpanel a Azure Portal](./media/sapfiori-tutorial/claimsaad2.png)

1. Válassza a **Mentés**lehetőséget, majd válassza az **Engedélyezés** lehetőséget az identitás-szolgáltató engedélyezéséhez.

    ![A Mentés és engedélyezés lehetőség az SAP-ben](./media/sapfiori-tutorial/configuration1.png)

1. Ha a rendszer kéri, válassza **az OK gombot** .

    ![Az SAP-ben az SAML 2,0 konfiguráció párbeszédpanelen az OK lehetőségre](./media/sapfiori-tutorial/configuration2.png)

### <a name="create-sap-fiori-test-user"></a>SAP Fiori-teszt felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre az SAP Fioriban. A felhasználókat az SAP Fiori platformon is felveheti a munkahelyi SAP-csapatával vagy a szervezete SAP-partnerével.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

1. Miután az Azure AD-szolgáltató aktiválva lett az SAP Fioriban, próbálja meg elérni az alábbi URL-címek egyikét az egyszeri bejelentkezés teszteléséhez (a Felhasználónév és a jelszó megadását nem kell megadnia):

    * https:\//\<sapurl\>/SAP/BC/BSP/SAP/it00/default.htm
    * https:\//\<sapurl\>/SAP/BC/BSP/SAP/it00/default.htm

    > [!NOTE]
    > Cserélje le a *sapurl* -t a tényleges SAP-állomásnévre.

1. A teszt URL-címnek el kell végeznie az SAP-ban az alábbi tesztelési alkalmazás oldalon. Ha megnyílik az oldal, az Azure AD egyszeri bejelentkezés beállítása sikeresen megtörtént.

    ![A standard test Application (alkalmazás tesztelése) oldal az SAP-ben](./media/sapfiori-tutorial/testingsso.png)

1. Ha a rendszer a Felhasználónév és a jelszó megadását kéri, engedélyezze a nyomkövetést a probléma diagnosztizálásához. Használja a következő URL-címet a nyomkövetéshez:\//\<HTTPS\>: sapurl/SAP/BC/WebDynpro/SAP/sec_diag_tool? SAP-Client = 122&SAP-Language = en #.

## <a name="additional-resources"></a>További háttéranyagok

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Az SAP Fiori kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

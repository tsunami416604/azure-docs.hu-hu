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
ms.openlocfilehash: 44ea1d23c63f8d0842cb60711b10a92606746c02
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2019
ms.locfileid: "59011491"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-fiori"></a>Oktatóanyag: Az Azure Active Directory-integráció az SAP Fiori

Ebben az oktatóanyagban elsajátíthatja, hogyan SAP Fiori integrálása az Azure Active Directory (Azure AD).
Az SAP Fiori integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá az SAP Fiori Azure AD-ben.
* Engedélyezheti a felhasználókat, hogy a rendszer automatikusan bejelentkezve SAP Fiori (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Konfigurálja az Azure AD-integráció az SAP Fiori, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a [ingyenes fiókkal](https://azure.microsoft.com/free/)
* Az SAP Fiori egyszeri bejelentkezés engedélyezve van az előfizetés
* Az SAP Fiori V7.20 legalább szükséges

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja az SAP Fiori **SP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-sap-fiori-from-the-gallery"></a>Az SAP Fiori hozzáadása a katalógusból

Az Azure AD-be SAP Fiori-integráció konfigurálásához, hozzá kell SAP Fiori a galériából a felügyelt SaaS-alkalmazások listájára.

**Az SAP Fiori hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **SAP Fiori**válassza **SAP Fiori** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Az SAP Fiori a találatok listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az SAP Fiori nevű tesztfelhasználó alapján **Britta Simon**.
Egyszeri bejelentkezés működjön, az Azure AD-felhasználót és a kapcsolódó felhasználó, az SAP Fiori hivatkozás kapcsolata kell hozható létre.

Az Azure AD egyszeri bejelentkezés az SAP Fiori tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az SAP Fiori egyszeri bejelentkezés konfigurálása](#configure-sap-fiori-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre SAP Fiori-tesztfelhasználót](#create-sap-fiori-test-user)**  – a felhasználó Azure ad-ben reprezentációja kapcsolódó SAP Fiori-megfelelője a Britta Simon rendelkeznie.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Konfigurálja az Azure AD egyszeri bejelentkezés az SAP Fiori, hajtsa végre az alábbi lépéseket:

1. Nyisson meg egy új böngészőablakban, és jelentkezzen be rendszergazdaként a SAP Fiori-vállalati hely

2. Győződjön meg arról, hogy **http** és **https** szolgáltatások aktívak, és a megfelelő portok vannak hozzárendelve az **SMICM** T-kódot.

3. Jelentkezzen be a vállalati ügyfél SAP-rendszer (T01), ahol egyszeri bejelentkezés szükség, és aktiválja a HTTP biztonsági munkamenet felügyeleti.

    a. Lépjen a tranzakció kódot **SICF_SESSIONS**. Megjeleníti az összes releváns profil paramétereinek aktuális értékekkel. Azok az alábbihoz hasonlóan:-
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
    > Állítsa be a fenti paraméterek beállításával a szervezet követelményeinek, a fenti paraméterek vannak megadva itt csak jelzése.

    b. Ha szükséges, módosítsa a paraméterek, a példány/alapértelmezett profil az SAP-rendszerhez, és indítsa újra az SAP-rendszerhez.

    c. Kattintson duplán a megfelelő ügyfél HTTP biztonsági munkamenet engedélyezése.

    ![A tanúsítvány letöltési hivatkozás](./media/sapfiori-tutorial/tutorial-sapnetweaver-profileparameter.png)

    d. Alább SICF szolgáltatások aktiválása:
    ```
    /sap/public/bc/sec/saml2
    /sap/public/bc/sec/cdc_ext_service
    /sap/bc/webdynpro/sap/saml2
    /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
    ```
4. Lépjen a tranzakció kódot **egy SAML2** üzleti ügyfél SAP-rendszer [T01/122]. Egy felhasználói felületet a böngészőben megnyílik. Ebben a példában az SAP business ügyfélként azt feltételezni 122.

    ![A tanúsítvány letöltési hivatkozás](./media/sapfiori-tutorial/tutorial-sapnetweaver-sapbusinessclient.png)

5. Adja meg a felhasználónevét és jelszavát adja meg a felhasználói felületen, és kattintson a **szerkesztése**.

    ![A tanúsítvány letöltési hivatkozás](./media/sapfiori-tutorial/tutorial-sapnetweaver-userpwd.png)

6. Cserélje le **szolgáltatónevet** T01122, a `http://T01122` , majd kattintson a **mentése**.

    > [!NOTE]
    > Alapértelmezett szolgáltató neve szerint jár <sid><client> formátumban, de az Azure AD nevet vár az formátumban <protocol>://<name>, szolgáltató neve megegyezik a https:// fenntartásához ajánló<sid><client> , hogy több SAP Fiori ABAP-motor konfigurálása az Azure ad-ben .

    ![A tanúsítvány letöltési hivatkozás](./media/sapfiori-tutorial/tutorial-sapnetweaver-providername.png)

7. **Service Provider metaadatok generálása**: – Miután konfigurálásával készen vagyunk a **helyi szolgáltató** és **megbízható szolgáltatók** lenne, a SAML 2.0-s felhasználói felület, a következő lépés beállítások Hozzon létre a szolgáltató metaadatait tartalmazó fájl (amely tartalmazná beállításokról, hitelesítési környezetek és egyéb konfigurációk, az SAP-ben). Ha ez a fájl jön létre, fel kell töltenie az Azure ad-ben kell.

    ![A tanúsítvány letöltési hivatkozás](./media/sapfiori-tutorial/tutorial-sapnetweaver-generatesp.png)

    a. Lépjen a **helyi szolgáltató lap**.

    b. Kattintson a **metaadatok**.

    c. Mentse a létrejövő **metaadatainak XML-fájl** a számítógépen, és töltse fel a **alapszintű SAML-konfigurációja** szakasz automatikus feltöltéséhez az **azonosító** és  **Válasz URL-cím** értékeket az Azure Portalon.

8. Az a [az Azure portal](https://portal.azure.com/), az a **SAP Fiori** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

9. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

10. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

11. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    a. Kattintson a **metaadatfájl feltöltése** feltölteni a **szolgáltató metaadatait tartalmazó fájl** , amelyeket korábban szerzett be.

    ![Metaadatfájl feltöltése](common/upload-metadata.png)

    b. Kattintson a **mappa embléma** válassza ki a metaadat-fájlt, és kattintson a **feltöltése**.

    ![metaadatfájl kiválasztása](common/browse-upload-metadata.png)

    c. A metaadatfájl sikeres feltöltése után a **azonosítója** és **válasz URL-cím** értékeket automatikusan az első **alapszintű SAML-konfigurációja** szövegmező szakaszban látható módon alább:

    ![Az SAP Fiori-tartomány és URL-címek egyszeri bejelentkezési adatait](common/sp-identifier-reply.png)

    d. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím:
    `https://<your company instance of SAP Fiori>`

    > [!NOTE]
    > Tapasztalataink szerint valamilyen hiba helytelen válasz URL-cím az áttelepített példány konfigurált jelentéskészítési néhány ügyfélnek. Ha bármely ilyen hibaüzenetet kapja, segítségével a következő PowerShell-parancsfájl, munkahelyi körülbelül állítsa be a helyes válasz URL-cím a példány.:
    ```
    Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    ``` 
    > ServicePrincipal objektumot azonosító először be kell állítani a saját maga, vagy átadható, amely szintén itt.

12. SAP Fiori-alkalmazás a SAML helyességi feltételek vár egy megadott formátumban. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a **felhasználói attribútumok** szakasz alkalmazás integráció lapján. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** gombra kattintva nyissa meg a **felhasználói attribútumok** párbeszédpanel.

    ![image](common/edit-attribute.png)

13. Az a **felhasználói jogcímek** szakaszában a **felhasználói attribútumok** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, a fenti képen látható módon, és hajtsa végre az alábbi lépéseket:

    a. Kattintson a **Szerkesztés ikon** megnyitásához a **kezelheti a felhasználói jogcímek** párbeszédpanel.

    ![image](./media/sapfiori-tutorial/nameidattribute.png)

    ![image](./media/sapfiori-tutorial/nameidattribute1.png)

    b. Az a **átalakítási** listáról válassza ki **ExtractMailPrefix()**.

    c. Az a **paraméter 1** listáról válassza ki **user.userprinicipalname**.

    d. Kattintson a **Save** (Mentés) gombra.

14. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **összevonási metaadatainak XML**  a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

15. Az a **SAP Fiori beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-sap-fiori-single-sign-on"></a>Az SAP Fiori-egyszeri bejelentkezés konfigurálása

1. Jelentkezzen be SAP-rendszerhez, és nyissa meg a tranzakció kódja egy SAML2. Új böngészőablakban nyílik SAML konfigurációs képernyőjén.

2. Megbízható identitás végpontok konfigurálása (Azure AD-) szolgáltató keresse **megbízható szolgáltatók** fülre.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapfiori-tutorial/tutorial-sapnetweaver-samlconfig.png)

3. Nyomja meg **Hozzáadás** válassza **metaadatfájl feltöltése** a helyi menüből.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapfiori-tutorial/tutorial-sapnetweaver-uploadmetadata.png)

4. Az Azure Portalról letöltött metaadatfájl feltöltése.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapfiori-tutorial/tutorial-sapnetweaver-metadatafile.png)

5. A következő képernyőn írja be a Alias nevét. Például a aadsts, és nyomja meg **tovább** folytatásához.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapfiori-tutorial/tutorial-sapnetweaver-aliasname.png)

6. Győződjön meg arról, hogy a **kivonatoló algoritmus** kell **SHA-256** , és nem igényel változtatásokat, és nyomja le az **tovább**.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapfiori-tutorial/tutorial-sapnetweaver-identityprovider.png)

7. A **egyszeri bejelentkezési végpont**, használjon **HTTP POST** kattintson **tovább** folytatásához.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect.png)

8. A **egyszeri kijelentkezési végpont** kiválasztása **HTTPRedirect** kattintson **tovább** folytatásához.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect1.png)

9. A **összetevő végpontok**, nyomja le az **tovább** folytatásához.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapfiori-tutorial/tutorial-sapnetweaver-artifactendpoint.png)

10. A **hitelesítési követelmények**, kattintson a **Befejezés**.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapfiori-tutorial/tutorial-sapnetweaver-authentication.png)

11. Lépjen a lap **szolgáltató megbízható** > **identitás-összevonási** (a képernyő aljáról). Kattintson a **Szerkesztés** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapfiori-tutorial/tutorial-sapnetweaver-trustedprovider.png)

12. Kattintson a **Hozzáadás** alatt a **identitás-összevonási** lap (alsó ablakban).

    ![Egyszeri bejelentkezés konfigurálása](./media/sapfiori-tutorial/tutorial-sapnetweaver-addidentityprovider.png)

13. Az előugró ablakban válassza **meghatározatlan** származó a **NameID támogatott formátumok** kattintson az OK gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameid.png)

14. Vegye figyelembe, hogy **felhasználói azonosító forrás** és **felhasználói azonosító leképezés módban** határozza meg, hogy SAP-felhasználó és az Azure ad-ben jogcím közötti kapcsolat.  

    #### <a name="scenario-sap-user-to-azure-ad-user-mapping"></a>Forgatókönyv: SAP-felhasználó, az Azure AD-felhasználó-hozzárendelés.

    a. SAP-NameID a részletek képernyőkép.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapfiori-tutorial/nameiddetails.png)

    b. Képernyőkép a megemlítve szükséges jogcímek, az Azure ad-ből.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapfiori-tutorial/claimsaad1.png)

    #### <a name="scenario-select-sap-user-id-based-on-configured-email-address-in-su01-in-this-case-email-id-should-be-configured-in-su01-for-each-user-who-requires-sso"></a>Forgatókönyv: Válassza ki az SAP felhasználói azonosító alapján SU01 beállított e-mail-címét. E-mail-azonosító ebben az esetben az összes felhasználója számára szükséges SSO su01 kell konfigurálni.

    a.  SAP-NameID a részletek képernyőkép.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameiddetails1.png)

    b. Képernyőkép a megemlítve szükséges jogcímek, az Azure ad-ből.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapfiori-tutorial/claimsaad2.png)

15. Kattintson a **mentése** majd **engedélyezése** identitásszolgáltató engedélyezéséhez.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapfiori-tutorial/configuration1.png)

16. Kattintson a **OK** egyszer kéri.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapfiori-tutorial/configuration2.png)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be `brittasimon@yourcompanydomain.extension`. Például: BrittaSimon@contoso.com.

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés SAP Fiori Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **SAP Fiori**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **SAP Fiori**.

    ![Az SAP Fiori-hivatkozás alkalmazásainak listájában](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-sap-fiori-test-user"></a>Az SAP Fiori-tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű SAP Fiori-a felhasználó hoz létre. A házon belül SAP szakértői csapat működik, vagy a felhasználók hozzáadása az SAP Fiori-platformon, a szervezet SAP partnerrel együttműködve.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

1. Miután az identitásszolgáltató az Azure AD lett aktiválva, próbálja alatt ellenőrizze az egyszeri bejelentkezési URL-cím elérése (hiba fog kérés nem felhasználónév és jelszó)

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    (vagy) az alábbi URL-cím használata

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    > [!NOTE]
    > Cserélje le tényleges SAP állomásnév sapurl.

2. A fenti URL-címet kell venniük, hogy említett képernyő alatt. Ha tudja elérni, akár az alábbi lapon, az Azure AD egyszeri bejelentkezés beállítása sikeresen történik.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapfiori-tutorial/testingsso.png)

3. Felhasználónév és jelszó kérése esetén a probléma diagnosztizálásához szerint engedélyezze a nyomkövetés, az alábbi URL-címe

    `https://<sapurl>/sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN#`

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
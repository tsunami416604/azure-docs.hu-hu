---
title: 'Oktatóanyag: Az Azure Active Directory-integráció az SAP netweaver megoldással |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezést az SAP NetWeaver és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 1b9e59e3-e7ae-4e74-b16c-8c1a7ccfdef3
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2018
ms.author: jeedes
ms.openlocfilehash: 607d05818966e62407795640d223f1aed2f59bbb
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54156748"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-netweaver"></a>Oktatóanyag: Az Azure Active Directory-integráció az SAP netweaver megoldással

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálható a SAP NetWeaver az Azure Active Directory (Azure AD).

SAP NetWeaver integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá az SAP NetWeaver az Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett az SAP NetWeaver (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Előfeltételek

SAP netweaver az Azure AD-integráció konfigurálásához lesz szüksége a következő elemek:

- Azure AD-előfizetés
- SAP NetWeaver egyszeri bejelentkezés engedélyezve van az előfizetés
- SAP NetWeaver V7.20 legalább szükséges

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.
Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. SAP NetWeaver hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-sap-netweaver-from-the-gallery"></a>SAP NetWeaver hozzáadása a katalógusból

Konfigurálja az integráció az SAP NetWeaver az Azure AD-be, szüksége SAP NetWeaver a katalógusból hozzáadása a felügyelt SaaS-alkalmazások listájában.

**SAP NetWeaver hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **SAP NetWeaver**válassza **SAP NetWeaver** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![SAP NetWeaver a találatok listájában](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az SAP NetWeaver a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó, az SAP NetWeaver mi egy felhasználó számára az Azure ad-ben. Más szóval Azure AD-felhasználót és az SAP NetWeaver a kapcsolódó felhasználó közötti kapcsolat kapcsolatot kell létrehozni.

Az Azure AD egyszeri bejelentkezés az SAP NetWeaver tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[SAP NetWeaver tesztfelhasználó létrehozása](#creating-sapnetweaver-test-user)**  - a-megfelelője a Britta Simon rendelkezik az SAP NetWeaver, amely kapcsolódik az Azure AD felhasználói ábrázolása.
4. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és az SAP NetWeaver alkalmazás egyszeri bejelentkezés konfigurálása.

**Konfigurálja az Azure AD egyszeri bejelentkezés az SAP netweaver megoldással, hajtsa végre az alábbi lépéseket:**

1. Nyisson meg egy új böngészőablakban, és jelentkezzen be az SAP NetWeaver vállalati hely rendszergazdaként

2. Győződjön meg arról, hogy **http** és **https** szolgáltatások aktívak, és a megfelelő portok vannak hozzárendelve az **SMICM** T-kódot.

3. Jelentkezzen be vállalati ügyfél SAP-rendszer (T01), ahol egyszeri bejelentkezés szükség, és HTTP biztonsági munkamenet felügyeleti aktiválása.

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

    ![A tanúsítvány letöltési hivatkozás](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_profileparameter.png)

    d. Alább SICF szolgáltatások aktiválása:
    ```
    /sap/public/bc/sec/saml2
    /sap/public/bc/sec/cdc_ext_service
    /sap/bc/webdynpro/sap/saml2
    /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
    ```
4. Lépjen a tranzakció kódot **egy SAML2** üzleti ügyfél SAP-rendszer [T01/122]. Egy felhasználói felületet a böngészőben megnyílik. Ebben a példában az SAP business ügyfélként azt feltételezni 122.

    ![A tanúsítvány letöltési hivatkozás](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_sapbusinessclient.png)

5. Adja meg a felhasználónevét és jelszavát adja meg a felhasználói felületen, és kattintson a **szerkesztése**.

    ![A tanúsítvány letöltési hivatkozás](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_userpwd.png)

6. Cserélje le **szolgáltatónevet** T01122, a `http://T01122` , majd kattintson a **mentése**.

    > [!NOTE]
    > Alapértelmezett szolgáltató neve szerint jár <sid> <client> formátumban, de az Azure AD nevet vár az formátumban <protocol>://<name>, szolgáltató neve megegyezik a https:// fenntartásához ajánló<sid> <client> , hogy több SAP NetWeaver ABAP motorok konfigurálása az Azure ad-ben.

    ![A tanúsítvány letöltési hivatkozás](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_providername.png)

7. **Service Provider metaadatok generálása**: – Miután konfigurálásával készen vagyunk a **helyi szolgáltató** és **megbízható szolgáltatók** lenne, a SAML 2.0-s felhasználói felület, a következő lépés beállítások Hozzon létre a szolgáltató metaadatait tartalmazó fájl (amely tartalmazná beállításokról, hitelesítési környezetek és egyéb konfigurációk, az SAP-ben). Ha ez a fájl jön létre, fel kell töltenie az Azure ad-ben kell.

    ![A tanúsítvány letöltési hivatkozás](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_generatesp.png)

    a. Lépjen a **helyi szolgáltató lap**.

    b. Kattintson a **metaadatok**.

    c. Mentse a létrejövő **metaadatainak XML-fájl** a számítógépen, és töltse fel a **alapszintű SAML-konfigurációja** polulate automatikus szakasz a **azonosító** és  **Válasz URL-cím** értékeket az Azure Portalon.

8. Az Azure Portalon az a **SAP NetWeaver** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

9. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen kattintson **kiválasztása** a **SAML** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés konfigurálása](common/tutorial_general_301.png)

10. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](common/editconfigure.png)

11. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    a. Kattintson a **metaadatfájl feltöltése** feltölteni a **szolgáltató metaadatait tartalmazó fájl** , amelyeket korábban szerzett be.

    ![Metaadatfájl feltöltése](common/editmetadataupload.png)

    b. Kattintson a **mappa embléma** válassza ki a metaadat-fájlt, és kattintson a **feltöltése**.

    ![Metaadatfájl feltöltése](common/uploadmetadata.png)

    c. A metaadatfájl sikeres feltöltését követően a **azonosító** és **válasz URL-cím** értékeket automatikusan az első **alapszintű SAML-konfigurációja** szövegmező szakaszban, ahogy az alábbi :

    ![SAP NetWeaver tartomány és URL-címek egyszeri bejelentkezési adatait](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_url.png)

    d. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<your company instance of SAP NetWeaver>`

12. SAP NetWeaver alkalmazás a SAML helyességi feltételek vár egy megadott formátumban. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a **felhasználói attribútumok** szakasz alkalmazás integráció lapján. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** gombra kattintva nyissa meg a **felhasználói attribútumok** párbeszédpanel.

    ![Attribútum szakasz](./media/sapnetweaver-tutorial/edit_attribute.png)

13. Az a **felhasználói jogcímek** szakaszában a **felhasználói attribútumok** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, a fenti képen látható módon, és hajtsa végre az alábbi lépéseket:

    a. Kattintson a **szerkesztése** ikonra kattintva nyissa meg a **kezelheti a felhasználói jogcímek** párbeszédpanel.
    
    ![Attribútum szakasz](./media/sapnetweaver-tutorial/nameidattribute.png)

    b. Az a **kezelheti a felhasználói jogcímek** fülre, hajtsa végre az alábbi lépéseket:

    ![Attribútum szakasz](./media/sapnetweaver-tutorial/nameidattribute1.png)

    * Válassza ki **átalakítási**.
  
    * Az a **átalakítási** listáról válassza ki `ExtractMailPrefix()`.
  
    * Az a **paraméter 1** listáról válassza ki `user.userprincipalname`.

    * Kattintson a **Save** (Mentés) gombra.

14. Az a **SAML-aláíró tanúsítvány** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez **összevonási metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_certificate.png)

15. Az a **beállítása az SAP NetWeaver** területén másolja a megfelelő URL-címet a követelmény alapján.

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

    ![SAP NetWeaver-konfiguráció](common/configuresection.png)

16. Jelentkezzen be SAP-rendszerhez, és nyissa meg a tranzakció kódja egy SAML2. Új böngészőablakban nyílik SAML konfigurációs képernyőjén.

17. Megbízható identitás végpontok konfigurálása (Azure AD-) szolgáltató keresse **megbízható szolgáltatók** fülre.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_samlconfig.png)

18. Nyomja meg **Hozzáadás** válassza **metaadatfájl feltöltése** a helyi menüből.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_uploadmetadata.png)

19. Az Azure Portalról letöltött metaadatfájl feltöltése.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_metadatafile.png)

20. A következő képernyőn írja be a Alias nevét. Például a aadsts, és nyomja meg **tovább** folytatásához.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_aliasname.png)

21. Győződjön meg arról, hogy a **kivonatoló algoritmus** kell **SHA-256** , és nem igényel változtatásokat, és nyomja le az **tovább**.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_identityprovider.png)

22. A **egyszeri bejelentkezési végpont**, használjon **HTTP POST** kattintson **tovább** folytatásához.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect.png)

23. A **egyszeri kijelentkezési végpont** kiválasztása **HTTPRedirect** kattintson **tovább** folytatásához.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect1.png)

24. A **összetevő végpontok**, nyomja le az **tovább** folytatásához.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_artifactendpoint.png)

25. A **hitelesítési követelmények**, kattintson a **Befejezés**.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_authentication.png)

26. Lépjen a lap **szolgáltató megbízható** > **identitás-összevonási** (a képernyő aljáról). Kattintson a **Szerkesztés** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_trustedprovider.png)

27. Kattintson a **Hozzáadás** alatt a **identitás-összevonási** lap (alsó ablakban).

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_addidentityprovider.png)

28. Az előugró ablakban válassza **meghatározatlan** származó a **NameID támogatott formátumok** kattintson az OK gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameid.png)

29. Vegye figyelembe, hogy **felhasználói azonosító forrás** és **felhasználói azonosító leképezés módban** határozza meg, hogy SAP-felhasználó és az Azure ad-ben jogcím közötti kapcsolat.  

    ####<a name="scenario-sap-user-to-azure-ad-user-mapping"></a>Forgatókönyv: SAP-felhasználó, az Azure AD-felhasználó-hozzárendelés.

    a. NameID részletek képernyőfelvételen az SAP.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/nameiddetails.png)

    b. Képernyőfelvétel megemlítve szükséges jogcímek, az Azure ad-ből.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/claimsaad1.png)

    ####<a name="scenario-select-sap-user-id-based-on-configured-email-address-in-su01-in-this-case-email-id-should-be-configured-in-su01-for-each-user-who-requires-sso"></a>Forgatókönyv: Válassza ki az SAP felhasználói azonosító alapján SU01 beállított e-mail-címét. E-mail-azonosító ebben az esetben az összes felhasználója számára szükséges SSO su01 kell konfigurálni.

    a.  NameID részletek képernyőfelvételen az SAP.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameiddetails1.png)

    b. Képernyőfelvétel megemlítve szükséges jogcímek, az Azure ad-ből.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/claimsaad2.png)

30. Kattintson a **mentése** majd **engedélyezése** identitásszolgáltató engedélyezéséhez.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/configuration1.png)

31. Kattintson a **OK** egyszer kéri.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/configuration2.png)

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![Az Azure AD-felhasználó létrehozása][100]

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](common/create_aaduser_01.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](common/create_aaduser_02.png)

    a. Az a **neve** írja be a következőt **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be a **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **tulajdonságok**, jelölje be a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Létrehozás** gombra.

### <a name="creating-sap-netweaver-test-user"></a>SAP NetWeaver tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű SAP NetWeaver a felhasználó hoz létre. A házon belül SAP szakértői csapat működik, vagy a felhasználók hozzáadása az SAP NetWeaver platformon, a szervezet SAP partnerrel együttműködve.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon Azure egyszeri bejelentkezést az SAP NetWeaver való hozzáférés biztosítása használja.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**válassza **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201]

2. Az alkalmazások listájában jelölje ki a **SAP NetWeaver**.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_app.png) 

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Az a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelése** gombra.

### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

1. Miután az identitásszolgáltató az Azure AD lett aktiválva, próbálja alatt ellenőrizze az egyszeri bejelentkezési URL-cím elérése (hiba fog kérés nem felhasználónév és jelszó)

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    (vagy) az alábbi URL-cím használata

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    > [!NOTE]
    > Cserélje le tényleges SAP állomásnév sapurl.

2. A fenti URL-címet kell venniük, hogy említett képernyő alatt. Ha tudja elérni, akár az alábbi lapon, az Azure AD egyszeri bejelentkezés beállítása sikeresen történik.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapnetweaver-tutorial/testingsso.png)

3. Felhasználónév és jelszó kérése esetén a probléma diagnosztizálásához szerint engedélyezze a nyomkövetés, az alábbi URL-címe

    `https://<sapurl>/sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN#`

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png

---
title: 'Oktatóanyag: Azure Active Directoryval integrált SAP HANA |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és az SAP HANA között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: cef4a146-f4b0-4e94-82de-f5227a4b462c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: 626dfe07c13c90686157dea710715b333a378136
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>Oktatóanyag: Azure Active Directoryval integrált SAP HANA

Ebben az oktatóanyagban elsajátíthatja SAP HANA integrálása az Azure Active Directory (Azure AD).

Az Azure ad-val integrálásakor SAP HANA töltse le a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér SAP HANA szabályozhatja.
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni be van jelentkezve SAP HANA a saját Azure AD-fiókok.
- A fiók egyetlen központi helyen--az Azure-portálon kezelheti.

Az Azure AD SaaS integrálásáról további információért lásd: [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása SAP HANA-nal, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egyszeri bejelentkezés (SSO) engedélyezve van egy SAP HANA-előfizetéshez
- Bármely nyilvános IaaS fut egy HANA-példány helyszíni, Azure virtuális gép vagy SAP nagy példányok az Azure-ban
- A XSA felügyeleti webes felülete, valamint a HANA Studio telepítve HANA-példányon

> [!NOTE]
> Éles környezetben az SAP HANA teszteléséhez lépéseit az oktatóanyag használatát nem javasoljuk. Az integráció először tesztelje a fejlesztési vagy tesztelési környezetben az alkalmazás, és használja az éles környezetben.

Ez az oktatóanyag lépéseit teszteléséhez hajtsa végre az ezek az ajánlások:

- Ne használja az éles környezetben, nem szükséges.
- [Egy hónapos ingyenes próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/) az Azure AD, ha még nem telepítette az Azure AD próbaverziójának környezetben.

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Az ebben az oktatóanyagban a forgatókönyv két fő építőelemeket áll:

1. SAP HANA hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="add-sap-hana-from-the-gallery"></a>SAP HANA hozzáadása a gyűjteményből
Az Azure AD integrálása a SAP HANA konfigurálásához hozzá SAP HANA a gyűjteményből a kezelt SaaS-alkalmazások listáját.

**A gyűjteményből SAP HANA hozzáadásához tegye a következőket:**

1. Az a [Azure-portálon](https://portal.azure.com), a bal oldali panelen válassza ki a **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Ugrás a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Az új alkalmazás hozzáadásához válassza a **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **SAP HANA**. Válassza ki **SAP HANA** az eredmények panelen. Végül válassza ki a **Hozzáadás** gombra kattintva vegye fel az alkalmazást. 

    ![Az új alkalmazás](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezést az SAP HANA "Britta Simon." nevű tesztfelhasználó alapján

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, aki a párjukhoz felhasználó SAP HANA egy felhasználó számára az Azure AD. Más szóval kell egy Azure AD-felhasználó és a kapcsolódó felhasználó közötti kapcsolatot létesíteni az SAP HANA.

SAP HANA biztosítanak a **felhasználónév** ugyanazt az értéket, az érték a **felhasználónév** az Azure ad-ben. Ez a lépés a két felhasználó közötti kapcsolatot létesít.

Az Azure AD egyszeri bejelentkezést az SAP HANA tesztelése és konfigurálása, végezze el a következő építőelemeket:

1. [Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on) ahhoz, hogy a felhasználók számára a szolgáltatás használatához.
2. [Hozzon létre egy Azure AD-teszt felhasználó](#creating-an-azure-ad-test-user) az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. [Hozzon létre egy SAP HANA tesztfelhasználó](#creating-a-sap-hana-test-user) való Britta Simon megfelelője a egy SAP HANA, amely csatolva van a felhasználó az Azure AD ábrázolását.
4. [Rendelje hozzá az Azure AD-teszt felhasználó](#assigning-the-azure-ad-test-user) Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. [Egyszeri bejelentkezés tesztelése](#testing-single-sign-on) ellenőrzése, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az SAP HANA-alkalmazásban.

**Az Azure AD egyszeri bejelentkezést az SAP HANA megadásához tegye a következőket:**

1. Az Azure portálon a a **SAP HANA** alkalmazás integrációs lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédpanel **SAML-alapú bejelentkezés**, jelölje be **mód**.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_samlbase.png)

3. Az a **SAP HANA-tartomány és az URL-címek** területen tegye a következőket:

    ![Tartomány- és URL-címeket az egyszeri bejelentkezés információk](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_url.png)

    a. Az a **azonosító** mezőbe írja be a következőt: `HA100` 

    b. Az a **válasz URL-CÍMEN** mezőbe írja be a következő mintát olyan URL-címe: `https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc`

    > [!NOTE] 
    > Ezek az értékek nem valódi. A tényleges azonosítójú frissítheti ezeket az értékeket, és válasz URL-CÍMÉT. Lépjen kapcsolatba a [SAP HANA ügyfél-támogatási csoport](https://cloudplatform.sap.com/contact.html) beolvasni ezeket az értékeket. 

4. Az a **SAML-aláíró tanúsítványa** szakaszban jelölje be **metaadatainak XML-kódja**. Mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_certificate.png) 

    >[!Note]
    >Ha a tanúsítvány nem aktív, majd aktiválja az kiválasztásával a **új tanúsítvány aktiválásához** jelölőnégyzetet az Azure ad-ben. 

5. Az SAP HANA-alkalmazást a SAML helyességi feltételek vár egy meghatározott formátumban. Az alábbi képernyőfelvételen látható egy példa ezt a formátumot. 

    Itt azt átnevezte a **felhasználói azonosító** rendelkező a **ExtractMailPrefix()** funkciója **user.mail**. Ez biztosítja, hogy a felhasználó e-mail fiókot, amely a egyedi felhasználói azonosító az előtag értéke Ezt a felhasználói Azonosítót küld a SAP HANA-alkalmazás minden sikeres válaszként.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-saphana-tutorial/attribute.png)

6. Az a **felhasználói attribútumok** szakasza a **egyszeri bejelentkezés** párbeszédpanel mezőben a következő lépéseket:

    a. Az a **felhasználói azonosító** legördülő listában válassza **ExtractMailPrefix**.
    
    b. Az a **Mail** legördülő listában válassza **user.mail**.

7. Válassza ki a **Mentés** gombot.

    ![Az egyszeri bejelentkezés mentési gomb konfigurálása](./media/active-directory-saas-saphana-tutorial/tutorial_general_400.png)
    
8. Egyszeri bejelentkezés konfigurálása a SAP HANA-oldalon, jelentkezzen be a **HANA XSA Webkonzol** nyissa meg a megfelelő HTTPS-végponton.

    > [!NOTE]
    > Az alapértelmezett beállítás az URL-címet átirányítja a kérést egy bejelentkezési képernyő, egy SAP HANA-adatbázis hitelesített felhasználó hitelesítő adatait, amelyhez. A felhasználó számára kérelmezni SAML-alapú felügyeleti feladatok elvégzéséhez engedélyekkel kell rendelkeznie.

9. Lépjen a XSA webes felületen **SAML-Identitásszolgáltatóként**. Ott, válassza ki a **+** gomb megjelenítése a képernyő alján a **identitás szolgáltató adatai hozzáadása** ablaktáblán. Ezután a következő lépéseket:

    ![Identitás-szolgáltató felvétele](./media/active-directory-saas-saphana-tutorial/sap1.png)

    a. Az a **identitás szolgáltató adatai hozzáadása** ablaktáblán, illessze be a metaadatok XML (amely az Azure-portálról letöltött) tartalmát a **metaadatok** mezőbe.

    ![Identitásszolgáltató beállítások hozzáadása](./media/active-directory-saas-saphana-tutorial/sap2.png)

    b. Az XML-dokumentum tartalma érvényes, ha az elemzési folyamat kibontja a vonatkozó szükséges információkat a **tulajdonosa Entitásazonosító és kibocsátó** mezőiben a **általános adatok** terület képernyőn. Is bontja ki a következő URL-cím mezők a szükséges információkat a **cél** terület, például képernyőn a **alap URL-cím és SingleSignOn URL-cím (*)** mezőket.

    ![Identitásszolgáltató beállítások hozzáadása](./media/active-directory-saas-saphana-tutorial/sap3.png)

    c. Az a **neve** jelölését a **általános adatok** területen kattintson a jobb adjon meg egy nevet az új SAML SSO identitásszolgáltató számára.

    > [!NOTE]
    > A SAML IDP nevét kötelező megadni, és egyedinek kell lennie. Úgy tűnik, a rendelkezésre álló SAML IDPs jelenik meg, ha a SAML jelöli ki a hitelesítési módszert az SAP HANA XS alkalmazásokkal való használatra listájában. Például ezt megteheti a **hitelesítési** terület a XS összetevő-felügyeleti eszköz képernyőn.

10. Válassza ki **mentése** menteni a SAML-Identitásszolgáltatóként részleteit, és adja hozzá az új SAML IDP ismert SAML IDPs listája.

    ![Mentés gombja](./media/active-directory-saas-saphana-tutorial/sap4.png)

11. A HANA Studio, a rendszer tulajdonságain belül a **konfigurációs** lapra, amelyet a beállítások szűrése **saml**. Majd szükség esetén módosítsa a **assertion_timeout** a **10 másodpercnél** való **120 másodperc**.

    ![assertion_timeout beállítás](./media/active-directory-saas-saphana-tutorial/sap7.png)

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com) közben állítja be az alkalmazás! Ez az alkalmazás a hozzáadása után a **Active Directory** > **vállalati alkalmazások** szakaszban jelölje be a **egyszeri bejelentkezés** lapra, és a beágyazott eléréséhez a dokumentáció a **konfigurációs** alsó szakasz. További embedded dokumentációjából szolgáltatásáról a [az Azure AD dokumentációjában beágyazott](https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure ad-ben, a következő lépéseket:**

1. Az a **Azure-portálon**, a bal oldali panelen válassza ki a **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-saphana-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**. Válassza ki **minden felhasználó**.
    
    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-saphana-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanelen jelölje ki **Hozzáadás** a párbeszédpanel tetején.
 
    ![A Hozzáadás gombra.](./media/active-directory-saas-saphana-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel mezőben a következő lépéseket:
 
    ![A felhasználó párbeszédpanel](./media/active-directory-saas-saphana-tutorial/create_aaduser_04.png) 

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó**, és jegyezze fel a jelszót.

    d. Kattintson a **Létrehozás** gombra.
 
### <a name="create-a-sap-hana-test-user"></a>SAP HANA tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók SAP HANA bejelentkezni, el kell juttatnia azokat a SAP HANA.
SAP HANA támogatja közvetlenül az időponthoz kötött kiosztást, ami által alapértelmezés szerint engedélyezve van.

Ha a felhasználó manuálisan létrehozásához szükséges, a következő lépéseket:

>[!NOTE]
>A külső hitelesítés a felhasználó által használt módosíthatja. Egy külső rendszer például a Kerberos hitelesítéshez is. Külső identitások kapcsolatos részletes információkért forduljon a [tartományi rendszergazda](https://cloudplatform.sap.com/contact.html).

1. Nyissa meg a [SAP HANA Studio](https://help.sap.com/viewer/a2a49126a5c546a9864aae22c05c3d0e/2.0.01/en-us) rendszergazdaként, majd engedélyezze az adatbázis-felhasználó SAML-alapú egyszeri bejelentkezéshez.

    ![Felhasználó létrehozása](./media/active-directory-saas-saphana-tutorial/sap5.png)

2. Jelölje be a láthatatlan balra **SAML**, majd válassza ki a **konfigurálása** hivatkozásra.

3. Válassza ki **Hozzáadás** a SAML IDP hozzáadni.  Válassza ki a megfelelő SAML IDP, majd a **OK**.

4. Adja hozzá a **külső identitások** (ebben az esetben az BrittaSimon), vagy válasszon **bármely**. Ezután kattintson az **OK** gombra.

    >[!Note]
    >Ha a **bármely** jelölőnégyzet nincs bejelölve, akkor HANA felhasználóneve meg kell egyeznie a felhasználó a felhasználónév, a tartományi utótag elé. (Például BrittaSimon@contoso.com a HANA BrittaSimon válik.)

5. Tesztelési célokra, rendelje hozzá az összes **XS** a felhasználói szerepköröket.

    ![szerepkörök hozzárendelése](./media/active-directory-saas-saphana-tutorial/sap6.png)

    > [!TIP]
    > Csak a használati esetek megfelelő engedélyeket kell adnia.

6. Mentse a felhasználó.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés SAP HANA Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**Az SAP HANA Britta Simon rendeléséhez hajtsa végre az alábbi lépéseket:**

1. Nyissa meg az alkalmazások megtekintése az Azure-portálon. Nyissa meg a könyvtár nézet, és navigáljon a **vállalati alkalmazások**. Válassza ki **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **SAP HANA**.

    ![Felhasználó hozzárendelése](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_app.png) 

3. A bal oldali menüben válasszon ki **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202] 

4. Válassza ki a **Hozzáadás** gombra. Az a **hozzáadása hozzárendelés** párbeszédpanelen jelölje ki **felhasználók és csoportok**.

    ![A hozzárendelés hozzáadása panelen][203]

5. Az a **felhasználók és csoportok** párbeszédpanelen jelölje ki **Britta Simon** a a **felhasználók** listája.

6. Kattintson a **válasszon** gombra a **felhasználók és csoportok** párbeszédpanel megnyitásához.

7. Válassza ki a **hozzárendelése** gombra a **hozzáadása hozzárendelés** párbeszédpanel megnyitásához.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panel segítségével tesztelheti.

Az SAP HANA csempe a hozzáférési panelen válassza ki, amikor, kell beolvasni automatikusan be van jelentkezve az SAP HANA-alkalmazás.
A hozzáférési panel kapcsolatos további információkért lásd: [a hozzáférési panel bemutatása](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_203.png


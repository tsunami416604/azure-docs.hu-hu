---
title: 'Oktatóanyag: Azure Active Directory-integráció az SAP HANA |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezést az SAP HANA és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: cef4a146-f4b0-4e94-82de-f5227a4b462c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: c466e811d868403c59d6615882422996442d792a
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39045827"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>Oktatóanyag: Azure Active Directory-integráció az SAP HANA

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálható a SAP HANA az Azure Active Directory (Azure AD).

SAP HANA és az Azure AD integrálása, kérje le a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá az SAP HANA az Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezik az SAP HANA az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen--az Azure Portalon kezelheti.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az SAP HANA az Azure AD-integráció konfigurálásához lesz szüksége a következő elemek:

- Az Azure AD-előfizetéshez
- Egy SAP HANA-előfizetést, amely egyszeri bejelentkezés (SSO) engedélyezve van
- Bármely nyilvános IaaS-on futó HANA-példány helyszíni, Azure virtuális Gépen vagy SAP nagyméretű példányok az Azure-ban
- A XSA felügyeleti webes felület, valamint a HANA Studio telepítve van a HANA-példányon

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez az SAP Hana éles környezetben használatát nem javasoljuk. Az integráció először tesztelje a fejlesztési vagy az alkalmazás átmeneti környezetben, és az éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez hajtsa végre ezeket a javaslatokat:

- Ne használja éles környezetben, csak szükség esetén.
- [Egy hónapos ingyenes próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/) az Azure AD, ha még nem rendelkezik egy Azure ad-ben a próbakörnyezet.

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Ebben az oktatóanyagban a forgatókönyv két fő építőelemeket áll:

1. SAP HANA hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="add-sap-hana-from-the-gallery"></a>SAP HANA hozzáadása a katalógusból
Konfigurálhatja az integráció az SAP Hana az Azure AD-be, adja hozzá a katalógus az SAP HANA a felügyelt SaaS-alkalmazások listájában.

**SAP HANA hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a [az Azure portal](https://portal.azure.com), a bal oldali panelen válassza ki a **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Lépjen a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
3. Az új alkalmazás hozzáadásához válassza a **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **SAP HANA**. Válassza ki **SAP HANA** az eredmények panelen. Végül válassza ki a **Hozzáadás** gombra kattintva vegye fel az alkalmazást. 

    ![Az új alkalmazás](./media/saphana-tutorial/tutorial_saphana_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az SAP HANA "Britta Simon." nevű tesztelési felhasználó alapján

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, akik a partner felhasználó SAP HANA-ban, hogy egy felhasználó Azure AD-ben. Más szóval kell létesítenie egy Azure AD-felhasználót és a kapcsolódó felhasználó közötti kapcsolat az SAP HANA-ban.

SAP HANA, adjon a **felhasználónév** ugyanazt az értéket az értéket a **felhasználónév** az Azure ad-ben. Ebben a lépésben létrehozza a két olyan felhasználó közötti kapcsolat.

Az Azure AD egyszeri bejelentkezés az SAP HANA tesztelése és konfigurálása, hajtsa végre a következő építőelemeket:

1. [Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on) ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. [Hozzon létre egy Azure ad-ben tesztfelhasználót](#creating-an-azure-ad-test-user) az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. [Hozzon létre egy SAP HANA tesztfelhasználót](#creating-a-sap-hana-test-user) egy megfelelője a Britta Simon rendelkeznie, amely kapcsolódik az Azure AD leképezése a felhasználó az SAP HANA-ban.
4. [Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assigning-the-azure-ad-test-user) Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. [Egyszeri bejelentkezés tesztelése](#testing-single-sign-on) ellenőrzése, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, majd egyszeri bejelentkezést az SAP HANA-alkalmazás konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálásához az SAP HANA, az alábbi lépéseket:**

1. Az Azure Portalon az a **SAP HANA** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédpanel **SAML-alapú bejelentkezés**válassza **mód**.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/saphana-tutorial/tutorial_saphana_samlbase.png)

3. Az a **SAP HANA-tartomány és URL-címek** területén az alábbi lépéseket:

    ![Tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/saphana-tutorial/tutorial_saphana_url.png)

    a. Az a **azonosító** mezőbe írja be a következőt: `HA100` 

    b. Az a **válasz URL-cím** mezőbe írja be egy URL-CÍMÉT a következő mintának: `https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc`

    > [!NOTE] 
    > Ezek az értékek nem valódi. Az értékeket módosítsa a tényleges azonosítóval, és a válasz URL-címe. Forduljon a [SAP HANA ügyfél-támogatási csapatának](https://cloudplatform.sap.com/contact.html) beolvasni ezeket az értékeket. 

4. Az a **SAML-aláíró tanúsítvány** szakaszban jelölje be **metaadatainak XML**. Mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/saphana-tutorial/tutorial_saphana_certificate.png) 

    >[!Note]
    >Ha a tanúsítvány nem aktív, majd aktívvá válik kiválasztásával a **új tanúsítvány aktívvá** jelölőnégyzetet az Azure ad-ben. 

5. Az SAP HANA-alkalmazás a SAML helyességi feltételek vár egy megadott formátumban. Az alábbi képernyőképen Ez a formátum egy példát mutat be. 

    Itt azt már leképezve a **felhasználóazonosító** az a **ExtractMailPrefix()** funkcióját **user.mail**. Ez biztosítja, hogy az előtag értékének, a felhasználó e-mail címe, amely a felhasználó egyedi azonosítója. A felhasználói azonosító az SAP HANA-alkalmazást, az minden sikeres válasz érkezik.

    ![Egyszeri bejelentkezés konfigurálása](./media/saphana-tutorial/attribute.png)

6. Az a **felhasználói attribútumok** szakaszában a **egyszeri bejelentkezési** párbeszédpanel mezőbe az alábbi lépéseket:

    a. Az a **felhasználóazonosító** legördülő listában válassza **ExtractMailPrefix**.
    
    b. Az a **Mail** legördülő listában válassza **user.mail**.

7. Válassza ki a **Mentés** gombot.

    ![Az egyszeri bejelentkezés mentési gomb konfigurálása](./media/saphana-tutorial/tutorial_general_400.png)
    
8. Az SAP HANA oldalán konfigurálása egyszeri bejelentkezéshez, jelentkezzen be a **HANA XSA Webkonzol** nyissa meg a megfelelő HTTPS-végpontokat.

    > [!NOTE]
    > Az alapértelmezett beállítás az URL-címet átirányítja a kérést egy bejelentkezési képernyő, amely egy SAP HANA-adatbázis hitelesített felhasználó hitelesítő adatait igényli. A felhasználó, aki bejelentkezik jogosultnak kell lennie az SAML-felügyeleti feladatok végrehajtásához.

9. Lépjen a XSA Web-felületén **identitásszolgáltató SAML**. Itt válassza ki a **+** gombra a megjelenítendő képernyő alján a **identitás kiadói információk hozzáadása** ablaktáblán. Ezután az alábbi lépéseket:

    ![Identitásszolgáltató hozzáadása](./media/saphana-tutorial/sap1.png)

    a. Az a **identitás kiadói információk hozzáadása** panelen illessze be a metaadatainak XML (amely az Azure Portalról letöltött) tartalmát a **metaadatok** mezőbe.

    ![Identitásszolgáltató-beállítások hozzáadása](./media/saphana-tutorial/sap2.png)

    b. Az XML-dokumentum tartalmát érvényesek, ha az elemzési folyamat kinyeri a számára szükséges információkat a **tárgy, entitás azonosítója és a kiállító** mezőt a **általános adatok** képernyőterület. Az URL-címet a mezőket, a szükséges információkat is bontja ki a **cél** képernyőterület, például a **alap URL-cím és az egyszeri bejelentkezés URL-cím (*)** mezőket.

    ![Identitásszolgáltató-beállítások hozzáadása](./media/saphana-tutorial/sap3.png)

    c. Az a **neve** mezőjében a **általános adatok** képernyőterület, adja meg az SAML SSO új identitásszolgáltató nevét.

    > [!NOTE]
    > Az SAML-Identitásszolgáltató nevét kötelező megadni, és egyedinek kell lennie. A rendelkezésre álló SAML identitásszolgáltató SAML az SAP HANA XS alkalmazásokkal való használatra hitelesítési módszer kiválasztásakor megjelenő listájában jelenik meg. Például ezt megteheti a **hitelesítési** képernyőn az XS összetevő eszköz területéhez.

10. Válassza ki **mentése** részleteit az SAML-identitásszolgáltató mentése, és adja hozzá az új Identitásszolgáltató SAML ismert az SAML-identitásszolgáltató listájához.

    ![Mentés gomb](./media/saphana-tutorial/sap4.png)

11. A HANA Studio, a rendszer tulajdonságain belül a **konfigurációs** lapon, a beállítások szerint szűrheti **saml**. Majd szükség esetén módosítsa a **assertion_timeout** a **10 másodperc** való **120 másodperc**.

    ![assertion_timeout beállítás](./media/saphana-tutorial/sap7.png)

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com) közben állítja be az alkalmazás! Ez az alkalmazás hozzáadása után a **Active Directory** > **vállalati alkalmazások** szakaszban jelölje be a **egyszeri bejelentkezés** lapra, és a beágyazott eléréséhez dokumentáció a **konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásáról a [Azure ad-ben a beágyazott dokumentáció](https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali panelen válassza ki a **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](./media/saphana-tutorial/create_aaduser_01.png) 

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**. Válassza ki **minden felhasználó**.
    
    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/saphana-tutorial/create_aaduser_02.png) 

3. Megnyitásához a **felhasználói** párbeszédpanelen jelölje ki **Hozzáadás** a párbeszédpanel tetején.
 
    ![A Hozzáadás gombra.](./media/saphana-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel mezőbe az alábbi lépéseket:
 
    ![A felhasználó párbeszédpanel](./media/saphana-tutorial/create_aaduser_04.png) 

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése**, majd írja le a jelszót.

    d. Kattintson a **Létrehozás** gombra.
 
### <a name="create-a-sap-hana-test-user"></a>SAP HANA tesztfelhasználó létrehozása

Ahhoz, hogy jelentkezzen be az SAP HANA az Azure AD-felhasználók, kell építheti ki azokat az SAP HANA-ban.
SAP HANA támogatja a just-in-time-kiépítés, ami által alapértelmezés szerint engedélyezve van.

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

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon a hozzáférés biztosításával az SAP Hana-hoz az Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel az SAP HANA, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése. Nyissa meg a könyvtár nézet, és nyissa meg **vállalati alkalmazások**. Válassza ki **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **SAP HANA**.

    ![Felhasználó hozzárendelése](./media/saphana-tutorial/tutorial_saphana_app.png) 

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202] 

4. Válassza ki a **Hozzáadás** gombra. Az a **hozzárendelés hozzáadása** párbeszédpanelen jelölje ki **felhasználók és csoportok**.

    ![A hozzárendelés hozzáadása panel][203]

5. Az a **felhasználók és csoportok** párbeszédpanelen jelölje ki **Britta Simon** a a **felhasználók** listája.

6. Kattintson a **kiválasztása** gombra a **felhasználók és csoportok** párbeszédpanel bezárásához.

7. Válassza ki a **hozzárendelése** gombra a **hozzárendelés hozzáadása** párbeszédpanel bezárásához.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panel segítségével tesztelheti.

Az SAP HANA-csempe kiválasztásakor a hozzáférési panelen azt kell lekérése automatikusan bejelentkezik az SAP HANA-alkalmazás.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/saphana-tutorial/tutorial_general_01.png
[2]: ./media/saphana-tutorial/tutorial_general_02.png
[3]: ./media/saphana-tutorial/tutorial_general_03.png
[4]: ./media/saphana-tutorial/tutorial_general_04.png

[100]: ./media/saphana-tutorial/tutorial_general_100.png

[200]: ./media/saphana-tutorial/tutorial_general_200.png
[201]: ./media/saphana-tutorial/tutorial_general_201.png
[202]: ./media/saphana-tutorial/tutorial_general_202.png
[203]: ./media/saphana-tutorial/tutorial_general_203.png


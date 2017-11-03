---
title: "Oktatóanyag: Azure Active Directoryval integrált SAP HANA |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és az SAP HANA között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: cef4a146-f4b0-4e94-82de-f5227a4b462c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: a7e73f6ee763d1005ad85935cf2d8f6b24ecf116
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>Oktatóanyag: Azure Active Directoryval integrált SAP HANA

Ebben az oktatóanyagban elsajátíthatja SAP HANA integrálása az Azure Active Directory (Azure AD).

SAP HANA integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a SAP HANA hozzáféréssel rendelkező Azure AD-ben
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett az SAP HANA (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása SAP HANA-nal, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy SAP HANA egyszeri bejelentkezés engedélyezve van az előfizetés
- Egy futó HANA példány bármely nyilvános IaaS, a helyszíni vagy Azure virtuális gépeken vagy SAP nagy példányok az Azure-ban
- A XSA felügyeleti webes felülete, valamint HANA Studio telepítve HANA-példányon.

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem javasoljuk az SAP HANA éles környezetben. Az integráció először tesztelje fejlesztési vagy tesztelési környezetben az alkalmazás, majd az éles környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. SAP HANA hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-sap-hana-from-the-gallery"></a>SAP HANA hozzáadása a gyűjteményből
Az Azure AD integrálása a SAP HANA konfigurálásához kell hozzáadnia SAP HANA a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből SAP HANA hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **SAP HANA**, jelölje be **SAP HANA** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást. 

    ![Az új alkalmazás](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezést az SAP HANA "Britta Simon." nevű tesztfelhasználó alapján

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a SAP HANA tartozó felhasználót a felhasználó Azure AD-ben. Ez azt jelenti egy Azure AD-felhasználó és a kapcsolódó felhasználó a SAP HANA közötti kapcsolat kapcsolatot kell létrehozni.

Az SAP HANA, rendelje az értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést az SAP HANA tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Egy SAP HANA tesztfelhasználó létrehozása](#creating-a-sap-hana-test-user)**  - való Britta Simon megfelelője a egy SAP HANA, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az SAP HANA-alkalmazásban.

**Az SAP HANA konfigurálása az Azure AD egyszeri bejelentkezést, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **SAP HANA** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_samlbase.png)

3. Az a **SAP HANA-tartomány és az URL-címek** területen tegye a következőket:

    ![Tartomány- és URL-címeket az egyszeri bejelentkezés információk](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_url.png)

    a. Az a **azonosító** szövegmezőhöz típusú, mint:`HA100` 

    b. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe:`https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges azonosítója és a válasz URL-CÍMEN. Ügyfél [SAP HANA-ügyfél-támogatási csoport](https://cloudplatform.sap.com/contact.html) beolvasni ezeket az értékeket. 

4. Az a **SAML-aláíró tanúsítványa** területen kattintson **metaadatainak XML-kódja** és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_certificate.png) 

    >[!Note]
    >Ha a tanúsítvány nincs aktív majd aktiválja ezt a "Aktívvá új tanúsítvány" jelölőnégyzetre kattintva az Azure AD-ben. 

5. SAP HANA-alkalmazás a SAML helyességi feltételek vár egy meghatározott formátumban. Az alábbi képernyőfelvételen látható egy példa a. Itt azt leképezett a **felhasználói azonosító** rendelkező **ExtractMailPrefix()** funkciója **user.mail**. Ez biztosítja, hogy az e-mailek, a felhasználó, amely a egyedi felhasználói azonosító. az előtag értéke Ez az SAP HANA-alkalmazás minden sikeres válaszként küld.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-saphana-tutorial/attribute.png)

6. Az a **felhasználói attribútumok** a szakasz a **egyszeri bejelentkezés** párbeszédpanel:

    a. Az a **felhasználói azonosító** legördülő listában válassza ki **ExtractMailPrefix**.
    
    b. Az a **Mail** legördülő listában válassza ki **user.mail**.

7. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-saphana-tutorial/tutorial_general_400.png)
    
8. Egyszeri bejelentkezés konfigurálása **SAP HANA** ügyféloldali, jelentkezzen be a **HANA XSA Webkonzol** keresse meg azt a megfelelő HTTPS-végpontnak.

    > [!Note]
    > Az alapértelmezett beállítás az URL-címet átirányítja a kérést egy bejelentkezési képernyő, amely szükséges a hitelesítő adatokat egy hitelesített SAP HANA adatbázis-felhasználó a bejelentkezési folyamat befejezéséhez. A felhasználó, aki bejelentkezik a SAML-alapú felügyeleti feladatok elvégzéséhez szükséges jogosultsággal kell rendelkeznie.

9. A XSA webes felülete, lépjen a **SAML-Identitásszolgáltatóként** , és kattintson a **"+"** -gombra kattintva megjelenítheti az Identity Provider információ hozzáadása ablak, és hajtsa végre a következő lépéseket a képernyő alján:

    ![Identitás-szolgáltató felvétele](./media/active-directory-saas-saphana-tutorial/sap1.png)

    a. Az a **identitás szolgáltató adatai hozzáadása** ablaktáblán, a metaadatok XML-fájlok, amelyek az Azure portálról letöltött tartalmának beillesztése a **metaadatok** szövegmező.

    ![Identitásszolgáltató beállítások hozzáadása](./media/active-directory-saas-saphana-tutorial/sap2.png)

    b. Az XML-dokumentum tartalma érvényes, ha az elemzési folyamat kibontja elhelyezni a szükséges adatokat a **tulajdonosa Entitásazonosító és kibocsátó** az általános adatok mezőinek képernyőn terület, és a URL-címet a cél képernyő területet, például  **SingleSignOn és alap URL-címe (*)**.

    ![Identitásszolgáltató beállítások hozzáadása](./media/active-directory-saas-saphana-tutorial/sap3.png)

    c. Az általános adatterület képernyő név mezőben adja meg az új SAML SSO identitásszolgáltató nevét.

    > [!Note]
    > A SAML IDP nevét kötelező megadni, és egyedi; kell lennie azt listájában jelenik meg a rendelkezésre álló SAML IDPs akkor jelenik meg, ha SAML-alapú hitelesítési módszer az SAP HANA XS alkalmazásokkal való használatra, például a XS összetevő-felügyeleti eszköz hitelesítési képernyő területén.

10. Mentse az új SAML-Identitásszolgáltatóként részleteit. Válasszon **mentése** mentése a SAML-Identitásszolgáltatóként részleteit, és adja hozzá az új SAML IDP ismert SAML IDPs listája.

    ![Mentés gombja](./media/active-directory-saas-saphana-tutorial/sap4.png)

11. HANA Studio rendszer tulajdonságain belül a **konfigurációs** lapján csak a beállítások alapján szűrése **saml** , és módosítsa a **assertion_timeout** a **10 másodpercnél** való **120 másodperc**.

    ![assertion_timeout beállítás](./media/active-directory-saas-saphana-tutorial/sap7.png)

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-saphana-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-saphana-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![A Hozzáadás gombra.](./media/active-directory-saas-saphana-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![A felhasználó párbeszédpanel](./media/active-directory-saas-saphana-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-sap-hana-test-user"></a>Egy SAP HANA tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók SAP HANA bejelentkezni, akkor ki kell építenie az SAP HANA.
SAP HANA támogatja just-in-time kiosztást, amely alapértelmezés szerint van engedélyezve.

Ha a felhasználó manuálisan létrehozásához szükséges, hajtsa végre az alábbi lépéseket:

>[!Note]
>A külső hitelesítés a felhasználó által használt módosíthatja.
A külső rendszerek, például egy Kerberos rendszer a külső felhasználók hitelesítése. Külső identitások kapcsolatos részletes információkért forduljon a [tartományi rendszergazda](https://cloudplatform.sap.com/contact.html).

1. Nyissa meg a [SAP HANA Studio](https://help.sap.com/viewer/a2a49126a5c546a9864aae22c05c3d0e/2.0.01/en-us) rendszergazdaként, az adatbázis-felhasználó SAML SSO engedélyezése.

    ![Felhasználó létrehozása](./media/active-directory-saas-saphana-tutorial/sap5.png)

2. Osztásjelek balra láthatatlan jelölőnégyzet **SAML** és a konfigurálás hivatkozás.

3. Kattintson a **Hozzáadás** a SAML IDP, és kattintson a **OK** a megfelelő SAML IDP kiválasztása.

4. Adja hozzá a **külső identitások** (például) Itt BrittaSimon), vagy válasszon **"Bármely"** kattintson **OK**.

    >[!Note]
    >Ha nincs bejelölve a "Bármely" jelölőnégyzetet, majd HANA felhasználóneve meg kell egyeznie a felhasználó a felhasználónév, a tartományi utótag elé (azaz BrittaSimon@contoso.com válna a HANA BrittaSimon).

5. Tesztelési célokra, rendelje hozzá az összes **"XS"** a felhasználói szerepköröket.

    ![szerepkörök hozzárendelése](./media/active-directory-saas-saphana-tutorial/sap6.png)

    > [!TIP]
    > A használati esetek, csak a megfelelő engedélyeket kell adnia.

6. Mentse a felhasználó.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés SAP HANA Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**Az SAP HANA Britta Simon rendeléséhez hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **SAP HANA**.

    ![Felhasználó hozzárendelése](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési Panel egy SAP HANA mozaik gombra kattint, akkor kell beolvasni automatikusan bejelentkezett az SAP HANA-alkalmazáshoz.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)



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


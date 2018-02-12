---
title: "Oktatóanyag: Azure Active Directory-integráció ADP összevont egyszeri bejelentkezési modellel |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és ADP összevont egyszeri Bejelentkezést."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7be5331b-0481-48f7-9d6b-619dfec657e1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/02/2018
ms.author: jeedes
ms.openlocfilehash: 76bbee514eac36aec8eaf84e8a20f85a27559fc7
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2018
---
# <a name="tutorial-azure-active-directory-integration-with-adp-federated-sso"></a>Oktatóanyag: Azure Active Directoryval integrált ADP összevont egyszeri bejelentkezés

Ebben az oktatóanyagban elsajátíthatja ADP összevont egyszeri Bejelentkezéses integrálása az Azure Active Directory (Azure AD).

ADP összevont egyszeri Bejelentkezéses integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Azt is szabályozhatja az Azure AD, aki hozzáfér ADP összevont egyszeri Bejelentkezést.
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni aláírt a ADP összevont egyszeri Bejelentkezést (egyszeri bejelentkezés).
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integrációs konfigurálásához ADP összevont egyszeri bejelentkezési modellel, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy engedélyezett ADP összevont SSO-előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből ADP összevont Egyszeri hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-adp-federated-sso-from-the-gallery"></a>A gyűjteményből ADP összevont Egyszeri hozzáadása
Az Azure AD integrálása a ADP összevont Egyszeri konfigurálásához kell hozzáadnia ADP összevont egyszeri Bejelentkezést a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a ADP összevont egyszeri Bejelentkezést a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1.  Jelentkezzen be rendszergazdaként a Microsoft Azure identitáskezelési szolgáltató környezetben.

2. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

3. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
4. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

5. Írja be a keresőmezőbe, **ADP összevont egyszeri Bejelentkezéses**, jelölje be **ADP összevont egyszeri Bejelentkezéses** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![ADP összevont egyszeri Bejelentkezést az eredménylistában](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD az egyszeri bejelentkezés ADP összevont SSO "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó ADP összevont egyszeri Bejelentkezést a felhasználók az Azure AD. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó ADP összevont egyszeri Bejelentkezéses felhasználó közötti kapcsolat kapcsolatot kell létrehozni.

ADP összevont egyszeri Bejelentkezést, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD az egyszeri bejelentkezés ADP összevont egyszeri bejelentkezési modellel tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Hozzon létre egy ADP összevont egyszeri Bejelentkezéses tesztfelhasználó](#create-an-adp-federated-sso-test-user)**  - való egy megfelelője a Britta Simon ADP összevont egyszeri Bejelentkezést, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az ADP összevont egyszeri bejelentkezés alkalmazásokhoz.

**Konfigurálja az Azure AD az egyszeri bejelentkezés ADP összevont egyszeri bejelentkezési modellel, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **ADP összevont egyszeri Bejelentkezéses** alkalmazás integráció lapján, kattintson a **tulajdonságai lap** és hajtsa végre a következő lépéseket: 

    ![Egyszeri bejelentkezés tulajdonságai](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_prop.png)

    a. Állítsa be a **engedélyezve van, hogy a felhasználók bejelentkezési** érték mező **Igen**.

    b. Másolás a **felhasználói URL-CÍMEN** és illessze be, hogy **konfigurálása bejelentkezési URL-cím szakasz**, amely esetén, tekintse meg az oktatóanyag későbbi részében.

    c. Állítsa be a **szükséges felhasználói kiosztása** érték mező **Igen**.

    d. Állítsa be a **a felhasználók számára látható** érték mező **nem**.

2. Kattintson a **egyszeri bejelentkezés** a **ADP összevont egyszeri Bejelentkezéses** alkalmazás integráció lapján.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

3. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_samlbase.png)

4. Az a **ADP összevont egyszeri Bejelentkezéses tartomány és az URL-címek** területen tegye a következőket:

    ![Az egyszeri bejelentkezés információk ADP összevont egyszeri Bejelentkezéses tartomány és az URL-címek](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_url.png)

    Az a **azonosító** szövegmezőhöz URL-címet írja be:`https://fed.adp.com/` 
    
5. ADP összevont egyszeri bejelentkezési alkalmazás a SAML helyességi feltételek egy meghatározott formátumban, amelyek megkövetelik olyan egyéni attribútum-leképezésekhez hozzáadása a SAML-jogkivonat attribútumok konfigurációs vár. Az alábbi képernyőfelvételen látható egy példa a. A jogcím neve mindig lesz **"PersonImmutableID"** és amelynek értéke azt van leképezve **employeeid**. 

    A felhasználó Azure ad-ADP összevont egyszeri bejelentkezési leképezés a elvégzett Itt a **employeeid** , de ez leképezheti alkalmazás beállításai alapján egy másik értéket. Ezért kérjük, munkahelyi rendelkező [ADP támogatási csoport](https://www.adp.com/contact-us/overview.aspx) először használja a helyes azonosító egy olyan felhasználó, és képezze le az értéket a a **"PersonImmutableID"** jogcímek.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_attribute.png)

6. A a **felhasználói attribútumok** a szakasz a **egyszeri bejelentkezés** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, az ábrán látható módon, és hajtsa végre a következő lépéseket:
    
    | Attribútum neve | Attribútum értéke |
    | ------------------- | -------------------- |    
    | PersonImmutableID | user.employeeid |
    
    a. Kattintson a **Hozzáadás attribútum** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_attribute_04.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_attribute_05.png)

    b. Az a **neve** szövegmező, írja be az adott sorhoz feltüntetett attribútumot nevét.

    c. Az a **érték** kilistázásához írja be a sorhoz látható attribútum értéke.
    
    d. Kattintson az **OK** gombra.

    > [!NOTE] 
    > A SAML-előfeltétel konfigurálása előtt kapcsolatba kell lépnie a [ADP támogatási csoport](https://www.adp.com/contact-us/overview.aspx) és az egyedi azonosító attribútum értékének kérhetnek a bérlő. Ezt az értéket az egyéni jogcímleírásokat, az alkalmazás konfigurálása van szüksége. 

7. Az a **SAML-aláíró tanúsítványa** területen kattintson **metaadatainak XML-kódja** és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_certificate.png) 

8. Egyszeri bejelentkezés konfigurálása **ADP összevont egyszeri Bejelentkezéses** oldalon kell feltölteni a letöltött **metaadatainak XML-kódja** a a [ADP összevont egyszeri Bejelentkezéses webhely](https://adpfedsso.adp.com/public/login/index.fcc).

> [!NOTE]  
> Ez a folyamat eltarthat néhány nap múlva. 

### <a name="configure-your-adp-services-for-federated-access"></a>Az összevont hozzáférést a ADP Services-szolgáltatás(ok) konfigurálása

>[!Important]
> Az alkalmazottak, akik ADP szolgáltatások összevont hozzáférést igényelnek hozzá kell rendelni a ADP service-alkalmazást, és ezt követően, a felhasználók az adott ADP szolgáltatás kell hozzárendelve.
Az Ön képviselőjével is ADP megerősítő megérkezésekor a ADP Services-szolgáltatás(ok) és hozzárendelése/kezelése felhasználók beállítása az adott ADP szolgáltatásokhoz való hozzáférés szabályozása.

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **ADP összevont egyszeri Bejelentkezéses**, jelölje be **ADP összevont egyszeri Bejelentkezéses** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![ADP összevont egyszeri Bejelentkezést az eredménylistában](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_addservicegallery.png)

5. Az Azure portálon a a **ADP összevont egyszeri Bejelentkezéses** alkalmazás integráció lapján, kattintson a **tulajdonságai lap** és hajtsa végre a következő lépéseket:  

    ![Egyszeri bejelentkezés linkedproperties](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_linkedproperties.png)

    a.  Állítsa be a **engedélyezve van, hogy a felhasználók bejelentkezési** érték mező **Igen**.

    b.  Állítsa be a **szükséges felhasználói kiosztása** érték mező **Igen**.

    c.  Állítsa be a **a felhasználók számára látható** érték mező **Igen**.

6. Kattintson a **egyszeri bejelentkezés** a **ADP összevont egyszeri Bejelentkezéses** alkalmazás integráció lapján.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

7. A a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **társított bejelentkezés** az alkalmazás csatolni **ADP összevont egyszeri Bejelentkezéses**.
<!---Loc Comment:Sentence "to link your application to ADP Federated SSO." should be concatenated with the previous sentence--->

    ![Single sign-on linked](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_linked.png)

8. Keresse meg a **konfigurálása bejelentkezési URL-cím** területen tegye a következőket:

    ![Egyszeri bejelentkezés prop](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_linkedsignon.png)
                                                              
    a. Beillesztés a **felhasználói URL-CÍMEN**, amely a fenti másolta **tulajdonságai lap** (alkalmazásból a fő ADP összevont SSO).
                                                             
    b. Az alábbiakban az 5-alkalmazásokat, amelyek támogatják a különböző **továbbítási állapot URL-címek**. Rendelkezik a megfelelő hozzáfűzendő **továbbítási állapot URL-cím** manuálisan érték az adott alkalmazás a **felhasználói URL-CÍMEN**.
    
    * **Most már a ADP dolgozók számára**
        
        `<User access URL>?Relay State=https://fed.adp.com/saml/fedlanding.html?WFN`

    * **ADP munkaerő most fokozott idő**
        
        `<User access URL>?Relay State=https://fed.adp.com/saml/fedlanding.html?EETDC2`
    
    * **HCM ADP Vantage**
        
        `<User access URL>?Relay State=https://fed.adp.com/saml/fedlanding.html?ADPVANTAGE`

    * **ADP Enterprise HR**

        `<User access URL>?Relay State=https://fed.adp.com/saml/fedlanding.html?PORTAL`

    * **MyADP**

        `<User access URL>?Relay State=https://fed.adp.com/saml/fedlanding.html?REDBOX`

9. **Mentés** a módosításokat.

10. A ADP képviselője megerősítését beérkezésekor először teszt egy vagy két felhasználókkal.

    a. Néhány felhasználó hozzárendelése a ADP szolgáltatás alkalmazásnak, hogy összevont hozzáférést tesztelése.

    b. Teszt akkor sikeres, ha a felhasználók férhetnek hozzá a gyűjteményben lévő ADP service-alkalmazást, és hozzáférhet a ADP szolgáltatásában.
 
11. A megerősítő a sikeres vizsgálat rendelje hozzá az összevont ADP szolgáltatást az egyes felhasználókra vagy felhasználói csoportok, amelyek esetén, tekintse meg az oktatóanyag későbbi részében, és megkezdik az alkalmazottaknak. 

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-adpfederatedsso-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-adpfederatedsso-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/active-directory-saas-adpfederatedsso-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-adpfederatedsso-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-an-adp-federated-sso-test-user"></a>Hozzon létre egy ADP összevont egyszeri Bejelentkezéses tesztfelhasználó számára

Ez a szakasz célja Britta Simon meghívta ADP összevont egyszeri Bejelentkezéses felhasználó létrehozásához. Együttműködve [ADP támogatási csoport](https://www.adp.com/contact-us/overview.aspx) a felhasználók hozzáadása a ADP összevont egyszeri bejelentkezési fiók.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban Britta Simon használandó Azure egyszeri bejelentkezés által biztosított hozzáférés ADP összevont egyszeri bejelentkezés engedélyezése.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése ADP összevont egyszeri Bejelentkezést, hajtsa végre a következő lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **ADP összevont egyszeri Bejelentkezéses**.

    ![Az alkalmazások listáját a ADP összevont egyszeri Bejelentkezéses hivatkozás](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adpfederatedsso_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési Panel ADP összevont egyszeri Bejelentkezéses mozaik gombra kattint, akkor kell beolvasása automatikusan bejelentkezett ADP összevont egyszeri Bejelentkezéses Alkalmazásmódosítások.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_203.png


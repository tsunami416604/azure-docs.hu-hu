---
title: 'Oktatóanyag: Azure Active Directory-integráció az EBSCO |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és EBSCO között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 144f7f65-69e9-4016-a151-fe1104fd6ba8
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: jeedes
ms.openlocfilehash: 50e9a65f881981964270e0a8bbc7af46a9dfd27a
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39047725"
---
# <a name="tutorial-azure-active-directory-integration-with-ebsco"></a>Oktatóanyag: Azure Active Directory-integráció az EBSCO

Ebben az oktatóanyagban elsajátíthatja, hogyan EBSCO integrálása az Azure Active Directory (Azure AD).

EBSCO integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá EBSCO Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett EBSCO (egyszeri bejelentkezés), az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

EBSCO az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy EBSCO egyszeri bejelentkezéses engedélyezett előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. EBSCO hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-ebsco-from-the-gallery"></a>EBSCO hozzáadása a katalógusból
Az Azure AD integrálása a EBSCO konfigurálásához hozzá kell EBSCO a katalógusból a felügyelt SaaS-alkalmazások listájára.

**EBSCO hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **EBSCO**válassza **EBSCO** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában EBSCO](./media/ebsco-tutorial/tutorial_ebsco_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés EBSCO a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó EBSCO mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó EBSCO hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az EBSCO tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Hozzon létre egy EBSCO tesztfelhasználót](#create-an-ebsco-test-user)**  -EBSCOhost kiépítés felhasználó/személyre szabása automatizálható. EBSCO támogatja a Just-In-Time felhasználóátadást készíthet elő.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és EBSCO alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés EBSCO, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **EBSCO** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/ebsco-tutorial/tutorial_ebsco_samlbase.png)

3. Az a **EBSCO tartomány és URL-címek** területén kövesse az alábbi lépéseket, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód:

    ![EBSCO tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/ebsco-tutorial/tutorial_ebsco_url.png)

    Az a **azonosító** szövegmezőbe írja be egy URL-címe: `pingsso.ebscohost.com`

4. Ellenőrizze **speciális URL-beállítások megjelenítése** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![EBSCO tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/ebsco-tutorial/tutorial_ebsco_url1.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `http://search.ebscohost.com/login.aspx?authtype=sso&custid=<unique EBSCO customer ID>&profile=<profile ID>`
     
    > [!NOTE] 
    > A bejelentkezési URL-érték nem valódi. Frissítse az értéket a tényleges bejelentkezési URL-CÍMÉT. Kapcsolattartó [EBSCO ügyfél-támogatási csapatának](mailto:sso@ebsco.com) a gépkulcsengedélyek értékének. 

    o **egyedi elemek:**  

    o **Custid** adjon meg egyedi EBSCO ügyfél-azonosító = 

    o **profil** = ügyfelek testre szabhatnak a hivatkozásra kattintva a felhasználó egy adott profilhoz (attól függően, mire azok vásárolhatnak EBSCO). Megadhatják, hogy egy adott profil. A fő azonosítók a következők: eds (EBSCO Discovery szolgáltatás) és ehost (EBSOCOhost adatbázisok). Utasítások a [Itt](https://help.ebsco.com/interfaces/EBSCOhost/EBSCOhost_FAQs/How_do_I_set_up_direct_links_to_EBSCOhost_profiles_and_or_databases#profile).

5. EBSCO alkalmazás a SAML helyességi feltételek vár egy megadott formátumban. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a "**felhasználói attribútumok**" szakasz alkalmazás integráció lapján. Az alábbi képernyőfelvételen látható erre egy példa látható.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/ebsco-tutorial/tutorial_ebsco_attribute.png)

    > [!Note]
    > A **neve** attribútum kötelező, és az le van képezve **felhasználóazonosító** EBSCO alkalmazásban. Ez hozzáadódik alapértelmezés szerint, így nem kell manuálisan adja hozzá ezt.
    
6. Az a **felhasználói attribútumok** szakaszában a **egyszeri bejelentkezési** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, a fenti képen látható módon, és hajtsa végre az alábbi lépéseket:
    
    | Attribútum neve | Attribútum értéke |
    | ---------------| --------------- |    
    | FirstName   | User.givenName |
    | LastName   | User.surname |
    | E-mail   | user.mail |

    a. Kattintson a **attribútum hozzáadása** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/ebsco-tutorial/tutorial_officespace_04.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/ebsco-tutorial/tutorial_attribute_05.png)
    
    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.
    
    c. Az a **érték** list, írja be az adott sorhoz feltüntetett attribútumot értéket.
    
    d. Kattintson a **Ok**

7. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/ebsco-tutorial/tutorial_ebsco_certificate.png) 

8. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/ebsco-tutorial/tutorial_general_400.png)
    
9. Az egyszeri bejelentkezés konfigurálása **EBSCO** oldalon kell küldenie a letöltött **metaadatainak XML** való [EBSCO támogatási csoportjának](mailto:sso@ebsco.com). Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/ebsco-tutorial/create_aaduser_01.png)

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/ebsco-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/ebsco-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/ebsco-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-an-ebsco-test-user"></a>Hozzon létre egy EBSCO tesztfelhasználó számára

EBSCO, esetén felhasználókiépítés automatikusan sor kerül.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

Az Azure AD EBSCO alkalmazás továbbítja a szükséges adatokat. EBSCO a felhasználók átadásának lehet automatikus vagy egy egyszeri űrlap szükséges. Attól függ, hogy rendelkezik-e az ügyfél nagy mennyiségű előre meglévő EBSCOhost fiók személyes beállítások mentve. Ugyanaz az ezekről is a [EBSCO támogatási csoportjának](mailto:sso@ebsco.com) végrehajtása során. Mindkét módszer esetén a tesztelés előtt minden EBSCOhost fiókokat hozhat létre az ügyfél nem rendelkezik.

   >[!Note]
   >EBSCOhost kiépítés felhasználó/személyre szabása automatizálható. Kapcsolattartó [EBSCO támogatási csoportjának](mailto:sso@ebsco.com) kapcsolatos Just-In-Time felhasználóátadást készíthet elő. 
 
### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés EBSCO Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel EBSCO, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **EBSCO**.

    ![Az alkalmazások listáját a EBSCO hivatkozásra](./media/ebsco-tutorial/tutorial_ebsco_app.png)  

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

1. Ha a hozzáférési panelen a EBSCO csempére kattint, meg kell lekérése automatikusan bejelentkezett az EBSCO alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

2. Miután bejelentkezett az alkalmazásba, kattintson a a **jelentkezzen be a** gombra a jobb felső sarokban.

    ![A EBSCO bejelentkezési alkalmazásainak listájában](./media/ebsco-tutorial/tutorial_ebsco_signin.png)
 
3. Párosítsa az intézményi SAML/bejelentkezés egyszeri felszólítást kapni fog egy **most már az intézmény fiókkal összekapcsolni MyEBSCOhost fiókját** vagy **MyEBSCOhost új fiók létrehozása és a hivatkozás a intézményi fiókot**. A fiók személyre szabása, a EBSCOhost alkalmazására szolgál. Válassza a **hozzon létre egy új fiókot** és látni fogja, hogy az űrlap személyre szabáshoz előre kitöltött a saml-válasz értékekkel-e az alábbi képernyőképen látható módon. Kattintson a **"Folytatás"** a mentéshez.
    
     ![Az alkalmazások listáját a EBSCO felhasználó](./media/ebsco-tutorial/tutorial_ebsco_user.png)

4. A fenti beállítás befejezése után törölje a jelet a cookie-k/gyorsítótár és a bejelentkezési újra. Nem kell manuálisan bejelentkezési újra, és a személyre szabási beállításait a rendszer megjegyezze őket

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/ebsco-tutorial/tutorial_general_01.png
[2]: ./media/ebsco-tutorial/tutorial_general_02.png
[3]: ./media/ebsco-tutorial/tutorial_general_03.png
[4]: ./media/ebsco-tutorial/tutorial_general_04.png

[100]: ./media/ebsco-tutorial/tutorial_general_100.png

[200]: ./media/ebsco-tutorial/tutorial_general_200.png
[201]: ./media/ebsco-tutorial/tutorial_general_201.png
[202]: ./media/ebsco-tutorial/tutorial_general_202.png
[203]: ./media/ebsco-tutorial/tutorial_general_203.png


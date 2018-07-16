---
title: 'Oktatóanyag: Azure Active Directory-integráció az ADP |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és ADP között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7be5331b-0481-48f7-9d6b-619dfec657e1
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/29/2018
ms.author: jeedes
ms.openlocfilehash: 75b84c2856373126ceba0fc536e41d270f4d2d05
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39048779"
---
# <a name="tutorial-azure-active-directory-integration-with-adp"></a>Oktatóanyag: Azure Active Directory-integráció az ADP

Ebben az oktatóanyagban elsajátíthatja, hogyan ADP integrálása az Azure Active Directory (Azure AD).

ADP integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá ADP Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett ADP (egyszeri bejelentkezés), az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

ADP az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Az engedélyezett ADP előfizetéssel

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. ADP hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-adp-from-the-gallery"></a>ADP hozzáadása a katalógusból
Az Azure AD integrálása a ADP konfigurálásához hozzá kell ADP a katalógusból a felügyelt SaaS-alkalmazások listájára.

**ADP hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1.  Jelentkezzen be rendszergazdaként a Microsoft Azure-identitás szolgáltatói környezettől.

2. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

3. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
4. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

5. A Keresés mezőbe írja be a **ADP**válassza **ADP** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában ADP](./media/adpfederatedsso-tutorial/tutorial_adp_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés ADP a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó ADP mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó ADP hivatkozás kapcsolata kell létrehozni.

ADP, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az ADP tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Hozzon létre egy ADP tesztfelhasználót](#create-an-adp-test-user)**  – egy megfelelője a Britta Simon ADP, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és ADP alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés ADP, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **ADP** alkalmazás integrációs oldalán kattintson a **tulajdonságai lap** , és hajtsa végre az alábbi lépéseket: 

    ![Egyszeri bejelentkezési tulajdonságainál](./media/adpfederatedsso-tutorial/tutorial_adp_prop.png)

    a. Állítsa be a **engedélyezett a felhasználók bejelentkezhetnek** érték mező **Igen**.

    b. Másolás a **felhasználói hozzáférési URL-címe** , és illessze be kell **konfigurálása bejelentkezési URL-cím szakasz**, amely kifejtett az oktatóanyag későbbi részében.

    c. Állítsa be a **felhasználó-hozzárendelés szükséges** érték mező **Igen**.

    d. Állítsa be a **a felhasználók számára látható** érték mező **nem**.

2. Kattintson a **egyszeri bejelentkezési** a **ADP** alkalmazás integráció lapján.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

3. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/adpfederatedsso-tutorial/tutorial_adp_samlbase.png)

4. Az a **ADP tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![ADP tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/adpfederatedsso-tutorial/tutorial_adp_url.png)

    Az a **azonosító** szövegmezőbe írja be egy URL-címe: `https://fed.adp.com` 
    
5. A ADP alkalmazás a SAML helyességi feltételek vár egy megadott formátumban, amely megköveteli, hogy egyéni attribútum-leképezéshez az SAML-jogkivonat attribútumai konfigurációja. Az alábbi képernyőfelvételen látható erre egy példa látható. A jogcím neve mindig lesz **"PersonImmutableID"** és az érték, amely azt van leképezve **employeeid**. 

    A felhasználóleképezést ADP az Azure AD-ből az történik Itt a **employeeid** , de ez leképezhet az alkalmazás-beállításai alapján egy másik értéket. Ezért kérjük, munkahelyi az [ADP támogatási csapatának](https://www.adp.com/contact-us/overview.aspx) először a felhasználó a megfelelő azonosító használja, és képezze le az értéket a **"PersonImmutableID"** jogcím.

    ![Egyszeri bejelentkezés konfigurálása](./media/adpfederatedsso-tutorial/tutorial_adp_attribute.png)

6. Az a **felhasználói attribútumok** szakaszában a **egyszeri bejelentkezési** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, az ábrán látható módon, és hajtsa végre az alábbi lépéseket:
    
    | Attribútum neve | Attribútum értéke |
    | ------------------- | -------------------- |    
    | PersonImmutableID | User.EmployeeID |
    
    a. Kattintson a **attribútum hozzáadása** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/adpfederatedsso-tutorial/tutorial_attribute_04.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/adpfederatedsso-tutorial/tutorial_attribute_05.png)

    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    c. Az a **érték** list, írja be az adott sorhoz feltüntetett attribútumot értéket.
    
    d. Kattintson az **OK** gombra.

    > [!NOTE] 
    > Az SAML-előfeltétel konfigurálása előtt kell kapcsolódni a [ADP támogatási csapatának](https://www.adp.com/contact-us/overview.aspx) és az egyedi azonosító attribútum értékét a bérlő számára. Szüksége lesz erre az értékre az alkalmazás egyéni jogcím konfigurálása. 

7. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/adpfederatedsso-tutorial/tutorial_adp_certificate.png) 

8. Az egyszeri bejelentkezés konfigurálása **ADP** oldalán, fel kell töltenie a letöltött **metaadatainak XML** a a [ADP webhely](https://adpfedsso.adp.com/public/login/index.fcc).

> [!NOTE]  
> Ez a folyamat néhány napig is eltarthat. 

### <a name="configure-your-adp-services-for-federated-access"></a>Az összevont hozzáférést a ADP szolgáltatás(ok) konfigurálása

>[!Important]
> Az alkalmazottak, akik ADP szolgáltatásait összevont hozzáférést igényelnek a ADP service-alkalmazáshoz, és ezt követően kell hozzárendelni, a felhasználók kell hozzárendelni, az adott ADP szolgáltatásba.
A ADP képviselőjével megerősítését megérkezésekor a ADP szolgáltatás(ok) és hozzárendelése és kezelése felhasználók beállítása az adott ADP szolgáltatásokhoz való hozzáférés szabályozása.

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **ADP**válassza **ADP** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában ADP](./media/adpfederatedsso-tutorial/tutorial_adp_addservicegallery.png)

5. Az Azure Portalon az a **ADP** alkalmazás integrációs oldalán kattintson a **tulajdonságai lap** , és hajtsa végre az alábbi lépéseket:  

    ![Egyszeri bejelentkezés linkedproperties](./media/adpfederatedsso-tutorial/tutorial_adp_linkedproperties.png)

    a.  Állítsa be a **engedélyezett a felhasználók bejelentkezhetnek** érték mező **Igen**.

    b.  Állítsa be a **felhasználó-hozzárendelés szükséges** érték mező **Igen**.

    c.  Állítsa be a **a felhasználók számára látható** érték mező **Igen**.

6. Kattintson a **egyszeri bejelentkezési** a **ADP** alkalmazás integráció lapján.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

7. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **csatolt bejelentkezés**. az alkalmazás összekapcsolása **ADP**.

    ![Egyszeri bejelentkezés társított](./media/adpfederatedsso-tutorial/tutorial_adp_linked.png)

8. Keresse meg a **bejelentkezési URL-cím konfigurálása** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés prop](./media/adpfederatedsso-tutorial/tutorial_adp_linkedsignon.png)
                                                              
    a. Illessze be a **felhasználói hozzáférési URL-címe**, amely a fent másolta **tulajdonságai lap** (az alkalmazásból fő ADP).
                                                             
    b. Az alábbiakban az 5 alkalmazásokról, amelyek támogatják a különböző **továbbítási állapot URL-címek**. Rendelkezik a megfelelő hozzáfűzni kívánt **továbbító állapot URL-címe** manuálisan az adott alkalmazás értékét a **felhasználói hozzáférési URL-címe**.
    
    * **Most már a ADP dolgozók számára**
        
        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?WFN`

    * **ADP munkaerő továbbfejlesztettük az idő**
        
        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?EETDC2`
    
    * **HCM ADP Vantage**
        
        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?ADPVANTAGE`

    * **HR ADP Enterprise**

        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?PORTAL`

    * **MyADP**

        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?REDBOX`

9. **Mentés** a módosításokat.

10. A ADP képviselőjével megerősítését beérkezésekor egy vagy két felhasználókkal teszt kezdődik.

    a. Néhány felhasználók hozzárendelése a ADP szolgáltatás összevont hozzáférési tesztelni az alkalmazást.

    b. Teszt akkor sikeres, ha a felhasználók a katalógusban az ADP service-alkalmazás elérésére, és érhetik el a ADP szolgáltatást.
 
11. A megerősítés sikeres vizsgálat rendelje hozzá a az összevont ADP szolgáltatás egyes felhasználókat vagy felhasználói csoportok, amelyek az oktatóanyag későbbi részében kifejtett és bevezetése az alkalmazottaknak. 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/adpfederatedsso-tutorial/create_aaduser_01.png)

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/adpfederatedsso-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/adpfederatedsso-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/adpfederatedsso-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-an-adp-test-user"></a>Hozzon létre egy ADP tesztfelhasználó számára

Ez a szakasz célja ADP Britta Simon nevű felhasználó létrehozásához. Együttműködve [ADP támogatási csapatának](https://www.adp.com/contact-us/overview.aspx) a felhasználók hozzáadása a ADP fiókban.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés ADP Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel ADP, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **ADP**.

    ![Az alkalmazások listáját a ADP hivatkozás](./media/adpfederatedsso-tutorial/tutorial_adp_app.png)  

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a ADP csempére kattint, meg kell lekérése automatikusan bejelentkezett az ADP alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/adpfederatedsso-tutorial/tutorial_general_01.png
[2]: ./media/adpfederatedsso-tutorial/tutorial_general_02.png
[3]: ./media/adpfederatedsso-tutorial/tutorial_general_03.png
[4]: ./media/adpfederatedsso-tutorial/tutorial_general_04.png

[100]: ./media/adpfederatedsso-tutorial/tutorial_general_100.png

[200]: ./media/adpfederatedsso-tutorial/tutorial_general_200.png
[201]: ./media/adpfederatedsso-tutorial/tutorial_general_201.png
[202]: ./media/adpfederatedsso-tutorial/tutorial_general_202.png
[203]: ./media/adpfederatedsso-tutorial/tutorial_general_203.png


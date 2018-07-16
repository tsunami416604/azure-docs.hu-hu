---
title: 'Oktatóanyag: Azure Active Directory-integráció a workdayjel képes |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Workday között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: e9da692e-4a65-4231-8ab3-bc9a87b10bca
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2018
ms.author: jeedes
ms.openlocfilehash: 641f2e19791be48d014eeb61fb2848fc2a33a9cc
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39042044"
---
# <a name="tutorial-azure-active-directory-integration-with-workday"></a>Oktatóanyag: Azure Active Directory-integráció a workdayjel képes

Ebben az oktatóanyagban megismerheti, hogyan integrálható a Workday az Azure Active Directory (Azure AD).

Az Azure AD integrálása a Workday nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá a Workday az Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett workdaybe (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Workday, a következőkre van szükség:

- Az Azure AD-előfizetéshez
- A Workday egyszeri bejelentkezéses engedélyezett előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Workday hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-workday-from-the-gallery"></a>Workday hozzáadása a katalógusból
Az Azure AD integrálása a Workday konfigurálásához hozzá kell Workday a galériából a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a Workdayből a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **Workday**válassza **Workday** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában munkanap](./media/workday-tutorial/tutorial_workday_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a workdayjel képes a teszt "Britta Simon" nevű felhasználó.

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, a partner felhasználó WORKDAY mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó WORKDAY hivatkozás kapcsolata kell létrehozni.

A Workdayben, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** hivatkozás viszony.

Az Azure AD egyszeri bejelentkezés a workdayjel képes tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Hozzon létre egy Workday tesztfelhasználót](#create-a-workday-test-user)**  – szeretné, hogy egy Britta Simon megfelelője a WORKDAY, amely kapcsolódik az Azure AD felhasználói ábrázolása.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a Workday-alkalmazás az egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálásához a workdayjel képes, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Workday** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/workday-tutorial/tutorial_workday_samlbase.png)

3. Az a **Workday tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Munkanapi tartomány és URL-címek egyszeri bejelentkezési adatait](./media/workday-tutorial/tutorial_workday_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://impl.workday.com/<tenant>/login-saml2.htmld`

    b. Az a **azonosító** szövegmezőbe írja be egy URL-címe: `http://www.workday.com`

4. Ellenőrizze **speciális URL-beállítások megjelenítése** , és hajtsa végre a következő lépést:

    ![Munkanapi tartomány és URL-címek egyszeri bejelentkezési adatait](./media/workday-tutorial/tutorial_workday_url1.png)

    Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://impl.workday.com/<tenant>/login-saml.htmld`
     
    > [!NOTE] 
    > Ezek az értékek nem a valós. Ezek az értékek frissítse a tényleges bejelentkezési URL-cím és a válasz URL-cím. A válasz URL-cím rendelkeznie kell egy altartomány például: www, wd2, wd3, wd3-impl, wd5, wd5-impl). Vagy hasonló "*http://www.myworkday.com*" működik, de "*http://myworkday.com*" nem létezik. Kapcsolattartó [Workday ügyfél-támogatási csapatának](https://www.workday.com/en-us/partners-services/services/support.html) beolvasni ezeket az értékeket.  

5. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/workday-tutorial/tutorial_workday_certificate.png) 

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/workday-tutorial/tutorial_general_400.png)
    
7. Az a **Workday konfigurációs** területén kattintson **konfigurálása Workday** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Munkanapi konfiguráció](./media/workday-tutorial/tutorial_workday_configure.png) 

8. Egy másik böngészőablakban jelentkezzen be a Workday vállalati hely rendszergazdaként.

9. Az a **keresőmezőbe** nevű keresési **szerkesztése Bérlőbeállítást – biztonsági** bal oldalt a kezdőlap tetején található.
   
    ![Bérlői biztonsági szerkesztése](./media/workday-tutorial/IC782925.png "bérlői biztonsági szerkesztése")

10. Az a **átirányítási URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:
   
    ![Átirányítási URL-címek](./media/workday-tutorial/IC7829581.png "átirányítási URL-címek")
   
    a. Kattintson a **sort ad hozzá a**.
   
    b. Az a **bejelentkezés átirányítási URL-cím** szövegmezőbe, és a **Mobile átirányítási URL-cím** szövegmezőbe írja be a **bejelentkezési URL-** megadott a **Workday tartomány és URL-címek** szakaszában az Azure Portalon.
   
    c. Az Azure Portalon az a **bejelentkezés konfigurálása** ablakban, a másolási a **kijelentkezéses URL-cím**, és illessze be azt a **kijelentkezési átirányítási URL-cím** szövegmezőbe.

    d. A **környezetekben használt** szövegmezőben válassza ki a környezet nevét.  

    >[!NOTE]
    > A környezet attribútum vannak kötve, a bérlői URL-cím értékét:  
    >-Ha a tartomány neve a Workday-bérlői URL-cím kezdődik impl például: *https://impl.workday.com/\<tenant\>/login-saml2.htmld*), a **környezet** attribútum megvalósítási értékre kell állítani.  
    >-Ha a tartománynév valami mást kezdődik, lépjen kapcsolatba kell [Workday ügyfél-támogatási csapatának](https://www.workday.com/en-us/partners-services/services/support.html) beolvasni a megfelelő **környezet** érték.

11. Az a **SAML-telepítő** szakaszban, hajtsa végre az alábbi lépéseket:
   
    ![SAML-telepítő](./media/workday-tutorial/IC782926.png "SAML-telepítő")
   
    a.  Válassza ki **SAML-hitelesítés engedélyezése**.
   
    b.  Kattintson a **sort ad hozzá a**.

12. Az a **SAML Identitásszolgáltatók** szakaszban, hajtsa végre az alábbi lépéseket:
   
    ![SAML-Identitásszolgáltatók](./media/workday-tutorial/IC7829271.png "SAML Identitásszolgáltatók")
   
    a. Az a **identitásszolgáltató neve** szövegmezőbe írja be a szolgáltató nevét (például: *SPInitiatedSSO*).
   
    b. Az Azure Portalon az a **bejelentkezés konfigurálása** ablakban, a Másolás a **SAML Entitásazonosító** értéket, és illessze be azt a **kibocsátó** szövegmezőbe.

    ![SAML-Identitásszolgáltatók](./media/workday-tutorial/IC7829272.png "SAML Identitásszolgáltatók")
   
    c. Az Azure Portalon az a **bejelentkezés konfigurálása** ablakban, a Másolás a **kijelentkezéses URL-cím** értéket, és illessze be azt a **kijelentkezési válasz URL-címe** szövegmezőbe.

    d. Az Azure Portalon az a **bejelentkezés konfigurálása** ablakban, a másolási a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** értéket, és illessze be azt a **identitásszolgáltató egyszeri bejelentkezési szolgáltatás URL-címe** szövegmezőbe.

    e. A **környezetekben használt** szövegmezőben válassza ki a környezet nevét.

    f. Kattintson a **szolgáltató nyilvános kulcs Identitástanúsítványt**, és kattintson a **létrehozás**. 

    ![Hozzon létre](./media/workday-tutorial/IC782928.png "létrehozása")

    g. Kattintson a **x509 létrehozása nyilvános kulcs**. 

    ![Hozzon létre](./media/workday-tutorial/IC782929.png "létrehozása")

13. Az a **nézet x509 nyilvános kulcs** szakaszban, hajtsa végre az alábbi lépéseket: 
   
    ![Nyilvános kulcs nézet x509](./media/workday-tutorial/IC782930.png "nézet x509 nyilvános kulcs") 
   
    a. Az a **neve** szövegmezőbe írja be a tanúsítvány nevét (például: *PPE\_SP*).
   
    b. Az a **érvényes a** szövegmezőbe írja be az attribútum értékét a tanúsítvány érvényes.
   
    c.  Az a **érvényes való** szövegmezőbe írja be a tanúsítvány attribútum értéke érvénytelen.
   
    > [!NOTE]
    > Kérheti az érvényes dátumot és az érvényes, ha duplán kattint a letöltött tanúsítvány az a dátum a.  A dátumok vannak felsorolva a **részletek** fülre.
    > 
    >
   
    d.  Nyissa meg a base-64 kódolású tanúsítványt a Jegyzettömbben, és másolja a tartalmát.
   
    e.  Az a **tanúsítvány** szövegmezőjébe illessze be a vágólap tartalmát.
   
    f.  Kattintson az **OK** gombra.

14. Hajtsa végre az alábbi lépéseket: 
   
    ![Egyszeri bejelentkezés konfigurációja](./media/workday-tutorial/WorkdaySSOConfiguratio.png "egyszeri bejelentkezés konfigurálása")
   
    a.  Az a **szolgáltatás Szolgáltatóazonosító** szövegmezőbe írja be **http://www.workday.com**.
   
    b. Válassza ki **nem Deflate hitelesítési Szolgáltató által kezdeményezett kérelem**.
   
    c. Mint **hitelesítési kérelmek aláírását módszer**válassza **SHA256**. 
   
    ![Hitelesítési kérelem-aláírás módszer](./media/workday-tutorial/WorkdaySSOConfiguration.png "hitelesítési kérelmek aláírását módja") 
   
    d. Kattintson az **OK** gombra. 
   
    ![OK](./media/workday-tutorial/IC782933.png "OK")

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/workday-tutorial/create_aaduser_01.png)

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/workday-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/workday-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/workday-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-workday-test-user"></a>Workday tesztfelhasználó létrehozása

Ebben a szakaszban egy WORKDAY Britta Simon nevű felhasználó létrehozásához. Együttműködve [Workday ügyfél-támogatási csapatának](https://www.workday.com/en-us/partners-services/services/support.html) a felhasználók hozzáadása a Workday-platformon. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva. 

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon workdaybe a hozzáférés biztosításával Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel a Workday, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **Workday**.

    ![Az alkalmazások listáját a Workday hivatkozás](./media/workday-tutorial/tutorial_workday_app.png)  

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Workday csempére kattint, akkor kell lekérése automatikusan bejelentkezett a Workday-alkalmazásba.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/workday-tutorial/tutorial_general_01.png
[2]: ./media/workday-tutorial/tutorial_general_02.png
[3]: ./media/workday-tutorial/tutorial_general_03.png
[4]: ./media/workday-tutorial/tutorial_general_04.png

[100]: ./media/workday-tutorial/tutorial_general_100.png

[200]: ./media/workday-tutorial/tutorial_general_200.png
[201]: ./media/workday-tutorial/tutorial_general_201.png
[202]: ./media/workday-tutorial/tutorial_general_202.png
[203]: ./media/workday-tutorial/tutorial_general_203.png

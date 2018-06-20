---
title: 'Oktatóanyag: Azure Active Directoryval integrált Workday |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a Workday között.
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
ms.openlocfilehash: d3d85dd3ec074d01d597e34a45f27434214fc0ad
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36212264"
---
# <a name="tutorial-azure-active-directory-integration-with-workday"></a>Oktatóanyag: Azure Active Directoryval integrált munkanap

Ebben az oktatóanyagban elsajátíthatja Workday integrálása az Azure Active Directory (Azure AD).

Munkanapok integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér a Workday szabályozhatja.
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni aláírt a Workday (egyszeri bejelentkezés) a saját Azure AD-fiókok.
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a munkanapok, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- A Workday egyszeri bejelentkezés engedélyezve van az előfizetésben

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Munkanapok hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-workday-from-the-gallery"></a>Munkanapok hozzáadása a gyűjteményből
Az Azure AD integrálása a Workday konfigurálásához kell hozzáadnia Workday a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a Workday a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **Workday**, jelölje be **Workday** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában munkanap](./media/workday-tutorial/tutorial_workday_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezést a Workday "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a Workday tartozó felhasználót a felhasználó Azure AD-ben. Ez azt jelenti egy Azure AD-felhasználó és a kapcsolódó felhasználó a Workday közötti kapcsolat kapcsolatot kell létrehozni.

A Workday, rendelje az értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a Workday tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Munkanapok tesztfelhasználó létrehozása](#create-a-workday-test-user)**  - való Britta Simon egy megfelelője a Workday, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és a Workday-alkalmazás az egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezést a Workday megadásához hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Workday** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/workday-tutorial/tutorial_workday_samlbase.png)

3. Az a **Workday tartomány és az URL-címek** területen tegye a következőket:

    ![Az egyszeri bejelentkezés információkat WORKDAY tartomány- és URL-címek](./media/workday-tutorial/tutorial_workday_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://impl.workday.com/<tenant>/login-saml2.htmld`

    b. Az a **azonosító** szövegmezőhöz URL-címet írja be: `http://www.workday.com`

4. Ellenőrizze **megjelenítése speciális URL-beállításainak** és hajtsa végre a következő lépést:

    ![Az egyszeri bejelentkezés információkat WORKDAY tartomány- és URL-címek](./media/workday-tutorial/tutorial_workday_url1.png)

    Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe: `https://impl.workday.com/<tenant>/login-saml.htmld`
     
    > [!NOTE] 
    > Ezek az értékek nincsenek tényleges. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és a válasz URL-CÍMEN. A válasz URL-címe például altartomány kell rendelkeznie: www, wd2, wd3, wd3-impl, wd5, wd5-impl). Valami, amit például "*http://www.myworkday.com*" működik, de "*http://myworkday.com*" viszont nem. Ügyfél [Workday ügyfél-támogatási csoport](https://www.workday.com/en-us/partners-services/services/support.html) beolvasni ezeket az értékeket.  

5. A a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/workday-tutorial/tutorial_workday_certificate.png) 

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/workday-tutorial/tutorial_general_400.png)
    
7. A a **Workday konfigurációs** kattintson **konfigurálása Workday** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-címet, a SAML entitás azonosítója és a SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![WORKDAY konfiguráció](./media/workday-tutorial/tutorial_workday_configure.png) 

8. Egy másik webes böngészőablakban bejelentkezni a Workday vállalati webhely rendszergazdaként.

9. Az a **keresőmezőbe** nevű keresési **bérlői beállításának szerkesztése – biztonsági** a felső bal oldalán található a kezdőlap.
   
    ![Bérlői biztonsági szerkesztése](./media/workday-tutorial/IC782925.png "bérlői biztonsági szerkesztése")

10. Az a **átirányítási URL-eket** területen tegye a következőket:
   
    ![Átirányítási URL-eket](./media/workday-tutorial/IC7829581.png "átirányítási URL-címek")
   
    a. Kattintson a **sort ad hozzá a**.
   
    b. Az a **bejelentkezési átirányítási URL-cím** szövegmező és a **Mobile átirányítási URL-cím** szövegmezőhöz típusa a **bejelentkezési URL-cím** megadott a **Workday tartomány és az URL-címek** szakasza az Azure-portálon.
   
    c. Az Azure portálon a a **bejelentkezés konfigurálása** ablakban, a Másolás a **Sign-Out URL-cím**, majd illessze be azt a **kijelentkezési átirányítási URL-cím** szövegmező.

    d. A **környezetben használt** szövegmező, válassza ki a környezet nevét.  

    >[!NOTE]
    > A környezet attribútum értéke van kötve a bérlői URL-cím értéke:  
    >-Ha a tartomány neve a Workday-bérlői URL-cím kezdődik impl például: *https://impl.workday.com/\<tenant\>/login-saml2.htmld*), a **környezet** attribútum értéke megvalósításához.  
    >-Ha a tartomány neve kezdődik valami mást, szeretné-e forduljon [Workday ügyfél-támogatási csoport](https://www.workday.com/en-us/partners-services/services/support.html) beolvasni a megfelelő **környezet** érték.

11. Az a **SAML-alapú telepítő** területen tegye a következőket:
   
    ![SAML-alapú telepítő](./media/workday-tutorial/IC782926.png "SAML-alapú telepítő")
   
    a.  Válassza ki **SAML-alapú hitelesítés engedélyezéséhez**.
   
    b.  Kattintson a **sort ad hozzá a**.

12. Az a **SAML-alapú identitás-szolgáltatóktól** területen tegye a következőket:
   
    ![SAML-alapú identitás-szolgáltatóktól](./media/workday-tutorial/IC7829271.png "SAML-alapú identitás-szolgáltatóktól")
   
    a. Az a **identitás szolgáltatónevet** szövegmező, írja be a szolgáltató nevét (például: *SPInitiatedSSO*).
   
    b. Az Azure portálon a a **bejelentkezés konfigurálása** ablakban, a Másolás a **SAML Entitásazonosító** értékét, és illessze be azt a **kibocsátó** szövegmező.

    ![SAML-alapú identitás-szolgáltatóktól](./media/workday-tutorial/IC7829272.png "SAML-alapú identitás-szolgáltatóktól")
   
    c. Az Azure portálon a a **bejelentkezés konfigurálása** ablakban, a Másolás a **Sign-Out URL-cím** értékét, és illessze be azt a **kijelentkezési válaszcíme (URL)** szövegmező.

    d. Az Azure portálon a a **bejelentkezés konfigurálása** ablakban, a Másolás a **SAML-alapú egyszeri bejelentkezési URL-címe** értékét, és illessze be azt a **IdP egyszeri bejelentkezési URL-címe** szövegmező.

    e. A **környezetben használt** szövegmező, válassza ki a környezet nevét.

    f. Kattintson a **szolgáltató nyilvános kulcs Identitástanúsítvány**, és kattintson a **létrehozása**. 

    ![Hozzon létre](./media/workday-tutorial/IC782928.png "létrehozása")

    g. Kattintson a **x509 létrehozása nyilvános kulcs**. 

    ![Hozzon létre](./media/workday-tutorial/IC782929.png "létrehozása")

13. Az a **nézet x509 nyilvános kulcs** területen tegye a következőket: 
   
    ![Nézet x509 nyilvános kulcs](./media/workday-tutorial/IC782930.png "nézet x509 nyilvános kulcs") 
   
    a. Az a **neve** szövegmező, írja be a tanúsítvány nevét (például: *PPE\_SP*).
   
    b. Az a **érvényes a** szövegmező, írja be a tanúsítványhoz tartozó attribútum értéke érvénytelen.
   
    c.  Az a **érvényes való** szövegmező, írja be a tanúsítványhoz tartozó attribútum értéke érvénytelen.
   
    > [!NOTE]
    > Letölthető a érvényes dátumot és az érvényes, kattintson duplán a letöltött tanúsítvány az a dátum.  A dátumok találhatók a **részletek** fülre.
    > 
    >
   
    d.  Nyissa meg a base-64 kódolású tanúsítvány a Jegyzettömbben, és másolja a tartalmát.
   
    e.  Az a **tanúsítvány** szövegmezőhöz a vágólap tartalmának beillesztése.
   
    f.  Kattintson az **OK** gombra.

14. Hajtsa végre a következő lépéseket: 
   
    ![Egyszeri bejelentkezés konfigurációs](./media/workday-tutorial/WorkdaySSOConfiguratio.png "SSO-konfiguráció")
   
    a.  Az a **szolgáltatás Szolgáltatóazonosító** szövegmezőhöz típus **http://www.workday.com**.
   
    b. Válassza ki **nem Deflate a Szolgáltató által kezdeményezett hitelesítési kérelem**.
   
    c. Mint **hitelesítési kérelmek aláírási metódus**, jelölje be **SHA256**. 
   
    ![Hitelesítési kérelem aláírási metódus](./media/workday-tutorial/WorkdaySSOConfiguration.png "hitelesítési kérelmek aláírás módja") 
   
    d. Kattintson az **OK** gombra. 
   
    ![OK](./media/workday-tutorial/IC782933.png "OK")

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/workday-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/workday-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/workday-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/workday-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-workday-test-user"></a>Munkanapok tesztfelhasználó létrehozása

Ebben a szakaszban a Workday Britta Simon nevű felhasználó hoz létre. Együttműködve [Workday ügyfél-támogatási csoport](https://www.workday.com/en-us/partners-services/services/support.html) a felhasználók hozzáadása a Workday platform. Felhasználók kell létrehoznia és aktiválni az egyszeri bejelentkezés használata előtt. 

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Workday Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése Workday, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Workday**.

    ![Az alkalmazások listáját a Workday-hivatkozás](./media/workday-tutorial/tutorial_workday_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen a Workday csempére kattint, akkor kell beolvasása automatikusan bejelentkezett a Workday-alkalmazásba.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)


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

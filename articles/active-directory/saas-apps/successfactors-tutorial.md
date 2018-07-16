---
title: 'Oktatóanyag: Azure Active Directory-integráció az SuccessFactors |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és SuccessFactors között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 32bd8898-c2d2-4aa7-8c46-f1f5c2aa05f1
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: 8401462ed4bf5ef2ac1ff10cf3f6750cbed7b4e5
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39050801"
---
# <a name="tutorial-azure-active-directory-integration-with-successfactors"></a>Oktatóanyag: Azure Active Directory-integráció az SuccessFactors

Ebben az oktatóanyagban elsajátíthatja, hogyan SuccessFactors integrálása az Azure Active Directory (Azure AD).

SuccessFactors integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá SuccessFactors Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett SuccessFactors (egyszeri bejelentkezés), az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

SuccessFactors az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy SuccessFactors egyszeri bejelentkezéses engedélyezett előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. SuccessFactors hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-successfactors-from-the-gallery"></a>SuccessFactors hozzáadása a katalógusból
Az Azure AD integrálása a SuccessFactors konfigurálásához hozzá kell SuccessFactors a katalógusból a felügyelt SaaS-alkalmazások listájára.

**SuccessFactors hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **SuccessFactors**válassza **SuccessFactors** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában SuccessFactors](./media/successfactors-tutorial/tutorial_successfactors_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés SuccessFactors a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó SuccessFactors mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó SuccessFactors hivatkozás kapcsolata kell létrehozni.

SuccessFactors, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az SuccessFactors tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Hozzon létre egy SuccessFactors tesztfelhasználót](#create-a-successfactors-test-user)**  – egy megfelelője a Britta Simon SuccessFactors, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és SuccessFactors alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés SuccessFactors, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **SuccessFactors** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/successfactors-tutorial/tutorial_successfactors_samlbase.png)

3. Az a **SuccessFactors tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![SuccessFactors tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/successfactors-tutorial/tutorial_successfactors_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe:
    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.eu`|

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe:
    | |
    |--|
    | `https://www.successfactors.com/<companyname>`|
    | `https://www.successfactors.com`|
    | `https://<companyname>.successfactors.eu`|
    | `https://www.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://hcm4preview.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.eu`|
    | `https://www.successfactors.cn`|
    | `https://www.successfactors.cn/<companyname>`|

    c. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe:
    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.successfactors.com`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.successfactors.eu`|
    | `https://<companyname>.sapsf.eu`|
    | `https://<companyname>.sapsf.eu/<companyname>`|
    | `https://<companyname>.sapsf.cn`|
    | `https://<companyname>.sapsf.cn/<companyname>`|
         
    > [!NOTE] 
    > Ezek a értékei nem valódi. Frissítse a tényleges azonosítóját, válasz URL-cím és bejelentkezési URL-ezeket az értékeket. Kapcsolattartó [SuccessFactors ügyfél-támogatási csapatának](https://www.successfactors.com/en_us/support.html) beolvasni ezeket az értékeket. 

4. Az a **SAML-aláíró tanúsítvány** területén kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/successfactors-tutorial/tutorial_successfactors_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/successfactors-tutorial/tutorial_general_400.png)
    
6. Az a **SuccessFactors konfigurációs** területén kattintson **konfigurálása SuccessFactors** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/successfactors-tutorial/tutorial_successfactors_configure.png) 

7. Egy másik böngészőablakban, jelentkezzen be a **SuccessFactors felügyeleti portál** rendszergazdaként.
    
8. Látogasson el **alkalmazásbiztonsági** natívan **egyszeri bejelentkezést a szolgáltatás**. 

9. Helyezze el az értéket a **Token alaphelyzetbe** kattintson **Token mentése** SAML SSO engedélyezése.
   
    ![Egyszeri bejelentkezés az alkalmazás oldalán konfigurálása][11]

    > [!NOTE] 
    > Ez az érték a be-és kikapcsolása kapcsoló szolgál. Bármilyen érték mentette, a SAML SSO-e be. Ha egy üres értéket a rendszer menti a SAML SSO OFF állásban.

10. Az alábbi képernyőfelvétel natív, és hajtsa végre a következő műveleteket:
   
    ![Egyszeri bejelentkezés az alkalmazás oldalán konfigurálása][12]
   
    a. Válassza ki a **SAML SSO-v2** választógomb
   
    b. Állítsa be a **SAML, amely azt mutatja, entitás neve**(például az SAML-kibocsátó + vállalat neve).
   
    c. Az a **kiállítójának URL-címe** szövegmezőjébe illessze be a **SAML Entitásazonosító** az Azure Portalról másolt érték.
   
    d. Válassza ki **válasz (vevői generált/identitásszolgáltató/AP)** , **kötelező kötelező aláírás**.
   
    e. Válassza ki **engedélyezve** , **SAML jelző engedélyezése**.
   
    f. Válassza ki **nem** , **bejelentkezési kérelem-aláírás (SF generált/SP/RP)**.
   
    g. Válassza ki **böngésző/Post profil** , **SAML-alapú profilban**.
   
    h. Válassza ki **nem** , **kényszerítése a tanúsítvány érvényes időtartama**.
   
    i. Másolja a letöltött tanúsítvány-fájl tartalmát az Azure Portalról, és illessze be azt a **SAML-tanúsítvány ellenőrzése** szövegmezőbe.

    > [!NOTE] 
    > A tanúsítványok tartalmát kell kezdődnie tanúsítvány és a záró tanúsítvány címkéket.

11. Keresse meg az SAML-V2, és hajtsa végre az alábbi lépéseket:
   
    ![Egyszeri bejelentkezés az alkalmazás oldalán konfigurálása][13]
   
    a. Válassza ki **Igen** , **támogatja az SP által kezdeményezett globális kijelentkezési**.
   
    b. Az a **globális kijelentkezési szolgáltatás URL-címe (LogoutRequest cél)** szövegmezőjébe illessze be a **kijelentkezéses URL-cím** érték, amely másolta űrlapon az Azure Portalon.
   
    c. Válassza ki **nem** , **sp titkosítani kell az összes NameID elem megkövetelése**.
   
    d. Válassza ki **meghatározatlan** , **nameid-formátumához**.
   
    e. Válassza ki **Igen** , **engedélyezése sp által kezdeményezett bejelentkezési (AuthnRequest)**.
   
    f. Az a **küldési kérelmek, mint a céges szintű kiállító** szövegmezőjébe illessze be **SAML egyszeri bejelentkezési szolgáltatás URL-cím** az Azure Portalról másolt érték.

12. Hajtsa végre ezeket a lépéseket, ha azt szeretné, hogy a bejelentkezési felhasználónevek megkülönbözteti a kis-és nagybetű nincs megkülönböztetve.
   
    ![Egyszeri bejelentkezés konfigurálása][29]
    
    a. Látogasson el **vállalati beállítások**(aljának közelében).
   
    b. Jelölje be a jelölőnégyzetet közel **engedélyezése eset – bizalmas adatokkal nem rendelkező felhasználónév**.
   
    c.Click **mentése**.
   
    > [!NOTE] 
    > Ha ezt engedélyezni szeretné, a rendszer ellenőrzi, ha létrehoz egy ismétlődő SAML-bejelentkezési név. Például ha az ügyfél a felhasználó1, user1 felhasználónevek rendelkezik. Kis-és nagybetűk azonnal véve teszi ezeket az ismétlődéseket. A rendszer nem engedélyezi a szolgáltatást, és a egy hibaüzenetet kínálja. Az ügyfél kell módosítani a felhasználónevek egyikét, így más van írva.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/successfactors-tutorial/create_aaduser_01.png)

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/successfactors-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/successfactors-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/successfactors-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-successfactors-test-user"></a>SuccessFactors tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók SuccessFactors jelentkezzen be, akkor ki kell építeni SuccessFactors be.  
SuccessFactors, esetén kiépítése a manuális feladat.

SuccessFactors létrehozott felhasználók lekéréséhez lépjen kapcsolatba kell a [SuccessFactors támogatási csoportjának](https://www.successfactors.com/en_us/support.html).

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés SuccessFactors Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel SuccessFactors, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **SuccessFactors**.

    ![Az alkalmazások listáját a SuccessFactors hivatkozásra](./media/successfactors-tutorial/tutorial_successfactors_app.png)  

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a SuccessFactors csempére kattint, meg kell lekérése automatikusan bejelentkezett az SuccessFactors alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/successfactors-tutorial/tutorial_general_01.png
[2]: ./media/successfactors-tutorial/tutorial_general_02.png
[3]: ./media/successfactors-tutorial/tutorial_general_03.png
[4]: ./media/successfactors-tutorial/tutorial_general_04.png

[11]: ./media/successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/successfactors-tutorial/tutorial_successfactors_09.png
[14]: ./media/successfactors-tutorial/tutorial_general_05.png
[15]: ./media/successfactors-tutorial/tutorial_general_06.png
[29]: ./media/successfactors-tutorial/tutorial_successfactors_10.png

[100]: ./media/successfactors-tutorial/tutorial_general_100.png

[200]: ./media/successfactors-tutorial/tutorial_general_200.png
[201]: ./media/successfactors-tutorial/tutorial_general_201.png
[202]: ./media/successfactors-tutorial/tutorial_general_202.png
[203]: ./media/successfactors-tutorial/tutorial_general_203.png


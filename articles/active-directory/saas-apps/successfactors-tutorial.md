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
ms.date: 11/13/2018
ms.author: jeedes
ms.openlocfilehash: 89224b32efaecdf7a2797b034b1beac7ad191ee5
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51685225"
---
# <a name="tutorial-azure-active-directory-integration-with-successfactors"></a>Oktatóanyag: Azure Active Directory-integráció az SuccessFactors

Ebben az oktatóanyagban elsajátíthatja, hogyan SuccessFactors integrálása az Azure Active Directory (Azure AD).

SuccessFactors integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá SuccessFactors Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett SuccessFactors (egyszeri bejelentkezés), az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Előfeltételek

SuccessFactors az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy SuccessFactors egyszeri bejelentkezés engedélyezve van az előfizetés

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

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

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

Az Azure AD egyszeri bejelentkezés az SuccessFactors tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[SuccessFactors tesztfelhasználó létrehozása](#creating-a-successfactors-test-user)**  – egy megfelelője a Britta Simon SuccessFactors, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
4. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és SuccessFactors alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés SuccessFactors, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **SuccessFactors** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen kattintson **kiválasztása** a **SAML** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés konfigurálása](common/tutorial_general_301.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](common/editconfigure.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

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
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL-, azonosítóját és válasz URL-cím. Kapcsolattartó [SuccessFactors ügyfél-támogatási csapatának](https://www.successfactors.com/support.html) beolvasni ezeket az értékeket. 

5. Az a **SAML-aláíró tanúsítvány** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez **tanúsítvány (Base64)**, és mentse a tanúsítványfájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/successfactors-tutorial/tutorial_successfactors_certificate.png) 

6. Az a **SuccessFactors beállítása** területén másolja a megfelelő URL-címet a követelmény alapján.

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

    ![SuccessFactors konfiguráció](common/configuresection.png)

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
   
    c. Az a **kiállítójának URL-címe** szövegmezőjébe illessze be a **az Azure AD-azonosító** az Azure Portalról másolt érték.
   
    d. Válassza ki **helyességi feltétel** , **kötelező kötelező aláírás**.
   
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

### <a name="creating-a-successfactors-test-user"></a>SuccessFactors tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók SuccessFactors jelentkezzen be, akkor ki kell építeni SuccessFactors be.  
SuccessFactors, esetén kiépítése a manuális feladat.

SuccessFactors létrehozott felhasználók lekéréséhez lépjen kapcsolatba kell a [SuccessFactors támogatási csoportjának](https://www.successfactors.com/support.html).

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés SuccessFactors Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**válassza **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201]

2. Az alkalmazások listájában jelölje ki a **SuccessFactors**.

    ![Egyszeri bejelentkezés konfigurálása](./media/successfactors-tutorial/tutorial_successfactors_app.png)

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Az a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelése** gombra.

### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a SuccessFactors csempére kattint, meg kell lekérése automatikusan bejelentkezett az SuccessFactors alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

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
[11]: ./media/successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/successfactors-tutorial/tutorial_successfactors_09.png
[29]: ./media/successfactors-tutorial/tutorial_successfactors_10.png

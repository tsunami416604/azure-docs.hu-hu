---
title: 'Oktatóanyag: Azure Active Directoryval integrált SuccessFactors |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és SuccessFactors között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 32bd8898-c2d2-4aa7-8c46-f1f5c2aa05f1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: 63ee7f7a08d16048eb3f0672cac4c2f41a3ed0f4
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-successfactors"></a>Oktatóanyag: Azure Active Directoryval integrált SuccessFactors

Ebben az oktatóanyagban elsajátíthatja SuccessFactors integrálása az Azure Active Directory (Azure AD).

SuccessFactors integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér SuccessFactors szabályozhatja.
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett SuccessFactors (egyszeri bejelentkezés) számára a saját Azure AD-fiókok.
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs SuccessFactors, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy SuccessFactors egyszeri bejelentkezés engedélyezve van az előfizetésben

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből SuccessFactors hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-successfactors-from-the-gallery"></a>A gyűjteményből SuccessFactors hozzáadása
Az Azure AD integrálása a SuccessFactors konfigurálásához kell hozzáadnia SuccessFactors a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből SuccessFactors hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **SuccessFactors**, jelölje be **SuccessFactors** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában SuccessFactors](./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján SuccessFactors.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó SuccessFactors a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a SuccessFactors közötti kapcsolat kapcsolatot kell létrehozni.

SuccessFactors, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a SuccessFactors tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[SuccessFactors tesztfelhasználó létrehozása](#create-a-successfactors-test-user)**  - való Britta Simon valami SuccessFactors, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az SuccessFactors alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés SuccessFactors, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **SuccessFactors** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_samlbase.png)

3. Az a **SuccessFactors tartomány és az URL-címek** területen tegye a következőket:

    ![Az egyszeri bejelentkezés információk SuccessFactors tartomány és az URL-címek](./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe:
    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.eu`|

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe:
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

    c. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe:
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
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges azonosítója, válasz URL-CÍMEN és bejelentkezési URL-cím. Ügyfél [SuccessFactors ügyfél-támogatási csoport](https://www.successfactors.com/en_us/support.html) beolvasni ezeket az értékeket. 

4. Az a **SAML-aláíró tanúsítványa** kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-successfactors-tutorial/tutorial_general_400.png)
    
6. A a **SuccessFactors konfigurációs** kattintson **konfigurálása SuccessFactors** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-címet, a SAML entitás azonosítója és a SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_configure.png) 

7. Egy másik webes böngészőablakban, jelentkezzen be a **SuccessFactors felügyeleti portál** rendszergazdaként.
    
8. Látogasson el **alkalmazásbiztonsági** és natív módon **egyszeri bejelentkezést a szolgáltatás**. 

9. Helyezze el az értéket a **Token alaphelyzetbe** kattintson **Token mentése** SAML SSO engedélyezése.
   
    ![Alkalmazás ügyféloldali egyszeri bejelentkezés konfigurálása][11]

    > [!NOTE] 
    > Ez az érték a be-és kikapcsolása kapcsoló lesz. Ha bármely érték menti, a SAML SSO ON értékre állítva. Ha egy üres érték. menti a SAML SSO OFF állásban.

10. Natív módon képernyőkép alatt, és hajtsa végre a következő műveleteket:
   
    ![Alkalmazás ügyféloldali egyszeri bejelentkezés konfigurálása][12]
   
    a. Válassza ki a **SAML v2 SSO** választógomb
   
    b. Állítsa be a **SAML biztos fél neve**(például a SAML kibocsátó + a vállalat neve).
   
    c. Az a **kiállítójának URL-címe** szövegmező, illessze be a **SAML Entitásazonosító** érték, amely az Azure portálról másolta.
   
    d. Válassza ki **választ (felhasználói generált/IdP/AP)** , **kötelező kötelező aláírás**.
   
    e. Válassza ki **engedélyezett** , **SAML jelző engedélyezése**.
   
    f. Válassza ki **nem** , **bejelentkezési kérelem-aláírás (KB generált/SP/RP)**.
   
    g. Válassza ki **böngésző/utólagos profil** , **SAML profil**.
   
    h. Válassza ki **nem** , **kényszerítése a tanúsítvány érvényes időtartama**.
   
    i. A letöltött tanúsítvány-fájl tartalmának másolása Azure-portálon, és illessze be azt a **SAML ellenőrzése tanúsítvány** szövegmező.

    > [!NOTE] 
    > A tanúsítványok tartalmát kell kezdődniük tanúsítványt és a záró tanúsítvány címkék.

11. Navigáljon a SAML V2, és végezze el az alábbi lépéseket:
   
    ![Alkalmazás ügyféloldali egyszeri bejelentkezés konfigurálása][13]
   
    a. Válassza ki **Igen** , **támogatja a Szolgáltató által kezdeményezett globális kijelentkezési**.
   
    b. Az a **globális kijelentkezési URL-címe (LogoutRequest cél)** szövegmező, illessze be a **Sign-Out URL-cím** értéket, amelyet másolt alkotnak az Azure-portálon.
   
    c. Válassza ki **nem** , **sp titkosítani kell az összes NameID elem szükséges**.
   
    d. Válassza ki **meghatározatlan** , **NameID formátum**.
   
    e. Válassza ki **Igen** , **engedélyezése sp kezdeményezett (AuthnRequest) bejelentkezési**.
   
    f. Az a **küldési kérelmek vállalati szintű kiállítóként** szövegmezőhöz Beillesztés **SAML-alapú egyszeri bejelentkezési URL-címe** érték, amely az Azure portálról másolta.

12. Hajtsa végre ezeket a lépéseket, ha engedélyezni szeretné a bejelentkezési felhasználónevek-és nagybetűket.
   
    ![Egyszeri bejelentkezés konfigurálása][29]
    
    a. Látogasson el **vállalati beállítások**(közelében alsó).
   
    b. Jelölje be a jelölőnégyzetet közelében **engedélyezése nem-nagybetűk felhasználónév**.
   
    c.Click **mentése**.
   
    > [!NOTE] 
    > Ezzel a kísérli meg, ha a rendszer ellenőrzi, ha létrehoz egy duplikált SAML-bejelentkezési nevet. Például ha az ügyfél rendelkezik felhasználónevek felhasználó1, mind a felhasználó1. Ezeket az ismétlődéseket véve számítógépnél nagybetűk teszi. A rendszer hibaüzenetet lehetővé teszi, és engedélyezze a szolgáltatást. Az ügyfél megváltoztatja a felhasználónevek, a másik hibátlanul kell.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-successfactors-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-successfactors-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/active-directory-saas-successfactors-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-successfactors-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-successfactors-test-user"></a>SuccessFactors tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók SuccessFactors bejelentkezni, akkor ki kell építenie a SuccessFactors.  
SuccessFactors, ha egy kézi tevékenység.

Ahhoz, hogy a felhasználó hozott létre az SuccessFactors, kapcsolatba kell lépnie a [SuccessFactors támogatási csoport](https://www.successfactors.com/en_us/support.html).

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés SuccessFactors Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése SuccessFactors, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **SuccessFactors**.

    ![Az alkalmazások listáját a SuccessFactors hivatkozás](./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen SuccessFactors csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az SuccessFactors alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_04.png

[11]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_09.png
[14]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_05.png
[15]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_06.png
[29]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_10.png

[100]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_203.png


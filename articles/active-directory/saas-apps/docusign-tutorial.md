---
title: 'Oktatóanyag: Azure Active Directoryval integrált DocuSign |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és DocuSign között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: jeedes
ms.openlocfilehash: 7b8c11a41ee0da5e65a89cfaefc35563eba81a06
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2018
ms.locfileid: "35927692"
---
# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>Oktatóanyag: Azure Active Directoryval integrált DocuSign

Ebben az oktatóanyagban elsajátíthatja DocuSign integrálása az Azure Active Directory (Azure AD).

DocuSign integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a DocuSign hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett DocuSign (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs DocuSign, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy DocuSign egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből DocuSign hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-docusign-from-the-gallery"></a>A gyűjteményből DocuSign hozzáadása
Az Azure AD integrálása a DocuSign konfigurálásához kell hozzáadnia DocuSign a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből DocuSign hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Kattintson a **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **DocuSign**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/docusign-tutorial/tutorial_docusign_search.png)

5. Az eredmények panelen válassza ki a **DocuSign**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/docusign-tutorial/tutorial_docusign_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban konfigurálása, és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon." nevű tesztfelhasználó alapján DocuSign

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó DocuSign a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a DocuSign közötti kapcsolat kapcsolatot kell létrehozni.

Ez a hivatkozás kapcsolat létesíti értéket rendeli az **felhasználónév** értékeként Azure AD-ben a **felhasználónév** DocuSign a.

Az Azure AD egyszeri bejelentkezést a DocuSign tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[DocuSign tesztfelhasználó létrehozása](#creating-a-docusign-test-user)**  - való Britta Simon valami DocuSign, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az DocuSign alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés DocuSign, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **DocuSign** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/docusign-tutorial/tutorial_docusign_samlbase.png)

3. Az a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány (Base 64)** , és mentse a fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/docusign-tutorial/tutorial_docusign_certificate.png) 

4. A a **DocuSign konfigurációs** az Azure portál, kattintson a szakasz **konfigurálása DocuSign** konfigurálása bejelentkezés ablak megnyitásához. Másolás a **Sign-Out URL-címet, a SAML entitás azonosítója és a SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**
    
    ![Egyszeri bejelentkezés konfigurálása](./media/docusign-tutorial/tutorial_docusign_configure.png)

5. Egy másik webes böngészőablakban, jelentkezzen be a **DocuSign felügyeleti portál** rendszergazdaként.

6. Kattintson a bal oldali navigációs menü **tartományok**.
   
    ![Egyszeri bejelentkezés konfigurálása][51]

7. Kattintson a jobb oldali ablaktáblában **jogcím tartomány**.
   
    ![Egyszeri bejelentkezés konfigurálása][52]

8. A a **tartomány jogcím** párbeszédpanelen, a a **tartománynév** szövegmező, írja be a vállalati tartományhoz, és kattintson **jogcím**. Győződjön meg arról, hogy ellenőrizze a tartományt, és aktív állapota.
   
    ![Egyszeri bejelentkezés konfigurálása][53]

9. A bal oldali menüben kattintson **identitás-szolgáltatóktól**  
   
    ![Egyszeri bejelentkezés konfigurálása][54]
10. Kattintson a jobb oldali ablaktáblában **identitásszolgáltató hozzáadása**. 
   
    ![Egyszeri bejelentkezés konfigurálása][55]

11. Az a **identitás szolgáltató beállításainak** lapon, a következő lépésekkel:
   
    ![Egyszeri bejelentkezés konfigurálása][56]

    a. Az a **neve** szövegmező, írjon be egy egyedi nevet a konfigurációt. Ne használjon szóközt.

    b. Beillesztés **SAML Entitásazonosító** azokat a **Identity Provider kibocsátó** szövegmező.

    c. Beillesztés **SAML-alapú egyszeri bejelentkezési URL-címe** azokat a **Identity Provider bejelentkezési URL-cím** szövegmező.

    d. Beillesztés **Sign-Out URL-cím** azokat a **Identity Provider kijelentkezési URL-cím** szövegmező.

    e. Válassza ki **AuthN kérelmet aláíró**.

    f. Mint **küldése AuthN kérésére**, jelölje be **POST**.

    g. Mint **küldési kijelentkezési kérésére**, jelölje be **beolvasása**.

12. Az a **egyéni attribútum leképezési** területen válassza ki a mezőt hozzá kívánja rendelni az Azure AD jogcímet. Ebben a példában a **emailaddress** jogcím értékét le van képezve **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**. Azure ad-e-mailek jogcímet alapértelmezett jogcím neve. 
   
    > [!NOTE]
    > Használja a megfelelő **felhasználói azonosító** a felhasználó az Azure AD felhasználó-hozzárendelés DocuSign hozzárendelését. Válassza ki a megfelelő mezőben, és adja meg a szervezet beállításai alapján a megfelelő értéket.
          
    ![Egyszeri bejelentkezés konfigurálása][57]

13. Az a **szolgáltató Identitástanúsítvány** területén kattintson **tanúsítvány hozzáadása**, majd töltse fel a tanúsítvány már letöltötte az Azure AD portálon.   
   
    ![Egyszeri bejelentkezés konfigurálása][58]

14. Kattintson a **Save** (Mentés) gombra.

15. Az a **identitás-szolgáltatóktól** kattintson **műveletek**, és kattintson a **végpontok**.   
   
    ![Egyszeri bejelentkezés konfigurálása][59]
 
16. Az a **SAML 2.0 végpontok megtekintése** lap **DocuSign felügyeleti portál**, hajtsa végre a következő lépéseket:
   
    ![Egyszeri bejelentkezés konfigurálása][60]
   
    a. Másolás a **szolgáltató kibocsátó URL-címe**, majd illessze be a **azonosítója** a szövegmező **DocuSign tartomány és az URL-címek** az Azure portál, a minta a következő szakaszban: `https://<subdomain>.docusign.com/organization/<uniqueID>/saml2/login/sp/<uniqueID>`.
   
    b. Másolás a **szolgáltató bejelentkezési URL-címe**, majd illessze be a **URL-cím bejelentkezési** a szövegmező **DocuSign tartomány és az URL-címek** az Azure portál, a minta a következő szakaszban: `https://<subdomain>.docusign.com/organization/<uniqueID>/saml2/` .

    ![Egyszeri bejelentkezés konfigurálása](./media/docusign-tutorial/tutorial_docusign_url.png)
      
    c.  Kattintson a **Bezárás**
    
17. Az Azure portálon kattintson **mentése**.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/docusign-tutorial/tutorial_general_400.png)

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/docusign-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/docusign-tutorial/create_aaduser_02.png) 

3. Kattintson a párbeszédpanel tetején **Hozzáadás** megnyitásához a **felhasználói** párbeszédpanel.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/docusign-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/docusign-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-docusign-test-user"></a>DocuSign tesztfelhasználó létrehozása

Alkalmazás által támogatott **csak az idő a felhasználók átadása** , miután a felhasználók hitelesítésére az alkalmazás automatikusan létrejönnek.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított a hozzáférés DocuSign Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése DocuSign, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **DocuSign**.

    ![Egyszeri bejelentkezés konfigurálása](./media/docusign-tutorial/tutorial_docusign_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen DocuSign csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az DocuSign alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)
* [A felhasználók átadása konfigurálása](docusign-provisioning-tutorial.md)


<!--Image references-->

[1]: ./media/docusign-tutorial/tutorial_general_01.png
[2]: ./media/docusign-tutorial/tutorial_general_02.png
[3]: ./media/docusign-tutorial/tutorial_general_03.png
[4]: ./media/docusign-tutorial/tutorial_general_04.png
[51]: ./media/docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/docusign-tutorial/tutorial_docusign_29.png
[100]: ./media/docusign-tutorial/tutorial_general_100.png

[200]: ./media/docusign-tutorial/tutorial_general_200.png
[201]: ./media/docusign-tutorial/tutorial_general_201.png
[202]: ./media/docusign-tutorial/tutorial_general_202.png
[203]: ./media/docusign-tutorial/tutorial_general_203.png


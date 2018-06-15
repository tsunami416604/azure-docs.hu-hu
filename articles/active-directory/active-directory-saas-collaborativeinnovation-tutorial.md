---
title: 'Oktatóanyag: Azure Active Directoryval integrált együttműködési innováció |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és az együttműködési innováció között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: bba95df3-75a4-4a93-8805-b3a8aa3d4861
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2017
ms.author: jeedes
ms.openlocfilehash: af431fea063b3643299f2558a88eb80243932dfd
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34339373"
---
# <a name="tutorial-azure-active-directory-integration-with-collaborative-innovation"></a>Oktatóanyag: Azure Active Directoryval integrált együttműködési innováció

Ebben az oktatóanyagban elsajátíthatja együttműködési innováció integrálása az Azure Active Directory (Azure AD).

Együttműködési innováció integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a együttműködési innováció hozzáféréssel rendelkező Azure AD-ben
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett való együttműködést innováció (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Együttműködési innováció konfigurálása az Azure AD-integrációs, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- A együttműködési innováció egyszeri bejelentkezés engedélyezve van az előfizetésben

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Együttműködési innováció hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-collaborative-innovation-from-the-gallery"></a>Együttműködési innováció hozzáadása a gyűjteményből
Az Azure AD integrálása a együttműködési innováció konfigurálásához kell hozzáadnia együttműködési innováció a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből együttműködési innováció hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **együttműködési innováció**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-collaborativeinnovation-tutorial/tutorial_collaborativeinnovation_search.png)

5. Az eredmények panelen válassza ki a **együttműködési innováció**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-collaborativeinnovation-tutorial/tutorial_collaborativeinnovation_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezést az együttműködési innováció "Britta Simon." nevű tesztfelhasználó alapján

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó együttműködési innováció a felhasználó Azure AD-ben. Ez azt jelenti egy Azure AD-felhasználó és a kapcsolódó felhasználó a együttműködési innováció közötti kapcsolat kapcsolatot kell létrehozni.

Együttműködési innováció, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést az együttműködési innováció tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Együttműködési innováció tesztfelhasználó létrehozása](#creating-a-collaborative-innovation-test-user)**  - való egy megfelelője a Britta Simon együttműködési innováció, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és az együttműködési innováció alkalmazásban egyszeri bejelentkezés konfigurálása.

**Együttműködési innováció konfigurálása az Azure AD egyszeri bejelentkezést, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **együttműködési innováció** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-collaborativeinnovation-tutorial/tutorial_collaborativeinnovation_samlbase.png)

3. Az a **együttműködési innováció tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-collaborativeinnovation-tutorial/tutorial_collaborativeinnovation_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://<instancename>.foundry.<companyname>.com/`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: `https://<instancename>.foundry.<companyname>.com`
    
    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Ügyfél [együttműködési innováció ügyfél-támogatási csoport](https://www.unilever.com/contact/) beolvasni ezeket az értékeket.  

4. Együttműködési innováció alkalmazás vár a SAML helyességi feltételek egy meghatározott formátumban. Állítsa be a következő jogcímeket ehhez az alkalmazáshoz. Ezek az attribútumok értékének kezelheti a "**felhasználói attribútumok**" szakasz alkalmazás integráció lapján. Az alábbi képernyőfelvételen látható egy példa a.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-collaborativeinnovation-tutorial/attribute.png)
    
5. Kattintson a **nézet és egyéb felhasználói attribútumok szerkesztése** jelölőnégyzet a **felhasználói attribútumok** szakaszban bontsa ki az attribútumok. Hajtsa végre a következő lépéseket mindegyik a megjelenített attribútumok-

    | Attribútum neve | Attribútum értéke |
    | ---------------| --------------- |    
    | givenName | User.givenName |
    | Vezetéknév | User.surname |
    | e-mail cím | User.userPrincipalName |
    | név | User.userPrincipalName |

    a. Az attribútum megnyitásához kattintson a **attribútum szerkesztése** ablak.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-collaborativeinnovation-tutorial/url_update.png)

    b. Az URL-cím érték törlése a **Namespace**.
    
    c. Kattintson a **Ok** a beállítás mentéséhez.

6. Az a **SAML-aláíró tanúsítványa** területen kattintson **metaadatainak XML-kódja** és mentse a metaadat-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-collaborativeinnovation-tutorial/tutorial_collaborativeinnovation_certificate.png) 

7. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-collaborativeinnovation-tutorial/tutorial_general_400.png)

8. Egyszeri bejelentkezés konfigurálása **együttműködési innováció** oldalon kell küldeniük a letöltött **metaadatainak XML-kódja** való [együttműködési innováció támogatási csoport](https://www.unilever.com/contact/). Akkor állítsa be ezt a beállítást, hogy a SAML SSO kapcsolat mindkét oldalán megfelelően beállítva.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-collaborativeinnovation-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-collaborativeinnovation-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-collaborativeinnovation-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-collaborativeinnovation-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-collaborative-innovation-test-user"></a>Együttműködési innováció tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók együttműködési innováció bejelentkezni, akkor ki kell építenie az együttműködést innováció.  

Ez az alkalmazás esetén automatikus, ha az alkalmazás csak az idő a felhasználók átadása támogatja. Így nincs szükség az itt bármely lépések végrehajtásához.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban Britta Simon használandó Azure egyszeri bejelentkezés által biztosított együttműködési innováció hozzáférés engedélyezéséhez.

![Felhasználó hozzárendelése][200] 

**Együttműködési innováció Britta Simon rendel, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **együttműködési innováció**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-collaborativeinnovation-tutorial/tutorial_collaborativeinnovation_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési Panel együttműködési innováció mozaik gombra kattint, szerezheti be együttműködési innováció alkalmazás bejelentkezési oldalán.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-collaborativeinnovation-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-collaborativeinnovation-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-collaborativeinnovation-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-collaborativeinnovation-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-collaborativeinnovation-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-collaborativeinnovation-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-collaborativeinnovation-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-collaborativeinnovation-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-collaborativeinnovation-tutorial/tutorial_general_203.png


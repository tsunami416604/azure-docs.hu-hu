---
title: "Oktatóanyag: Azure Active Directoryval integrált FilesAnywhere |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és FilesAnywhere között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: jeedes
ms.openlocfilehash: 4153056bd21006061c6ad8ff9cf3c17de9248628
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="tutorial-azure-active-directory-integration-with-filesanywhere"></a>Oktatóanyag: Azure Active Directoryval integrált FilesAnywhere

Ebben az oktatóanyagban elsajátíthatja FilesAnywhere integrálása az Azure Active Directory (Azure AD).

FilesAnywhere integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a FilesAnywhere hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett FilesAnywhere (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure felügyeleti portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs FilesAnywhere, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy FilesAnywhere egyszeri bejelentkezés engedélyezve van az előfizetésben


> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.


Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, ha ez nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből FilesAnywhere hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés


## <a name="adding-filesanywhere-from-the-gallery"></a>A gyűjteményből FilesAnywhere hozzáadása
Az Azure AD integrálása a FilesAnywhere konfigurálásához kell hozzáadnia FilesAnywhere a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből FilesAnywhere hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure felügyeleti portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Kattintson a **Hozzáadás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **FilesAnywhere**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-FilesAnywhere-tutorial/tutorial_FilesAnywhere_search.png)

5. Az eredmények panelen válassza ki a **FilesAnywhere**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-FilesAnywhere-tutorial/tutorial_FilesAnywhere_addfromgallery.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján FilesAnywhere.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó FilesAnywhere a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a FilesAnywhere közötti kapcsolat kapcsolatot kell létrehozni.

Ez a hivatkozás kapcsolat létesíti értéket rendeli az **felhasználónév** értékeként Azure AD-ben a **felhasználónév** FilesAnywhere a.

Az Azure AD egyszeri bejelentkezést a FilesAnywhere tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[FilesAnywhere tesztfelhasználó létrehozása](#creating-a-filesanywhere-test-user)**  - való Britta Simon valami FilesAnywhere, amely csatolva van rá, hogy az Azure AD ábrázolása.
3. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
4. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure felügyeleti portálon, és konfigurálása egyszeri bejelentkezéshez az FilesAnywhere alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés FilesAnywhere, hajtsa végre az alábbi lépéseket:**

1. Az Azure felügyeleti portálján a a **FilesAnywhere** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. A a **egyszeri bejelentkezés** párbeszédpanel, mint **mód** kiválasztása **SAML-alapú bejelentkezés** a engedélyezése az egyszeri bejelentkezéshez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-FilesAnywhere-tutorial/tutorial_FilesAnywhere_samlbase.png)

3. Az a **FilesAnywhere tartomány és az URL-címek** szakaszban, ha szeretne beállítani az alkalmazás **IDP kezdeményezett mód**:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-FilesAnywhere-tutorial/tutorial_filesanywhere_url.png)
    
    a. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe:`https://<company name>.filesanywhere.com/saml20.aspx?c=215`
> [!NOTE]
> Ne feledje, hogy az érték **215** van egy **clientid** és csak egy példa. Cserélje le a tényleges clientid értéket kell.

4. Az a **FilesAnywhere tartomány és az URL-címek** szakaszban, ha szeretne beállítani az alkalmazás **Szolgáltató kezdeményezett mód**, hajtsa végre a következő lépéseket:
    
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-FilesAnywhere-tutorial/tutorial_filesanywhere_url1.png)

    a. Kattintson a **megjelenítése speciális URL-beállításainak** beállítás

    b. Az a **URL-cím bejelentkezési** szövegmező, adja meg a következő minta használatával URL-címe:`https://<sub domain>.filesanywhere.com/`

    > [!NOTE] 
    > Ne feledje, hogy ezek nincsenek a valódi értékek. Akkor frissítheti ezeket az értékeket a tényleges URL-cím bejelentkezési és válasz URL-CÍMEN. Ügyfél [FilesAnywhere támogatási csoport](mailto:support@FilesAnywhere.com) beolvasni ezeket az értékeket. 

5. FilesAnywhere alkalmazás vár a SAML helyességi feltételek egy meghatározott formátumban. Állítsa be a következő jogcímeket ehhez az alkalmazáshoz. Ezek az attribútumok értékének kezelheti a "**felhasználói attribútumok**" szakasz alkalmazás integráció lapján. Az alábbi képernyőfelvételen látható egy példa a.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-FilesAnywhere-tutorial/tutorial_filesanywhere_attribute.png)
    
    Amikor a felhasználók előfizet az FilesAnywhere értékének beolvasása **clientid** attribútumot [FilesAnywhere team](mailto:support@FilesAnywhere.com). Fel kell vennie az "Ügyfél-azonosító" attribútum FilesAnywhere által biztosított egyedi értékkel. A fent látható összes attribútum megadása kötelező.
    > [!NOTE] 
    > Ne feledje, hogy az érték **2331** a **clientid** csak egy példa. Meg kell adnia a tényleges érték.


6. A a **felhasználói attribútumok** a szakasz a **egyszeri bejelentkezés** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, a fenti ábrán látható módon, és hajtsa végre a következő lépéseket:
    
    | Attribútum neve | Attribútum-érték |
    | ---------------| --------------- |    
    | ClientID | *"uniquevalue"* |

    a. Kattintson a **Hozzáadás attribútum** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-FilesAnywhere-tutorial/tutorial_FilesAnywhere_04.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-FilesAnywhere-tutorial/tutorial_FilesAnywhere_05.png)
    
    b. Az a **neve** szövegmező, írja be az adott sorhoz feltüntetett attribútumot nevét.
    
    c. Az a **érték** kilistázásához írja be a sorhoz látható attribútum értéke.
    
    d. Kattintson a **Ok**

7. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-FilesAnywhere-tutorial/tutorial_general_400.png)

8. A a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-FilesAnywhere-tutorial/tutorial_FilesAnywhere_certificate.png) 

9. A a **FilesAnywhere konfigurációs** kattintson **konfigurálása FilesAnywhere** megnyitásához **bejelentkezés konfigurálása** ablak.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-FilesAnywhere-tutorial/tutorial_FilesAnywhere_configure.png) 

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-FilesAnywhere-tutorial/tutorial_FilesAnywhere_configuresignon.png)

10. Ahhoz, hogy egyszeri Bejelentkezéses konfigurációs teljes FilesAnywhere végén az alkalmazás, lépjen kapcsolatba [FilesAnywhere támogatási csoport](mailto:support@FilesAnywhere.com) és adja meg a letöltött SAML jogkivonat-aláíró tanúsítvány és az egyszeri bejelentkezés (SSO) URL-címet.

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure felügyeleti portálján Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure Management portal**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-FilesAnywhere-tutorial/create_aaduser_01.png) 

2. Ugrás a **felhasználók és csoportok** kattintson **minden felhasználó** azon felhasználók listájának megjelenítéséhez.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-FilesAnywhere-tutorial/create_aaduser_02.png) 

3. Kattintson a párbeszédpanel tetején **Hozzáadás** megnyitásához a **felhasználói** párbeszédpanel.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-FilesAnywhere-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-FilesAnywhere-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra. 



### <a name="creating-a-filesanywhere-test-user"></a>FilesAnywhere tesztfelhasználó létrehozása

Alkalmazás támogatja a csak az idő a felhasználók átadása, miután a felhasználók hitelesítésére az alkalmazás automatikusan létrejön. 


### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított a hozzáférés FilesAnywhere Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése FilesAnywhere, hajtsa végre az alábbi lépéseket:**

1. Az Azure felügyeleti portálra, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **FilesAnywhere**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-FilesAnywhere-tutorial/tutorial_FilesAnywhere_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    


### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen FilesAnywhere csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az FilesAnywhere alkalmazására.


## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-FilesAnywhere-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-FilesAnywhere-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-FilesAnywhere-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-FilesAnywhere-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-FilesAnywhere-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-FilesAnywhere-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-FilesAnywhere-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-FilesAnywhere-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-FilesAnywhere-tutorial/tutorial_general_203.png

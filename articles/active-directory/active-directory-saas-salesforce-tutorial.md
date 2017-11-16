---
title: "Oktatóanyag: Azure Active Directoryval integrált Salesforce |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a Salesforce között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: d2d7d420-dc91-41b8-a6b3-59579e043b35
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: jeedes
ms.openlocfilehash: 7471a6999ac4dcd15f5162e69cb0100cc1e3b14f
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce"></a>Oktatóanyag: Azure Active Directoryval integrált Salesforce

Ebben az oktatóanyagban elsajátíthatja Salesforce integrálása az Azure Active Directory (Azure AD).

Salesforce integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a Salesforce hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni aláírt a Salesforce (egyszeri bejelentkezés) a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Salesforce, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- A Salesforce egyszeri bejelentkezés engedélyezve van az előfizetésben

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Salesforce hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-salesforce-from-the-gallery"></a>Salesforce hozzáadása a gyűjteményből
Az Azure AD integrálása a Salesforce konfigurálásához kell hozzáadnia Salesforce a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**Vegye fel a Salesforce a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Kattintson a **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **Salesforce**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_search.png)

5. Az eredmények panelen válassza ki a **Salesforce**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezést a Salesforce "Britta Simon." nevű tesztfelhasználó alapján

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó a Salesforce-ban a felhasználók az Azure ad-ben. Ez azt jelenti egy Azure AD-felhasználó és a kapcsolódó felhasználó a Salesforce-ban közötti kapcsolat kapcsolatot kell létrehozni.

Ez a hivatkozás kapcsolat létesíti értéket rendeli az **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a Salesforce-ban.

Az Azure AD egyszeri bejelentkezést a Salesforce tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[A Salesforce tesztfelhasználó létrehozása](#creating-a-salesforce-test-user)**  - való Britta Simon egy megfelelője a Salesforce, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és a Salesforce alkalmazást az egyszeri bejelentkezés konfigurálása.

**Konfigurálása az Azure AD az egyszeri bejelentkezés Salesforce, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Salesforce** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_samlbase.png)

3. Az a **Salesforce-tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_url.png)

    Az a **bejelentkezési URL-cím** szövegmező, írja be az értéket a következő minta használatával: 
   * Vállalati fiók:`https://<subdomain>.my.salesforce.com`
   * Fejlesztői fiók:`https://<subdomain>-dev-ed.my.salesforce.com`

    > [!NOTE] 
    > Ezek az értékek nincsenek tényleges. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-címet. Ügyfél [Salesforce ügyfél-támogatási csoport](https://help.salesforce.com/support) beolvasni ezeket az értékeket. 
 
4. Az a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_certificate.png) 

5. Kattintson a **mentése** gomb ![konfigurálása egyszeri bejelentkezéshez](./media/active-directory-saas-salesforce-tutorial/tutorial_general_400.png)

6. A a **Salesforce konfigurációs** kattintson **konfigurálása Salesforce** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító és SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.** 

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_configure.png) 
<CS>
7.  Új lap megnyitása a böngésző és a Salesforce-rendszergazdai fiókjával való bejelentkezéshez.

8.  Az a **rendszergazda** navigációs ablaktábláján kattintson **biztonsági vezérlők** a kapcsolódó szakasz kibontásához. Kattintson a **egyszeri bejelentkezési beállítások**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-salesforce-tutorial/sf-admin-sso.png)

9.  Az a **egyszeri bejelentkezési beállítások** lapján kattintson a **szerkesztése** gombra.
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-edit.png)

      > [!NOTE]
      > Ha nem lehet engedélyezni az egyszeri bejelentkezés beállítása a Salesforce-fiókhoz, esetleg forduljon [Salesforce ügyfél-támogatási csoport](https://help.salesforce.com/support). 

10. Válassza ki **SAML engedélyezett**, és kattintson a **mentése**.

      ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-salesforce-tutorial/sf-enable-saml.png)
11. A SAML-alapú egyszeri bejelentkezés beállítások konfigurálásához kattintson **új**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-new.png)

12. Az a **SAML-alapú egyszeri bejelentkezési beállítás szerkesztése** lapon, a következő konfigurációkat:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-salesforce-tutorial/sf-saml-config.png)

    a. Az a **neve** mezőben adjon meg egy felhasználóbarát nevet ehhez a konfigurációhoz. Értéket biztosító **neve** automatikusan feltölti a **API-név** szövegmező.

    b. Beillesztés **SAML Entitásazonosító** be értéket a **kibocsátó** mezőjét a Salesforce-ban.

    c. Az a **entitásazonosító szövegmező**, írja be a Salesforce-tartománynév, a következő minta használatával:
      
      * Vállalati fiók:`https://<subdomain>.my.salesforce.com`
      * Fejlesztői fiók:`https://<subdomain>-dev-ed.my.salesforce.com`
      
    d. Kattintson **Tallózás** vagy **Choose File** megnyitásához a **válassza ki a feltöltendő fájlt** párbeszédpanelen válassza ki a Salesforce-tanúsítványt, és kattintson **nyissa meg a**töltse fel a tanúsítványt.

    e. A **SAML identitástípus**, jelölje be **helyességi feltételt tartalmaz salesforce.com felhasználónév**.

    f. A **SAML-alapú identitás hely**, jelölje be **a tulajdonos utasítás NameIdentifier elemében van identitás**

    g. Beillesztés **egyszeri bejelentkezési URL-címe** azokat a **Identity Provider bejelentkezési URL-cím** mezőjét a Salesforce-ban.
    
    h. A **szolgáltató által kezdeményezett kérelem Szolgáltatáskötés**, jelölje be **HTTP-átirányítás**.
    
    i. Végezetül kattintson **mentése** az SAML-alapú egyszeri bejelentkezés beállítások alkalmazásához.

13. A Salesforce bal oldali navigációs ablaktábláján kattintson **tartományok** bontsa ki a kapcsolódó csomópontot, majd **saját tartomány**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-salesforce-tutorial/sf-my-domain.png)

14. Görgessen le a **hitelesítési** szakaszt, és kattintson a **szerkesztése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-salesforce-tutorial/sf-edit-auth-config.png)

15. Az a **hitelesítési szolgáltatás** szakaszt, válassza ki a SAML SSO konfigurációs rövid nevét, és kattintson a **mentése**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-salesforce-tutorial/sf-auth-config.png)

    > [!NOTE]
    > Ha egynél több hitelesítési szolgáltatás be van jelölve, a rendszer kéri a felhasználóktól mely hitelesítési szolgáltatás, például a bejelentkezéshez egyszeri bejelentkezést a Salesforce-környezet elindítása során használandó kiválasztásához. Ha nem szeretné, hogy megtörténjen-e, majd meg kell **hagyja bejelölve minden hitelesítési szolgáltatás**.
<CE>    
> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. A a bal oldali navigációs panelen a **Azure-portálon**, kattintson a **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-salesforce-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-salesforce-tutorial/create_aaduser_02.png) 

3. Kattintson a párbeszédpanel tetején **Hozzáadás** megnyitásához a **felhasználói** párbeszédpanel.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-salesforce-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-salesforce-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-salesforce-test-user"></a>A Salesforce tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó létrehozása a Salesforce-ban. Salesforce támogatja közvetlenül az időponthoz kötött kiosztást, amely alapértelmezés szerint engedélyezve van.
Nincs ebben a szakaszban az Ön művelet elem. Ha a felhasználó nem létezik a Salesforce-ban, egy új létrejön a Salesforce elérésére tett kísérlet során.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Salesforce Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése Salesforce, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Salesforce**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Az egyszeri bejelentkezés beállításainak ellenőrzéséhez nyissa meg a hozzáférési panelre a [https://myapps.microsoft.com](https://myapps.microsoft.com/), majd jelentkezzen be a fiókot, és kattintson a **Salesforce**.

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)
* [A felhasználók átadása konfigurálása](active-directory-saas-salesforce-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_203.png


---
title: 'Oktatóanyag: Azure Active Directoryval integrált Salesforce |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a Salesforce között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: d2d7d420-dc91-41b8-a6b3-59579e043b35
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: e5b9fb0e1696e83ca4dfc8936613631f4c84ca47
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34590782"
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce"></a>Oktatóanyag: Azure Active Directoryval integrált Salesforce

Ebben az oktatóanyagban elsajátíthatja Salesforce integrálása az Azure Active Directory (Azure AD).

Salesforce integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér a Salesforce szabályozhatja.
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni aláírt a Salesforce (egyszeri bejelentkezés) a saját Azure AD-fiókok.
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Salesforce, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- A Salesforce egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Salesforce hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-salesforce-from-the-gallery"></a>Salesforce hozzáadása a gyűjteményből
Az Azure AD integrálása a Salesforce konfigurálásához kell hozzáadnia Salesforce a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**Vegye fel a Salesforce a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **Salesforce**, jelölje be **Salesforce** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában Salesforce](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD az egyszeri bejelentkezés Salesforce "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó a Salesforce-ban a felhasználók az Azure ad-ben. Ez azt jelenti egy Azure AD-felhasználó és a kapcsolódó felhasználó a Salesforce-ban közötti kapcsolat kapcsolatot kell létrehozni.

A Salesforce, rendelje az értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a Salesforce tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Salesforce tesztfelhasználó létrehozása](#create-a-salesforce-test-user)**  - való Britta Simon egy megfelelője a Salesforce, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és a Salesforce alkalmazást az egyszeri bejelentkezés konfigurálása.

**Konfigurálása az Azure AD az egyszeri bejelentkezés Salesforce, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Salesforce** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_samlbase.png)

3. Az a **Salesforce-tartomány és az URL-címek** területen tegye a következőket:

    ![Salesforce-tartomány és az URL-címeket az egyszeri bejelentkezés információk](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_url.png)
    
    a. Az a **bejelentkezési URL-cím** szövegmező, írja be az értéket a következő minta használatával:
    
    Vállalati fiók: `https://<subdomain>.my.salesforce.com`

    Fejlesztői fiók: `https://<subdomain>-dev-ed.my.salesforce.com`
    
    b. Az a **azonosító** szövegmező, írja be az értéket a következő minta használatával:
    
    Vállalati fiók: `https://<subdomain>.my.salesforce.com`

    Fejlesztői fiók: `https://<subdomain>-dev-ed.my.salesforce.com`
    
    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Ügyfél [Salesforce ügyfél-támogatási csoport](https://help.salesforce.com/support) beolvasni ezeket az értékeket.

4. Az a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-salesforce-tutorial/tutorial_general_400.png)

6. A a **Salesforce konfigurációs** kattintson **konfigurálása Salesforce** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító és SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Salesforce-konfiguráció](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_configure.png) 

7. Új lap megnyitása a böngésző és a Salesforce-rendszergazdai fiókjával való bejelentkezéshez.

8. Kattintson a **telepítő** alatt **beállítások ikonra** az oldal jobb felső sarkában a.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-salesforce-tutorial/configure1.png)

9. Görgessen le a **beállítások** navigációs ablaktábláján kattintson **identitás** a kapcsolódó szakasz kibontásához. Kattintson a **egyszeri bejelentkezési beállítások**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-salesforce-tutorial/sf-admin-sso.png)

10. Az a **egyszeri bejelentkezési beállítások** lapján kattintson a **szerkesztése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-edit.png)
    
    > [!NOTE]
    > Ha nem lehet engedélyezni az egyszeri bejelentkezés beállítása a Salesforce-fiókhoz, esetleg forduljon [Salesforce ügyfél-támogatási csoport](https://help.salesforce.com/support). 

11. Válassza ki **SAML engedélyezett**, és kattintson a **mentése**.

      ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-salesforce-tutorial/sf-enable-saml.png)
12. A SAML-alapú egyszeri bejelentkezés beállítások konfigurálásához kattintson **új**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-new.png)

13. Az a **SAML-alapú egyszeri bejelentkezési beállítás szerkesztése** lapon, a következő konfigurációkat:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-salesforce-tutorial/sf-saml-config.png)

    a. Az a **neve** mezőben adjon meg egy felhasználóbarát nevet ehhez a konfigurációhoz. Értéket biztosító **neve** automatikusan feltölti a **API-név** szövegmező.

    b. Az a **kibocsátó** mezőbe illessze be az értékét **SAML Entitásazonosító**, amely az Azure-portálon másolta.

    c. Az a **entitásazonosító szövegmező**, írja be a Salesforce-tartománynév, a következő minta használatával:
      
      * Vállalati fiók: `https://<subdomain>.my.salesforce.com`
      * Fejlesztői fiók: `https://<subdomain>-dev-ed.my.salesforce.com`
      
    d. Töltse fel a **szolgáltató Identitástanúsítvány**, kattintson a **Choose File** keresse meg és jelölje ki a tanúsítványfájlt, amely az Azure-portálról letöltött.

    e. Mint **SAML identitástípus**, a következő lehetőségek közül választhat:
    
      * Válassza ki **helyességi feltételt tartalmaz, a felhasználónév Salesforce**, ha a SAML-előfeltétel szolgáltatóé, a Salesforce felhasználónév

      * Válassza ki **helyességi feltételt tartalmaz az összevonási azonosító felhasználó**, ha a felhasználó objektum összevonási azonosító van beadott SAML-előfeltétel

      * Válassza ki **helyességi feltételt tartalmaz a használja a User objektum azonosítója**, ha a SAML-előfeltétel szolgáltatóé, a felhasználó a felhasználói azonosító

    f. A **SAML-alapú identitás hely**, jelölje be **identitás a tulajdonos utasítás NameIdentifier elemében van**.

    g. A **szolgáltató által kezdeményezett kérelem Szolgáltatáskötés**, jelölje be **HTTP-átirányítás**.

    h. A **Identity Provider bejelentkezési URL-cím** szövegmezőhöz illessze be az értékét **egyszeri bejelentkezési URL-címe**, amely az Azure-portálon másolta
    
    i. Végezetül kattintson **mentése** az SAML-alapú egyszeri bejelentkezés beállítások alkalmazásához.

14. A Salesforce bal oldali navigációs ablaktábláján kattintson **vállalati beállítások** bontsa ki a kapcsolódó csomópontot, majd **saját tartomány**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-salesforce-tutorial/sf-my-domain.png)

15. Görgessen le a **hitelesítési** szakaszt, és kattintson a **szerkesztése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-salesforce-tutorial/sf-edit-auth-config.png)

16. Az a **hitelesítési** szakaszt, ellenőrizze a **bejelentkezési oldal** , **hitelesítési szolgálata van** a SAML SSO konfigurációs, és kattintson a  **Mentés**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-salesforce-tutorial/sf-auth-config.png)

    > [!NOTE]
    > Ha egynél több hitelesítési szolgáltatás be van jelölve, a rendszer kéri a felhasználóktól mely hitelesítési szolgáltatás, például a bejelentkezéshez egyszeri bejelentkezést a Salesforce-környezet elindítása során használandó kiválasztásához. Ha nem szeretné, hogy megtörténjen-e, majd meg kell **hagyja bejelölve minden hitelesítési szolgáltatás**.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-salesforce-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-salesforce-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/active-directory-saas-salesforce-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-salesforce-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="create-a-salesforce-test-user"></a>Salesforce tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó létrehozása a Salesforce-ban. Salesforce támogatja közvetlenül az időponthoz kötött kiosztást, amely alapértelmezés szerint engedélyezve van. Nincs ebben a szakaszban az Ön művelet elem. Ha a felhasználó nem létezik a Salesforce-ban, egy új létrejön a Salesforce elérésére tett kísérlet során. Salesforce is támogatja az automatikus a felhasználók átadása, további részletei [Itt](active-directory-saas-salesforce-provisioning-tutorial.md) automatikus felhasználólétesítés konfigurálásához.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Salesforce Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200]

**Britta Simon hozzárendelése Salesforce, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201]

2. Az alkalmazások listában válassza ki a **Salesforce**.

    ![Az alkalmazások listáját a Salesforce-hivatkozás](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_app.png)

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen a Salesforce csempére kattint, akkor kell beolvasni automatikusan bejelentkezett a Salesforce alkalmazáshoz.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)
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

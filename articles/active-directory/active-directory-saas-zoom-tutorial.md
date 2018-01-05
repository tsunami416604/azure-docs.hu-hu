---
title: "Oktatóanyag: Azure Active Directoryval integrált nagyítás |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a Nagyítás között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0ebdab6c-83a8-4737-a86a-974f37269c31
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/28/2017
ms.author: jeedes
ms.openlocfilehash: 5a6d9ea9de1035bf9c84cf3c451cc1121f04a82a
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2018
---
# <a name="tutorial-azure-active-directory-integration-with-zoom"></a>Oktatóanyag: Azure Active Directoryval integrált nagyítás

Ebben az oktatóanyagban elsajátíthatja a Nagyítás integrálása az Azure Active Directory (Azure AD).

A Nagyítás integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér a Nagyítás szabályozhatja.
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett (egyszeri bejelentkezés) modul nagyításhoz a saját Azure AD-fiókok.
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a nagyítás, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- A Nagyítás egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A Nagyítás hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-zoom-from-the-gallery"></a>A Nagyítás hozzáadása a gyűjteményből
A Nagyítás a Azure AD-integráció konfigurálásához kell hozzáadnia a Nagyítás a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A Nagyítás hozzáadása a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **nagyítás**, jelölje be **nagyítás** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában a nagyításhoz](./media/active-directory-saas-zoom-tutorial/tutorial_zoom_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezést a nagyítás "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Nagyítás a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Nagyítás közötti kapcsolat kapcsolatot kell létrehozni.

A nagyítás, rendelje az értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a Nagyítás tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[A Nagyítás tesztfelhasználó létrehozása](#create-a-zoom-test-user)**  - való Britta Simon egy megfelelője a nagyítás, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és a Nagyítás alkalmazásban egyszeri bejelentkezés konfigurálása.

**A Nagyítás konfigurálása az Azure AD egyszeri bejelentkezést, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **nagyítás** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-zoom-tutorial/tutorial_zoom_samlbase.png)

3. Az a **nagyítás tartomány és az URL-címek** területen tegye a következőket:

    ![Nagyítás tartomány és az URL-címeket az egyszeri bejelentkezés információk](./media/active-directory-saas-zoom-tutorial/tutorial_zoom_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe:`https://<companyname>.zoom.us`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe:`<companyname>.zoom.us`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Ügyfél [nagyítás ügyfél-támogatási csoport](https://support.zoom.us/hc) beolvasni ezeket az értékeket.

4. A Nagyítás alkalmazás a SAML helyességi feltételek egy meghatározott formátumban, amelyek megkövetelik olyan egyéni attribútum-leképezésekhez hozzáadása a SAML-jogkivonat attribútumok konfigurációs vár. A következő jogcímek alkalmazás konfigurálása. Ezek az attribútumok értékének kezelheti a "**felhasználói attribútumok**" szakasz alkalmazás integráció lapján. 

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-Zoom-tutorial/tutorial_attribute.png)

5. A a **felhasználói attribútumok** a szakasz a **egyszeri bejelentkezés** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, az előző ábrán látható módon, és hajtsa végre a következő lépéseket:
    
    | Attribútum neve | Attribútum értéke | Namespace érték |
    | ------------------- | -----------|--------- |    
    | E-mail-cím | User.mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/mail`|
    | Utónév | User.givenName | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`|
    | Vezetéknév | User.surname | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname `|
    | Telefonszám | User.telephoneNumber | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/phone`|
    | Részleg | felhasználó.részleg | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/department`|

    a. Kattintson a **Hozzáadás attribútum** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-Zoom-tutorial/tutorial_attribute_04.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-Zoom-tutorial/tutorial_attribute_05.png)

    b. Az a **neve** szövegmező, írja be az adott sorhoz feltüntetett attribútumot nevét.

    c. Az a **érték** kilistázásához írja be a sorhoz látható attribútum értéke.

    d. Az a **Namespace** szövegmező, írja be a névtér értéke az adott sorhoz feltüntetett.
    
    e. Kattintson az **OK** gombra. 
 
6. A a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-zoom-tutorial/tutorial_zoom_certificate.png)

7. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-zoom-tutorial/tutorial_general_400.png)

8. A a **nagyítás konfigurációs** kattintson **konfigurálása nagyítás** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-címet, a SAML entitás azonosítója és a SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![A Nagyítás konfiguráció](./media/active-directory-saas-zoom-tutorial/tutorial_zoom_configure.png)

9. Egy másik webes böngészőablakban jelentkezzen be a nagyítási vállalati webhely rendszergazdaként.

10. Kattintson a **egyszeri bejelentkezés** fülre.
   
    ![Egyszeri bejelentkezés lapon](./media/active-directory-saas-zoom-tutorial/IC784700.png "egyszeri bejelentkezés")

11. Kattintson a **biztonsági ellenőrzést** lapot, és keresse meg a **egyszeri bejelentkezés** beállítások.

12. Egyszeri bejelentkezés csoportjában hajtsa végre az alábbi lépéseket:
   
    ![Az egyszeri bejelentkezés szakasz](./media/active-directory-saas-zoom-tutorial/IC784701.png "egyszeri bejelentkezés")
   
    a. Az a **bejelentkezési URL-címe** szövegmezőhöz illessze be az értékét **SAML-alapú egyszeri bejelentkezési URL-címe** ami Azure-portálon másolta.
   
    b. Az a **kijelentkezési URL-címe** szövegmezőhöz illessze be az értékét **Sign-Out URL-cím** ami Azure-portálon másolta.
     
    c. Nyissa meg a base-64 kódolású tanúsítvány a Jegyzettömbben, a tartalmának másolása a vágólapra és illessze be azt a **szolgáltató identitástanúsítvány** szövegmező.

    d. Az a **kibocsátó** szövegmezőhöz illessze be az értékét **SAML Entitásazonosító** ami Azure-portálon másolta. 

    e. Kattintson a **Save** (Mentés) gombra.

    > [!NOTE] 
    > További információkért látogasson el a Nagyítás dokumentáció [https://zoomus.zendesk.com/hc/en-us/articles/115005887566](https://zoomus.zendesk.com/hc/en-us/articles/115005887566)

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-zoom-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-zoom-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/active-directory-saas-zoom-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-zoom-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-zoom-test-user"></a>A Nagyítás tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók jelentkezzen be a nagyítás, akkor ki kell építenie a nagyítás. Nagyítás, ha egy kézi tevékenység.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Felhasználói fiók létrehozásához hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be a **nagyítás** vállalati hely rendszergazdaként.
 
2. Kattintson a **fiókkezelés** fülre, majd **felhasználókezelés**.

3. Felhasználók kezelése területen kattintson **felhasználók hozzáadása az**.
   
    ![Felhasználókezelés](./media/active-directory-saas-zoom-tutorial/IC784703.png "felhasználók kezelése")

4. Az a **felhasználók hozzáadása az** lapon, a következő lépésekkel:
   
    ![Felhasználók hozzáadása az](./media/active-directory-saas-zoom-tutorial/IC784704.png "felhasználók hozzáadása")
   
    a. Mint **felhasználótípust**, jelölje be **alapvető**.

    b. A a **e-mailek** szövegmező, írja be az e-mail cím egy érvényes Azure ad meg fiók kiépítése szeretné.

    c. Kattintson a **Hozzáadás** parancsra.

> [!NOTE]
> Bármely más nagyítás felhasználói fiók létrehozása eszközök vagy nagyítás kiépítését Azure Active Directory által nyújtott API-k felhasználói fiókokat.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés nagyítás Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**A Nagyítás Britta Simon rendeléséhez hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **nagyítás**.

    ![A Nagyítás hivatkozásra az alkalmazások listáját](./media/active-directory-saas-zoom-tutorial/tutorial_zoom_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ez a szakasz célja tesztelése az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési Panel nagyítás mozaik gombra kattint, meg kell beolvasása automatikusan bejelentkezett a Nagyítás alkalmazásba.

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-zoom-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-zoom-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-zoom-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-zoom-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-zoom-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-zoom-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-zoom-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-zoom-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-zoom-tutorial/tutorial_general_203.png


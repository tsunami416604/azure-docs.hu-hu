---
title: "Oktatóanyag: Azure Active Directoryval integrált Druva |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Druva között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: ab92b600-1fea-4905-b1c7-ef8e4d8c495c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2017
ms.author: jeedes
ms.openlocfilehash: 5be9f384d9e8c0871d26ccb5d35a49e9387ad753
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-druva"></a>Oktatóanyag: Azure Active Directoryval integrált Druva

Ebben az oktatóanyagban elsajátíthatja Druva integrálása az Azure Active Directory (Azure AD).

Druva integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér Druva szabályozhatja.
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett Druva (egyszeri bejelentkezés) számára a saját Azure AD-fiókok.
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs Druva, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Druva egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Druva hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-druva-from-the-gallery"></a>A gyűjteményből Druva hozzáadása
Az Azure AD integrálása a Druva konfigurálásához kell hozzáadnia Druva a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Druva hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **Druva**, jelölje be **Druva** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában Druva](./media/active-directory-saas-druva-tutorial/tutorial_druva_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján Druva.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Druva a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Druva közötti kapcsolat kapcsolatot kell létrehozni.

Druva, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a Druva tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Druva tesztfelhasználó létrehozása](#create-a-druva-test-user)**  - való Britta Simon valami Druva, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Druva alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés Druva, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Druva** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-druva-tutorial/tutorial_druva_samlbase.png)

3. Az a **Druva tartomány és az URL-címek** szakaszban, ha szeretne beállítani az alkalmazás **IDP** kezdeményezett mód:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-druva-tutorial/tutorial_druva_url.png)

    Az a **azonosító** szövegmező, írja be a karakterlánc-érték:`druva-cloud`
    
4. Ellenőrizze **megjelenítése speciális URL-beállításainak**. Ha szeretne beállítani az alkalmazás **SP** kezdeményezett mód:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-druva-tutorial/tutorial_druva_url1.png)
    
    Az a **bejelentkezési URL-cím** szövegmező, írja be az URL-cím:`https://cloud.druva.com/home`

5. Az a **SAML-aláíró tanúsítványa** kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-druva-tutorial/tutorial_druva_certificate.png) 

6. A Druva alkalmazás vár a SAML helyességi feltételek egy meghatározott formátumban, amelyhez egyéni attribútum leképezései hozzáadása a **SAML-jogkivonat attribútumok** konfigurációs. 

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-druva-tutorial/tutorial_druva_attribute.png)

7. A a **felhasználói attribútumok** a szakasz a **egyszeri bejelentkezés** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, az előző ábrán látható módon, és hajtsa végre a következő lépéseket:

    | Attribútum neve      | Attribútum értéke      |
    | ------------------- | -------------------- |
    | insync\_auth\_token |Adja meg a token generált érték |
    
    a. Kattintson a **Hozzáadás attribútum** megnyitásához a **attribútum hozzáadása** párbeszédpanel.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-druva-tutorial/tutorial_attribute_04.png)
    
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-druva-tutorial/tutorial_attribute_05.png)
    
    b. Az a **neve** szövegmező, írja be az adott sorhoz feltüntetett attribútumot nevét.

    c. Az a **érték** kilistázásához írja be a sorhoz látható attribútum értéke. A token generált érték esetén, tekintse meg az oktatóanyag későbbi részében.
    
    d. Kattintson az **OK** gombra.    

8. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-druva-tutorial/tutorial_general_400.png)

9. A a **Druva konfigurációs** kattintson **konfigurálása Druva** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out és SAML-alapú egyszeri bejelentkezés szolgáltatás URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-druva-tutorial/tutorial_druva_configure.png) 

10. Egy másik webes böngészőablakban jelentkezzen be a Druva vállalati webhely rendszergazdaként.

11. Ugrás a **kezelése \> beállítások**.

    ![Beállítások](./media/active-directory-saas-druva-tutorial/ic795091.png "beállítások")

12. Az egyszeri bejelentkezés beállításai párbeszédpanel hajtsa végre a következő lépéseket:

    ![Az egyszeri bejelentkezés beállítások](./media/active-directory-saas-druva-tutorial/ic795092.png "az egyszeri bejelentkezés beállításai")
    
    a. A **azonosító szolgáltató bejelentkezési URL-cím** szövegmezőhöz illessze be az értékét **egyszeri bejelentkezési URL-címe**, amely az Azure-portálon másolta.
        
    b. A **azonosító szolgáltató kijelentkezési URL-cím** szövegmezőhöz illessze be az értékét **Sign-Out URL-cím**, amely az Azure-portálon másolta
        
    c. Nyissa meg a base-64 kódolású tanúsítvány a Jegyzettömbben, a tartalmának másolása a vágólapra és illessze be azt a **azonosító szolgáltató tanúsítvány** szövegmező
     
    d. Lehetőségre a **beállítások** kattintson **mentése**.

13. Az a **beállítások** kattintson **SSO jogkivonat készítése**.

    ![Beállítások](./media/active-directory-saas-druva-tutorial/ic795093.png "beállítások")

14. Az a **egyszeri bejelentkezés hitelesítési jogkivonat** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezési Token](./media/active-directory-saas-druva-tutorial/ic795094.png "SSO jogkivonat")
    
    a. Kattintson a **másolási**, illessze be az értéket másolta a **érték** textbox a **attribútum hozzáadása** szakaszban az Azure portálon.
    
    b. Kattintson a **Bezárás** gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-druva-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-druva-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/active-directory-saas-druva-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-druva-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-druva-test-user"></a>Druva tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Druva bejelentkezni, akkor ki kell építenie a Druva. Druva, ha egy kézi tevékenység.

**Adja meg a felhasználók átadása, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **Druva** vállalati hely rendszergazdaként.

2. Ugrás a **kezelése \> felhasználók**.
   
   ![Felhasználók kezelése](./media/active-directory-saas-druva-tutorial/ic795097.png "felhasználók kezelése")

3. Kattintson a **új**.
   
   ![Felhasználók kezelése](./media/active-directory-saas-druva-tutorial/ic795098.png "felhasználók kezelése")

4. Új felhasználó létrehozása párbeszédpanelen hajtsa végre a következő lépéseket:
   
   ![Hozzon létre Új_felhasználó](./media/active-directory-saas-druva-tutorial/ic795099.png "Új_felhasználó létrehozása")
   
   a. Az a **E-mail cím** szövegmező, adja meg az e-mail címét, például a felhasználó  **brittasimon@contoso.com** .
   
   b. Az a **neve** szövegmező, írja be például a felhasználó nevét **BrittaSimon**.
   
   c. Kattintson a **létrehozza a felhasználó**.

>[!NOTE]
>Bármely más Druva felhasználói fiók létrehozása eszközök vagy Druva kiépíteni az Azure AD-felhasználói fiókok által nyújtott API-k.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Druva Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése Druva, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Druva**.

    ![Az alkalmazások listáját a Druva hivatkozás](./media/active-directory-saas-druva-tutorial/tutorial_druva_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Druva csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az Druva alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-druva-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-druva-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-druva-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-druva-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-druva-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-druva-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-druva-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-druva-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-druva-tutorial/tutorial_general_203.png


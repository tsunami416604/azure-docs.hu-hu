---
title: "Oktatóanyag: Azure Active Directoryval integrált Egnyte |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Egnyte között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 8c2101d4-1779-4b36-8464-5c1ff780da18
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2017
ms.author: jeedes
ms.openlocfilehash: 62d01333b61e73c83588d2d1701c0c300df4ab1c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-egnyte"></a>Oktatóanyag: Azure Active Directoryval integrált Egnyte

Ebben az oktatóanyagban elsajátíthatja Egnyte integrálása az Azure Active Directory (Azure AD).

Egnyte integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a Egnyte hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett Egnyte (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs Egnyte, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Egnyte egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Egnyte hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-egnyte-from-the-gallery"></a>A gyűjteményből Egnyte hozzáadása
Az Azure AD integrálása a Egnyte konfigurálásához kell hozzáadnia Egnyte a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Egnyte hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **Egnyte**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-egnyte-tutorial/tutorial_egnyte_search.png)

5. Az eredmények panelen válassza ki a **Egnyte**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-egnyte-tutorial/tutorial_egnyte_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban konfigurálása, és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon." nevű tesztfelhasználó alapján Egnyte

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Egnyte a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Egnyte közötti kapcsolat kapcsolatot kell létrehozni.

Egnyte, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a Egnyte tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Egy Egnyte tesztfelhasználó létrehozása](#creating-an-egnyte-test-user)**  - való Britta Simon valami Egnyte, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Egnyte alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés Egnyte, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Egnyte** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-egnyte-tutorial/tutorial_egnyte_samlbase.png)

3. Az a **Egnyte tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-egnyte-tutorial/tutorial_egnyte_url.png)

    Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe:`https://<companyname>.egnyte.com`

    > [!NOTE] 
    > Ez az érték nincs valós. Frissítse ezt az értéket a tényleges bejelentkezési URL-címet. Ügyfél [Egnyte ügyfél-támogatási csoport](https://www.egnyte.com/corp/contact_egnyte.html) lekérni ezt az értéket. 
 
4. Az a **SAML-aláíró tanúsítványa** kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-egnyte-tutorial/tutorial_egnyte_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-egnyte-tutorial/tutorial_general_400.png)

6. A a **Egnyte konfigurációs** kattintson **konfigurálása Egnyte** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító és SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-egnyte-tutorial/tutorial_egnyte_configure.png) 

7. Egy másik webes böngészőablakban jelentkezzen be a Egnyte vállalati webhely rendszergazdaként.

8. Kattintson a **beállítások**.
   
   ![Beállítások](./media/active-directory-saas-egnyte-tutorial/ic787819.png "beállítások")

9. A menüben kattintson a **beállítások**.

   ![Beállítások](./media/active-directory-saas-egnyte-tutorial/ic787820.png "beállítások")

10. Kattintson a **konfigurációs** fülre, majd **biztonsági**.

    ![Biztonsági](./media/active-directory-saas-egnyte-tutorial/ic787821.png "biztonsági")

11. Az a **egyszeri bejelentkezés hitelesítési** területen tegye a következőket:

    ![Egyszeri bejelentkezés hitelesítési](./media/active-directory-saas-egnyte-tutorial/ic787822.png "egyszeri bejelentkezés a hitelesítés")   
    
    a. Mint **egyszeri bejelentkezés hitelesítési**, jelölje be **SAML 2.0**.
   
    b. Mint **identitásszolgáltató**, jelölje be **AzureAD**.
   
    c. Beillesztés **SAML-alapú egyszeri bejelentkezési URL-címe** másolja az Azure-portálon a **Identity provider bejelentkezési URL-cím** szövegmező.
   
    d. Beillesztés **SAML Entitásazonosító** , amely az Azure portálról másolta a **Identity provider Entitásazonosító** szövegmező.
      
    e. A base-64 kódolású tanúsítvány megnyitása a Jegyzettömbben az Azure portálról letöltött, a tartalmának másolása a vágólapra és illessze be azt a **szolgáltató identitástanúsítvány** szövegmező.
   
    f. Mint **alapértelmezett felhasználói hozzárendelésének**, jelölje be **E-mail cím**.
   
    g. Mint **tartományspecifikus kibocsátó érték**, jelölje be **le van tiltva**.
   
    h. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-egnyte-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-egnyte-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-egnyte-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-egnyte-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-an-egnyte-test-user"></a>Egy Egnyte tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Egnyte bejelentkezni, akkor ki kell építenie a Egnyte. Egnyte, ha egy kézi tevékenység.

**A felhasználói fiókok létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **Egnyte** vállalati hely rendszergazdaként.

2. Ugrás a **beállítások \> felhasználók és csoportok**.

3. Kattintson a **új felhasználó hozzáadása**, majd válassza ki a hozzáadni kívánt felhasználó típusát.
   
   ![Felhasználók](./media/active-directory-saas-egnyte-tutorial/ic787824.png "felhasználók")

4. Az a **új általános jogú felhasználó** területen tegye a következőket:
   
   ![Új általános jogú felhasználó](./media/active-directory-saas-egnyte-tutorial/ic787825.png "új általános jogú felhasználó")   

   a. Típus a **E-mail**, **felhasználónév**, és egyéb részleteket a egy érvényes Azure Active Directory-fióknevet, amelyet kiépítését.
   
   b. Kattintson a **Save** (Mentés) gombra.
    
    >[!NOTE]
    >Az Azure Active Directory fióktulajdonos e-mailben értesítést fog kapni.
    >

>[!NOTE]
>Bármely más Egnyte felhasználói fiók létrehozása eszközök vagy rendelkezés AAD felhasználói fiókokhoz Egnyte által nyújtott API-k.
> 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Egnyte Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése Egnyte, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Egnyte**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-egnyte-tutorial/tutorial_egnyte_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Egnyte csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az Egnyte alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_203.png


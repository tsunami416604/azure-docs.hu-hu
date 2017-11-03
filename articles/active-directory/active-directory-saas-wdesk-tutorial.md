---
title: "Oktatóanyag: Azure Active Directoryval integrált Wdesk |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Wdesk között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 06900a91-a326-4663-8ba6-69ae741a536e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: jeedes
ms.openlocfilehash: 37660b80cfb01d6a3105aea5ce248f1e03c46695
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-wdesk"></a>Oktatóanyag: Azure Active Directoryval integrált Wdesk

Ebben az oktatóanyagban elsajátíthatja Wdesk integrálása az Azure Active Directory (Azure AD).

Wdesk integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a Wdesk hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett Wdesk (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg. [Alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs Wdesk, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Wdesk egyszeri bejelentkezés engedélyezve van az előfizetésben

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Wdesk hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-wdesk-from-the-gallery"></a>A gyűjteményből Wdesk hozzáadása
Az Azure AD integrálása a Wdesk konfigurálásához kell hozzáadnia Wdesk a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Wdesk hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **Wdesk**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_search.png)

5. Az eredmények panelen válassza ki a **Wdesk**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban konfigurálása, és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon." nevű tesztfelhasználó alapján Wdesk

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Wdesk a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Wdesk közötti kapcsolat kapcsolatot kell létrehozni.

Ez a hivatkozás kapcsolat létesíti értéket rendeli az **felhasználónév** értékeként Azure AD-ben a **felhasználónév** Wdesk a.

Az Azure AD egyszeri bejelentkezést a Wdesk tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Wdesk tesztfelhasználó létrehozása](#creating-a-wdesk-test-user)**  - való Britta Simon valami Wdesk, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Wdesk alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés Wdesk, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Wdesk** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_samlbase.png)

3. Az a **Wdesk tartomány és az URL-címek** szakaszban, ha szeretne beállítani az alkalmazás **IDP** kezdeményezett módban hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_url.png)

    a. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe:`https://<subdomain>.wdesk.com/auth/saml/sp/metadata/<instancename>`

    b. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe:`https://<subdomain>.wdesk.com/auth/saml/sp/consumer/<instancename>`

4. Ellenőrizze **megjelenítése speciális URL-beállításainak**. Ha szeretne beállítani az alkalmazás **SP** kezdeményezett mód, hajtsa végre a következő lépést:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_url1.png)

    Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe:`https://<subdomain>.wdesk.com/auth/login/saml/<instancename>`
     
    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges azonosítója, válasz URL-CÍMEN és bejelentkezési URL-cím. Ezek az értékek első WDesk portálról az SSO konfigurálásakor. 
  
5. Az a **SAML-aláíró tanúsítványa** területen kattintson **metaadatainak XML-kódja** és mentse a metaadat-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_certificate.png) 

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-wdesk-tutorial/tutorial_general_400.png)
    
7. Egy másik webes böngészőablakban, jelentkezzen be a biztonsági-rendszergazdájaként Wdesk.

8. Kattintson a bal alsó **Admin** válassza **Fiókadminisztrátor**:
 
     ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

9. Wdesk rendszergazda, lépjen a **biztonsági**, majd **SAML** > **SAML beállítások**:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig2.png)

10. A **általános beállítások**, ellenőrizze a **SAML-alapú egyszeri bejelentkezés engedélyezése**:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig3.png)

11. A **szolgáltató részletei**, hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig4.png)

      a. Másolás a **bejelentkezési URL-cím** és illessze be **bejelentkezési URL-cím** szövegmező Azure-portál.
   
      b. Másolás a **metaadatainak URL-címét** és illessze be **azonosító** szövegmező Azure-portál.
       
      c. Másolás a **ügyfél URL-címe** és illessze be **válasz URL-címen** szövegmező Azure-portál.
   
      d. Kattintson a **mentése** menti a módosításokat az Azure portálon.      

12. Kattintson a **IdP beállításainak konfigurálása** megnyitásához **IdP beállításainak szerkesztése** párbeszédpanel. Kattintson a **Choose File** kereséséhez a **Metadata.xml** mentett fájlt, Azure-portálon, majd töltse fel.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig5.png)
  
13. Kattintson a **módosítások mentése**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfigsavebutton.png)

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-wdesk-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-wdesk-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-wdesk-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-wdesk-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-wdesk-test-user"></a>Wdesk tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Wdesk bejelentkezni, akkor ki kell építenie a Wdesk. A Wdesk egy kézi tevékenység.

**Felhasználói fiók létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a biztonsági-rendszergazdájaként Wdesk.
2. Navigáljon a **Admin** > **rendszergazdai fiók**.

     ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. Kattintson a **tagok** alatt **személyek**.

4. Most kattintson **tag hozzáadása** megnyitásához **tag hozzáadása** párbeszédpanel megnyitásához. 
   
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-wdesk-tutorial/createuser1.png)  

5. A **felhasználói** szöveg mezőbe írja be például a felhasználó felhasználóneve  **brittasimon@contoso.com**  kattintson **Folytatás** gombra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-wdesk-tutorial/createuser3.png)

6.  Adja meg az alább látható módon:
  
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-wdesk-tutorial/createuser4.png)
 
    a. A **E-mail** szöveg mezőbe írja be például a felhasználó e-mail  **brittasimon@contoso.com** .

    b. A **Utónév** szöveg mezőbe írja be például a felhasználó utónevét **Britta**.

    c. A **Vezetéknév** szöveg mezőbe írja be például a felhasználó vezetékneve **Simon**.

7. Kattintson a **mentése tag** gombra.  

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-wdesk-tutorial/createuser5.png)

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Wdesk Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése Wdesk, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Wdesk**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Wdesk csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az Wdesk alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md).


## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_203.png


---
title: "Oktatóanyag: Azure Active Directoryval integrált AirWatch |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és AirWatch között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 96a3bb1c-96c6-40dc-8ea0-060b0c2a62e5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: 1996ec97e7c0d94c5606ca43bb5956548f1f3712
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="tutorial-azure-active-directory-integration-with-airwatch"></a>Oktatóanyag: Azure Active Directoryval integrált AirWatch

Ebben az oktatóanyagban elsajátíthatja AirWatch integrálása az Azure Active Directory (Azure AD).

AirWatch integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a AirWatch hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett AirWatch (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs AirWatch, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy AirWatch egyszeri bejelentkezés engedélyezve van az előfizetésben

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből AirWatch hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-airwatch-from-the-gallery"></a>A gyűjteményből AirWatch hozzáadása
Az Azure AD integrálása a AirWatch konfigurálásához kell hozzáadnia AirWatch a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből AirWatch hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **AirWatch**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_search.png)

5. Az eredmények panelen válassza ki a **AirWatch**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban konfigurálása, és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon." nevű tesztfelhasználó alapján AirWatch

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó AirWatch a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a AirWatch közötti kapcsolat kapcsolatot kell létrehozni.

Ez a hivatkozás kapcsolat létesíti értéket rendeli az **felhasználónév** értékeként Azure AD-ben a **felhasználónév** AirWatch a.

Az Azure AD egyszeri bejelentkezést a AirWatch tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[AirWatch tesztfelhasználó létrehozása](#creating-a-airwatch-test-user)**  - való Britta Simon valami AirWatch, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az AirWatch alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés AirWatch, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **AirWatch** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_samlbase.png)

3. Az a **AirWatch tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe:`https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode`

    b. Az a **azonosító** szövegmező, írja be az értéket, mint`AirWatch`

    > [!NOTE] 
    > Ez az érték nem a valós. Frissítse ezt az értéket a tényleges bejelentkezési URL-címet. Ügyfél [AirWatch ügyfél-támogatási csoport](http://www.air-watch.com/company/contact-us/) lekérni ezt az értéket. 
 
4. Az a **SAML-aláíró tanúsítványa** kattintson **metaadatainak XML-kódja** , és mentse az XML-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_certificate.png) 

5. A a **AirWatch konfigurációs** kattintson **konfigurálása AirWatch** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_configure.png) 

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-airwatch-tutorial/tutorial_general_400.png)
<CS>
7. Egy másik webes böngészőablakban jelentkezzen be a AirWatch vállalati webhely rendszergazdaként.

8. A bal oldali navigációs ablaktáblán kattintson **fiókok**, és kattintson a **rendszergazdák**.
   
   ![A rendszergazdák](./media/active-directory-saas-airwatch-tutorial/ic791920.png "rendszergazdák")

9. Bontsa ki a **beállítások** menüben, majd kattintson **címtárszolgáltatások**.
   
   ![Beállítások](./media/active-directory-saas-airwatch-tutorial/ic791921.png "beállítások")

10. Kattintson a **felhasználói** lap a **Base DN** szövegmező, írja be a tartomány nevét, és kattintson **mentése**.
   
   ![Felhasználói](./media/active-directory-saas-airwatch-tutorial/ic791922.png "felhasználó")

11. Kattintson a **Server** fülre.
   
   ![Kiszolgáló](./media/active-directory-saas-airwatch-tutorial/ic791923.png "kiszolgáló")

12. Hajtsa végre a következő lépéseket:
    
    ![Töltse fel](./media/active-directory-saas-airwatch-tutorial/ic791924.png "feltöltése")   
    
    a. Mint **Directory típusa**, jelölje be **nincs**.

    b. Válassza ki **SAML-alapú hitelesítéshez használandó**.

    c. A letöltött tanúsítvány feltöltése, kattintson a **feltöltése**.

13. Az a **kérelem** területen tegye a következőket:
    
    ![Kérelem](./media/active-directory-saas-airwatch-tutorial/ic791925.png "kérése")  

    a. Mint **kötési típus kérése**, jelölje be **POST**.

    b. Az Azure portálon a a **konfigurálhatja az egyszeri bejelentkezés Airwatch** párbeszédpanel lap, a Másolás a **SAML-alapú egyszeri bejelentkezési URL-címe** értékét, és illessze be azt a **Identity Provider egyszeri bejelentkezés URL-cím** szövegmező.

    c. Mint **NameID formátum**, jelölje be **E-mail cím**.

    d. Kattintson a **Save** (Mentés) gombra.

14. Kattintson a **felhasználói** újra fülre.
    
    ![Felhasználói](./media/active-directory-saas-airwatch-tutorial/ic791926.png "felhasználó")

15. Az a **attribútum** területen tegye a következőket:
    
    ![Attribútum](./media/active-directory-saas-airwatch-tutorial/ic791927.png "attribútum")

    a. Az a **objektumazonosító** szövegmezőhöz típus **http://schemas.microsoft.com/identity/claims/objectidentifier**.

    b. Az a **felhasználónév** szövegmezőhöz típus **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    c. Az a **megjelenített név** szövegmezőhöz típus **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.

    d. Az a **Utónév** szövegmezőhöz típus **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.

    e. Az a **Vezetéknév** szövegmezőhöz típus **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.

    f. Az a **E-mail** szövegmezőhöz típus **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    g. Kattintson a **Save** (Mentés) gombra.

<CE>

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-airwatch-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-airwatch-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-airwatch-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-airwatch-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a Britta Simon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-airwatch-test-user"></a>AirWatch tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók AirWatch bejelentkezni, akkor ki kell építenie a AirWatch.

* AirWatch, ha a kézi tevékenység kiépítés.

**Felhasználói fiók létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **AirWatch** vállalati hely rendszergazdaként.
2. A bal oldali navigációs ablaktábláján kattintson **fiókok**, és kattintson a **felhasználók**.
   
   ![Felhasználók](./media/active-directory-saas-airwatch-tutorial/ic791929.png "felhasználók")
3. Az a **felhasználók** menüben kattintson a **listanézet**, és kattintson a **Hozzáadás \> felhasználó hozzáadása**.
   
   ![Felhasználó hozzáadása](./media/active-directory-saas-airwatch-tutorial/ic791930.png "felhasználó hozzáadása")
4. Az a **hozzáadása / szerkesztése felhasználói** párbeszédpanelen hajtsa végre a következő lépéseket:

   ![Felhasználó hozzáadása](./media/active-directory-saas-airwatch-tutorial/ic791931.png "felhasználó hozzáadása")   
   1. Típus a **felhasználónév**, **jelszó**, **jelszó megerősítése**, **Utónév**, **keresztneve**,  **E-mail cím** egy érvényes Azure Active Directory-fiók szeretné azokat a kapcsolódó szövegmezők kiépítéséhez.
   2. Kattintson a **Save** (Mentés) gombra.

>[!NOTE]
>Bármely más AirWatch felhasználói fiók létrehozása eszközök vagy rendelkezés AAD felhasználói fiókokhoz AirWatch által nyújtott API-k.
>  

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés AirWatch Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése AirWatch, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **AirWatch**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha azt szeretné, az egyszeri bejelentkezés beállításainak ellenőrzéséhez nyissa meg a hozzáférési Panel. A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md).


## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_203.png


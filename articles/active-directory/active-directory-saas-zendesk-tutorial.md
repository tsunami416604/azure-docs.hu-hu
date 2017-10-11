---
title: "Oktatóanyag: Azure Active Directoryval integrált Zendesk |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a Zendesk között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 9d7c91e5-78f5-4016-862f-0f3242b00680
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.openlocfilehash: 51c06d838c5ed6286dfb99ea25faaaf33bad5f3c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-zendesk"></a>Oktatóanyag: Azure Active Directoryval integrált Zendesk

Ebben az oktatóanyagban elsajátíthatja Zendesk integrálása az Azure Active Directory (Azure AD).

Zendesk integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Szabályozhatja, aki hozzáférhet ehhez az Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni aláírt a Zendesk (egyszeri bejelentkezés) a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Ehhez az Azure AD-integrációs konfigurálni, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- A Zendesk egyszeri bejelentkezés engedélyezve van az előfizetés


> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.


Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Zendesk hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés


## <a name="adding-zendesk-from-the-gallery"></a>Zendesk hozzáadása a gyűjteményből
Az Azure AD integrálása a Zendesk konfigurálásához kell hozzáadnia ehhez a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá ehhez a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure Portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Kattintson a **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **Zendesk**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_search.png)

5. Az eredmények panelen válassza ki a **Zendesk**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján Zendesk.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a Zendesk tartozó felhasználót a felhasználó Azure AD-ben. Ez azt jelenti egy Azure AD-felhasználó és a kapcsolódó felhasználó a Zendesk közötti kapcsolat kapcsolatot kell létrehozni.

Ez a hivatkozás kapcsolat létesíti értéket rendeli az **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a Zendesk.

Az Azure AD egyszeri bejelentkezést a Zendesk tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Zendesk tesztfelhasználó létrehozása](#creating-a-zendesk-test-user)**  - való Britta Simon valami Zendesk, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Zendesk-alkalmazásban.

**A Zendesk konfigurálása az Azure AD egyszeri bejelentkezést, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Zendesk** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_samlbase.png)

3. Az a **Zendesk-tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, írja be az értéket a következő minta használatával:`https://<subdomain>.zendesk.com`

    b. Az a **azonosító** szövegmező, írja be az értéket a következő minta használatával:`https://<subdomain>.zendesk.com`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosító URL-t. Ügyfél [Zendesk támogatási csoport](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise) beolvasni ezeket az értékeket. 

4. Ehhez a SAML helyességi feltételek vár egy meghatározott formátumban. Nem kötelező SAML attribútum, de opcionálisan hozzáadhat egy attribútumot **felhasználói attribútumok** következő szakasz az alábbi lépéseket: 

     ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_attributes1.png)

    a. Kattintson a **megtekintése és szerkesztése az összes többi attribútumával** jelölőnégyzetet.
     
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_attributes2.png)
   
    b. Kattintson a **attribútum hozzáadása** megnyitásához **Hozzáadás attribútum** párbeszédpanel.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-zendesk-tutorial/tutorial_attribute_05.png)

    c. Az a **neve** szövegmező, írja be az attribútumnak a nevét (például **emailaddress**).
    
    d. Az a **érték** listára, válassza ki az attribútum értéke (mint **user.mail**).
    
    e. Kattintson a **Ok**
 
    > [!NOTE] 
    > A bővítményattribútumokat használatával ad hozzá az attribútumokat, amelyek nem az alapértelmezés szerint az Azure AD-ben. Kattintson a [állítható be SAML felhasználói attribútumok](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-) teljes listáját a beolvasandó SAML attribútumok, amelyek **Zendesk** fogad el. 

5. Az a **SAML-aláíró tanúsítványa** szakaszban, másolja a **UJJLENYOMAT** tanúsítvány értékét.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_certificate.png) 

6. Az a **Zendesk konfigurációs** területén kattintson **konfigurálása Zendesk** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out és SAML-alapú egyszeri bejelentkezés szolgáltatás URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_configure.png) 

7. Egy másik webes böngészőablakban jelentkezzen be a Zendesk vállalati webhely rendszergazdaként.

8. Kattintson a **Admin**.

9. A bal oldali navigációs ablaktáblán kattintson **beállítások**, és kattintson a **biztonsági**.

10. Az a **biztonsági** lapon, a következő lépésekkel: 
   
     ![Biztonsági](./media/active-directory-saas-zendesk-tutorial/ic773089.png "biztonsági")

    ![Egyszeri bejelentkezés](./media/active-directory-saas-zendesk-tutorial/ic773090.png "egyszeri bejelentkezés")

     a. Kattintson a **Admin & ügynökök** fülre.

     b. Válassza ki **egyszeri bejelentkezés (SSO) és a SAML**, majd válassza ki **SAML**.

     c. A **SAML SSO URL-cím** szövegmezőhöz illessze be az értékét **SAML-alapú egyszeri bejelentkezési URL-címe** ami Azure-portálon másolta. 

     d. A **távoli kijelentkezési URL-cím** szövegmezőhöz illessze be az értékét **Sign-Out URL-cím** ami Azure-portálon másolta.
        
     e. A **tanúsítvány-ujjlenyomat** szövegmező, illessze be a **ujjlenyomat** érték tanúsítvány, amely az Azure-portálon másolta.
     
     f. Kattintson a **Save** (Mentés) gombra.

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-zendesk-tutorial/create_aaduser_01.png) 

2. Megjelenítendő felhasználót tartalmazó listát Ugrás **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-zendesk-tutorial/create_aaduser_02.png) 

3. Kattintson a párbeszédpanel tetején **Hozzáadás** megnyitásához a **felhasználói** párbeszédpanel.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-zendesk-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-zendesk-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra. 

### <a name="creating-a-zendesk-test-user"></a>Zendesk tesztfelhasználó létrehozása

Ahhoz, hogy be tudjon jelentkezni az Azure AD-felhasználók **Zendesk**, akkor ki kell építenie a **Zendesk**.  
Attól függően, hogy az alkalmazások szerepkörrel a normális működés:

 1. **Végfelhasználói** fiókok bejelentkezéskor automatikusan törlődnek.
 2. **Ügynök** és **Admin** fiókokat kell manuálisan építhető **Zendesk** bejelentkezés előtt.
 
**Felhasználói fiók létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **Zendesk** bérlő.

2. Válassza ki a **Ügyféllista** fülre.

3. Válassza ki a **felhasználói** fülre, majd **Hozzáadás**.
   
    ![Felhasználó hozzáadása](./media/active-directory-saas-zendesk-tutorial/ic773632.png "felhasználó hozzáadása")
4. Írja be az e-mail cím egy meglévő Azure AD-fiókot rendelkezés szeretne, és kattintson a **mentése**.
   
    ![Új felhasználó](./media/active-directory-saas-zendesk-tutorial/ic773633.png "új felhasználó")

> [!NOTE]
> Bármely más Zendesk felhasználói fiók létrehozása eszközök, vagy rendelkezés AAD felhasználói fiókokhoz Zendesk által nyújtott API-k.


### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Zendesk Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Zendesk Britta Simon rendel, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Zendesk**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Zendesk csempére kattint, akkor kell beolvasása automatikusan bejelentkezett a Zendesk-alkalmazásba.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_203.png

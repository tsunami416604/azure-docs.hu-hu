---
title: "Oktatóanyag: Azure Active Directoryval integrált LinkedIn jogosultságszint-emelés |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a LinkedIn jogosultságszint-emelés között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 2ad9941b-c574-42c3-bd0f-5d6ec68537ef
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: jeedes
ms.openlocfilehash: 5b46323dc487bbc714c2306ed006afffe8c1bb6a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-elevate"></a>Oktatóanyag: Azure Active Directoryval integrált LinkedIn jogosultságszint-emelés

Ebben az oktatóanyagban elsajátíthatja LinkedIn jogosultságszint-emelés integrálása Azure Active Directory (Azure AD).

Jogosultságszint-emelés LinkedIn integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Szabályozhatja az Azure AD, aki hozzáfér a LinkedIn jogosultságszint-emelés
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett való LinkedIn jogosultságszint-emelés (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egy központi helyen – az Azure felügyeleti portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs LinkedIn jogosultságszint-emelés, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- A LinkedIn jogosultságszint-emelés egyszeri bejelentkezés engedélyezve van az előfizetésben

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, ha ez nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Jogosultságszint-emelés LinkedIn hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-linkedin-elevate-from-the-gallery"></a>Jogosultságszint-emelés LinkedIn hozzáadása a gyűjteményből
Az Azure AD integrálása a LinkedIn jogosultságszint-emelés konfigurálásához kell hozzáadnia LinkedIn jogosultságszint-emelés a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a LinkedIn jogosultságszint-emelés a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure felügyeleti portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Kattintson a **Hozzáadás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **LinkedIn jogosultságszint-emelés**. Az eredmények panelen kattintson a **LinkedIn jogosultságszint-emelés** hozzáadása az alkalmazáshoz.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-linkedinElevate-tutorial/tutorial-linkedinElevate_000.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD az egyszeri bejelentkezés LinkedIn jogosultságszint-emelés "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó LinkedIn jogosultságszint-emelés a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a LinkedIn jogosultságszint-emelés közötti kapcsolat kapcsolatot kell létrehozni.

Ez a hivatkozás kapcsolat létesíti értéket rendeli az **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a LinkedIn jogosultságszint-emelés.

Az Azure AD egyszeri bejelentkezést a LinkedIn jogosultságszint-emelés tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Jogosultságszint-emelés LinkedIn tesztfelhasználó létrehozása](#creating-a-linkedin-elevate-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure felügyeleti portálon, és konfigurálása egyszeri bejelentkezéshez az LinkedIn jogosultságszint-emelés alkalmazásban.

**Jogosultságszint-emelés LinkedIn konfigurálása az Azure AD egyszeri bejelentkezést, hajtsa végre az alábbi lépéseket:**

1. Az Azure felügyeleti portálján a a **LinkedIn jogosultságszint-emelés** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. A a **egyszeri bejelentkezés** párbeszédpanel, mint **mód** kiválasztása **SAML-alapú bejelentkezés** a engedélyezése az egyszeri bejelentkezéshez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-linkedinElevate-tutorial/tutorial-linkedin_01.png)

3. Egy másik webes böngészőablakban bejelentkezés a jogosultságszint-emelés LinkedIn-bérlő rendszergazdaként.

4. A **Account Center**, kattintson a **globális beállítások** alatt **beállítások**. Jelölje ki, **jogosultságszint-emelés - jogosultságszint-emelés AAD teszt** a legördülő listából.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_linkedin_admin_01.png)

5. Kattintson a **, vagy kattintson ide betölteni, és másolja az űrlap egyes mezőket** , és másolja **entitásazonosító** és **helyességi feltétel felhasználói hozzáférést (ACS) URL-címe**

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_linkedin_admin_03.png)

6. Azure-portál a **LinkedIn jogosultságszint-emelés tartomány és az URL-címek**, hajtsa végre az alábbi lépéseket, ha azt szeretné, hogy az egyszeri bejelentkezés konfigurálása **IdP kezdeményezett** mód

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_linkedin_signon_01.png)

    a. A a **azonosító** szövegmező, adja meg a **Entitásazonosító** másolt LinkedIn portálról 

    b. A a **válasz URL-CÍMEN** szövegmező, adja meg a **helyességi feltétel felhasználói hozzáférést (ACS) URL-cím** másolt LinkedIn portálról

7. Ha azt szeretné, hogy az egyszeri bejelentkezés konfigurálása **Szolgáltató kezdeményezett**, majd kattintson a speciális URL-cím megjelenítése a konfigurációs szakaszban beállítás, és a bejelentkezési URL-cím konfigurálása a következő mintát:

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=elevate&applicationInstanceId=<InstanceId>` 
    
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_linkedin_signon_02.png) 
    
8. A LinkedIn jogosultságszint-emelés alkalmazás a SAML helyességi feltételek egy meghatározott formátumban, amelyek megkövetelik olyan egyéni attribútum-leképezésekhez hozzáadása a SAML-jogkivonat attribútumok konfigurációs vár. Az alábbi képernyőfelvételen látható egy példa a. Az alapértelmezett érték **felhasználói azonosító** van **user.userprincipalname** , de ez le kell képezni a felhasználó e-mail címmel rendelkező LinkedIn jogosultságszint-emelés vár. Az adott használhatja **user.mail** attribútumot a listából, vagy használja a megfelelő attribútum értéket a szervezet konfiguráció alapján. 

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-linkedinElevate-tutorial/updateusermail.png)

9. A **felhasználói attribútumok** kattintson **nézet és egyéb felhasználói attribútumok szerkesztése** és attribútumainak beállítása. Hozzá kell adnia egy másik jogcím nevű **részleg** és az értéket meg kell feleltetni a **felhasználó.részleg**.

    | Attribútum neve | Attribútum értéke |
    | --- | --- |    
    | Szervezeti egység| felhasználó.részleg |

      ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-linkedinElevate-tutorial/userattribute.png)

      a. Kattintson a Hozzáadás attribútum attribútum részletek lapjának megnyitásához adja hozzá a részleg attribútumot, ahogy az alábbi-

      ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-linkedinElevate-tutorial/adduserattribute.png)

      b. Kattintson a **Ok** menteni az attribútum.

      c. Változtassa meg az attribútum nevét **emailaddress** való **e-mail**.


10. Az a **SAML-aláíró tanúsítványa** kattintson **metaadatainak XML-kódja** , és mentse az XML-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-linkedinElevate-tutorial/tutorial-linkedinElevate_certificate.png) 

11. Kattintson a **Save** (Mentés) gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_400.png)

12. Ugrás a **LinkedIn rendszergazdai beállítások** szakasz. Töltse fel az imént letöltött Azure-portálról feltöltése XML beállítást kattintva XML-fájlt.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_linkedin_metadata_03.png)

13. Kattintson a **a** SSO engedélyezése. Egyszeri bejelentkezési állapot állapotúról **nincs csatlakoztatva** való **csatlakoztatva**

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_linkedin_admin_05.png)

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure felügyeleti portálján Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure Management portal**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-linkedinElevate-tutorial/create_aaduser_01.png) 

2. Ugrás a **felhasználók és csoportok** kattintson **minden felhasználó** azon felhasználók listájának megjelenítéséhez.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-linkedinElevate-tutorial/create_aaduser_02.png) 

3. Kattintson a párbeszédpanel tetején **Hozzáadás** megnyitásához a **felhasználói** párbeszédpanel.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-linkedinElevate-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-linkedinElevate-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra. 

### <a name="creating-a-linkedin-elevate-test-user"></a>Jogosultságszint-emelés LinkedIn tesztfelhasználó létrehozása

Csatolt jogosultságszint-emelés alkalmazás támogatja a csak az idő a felhasználók átadása, miután a felhasználók hitelesítésére az alkalmazás automatikusan létrejön. A rendszergazda a beállítások lapon a portál tükrözés LinkedIn jogosultságszint-emelés a kapcsoló **automatikus hozzárendelése licencek** közvetlenül ahhoz, hogy időben aktív kiépítés, és ez lesz is rendel egy licencet a felhasználó.

   ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-linkedinElevate-tutorial/LinkedinUserprovswitch.png)

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon nyújtó rendszerre való jogosultságszint-emelés LinkedIn Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése LinkedIn jogosultságszint-emelés, hajtsa végre az alábbi lépéseket:**

1. Az Azure felügyeleti portálra, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **LinkedIn jogosultságszint-emelés**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-linkedinElevate-tutorial/tutorial-linkedinElevate_0001.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési Panel LinkedIn jogosultságszint-emelés mozaik gombra kattint, szerezheti be az Azure bejelentkezési oldalra, és a sikeres bejelentkezést, miután szerezheti be a jogosultságszint-emelés LinkedIn alkalmazásba.

## <a name="additional-resources"></a>További források

* [Oktatóanyag: Konfigurálása LinkedIn jogosultságszint-emelés az automatikus felhasználó kiépítése az Azure Active Directoryval](active-directory-saas-linkedinelevate-provisioning-tutorial.md)
* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_203.png

---
title: 'Oktatóanyag: Azure Active Directoryval integrált Zoho egy |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a Zoho egy között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: bbc3038c-0d8b-45dd-9645-368bd3d01a0f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2018
ms.author: jeedes
ms.openlocfilehash: 3ffabecad5cd82380d7f05d48761bd8f1e430b37
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655029"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho-one"></a>Oktatóanyag: Azure Active Directoryval integrált Zoho egy

Ebben az oktatóanyagban elsajátíthatja Zoho egy integrálása Azure Active Directory (Azure AD).

Egy Zoho integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Szabályozhatja, aki hozzáfér Zoho egy Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett (egyszeri bejelentkezés) egy Zoho a saját Azure AD-fiókok.
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs Zoho egy, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Zoho egy egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Zoho egy hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-zoho-one-from-the-gallery"></a>A gyűjteményből Zoho egy hozzáadása
Az Azure AD integrálása a Zoho egy konfigurálásához kell hozzáadnia Zoho egy a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a Zoho egy a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **Zoho egy**, jelölje be **Zoho egy** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylista egy Zoho](./media/active-directory-saas-zohoone-tutorial/tutorial_zohoone_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD az egyszeri bejelentkezés Zoho egy "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Zoho egy felhasználó az Azure ad-ben. Ez azt jelenti Zoho egy kapcsolódó felhasználói, valamint az Azure AD-felhasználó közötti kapcsolat kapcsolatot kell létrehozni.

Az Azure AD egyszeri bejelentkezést a Zoho egy tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Hozzon létre egy Zoho tesztfelhasználó](#create-a-zoho-one-test-user)**  - való egy megfelelője a Britta Simon Zoho egy felhasználó az Azure AD ábrázolását kapcsolódik.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és az Zoho egy alkalmazás egyszeri bejelentkezés konfigurálása.

**Konfigurálása az Azure AD az egyszeri bejelentkezés Zoho egy, a következő lépésekkel:**

1. Az Azure portálon a a **Zoho egy** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-zohoone-tutorial/tutorial_zohoone_samlbase.png)

3. Az a **Zoho tartománya és URL-címek** területen tegye a következőket, ha szeretne beállítani az alkalmazás **IDP** kezdeményezett mód:

    ![Az egyszeri bejelentkezés információk Zoho tartománya és URL-címek](./media/active-directory-saas-zohoone-tutorial/tutorial_zohoone_url.png)

    a. Az a **azonosítója (entitás azonosítója)** szövegmezőhöz URL-címet írja be: `one.zoho.com`

    b. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe: `https://accounts.zoho.com/samlresponse/<saml-identifier>`

    c. Ellenőrizze **megjelenítése speciális URL-beállításainak**.

    d. Az a **továbbítási állapotot** szövegmezőhöz URL-címet írja be:`https://one.zoho.com`

4. Ha szeretne beállítani az alkalmazás **SP** kezdeményezett módban hajtsa végre a következő lépést:

    Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://accounts.zoho.com/samlauthrequest/<domain_name>?serviceurl=https://one.zoho.com`
     
    > [!NOTE] 
    > Az előző **válasz URL-CÍMEN** és **bejelentkezési URL-cím** érték nincs valós. Az érték a tényleges válasz URL-CÍMEN és bejelentkezési URL-címet az oktatóanyag későbbi részében ismertetett frissíti. 

5. A a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-zohoone-tutorial/tutorial_zohoone_certificate.png) 

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-zohoone-tutorial/tutorial_general_400.png)
    
7. A a **Zoho egy konfigurációs** kattintson **Zoho egy konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out és SAML-alapú egyszeri bejelentkezés szolgáltatás URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egy Zoho konfiguráció](./media/active-directory-saas-zohoone-tutorial/tutorial_zohoone_configure.png) 

8. Egy másik webes böngészőablakban jelentkezzen be a Zoho egy vállalati webhely rendszergazdaként.

9. Az a **szervezet** lapra, kattintson **telepítő** alatt **SAML-alapú hitelesítés**.

    ![Zoho egy szervezeti](./media/active-directory-saas-zohoone-tutorial/tutorial_zohoone_setup.png)

10. Az előugró oldalon hajtsa végre a következő lépéseket:

    ![Zoho egy sig](./media/active-directory-saas-zohoone-tutorial/tutorial_zohoone_save.png)

    a. Az a **bejelentkezési URL** szövegmezőhöz illessze be az értékét **SAML-alapú egyszeri bejelentkezési URL-címe**, amely az Azure-portálon másolta.

    b. A a **Sign-out URL-cím** szövegmezőhöz illessze be az értékét **Sign-Out URL-cím**, amely az Azure-portálon másolta.

    c. Kattintson a **Tallózás** feltölteni a **tanúsítvány (Base64)** , amely az Azure-portálról letöltött.

    d. Kattintson a **Save** (Mentés) gombra.

11. A SAML-alapú hitelesítés beállítása a mentés után másolja a **SAML-Identfier** értékét, és használja ezt az értéket a **válasz URL-CÍMEN** az Azure portálon, a **Zoho tartománya és URL-címek** a szakasz.

    ![Zoho egy saml](./media/active-directory-saas-zohoone-tutorial/tutorial_zohoone_samlidenti.png)

12. Lépjen a **tartományok** fülre, majd **tartomány hozzáadása**.

    ![Zoho tartománya](./media/active-directory-saas-zohoone-tutorial/tutorial_zohoone_domain.png)

13. Az a **tartomány hozzáadása** lapon, a következő lépésekkel:

    ![Zoho egy tartomány hozzáadása](./media/active-directory-saas-zohoone-tutorial/tutorial_zohoone_adddomain.png)

    a. Az a **tartománynév** szövegmezőhöz típus tartomány például **contoso.com**.

    b. Kattintson a **Hozzáadás** parancsra.

    >[!Note]
    >A tartomány követő hozzáadása után [ezek](https://www.zoho.com/one/help/admin-guide/domain-verification.html) lépések végrehajtásával ellenőrizze a tartományt. Ha a tartomány verfified, használja a tartomány nevét a **bejelentkezési URL-cím** a **Zoho tartománya és URL-címek** szakaszban az Azure portálon.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-zohoone-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-zohoone-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/active-directory-saas-zohoone-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-zohoone-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-zoho-one-test-user"></a>Zoho egy tesztfelhasználó létrehozása

Ahhoz, hogy jelentkezzen be a Zoho egy Azure AD-felhasználók, akkor ki kell építenie a Zoho egy. A Zoho egy kézi tevékenység.

**Felhasználói fiók létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be egy, a biztonsági rendszergazda Zoho.

2. Az a **felhasználók** lapra, kattintson a **felhasználói embléma**.

    ![Zoho egy felhasználó](./media/active-directory-saas-zohoone-tutorial/tutorial_zohoone_users.png)

3. Az a **felhasználó hozzáadása** lapon, a következő lépésekkel:

    ![Zoho egy felhasználó hozzáadása](./media/active-directory-saas-zohoone-tutorial/tutorial_zohoone_adduser.png)
    
    a. A **neve** szöveg mezőbe írja be például a felhasználó nevét **Britta simon**.
    
    b. A **E-mail cím** szöveg mezőbe írja be például a felhasználó e-mail **brittasimon@contoso.com**.

    >[!Note]
    >Az ellenőrzött tartomány listából válassza ki a tartományt.

    c. Kattintson a **Hozzáadás** parancsra.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Zoho egy Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése Zoho egy, a következő lépésekkel:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Zoho egy**.

    ![Az alkalmazások listáját a Zoho egy hivatkozás](./media/active-directory-saas-zohoone-tutorial/tutorial_zohoone_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Zoho egy csempére kattint, akkor kell beolvasni automatikusan bejelentkezett az Zoho egy alkalmazáshoz.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-zohoone-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-zohoone-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-zohoone-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-zohoone-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-zohoone-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-zohoone-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-zohoone-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-zohoone-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-zohoone-tutorial/tutorial_general_203.png


---
title: 'Oktatóanyag: Azure Active Directoryval integrált Replicon |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Replicon között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 02a62f15-917c-417c-8d80-fe685e3fd601
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: jeedes
ms.openlocfilehash: 4afe9bb36080027ba1873dc6fd3938972044a5d1
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-replicon"></a>Oktatóanyag: Azure Active Directoryval integrált Replicon

Ebben az oktatóanyagban elsajátíthatja Replicon integrálása az Azure Active Directory (Azure AD).

Replicon integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér Replicon szabályozhatja.
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett Replicon (egyszeri bejelentkezés) számára a saját Azure AD-fiókok.
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs Replicon, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Replicon egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Replicon hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-replicon-from-the-gallery"></a>A gyűjteményből Replicon hozzáadása
Az Azure AD integrálása a Replicon konfigurálásához kell hozzáadnia Replicon a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Replicon hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **Replicon**, jelölje be **Replicon** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában replicon](./media/active-directory-saas-replicon-tutorial/tutorial_replicon_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján Replicon.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Replicon a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Replicon közötti kapcsolat kapcsolatot kell létrehozni.

Replicon, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a Replicon tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Replicon tesztfelhasználó létrehozása](#create-a-replicon-test-user)**  - való Britta Simon valami Replicon, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Replicon alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés Replicon, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Replicon** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-replicon-tutorial/tutorial_replicon_samlbase.png)

3. Az a **Replicon tartomány és az URL-címek** területen tegye a következőket:

    ![Az egyszeri bejelentkezés információk replicon tartomány és az URL-címek](./media/active-directory-saas-replicon-tutorial/tutorial_replicon_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://na2.replicon.com/<companyname>/saml2/sp-sso/post`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: `https://global.replicon.com/<companyname>`

    c. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe: `https://global.replicon.com/!/saml2/<companyname>/sso/post`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím, azonosítóját és a válasz URL-CÍMEN. Ügyfél [Replicon ügyfél-támogatási csoport](https://www.replicon.com/customerzone/contact-support) beolvasni ezeket az értékeket. 

4. Az a **SAML-aláíró tanúsítványa** területen kattintson **metaadatainak XML-kódja** és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-replicon-tutorial/tutorial_replicon_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-replicon-tutorial/tutorial_general_400.png)

6. Egy másik webes böngészőablakban jelentkezzen be a Replicon vállalati webhely rendszergazdaként.

7. SAML 2.0 konfigurálásához hajtsa végre az alábbi lépéseket:
   
    ![SAML-alapú hitelesítés engedélyezéséhez](./media/active-directory-saas-replicon-tutorial/ic777805.png "engedélyezése SAML-alapú hitelesítés")
    
    a. A megjelenítendő a **EnableSAML Authentication2** párbeszédpanel, az URL-cím, a következő bővítése után a vállalat kulcs: `/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`
    
    * Az alábbiakban látható a teljes URL-cím sémája:  
   `https://na2.replicon.com/\<YourCompanyKey\>/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`
   
   b. Kattintson a **+** bontsa ki a **v20Configuration** szakasz.

   c. Kattintson a **+** bontsa ki a **metaDataConfiguration** szakasz.

   d. Kattintson a **Choose File**ki az identity provider metaadatok XML-fájlt, majd kattintson a **Submit**.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-replicon-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-replicon-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/active-directory-saas-replicon-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-replicon-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-replicon-test-user"></a>Replicon tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Replicon bejelentkezni, akkor ki kell építenie Replicon be. Replicon, ha egy kézi tevékenység.

**Adja meg a felhasználók átadása, hajtsa végre az alábbi lépéseket:**

1. A webböngésző ablakának jelentkezzen be a Replicon vállalati webhely rendszergazdaként.

2. Ugrás a **felügyeleti \> felhasználók**.
   
    ![Felhasználók](./media/active-directory-saas-replicon-tutorial/ic777806.png "felhasználók")

3. Kattintson a **hozzáadni a felhasználót +**.
   
    ![Felhasználó hozzáadása](./media/active-directory-saas-replicon-tutorial/ic777807.png "felhasználó hozzáadása")

4. Az a **felhasználói profil** területen tegye a következőket:
   
    ![Felhasználói profil](./media/active-directory-saas-replicon-tutorial/ic777808.png "felhasználói profil")
    
    a. Az a **bejelentkezési név** szövegmezőhöz típus az Azure AD ki kívánja építeni például Azure AD-felhasználó e-mail címe **BrittaSimon@contoso.com**.
    
    b. Mint **hitelesítési típus**, jelölje be **SSO**.
    
    c. Az a **részleg** szövegmező, írja be a felhasználói osztály.

    d. Mint **alkalmazott típus**, jelölje be **rendszergazda**.

    e. Kattintson a **felhasználói profil mentése**.

>[!NOTE]
>Bármely más Replicon felhasználói fiók létrehozása eszközök vagy Replicon kiépíteni az Azure AD-felhasználói fiókok által nyújtott API-k.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Replicon Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése Replicon, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Replicon**.

    ![Az alkalmazások listáját a Replicon hivatkozás](./media/active-directory-saas-replicon-tutorial/tutorial_replicon_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Replicon csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az Replicon alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_203.png


---
title: "Oktatóanyag: Azure Active Directoryval integrált EBSCO |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és EBSCO között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 144f7f65-69e9-4016-a151-fe1104fd6ba8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: jeedes
ms.openlocfilehash: ea7fe09c31d88cf2095b3a3777b6b1f9feb8df46
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="tutorial-azure-active-directory-integration-with-ebsco"></a>Oktatóanyag: Azure Active Directoryval integrált EBSCO

Ebben az oktatóanyagban elsajátíthatja EBSCO integrálása az Azure Active Directory (Azure AD).

EBSCO integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér EBSCO szabályozhatja.
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett EBSCO (egyszeri bejelentkezés) számára a saját Azure AD-fiókok.
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs EBSCO, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy EBSCO egyszeri bejelentkezés engedélyezve van az előfizetésben

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből EBSCO hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-ebsco-from-the-gallery"></a>A gyűjteményből EBSCO hozzáadása
Az Azure AD integrálása a EBSCO konfigurálásához kell hozzáadnia EBSCO a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből EBSCO hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **EBSCO**, jelölje be **EBSCO** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában EBSCO](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján EBSCO.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó EBSCO a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a EBSCO közötti kapcsolat kapcsolatot kell létrehozni.

Az Azure AD egyszeri bejelentkezést a EBSCO tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Hozzon létre egy EBSCO tesztfelhasználó](#create-an-ebsco-test-user)**  -EBSCOhost kiépítés felhasználó/személyre szabása automatizálható. EBSCO közvetlenül időponthoz kötött támogatja a felhasználók átadása.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az EBSCO alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés EBSCO, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **EBSCO** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_samlbase.png)

3. Az a **EBSCO tartomány és az URL-címek** területen tegye a következőket, ha szeretne beállítani az alkalmazás **IDP** kezdeményezett mód:

    ![Az egyszeri bejelentkezés információk EBSCO tartomány és az URL-címek](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_url.png)

    Az a **azonosító** szövegmezőhöz URL-címet írja be:`pingsso.ebscohost.com`

4. Ellenőrizze **megjelenítése speciális URL-beállításainak** , és végezze el a következő lépés, ha szeretne beállítani az alkalmazás **SP** kezdeményezett mód:

    ![Az egyszeri bejelentkezés információk EBSCO tartomány és az URL-címek](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_url1.png)

    Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe:`http://search.ebscohost.com/login.aspx?authtype=sso&custid=<unique EBSCO customer ID>&profile=<profile ID>`
     
    > [!NOTE] 
    > A bejelentkezési URL-cím értéke nincs valós. Frissítse az értéket a tényleges bejelentkezési URL-címet. Ügyfél [EBSCO ügyfél-támogatási csoport](mailto:sso@ebsco.com) az értéket be kell olvasni. 

    o **egyedi elemeket:**  

    o **Custid** adjon meg egyedi EBSCO ügyfél azonosítója = 

    o **profil** = ügyfelek igazíthatja a felhasználó átirányítására egy adott profilt (attól függően, hogy mi ezek vásárolhat EBSCO) mutató hivatkozást. Tudják azt írni egy adott profil. A fő azonosítói eds (EBSCO észlelési szolgáltatás) és ehost (EBSOCOhost adatbázisok). Ugyanazon utasítások [Itt](https://help.ebsco.com/interfaces/EBSCOhost/EBSCOhost_FAQs/How_do_I_set_up_direct_links_to_EBSCOhost_profiles_and_or_databases#profile).

5. EBSCO alkalmazás vár a SAML helyességi feltételek egy meghatározott formátumban. A következő jogcímek alkalmazás konfigurálása. Ezek az attribútumok értékének kezelheti a "**felhasználói attribútumok**" szakasz alkalmazás integráció lapján. Az alábbi képernyőfelvételen látható egy példa a.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_attribute.png)

    > [!Note]
    > A **neve** attribútum megadása kötelező, és az le van képezve **felhasználói azonosító** EBSCO alkalmazásban. Ez az alapértelmezés szerint hozzáadva, nem kell manuálisan adja hozzá ezt.
    
6. A a **felhasználói attribútumok** a szakasz a **egyszeri bejelentkezés** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, a fenti ábrán látható módon, és hajtsa végre a következő lépéseket:
    
    | Attribútum neve | Attribútum értéke |
    | ---------------| --------------- |    
    | Utónév   | user.givenname |
    | Vezetéknév   | user.surname |
    | E-mail cím   | user.mail |

    a. Kattintson a **Hozzáadás attribútum** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-ebsco-tutorial/tutorial_officespace_04.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-ebsco-tutorial/tutorial_attribute_05.png)
    
    b. Az a **neve** szövegmező, írja be az adott sorhoz feltüntetett attribútumot nevét.
    
    c. Az a **érték** kilistázásához írja be a sorhoz látható attribútum értéke.
    
    d. Kattintson a **Ok**

7. Az a **SAML-aláíró tanúsítványa** területen kattintson **metaadatainak XML-kódja** és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_certificate.png) 

8. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-ebsco-tutorial/tutorial_general_400.png)
    
9. Egyszeri bejelentkezés konfigurálása **EBSCO** oldalon kell küldeniük a letöltött **metaadatainak XML-kódja** való [EBSCO támogatási csoport](mailto:sso@ebsco.com). Akkor állítsa be ezt a beállítást, hogy a SAML SSO kapcsolat mindkét oldalán megfelelően beállítva.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-ebsco-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-ebsco-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/active-directory-saas-ebsco-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-ebsco-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-an-ebsco-test-user"></a>Hozzon létre egy EBSCO tesztfelhasználó számára

EBSCO, ha a felhasználók átadása akkor automatikus.

**Felhasználói fiók létrehozásához hajtsa végre az alábbi lépéseket:**

Az Azure AD EBSCO alkalmazás továbbítja a szükséges adatokat. EBSCO meg a felhasználók átadása lehet automatikus vagy egyszeri űrlap igényelnek. Ez attól függ, hogy az ügyfél rendelkezik-e nagy mennyiségű, korábban létező EBSCOhost fiókok mentett személyes beállításokkal. Ugyanez a kell megvitatni a [EBSCO támogatási csoport](mailto:sso@ebsco.com) végrehajtása során. Mindkét módszer esetén az ügyfél nem fiókokat kell létrehoznia a EBSCOhost tesztelése előtt.

   >[!Note]
   >EBSCOhost kiépítés felhasználó/személyre szabása automatizálható. Ügyfél [EBSCO támogatási csoport](mailto:sso@ebsco.com) közvetlenül időbeni elmozdítására vonatkozó a felhasználók átadása. 
 
### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés EBSCO Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése EBSCO, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **EBSCO**.

    ![Az alkalmazások listáját a EBSCO hivatkozás](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

1. Ha a hozzáférési panelen EBSCO csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az EBSCO alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md).

2. Ha az alkalmazás bejelentkezni, kattintson a a **bejelentkezés** gombra a jobb felső sarokban.

    ![Az alkalmazások listáját EBSCO bejelentkezni](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_signin.png)
 
3. Kapni fog egy egyszeri kérdés kell párosítani a intézményi SAML/jelentkezzen be egy **a fiókját meglévő MyEBSCOhost intézmény fiókja most** vagy **hozzon létre egy új MyEBSCOhost fiókot, és a hivatkozás a intézmény fiók**. A fiók személyre szabása a EBSCOhost alkalmazására szolgál. A beállításnak a **hozzon létre egy új fiókot** és látni fogja, hogy az űrlap testre szabási célból saml-válasz értékeivel előre kész-e az alábbi képernyőfelvételen látható módon. Kattintson a **"Folytatás"** a mentéshez.
    
     ![Az alkalmazások listáját a EBSCO felhasználó](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_user.png)

4. A fenti telepítés befejezése után törölje a cookie-k/gyorsítótár bejelentkezési újra. Nem kell manuálisan signin újra, és a rendszer megjegyezze a személyre szabási beállításait

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_203.png


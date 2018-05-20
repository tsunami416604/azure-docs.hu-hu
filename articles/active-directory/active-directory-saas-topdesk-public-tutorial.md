---
title: 'Oktatóanyag: Azure Active Directoryval integrált TOPdesk - nyilvános |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és TOPdesk - nyilvános között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 0873299f-ce70-457b-addc-e57c5801275f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: jeedes
ms.openlocfilehash: 76b75ffde280047f8ed6c2d4173e905df1d7a658
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---public"></a>Oktatóanyag: Azure Active Directoryval integrált TOPdesk - nyilvános

Ebben az oktatóanyagban elsajátíthatja TOPdesk - nyilvános Azure Active Directory (Azure AD) integrálása.

TOPdesk - nyilvános az Azure AD integrálása lehetővé teszi a következő előnyöket biztosítja:

- Szabályozhatja, aki hozzáfér TOPdesk - nyilvános Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett TOPdesk - nyilvános (egyszeri bejelentkezés) a saját Azure AD-fiókok számára.
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integrációs konfigurálása TOPdesk - nyilvános, a következőkre van szüksége:

- Az Azure AD szolgáltatásra
- Egy TOPdesk - nyilvános egyszeri bejelentkezés engedélyezve van az előfizetésben

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Hozzáadás TOPdesk - nyilvános a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-topdesk---public-from-the-gallery"></a>Hozzáadás TOPdesk - nyilvános a gyűjteményből
A TOPdesk - nyilvános az Azure AD-integráció konfigurálása kell hozzáadnia a TOPdesk - nyilvános a gyűjteményből, a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a TOPdesk - nyilvános a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **TOPdesk - nyilvános**, jelölje be **TOPdesk - nyilvános** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![TOPdesk - nyilvános az eredménylistában](./media/active-directory-saas-topdesk-public-tutorial/tutorial_topdesk-public_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezést a TOPdesk tesztelése és konfigurálása – nyilvános "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés használatához az Azure AD meg kell tudni, hogy milyen a párjukhoz felhasználó TOPdesk - nyilvános egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználó és a kapcsolódó felhasználó a TOPdesk - hivatkozás kapcsolatának nyilvános kell létrehozni.

A TOPdesk - nyilvános, rendelje az értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a TOPdesk - tesztelése és konfigurálása a nyilvános, végre kell hajtania a következő építőelemeket:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Hozzon létre egy TOPdesk - nyilvános tesztfelhasználó](#create-a-topdesk---public-test-user)**  - való egy megfelelője a Britta Simon TOPdesk - nyilvános, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és a TOPdesk - nyilvános alkalmazás egyszeri bejelentkezés konfigurálása.

**Az Azure AD az egyszeri bejelentkezés konfigurálása TOPdesk - nyilvános, hajtsa végre a következő lépéseket:**

1. Az Azure portálon a a **TOPdesk - nyilvános** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-topdesk-public-tutorial/tutorial_topdesk-public_samlbase.png)

3. Az a **TOPdesk - nyilvános tartományi és URL-címek** területen tegye a következőket:

    ![Az egyszeri bejelentkezés információk TOPdesk - nyilvános tartományi és URL-címek](./media/active-directory-saas-topdesk-public-tutorial/tutorial_topdesk-public_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://<companyname>.topdesk.net`
    
    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: `https://<companyname>.topdesk.net/tas/public/login/verify`

    c. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe: `https://<companyname>.topdesk.net/tas/public/login/saml`
     
    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges azonosítója, válasz URL-CÍMEN és bejelentkezési URL-cím. Válasz URL-CÍMEN explaned az oktatóanyag későbbi részében. Ügyfél [TOPdesk - nyilvános ügyfél-támogatási csoport](https://help.topdesk.com/saas/enterprise/user/) beolvasni ezeket az értékeket.  

4. Az a **SAML-aláíró tanúsítványa** területen kattintson **metaadatainak XML-kódja** és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-topdesk-public-tutorial/tutorial_topdesk-public_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-topdesk-public-tutorial/tutorial_general_400.png)
    
6. A a **TOPdesk - nyilvános konfigurációs** kattintson **konfigurálása TOPdesk - nyilvános** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-címet, a SAML entitás azonosítója és a SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![TOPdesk - nyilvános konfigurációja](./media/active-directory-saas-topdesk-public-tutorial/tutorial_topdesk-public_configure.png) 

7. Jelentkezzen be a **TOPdesk - nyilvános** vállalati hely rendszergazdaként.

8. Az a **TOPdesk** menüben kattintson a **beállítások**.
   
    ![Beállítások](./media/active-directory-saas-topdesk-public-tutorial/ic790598.png "beállítások")

9. Kattintson a **bejelentkezési beállítások**.
   
    ![Bejelentkezési beállítások](./media/active-directory-saas-topdesk-public-tutorial/ic790599.png "bejelentkezési beállítások")

10. Bontsa ki a **bejelentkezési beállítások** menüben, majd kattintson **általános**.
   
    ![Általános](./media/active-directory-saas-topdesk-public-tutorial/ic790600.png "általános")

11. Az a **nyilvános** szakasza a **SAML bejelentkezési** konfigurációs szakaszban, hajtsa végre a következő lépéseket:
   
    ![Műszaki beállítások](./media/active-directory-saas-topdesk-public-tutorial/ic790601.png "műszaki beállításai")
   
    a. Kattintson a **letöltése** a nyilvános metaadatait tartalmazó fájl letöltéséhez, és mentse helyileg a számítógépen.
   
    b. Nyissa meg a letöltött metaadat-fájlt, és keresse meg a **AssertionConsumerService** csomópont.

    ![AssertionConsumerService](./media/active-directory-saas-topdesk-public-tutorial/ic790619.png "AssertionConsumerService")
   
    c. Másolás a **AssertionConsumerService** értékét, illessze be ezt az értéket a **válasz URL-CÍMEN** textbox **TOPdesk - nyilvános tartományi és URL-címek** szakasz.      
   
12. A tanúsítványfájl létrehozásához hajtsa végre az alábbi lépéseket:
    
    ![Tanúsítvány](./media/active-directory-saas-topdesk-public-tutorial/ic790606.png "tanúsítvány")
    
    a. Nyissa meg a letöltött metaadatfájl Azure-portálon.
    
    b. Bontsa ki a **RoleDescriptor** csomópont egy **xsi: type** a **táplált: ApplicationServiceType**.
    
    c. Másolja a értékének a **x.509** csomópont.
    
    d. Mentse a másolt **x.509** érték helyileg a fájlt a számítógépen.

13. Az a **nyilvános** kattintson **Hozzáadás**.
    
    ![SAML bejelentkezési](./media/active-directory-saas-topdesk-public-tutorial/ic790625.png "SAML-bejelentkezés")

14. Az a **SAML-alapú konfigurációs Segéd** párbeszédpanel lapon, a következő lépésekkel:
    
    ![SAML-alapú konfigurációs Segéd](./media/active-directory-saas-topdesk-public-tutorial/ic790608.png "SAML-alapú konfigurációs Segéd")
    
    a. Töltse fel az Azure-portálon, a letöltött metaadatfájl **összevonási metaadatok**, kattintson a **Tallózás**.

    b. Töltse fel a tanúsítványfájlt, a **tanúsítvány (RSA)**, kattintson a **Tallózás**.

    c. Fel kell töltenie az embléma fájlt során kapott azonosítóértékeket TOPdesk terméktámogatással a **embléma ikon**, kattintson a **Tallózás**.

    d. Az a **felhasználói név attribútum** szövegmezőhöz típus `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    e. Az a **megjelenített név** szövegmező, adja meg a konfiguráció nevét.

    f. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-topdesk-public-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-topdesk-public-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/active-directory-saas-topdesk-public-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-topdesk-public-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-topdesk---public-test-user"></a>Hozzon létre egy TOPdesk - nyilvános tesztfelhasználó számára

Ahhoz, hogy az Azure AD-felhasználók TOPdesk - be tudjon jelentkezni nyilvános, TOPdesk - nyilvános be kell kiépítve.  
TOPdesk - esetén nyilvános, kézi tevékenység.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Adja meg a felhasználók átadása, hajtsa végre az alábbi lépéseket:
1. Jelentkezzen be a **TOPdesk - nyilvános** vállalati hely rendszergazdaként.

2. Kattintson a felső menüben **TOPdesk \> új \> támogatófájljait \> személy**.
   
    ![Személy](./media/active-directory-saas-topdesk-public-tutorial/ic790628.png "személy")

3. Új személy párbeszédpanelen hajtsa végre a következő lépéseket:
   
    ![Új személy](./media/active-directory-saas-topdesk-public-tutorial/ic790629.png "új személy")
   
    a. Kattintson az Általános fülre.

    b. Az a **vezetékneve** szövegmező, írja be például a Simon a felhasználó vezetékneve
 
    c. Válassza ki a **hely** a fiókhoz.
 
    d. Kattintson a **Save** (Mentés) gombra.

> [!NOTE]
> Bármely más TOPdesk - nyilvános felhasználói fiók létrehozása eszközök is használhatja, vagy TOPdesk - nyilvános kiépítéséhez az Azure AD-felhasználói fiókok által nyújtott API-k.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés TOPdesk - nyilvános Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése TOPdesk - nyilvános, hajtsa végre a következő lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **TOPdesk - nyilvános**.

    ![A TOPdesk - nyilvános hivatkozás az alkalmazások listáját a](./media/active-directory-saas-topdesk-public-tutorial/tutorial_topdesk-public_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha kattint a TOPdesk – nyilvános csempére a hozzáférési panelen, meg kell beolvasni automatikusan aláírt a a TOPdesk - nyilvános alkalmazás.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-topdesk-public-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-topdesk-public-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-topdesk-public-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-topdesk-public-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-topdesk-public-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-topdesk-public-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-topdesk-public-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-topdesk-public-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-topdesk-public-tutorial/tutorial_general_203.png


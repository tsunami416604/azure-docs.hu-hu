---
title: "Oktatóanyag: Azure Active Directory-integráció a ScaleX vállalati |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és ScaleX vállalati között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c2379a8d-a659-45f1-87db-9ba156d83183
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2017
ms.author: jeedes
ms.openlocfilehash: f83d817647a5339176260bfcf73005045f9ead54
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-scalex-enterprise"></a>Oktatóanyag: Azure Active Directory-integráció a ScaleX vállalati

Ebben az oktatóanyagban elsajátíthatja ScaleX vállalati integrálása az Azure Active Directory (Azure AD).

ScaleX vállalati integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a ScaleX vállalati hozzáféréssel rendelkező Azure AD-ben
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett ScaleX vállalati (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg. Alkalmazás-hozzáférés és egyszeri bejelentkezés az [Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a ScaleX vállalati, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy ScaleX vállalati egyszeri bejelentkezés engedélyezve van az előfizetésben

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, ha ez nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből ScaleX vállalati hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-scalex-enterprise-from-the-gallery"></a>A gyűjteményből ScaleX vállalati hozzáadása
ScaleX vállalat az Azure AD-integráció konfigurálásához szüksége ScaleX vállalati hozzáadása a kezelt SaaS-alkalmazások listáját a gyűjteményből.

**A gyűjteményből ScaleX vállalati hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Kattintson a **Hozzáadás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **ScaleX vállalati**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-scalexenterprise-tutorial/tutorial_scalexenterprise_search.png)

5. Az eredmények panelen válassza ki a **ScaleX vállalati**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-scalexenterprise-tutorial/tutorial_scalexenterprise_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban konfigurálása és tesztelése az Azure AD egyszeri bejelentkezést a ScaleX vállalati "Britta Simon." nevű tesztfelhasználó alapján

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó ScaleX vállalati a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó ScaleX vállalat közötti kapcsolat kapcsolatot kell létrehozni.

Ez a hivatkozás kapcsolat létesíti értéket rendeli az **felhasználónév** értékeként Azure AD-ben a **felhasználónév** ScaleX vállalat.

Az Azure AD egyszeri bejelentkezést a vállalati ScaleX tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[ScaleX vállalati tesztfelhasználó létrehozása](#creating-a-scalex-enterprise-test-user)**  - kell rendelkeznie a megfelelője a Britta Simon ScaleX vállalat, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az ScaleX vállalati alkalmazásban.

**Az Azure AD egyszeri bejelentkezést a ScaleX vállalati megadásához hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **ScaleX vállalati** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. A a **egyszeri bejelentkezés** párbeszédpanel, mint **mód** válasszon **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-scalexenterprise-tutorial/tutorial_scalexenterprise_samlbase.png)

3. Az a **ScaleX vállalati tartomány és az URL-címek** területen tegye a következőket, ha szeretne beállítani az alkalmazás **IDP** kezdeményezett mód:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-scalexenterprise-tutorial/tutorial_scalexenterprise_url1.png)

    a. Az a **azonosító** szövegmező, írja be az értéket a következő minta használatával:`https://platform.rescale.com/saml2/<company id>/`

    b. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe:`https://platform.rescale.com/saml2/<company id>/acs/`

4. Ellenőrizze **megjelenítése speciális URL-beállításainak**, ha szeretne beállítani az alkalmazás **SP** kezdeményezett mód:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-scalexenterprise-tutorial/tutorial_scalexenterprise_url2.png)

    Az a **bejelentkezési URL-cím** szövegmező, írja be az értéket a következő minta használatával:`https://platform.rescale.com/saml2/<company id>/sso/`
     
    > [!NOTE] 
    > Ezek a megadandó nem valódi értékek. Ezek az értékek frissíti a tényleges azonosítója, válasz és bejelentkezési URL-címe. Ügyfél [ScaleX vállalati ügyfél-támogatási csoport](http://info.rescale.com/contact_sales) beolvasni ezeket az értékeket. 

5. A ScaleX alkalmazás vár a SAML helyességi feltételek egy meghatározott formátumban, amelyhez szükség van egyéni attribútum leképezéseket a SAML-jogkivonat attribútumok konfiguráció módosítására. Kattintson a **nézet és egyéb felhasználói attribútumok szerkesztése** jelölőnégyzetet, hogy nyissa meg az egyéni attribútumok beállításait.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-scalexenterprise-tutorial/scalex_attributes.png)
    
    a. Kattintson a jobb gombbal a attribútum **neve** , és válassza a törlés.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-scalexenterprise-tutorial/delete_attribute_name.png)

    b. Kattintson a **emailaddress** attribútum a attribútum szerkesztése ablak megnyitásához. Módosítsa az értéket **user.mail** való **user.userprincipalname** kattintson az OK gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-scalexenterprise-tutorial/edit_email_attribute.png)   
    
5. A a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-scalexenterprise-tutorial/tutorial_scalexenterprise_certificate.png) 

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_400.png)
    
7. A a **ScaleX vállalati konfiguráció** kattintson **konfigurálása ScaleX vállalati** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító** és **SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-scalexenterprise-tutorial/tutorial_scalexenterprise_configure.png) 

8. Egyszeri bejelentkezés konfigurálása **ScaleX vállalati** ügyféloldali, jelentkezzen be rendszergazdaként a vállalati ScaleX vállalati webhely.

9. Kattintson a felső jobbra, és válassza a menü **Contoso felügyeleti**.

    > [!NOTE] 
    > Contoso csak egy példa. A tényleges vállalat neve legyen. 

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-scalexenterprise-tutorial/Test_Admin.png) 

10. Válassza ki **integrációja** a felső menüben, és válassza ki a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-scalexenterprise-tutorial/admin_sso.png) 

11. Töltse ki az űrlapot az alábbiak szerint:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-scalexenterprise-tutorial/scalex_admin_save.png) 
    
    a. Válassza ki **"Bármely felhasználó, aki hitelesítheti létrehozása egyszeri bejelentkezési modellel."**

    b. **Szolgáltató saml**: illessze be az érték ***urn: oasis: nevek: tc: SAML:2.0:nameid-formátum: állandó***

    c. **Az ACS-válasz identitásszolgáltató e-mail mező neve**: illessze be az érték`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. **Identity Provider EntityDescriptor Entitásazonosító:** illessze be a **SAML Entitásazonosító** az Azure-portálon átmásolja értéket.

    e. **Identity Provider SingleSignOnService URL-címe:** illessze be a **SAML-alapú egyszeri bejelentkezési URL-címe** Azure-portálról.

    f. **Szolgáltató nyilvános X509 identitástanúsítvány:** nyissa meg a X509 tanúsítvány az Azure-portálról letöltve a Jegyzettömbben beillesztése ebben a mezőben. Ellenőrizze, hogy nincsenek a nincs sortörések a tanúsítványok tartalmának közepén.
    
    g. Ellenőrizze az alábbi jelölőnégyzeteket: **engedélyezve, a NameID titkosítására és a bejelentkezési AuthnRequests.**

    h. Kattintson a **egyszeri bejelentkezési beállítások** menti a beállításokat.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-scalexenterprise-tutorial/create_aaduser_01.png) 

2. Ugrás a **felhasználók és csoportok** kattintson **minden felhasználó** azon felhasználók listájának megjelenítéséhez.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-scalexenterprise-tutorial/create_aaduser_02.png) 

3. Kattintson a párbeszédpanel tetején **Hozzáadás** megnyitásához a **felhasználói** párbeszédpanel.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-scalexenterprise-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-scalexenterprise-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-scalex-enterprise-test-user"></a>ScaleX vállalati tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók ScaleX vállalati bejelentkezni, akkor ki kell építenie a ScaleX vállalati. ScaleX vállalati automatikus feladatról, és nincs manuális lépések szükségesek. Bárki, aki képes sikeresen hitelesíteni egyszeri bejelentkezési hitelesítő adatokkal rendelkező automatikusan megkapják a ScaleX oldalon.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon szerint ScaleX vállalati való hozzáférés engedélyezése az Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése ScaleX vállalati, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **ScaleX vállalati**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-scalexenterprise-tutorial/tutorial_scalexenterprise_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.

### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Kattintson a hozzáférési panelen ScaleX vállalati csempére, akkor fogja lekérni automatikusan bejelentkezett az ScaleX vállalati alkalmazás. A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](https://msdn.microsoft.com/library/dn308586).


## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_203.png


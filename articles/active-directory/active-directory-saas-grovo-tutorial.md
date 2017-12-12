---
title: "Oktatóanyag: Azure Active Directoryval integrált Grovo |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Grovo között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 399cecc3-aa62-4914-8b6c-5a35289820c1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2017
ms.author: jeedes
ms.openlocfilehash: 9deb4c9bd6719e7cf86883fba1306c435de0ebb9
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-grovo"></a>Oktatóanyag: Azure Active Directoryval integrált Grovo

Ebben az oktatóanyagban elsajátíthatja Grovo integrálása az Azure Active Directory (Azure AD).

Grovo integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér Grovo szabályozhatja.
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett Grovo (egyszeri bejelentkezés) számára a saját Azure AD-fiókok.
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs Grovo, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Grovo egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Grovo hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-grovo-from-the-gallery"></a>A gyűjteményből Grovo hozzáadása
Az Azure AD integrálása a Grovo konfigurálásához kell hozzáadnia Grovo a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Grovo hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **Grovo**, jelölje be **Grovo** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában Grovo](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban konfigurálása, és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon." nevű tesztfelhasználó alapján Grovo

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Grovo a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Grovo közötti kapcsolat kapcsolatot kell létrehozni.

Grovo, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD az egyszeri bejelentkezés az Grovo tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Grovo tesztfelhasználó létrehozása](#create-a-grovo-test-user)**  - való Britta Simon valami Grovo, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Grovo alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés Grovo, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Grovo** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_samlbase.png)

3. Az a **Grovo tartomány és az URL-címek** területen tegye a következőket, ha szeretne beállítani az alkalmazás **IDP** kezdeményezett mód:

    ![Az egyszeri bejelentkezés információk Grovo tartomány és az URL-címek](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_url.png)

    a. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe:`https://<subdomain>.grovo.com/sso/saml2/metadata`

    b. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe:`https://<subdomain>.grovo.com/sso/saml2/saml-assertion`

4.  Ellenőrizze **megjelenítése speciális URL-beállításainak**, hajtsa végre a következő lépéseket:  

    ![Az egyszeri bejelentkezés információk Grovo tartomány és az URL-címek](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_url1.png)

    a. Az a **állapot továbbítása** szövegmező, adja meg a következő minta használatával URL-címe:`https://<subdomain>.grovo.com`

    b. Ha szeretne beállítani az alkalmazás **SP** kezdeményezett mód, hajtsa végre a következő lépéseket:

    ![Az egyszeri bejelentkezés információk Grovo tartomány és az URL-címek](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_url2.png)

    Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe:`https://<subdomain>.grovo.com/sso/saml2/saml-assertion`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges azonosítója, válasz URL-CÍMEN, bejelentkezési URL-cím és a továbbítási állapotától. Ügyfél [Grovo támogatási csoport](https://www.grovo.com/contact-us) beolvasni ezeket az értékeket.
 
5. Grovo alkalmazás vár a SAML helyességi feltételek egy meghatározott formátumban. A következő jogcímek alkalmazás konfigurálása. Ezek az attribútumok értékének kezelheti a "**felhasználói attribútumok**" szakasz alkalmazás integráció lapján. Az alábbi képernyőfelvételen látható egy példa a.
    
    ![Egyszeri bejelentkezés attribútum konfigurálása](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_attribute.png)
    
6. A a **felhasználói attribútumok** a szakasz a **egyszeri bejelentkezés** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, az ábrán látható módon, és hajtsa végre a következő lépéseket:
    
    | Attribútum neve | Attribútum értéke |
    | ------------------- | -------------------- |    
    | Utónév              | User.givenName |
    | Vezetéknév               | User.surname |

    a. Kattintson a **Hozzáadás attribútum** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés attribútum konfigurálása](./media/active-directory-saas-grovo-tutorial/tutorial_attribute_04.png)

    ![Egyszeri bejelentkezés attribútum konfigurálása](./media/active-directory-saas-grovo-tutorial/tutorial_attribute_05.png)

    b. Az a **neve** szövegmező, írja be az adott sorhoz feltüntetett attribútumot nevét.

    c. Az a **érték** kilistázásához írja be a sorhoz látható attribútum értéke.
    
    d. Kattintson az **OK** gombra.


7. Az a **SAML-aláíró tanúsítványa** kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltése szabadkézi](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_certificate.png) 

8. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-grovo-tutorial/tutorial_general_400.png)

9. A a **Grovo konfigurációs** kattintson **konfigurálása Grovo** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító és SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Grovo konfiguráció](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_configure.png) 

10. Egy másik webes böngészőablakban jelentkezzen be Grovo rendszergazdaként.

11. Ugrás a **ADMIN** > **integrációja**.
 
    ![Grovo konfiguráció](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_admin.png) 

12. Kattintson a **SET UP** alatt **SP kezdeményezett SAML 2.0** szakasz.

    ![Grovo konfiguráció](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_setup.png)

13. A **SP kezdeményezett SAML 2.0** előugró ablakban, a következő lépésekkel:

    ![Grovo konfiguráció](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_saml.png)

    a. Az a **entitásazonosító** szövegmezőhöz illessze be az értékét **SAML Entitásazonosító** másolt Azure-portálon.

    b. Az a **egyszeri bejelentkezés a szolgáltatási végpont** szövegmezőhöz illessze be az értékét **SAML-alapú egyszeri bejelentkezési URL-címe** másolt Azure-portálon.

    c. Válassza ki **egyszeri bejelentkezés szolgáltatási végpont kötésének** , `urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect`.
    
    d. Nyissa meg a letöltött **Base64 kódolású tanúsítvány** Azure-portálon a Jegyzettömbben, illessze be azt a **nyilvános kulcs** szövegmező.

    e. Kattintson a **Tovább** gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-grovo-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-grovo-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/active-directory-saas-grovo-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-grovo-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
  
### <a name="create-a-grovo-test-user"></a>Grovo tesztfelhasználó létrehozása

Ez a szakasz célja Grovo Britta Simon nevű felhasználót létrehozni. Grovo támogatja just-in-time kiosztást, amely alapértelmezés szerint van engedélyezve. Nincs ebben a szakaszban az Ön művelet elem. Új felhasználó jön létre az Grovo elérésére, ha még nem létezik tett kísérlet során.
>[!Note]
>Ha a felhasználót manuálisan kell létrehozni, lépjen kapcsolatba kell [Grovo támogatási csoport](https://www.grovo.com/contact-us).

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Grovo Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése Grovo, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Grovo**.

    ![Az alkalmazások listáját a Grovo hivatkozás](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Grovo csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az Grovo alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-grovo-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-grovo-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-grovo-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-grovo-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-grovo-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-grovo-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-grovo-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-grovo-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-grovo-tutorial/tutorial_general_203.png


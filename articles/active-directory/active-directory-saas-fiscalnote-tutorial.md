---
title: 'Oktatóanyag: Azure Active Directoryval integrált FiscalNote |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és FiscalNote között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 55274f26-be7e-4514-964c-7186ecb55c4a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.openlocfilehash: 2e0d0f2d6fc428dbeb20ce5e056a8d6ed9a0ac97
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="tutorial-azure-active-directory-integration-with-fiscalnote"></a>Oktatóanyag: Azure Active Directoryval integrált FiscalNote

Ebben az oktatóanyagban elsajátíthatja FiscalNote integrálása az Azure Active Directory (Azure AD).

FiscalNote integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér FiscalNote szabályozhatja.
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett FiscalNote (egyszeri bejelentkezés) számára a saját Azure AD-fiókok.
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs FiscalNote, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy FiscalNote egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből FiscalNote hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-fiscalnote-from-the-gallery"></a>A gyűjteményből FiscalNote hozzáadása
Az Azure AD integrálása a FiscalNote konfigurálásához kell hozzáadnia FiscalNote a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből FiscalNote hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **FiscalNote**, jelölje be **FiscalNote** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában FiscalNote](./media/active-directory-saas-fiscalnote-tutorial/tutorial_fiscalnote_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján FiscalNote.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó FiscalNote a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a FiscalNote közötti kapcsolat kapcsolatot kell létrehozni.

Az Azure AD egyszeri bejelentkezést a FiscalNote tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[FiscalNote tesztfelhasználó létrehozása](#create-a-fiscalnote-test-user)**  - való Britta Simon valami FiscalNote, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az FiscalNote alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés FiscalNote, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **FiscalNote** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-fiscalnote-tutorial/tutorial_fiscalnote_samlbase.png)

3. Az a **FiscalNote tartomány és az URL-címek** területen tegye a következőket:

    ![Az egyszeri bejelentkezés információk FiscalNote tartomány és az URL-címek](./media/active-directory-saas-fiscalnote-tutorial/tutorial_fiscalnote_url.png)
    
    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://<InstanceName>.fiscalnote.com/login?client=<ClientID>&redirect_uri=https://app.fiscalnote.com/saml-login.html&audience=https://api.fiscalnote.com/&connection=<CONNECTION_NAME>&response_type=id_token%20token`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: `urn:auth0:fiscalnote:<CONNECTIONNAME>`

    > [!NOTE]
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Ügyfél [FiscalNote ügyfél-támogatási csoport](mailto:support@fiscalnote.com) beolvasni ezeket az értékeket.

4. Az a **SAML-aláíró tanúsítványa** kattintson **Certificate(Raw)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-fiscalnote-tutorial/tutorial_fiscalnote_certificate.png)

5. A FiscalNote alkalmazás a SAML helyességi feltételek egy meghatározott formátumban, amelyek megkövetelik olyan egyéni attribútum-leképezésekhez hozzáadása a SAML-jogkivonat attribútumok konfigurációs vár. A következő jogcímek alkalmazás konfigurálása. Ezek az attribútumok értékének kezelheti a "**felhasználói attribútumok**" szakasz alkalmazás integráció lapján.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-fiscalnote-tutorial/tutorial_attribute.png)

6. A a **felhasználói attribútumok** a szakasz a **egyszeri bejelentkezés** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, az előző ábrán látható módon, és hajtsa végre a következő lépéseket:
           
    | Attribútum neve | Attribútum értéke |
    | ---------------| ----------------|
    | név | User.userPrincipalName|
    | givenName| User.givenName|
    | familyName| User.surname|
    | e-mail| user.mail|
    
    a. Távolítsa el a meglévő attribútumok, és új attribútumokat. Kattintson a **Hozzáadás attribútum** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-fiscalnote-tutorial/tutorial_attribute_04.png)
    
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-fiscalnote-tutorial/tutorial_attribute_05.png)

    b. Az a **neve** szövegmező, írja be az adott sorhoz feltüntetett attribútumot nevét.

    c. Az a **érték** kilistázásához írja be a sorhoz látható attribútum értéke.

    d. Névtér hagyja üresen.
    
    e. Kattintson az **OK** gombra.

7. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-fiscalnote-tutorial/tutorial_general_400.png)

8. A a **FiscalNote konfigurációs** kattintson **konfigurálása FiscalNote** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-címet, a SAML entitás azonosítója és a SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![FiscalNote konfiguráció](./media/active-directory-saas-fiscalnote-tutorial/tutorial_fiscalnote_configure.png) 

9. Egyszeri bejelentkezés konfigurálása **FiscalNote** oldalon kell küldeniük a letöltött **Certificate(Raw), Sign-Out URL-címe, SAML Entitásazonosító és SAML-alapú egyszeri bejelentkezési URL-címe** való [FiscalNote támogatási csoport](mailto:support@fiscalnote.com). Akkor állítsa be ezt a beállítást, hogy a SAML SSO kapcsolat mindkét oldalán megfelelően beállítva.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-fiscalnote-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-fiscalnote-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/active-directory-saas-fiscalnote-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-fiscalnote-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-fiscalnote-test-user"></a>FiscalNote tesztfelhasználó létrehozása

Ez a szakasz célja FiscalNote Britta Simon nevű felhasználót létrehozni. FiscalNote támogatja just-in-time kiosztást, amely alapértelmezés szerint van engedélyezve. Nincs ebben a szakaszban az Ön művelet elem. Új felhasználó jön létre az FiscalNote elérésére, ha még nem létezik tett kísérlet során.
>[!Note]
>Ha manuálisan hozzon létre egy felhasználó van szüksége, forduljon a [FiscalNote támogatási csoport](mailto:support@fiscalnote.com).

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés FiscalNote Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése FiscalNote, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **FiscalNote**.

    ![Az alkalmazások listáját a FiscalNote hivatkozás](./media/active-directory-saas-fiscalnote-tutorial/tutorial_fiscalnote_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen FiscalNote csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az FiscalNote alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-fiscalnote-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-fiscalnote-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-fiscalnote-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-fiscalnote-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-fiscalnote-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-fiscalnote-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-fiscalnote-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-fiscalnote-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-fiscalnote-tutorial/tutorial_general_203.png


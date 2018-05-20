---
title: 'Oktatóanyag: Azure Active Directoryval integrált SignalFx |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és SignalFx között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 6d5ab4b0-29bc-4b20-8536-d64db7530f32
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.openlocfilehash: 67e9bb69e1e8741e4a4ff7e7b78cb06856d2450f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-signalfx"></a>Oktatóanyag: Azure Active Directoryval integrált SignalFx

Ebben az oktatóanyagban elsajátíthatja SignalFx integrálása az Azure Active Directory (Azure AD).

SignalFx integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér SignalFx szabályozhatja.
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett SignalFx (egyszeri bejelentkezés) számára a saját Azure AD-fiókok.
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs SignalFx, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy SignalFx egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből SignalFx hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-signalfx-from-the-gallery"></a>A gyűjteményből SignalFx hozzáadása
Az Azure AD integrálása a SignalFx konfigurálásához kell hozzáadnia SignalFx a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből SignalFx hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **SignalFx**, jelölje be **SignalFx** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában SignalFx](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján SignalFx.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó SignalFx a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a SignalFx közötti kapcsolat kapcsolatot kell létrehozni.

Az Azure AD egyszeri bejelentkezést a SignalFx tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[SignalFx tesztfelhasználó létrehozása](#create-a-signalfx-test-user)**  - való Britta Simon valami SignalFx, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az SignalFx alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés SignalFx, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **SignalFx** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_samlbase.png)

3. Az a **SignalFx tartomány és az URL-címek** területen tegye a következőket:

    ![Az egyszeri bejelentkezés információk SignalFx tartomány és az URL-címek](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_url.png)

    a. Az a **azonosító** szövegmezőhöz URL-címet írja be: `https://api.signalfx.com/v1/saml/metadata`

    b. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe: `https://api.signalfx.com/v1/saml/acs/<integration ID>`

    > [!NOTE] 
    > Az előző érték nincs valós értékének. A tényleges válasz URL-címet, az oktatóanyag későbbi részében ismertetett frissítse az értéket.

4. SignalFx alkalmazás vár a SAML helyességi feltételek egy meghatározott formátumban. A következő jogcímek alkalmazás konfigurálása. Ezek az attribútumok értékének kezelheti a **felhasználói attribútumok** szakasz alkalmazás integráció lapján. Az alábbi képernyőfelvételen látható egy példa a.   

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_attribute.png)

5. A a **felhasználói attribútumok** a szakasz a **egyszeri bejelentkezés** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, az ábrán látható módon, és hajtsa végre a következő lépéseket:
    
    | Attribútum neve | Attribútum értéke |
    | ------------------- | -------------------- |    
    | User.FirstName          | User.givenName |
    | User.email          | user.mail |
    | PersonImmutableID       | User.userPrincipalName    |
    | User.LastName       | User.surname    |

    a. Kattintson a **Hozzáadás attribútum** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása hozzáadása](./media/active-directory-saas-signalfx-tutorial/tutorial_attribute_04.png)

    ![Egyszeri bejelentkezés Addattb konfigurálása](./media/active-directory-saas-signalfx-tutorial/tutorial_attribute_05.png)

    b. Az a **neve** szövegmező, írja be az adott sorhoz feltüntetett attribútumot nevét.

    c. Az a **érték** kilistázásához írja be a sorhoz látható attribútum értéke.

    d. Hagyja a **Namespace** üres.
    
    e. Kattintson az **OK** gombra.
 
6. Az a **SAML-aláíró tanúsítványa** területen tegye a következőket: 

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_certificate.png)

    a. Kattintson a Másolás gombra másolása **alkalmazás összevonási metaadatainak URL-címe** és illessze be a Jegyzettömbbe.

    b. Kattintson a **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

7. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-signalfx-tutorial/tutorial_general_400.png)

8. A a **SignalFx konfigurációs** kattintson **konfigurálása SignalFx** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító** a a **rövid összefoglaló szakasz.**

    ![SignalFx konfiguráció](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_configure.png) 

9. Bejelentkezés a SignalFx vállalati webhely rendszergazdaként.

10. A SignalFx, kattintson a felső a **integrációja** Integrációk lapjának megnyitásához.

    ![SignalFx integráció](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_intg.png)

11. Kattintson a **Azure Active Directory** a csempén **bejelentkezési szolgáltatások** szakasz.
 
    ![SignalFx saml](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_saml.png)

12. Kattintson a **új integrációs** alatt pedig a **telepítése** lapon hajtsa végre a következő lépéseket:
 
    ![SignalFx samlintgpage](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_azure.png)

    a. Az a **neve** szövegmező típus, egy új integrációs nevet, például **OurOrgName SAML SSO**.

    b. Másolás a **integrációs azonosító** értékét, és a hozzáfűzni kívánt táblát a **válasz URL-CÍMEN** például `https://api.signalfx.com/v1/saml/acs/<integration ID>` a a **válasz URL-CÍMEN** a szövegmező **SignalFx tartomány és az URL-címek** szakaszban az Azure portálon.

    c. Kattintson a **fájl feltöltése** feltölteni a **Base64 kódolású tanúsítvány** az Azure portálról letöltött a **tanúsítvány** szövegmező.

    d. Az a **kiállítójának URL-címe** szövegmezőhöz illessze be az értékét **SAML Entitásazonosító**, amely az Azure portálról másolta.

    e. Az a **metaadatainak URL-CÍMÉT** szövegmező, illessze be a **alkalmazás összevonási metaadatainak URL-címe** , amely az Azure portálról másolta.

    f. Kattintson a **Save** (Mentés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-signalfx-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-signalfx-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/active-directory-saas-signalfx-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-signalfx-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
  
### <a name="create-a-signalfx-test-user"></a>SignalFx tesztfelhasználó létrehozása

Ez a szakasz célja SignalFx Britta Simon nevű felhasználót létrehozni. SignalFx támogatja just-in-time kiosztást, amely alapértelmezés szerint van engedélyezve. Nincs ebben a szakaszban az Ön művelet elem. Új felhasználó jön létre az SignalFx elérésére, ha még nem létezik tett kísérlet során.

Amikor egy felhasználó bejelentkezik SignalFx a SAML SSO az első alkalommal [SignalFx támogatási csoport](mailto:kmazzola@signalfx.com) őket, hogy azok átkattintással kell hitelesíteni hivatkozást tartalmazó e-mailt küld. Ez csak akkor történik, az első alkalommal a felhasználó bejelentkezésekor; további bejelentkezési kísérletek e-mail érvényesítési nem szükséges.

>[!Note]
>Ha manuálisan hozzon létre egy felhasználó van szüksége, forduljon a [SignalFx támogatási csoport](mailto:kmazzola@signalfx.com)

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés SignalFx Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése SignalFx, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **SignalFx**.

    ![Az alkalmazások listáját a SignalFx hivatkozás](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen SignalFx csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az SignalFx alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_203.png


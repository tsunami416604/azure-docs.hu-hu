---
title: 'Oktatóanyag: Azure Active Directoryval integrált Fluxx Labs |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Fluxx Labs között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d8fac770-bb57-4e1f-b50b-9ffeae239d07
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2018
ms.author: jeedes
ms.openlocfilehash: 2d22720e71788493d3663524f2b70783ba26b84d
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36218165"
---
# <a name="tutorial-azure-active-directory-integration-with-fluxx-labs"></a>Oktatóanyag: Azure Active Directoryval integrált Fluxx Labs

Ebben az oktatóanyagban elsajátíthatja Fluxx Labs integrálása az Azure Active Directory (Azure AD).

Fluxx Labs integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér Fluxx Labs szabályozhatja.
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett Fluxx laborokhoz (egyszeri bejelentkezés).
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs Fluxx Labs, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Fluxx Labs egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Fluxx Labs hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-fluxx-labs-from-the-gallery"></a>A gyűjteményből Fluxx Labs hozzáadása
Az Azure AD integrálása a Fluxx Labs konfigurálásához kell hozzáadnia Fluxx Labs a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Fluxx Labs hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **Fluxx Labs**, jelölje be **Fluxx Labs** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában Fluxx Labs szolgáltatásának ismertetése](./media/fluxxlabs-tutorial/tutorial_fluxxlabs_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD az egyszeri bejelentkezés Fluxx Labs "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Fluxx laborokban a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Fluxx Fejlesztőlaborokban lévő hivatkozás kapcsolatának kell létrehozni.

Fluxx laborokban, rendelje az értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a Fluxx Labs tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Fluxx Labs tesztfelhasználó létrehozása](#create-a-fluxx-labs-test-user)**  - való egy megfelelője a Britta Simon Fluxx Labs, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Fluxx Labs alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés Fluxx Labs, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Fluxx Labs** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/fluxxlabs-tutorial/tutorial_fluxxlabs_samlbase.png)

3. Az a **Fluxx Labs tartomány és az URL-címek** területen tegye a következőket:

    ![Az egyszeri bejelentkezés információk Fluxx Labs tartomány és az URL-címek](./media/fluxxlabs-tutorial/tutorial_fluxxlabs_url.png)

    a. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe:

    | Környezet | Az URL-minta|
    |-------------|------------|
    | Production | `https://<subdomain>.fluxx.io` |
    | Üzem előtti | `https://<subdomain>.preprod.fluxxlabs.com`|
        
    b. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe:

    | Környezet | Az URL-minta|
    |-------------|------------|
    | Production | `https://<subdomain>.fluxx.io/auth/saml/callback` |
    | Üzem előtti | `https://<subdomain>.preprod.fluxxlabs.com/auth/saml/callback`|
        
    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges azonosítója és a válasz URL-CÍMEN. Ügyfél [Fluxx Labs támogatási csoport](mailto:travis@fluxxlabs.com) beolvasni ezeket az értékeket.

4. A a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/fluxxlabs-tutorial/tutorial_fluxxlabs_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/fluxxlabs-tutorial/tutorial_general_400.png)

6. A a **Fluxx Labs konfigurációs** kattintson **konfigurálása Fluxx Labs** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító és SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Fluxx Labs konfiguráció](./media/fluxxlabs-tutorial/tutorial_fluxxlabs_configure.png)

7. Egy másik webes böngészőablakban jelentkezzen be a Fluxx Labs vállalati webhely rendszergazdaként.

8. Válassza ki **Admin** alatt a **beállítások** szakasz.

    ![Fluxx Labs konfiguráció](./media/fluxxlabs-tutorial/config1.png)

9. A rendszergazda panelen válassza ki a **beépülő modulok** > **integrációja** , és válassza **SAML SSO-(Disabled)**

    ![Fluxx Labs konfiguráció](./media/fluxxlabs-tutorial/config2.png)
    
10. Az attribútum a szakaszban a következő lépésekkel:
    
    ![Fluxx Labs konfiguráció](./media/fluxxlabs-tutorial/config3.png)

    a. Válassza ki a **SAML SSO** jelölőnégyzetet.

    b. Az a **kérelem elérési útja** szövegmezőhöz típus **/auth/saml**.

    c. Az a **visszahívási elérési** szövegmezőhöz típus **/auth/saml/callback**.

    d. Az a **helyességi feltétel fogyasztói szolgáltatás Url(Single Sign-On URL)** szövegmezőhöz illessze be az értékét **SAML-alapú egyszeri bejelentkezési URL-címe**, amely az Azure portálról másolta.

    e. Az a **célközönség (SP entitás azonosítója)** szövegmezőhöz illessze be az értékét **SAML Entitásazonosító**, amely az Azure portálról másolta.

    f. Nyissa meg a base-64 kódolású tanúsítvány a Jegyzettömbben, a tartalmának másolása a vágólapra és illessze be azt a **szolgáltató Identitástanúsítvány** szövegmező.

    g. A **névazonosítója formátum** szövegmező, írja be az értéket `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`.

    h. Kattintson a **Save** (Mentés) gombra.

    > [!NOTE]
    > Egyszer a tartalom mentése, a mező üres lesz a biztonsági, de az érték mentette a konfigurációban.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/fluxxlabs-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/fluxxlabs-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/fluxxlabs-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/fluxxlabs-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
  
### <a name="create-a-fluxx-labs-test-user"></a>Fluxx Labs tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Fluxx Labs bejelentkezni, akkor ki kell építenie Fluxx Labs be. Fluxx Labs, ha egy kézi tevékenység.

**Felhasználói fiók létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a Fluxx Labs vállalati webhely.

2. Kattintson a lent látható **ikon**.

    ![Fluxx Labs konfiguráció](./media/fluxxlabs-tutorial/config6.png)

3. Az irányítópulton kattintson a az alább megjelenő ikonra kattintva nyissa meg a **új személyek** kártya.

    ![Fluxx Labs konfiguráció](./media/fluxxlabs-tutorial/config4.png)

4. Az a **új személyek** területen tegye a következőket:
    
    ![Fluxx Labs konfiguráció](./media/fluxxlabs-tutorial/config5.png)

    a. Fluxx Labs e-mail cím használata Egyszeri bejelentkezéshez az egyedi azonosítóként. Feltöltése a **SSO UID** mezőt a, amely megfelel az e-mail címével, amely az egyszeri bejelentkezési modellel bejelentkezésként használják a felhasználó e-mail címét.

    b. Kattintson a **Save** (Mentés) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Fluxx Labs Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése Fluxx Labs, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Fluxx Labs**.

    ![Az alkalmazások listáját a Fluxx Labs hivatkozás](./media/fluxxlabs-tutorial/tutorial_fluxxlabs_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Fluxx Labs csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az Fluxx Labs alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/fluxxlabs-tutorial/tutorial_general_01.png
[2]: ./media/fluxxlabs-tutorial/tutorial_general_02.png
[3]: ./media/fluxxlabs-tutorial/tutorial_general_03.png
[4]: ./media/fluxxlabs-tutorial/tutorial_general_04.png

[100]: ./media/fluxxlabs-tutorial/tutorial_general_100.png

[200]: ./media/fluxxlabs-tutorial/tutorial_general_200.png
[201]: ./media/fluxxlabs-tutorial/tutorial_general_201.png
[202]: ./media/fluxxlabs-tutorial/tutorial_general_202.png
[203]: ./media/fluxxlabs-tutorial/tutorial_general_203.png

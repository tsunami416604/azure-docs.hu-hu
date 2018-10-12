---
title: 'Oktatóanyag: Azure Active Directory-integráció az Appraisd |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Appraisd között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: db063306-4d0d-43ca-aae0-09f0426e7429
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2018
ms.author: jeedes
ms.openlocfilehash: dae9a59b89f03a50b1adaed55cd8f97c06906526
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2018
ms.locfileid: "49118573"
---
# <a name="tutorial-azure-active-directory-integration-with-appraisd"></a>Oktatóanyag: Azure Active Directory-integráció az Appraisd

Ebben az oktatóanyagban elsajátíthatja, hogyan Appraisd integrálása az Azure Active Directory (Azure AD).

Appraisd integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá Appraisd Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Appraisd (egyszeri bejelentkezés), az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Appraisd az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy Appraisd egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Appraisd hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-appraisd-from-the-gallery"></a>Appraisd hozzáadása a katalógusból
Az Azure AD integrálása a Appraisd konfigurálásához hozzá kell Appraisd a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Appraisd hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![image](./media/appraisd-tutorial/selectazuread.png)

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![image](./media/appraisd-tutorial/a_select_app.png)
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![image](./media/appraisd-tutorial/a_new_app.png)

4. A Keresés mezőbe írja be a **Appraisd**válassza **Appraisd** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![image](./media/appraisd-tutorial/tutorial_appraisd_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Appraisd a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Appraisd mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Appraisd hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az Appraisd tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Hozzon létre egy Appraisd tesztfelhasználót](#create-an-appraisd-test-user)**  – egy megfelelője a Britta Simon Appraisd, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Appraisd alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Appraisd, hajtsa végre az alábbi lépéseket:**

1. Az a [az Azure portal](https://portal.azure.com/), az a **Appraisd** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![image](./media/appraisd-tutorial/B1_B2_Select_SSO.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML** módot az egyszeri bejelentkezés engedélyezése.

    ![image](./media/appraisd-tutorial/b1_b2_saml_sso.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** gombra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![image](./media/appraisd-tutorial/b1-domains_and_urlsedit.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, a következő lépésekkel, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_url.png)

    a. Kattintson a **további URL-címet beállítani**. 

    b. Az a **továbbítási állapot** szövegmezőbe írja be egy URL-címe: `<TENANTCODE>`

    c. Ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód, a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím: `https://app.appraisd.com/saml/<TENANTCODE>`

    > [!NOTE]
    > A tényleges bejelentkezési URL- és a továbbítási állapot értéke a Appraisd egyszeri bejelentkezési konfiguráció lapon, az oktatóanyag későbbi részében ismertetett kap.
    
5. Appraisd alkalmazás a SAML helyességi feltételek vár egy megadott formátumban. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a **felhasználói attribútumok** szakasz alkalmazás integráció lapján. Kattintson a **szerkesztése** gombra kattintva nyissa meg a **felhasználói attribútumok** párbeszédpanel.

    ![image](./media/appraisd-tutorial/i3-attribute.png)

6. Az a **felhasználói jogcímek** szakaszában a **felhasználói attribútumok** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, a fenti képen látható módon, és hajtsa végre az alábbi lépéseket:
    
    a. Kattintson a **szerkesztése** gombra kattintva nyissa meg a **kezelheti a felhasználói jogcímek** párbeszédpanel.

    ![image](./media/appraisd-tutorial/i2-attribute.png)

    ![image](./media/appraisd-tutorial/i4-attribute.png)

    b. Az a **forrásattribútum** listájához, válassza ki az attribútum értéke.

    c. Kattintson a **Save** (Mentés) gombra. 

7. Az a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** , és mentse a számítógépre.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_certficate.png) 

8. Az a **Appraisd beállítása** területén másolja a megfelelő URL-címet a követelmény alapján.

    a. Bejelentkezési URL

    b. Az Azure Ad-azonosító

    c. Kijelentkezési URL

    ![image](./media/appraisd-tutorial/d1_samlsonfigure.png)

9. Egy másik böngészőablakban jelentkezzen be Appraisd egy biztonsági-rendszergazdaként.

10. A felső, az oldal jobb kattintson a **beállítások** ikonra, majd keresse meg a **konfigurációs**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_sett.png)

11. A menüt a bal oldali menüjében kattintson a **SAML egyszeri bejelentkezés**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_single.png)

12. Az a **SAML 2.0 egyszeri bejelentkezés konfigurációja** lapon, a következő lépésekkel:
    
    ![image](./media/appraisd-tutorial/tutorial_appraisd_saml.png)

    a. Másolás a **alapértelmezett továbbítási állapot** értékét, és illessze be a **továbbítási állapot** szövegmezőjébe **alapszintű SAML-konfigurációja** az Azure Portalon.

    b. Másolás a **szolgáltatás által kezdeményezett bejelentkezési URL-cím** értékét, és illessze be a **bejelentkezési URL-** szövegmezőjébe **alapszintű SAML-konfigurációja** az Azure Portalon.

13. Görgessen lefelé az oldalon, amelyek ugyanazt a **felhasználók azonosítása**, hajtsa végre az alábbi lépéseket:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_identifying.png)

    a. Az a **Identity Provider egyszeri bejelentkezési URL-** szövegmezőbe, illessze be az értéket a **bejelentkezési URL-cím**, az Azure Portalon, majd kattintson a másolt **mentése**.

    b. Az a **Identity Provider kiállítójának URL-címe** szövegmezőbe, illessze be az értéket a **Azure Ad-azonosító**, az Azure Portalon, majd kattintson a másolt **mentése**.

    c. A Jegyzettömbben, nyissa meg az Azure Portalról letöltött base-64 kódolású tanúsítványt, másolja a tartalmat, és illessze be azt a **X.509-tanúsítvány** mezőbe, majd kattintson a **mentése**.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![image](./media/appraisd-tutorial/d_users_and_groups.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![image](./media/appraisd-tutorial/d_adduser.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![image](./media/appraisd-tutorial/d_userproperties.png)

    a. Az a **neve** írja be a következőt **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőtípus **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **tulajdonságok**, jelölje be a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Létrehozás** gombra.
 
### <a name="create-an-appraisd-test-user"></a>Hozzon létre egy Appraisd tesztfelhasználó számára

Ahhoz, hogy az Azure AD felhasználói bejelentkeznek a Appraisd, akkor ki kell építeni Appraisd be. Appraisd a kiépítés manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be egy biztonsági-rendszergazdaként Appraisd.

2. A felül, jobb a lapon kattintson a **beállítások** ikonra, majd keresse meg a **felügyeleti központ**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_admin.png)

3. Kattintson az eszköztáron az oldal tetején lévő **személyek**, majd keresse meg a **új felhasználó hozzáadása**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_user.png)

4. Az a **új felhasználó hozzáadása** lapon, a következő lépésekkel:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_newuser.png)
    
    a. A **Utónév** szöveget adja meg például a felhasználó utónevét **Britta**.

    b. A **Vezetéknév** szöveget adja meg például a felhasználó vezetékneve **simon**.

    c. A **E-mail** szöveget adja meg az e-mailt, például a felhasználó **Brittasimon@contoso.com**.

    d. Kattintson a **Felhasználó hozzáadása** parancsra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Appraisd Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**válassza **minden alkalmazás**.

    ![image](./media/appraisd-tutorial/d_all_applications.png)

2. Az alkalmazások listájában jelölje ki a **Appraisd**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_app.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![image](./media/appraisd-tutorial/d_leftpaneusers.png)

4. Válassza ki a **Hozzáadás** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![image](./media/appraisd-tutorial/d_assign_user.png)

4. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

5. Az a **hozzárendelés hozzáadása** párbeszédpanelen válassza ki a **hozzárendelése** gombra.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Appraisd csempére kattint, meg kell lekérése automatikusan bejelentkezett az Appraisd alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

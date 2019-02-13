---
title: 'Oktatóanyag: Az Azure Active Directory-integráció Workspot vezérlővel |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Workspot vezérlő között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3ea8e4e9-f61f-4f45-b635-b0e306eda3d1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8716d018756d1e6eadcd6ebeeaf4f67ad0bc4741
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56211155"
---
# <a name="tutorial-azure-active-directory-integration-with-workspot-control"></a>Oktatóanyag: Az Azure Active Directory-integráció Workspot vezérlővel

Ebben az oktatóanyagban elsajátíthatja, hogyan Workspot vezérlés integrálása az Azure Active Directory (Azure AD).

Workspot vezérlés integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá Workspot vezérlő Azure AD-ben.
- Engedélyezheti a felhasználók automatikusan első bejelentkezett (egyszeri bejelentkezés) Workspot szabályozhatja az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása Workspot vezérlővel, a következőkre van szükség:

- Azure AD-előfizetés
- Egy Workspot vezérlő egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Workspot vezérlőelem hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-workspot-control-from-the-gallery"></a>Workspot vezérlőelem hozzáadása a katalógusból
Az Azure AD-be Workspot vezérlő integráció konfigurálásához, kell Workspot vezérlő hozzáadása a felügyelt SaaS-alkalmazások listájában a katalógusból.

**Workspot vezérlő hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![image](./media/workspotcontrol-tutorial/selectazuread.png)

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![image](./media/workspotcontrol-tutorial/a_select_app.png)
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![image](./media/workspotcontrol-tutorial/a_new_app.png)

4. A Keresés mezőbe írja be a **Workspot vezérlő**, jelölje be **Workspot vezérlő** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban konfigurálása, és az Azure AD egyszeri bejelentkezés tesztelése Workspot vezérlővel "Britta Simon" nevű tesztfelhasználó alapján.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner Workspot felügyelő felhasználó mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Workspot vezérlőelem hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés Workspot vezérlővel tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Hozzon létre egy Workspot vezérlő tesztfelhasználót](#create-a-workspot-control-test-user)**  – szeretné, hogy egy megfelelője a Britta Simon Workspot vezérlőben, amely kapcsolódik az Azure AD felhasználói ábrázolása.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Workspot vezérlés alkalmazását az egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálása Workspot vezérlővel, hajtsa végre az alábbi lépéseket:**

1. Az a [az Azure portal](https://portal.azure.com/), a a **Workspot vezérlő** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![image](./media/workspotcontrol-tutorial/B1_B2_Select_SSO.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML** módot az egyszeri bejelentkezés engedélyezése.

    ![image](./media/workspotcontrol-tutorial/b1_b2_saml_sso.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** gombra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![image](./media/workspotcontrol-tutorial/b1-domains_and_urlsedit.png)

4. Az a **alapszintű SAML-konfigurációja** területén kövesse az alábbi lépéseket, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** intiated mód:

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_url.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<INSTANCENAME>-saml.workspot.com/saml/metadata`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<INSTANCENAME>-saml.workspot.com/saml/assertion`

    c. Kattintson a **további URL-címet beállítani** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

     ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_url1.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím:  `https://<INSTANCENAME>-saml.workspot.com/`

    > [!NOTE]
    > Ezek a értékei nem valódi. Az értékeket módosítsa a tényleges azonosítóját, válasz URL-cím és bejelentkezési URL-címet. Kapcsolattartó [Workspot vezérlő ügyfél-támogatási csapatának](mailto:support@workspot.com) beolvasni ezeket az értékeket. 

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** , és mentse a számítógépre.

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_certficate.png) 

6. Az a **Workspot felügyeletét** területén másolja a megfelelő URL-címet a követelmény alapján.

    Vegye figyelembe, hogy az URL-cím lehet, hogy tegyük fel, hogy a következő:

    a. Bejelentkezési URL

    b. Azure Ad Identifier

    c. Kijelentkezési URL

    ![image](./media/workspotcontrol-tutorial/d1_samlsonfigure.png) 

7. Egy másik böngészőablakban, bejelentkezési Workspot szabályozhatja a biztonság-rendszergazdájaként.

8. Kattintson az eszköztáron az oldal tetején lévő **telepítő**, majd keresse meg a **SAML**.

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_setup.png)

9. Az a **Security Assertion Markup nyelvi beállításait** lapon, a következő lépésekkel:
 
    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_saml.png)

    a. A a **Entitásazonosító** szövegmezőjébe illessze be az értéket, **Azure Ad-azonosító** az Azure Portalról másolt.   

    b.In a **URL-CÍMÉT nehézség** szövegmező, illessze be az értéket a **bejelentkezési URL-cím** az Azure Portalról másolt.

    c. Az a **kijelentkezési szolgáltatás URL-címe** szövegmezőjébe illessze be az értéket a **kijelentkezési URL-címe** az Azure Portalról másolt. 

    d. Kattintson a **frissítésfájl** gombra kattintva töltse fel a base-64 kódolású X.509-tanúsítvány, az Azure Portalról letöltött tanúsítvány.

    e. Kattintson a **Save** (Mentés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![image](./media/workspotcontrol-tutorial/d_users_and_groups.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![image](./media/workspotcontrol-tutorial/d_adduser.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![image](./media/workspotcontrol-tutorial/d_userproperties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőtípus **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **tulajdonságok**, jelölje be a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Létrehozás** gombra.
 
### <a name="create-a-workspot-control-test-user"></a>Workspot vezérlő tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Workspot vezérlő jelentkezzen be, akkor ki kell építeni Workspot vezérlőelemben. Workspot vezérlőben kiépítése a manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Workspot vezérlőelem egy biztonsági-rendszergazdaként jelentkezzen be.

2. Kattintson az eszköztáron az oldal tetején lévő **felhasználók**, majd keresse meg a **felhasználó hozzáadása**.

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_adduser.png)

3. Az a **új felhasználó hozzáadása** lapon, a következő lépésekkel:

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_addnewuser.png)

    a. A **Utónév** szöveget adja meg például a felhasználó utónevét **Britta**.

    b. A **Vezetéknév** szöveget adja meg például a felhasználó vezetékneve **simon**.

    c. A **E-mail** szöveget adja meg az e-mailt, például a felhasználó **Brittasimon@contoso.com**.

    d. Válassza ki a megfelelő felhasználói szerepkört a **szerepkör** legördülő listából.

    e. Válassza ki a megfelelő felhasználói csoportot a **csoport** legördülő listából.

    f. Kattintson a **felhasználó hozzáadása**.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon Workspot szabályozható a hozzáférés biztosításával az Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**válassza **minden alkalmazás**.

    ![image](./media/workspotcontrol-tutorial/d_all_applications.png)

2. Az alkalmazások listájában jelölje ki a **Workspot vezérlő**.

    ![image](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_app.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![image](./media/workspotcontrol-tutorial/d_leftpaneusers.png)

4. Válassza ki a **Hozzáadás** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![image](./media/workspotcontrol-tutorial/d_assign_user.png)

4. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

5. Az a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelése** gombra.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Workspot vezérlő csempére kattint, akkor kell lekérése automatikusan bejelentkezett Workspot vezérlés alkalmazását az.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

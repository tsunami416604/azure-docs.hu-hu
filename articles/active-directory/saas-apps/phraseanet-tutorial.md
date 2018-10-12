---
title: 'Oktatóanyag: Azure Active Directory-integráció az Phraseanet |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Phraseanet között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 078d84ce-e054-4ae1-a52e-46a94a6959a7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/09/2018
ms.author: jeedes
ms.openlocfilehash: cfeadbf1d46d9e36f8619cafe29d9dd69aad6eec
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2018
ms.locfileid: "49118570"
---
# <a name="tutorial-azure-active-directory-integration-with-phraseanet"></a>Oktatóanyag: Azure Active Directory-integráció az Phraseanet

Ebben az oktatóanyagban elsajátíthatja, hogyan Phraseanet integrálása az Azure Active Directory (Azure AD).

Phraseanet integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá Phraseanet Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Phraseanet (egyszeri bejelentkezés), az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Phraseanet az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy Phraseanet egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Phraseanet hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-phraseanet-from-the-gallery"></a>Phraseanet hozzáadása a katalógusból
Az Azure AD integrálása a Phraseanet konfigurálásához hozzá kell Phraseanet a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Phraseanet hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![image](./media/phraseanet-tutorial/selectazuread.png)

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![image](./media/phraseanet-tutorial/a_select_app.png)
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![image](./media/phraseanet-tutorial/a_new_app.png)

4. A Keresés mezőbe írja be a **Phraseanet**válassza **Phraseanet** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![image](./media/phraseanet-tutorial/tutorial_phraseanet_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Phraseanet a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Phraseanet mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Phraseanet hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az Phraseanet tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Hozzon létre egy Phraseanet tesztfelhasználót](#create-a-phraseanet-test-user)**  – egy megfelelője a Britta Simon Phraseanet, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Phraseanet alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Phraseanet, hajtsa végre az alábbi lépéseket:**

1. Az a [az Azure portal](https://portal.azure.com/), az a **Phraseanet** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![image](./media/phraseanet-tutorial/b1_b2_select_sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen kattintson **kiválasztása** a **SAML** módot az egyszeri bejelentkezés engedélyezése.

    ![image](./media/phraseanet-tutorial/b1_b2_saml_sso.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** gombra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![image](./media/phraseanet-tutorial/b1-domains_and_urlsedit.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    Az a **bejelentkezési URL-** szövegmezőbe írjon be egy URL-cím:  `https://<SUBDOMAIN>.alchemyasp.com`

    ![image](./media/phraseanet-tutorial/tutorial_phraseanet_url.png)

    > [!NOTE] 
    > A bejelentkezési URL-érték nem valódi. Frissítse az értéket a tényleges bejelentkezési URL-CÍMÉT. Kapcsolattartó [Phraseanet támogatási csapatának](mailto:support@alchemy.fr) a gépkulcsengedélyek értékének.
 
5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **összevonási metaadatainak XML**  , és mentse a számítógépre.

    ![image](./media/phraseanet-tutorial/tutorial_phraseanet_certificate.png) 

6. Az egyszeri bejelentkezés konfigurálása **Phraseanet** oldalon kell küldenie a letöltött **összevonási metaadatainak XML** való [Phraseanet támogatási csapatának](mailto:support@alchemy.fr). Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![image](./media/phraseanet-tutorial/d_users_and_groups.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![image](./media/phraseanet-tutorial/d_adduser.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![image](./media/phraseanet-tutorial/d_userproperties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőtípus **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **tulajdonságok**, jelölje be a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Létrehozás** gombra.
 
### <a name="create-a-phraseanet-test-user"></a>Phraseanet tesztfelhasználó létrehozása

Ebben a szakaszban egy felhasználói Britta Simon nevű Phraseanet hoz létre. Együttműködve [Phraseanet támogatási csapatának](mailto:support@alchemy.fr) a felhasználók hozzáadása az Phraseanet platformon. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Phraseanet Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**válassza **minden alkalmazás**.

    ![image](./media/phraseanet-tutorial/d_all_applications.png)

2. Az alkalmazások listájában jelölje ki a **Phraseanet**.

    ![image](./media/phraseanet-tutorial/tutorial_phraseanet_app.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![image](./media/phraseanet-tutorial/d_leftpaneusers.png)

4. Válassza ki a **Hozzáadás** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![image](./media/phraseanet-tutorial/d_assign_user.png)

4. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

5. Az a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelése** gombra.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Phraseanet csempére kattint, meg kell lekérése automatikusan bejelentkezett az Phraseanet alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



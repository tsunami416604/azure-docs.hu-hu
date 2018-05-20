---
title: 'Oktatóanyag: Azure Active Directoryval integrált Veritas vállalati Vault.cloud SSO |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a Veritas vállalati Vault.cloud SSO között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2017
ms.author: jeedes
ms.openlocfilehash: a60df7f743c893305f0b3b4aba05505e761f505d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-veritas-enterprise-vaultcloud-sso"></a>Oktatóanyag: Azure Active Directoryval integrált Veritas vállalati Vault.cloud egyszeri bejelentkezés

Ebben az oktatóanyagban elsajátíthatja Veritas vállalati Vault.cloud SSO integrálása az Azure Active Directory (Azure AD).

Veritas vállalati Vault.cloud SSO integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a Veritas vállalati Vault.cloud SSO hozzáféréssel rendelkező Azure AD-ben
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni aláírt a Veritas vállalati Vault.cloud SSO (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integrációs konfigurálásához Veritas vállalati Vault.cloud egyszeri bejelentkezési modellel, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Veritas vállalati Vault.cloud SSO egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Veritas vállalati Vault.cloud SSO hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-veritas-enterprise-vaultcloud-sso-from-the-gallery"></a>A gyűjteményből Veritas vállalati Vault.cloud SSO hozzáadása
Az Azure AD integrálása a Veritas vállalati Vault.cloud SSO konfigurálásához kell hozzáadnia Veritas vállalati Vault.cloud egyszeri Bejelentkezést a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Veritas vállalati Vault.cloud SSO hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **Veritas vállalati Vault.cloud SSO**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-veritas-tutorial/tutorial_veritas_search.png)

5. Az eredmények panelen válassza ki a **Veritas vállalati Vault.cloud SSO**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-veritas-tutorial/tutorial_veritas_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD az egyszeri bejelentkezés Veritas vállalati Vault.cloud SSO "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Veritas vállalati Vault.cloud egyszeri Bejelentkezést a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Veritas vállalati Vault.cloud SSO közötti kapcsolat kapcsolatot kell létrehozni.

Veritas vállalati Vault.cloud SSO, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD az egyszeri bejelentkezés Veritas vállalati Vault.cloud egyszeri bejelentkezési modellel tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Veritas vállalati Vault.cloud SSO tesztfelhasználó létrehozása](#creating-a-veritas-enterprise-vaultcloud-sso-test-user)**  - való egy megfelelője a Britta Simon Veritas vállalati Vault.cloud egyszeri Bejelentkezést, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Veritas vállalati Vault.cloud SSO-alkalmazásban.

**Konfigurálja az Azure AD az egyszeri bejelentkezés Veritas vállalati Vault.cloud egyszeri bejelentkezési modellel, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Veritas vállalati Vault.cloud SSO** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-veritas-tutorial/tutorial_veritas_samlbase.png)

3. Az a **Veritas vállalati Vault.cloud SSO tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-veritas-tutorial/tutorial_veritas_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://personal.ap.archive.veritas.com/CID=<CUSTOMERID>`

    b. Az a **azonosító** szövegmezőhöz az Adatközponton adott az URL-cím használata

    | Adatközpont| URL-cím |
    |----------|----|
    | Észak-Amerika| `https://auth.lax.archivecloud.net` |
    | Európa | `https://auth.ams.archivecloud.net` |
    | Ázsia és a Csendes-óceáni térség| `https://auth.syd.archivecloud.net`|

    c. Az a **válasz URL-CÍMEN** szövegmezőhöz az Adatközponton adott az URL-cím használata

    | Adatközpont| URL-cím |
    |----------|----|
    | Észak-Amerika| `https://auth.lax.archivecloud.net` |
    | Európa | `https://auth.ams.archivecloud.net` |
    | Ázsia és a Csendes-óceáni térség| `https://auth.syd.archivecloud.net`|
    
    > [!NOTE] 
    > Ez az érték nincs valós. Frissítse ezt az értéket a tényleges bejelentkezési URL-címet. Ügyfél [Veritas vállalati Vault.cloud SSO ügyfél-támogatási csoport](https://www.veritas.com/support/.html) lekérni ezt az értéket. 

4. Az a **SAML-aláíró tanúsítványa** kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-veritas-tutorial/tutorial_veritas_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-veritas-tutorial/tutorial_general_400.png)

6. A a **Veritas vállalati Vault.cloud SSO konfigurációs** kattintson **konfigurálása Veritas vállalati Vault.cloud SSO** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-veritas-tutorial/tutorial_veritas_configure.png) 

7. Egyszeri bejelentkezés konfigurálása **Veritas vállalati Vault.cloud SSO** oldalon kell küldeniük a letöltött **Certificate(Base64)** és **SAML-alapú egyszeri bejelentkezési URL-címe** a [Veritas vállalati Vault.cloud SSO támogatási csoport](https://www.veritas.com/support/.html).

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-veritas-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-veritas-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-veritas-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-veritas-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-veritas-enterprise-vaultcloud-sso-test-user"></a>Veritas vállalati Vault.cloud SSO tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon meghívta vállalati Vault.cloud egyszeri Bejelentkezéses felhasználó hoz létre. Együttműködve [Veritas vállalati Vault.cloud SSO támogatási csoport](https://www.veritas.com/support/.html) hozzáadása a felhasználók a vállalati Vault.cloud SSO platform. Felhasználók kell létrehoznia és aktiválni az egyszeri bejelentkezés használata előtt.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Veritas vállalati Vault.cloud SSO Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése Veritas vállalati Vault.cloud SSO, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Veritas vállalati Vault.cloud SSO**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-veritas-tutorial/tutorial_veritas_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Veritas vállalati Vault.cloud SSO csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az Veritas vállalati Vault.cloud SSO alkalmazáshoz.

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-veritas-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-veritas-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-veritas-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-veritas-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-veritas-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-veritas-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-veritas-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-veritas-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-veritas-tutorial/tutorial_general_203.png


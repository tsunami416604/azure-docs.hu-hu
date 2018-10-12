---
title: 'Oktatóanyag: Azure Active Directory-integráció az Box |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Box között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3b565c8d-35e2-482a-b2f4-bf8fd7d8731f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/09/2018
ms.author: jeedes
ms.openlocfilehash: 535f59b1b0dc56b183c8a019d101b4fd4f1bfad6
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2018
ms.locfileid: "49116125"
---
# <a name="tutorial-azure-active-directory-integration-with-box"></a>Oktatóanyag: Azure Active Directory-integráció az mezőbe

Ebben az oktatóanyagban elsajátíthatja, hogyan Box integrálása az Azure Active Directory (Azure AD).

Box integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, aki hozzáféréssel rendelkezik a Boxba Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett (egyszeri bejelentkezés) mezőben az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Box, a következőkre van szükség:

- Azure AD-előfizetés
- A Box egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Mező hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-box-from-the-gallery"></a>Mező hozzáadása a katalógusból
Konfigurálása az Azure AD integrálása a Box, hozzá kell mezőbe a galériából a felügyelt SaaS-alkalmazások listájára.

**Mező hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![image](./media/box-tutorial/selectazuread.png)

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![image](./media/box-tutorial/a_select_app.png)
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![image](./media/box-tutorial/a_new_app.png)

4. A Keresés mezőbe írja be a **Box**, jelölje be **Box** eredmény panelen kattintson a **hozzáadása** gombra kattintva vegye fel az alkalmazást.

     ![image](./media/box-tutorial/tutorial_Box_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a Box, a teszt "Britta Simon" nevű felhasználó.

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, a partner felhasználó mezőben mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó a boxban hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés a Box tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Hozzon létre egy Box tesztfelhasználót](#create-a-box-test-user)**  - a-megfelelője a Britta Simon rendelkezik, amely kapcsolódik az Azure AD felhasználói ábrázolása a boxban.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a Box alkalmazás egyszeri bejelentkezés konfigurálása.

**Konfigurálja az Azure AD egyszeri bejelentkezés a Box, hajtsa végre az alábbi lépéseket:**

1. Az a [az Azure portal](https://portal.azure.com/), a a **Box** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![image](./media/box-tutorial/b1_b2_select_sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen kattintson **kiválasztása** a **SAML** módot az egyszeri bejelentkezés engedélyezése.

    ![image](./media/box-tutorial/b1_b2_saml_sso.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** gombra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![image](./media/box-tutorial/b1-domains_and_urlsedit.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<SUBDOMAIN>.account.box.com`

    b. Az a **azonosító** szövegmezőbe írja be egy URL-címe:`box.net`

    ![image](./media/box-tutorial/tutorial_box_url.png)

    > [!NOTE] 
    > A bejelentkezési URL-érték nem valódi. Frissítse az értéket a tényleges bejelentkezési URL-CÍMÉT. Kapcsolattartó [be támogatási csapatának](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire) a gépkulcsengedélyek értékének.
 
5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **összevonási metaadatainak XML**  , és mentse a számítógépre.

    ![image](./media/box-tutorial/tutorial_Box_certificate.png)

6. Az alkalmazás egyszeri bejelentkezés konfigurálásához hajtsa végre az eljárást a [az egyszeri bejelentkezés beállítása a saját](https://community.box.com/t5/How-to-Guides-for-Admins/Setting-Up-Single-Sign-On-SSO-for-your-Enterprise/ta-p/1263#ssoonyourown). 

>[!NOTE]
>Ha nem sikerül, a Box-fiók számára az egyszeri bejelentkezési beállításainak konfigurálására, szeretné-e küldeni a letöltött **összevonási metaadatainak XML** való [be támogatási csapatának](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire). Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![image](./media/box-tutorial/d_users_and_groups.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![image](./media/box-tutorial/d_adduser.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![image](./media/box-tutorial/d_userproperties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőtípus **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **tulajdonságok**, jelölje be a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Létrehozás** gombra.
 
### <a name="create-a-box-test-user"></a>Box tesztfelhasználó létrehozása

Ez a szakasz célja a egy mezőben Britta Simon nevű felhasználó létrehozásához. Box támogatja a just-in-time-kiépítés engedélyezve alapértelmezés szerint ez. Nincs meg ebben a szakaszban a művelet elem. Új felhasználó próbál hozzáférni a Box, ha még nem létezik jön létre.
>[!Note]
>Ha manuálisan hozzon létre egy felhasználót van szüksége, forduljon a [be támogatási csapatának](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire).

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon a hozzáférés biztosításával a Boxba Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**válassza **minden alkalmazás**.

    ![image](./media/box-tutorial/d_all_applications.png)

2. Az alkalmazások listájában jelölje ki a **Box**.

    ![image](./media/box-tutorial/tutorial_Box_app.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![image](./media/box-tutorial/d_leftpaneusers.png)

4. Válassza ki a **Hozzáadás** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![image](./media/box-tutorial/d_assign_user.png)

4. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

5. Az a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelése** gombra.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Amikor kiválasztja a **Box** csempére a hozzáférési panelen, a Box alkalmazás való bejelentkezéshez kap a bejelentkezési oldal.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Felhasználók átadásának konfigurálása](box-userprovisioning-tutorial.md)



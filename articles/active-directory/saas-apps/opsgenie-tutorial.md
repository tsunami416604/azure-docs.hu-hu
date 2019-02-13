---
title: 'Oktatóanyag: Az Azure Active Directory-integráció az opsgenie segítségével |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és az opsgenie segítségével között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 41b59b22-a61d-4fe6-ab0d-6c3991d1375f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f9858fc38698ae2c5bd272a3494bcf02bce2d8e9
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56194597"
---
# <a name="tutorial-azure-active-directory-integration-with-opsgenie"></a>Oktatóanyag: Az Azure Active Directory-integráció az opsgenie segítségével

Ebben az oktatóanyagban elsajátíthatja, hogyan opsgenie segítségével integrálhatja az Azure Active Directoryval (Azure AD).

Opsgenie segítségével integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá az opsgenie segítségével az Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett az opsgenie segítségével (egyszeri bejelentkezés) az Azure AD-fiókjukat
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integrációs konfigurálás az opsgenie segítségével, a következőkre van szükség:

- Azure AD-előfizetés
- Egy opsgenie segítségével egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Hozzáadás a katalógusból, opsgenie segítségével
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-opsgenie-from-the-gallery"></a>Hozzáadás a katalógusból, opsgenie segítségével
Az Azure AD integrálása opsgenie segítségével a konfigurálása, hozzá kell opsgenie segítségével a galériából a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá az opsgenie segítségével a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **opsgenie segítségével**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/opsgenie-tutorial/tutorial_opsgenie_search.png)

1. Az eredmények panelen válassza ki a **opsgenie segítségével**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/opsgenie-tutorial/tutorial_opsgenie_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az opsgenie segítségével a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben kell tudja, hogy mi a partner felhasználó opsgenie segítségével a felhasználó Azure AD-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó az opsgenie segítségével hivatkozás kapcsolata kell létrehozni.

Opsgenie segítségével, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az opsgenie segítségével tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Opsgenie segítségével tesztfelhasználó létrehozása](#creating-a-opsgenie-test-user)**  – egy megfelelője a Britta Simon opsgenie segítségével, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és az opsgenie segítségével alkalmazás egyszeri bejelentkezés konfigurálása.

**Konfigurálás az opsgenie segítségével az Azure AD egyszeri bejelentkezés, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **opsgenie segítségével** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/opsgenie-tutorial/tutorial_opsgenie_samlbase.png)

1. Az a **opsgenie segítségével tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/opsgenie-tutorial/tutorial_opsgenie_url.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be az URL-cím: `https://app.opsgenie.com/auth/login`

1. Az a **SAML-aláíró tanúsítvány** területén kattintson a Másolás gombra, hogy **alkalmazás összevonási metaadatainak URL-címe** , és illessze be a Jegyzettömbbe.

    ![A tanúsítvány letöltési hivatkozás](./media/opsgenie-tutorial/tutorial_opsgenie_certificate.png)

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/opsgenie-tutorial/tutorial_general_400.png)

1. Az a **opsgenie segítségével konfigurációs** területén kattintson **konfigurálása opsgenie segítségével** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** rövid összefoglaló szakaszából.

    ![Egyszeri bejelentkezés konfigurálása](./media/opsgenie-tutorial/tutorial_opsgenie_configure.png)

1. Nyisson meg egy másik, és ezután jelentkezzen be rendszergazdaként opsgenie segítségével.

1. Kattintson a **beállítások**, majd kattintson a **az egyszeri bejelentkezést** fülre.
   
    ![Opsgenie segítségével Single Sign-On](./media/opsgenie-tutorial/tutorial_opsgenie_06.png)

1. Egyszeri bejelentkezés engedélyezéséhez jelölje be **engedélyezve**.
   
    ![Opsgenie segítségével beállításai](./media/opsgenie-tutorial/tutorial_opsgenie_07.png) 

1. Az a **szolgáltató** területén kattintson a **Azure Active Directory** fülre.
   
    ![Opsgenie segítségével beállításai](./media/opsgenie-tutorial/tutorial_opsgenie_08.png) 

1. Az Azure Active Directory párbeszédpanel oldalon hajtsa végre az alábbi lépéseket:
   
    ![Opsgenie segítségével beállításai](./media/opsgenie-tutorial/tutorial_opsgenie_09.png)
    
    a. Az a **SAML 2.0-s végpontjának** szövegmezőjébe illessze be **egyszeri bejelentkezési szolgáltatás URL-Címként**az Azure Portalról másolt érték.
    
    b. Az a **metaadatok URL-címe:** szövegmezőjébe illessze be **alkalmazás összevonási metaadatainak URL-címe** az Azure Portalról másolt érték.
    
    c. Kattintson a **módosítások mentése**.

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/opsgenie-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/opsgenie-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/opsgenie-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/opsgenie-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-opsgenie-test-user"></a>Opsgenie segítségével tesztfelhasználó létrehozása

Ez a szakasz célja az opsgenie segítségével Britta Simon nevű felhasználó létrehozásához. 

1. Egy böngészőablakban jelentkezzen be rendszergazdaként az opsgenie segítségével bérlő.

1. Keresse meg a felhasználók listában kattintva **felhasználói** a bal oldali panelen.
   
   ![Opsgenie segítségével beállításai](./media/opsgenie-tutorial/tutorial_opsgenie_10.png) 

1. Kattintson a **felhasználó hozzáadása**.

1. Az a **felhasználó hozzáadása** párbeszédpanelen hajtsa végre az alábbi lépéseket:
   
   ![Opsgenie segítségével beállításai](./media/opsgenie-tutorial/tutorial_opsgenie_11.png)
   
   a. Az a **E-mail** szövegmezőbe BrittaSimon az e-mail-cím típusú foglalkozik az Azure Active Directoryban.
   
   b. Az a **teljes fájlvisszaállítási név** szövegmezőbe írja be **Britta Simon**.
   
   c. Kattintson a **Save** (Mentés) gombra. 

>[!NOTE]
>Britta egy saját profil beállításával kapcsolatos utasításokat tartalmazó e-mailt kapja.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés opsgenie segítségével Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel opsgenie segítségével, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **opsgenie segítségével**.

    ![Egyszeri bejelentkezés konfigurálása](./media/opsgenie-tutorial/tutorial_opsgenie_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ez a szakasz célja a a hozzáférési Panel használatával az Azure AD egyszeri bejelentkezési konfiguráció tesztelése.

Ha a hozzáférési panelen az opsgenie segítségével csempére kattint, akkor kell lekérése automatikusan bejelentkezett az opsgenie segítségével alkalmazáshoz.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/opsgenie-tutorial/tutorial_general_01.png
[2]: ./media/opsgenie-tutorial/tutorial_general_02.png
[3]: ./media/opsgenie-tutorial/tutorial_general_03.png
[4]: ./media/opsgenie-tutorial/tutorial_general_04.png

[100]: ./media/opsgenie-tutorial/tutorial_general_100.png

[200]: ./media/opsgenie-tutorial/tutorial_general_200.png
[201]: ./media/opsgenie-tutorial/tutorial_general_201.png
[202]: ./media/opsgenie-tutorial/tutorial_general_202.png
[203]: ./media/opsgenie-tutorial/tutorial_general_203.png


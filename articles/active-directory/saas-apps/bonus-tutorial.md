---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Bonusly |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Bonusly között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 29fea32a-fa20-47b2-9e24-26feb47b0ae6
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: 181577c6a170d28679e1ca486dc2a8c5a596f20d
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54812131"
---
# <a name="tutorial-azure-active-directory-integration-with-bonusly"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Bonusly

Ebben az oktatóanyagban elsajátíthatja, hogyan Bonusly integrálása az Azure Active Directory (Azure AD).

Bonusly integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá Bonusly Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Bonusly (egyszeri bejelentkezés) az Azure AD-fiókjukkal
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Bonusly az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy Bonusly egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Bonusly hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-bonusly-from-the-gallery"></a>Bonusly hozzáadása a katalógusból
Az Azure AD integrálása a Bonusly konfigurálásához hozzá kell Bonusly a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Bonusly hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **Bonusly**válassza **Bonusly** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában Bonusly](./media/bonus-tutorial/tutorial_bonusly_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Bonusly a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Bonusly mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Bonusly hivatkozás kapcsolata kell létrehozni.

Bonusly, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az Bonusly tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy Bonusly tesztfelhasználót](#create-a-bonusly-test-user)**  – egy megfelelője a Britta Simon Bonusly, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Bonusly alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Bonusly, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Bonusly** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/bonus-tutorial/tutorial_bonusly_samlbase.png)

1. Az a **Bonusly tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Bonusly tartomány és URL-címek egyszeri bejelentkezési adatait](./media/bonus-tutorial/tutorial_bonusly_url.png)

    Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://Bonus.ly/saml/<tenant-name>`

    > [!NOTE] 
    > Az érték nem valódi. Frissítse az értéket a tényleges válasz URL-cím. Kapcsolattartó [Bonusly támogatási csapatának](https://bonus.ly/contact) a gépkulcsengedélyek értékének.
 
1. Az a **SAML-aláíró tanúsítvány** területén másolja a **UJJLENYOMAT** érték a tanúsítványból.

    ![A tanúsítvány letöltési hivatkozás](./media/bonus-tutorial/tutorial_bonusly_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/bonus-tutorial/tutorial_general_400.png)

1. Az a **Bonusly konfigurációs** területén kattintson **Bonusly konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Bonusly konfiguráció](./media/bonus-tutorial/tutorial_bonusly_configure.png) 

1. Egy másik böngészőablakban jelentkezzen be a **Bonusly** bérlő.

1. A felső eszköztáron kattintson **beállítások**, majd válassza ki **Integrációk és az alkalmazások**.
   
    ![Bonusly közösségi szakasz](./media/bonus-tutorial/ic773686.png "Bonusly")
1. A **egyszeri bejelentkezés**válassza **SAML**.

1. Az a **SAML** párbeszédpanel lapon, a következő lépésekkel:
   
    ![Bonusly Saml párbeszédpanel lap](./media/bonus-tutorial/ic773687.png "Bonusly")
   
    a. Az a **identitásszolgáltató SSO cél URL-cím** szövegmezőbe, illessze be az értéket a **SAML egyszeri bejelentkezési szolgáltatás URL-cím**, az Azure Portalról másolt.
   
    b. Az a **identitásszolgáltató kibocsátója** szövegmező, illessze be az értéket a **SAML Entitásazonosító**, az Azure Portalról másolt. 

    c. Az a **identitásszolgáltató bejelentkezési URL-cím** szövegmezőbe, illessze be az értéket a **SAML egyszeri bejelentkezési szolgáltatás URL-cím**, az Azure Portalról másolt.

    d. Illessze be a **ujjlenyomat** az Azure Portalról másolt érték a **tanúsítvány-ujjlenyomat** szövegmezőbe.
   
1. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](./media/bonus-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/bonus-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![A Hozzáadás gombra.](./media/bonus-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![A felhasználó párbeszédpanel](./media/bonus-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-bonusly-test-user"></a>Bonusly tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Bonusly jelentkezzen be, akkor ki kell építeni Bonusly be. Bonusly, esetén kiépítése a manuális feladat.

>[!NOTE]
>Bármely más Bonusly felhasználói fiók létrehozása eszközöket használhatja, vagy az aad-ben a felhasználói fiókok kiépítését Bonusly által biztosított API-k.
>  

**Felhasználók átadásának konfigurálása, hajtsa végre az alábbi lépéseket:**

1. Egy böngészőablakban jelentkezzen be a Bonusly bérlő.

1. Kattintson a **beállítások**.
 
    ![Beállítások](./media/bonus-tutorial/ic781041.png "beállításai")

1. Kattintson a **felhasználók és a prémium** fülre.
   
    ![Felhasználók és a prémium](./media/bonus-tutorial/ic781042.png "felhasználók és a prémium")

1. Kattintson a **felhasználók kezelése**.
   
    ![Felhasználók kezelése](./media/bonus-tutorial/ic781043.png "felhasználók kezelése")

1. Kattintson a **felhasználó hozzáadása**.
   
    ![Felhasználó hozzáadása](./media/bonus-tutorial/ic781044.png "felhasználó hozzáadása")

1. Az a **felhasználó hozzáadása** párbeszédpanelen hajtsa végre az alábbi lépéseket:
   
    ![Felhasználó hozzáadása](./media/bonus-tutorial/ic781045.png "felhasználó hozzáadása")  

    a. Az a **Utónév** szövegmezőbe írja be például a felhasználó utónevét **Britta**.

    b. Az a **Vezetéknév** szövegmezőbe írja be például a felhasználó vezetékneve **Simon**.
 
    c. Az a **E-mail** szövegmezőben adja meg az e-mail címét, például a felhasználó **brittasimon@contoso.com**.

    d. Kattintson a **Save** (Mentés) gombra.
   
     >[!NOTE]
     >Az Azure ad-ben fióktulajdonos kap egy e-mailt, amelyben a hivatkozást a fiók megerősítéséhez, mielőtt aktívvá válik.
     >  

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Bonusly Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel Bonusly, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Bonusly**.

    ![Az alkalmazások listáját a Bonusly hivatkozásra](./media/bonus-tutorial/tutorial_bonusly_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ez a szakasz célja tesztelése az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

A Bonusly csempére kattintva a hozzáférési panelen, meg kell lekérése automatikusan bejelentkezett a Bonusly alkalmazásba.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/bonus-tutorial/tutorial_general_01.png
[2]: ./media/bonus-tutorial/tutorial_general_02.png
[3]: ./media/bonus-tutorial/tutorial_general_03.png
[4]: ./media/bonus-tutorial/tutorial_general_04.png

[100]: ./media/bonus-tutorial/tutorial_general_100.png

[200]: ./media/bonus-tutorial/tutorial_general_200.png
[201]: ./media/bonus-tutorial/tutorial_general_201.png
[202]: ./media/bonus-tutorial/tutorial_general_202.png
[203]: ./media/bonus-tutorial/tutorial_general_203.png


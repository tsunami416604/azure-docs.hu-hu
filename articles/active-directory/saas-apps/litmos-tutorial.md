---
title: 'Oktatóanyag: Azure Active Directory-integráció az Litmos |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Litmos között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: jeedes
ms.assetid: cfaae4bb-e8e5-41d1-ac88-8cc369653036
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: a0c70ee6419280b0975d77fb213f9406286708cc
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39428002"
---
# <a name="tutorial-azure-active-directory-integration-with-litmos"></a>Oktatóanyag: Azure Active Directory-integráció az Litmos

Ebben az oktatóanyagban elsajátíthatja, hogyan Litmos integrálása az Azure Active Directory (Azure AD).

Litmos integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá Litmos Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Litmos (egyszeri bejelentkezés), az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Litmos az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy Litmos egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Litmos hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-litmos-from-the-gallery"></a>Litmos hozzáadása a katalógusból
Az Azure AD integrálása a Litmos konfigurálásához hozzá kell Litmos a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Litmos hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **Litmos**válassza **Litmos** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában Litmos](./media/litmos-tutorial/tutorial_litmos_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Litmos a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Litmos mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Litmos hivatkozás kapcsolata kell létrehozni.

Litmos, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az Litmos tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy Litmos tesztfelhasználót](#create-a-litmos-test-user)**  – egy megfelelője a Britta Simon Litmos, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Litmos alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Litmos, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Litmos** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/litmos-tutorial/tutorial_litmos_samlbase.png)

1. Az a **Litmos tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Litmos tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/litmos-tutorial/tutorial_litmos_url.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<companyname>.litmos.com/account/Login`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<companyname>.litmos.com/integration/samllogin`

    > [!NOTE] 
    > Ezek a értékei nem valódi. Az értékeket módosítsa a tényleges azonosítója és a válasz URL-cím, amelyeket később az oktatóanyagban, vagy forduljon a [Litmos támogatási csoportjának](https://www.litmos.com/contact-us/) beolvasni ezeket az értékeket.

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/litmos-tutorial/tutorial_litmos_certificate.png)

1. A konfigurálás részeként testre kell szabnia a **SAML-jogkivonat attribútumai** Litmos alkalmazásához.

    ![Attribútum szakasz](./media/litmos-tutorial/tutorial_attribute.png)
           
    | Attribútum neve   | Attribútum értéke |   
    | ---------------  | ----------------|
    | FirstName |User.givenName |
    | LastName  |User.surname |
    | E-mail |user.mail |

    a. Kattintson a **attribútum hozzáadása** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Attribútum hozzáadása](./media/litmos-tutorial/tutorial_attribute_04.png)

    ![Dailog attribútum hozzáadása](./media/litmos-tutorial/tutorial_attribute_05.png)

    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    c. Az a **érték** list, írja be az adott sorhoz feltüntetett attribútumot értéket.
    
    d. Kattintson az **OK** gombra.     

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/litmos-tutorial/tutorial_general_400.png)

1. Egy másik böngészőablakban bejelentkezés Litmos vállalat webhelye rendszergazdaként.

1. A bal oldali navigációs sávon kattintson **fiókok**.
   
    ![Alkalmazás oldalán a fiókok területen][22] 

1. Kattintson a **Integrációk** fülre.
   
    ![Integráció lapon][23] 

1. Az a **Integrációk** fülre, görgessen le a **3. fél Integrációk**, és kattintson a **SAML 2.0** fülre.
   
    ![SAML 2.0-s szakasz][24] 

1. Másolja az értéket a **litmos számára a SAML-végpont:** és illessze be azt a **válasz URL-cím** szövegmezőjébe a **Litmos tartomány és URL-címek** szakaszban az Azure Portalon. 
   
    ![SAML-végpont][26] 

1. Az a **Litmos** alkalmazás, hajtsa végre az alábbi lépéseket:
    
     ![Litmos alkalmazás][25] 
     
     a. Kattintson a **SAML engedélyezése**.
    
     b. Nyissa meg a base-64 kódolású tanúsítványt a Jegyzettömbben, a tartalmát a vágólapra másolja és illessze be azt a **SAML X.509-tanúsítvány** szövegmezőbe.
     
     c. Kattintson a **módosítások mentése**.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/litmos-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/litmos-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/litmos-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/litmos-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
  
### <a name="create-a-litmos-test-user"></a>Litmos tesztfelhasználó létrehozása

Ez a szakasz célja Litmos Britta Simon nevű felhasználó létrehozásához.  
Az Litmos alkalmazás támogatja a Just-in-Time-kiépítés. Ez azt jelenti, hogy egy felhasználói fiókot automatikusan jön létre szükség esetén próbál hozzáférni az alkalmazáshoz a hozzáférési panelen.

**Britta Simon Litmos nevű felhasználó létrehozásához hajtsa végre az alábbi lépéseket:**

1. Egy másik böngészőablakban bejelentkezés Litmos vállalat webhelye rendszergazdaként.

1. A bal oldali navigációs sávon kattintson **fiókok**.
   
    ![Alkalmazás oldalán a fiókok területen][22] 

1. Kattintson a **Integrációk** fülre.
   
    ![Integrációk lap][23] 

1. Az a **Integrációk** fülre, görgessen le a **3. fél Integrációk**, és kattintson a **SAML 2.0** fülre.
   
    ![SAML 2.0][24] 
    
1. Válassza ki **felhasználók automatikus létrehozása**
   
    ![Felhasználók automatikus létrehozása][27] 

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Litmos Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel Litmos, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Litmos**.

    ![Az alkalmazások listáját a Litmos hivatkozásra](./media/litmos-tutorial/tutorial_litmos_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ez a szakasz célja tesztelése az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.  

Ha a hozzáférési panelen a Litmos csempére kattint, meg kell lekérése automatikusan bejelentkezett az Litmos alkalmazáshoz. 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/litmos-tutorial/tutorial_general_01.png
[2]: ./media/litmos-tutorial/tutorial_general_02.png
[3]: ./media/litmos-tutorial/tutorial_general_03.png
[4]: ./media/litmos-tutorial/tutorial_general_04.png
[21]: ./media/litmos-tutorial/tutorial_litmos_60.png
[22]: ./media/litmos-tutorial/tutorial_litmos_61.png
[23]: ./media/litmos-tutorial/tutorial_litmos_62.png
[24]: ./media/litmos-tutorial/tutorial_litmos_63.png
[25]: ./media/litmos-tutorial/tutorial_litmos_64.png
[26]: ./media/litmos-tutorial/tutorial_litmos_65.png
[27]: ./media/litmos-tutorial/tutorial_litmos_66.png

[100]: ./media/litmos-tutorial/tutorial_general_100.png

[200]: ./media/litmos-tutorial/tutorial_general_200.png
[201]: ./media/litmos-tutorial/tutorial_general_201.png
[202]: ./media/litmos-tutorial/tutorial_general_202.png
[203]: ./media/litmos-tutorial/tutorial_general_203.png


---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező CloudPassage |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és CloudPassage között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: bfe1f14e-74e4-4680-ac9e-f7355e1c94cc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: d8bddc309db3ede67586a2067fcf57fbede7d6b3
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55152790"
---
# <a name="tutorial-azure-active-directory-integration-with-cloudpassage"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező CloudPassage

Ebben az oktatóanyagban elsajátíthatja, hogyan CloudPassage integrálása az Azure Active Directory (Azure AD).

CloudPassage integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá CloudPassage Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett CloudPassage (egyszeri bejelentkezés) az Azure AD-fiókjukkal
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

CloudPassage az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy CloudPassage egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. CloudPassage hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-cloudpassage-from-the-gallery"></a>CloudPassage hozzáadása a katalógusból
Az Azure AD integrálása a CloudPassage konfigurálásához hozzá kell CloudPassage a katalógusból a felügyelt SaaS-alkalmazások listájára.

**CloudPassage hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **CloudPassage**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/cloudpassage-tutorial/tutorial_cloudpassage_search.png)

1. Az eredmények panelen válassza ki a **CloudPassage**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/cloudpassage-tutorial/tutorial_cloudpassage_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban konfigurálja, és a teszt "Britta Simon." nevű felhasználó CloudPassage az Azure AD egyszeri bejelentkezés tesztelése

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó CloudPassage mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó CloudPassage hivatkozás kapcsolata kell létrehozni.

CloudPassage, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az CloudPassage tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[CloudPassage tesztfelhasználó létrehozása](#creating-a-cloudpassage-test-user)**  – egy megfelelője a Britta Simon CloudPassage, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és CloudPassage alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés CloudPassage, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **CloudPassage** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/cloudpassage-tutorial/tutorial_cloudpassage_samlbase.png)

1. Az a **CloudPassage tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/cloudpassage-tutorial/tutorial_cloudpassage_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://portal.cloudpassage.com/saml/init/accountid`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://portal.cloudpassage.com/saml/consume/accountid`. Megtekintheti a értéket ehhez az attribútumhoz kattintva **egyszeri bejelentkezés beállítása dokumentáció** a a **egyszeri bejelentkezési beállításainak** a CloudPassage portál szakaszát.

    ![Egyszeri bejelentkezés konfigurálása](./media/cloudpassage-tutorial/tutorial_cloudpassage_05.png)
     
    > [!NOTE] 
    > Ezek a értékei nem valódi. Frissítse a tényleges válasz URL-címet, és a bejelentkezési URL-ezeket az értékeket. Kapcsolattartó [CloudPassage ügyfél-támogatási csapatának](https://www.cloudpassage.com/company/contact/) beolvasni ezeket az értékeket. 

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/cloudpassage-tutorial/tutorial_cloudpassage_certificate.png) 

1. CloudPassage alkalmazását a SAML helyességi feltételek vár egy megadott formátumban, amely megköveteli, hogy egyéni attribútum-leképezéshez az SAML-jogkivonat attribútumai konfigurációja. Az alábbi képernyőfelvételen látható erre egy példa látható.
   
   ![Egyszeri bejelentkezés konfigurálása](./media/cloudpassage-tutorial/tutorial_cloudpassage_25.png) 

1. Az a **felhasználói attribútumok** szakaszában a **egyszeri bejelentkezési** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, a fenti képen látható módon, és hajtsa végre az alábbi lépéseket:

    | Attribútum neve | Attribútum értéke |
    | --- | --- |
    | Keresztnév |user.givenname |
    | Vezetéknév |user.surname |
    | e-mail |user.mail |
    
    a. Kattintson a **attribútum hozzáadása** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/cloudpassage-tutorial/tutorial_attribute_04.png)
    
    ![Egyszeri bejelentkezés konfigurálása](./media/cloudpassage-tutorial/tutorial_attribute_05.png)
    
    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    c. Az a **érték** list, írja be az adott sorhoz feltüntetett attribútumot értéket.
    
    d. Kattintson az **OK** gombra.

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/cloudpassage-tutorial/tutorial_general_400.png)
    
1. Az a **CloudPassage konfigurációs** területén kattintson **konfigurálása CloudPassage** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/cloudpassage-tutorial/tutorial_cloudpassage_configure.png) 

1. Egy másik böngészőablakban bejelentkezés CloudPassage vállalat webhelye rendszergazdaként.

1. A felső menüben kattintson **beállítások**, és kattintson a **helyfelügyelet**. 
   
    ![Egyszeri bejelentkezés konfigurálása][12]

1. Kattintson a **hitelesítési beállítások** fülre. 
   
    ![Egyszeri bejelentkezés konfigurálása][13]

1. Az a **egyszeri bejelentkezési beállításainak** szakaszban, hajtsa végre az alábbi lépéseket: 
   
    ![Egyszeri bejelentkezés konfigurálása][14]

    a. Válassza ki **engedélyezése egyetlen sign-on(SSO) (egyszeri bejelentkezés beállítása dokumentáció)** jelölőnégyzetet.
    
    b. Beillesztés **SAML Entitásazonosító** be a **SAML kiállítójának URL-címe** szövegmezőbe.
  
    c. Beillesztés **SAML egyszeri bejelentkezési szolgáltatás URL-cím** be a **SAML-végpont URL-címe** szövegmezőbe.
  
    d. Beillesztés **kijelentkezéses URL-cím** be a **kijelentkezési kezdőlapja** szövegmezőbe.
  
    e. A Jegyzettömb alkalmazásban nyissa meg a letöltött tanúsítvány, a letöltött tanúsítvány tartalmát a vágólapra másolja és illessze be azt a **x 509 tanúsítvány** szövegmezőbe.
  
    f. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/cloudpassage-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/cloudpassage-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/cloudpassage-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/cloudpassage-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-cloudpassage-test-user"></a>CloudPassage tesztfelhasználó létrehozása

Ez a szakasz célja CloudPassage Britta Simon nevű felhasználó létrehozásához.

**Britta Simon CloudPassage nevű felhasználó létrehozásához hajtsa végre az alábbi lépéseket:**

1. Bejelentkezés a **CloudPassage** rendszergazdaként a vállalati webhely. 

1. A felső eszköztáron kattintson **beállítások**, és kattintson a **helyfelügyelet**. 
   
   ![CloudPassage tesztfelhasználó létrehozása][22] 

1. Kattintson a **felhasználók** fülre, majd **új felhasználó hozzáadása**. 
   
   ![CloudPassage tesztfelhasználó létrehozása][23]

1. Az a **új felhasználó hozzáadása** szakaszban, hajtsa végre az alábbi lépéseket: 
   
   ![CloudPassage tesztfelhasználó létrehozása][24]
    
    a. Az a **Utónév** szövegmezőbe írja be a Britta. 
  
    b. Az a **Vezetéknév** szövegmezőbe írja be a Simon.
  
    c. A a **felhasználónév** szövegmezőbe a **E-mail** szövegmezőbe, és a **E-mail írja be újra** szövegmezőbe írja be a Britta felhasználónevet az Azure ad-ben.
  
    d. Mint **hozzáférés típusa**válassza **Halo portál hozzáférés engedélyezése**.
  
    e. Kattintson a **Hozzáadás** parancsra.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés CloudPassage Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel CloudPassage, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **CloudPassage**.

    ![Egyszeri bejelentkezés konfigurálása](./media/cloudpassage-tutorial/tutorial_cloudpassage_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ez a szakasz célja a a hozzáférési Panel használatával az Azure AD egyszeri bejelentkezési konfiguráció tesztelése.

Ha a hozzáférési panelen a CloudPassage csempére kattint, meg kell lekérése automatikusan bejelentkezett az CloudPassage alkalmazáshoz.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/cloudpassage-tutorial/tutorial_general_01.png
[2]: ./media/cloudpassage-tutorial/tutorial_general_02.png
[3]: ./media/cloudpassage-tutorial/tutorial_general_03.png
[4]: ./media/cloudpassage-tutorial/tutorial_general_04.png
[12]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_07.png
[13]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_08.png
[14]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_09.png
[15]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_10.png
[22]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_15.png
[23]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_16.png
[24]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_17.png

[100]: ./media/cloudpassage-tutorial/tutorial_general_100.png

[200]: ./media/cloudpassage-tutorial/tutorial_general_200.png
[201]: ./media/cloudpassage-tutorial/tutorial_general_201.png
[202]: ./media/cloudpassage-tutorial/tutorial_general_202.png
[203]: ./media/cloudpassage-tutorial/tutorial_general_203.png


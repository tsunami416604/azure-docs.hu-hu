---
title: 'Oktatóanyag: Azure Active Directory-integráció az Igloo szoftver |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Igloo szoftverek között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 2eb625c1-d3fc-4ae1-a304-6a6733a10e6e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: d49a08c6f57f5248f17539cd9d0467d132f7a63d
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39447407"
---
# <a name="tutorial-azure-active-directory-integration-with-igloo-software"></a>Oktatóanyag: Azure Active Directory-integráció Igloo szoftverrel

Ebben az oktatóanyagban elsajátíthatja, hogyan Igloo szoftver integrálása az Azure Active Directory (Azure AD).

Igloo szoftver integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá Igloo szoftverek az Azure AD-ben
- Az Azure AD-fiókjukat engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Igloo szoftverre (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása Igloo szoftverekkel rendelkező, a következő elemek szükségesek:

- Az Azure AD-előfizetéshez
- Egy Igloo szoftver egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Igloo szoftver hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-igloo-software-from-the-gallery"></a>Igloo szoftver hozzáadása a katalógusból
Az Azure AD-be Igloo szoftver integráció konfigurálásához, kell Igloo szoftver hozzáadása a felügyelt SaaS-alkalmazások listájában a katalógusból.

**Igloo szoftver hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **Igloo szoftver**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/igloo-software-tutorial/tutorial_igloosoftware_search.png)

1. Az eredmények panelen válassza ki a **Igloo szoftver**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/igloo-software-tutorial/tutorial_igloosoftware_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Igloo szoftver úgynevezett "Britta Simon" tesztfelhasználó alapján.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Igloo szoftverfrissítési mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Igloo szoftveres hivatkozás kapcsolata kell létrehozni.

Igloo szoftver, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés Igloo szoftverrel tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Egy Igloo szoftver tesztfelhasználó létrehozása](#creating-an-igloo-software-test-user)**  – egy megfelelője a Britta Simon Igloo szoftver, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Igloo szoftver alkalmazását az egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálása Igloo szoftverrel, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Igloo szoftver** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/igloo-software-tutorial/tutorial_igloosoftware_samlbase.png)

1. Az a **Igloo szoftver tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/igloo-software-tutorial/tutorial_igloosoftware_url.png)
    
    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<company name>.igloocommmunities.com`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<company name>.igloocommmunities.com/saml.digest`

    c. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<company name>.igloocommmunities.com/saml.digest`

    > [!NOTE] 
    > Ezek a értékei nem valódi. Frissítse a tényleges azonosítóját, válasz URL-cím és bejelentkezési URL-ezeket az értékeket. Kapcsolattartó [Igloo Szoftverügyfél támogatási csapatának](https://www.igloosoftware.com/services/support) beolvasni ezeket az értékeket. 

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/igloo-software-tutorial/tutorial_igloosoftware_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/igloo-software-tutorial/tutorial_general_400.png)
    
1. Az a **Igloo szoftverfrissítési konfiguráció** területén kattintson **Igloo szoftver konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-CÍMÉT és a SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/igloo-software-tutorial/tutorial_igloosoftware_configure.png) 

1. Egy másik böngészőablakban jelentkezzen be a Igloo szoftver vállalati hely rendszergazdaként.

1. Nyissa meg a **vezérlőpultot**.
   
     ![Vezérlőpultot](./media/igloo-software-tutorial/ic799949.png "vezérlőpultot")

1. Az a **tagsági** lapra, majd **bejelentkezést a beállítások**.
   
    ![Bejelentkezési beállítások](./media/igloo-software-tutorial/ic783968.png "bejelentkezési beállítások")

1. A SAML-konfigurációja területen kattintson a **SAML-hitelesítés konfigurálása**.
   
    ![SAML-konfigurációja](./media/igloo-software-tutorial/ic783969.png "SAML-konfigurációja")
   
1. Az a **általános konfigurációs** szakaszban, hajtsa végre az alábbi lépéseket:
   
    ![Általános konfiguráció](./media/igloo-software-tutorial/ic783970.png "általános konfiguráció")

    a. Az a **kapcsolatnevet** szövegmezőbe írjon be egy egyéni nevet a konfigurációnak.
   
    b. Az a **identitásszolgáltató bejelentkezési URL-cím** szövegmező, illessze be az értéket a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** Azure Portalról másolt.
   
    c. A a **identitásszolgáltató kijelentkezési URL-címe** szövegmezőjébe illessze be az értéket, **kijelentkezéses URL-cím** Azure Portalról másolt.
    
    d. Válassza ki **kijelentkezési válaszok és a HTTP típusú** , **POST**.
   
    e. Nyissa meg a **base-64** kódolt tanúsítvány a Jegyzettömbben az Azure-portálról letöltött, a tartalmát a vágólapra másolja és illessze be azt a **nyilvános tanúsítvány** szövegmezőbe.
    
1. Az a **válasz és a hitelesítési konfiguráció**, hajtsa végre az alábbi lépéseket:
    
    ![Válasz és a hitelesítési konfiguráció](./media/igloo-software-tutorial/IC783971.png "válasz és hitelesítés konfigurációját.")
  
      a. Mint **identitásszolgáltató**válassza **Microsoft ADFS**.
      
      b. Mint **azonosító típusának**válassza **E-mail cím**. 

      c. Az a **E-mail attribútum** szövegmezőbe írja be **emailaddress**.

      d. Az a **First Name attribútum** szövegmezőbe írja be **givenname**.

      e. Az a **utolsó név attribútum** szövegmezőbe írja be **Vezetéknév**.

1. Hajtsa végre az alábbi lépéseket a konfigurálás befejezéséhez:
    
    ![Felhasználó létrehozása bejelentkezési](./media/igloo-software-tutorial/IC783972.png "bejelentkezési felhasználó létrehozása") 

     a. Mint **bejelentkezési felhasználói létrehozása**válassza **új felhasználó létrehozása a helyen található, bejelentkezéskor**.

     b. Mint **bejelentkezési beállítások**válassza **használható SAML gombra a "Bejelentkezés" képernyőn**.

     c. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/igloo-software-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/igloo-software-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/igloo-software-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/igloo-software-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-an-igloo-software-test-user"></a>Egy Igloo szoftver tesztfelhasználó létrehozása

Nincs művelet elem, hogy a felhasználók átadásának Igloo szoftver konfigurálása.  

Hozzárendelt felhasználóval próbál bejelentkezni a hozzáférési panelen Igloo szoftver, a Igloo szoftver ellenőrzi, hogy a felhasználó létezik-e.  Ha nincs felhasználói fiók elérhető még, automatikusan létrehozott Igloo Software-től.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Igloo szoftver Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel Igloo szoftver, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Igloo szoftver**.

    ![Egyszeri bejelentkezés konfigurálása](./media/igloo-software-tutorial/tutorial_igloosoftware_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Igloo szoftver csempére kattint, meg kell lekérése automatikusan bejelentkezett az Igloo szoftver alkalmazás.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/igloo-software-tutorial/tutorial_general_01.png
[2]: ./media/igloo-software-tutorial/tutorial_general_02.png
[3]: ./media/igloo-software-tutorial/tutorial_general_03.png
[4]: ./media/igloo-software-tutorial/tutorial_general_04.png

[100]: ./media/igloo-software-tutorial/tutorial_general_100.png

[200]: ./media/igloo-software-tutorial/tutorial_general_200.png
[201]: ./media/igloo-software-tutorial/tutorial_general_201.png
[202]: ./media/igloo-software-tutorial/tutorial_general_202.png
[203]: ./media/igloo-software-tutorial/tutorial_general_203.png


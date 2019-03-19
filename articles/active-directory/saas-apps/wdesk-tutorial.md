---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Wdesk |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Wdesk között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 06900a91-a326-4663-8ba6-69ae741a536e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75d0e962169529ab8d17aeeeed8aab26e7b7e994
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57880874"
---
# <a name="tutorial-azure-active-directory-integration-with-wdesk"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Wdesk

Ebben az oktatóanyagban elsajátíthatja, hogyan Wdesk integrálása az Azure Active Directory (Azure AD).

Wdesk integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá Wdesk Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Wdesk (egyszeri bejelentkezés) az Azure AD-fiókjukkal
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd. [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Wdesk az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy Wdesk egyszeri bejelentkezéses engedélyezett előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Wdesk hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-wdesk-from-the-gallery"></a>Wdesk hozzáadása a katalógusból
Az Azure AD integrálása a Wdesk konfigurálásához hozzá kell Wdesk a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Wdesk hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **Wdesk**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/wdesk-tutorial/tutorial_wdesk_search.png)

1. Az eredmények panelen válassza ki a **Wdesk**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/wdesk-tutorial/tutorial_wdesk_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban konfigurálja, és a teszt "Britta Simon." nevű felhasználó Wdesk az Azure AD egyszeri bejelentkezés tesztelése

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Wdesk mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Wdesk hivatkozás kapcsolata kell létrehozni.

Ez a hivatkozás-kapcsolat létesítéséhez értéket rendeli az **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** Wdesk a.

Az Azure AD egyszeri bejelentkezés az Wdesk tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Wdesk tesztfelhasználó létrehozása](#creating-a-wdesk-test-user)**  – egy megfelelője a Britta Simon Wdesk, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Wdesk alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Wdesk, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Wdesk** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/wdesk-tutorial/tutorial_wdesk_samlbase.png)

1. Az a **Wdesk tartomány és URL-címek** szakaszra, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett módban a következő lépésekkel:

    ![Egyszeri bejelentkezés konfigurálása](./media/wdesk-tutorial/tutorial_wdesk_url.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<subdomain>.wdesk.com/auth/saml/sp/metadata/<instancename>`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<subdomain>.wdesk.com/auth/saml/sp/consumer/<instancename>`

1. Ellenőrizze **speciális URL-beállítások megjelenítése**. Ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód, hajtsa végre a következő lépést:

    ![Egyszeri bejelentkezés konfigurálása](./media/wdesk-tutorial/tutorial_wdesk_url1.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<subdomain>.wdesk.com/auth/login/saml/<instancename>`
     
    > [!NOTE] 
    > Ezek a értékei nem valódi. Frissítse a tényleges azonosítóját, válasz URL-cím és bejelentkezési URL-ezeket az értékeket. Ezek az értékek első WDesk portálról az egyszeri bejelentkezés konfigurálásakor. 
  
1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/wdesk-tutorial/tutorial_wdesk_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/wdesk-tutorial/tutorial_general_400.png)
    
1. Egy másik böngészőablakban, jelentkezzen be egy biztonsági-rendszergazdaként Wdesk.

1. Kattintson a bal alsó **rendszergazdai** válassza **Fiókadminisztrátor**:
 
     ![Egyszeri bejelentkezés konfigurálása](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

1. Wdesk rendszergazdaként, lépjen a **biztonsági**, majd **SAML** > **SAML-beállítások**:

    ![Egyszeri bejelentkezés konfigurálása](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig2.png)

1. A **általános beállítások**, ellenőrizze a **SAML egyszeri bejelentkezés engedélyezése**:

    ![Egyszeri bejelentkezés konfigurálása](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig3.png)

1. A **szolgáltató szolgáltatásismertető**, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig4.png)

      a. Másolás a **bejelentkezési URL-cím** , és illessze be a **bejelentkezési URL-** szövegmezőben, hogy az Azure Portalon.
   
      b. Másolás a **metaadatok URL-címe** , és illessze be a **azonosító** szövegmezőben, hogy az Azure Portalon.
       
      c. Másolás a **ügyfél URL-címe** , és illessze be a **válasz URL-cím** szövegmezőben, hogy az Azure Portalon.
   
      d. Kattintson a **mentése** menti a módosításokat az Azure Portalon.      

1. Kattintson a **IdP-beállítások konfigurálása** megnyitásához **IdP-beállítások szerkesztése** párbeszédpanel. Kattintson a **fájl kiválasztása** keresse meg a **Metadata.xml** mentett fájlt, az Azure Portalon, majd töltse fel.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig5.png)
  
1. Kattintson a **módosítások mentése**.

    ![Egyszeri bejelentkezés konfigurálása](./media/wdesk-tutorial/tutorial_wdesk_ssoconfigsavebutton.png)

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/wdesk-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/wdesk-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/wdesk-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/wdesk-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-wdesk-test-user"></a>Wdesk tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Wdesk jelentkezzen be, akkor ki kell építeni Wdesk be. Wdesk a kiépítés manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be egy biztonsági-rendszergazdaként Wdesk.
1. Navigáljon a **rendszergazdai** > **rendszergazdai fiók**.

     ![Egyszeri bejelentkezés konfigurálása](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

1. Kattintson a **tagok** alatt **személyek**.

1. Most kattintson **tag hozzáadása** megnyitásához **tag hozzáadása** párbeszédpanel bezárásához. 
   
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/wdesk-tutorial/createuser1.png)  

1. A **felhasználói** szöveg mezőbe írja be például a felhasználó felhasználóneve **brittasimon\@contoso.com** kattintson **Folytatás** gombra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/wdesk-tutorial/createuser3.png)

1.  Adja meg adatait alább látható módon:
  
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/wdesk-tutorial/createuser4.png)
 
    a. A **E-mail** szöveget adja meg az e-mailt, például a felhasználó **brittasimon\@contoso.com**.

    b. A **Utónév** szöveget adja meg például a felhasználó utónevét **Britta**.

    c. A **Vezetéknév** szöveget adja meg például a felhasználó vezetékneve **Simon**.

1. Kattintson a **mentése tag** gombra.  

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/wdesk-tutorial/createuser5.png)

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Wdesk Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel Wdesk, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Wdesk**.

    ![Egyszeri bejelentkezés konfigurálása](./media/wdesk-tutorial/tutorial_wdesk_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Wdesk csempére kattint, meg kell lekérése automatikusan bejelentkezett az Wdesk alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).


## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/wdesk-tutorial/tutorial_general_01.png
[2]: ./media/wdesk-tutorial/tutorial_general_02.png
[3]: ./media/wdesk-tutorial/tutorial_general_03.png
[4]: ./media/wdesk-tutorial/tutorial_general_04.png

[100]: ./media/wdesk-tutorial/tutorial_general_100.png

[200]: ./media/wdesk-tutorial/tutorial_general_200.png
[201]: ./media/wdesk-tutorial/tutorial_general_201.png
[202]: ./media/wdesk-tutorial/tutorial_general_202.png
[203]: ./media/wdesk-tutorial/tutorial_general_203.png


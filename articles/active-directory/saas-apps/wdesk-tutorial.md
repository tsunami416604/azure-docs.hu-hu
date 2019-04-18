---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Wdesk |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Wdesk között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 06900a91-a326-4663-8ba6-69ae741a536e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 71feb455457fdf75fb19121bac1927b42fe38b67
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59265882"
---
# <a name="tutorial-azure-active-directory-integration-with-wdesk"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Wdesk

Ebben az oktatóanyagban elsajátíthatja, hogyan Wdesk integrálása az Azure Active Directory (Azure AD).
Wdesk integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá Wdesk Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezve Wdesk (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Wdesk az Azure AD-integráció konfigurálásához a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a [ingyenes fiókkal](https://azure.microsoft.com/free/)
* Wdesk egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja a Wdesk **SP** és **Identitásszolgáltató** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-wdesk-from-the-gallery"></a>Wdesk hozzáadása a katalógusból

Az Azure AD integrálása a Wdesk konfigurálásához hozzá kell Wdesk a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Wdesk hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Wdesk**válassza **Wdesk** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Az eredmények listájában Wdesk](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az Wdesk nevű tesztfelhasználó alapján **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó Wdesk hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az Wdesk tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Wdesk egyszeri bejelentkezés konfigurálása](#configure-wdesk-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre Wdesk tesztfelhasználót](#create-wdesk-test-user)**  – egy megfelelője a Britta Simon Wdesk, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Wdesk, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **Wdesk** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszra, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód, hajtsa végre az alábbi lépéseket:

    ![Wdesk tartomány és URL-címeket egyetlen bejelentkezési adatait](common/idp-intiated.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<subdomain>.wdesk.com/auth/saml/sp/metadata/<instancename>`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<subdomain>.wdesk.com/auth/saml/sp/consumer/<instancename>`

5. Kattintson a **további URL-címet beállítani** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![Wdesk tartomány és URL-címeket egyetlen bejelentkezési adatait](common/metadata-upload-additional-signon.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím:  `https://<subdomain>.wdesk.com/auth/login/saml/<instancename>`

    > [!NOTE]
    > Ezek a értékei nem valódi. Frissítse a tényleges azonosítóját, válasz URL-cím és bejelentkezési URL-ezeket az értékeket. Ezek az értékek első WDesk portálról az egyszeri bejelentkezés konfigurálásakor.

4. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **összevonási metaadatainak XML**  a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

6. Az a **Wdesk beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-wdesk-single-sign-on"></a>Wdesk egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be Wdesk egy biztonsági-rendszergazdaként.

2. Kattintson a bal alsó **rendszergazdai** válassza **Fiókadminisztrátor**:
 
     ![Egyszeri bejelentkezés konfigurálása](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. Wdesk rendszergazdaként, lépjen a **biztonsági**, majd **SAML** > **SAML-beállítások**:

    ![Egyszeri bejelentkezés konfigurálása](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig2.png)

4. A **általános beállítások**, ellenőrizze a **SAML egyszeri bejelentkezés engedélyezése**:

    ![Egyszeri bejelentkezés konfigurálása](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig3.png)

5. A **szolgáltató szolgáltatásismertető**, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig4.png)

      a. Másolás a **bejelentkezési URL-cím** , és illessze be a **bejelentkezési URL-** szövegmezőben, hogy az Azure Portalon.
   
      b. Másolás a **metaadatok URL-címe** , és illessze be a **azonosító** szövegmezőben, hogy az Azure Portalon.
       
      c. Másolás a **ügyfél URL-címe** , és illessze be a **válasz URL-cím** szövegmezőben, hogy az Azure Portalon.
   
      d. Kattintson a **mentése** menti a módosításokat az Azure Portalon.      

6. Kattintson a **IdP-beállítások konfigurálása** megnyitásához **IdP-beállítások szerkesztése** párbeszédpanel. Kattintson a **fájl kiválasztása** keresse meg a **Metadata.xml** mentett fájlt, az Azure Portalon, majd töltse fel.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig5.png)
  
7. Kattintson a **módosítások mentése**.

    ![Egyszeri bejelentkezés konfigurálása](./media/wdesk-tutorial/tutorial_wdesk_ssoconfigsavebutton.png)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be brittasimon@yourcompanydomain.extension. Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Wdesk Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Wdesk**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Wdesk**.

    ![Az alkalmazások listáját a Wdesk hivatkozásra](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-wdesk-test-user"></a>Wdesk tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Wdesk bejelentkezni, akkor ki kell építeni Wdesk be. Wdesk a kiépítés manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be egy biztonsági-rendszergazdaként Wdesk.

2. Navigáljon a **rendszergazdai** > **rendszergazdai fiók**.

     ![Egyszeri bejelentkezés konfigurálása](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. Kattintson a **tagok** alatt **személyek**.

4. Most kattintson **tag hozzáadása** megnyitásához **tag hozzáadása** párbeszédpanel bezárásához. 
   
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/wdesk-tutorial/createuser1.png)  

5. A **felhasználói** szöveget adja meg például a felhasználó felhasználóneve brittasimon@contoso.com kattintson **Folytatás** gombra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/wdesk-tutorial/createuser3.png)

6.  Adja meg adatait alább látható módon:
  
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/wdesk-tutorial/createuser4.png)
 
    a. A **E-mail** szöveget adja meg az e-mailt, például a felhasználó brittasimon@contoso.com.

    b. A **Utónév** szöveget adja meg például a felhasználó utónevét **Britta**.

    c. A **Vezetéknév** szöveget adja meg például a felhasználó vezetékneve **Simon**.

7. Kattintson a **mentése tag** gombra.  

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/wdesk-tutorial/createuser5.png)

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Wdesk csempére kattint, meg kell lehet automatikusan bejelentkezett a Wdesk, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


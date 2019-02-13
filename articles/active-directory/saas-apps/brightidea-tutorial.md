---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Brightidea |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Brightidea között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3adae3e0-f43b-492f-b373-6a512d2d6046
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c839c6cfa15c9a2e0874939d05467050f7e4849
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56189220"
---
# <a name="tutorial-azure-active-directory-integration-with-brightidea"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Brightidea

Ebben az oktatóanyagban elsajátíthatja, hogyan Brightidea integrálása az Azure Active Directory (Azure AD).
Brightidea integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá Brightidea Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezve Brightidea (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Brightidea az Azure AD-integráció konfigurálásához a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* Brightidea egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.


* Támogatja a Brightidea **SP és IDP** által kezdeményezett egyszeri bejelentkezés
* Támogatja a Brightidea **igény szerinti** felhasználók átadása


## <a name="adding-brightidea-from-the-gallery"></a>Brightidea hozzáadása a katalógusból

Az Azure AD integrálása a Brightidea konfigurálásához hozzá kell Brightidea a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Brightidea hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Brightidea**válassza **Brightidea** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Az eredmények listájában Brightidea](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az Brightidea nevű tesztfelhasználó alapján **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó Brightidea hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az Brightidea tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Brightidea egyszeri bejelentkezés konfigurálása](#configure-brightidea-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre Brightidea tesztfelhasználót](#create-brightidea-test-user)**  – egy megfelelője a Britta Simon Brightidea, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Brightidea, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **Brightidea** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszt, ha rendelkezik **szolgáltató metaadatait tartalmazó fájl** és való konfigurálásához a **Identitásszolgáltató** intiated mód hajtsa végre a következő lépéseket:

    a. Kattintson a **metaadatfájl feltöltése**.

    ![Metaadatfájl feltöltése](common/upload-metadata.png)

    b. Kattintson a **mappa embléma** válassza ki a metaadat-fájlt, és kattintson a **feltöltése**.

    ![metaadatfájl kiválasztása](common/browse-upload-metadata.png)

    c. A metaadatait tartalmazó fájl sikeresen feltöltve, miután a **azonosító** és **válasz URL-cím** értékek első Brightidea szakasz szövegmezőben automatikusan:

    ![image](common/idp-intiated.png)

    > [!Note]
    > Ha a **azonosító** és **válasz URL-cím** értékek nem automatikus polulated lekérése, majd adja meg az értékeket manuálisan a követelmény alapján.

5. Kattintson a **további URL-címet beállítani** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![image](common/metadata-upload-additional-signon.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím:  `https://<SUBDOMAIN>.brightidea.com`

4. Az a **beállítás egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **összevonási metaadatainak XML**  a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

6. Az a **Brightidea beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Ad Identifier

    c. Kijelentkezési URL

### <a name="configure-brightidea-single-sign-on"></a>Brightidea egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be rendszergazdai hitelesítő adatok használatával Brightidea.

2. A Brightidea rendszeren lévő, az egyszeri bejelentkezés funkció lekéréséhez, navigáljon a **vállalati telepítő** -> **hitelesítés lap**. Hiba jelenik meg két sub lap található: Hitelesítés kiválasztása és az SAML-profilok.

    ![Brightidea konfiguráció](./media/brightidea-tutorial/configure1.png)

3. Válassza ki **Auth kijelölés**. Alapértelmezés szerint is csak mutatja a két szabványos módszer: Brightidea bejelentkezési és regisztrációs. Ha hozzáadott egyszeri bejelentkezési módszerként azt jelennek meg a listában.

    ![Brightidea konfiguráció](./media/brightidea-tutorial/configure2.png)

4. Válassza ki **SAML-profilok** , és hajtsa végre az alábbi lépéseket:

    ![Brightidea konfiguráció](./media/brightidea-tutorial/configure3.png)

    a. Kattintson a a **metaadatok letöltése** , és töltse fel a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

    b. Kattintson a a **új hozzáadása** gomb alatt a **identitás szolgáltató beállítás** , és hajtsa végre az alábbi lépéseket:
    
    ![Brightidea konfiguráció](./media/brightidea-tutorial/configure4.png)
    
     * Adja meg a **SAML profilnév** mint például: `Azure Ad SSO`
    
     * A **metaadatok feltöltése**, kattintással válassza ki a fájlt, és az Azure-portálról letöltött metaadatfájl feltöltése.

     > [!NOTE]
     > A metaadat-fájl, a többi mező feltöltése után **egyszeri bejelentkezési szolgáltatás, identitásszolgáltató kibocsátója, töltse fel nyilvános kulcs** automatikusan kitölti.

     * Az a **E-mail** szövegmezőbe írja be az értéket, `mail`.
     
     * Az a **képernyő nevét** szövegmezőbe írja be az értéket, `givenName`.
     
     * Kattintson a **módosítások mentése**.  

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** írja be a következőt **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be a **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Brightidea Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Brightidea**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Brightidea**.

    ![Az alkalmazások listáját a Brightidea hivatkozásra](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-brightidea-test-user"></a>Brightidea tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó Brightidea jön létre. Brightidea támogatja a just-in-time-felhasználók létrehozásának, amely alapértelmezés szerint engedélyezve van. Nincs meg ebben a szakaszban a művelet elem. Ha a felhasználó már nem létezik az Brightidea, egy új jön létre a hitelesítés után.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Brightidea csempére kattint, meg kell lehet automatikusan bejelentkezett a Brightidea, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


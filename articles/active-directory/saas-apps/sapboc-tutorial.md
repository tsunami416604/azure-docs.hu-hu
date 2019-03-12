---
title: 'Oktatóanyag: Az Azure Active Directory-integráció az SAP Business objektum-felhővel |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és az SAP Business objektum felhő között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 6c5e44f0-4e52-463f-b879-834d80a55cdf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7af211710d6b1cce2b06999f9d15882b163dbf60
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57766377"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-object-cloud"></a>Oktatóanyag: Az Azure Active Directory-integráció az SAP Business objektum-felhővel

Ebben az oktatóanyagban elsajátíthatja, hogyan SAP Business objektum Felhőbeli integrálása az Azure Active Directory (Azure AD).
SAP Business objektum Felhőbeli integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, aki hozzáféréssel rendelkezik az SAP Business objektum felhőalapú Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy a rendszer automatikusan bejelentkezve az SAP Business objektum Cloud (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Konfigurálni az Azure AD-integráció az SAP Business objektum Cloud, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* SAP Business objektum felhőalapú egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Az SAP Business objektum Cloud támogatja **SP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-sap-business-object-cloud-from-the-gallery"></a>Az SAP Business objektum Cloud hozzáadása a katalógusból

Konfigurálja az integráció az SAP Business objektum Cloud az Azure AD-be, szüksége a katalógus az SAP Business objektum Cloud hozzáadása a felügyelt SaaS-alkalmazások listájában.

**Az SAP Business objektum Cloud hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **SAP Business objektum Cloud**, jelölje be **SAP Business objektum Cloud** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Az SAP Business objektum Cloud a találatok listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az SAP Business objektum Cloud nevű tesztfelhasználó alapján **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó SAP Business objektum felhőben hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az SAP Business objektum Cloud tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[SAP Business objektum felhőalapú egyszeri bejelentkezés konfigurálása](#configure-sap-business-object-cloud-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre az SAP Business objektum Cloud tesztfelhasználót](#create-sap-business-object-cloud-test-user)**  – szeretné, hogy egy Britta Simon megfelelője a felhőben SAP Business objektum, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Konfigurálja az Azure AD egyszeri bejelentkezés az SAP Business objektum-felhővel, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **SAP Business objektum Cloud** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![SAP Business objektum felhőalapú tartomány és URL-címek egyszeri bejelentkezési adatait](common/sp-identifier.png)

    a. Az a **bejelentkezési URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím:
    | |
    |-|-|
    | `https://<sub-domain>.sapanalytics.cloud/` |
    | `https://<sub-domain>.sapbusinessobjects.cloud/` |

    b. Az a **azonosító (entityid)** szövegmezőbe írja be a következő minta használatával URL-cím:
    | |
    |-|-|
    | `<sub-domain>.sapbusinessobjects.cloud` |
    | `<sub-domain>.sapanalytics.cloud` |

    > [!NOTE] 
    > Az alábbi URL-címek értékei csak bemutatásához. Frissítse az értékeket a tényleges bejelentkezési URL-cím és az azonosító URL-t. A bejelentkezési URL-Címének lekéréséhez, lépjen kapcsolatba a [SAP Business objektum felhőalapú ügyfél-támogatási csapatának](https://help.sap.com/viewer/product/SAP_BusinessObjects_Cloud/release/). Az azonosító URL-t kaphat úgy, hogy az SAP Business objektum felhőmetaadatok letölti a felügyeleti konzolon. Ennek a magyarázatát az oktatóanyag későbbi részében.

4. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **összevonási metaadatainak XML**  a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

### <a name="configure-sap-business-object-cloud-single-sign-on"></a>SAP Business objektum felhőalapú egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be az SAP Business objektum felhőalapú vállalati hely rendszergazdaként.

2. Válassza ki **menü** > **rendszer** > **felügyeleti**.
    
    ![Válassza a menüben, majd a rendszer, majd felügyeleti](./media/sapboc-tutorial/config1.png)

3. Az a **biztonsági** lapon jelölje be a **szerkesztése** (toll) ikonra.
    
    ![A biztonság lapon válassza a Szerkesztés ikon](./media/sapboc-tutorial/config2.png)  

4. A **hitelesítési módszer**válassza **SAML egyszeri bejelentkezéses (SSO)**.

    ![SAML egyszeri bejelentkezés a hitelesítési módszer kiválasztása](./media/sapboc-tutorial/config3.png)  

5. Töltse le a service provider metaadatok (1. lépés), jelölje be **letöltése**. A metaadatok fájlban keresse meg és másolja a **entityID** értéket. Az Azure Portalon a a **alapszintű SAML-konfigurációja** párbeszédpanelen illessze be az értéket a **azonosító** mezőbe.

    ![Másolja és illessze be a entityID érték](./media/sapboc-tutorial/config4.png)  

6. A service provider metaadatok (2. lépés) feltölteni a fájlt a letöltött az Azure Portalról a **identitásszolgáltató feltöltése metaadatok**válassza **feltöltése**.  

    ![Identitásszolgáltató feltöltése metaadatok válassza a feltöltés](./media/sapboc-tutorial/config5.png)

7. Az a **felhasználói attribútum** listájához, válassza ki a példányhoz használni kívánt felhasználói attribútum (3. lépés). Az identitásszolgáltató rendeli hozzá a felhasználói attribútum. A felhasználó az oldalon adja meg egy egyéni attribútumot, használja a **egyéni SAML-leképezés** lehetőséget. Másik lehetőségként megadhatja **E-mail** vagy **felhasználói azonosító** felhasználói attribútumként. A példánkban a kiválasztott **E-mail** , mert azt a felhasználói azonosító jogcím-leképezve a **userprincipalname** attribútum a **felhasználói attribútumok & jogcímek** szakasz az Azure Portalon. Ez lehetővé teszi egy egyedi felhasználói e-mailt, amelyet a rendszer elküld az SAP Business objektum felhőalkalmazásba minden sikeres SAML-válasz.

    ![Válassza ki a felhasználói attribútum](./media/sapboc-tutorial/config6.png)

8. A fiók ellenőrzése az identitásszolgáltatóval (4. lépés), az a **bejelentkezési hitelesítő adatok (E-mail)** mezőbe írja be a felhasználó e-mail címét. Ezután válassza ki **fiók ellenőrzése**. A rendszer hozzáadja a felhasználói fiók bejelentkezési hitelesítő adataival.

    ![Adja meg e-mail címét, és válassza ki a fiók ellenőrzése](./media/sapboc-tutorial/config7.png)

9. Válassza ki a **mentése** ikonra.

    ![Mentés ikonra](./media/sapboc-tutorial/save.png)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőtípus **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon Azure egyszeri bejelentkezést az SAP Business objektum Cloud való hozzáférés biztosítása használja.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **SAP Business objektum Cloud**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **SAP Business objektum Cloud**.

    ![Az alkalmazások listáját az SAP Business objektum Cloud hivatkozásra](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-sap-business-object-cloud-test-user"></a>Az SAP Business objektum Cloud tesztfelhasználó létrehozása

Az Azure AD-felhasználók ki kell építeni az SAP Business objektum felhőben megelőzően is bejelentkeznek az SAP Business objektum felhőbe. Az SAP Business objektum Cloud a kiépítés manuális feladat.

A felhasználói fiók kiépítése:

1. Jelentkezzen be rendszergazdaként az SAP Business objektum felhőalapú vállalati hely.

2. Válassza ki **menü** > **biztonsági** > **felhasználók**.

    ![Alkalmazott hozzáadása](./media/sapboc-tutorial/user1.png)

3. Az a **felhasználók** adja hozzá az új felhasználó adatai, jelölje be **+**. 

    ![Felhasználók hozzáadására szolgáló oldala](./media/sapboc-tutorial/user4.png)

    Ezután kövesse az alábbi lépéseket:

    a. Az a **Felhasználóazonosító** mezőbe írja be például a felhasználó a felhasználói azonosító **Britta**.

    b. Az a **UTÓNÉV** mezőbe írja be például a felhasználó utónevét **Britta**.

    c. Az a **Vezetéknév** mezőbe írja be például a felhasználó vezetékneve **Simon**.

    d. Az a **megjelenítendő név** mezőbe írja be például a felhasználó teljes neve **Britta Simon**.

    e. Az a **E-MAIL** mezőbe írja be például a felhasználó e-mail-címe **brittasimon@contoso.com**.

    f. Az a **szerepkörök kiválasztása** lapon válassza ki a megfelelő szerepkört a felhasználó számára, és válassza ki **OK**.

      ![Szerepkör kiválasztása](./media/sapboc-tutorial/user3.png)

    g. Válassza ki a **mentése** ikonra.    

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen az SAP Business objektum Cloud csempére kattint, akkor kell automatikusan megtörténik a, amelynek beállítása egyszeri bejelentkezés az SAP Business objektum felhőbe. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


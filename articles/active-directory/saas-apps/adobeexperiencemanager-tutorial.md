---
title: 'Oktatóanyag: Az Azure Active Directory-integráció az Adobe Experience Manager |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és az Adobe Experience Manager között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 88a95bb5-c17c-474f-bb92-1f80f5344b5a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70096267a151971e9fb3b56a217da58ef9750264
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57405844"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>Oktatóanyag: Az Azure Active Directory-integráció az Adobe Experience Manager

Ebben az oktatóanyagban elsajátíthatja, hogyan Adobe Experience Manager integrálása az Azure Active Directory (Azure AD).
Az Adobe Experience Manager integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, aki hozzáfér az Adobe Experience Manager Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy a rendszer automatikusan bejelentkezett az Adobe Experience Manager (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása az Adobe Experience Manager, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* Az Adobe Experience Manager egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Az Adobe Experience Manager támogatja a **SP és IDP** által kezdeményezett egyszeri bejelentkezés

* Az Adobe Experience Manager támogatja a **igény szerinti** felhasználók átadása

## <a name="adding-adobe-experience-manager-from-the-gallery"></a>Az Adobe Experience Manager hozzáadása a katalógusból

Az Azure AD-be Adobe Experience Manager-integráció konfigurálása szüksége a katalógusból az Adobe Experience Manager hozzáadása a felügyelt SaaS-alkalmazások listájában.

**Az Adobe Experience Manager hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Adobe Experience Manager**, jelölje be **Adobe Experience Manager** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Az Adobe Experience Manager a találatok listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az [alkalmazásnév] nevű tesztfelhasználó alapján **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó [alkalmazásnév] a hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az [alkalmazásnév] tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az Adobe Experience Manager egyszeri bejelentkezés konfigurálása](#configure-adobe-experience-manager-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre az Adobe Experience Manager tesztfelhasználót](#create-adobe-experience-manager-test-user)**  – szeretné, hogy egy megfelelője a Britta Simon az Adobe Experience Manager, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Az Azure AD egyszeri bejelentkezés az [alkalmazásnév] konfigurálásához hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **Adobe Experience Manager** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszra, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód, hajtsa végre az alábbi lépéseket:

    ![Az Adobe Experience Manager tartomány és URL-címeket egyetlen bejelentkezési adatait](common/idp-intiated.png)

    a. Az a **azonosító** szöveg mezőbe írjon be egy egyedi értéket megadhat az AEM-kiszolgálón is.

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<AEM Server Url>/saml_login`

    > [!NOTE]
    > A válasz URL-cím értéke nem valódi. Válasz URL-cím értékét frissítse a tényleges válasz URL-cím. Ez az érték beszerzéséhez forduljon a [Adobe Experience Manager ügyfél-támogatási csapatának](https://helpx.adobe.com/support/experience-manager.html) lekérni ezt az értéket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

5. Kattintson a **további URL-címet beállítani** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![Az Adobe Experience Manager tartomány és URL-címeket egyetlen bejelentkezési adatait](common/metadata-upload-additional-signon.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be az Adobe Experience Manager URL-címe.

6. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

7. Az a **állítsa be az Adobe Experience Manager** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Ad Identifier

    c. Kijelentkezési URL

### <a name="configure-adobe-experience-manager-single-sign-on"></a>Az Adobe Experience Manager egyszeri bejelentkezés konfigurálása

1. Egy másik böngésző ablakában nyissa meg a **Adobe Experience Manager** felügyeleti portálon.

2. Válassza ki **beállítások** > **biztonsági** > **felhasználók**.

    ![Az egyszeri bejelentkezés mentési gomb konfigurálása](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user.png)

3. Válassza ki **rendszergazda** vagy más, megfelelő felhasználó.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin6.png)

4. Válassza ki **Fiókbeállítások** > **kezelése TrustStore**.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_managetrust.png)

5. Alatt **hozzáadása tanúsítvány CER-fájlból**, kattintson a **válassza ki a tanúsítványfájl**. Keresse meg és válassza ki a tanúsítványfájlt, amely már letöltötte az Azure Portalról.

    ![Egyszeri bejelentkezés konfigurálása Mentés gombra](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user2.png)

6. A TrustStore hozzáadta a tanúsítványt. Megjegyzés: a tanúsítvány az alias.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin7.png)

7. Az a **felhasználók** lapon jelölje be **hitelesítési-szolgáltatás**.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin8.png)

8. Válassza ki **Fiókbeállítások** > **létrehozása/kezelése KeyStore**. Hozzon létre KeyStore jelszó megadásával.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin9.png)

9. Lépjen vissza a rendszergazdai képernyős. Válassza ki **beállítások** > **műveletek** > **Webkonzol**.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin1.png)

    Ez megnyitja a konfiguráció lapon.

    ![Az egyszeri bejelentkezés mentési gomb konfigurálása](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin2.png)

10. Keresés **Adobe Gránit SAML 2.0-s hitelesítési kezelő**. Válassza ki a **Hozzáadás** ikonra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin3.png)

11. Az alábbi műveleteket ezen az oldalon.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin4.png)

    a. Az a **elérési** mezőbe írja be **/**.

    b. Az a **Identitásszolgáltató URL-cím** mezőbe írja be a **bejelentkezési URL-cím** , az Azure Portalról másolt érték.

    c. Az a **Identitásszolgáltató tanúsítvány Alias** mezőbe írja be a **tanúsítvány Alias** TrustStore hozzáadott értéket.

    d. Az a **biztonsági a megadott Entitásazonosító** mezőbe írja be az egyedi **Azure Ad-azonosító** érték, amely az Azure Portalon konfigurált.

    e. Az a **helyességi feltétel fogyasztói URL-címe** mezőbe írja be a **válasz URL-cím** érték, amely az Azure Portalon konfigurált.

    f. Az a **kulcs Store jelszavát** mezőbe írja be a **jelszó** KeyStore beállított.

    g. Az a **felhasználói Attribútumazonosító** mezőbe írja be a **Névazonosító** vagy egy másik felhasználói azonosítója, amely az Ön esetében fontos.

    h. Válassza ki **automatikus létrehozás CRX felhasználók**.

    i. Az a **kijelentkezési URL-címe** mezőbe írja be az egyedi **kijelentkezési URL-címe** érték, amely az Azure Portalról kapott.

    j. Kattintson a **Mentés** gombra.

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

Ebben a szakaszban engedélyezze Britta Simon a hozzáférés biztosításával az Adobe Experience Manager Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Adobe Experience Manager**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Adobe Experience Manager**.

    ![Az alkalmazások listáját az Adobe Experience Manager hivatkozásra](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-adobe-experience-manager-test-user"></a>Az Adobe Experience Manager tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon Adobe Experience Manager nevű felhasználó létrehozásához. Ha bejelölte a **automatikus létrehozás CRX felhasználók** lehetőségnél felhasználók automatikusan létrehozza a sikeres hitelesítés után.

Ha szeretné manuálisan létrehozni a felhasználók, a [Adobe Experience Manager ügyfélszolgálathoz](https://helpx.adobe.com/support/experience-manager.html) a felhasználók hozzáadása az Adobe Experience Manager platformon.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen az Adobe Experience Manager csempére kattint, akkor kell automatikusan megtörténik a, az Adobe Experience Manager, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

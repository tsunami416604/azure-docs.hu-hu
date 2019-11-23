---
title: 'Oktatóanyag: Azure Active Directory integráció az Adobe Experience Managerrel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és az Adobe Experience Manager között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 88a95bb5-c17c-474f-bb92-1f80f5344b5a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f39751f40b32c5da24e13d75d2607d7da0a57ad3
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73154115"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>Oktatóanyag: Azure Active Directory integráció az Adobe Experience Managerrel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az Adobe Experience Managert Azure Active Directory (Azure AD) használatával.
Az Adobe Experience Manager és az Azure AD integrálásával a következő előnyöket nyújtja:

* Szabályozhatja az Azure AD-t, aki hozzáfér az Adobe Experience Managerhez.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek az Adobe Experience Managerbe (egyszeri bejelentkezés) az Azure AD-fiókkal.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció az Adobe Experience Managerrel való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* Az Adobe Experience Manager egyszeri bejelentkezést engedélyező előfizetése

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* Az Adobe Experience Manager támogatja **az SP és a identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést

* Az Adobe Experience Manager **csak időben támogatja a** felhasználók üzembe helyezését

## <a name="adding-adobe-experience-manager-from-the-gallery"></a>Az Adobe Experience Manager hozzáadása a gyűjteményből

Az Adobe Experience Manager Azure AD-integrációjának konfigurálásához hozzá kell adnia az Adobe Experience Managert a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Az Adobe Experience Manager katalógusból való hozzáadásához hajtsa végre a következő lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)** , kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **Adobe Experience Manager**kifejezést, válassza az **Adobe Experience Manager** elemet az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Az Adobe Experience Manager az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálja és teszteli az [alkalmazásnév] névvel a **Britta Simon**nevű tesztelési felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és az [alkalmazásnév] kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés az [alkalmazásnév] használatával történő konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. Az **[Adobe Experience Manager egyszeri bejelentkezésének konfigurálása](#configure-adobe-experience-manager-single-sign-on)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre egy Adobe Experience Manager-teszt felhasználót](#create-adobe-experience-manager-test-user)** – hogy a Britta Simon partnere legyen az Adobe Experience Managerben, amely a felhasználó Azure ad-képviseletéhez van társítva.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés az [alkalmazásnév] használatával történő konfigurálásához hajtsa végre a következő lépéseket:

1. Az [Azure Portal](https://portal.azure.com/)az **Adobe Experience Manager** alkalmazás-integráció lapon válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    ![Az Adobe Experience Manager tartomány és URL-címek egyszeri bejelentkezési adatai](common/idp-intiated.png)

    a. Az **azonosító** szövegmezőbe írja be az AEM-kiszolgálón definiált egyedi értéket is.

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<AEM Server Url>/saml_login`

    > [!NOTE]
    > A válasz URL-cím értéke nem valódi. A válasz URL-címének frissítése a tényleges válasz URL-címével. Az érték beszerzéséhez lépjen kapcsolatba az [Adobe Experience Manager ügyfél-támogatási csapatával](https://helpx.adobe.com/support/experience-manager.html) az érték beszerzéséhez. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    ![Az Adobe Experience Manager tartomány és URL-címek egyszeri bejelentkezési adatai](common/metadata-upload-additional-signon.png)

    A **bejelentkezési URL-cím** szövegmezőbe írja be az Adobe Experience Manager-kiszolgáló URL-címét.

6. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

7. Az **Adobe Experience Manager beállítása** szakaszban adja meg a megfelelő URL (eke) t a követelménynek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure ad-azonosító

    c. Kijelentkezési URL

### <a name="configure-adobe-experience-manager-single-sign-on"></a>Az Adobe Experience Manager egyszeri bejelentkezésének konfigurálása

1. Egy másik böngészőablakban nyissa meg az **Adobe Experience Manager** felügyeleti portált.

2. Válassza a **beállítások** > **biztonsági** > **felhasználók**lehetőséget.

    ![Az egyszeri bejelentkezéses mentés gomb konfigurálása](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user.png)

3. Válassza a **rendszergazda** vagy bármely más megfelelő felhasználó lehetőséget.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin6.png)

4. Válassza a **fiókbeállítások** > a **TrustStore kezelése**lehetőséget.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_managetrust.png)

5. A **tanúsítvány hozzáadása a CER-fájlból**területen kattintson a **tanúsítványfájl kiválasztása**elemre. Tallózással keresse meg és válassza ki a tanúsítványt, amelyet már letöltött a Azure Portalból.

    ![Egyszeri bejelentkezéses mentés beállítása gomb](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user2.png)

6. A rendszer hozzáadja a tanúsítványt a TrustStore. Jegyezze fel a tanúsítvány aliasát.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin7.png)

7. A **felhasználók** lapon válassza a **hitelesítés – szolgáltatás**elemet.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin8.png)

8. Válassza a **fiókbeállítások** > a **tároló létrehozása/kezelése**lehetőséget. Hozzon létre egy tárolót a jelszó megadásával.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin9.png)

9. Lépjen vissza a felügyeleti képernyőre. Ezután válassza a **beállítások** > **Operations** > **webkonzol**lehetőséget.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin1.png)

    Ekkor megnyílik a konfigurációs oldal.

    ![Az egyszeri bejelentkezéses mentés gomb konfigurálása](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin2.png)

10. Az **Adobe gránit SAML 2,0 hitelesítési kezelőjének**keresése. Ezután válassza a **Hozzáadás** ikont.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin3.png)

11. Ezen a lapon végezze el a következő műveleteket.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin4.png)

    a. Az **elérési út** mezőbe írja be a **/** .

    b. A **identitásszolgáltató URL-címe** mezőbe írja be a Azure Portalból másolt **bejelentkezési URL** -értéket.

    c. Az **identitásszolgáltató-tanúsítvány aliasa** mezőben adja meg a TrustStore-ben hozzáadott **tanúsítvány-alias** értéket.

    d. A **biztonsági megadott entitás azonosítója** mezőben adja meg a Azure Portal konfigurált egyedi **Azure ad-azonosító** értékét.

    e. Az **állítás fogyasztói szolgáltatás URL-címe** mezőbe írja be a Azure Portalban konfigurált **Válasz URL-címet** .

    f. A **Kulcstároló jelszava** mezőben adja meg a tárolóban beállított **jelszót** .

    g. A **felhasználói ATTRIBÚTUM azonosítója** mezőben adja meg az adott esetben szükséges **azonosítót** vagy más felhasználói azonosítót.

    h. Válassza az **CRX-felhasználók létrehozása**lehetőséget.

    i. A **kijelentkezési URL-cím** mezőbe írja be a Azure Portalból kapott egyedi **kijelentkezési URL-** értéket.

    j. Kattintson a **Mentés** gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A **Felhasználónév** mezőbe írja be a következőt: **brittasimon\@yourcompanydomain. Extension**  
    Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést az Adobe Experience Manager elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd az **Adobe Experience Manager**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza az **Adobe Experience Manager**elemet.

    ![Az Adobe Experience Manager hivatkozása az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-adobe-experience-manager-test-user"></a>Az Adobe Experience Manager tesztelési felhasználójának létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre az Adobe Experience Managerben. Ha a CRX- **felhasználók automatikus létrehozása** lehetőséget választotta, a rendszer automatikusan létrehozza a felhasználókat a sikeres hitelesítés után.

Ha manuálisan szeretné létrehozni a felhasználókat, működjön együtt az [Adobe Experience Manager támogatási csapatával](https://helpx.adobe.com/support/experience-manager.html) a felhasználók hozzáadásához az Adobe Experience Manager platformon.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Amikor az Adobe Experience Manager csempére kattint a hozzáférési panelen, automatikusan be kell jelentkeznie az Adobe Experience Managerbe, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

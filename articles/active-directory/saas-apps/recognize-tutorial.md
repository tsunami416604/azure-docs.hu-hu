---
title: 'Oktatóanyag: Azure Active Directory integráció a felismeréssel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és felismerheti.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: cfad939e-c8f4-45a0-bd25-c4eb9701acaa
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: c0a513ad8fe772839813615d065616f2c911a031
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2019
ms.locfileid: "68943339"
---
# <a name="tutorial-azure-active-directory-integration-with-recognize"></a>Oktatóanyag: Azure Active Directory integráció felismeréssel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az felismerést Azure Active Directory (Azure AD) használatával.
Az Azure AD-vel való felismerés integrálása a következő előnyöket biztosítja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a felismeréshez.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek az Azure AD-fiókjával való felismerésre (egyszeri bejelentkezésre).
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció felismeréssel való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* Egyszeri bejelentkezésre engedélyezett előfizetés felismerése

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A felismerés támogatja az **SP** által kezdeményezett egyszeri bejelentkezést

## <a name="adding-recognize-from-the-gallery"></a>Felismerés hozzáadása a gyűjteményből

Az Azure AD-ben való felismerés integrálásának konfigurálásához fel kell vennie a gyűjteményt a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Ha fel szeretne venni egy felismerést a gyűjteményből, hajtsa végre a következő lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)** , kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a következőt: **felismerés**, válassza a **felismerés** az eredményekből panelt, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Felismerés az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálhatja és tesztelheti a **Britta Simon**nevű tesztelési felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a kapcsolódó felhasználó közötti kapcsolati kapcsolatot kell létrehozni.

Az Azure AD egyszeri bejelentkezés felismeréssel való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. Az **[egyszeri bejelentkezés felismerésének beállítása](#configure-recognize-single-sign-on)** – az alkalmazás oldalának egyszeri bejelentkezési beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. A **[felismerési teszt felhasználójának létrehozása](#create-recognize-test-user)** – hogy a Britta Simon partnere legyen, amely a felhasználó Azure ad-képviseletéhez van csatolva.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés felismeréssel való konfigurálásához hajtsa végre a következő lépéseket:

1. Az [Azure Portal](https://portal.azure.com/)az alkalmazás-integráció **felismerése** lapon válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az alapszintű **SAML-konfiguráció** szakaszban, ha **szolgáltatói metaadatokat tartalmazó fájllal**rendelkezik, hajtsa végre a következő lépéseket:

    >[!NOTE]
    >A **szolgáltatói metaadat-fájlt** az oktatóanyag felismerési **egyszeri bejelentkezés beállítása** szakaszában találja.

    a. Kattintson a **metaadatfájl feltöltése**.

    ![Metaadatfájl feltöltése](common/upload-metadata.png)

    b. Kattintson a **mappa embléma** válassza ki a metaadat-fájlt, és kattintson a **feltöltése**.

    ![metaadat-fájl kiválasztása](common/browse-upload-metadata.png)

    c. A metaadat-fájl feltöltése után az **azonosító** érték az alapszintű SAML-konfiguráció szakaszban automatikusan feltöltve lesz.

    ![A tartomány és az URL-címek egyszeri bejelentkezési adatainak felismerése](common/sp-identifier.png)

     A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://recognizeapp.com/<your-domain>/saml/sso`

    > [!Note]
    > Ha az **azonosító** értéke nem jelenik meg automatikusan, az azonosító értékét a szolgáltatói metaadatok URL-címének megnyitásával, az egyszeri bejelentkezés beállításai szakaszban találja, amelyet a következő témakörben talál: az írásvédett **egyszeri bejelentkezés konfigurálása** oktatóanyag. A bejelentkezési URL-cím értéke nem valós. Frissítse az értéket a tényleges bejelentkezési URL-címmel. Az érték lekéréséhez forduljon az [ügyfél-támogatási csapathoz](mailto:support@recognizeapp.com) . Az Azure Portal alapszintű **SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

6. A felismerés **beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-recognize-single-sign-on"></a>Az egyszeri bejelentkezés felismerésének konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a felismerő bérlőre rendszergazdaként.

2. A jobb felső sarokban kattintson a **menü**elemre. Válassza a **vállalati rendszergazda**lehetőséget.
   
    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalán](./media/recognize-tutorial/tutorial_recognize_000.png)

3. A bal oldali navigációs ablaktáblán kattintson a **Beállítások**elemre.
   
    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalán](./media/recognize-tutorial/tutorial_recognize_001.png)

4. Hajtsa végre az alábbi lépéseket az **SSO-beállítások** szakaszban.
   
    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalán](./media/recognize-tutorial/tutorial_recognize_002.png)
    
    a. Az **SSO engedélyezése**beállításnál válassza **a be**lehetőséget.

    b. Az **identitásszolgáltató entitás-azonosító** szövegmezőbe illessze be a Azure Portalból másolt **Azure ad-azonosító** értékét.
    
    c. Az **SSO cél URL-címe** szövegmezőbe illessze be azt a **bejelentkezési URL-címet** , amelyet a Azure Portalból másolt.
    
    d. A **slo-cél URL-címe** szövegmezőbe illessze be a kijelentkezési **URL-cím** értékét, amelyet a Azure Portal másolt. 
    
    e. Nyissa meg a letöltött **tanúsítvány (Base64)** fájlt a Jegyzettömbben, másolja a vágólapra a tartalmát, majd illessze be a **tanúsítvány** szövegmezőbe.
    
    f. Kattintson a **Beállítások mentése** gombra. 

5. Az **SSO-beállítások** szakasz mellett másolja az URL-címet a **szolgáltatói metaadatok URL-címe**alatt.
   
    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalán](./media/recognize-tutorial/tutorial_recognize_003.png)

6. A metaadat-dokumentum letöltéséhez nyissa meg a **metaadatok URL-hivatkozását** egy üres böngésző alatt. Ezután másolja a EntityDescriptor értéket (entityID) a fájlból, és illessze be az **azonosító** szövegmezőbe az alapszintű **SAML** -konfigurációban Azure Portalon.
    
    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalán](./media/recognize-tutorial/tutorial_recognize_004.png)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A **Felhasználónév** mezőbe írja be brittasimon@yourcompanydomain.extensiona nevet. Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést a felismeréshez való hozzáférés biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **felismerés**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **felismerés**lehetőséget.

    ![Az alkalmazások lista felismerés hivatkozása](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-recognize-test-user"></a>Felismerési teszt felhasználó létrehozása

Annak érdekében, hogy az Azure AD-felhasználók bejelentkezzenek az felismerésbe, fel kell venni őket a felismerésbe. Felismerés esetén a kiépítés manuális feladat.

Ez az alkalmazás nem támogatja a SCIM-létesítést, de a felhasználókra vonatkozó másodlagos felhasználói szinkronizálást is tartalmaz. 

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a vállalati webhely felismerése rendszergazdaként.

2. A jobb felső sarokban kattintson a **menü**elemre. Válassza a **vállalati rendszergazda**lehetőséget.

3. A bal oldali navigációs ablaktáblán kattintson a **Beállítások**elemre.

4. Hajtsa végre a következő lépéseket a **felhasználói szinkronizálás** szakaszban.
   
    ![Új felhasználó](./media/recognize-tutorial/tutorial_recognize_005.png "Új felhasználó")
   
    a. Ha **engedélyezve van a szinkronizálás**, válassza **a be**lehetőséget.
   
    b. A **szinkronizálási szolgáltató**kiválasztása lapon válassza a **Microsoft/Office 365**lehetőséget.
   
    c. Kattintson a **felhasználói szinkronizálás futtatása**elemre.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a felismerő csempére kattint, automatikusan be kell jelentkeznie arra az felismerésre, amelyhez az SSO-t beállította. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


---
title: 'Oktatóanyag: Azure Active Directory integráció az Adobe Sign szolgáltatással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és az Adobe Sign között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.openlocfilehash: b7d1ea6c1ee21970a598855471284130160e1d65
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88537922"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-sign"></a>Oktatóanyag: Azure Active Directory integráció az Adobe Sign szolgáltatással

Ez az oktatóanyag azt ismerteti, hogyan integrálható az Adobe Sign Azure Active Directory (Azure AD) szolgáltatással.
Az Adobe Sign és az Azure AD integrálásával a következő előnyöket nyújtja:

* Megadhatja az Azure AD-ban, hogy ki férhet hozzá az Adobe Sign szolgáltatáshoz.
* Engedélyezheti a felhasználók számára, hogy automatikusan bejelentkezzenek az Adobe Signbe (egyszeri bejelentkezés) az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció az Adobe Sign használatával való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* Az Adobe Sign egyszeri bejelentkezést engedélyező előfizetése

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* Az Adobe Sign támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-adobe-sign-from-the-gallery"></a>Adobe-bejelentkezés hozzáadása a gyűjteményhez

Az Adobe-bejelentkezés az Azure AD-be való integrálásának konfigurálásához fel kell vennie az Adobe-bejelentkezést a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Ha Adobe-bejelentkezést szeretne hozzáadni a katalógushoz, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **Adobe Sign**kifejezést, válassza az **Adobe-bejelentkezés** az eredményekből panelt, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Adobe-bejelentkezés az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az Adobe Sign szolgáltatással konfigurálhatja és tesztelheti egy **Britta Simon**nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és az Adobe-bejelentkezéshez kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés az Adobe Sign használatával történő konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. Az **[Adobe Sign egyszeri bejelentkezés konfigurálása](#configure-adobe-sign-single-sign-on)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Hozzon létre egy Adobe Sign test User](#create-adobe-sign-test-user)** -t, hogy az Azure ad-beli felhasználóhoz kapcsolódó Britta
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés az Adobe Sign használatával történő konfigurálásához hajtsa végre a következő lépéseket:

1. Az [Azure Portal](https://portal.azure.com/)az **Adobe Sign** Application Integration lapon válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![Az Adobe Sign domain és az URL-címek egyszeri bejelentkezési adatai](common/sp-identifier.png)

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<companyname>.echosign.com/`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<companyname>.echosign.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. Az értékek megszerzéséhez lépjen kapcsolatba az [Adobe Sign ügyfél-támogatási csapatával](https://helpx.adobe.com/in/contact/support.html) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

4. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. Az **Adobe-aláírás beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmény szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure ad-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-adobe-sign-single-sign-on"></a>Az Adobe Sign egyszeri bejelentkezés konfigurálása

1. A konfiguráció előtt lépjen kapcsolatba az [Adobe Sign ügyfél-támogatási csapatával](https://helpx.adobe.com/in/contact/support.html) , és adja hozzá a tartományt az Adobe Sign engedélyezési listájában. A következő módon adhatja hozzá a tartományt:

    a. Az [Adobe Sign ügyfél-támogatási csapata](https://helpx.adobe.com/in/contact/support.html) véletlenszerűen generált tokent küld. A tartományhoz a token a következőhöz hasonló lesz: **Adobe-Sign-ellenőrzés = xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx**

    b. Tegye közzé az ellenőrző tokent egy DNS-szöveges rekordban, és értesítse az [Adobe Sign ügyfél támogatási csapatát](https://helpx.adobe.com/in/contact/support.html).
    
    > [!NOTE]
    > Ez eltarthat néhány napig, vagy hosszabb időt is igénybe vehet. Vegye figyelembe, hogy a DNS-propagálás késése azt jelenti, hogy a DNS-ben közzétett érték nem jelenik meg egy órán belül. A rendszergazdának ismernie kell a token DNS-szöveges rekordban való közzétételének módját.
    
    c. Ha a támogatási jegyen keresztül értesíti az [Adobe Sign-ügyfél támogatási csapatát](https://helpx.adobe.com/in/contact/support.html) , a jogkivonat közzététele után érvényesíti a tartományt, és hozzáadja a fiókjához.
    
    d. Általában a következő módon teheti közzé a tokent egy DNS-rekordon:

    * Jelentkezzen be a tartományi fiókjába
    * A DNS-rekord frissítésére szolgáló oldal megkeresése. Ezt a lapot DNS-kezelésnek, névkiszolgálók felügyeletének vagy speciális beállításoknak lehet nevezni.
    * Keresse meg a tartományhoz tartozó TXT-rekordokat.
    * Adjon hozzá egy TXT-rekordot az Adobe által megadott teljes jogkivonat-értékkel.
    * Mentse a módosításokat.

1. Egy másik böngészőablakban jelentkezzen be az Adobe Sign céges webhelyre rendszergazdaként.

1. Az SAML menüben válassza a **Fiókbeállítások**  >  **SAML-beállítások**elemet.
   
    ![Az Adobe Sign SAML-beállítások oldalának képernyőképe](./media/adobe-echosign-tutorial/ic789520.png "Fiók")

1. Az **SAML-beállítások** szakaszban hajtsa végre a következő lépéseket:
  
   ![Az SAML-beállítások képernyőképe](./media/adobe-echosign-tutorial/ic789521.png "SAML-beállítások")
   
   ![Az SAML-beállítások képernyőképe](./media/adobe-echosign-tutorial/ic789522.png "SAML-beállítások")

   a. Az **SAML mód**területen válassza az **SAML kötelező**lehetőséget.
   
   b. Jelölje be a **EchoSign-fiók rendszergazdáinak engedélyezése a EchoSign hitelesítő adataik használatával történő bejelentkezéshez**lehetőséget.
   
   c. A **felhasználó létrehozása**területen válassza az SAML-n **keresztül hitelesített felhasználók automatikus hozzáadása**lehetőséget.

   d. Illessze be az **Azure ad-azonosítót**, amelyet a Azure Portal másolt a **IDENTITÁSSZOLGÁLTATÓ-entitás azonosítója** szövegmezőbe.
    
   e. Illessze be a **bejelentkezési URL-címet**, amelyet Azure Portal a **identitásszolgáltató bejelentkezési URL-címe** szövegmezőbe másolt.
   
   f. Illessze be a **kijelentkezési URL-címet**, amelyet a Azure Portal a **identitásszolgáltató KIJELENTKEZÉSI URL-címe** szövegmezőbe másolt.

   : Nyissa meg a letöltött **tanúsítvány (Base64)** fájlt a Jegyzettömbben. Másolja a tartalmát a vágólapra, majd illessze be a **identitásszolgáltató-tanúsítvány** szövegmezőbe.

   h. Válassza a **módosítások mentése**lehetőséget.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása 

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságainál végezze el a következő lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A **Felhasználónév** mezőbe írja be a **brittasimon \@ yourcompanydomain. Extension** nevet  
    Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon az Azure egyszeri bejelentkezés használatára teszi lehetővé, hogy hozzáférést biztosítson az Adobe Sign szolgáltatáshoz.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd az **Adobe Sign**elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában írja be és válassza az **Adobe Sign**elemet.

    ![Az Adobe Sign hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-adobe-sign-test-user"></a>Adobe Sign test-felhasználó létrehozása

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek az Adobe Signbe, az Adobe Sign szolgáltatásba kell azokat kiépíteni. Ez egy manuális feladat.

>[!NOTE]
>Az Azure AD felhasználói fiókjainak kiépítéséhez az Adobe Sign által biztosított bármely más, az Adobe Sign felhasználói fiók létrehozására szolgáló eszközt vagy API-t használhat. 

1. Jelentkezzen be az **Adobe Sign** céges webhelyre rendszergazdaként.

2. A felső menüben válassza a **fiók**lehetőséget. Ezután a bal oldali ablaktáblán válassza a **felhasználók & csoportok**  >  **új felhasználó létrehozása**lehetőséget.
   
    ![Képernyőkép az Adobe Sign céges webhelyről, a fiókkal, a felhasználókkal &csoportokkal, és új felhasználó kijelölése](./media/adobe-echosign-tutorial/ic789524.png "Fiók")
   
3. Az **új felhasználó létrehozása** szakaszban hajtsa végre a következő lépéseket:
   
    ![Képernyőkép az új felhasználó létrehozása szakaszról](./media/adobe-echosign-tutorial/ic789525.png "Felhasználó létrehozása")
   
    a. Írja be a kapcsolódó szövegmezőbe felépíteni kívánt érvényes Azure AD **-fiók E-mail címét**, **utónevét**és **vezetéknevét** .
   
    b. Válassza a **felhasználó létrehozása**lehetőséget.

>[!NOTE]
>A Azure Active Directory fiók tulajdonosa egy e-mailt kap, amely tartalmazza a fiók megerősítésére szolgáló hivatkozást, mielőtt az aktívvá válna. 

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Amikor a hozzáférési panelen az Adobe Sign csempére kattint, automatikusan be kell jelentkeznie arra az Adobe-aláírásba, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az Automatikus feladattal foglalkozó munkahelygel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az Automatikus munkavégzési munkahely között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a9a7ff71-c389-4169-aafd-d7a505244797
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97a68fd74449ea34438348cbc4c9d09c06d46d71
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73157808"
---
# <a name="tutorial-azure-active-directory-integration-with-autotask-workplace"></a>Oktatóanyag: Az Azure Active Directory integrációja az Automatikus feladattal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az Automatikus feladat munkahelyét az Azure Active Directoryval (Azure AD).
Az Automatikus feladat munkahely és az Azure AD integrálása a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá az Automatikus feladat munkahelyéhez.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve legyenek az Automatikus feladat munkahelyre (Egyszeri bejelentkezés) az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához az Automatikus feladat workplace-lel a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* Automatikus feladat: Workplace egyszeri bejelentkezéssel rendelkező előfizetés
* Automatikus munkavégzési munkahely egyszeri bejelentkezés az engedélyezett előfizetésre
* A Workplace-ben rendszergazdának vagy rendszergazdai rendszergazdának kell lenned.
* Rendszergazdai fiókkal kell rendelkeznie az Azure AD-ben.
* A felhasználók, amelyek ezt a funkciót fogja használni, a Workplace és az Azure AD-n belüli fiókokkal kell rendelkezniük, és mindkét e-mail-címüknek meg kell egyeznie.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* Az Autotask Workplace támogatja az **SP és az IDP** által kezdeményezett sso-t

## <a name="adding-autotask-workplace-from-the-gallery"></a>Automatikus feladat munkahely hozzáadása a galériából

Az Automatikus feladat munkahely azure AD-be való integrálásának konfigurálásához hozzá kell adnia az Automatikus feladat workplace-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha az Automatikus feladatmunkahely alkalmazást a gyűjteményből szeretné hozzáadni, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **Automatikus munka közben**, válassza az Automatikus munkavégzés **impereta lehetőséget** az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Automatikus feladat munkahely az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését az Autotask Workplace szolgáltatással egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, az Azure AD-felhasználó és a kapcsolódó felhasználó közötti kapcsolat Autotask Workplace létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez az Autotask Workplace szolgáltatással a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja az Automatikus munkavégzési munkahely egyszeri bejelentkezést](#configure-autotask-workplace-single-sign-on)** – az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre Autotask Workplace teszt felhasználó](#create-autotask-workplace-test-user)** - egy megfelelője Britta Simon az Automatikus feladat workplace, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához hajtsa végre az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)az **Automatikus munkamunkahely** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban Ha az alkalmazást **IDP** által kezdeményezett módban kívánja konfigurálni, hajtsa végre a következő lépéseket:

    ![Automatikus munkavégzési munkahelyi tartomány és URL-címek egyszeri bejelentkezési adatai](common/idp-intiated.png)

    a. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<subdomain>.awp.autotask.net/singlesignon/saml/metadata`

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://<subdomain>.awp.autotask.net/singlesignon/saml/SSO`

5. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    ![Automatikus munkavégzési munkahelyi tartomány és URL-címek egyszeri bejelentkezési adatai](common/metadata-upload-additional-signon.png)

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<subdomain>.awp.autotask.net/loginsso`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Lépjen kapcsolatba [az Autotask Workplace ügyfél támogatási csapatával,](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

6. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány csoportjában** kattintson a **Letöltés** gombra, ha letöltheti az **összevonási metaadat-XML-t** a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

7. Az **Automatikus feladat beállítása munka eszköz csoportban** másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure-hirdetésazonosító

    c. Kijelentkezés URL-címe

### <a name="configure-autotask-workplace-single-sign-on"></a>Automatikus munkavégzési munkahely egyszeri bejelentkezésének konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Workplace Online szolgáltatásba a rendszergazdai hitelesítő adatokkal.

    > [!Note]
    > Az IdP konfigurálásakor meg kell adni egy altartományt. A helyes altartomány megerősítéséhez jelentkezzen be a Workplace Online szolgáltatásba. Miután bejelentkezett, jegyezze fel az URL-ben lévő altartományt. Az aldomain a "https://" és a ".awp.autotask.net/" közötti rész, és nekünk, eu, ca vagy au-nak kell lennie.

2. Lépjen a **Konfiguráció** > **egyszeri bejelentkezésre,** és hajtsa végre az alábbi lépéseket:

    ![Automatikus munkavégzés egyszeri bejelentkezési konfiguráció](./media/autotaskworkplace-tutorial/tutorial_autotaskssoconfig1.png)

    a. Válassza az **XML metaadatfájl** lehetőséget, majd töltse fel a letöltött **összevonási metaadat-XML-t** az Azure Portalról.

    b. Kattintson **az Enable SSO gombra.**

    ![Automatikus feladat egyszeri bejelentkezés a konfiguráció jóváhagyása](./media/autotaskworkplace-tutorial/tutorial_autotaskssoconfig2.png)

    c. Jelölje be a **Helyes adatok megerősítése, és bízom ebben az IdP-ben** jelölőnégyzetben.

    d. Kattintson a **Jóváhagyás gombra.**

> [!Note]
> Ha segítségre van szükséged az Automatikus feladat munkahely konfigurálásához, kérjük, ezen [az oldalon](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) kaphatsz segítséget a Workplace-fiókkal kapcsolatban.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ez a szakasz célja, hogy hozzon létre egy tesztfelhasználót az Azure Portalon Britta Simon.

1. Az Azure Portalon a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd az **Összes felhasználó**lehetőséget.

    ![A "Felhasználók és csoportok" és a "Minden felhasználó" linkek](common/users.png)

2. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A Felhasználó tulajdonságokban hajtsa végre a következő lépéseket.

    ![A Felhasználó párbeszédpanel](common/user-properties.png)

    a. A **Név** mezőbe írja be **a BrittaSimon**értéket.
  
    b. A **Felhasználónév** mezőbe írja be **a\@brittasimon yourcompanydomain.extension típusú felhasználónév mezőt.**  
    Például: BrittaSimon@contoso.com

    c. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket.

    d. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi Britta Simon számára az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít az Automatikus feladat munkahelyéhez.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza az Automatikus **munkavégzés im2-es lehetőséget.**

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza az **Automatikus munkavégzés imázsa lehetőséget.**

    ![Az Automatikus feladat munkahelye hivatkozás az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-autotask-workplace-test-user"></a>Automatikus feladat munkahelyteszt-felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre az Automatikus feladat munkahelyén. Kérjük, működjön együtt [autotask workplace támogatási csapat](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) a felhasználók hozzáadása az Automatikus munkahelyi platformon.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen az Automatikus munkamunkahely csempére kattint, automatikusan be kell jelentkeznie az Automatikus munkavégzési munkahelybe, amelyhez beállítja az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

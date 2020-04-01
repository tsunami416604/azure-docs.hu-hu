---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az InstaVR Viewer programmal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az InstaVR Viewer között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 13ffa29f-d0a5-4b21-b296-cfd76f380940
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8de94f83c260a86f313a2dd04cdd5a7ae8fc1cda
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73155310"
---
# <a name="tutorial-azure-active-directory-integration-with-instavr-viewer"></a>Oktatóanyag: Az Azure Active Directory integrációja az InstaVR Viewer programmal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az InstaVR Viewert az Azure Active Directoryval (Azure AD).
Az InstaVR Viewer integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá az InstaVR Viewer-hez.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve legyenek az InstaVR Viewer (Single Sign-On) alkalmazásba az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció instaVR-megjelenítővel való konfigurálásához a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* InstaVR Viewer egyszeri bejelentkezéses előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* Az InstaVR Viewer támogatja az **SP** által kezdeményezett SSO-t
* Az InstaVR Viewer támogatja a **Just In Time** felhasználói kiépítést

## <a name="adding-instavr-viewer-from-the-gallery"></a>InstaVR Viewer hozzáadása a galériából

Az InstaVR Viewer azure AD-be való integrációjának konfigurálásához hozzá kell adnia az InstaVR Viewert a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha az InstaVR Viewert a galériából szeretné hozzáadni, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **InstaVR Viewer**kifejezést, válassza az **InstaVR Viewer** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![InstaVR Viewer az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését az InstaVR Viewer nal egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés működéséhez létre kell hozni egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó instaVR Viewer létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez az InstaVR Viewer alkalmazással a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja az InstaVR Viewer Single Sign-On](#configure-instavr-viewer-single-sign-on)** --t az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre InstaVR Viewer teszt felhasználó](#create-instavr-viewer-test-user)** - egy megfelelője Britta Simon in InstaVR Viewer, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához az InstaVR Viewer programmal hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)az **InstaVR Viewer** alkalmazásintegrációs lapján válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![Az InstaVR viewer tartományés url-ek egyszeri bejelentkezési információi](common/sp-identifier.png)

    a. A Bejelentkezés az **URL-cím** mezőbe írja be az URL-címet a következő minta használatával:`https://console.instavr.co/auth/saml/login/<WEBPackagedURL>`

    > [!NOTE]
    > Nincs rögzített minta a Bejelentkezés az URL-címen. Ez akkor jön létre, amikor az InstaVR Viewer ügyfél webes csomagolást végez. Ez egyedülálló minden ügyfél és a csomag. A pontos Sign on URL megszerzéséhez be kell jelentkeznie az InstaVR Viewer példányába, és webes csomagolást kell végeznie.

    b. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő minta használatával:`https://console.instavr.co/auth/saml/sp/<WEBPackagedURL>`

    > [!NOTE]
    > Az azonosító értéke nem valós. Frissítse ezt az értéket a tényleges azonosító értékkel, amelyet az oktatóanyag későbbi részében ismertetünk.

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** csoportban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** és az **összevonási metaadatfájl** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadata-certificatebase64.png)

6. Az **InstaVR-megjelenítő beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure-hirdetésazonosító

    c. Kijelentkezés URL-címe

### <a name="configure-instavr-viewer-single-sign-on"></a>Az InstaVR viewer egyszeri bejelentkezésének konfigurálása

1. Nyisson meg egy új böngészőablakot, és jelentkezzen be rendszergazdaként az InstaVR Viewer vállalati webhelyére.

2. Kattintson a **Felhasználói ikonra,** és válassza **a Fiók lehetőséget.**

    ![Az InstaVR megjelenítő konfigurációja](media/instavr-viewer-tutorial/tutorial-instavr-viewer-account.png)

3. Görgessen le az **SAML hitelesítésig,** és hajtsa végre a következő lépéseket:

    ![Az InstaVR megjelenítő konfigurációja](media/instavr-viewer-tutorial/tutorial-instavr-viewer-configure.png)

    a. Az **SSO URL-cím** szövegmezőbe illessze be a **bejelentkezési URL-értéket,** amelyet az Azure Portalról másolt.

    b. A **Kijelentkezés URL-cím** mezőjébe illessze be a **kijelentkezési URL-cím** értékét, amelyet az Azure Portalról másolt.

    c. Az **Entitásazonosító** szövegdobozába illessze be az **Azure Hirdetési azonosító** értékét, amelyet az Azure Portalról másolt.

    d. A letöltött tanúsítványfájl feltöltéséhez kattintson a **Frissítés gombra.**

    e. A letöltött összevonási metaadatfájl feltöltéséhez kattintson a **Frissítés gombra.**

    f. Másolja az **entitásazonosító** értékét, és illessze be az Azure Portal **alapszintű SAML-konfigurációja** szakasz **azonosító (entitásazonosító)** mezőjébe.

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

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés t engedhozzá az InstaVR Viewer.

1. Az Azure portalon válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza az **InstaVR-megjelenítő**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában írja be és válassza az **InstaVR Viewer**lehetőséget.

    ![Az InstaVR Viewer hivatkozása az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-instavr-viewer-test-user"></a>InstaVR Viewer tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre az InstaVR Viewer alkalmazásban. Az InstaVR Viewer támogatja a just-in-time felhasználói kiépítést, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nincs műveletelem. Ha a felhasználó még nem létezik az InstaVR Viewer alkalmazásban, a hitelesítés után új jelenik meg egy új. Ha bármilyen problémával szembesül, kérjük, forduljon [az InstaVR Viewer támogatási csapatához.](mailto:contact@instavr.co)

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

1. Nyisson meg egy új böngészőablakot, és jelentkezzen be rendszergazdaként az InstaVR Viewer vállalati webhelyére.

2. Válassza a **csomag** lehetőséget a bal oldali navigációs panelen, majd a **Csomag megjelenítése a weben**lehetőséget.

    ![Az InstaVR megjelenítő konfigurációja](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing1.png)

3. Válassza a **Download** (Letöltés) lehetőséget.

    ![Az InstaVR megjelenítő konfigurációja](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing2.png)

4. Válassza **a Hosted Page megnyitása** lehetőséget, miután a rendszer átirányítja az Azure AD-be bejelentkezéshez.

    ![Az InstaVR megjelenítő konfigurációja](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing3.png)

5. Adja meg az Azure AD hitelesítő adatait, hogy sikeresen jelentkezzen be az Azure AD-n keresztül SSO.

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

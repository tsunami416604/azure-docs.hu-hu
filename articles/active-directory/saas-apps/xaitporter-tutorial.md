---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a XaitPorterrel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a XaitPorter között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d33c7cb7-0550-425b-882a-619a713a71b7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/03/2019
ms.author: jeedes
ms.openlocfilehash: 8652073eb3d7d154958566b68fb6e27c35d8da30
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67086518"
---
# <a name="tutorial-azure-active-directory-integration-with-xaitporter"></a>Oktatóanyag: Az Azure Active Directory integrációja a XaitPorterrel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a XaitPortert az Azure Active Directoryval (Azure AD).
A XaitPorter integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a XaitPorterhez.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve legyenek a XaitPorterbe (Single Sign-On) az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a XaitPorterrel a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/)kaphat.
* XaitPorter egyszeri bejelentkezéses előfizetéssel rendelkező előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* Az XaitPorter támogatja az **SP** által kezdeményezett SSO-t

## <a name="adding-xaitporter-from-the-gallery"></a>XaitPorter hozzáadása a galériából

A XaitPorter Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a XaitPortert a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha hozzá szeretné adni a XaitPortert a galériából, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **XaitPorter ( XaitPorter**) parancsot, válassza a **XaitPorter** elemet az eredménypanelről, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![XaitPorter az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a XaitPorterrel egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, egy Azure AD-felhasználó és a kapcsolódó felhasználó xaitporter közötti kapcsolat létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a XaitPorterrel a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja az XaitPorter Single Sign-On](#configure-xaitporter-single-sign-on)** --t az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[XaitPorter tesztfelhasználó létrehozása](#create-xaitporter-test-user)** – a XaitPorterben lévő Britta Simon megfelelője, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához a XaitPorterrel hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **XaitPorter** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![XaitPorter Domain és URL-címek egyszeri bejelentkezési információi](common/sp-identifier.png)

    a. A Bejelentkezés az **URL-cím** mezőbe írja be az URL-címet a következő minta használatával:`https://<subdomain>.xaitporter.com/saml/login`

    b. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<subdomain>.xaitporter.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Bejelentkezési URL-címmel és azonosítóval. Lépjen kapcsolatba [az XaitPorter ügyféltámogatási csapatával,](https://www.xait.com/support/) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány szakaszában** kattintson a Másolás gombra az **Alkalmazásösszevonás metaadat-címének** másolásához és mentéséhez a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)

6. Adja meg az **IP-címet** vagy az **App Federation metaadat-url-címét** a [SmartRecruiters támogatási csapatának,](https://www.smartrecruiters.com/about-us/contact-us/)hogy a XaitPorter biztosíthassa, hogy az IP-cím elérhető legyen a XaitPorter példányból, amely az ő oldalukon konfigurálja az engedélyezési listát. 

### <a name="configure-xaitporter-single-sign-on"></a>XaitPorter egyszeri bejelentkezés konfigurálása

1. A XaitPorter konfigurációjának automatizálásához telepítenie kell a **My Apps Secure Sign-in böngészőbővítményt** **a Bővítmény telepítése**gombra kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Hozzáadása után kiterjesztés a böngésző, kattintson **a Setup XaitPorter** irányítja, hogy a XaitPorter alkalmazás. Itt adja meg a rendszergazdai hitelesítő adatokat a XaitPorterbe való bejelentkezéshez. A böngésző bővítmény automatikusan konfigurálja az alkalmazást, és automatizálja a 3-6.

    ![Beállítási konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a XaitPorter t, nyisson meg egy új böngészőablakot, és jelentkezzen be rendszergazdaként a XaitPorter vállalati webhelyére, és hajtsa végre a következő lépéseket:

4. Kattintson a **Admin**.

    ![Egyszeri bejelentkezés konfigurálása](./media/xaitporter-tutorial/user1.png)

5. Válassza az **Egyszeri bejelentkezés kezelése lehetőséget** a **Rendszerbeállítás** legördülő listából.

    ![Egyszeri bejelentkezés konfigurálása](./media/xaitporter-tutorial/user2.png)

6. Az **EGYSZERI BEJELENTKEZÉS KEZELÉSE** csoportban hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/xaitporter-tutorial/user3.png)

    a. Válassza **az Egyszeri bejelentkezés hitelesítésének engedélyezése**lehetőséget.

    b. Az **Identitásszolgáltató beállításai** szövegmezőben illessze be az **Alkalmazásösszevonás metaadat-címét,** amelyet az Azure Portalról másolt, majd kattintson a **Fetch gombra.**

    c. Válassza **a Felhasználók automatikus létrehozásának engedélyezése**lehetőséget.

    d. Kattintson az **OK** gombra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása 

Ez a szakasz célja, hogy hozzon létre egy tesztfelhasználót az Azure Portalon Britta Simon.

1. Az Azure Portalon a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd az **Összes felhasználó**lehetőséget.

    ![A "Felhasználók és csoportok" és a "Minden felhasználó" linkek](common/users.png)

2. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A Felhasználó tulajdonságokban hajtsa végre a következő lépéseket.

    ![A Felhasználó párbeszédpanel](common/user-properties.png)

    a. A **Név** mezőbe írja be **a BrittaSimon**értéket.
  
    b. A **Felhasználónév** mező brittasimon@yourcompanydomain.extensiontípusa mezőben. Például: BrittaSimon@contoso.com

    c. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket.

    d. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés t a XaitPorter használatával.

1. Az Azure portalon válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza a **XaitPorter**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza az **XaitPorter**lehetőséget.

    ![A XaitPorter hivatkozás az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-xaitporter-test-user"></a>XaitPorter tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a XaitPorter ben. Együttműködve [XaitPorter ügyfél támogatási csapatával](https://www.xait.com/support/) a felhasználók hozzáadása a XaitPorter platformon. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a XaitPorter csempére kattint, automatikusan be kell jelentkeznie arra a XaitPorterbe, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


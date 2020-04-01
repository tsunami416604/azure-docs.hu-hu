---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Trisotech Digital Enterprise Server kiszolgálóval | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Trisotech Digital Enterprise Server között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6d54d20c-eca1-4fa6-b56a-4c3ed0593db0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 56399f99ede611c4a120603cce3a3eede2728c6d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67088264"
---
# <a name="tutorial-azure-active-directory-integration-with-trisotech-digital-enterprise-server"></a>Oktatóanyag: Az Azure Active Directory integrációja a Trisotech Digital Enterprise Server kiszolgálóval

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Trisotech Digital Enterprise Server alkalmazást az Azure Active Directoryval (Azure AD).
A Trisotech Digital Enterprise Server és az Azure AD integrálása a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a Trisotech Digital Enterprise Server kiszolgálóhoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve legyenek a Trisotech Digital Enterprise Server (Single Sign-On) szolgáltatásba az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a Trisotech Digital Enterprise Server kiszolgálóval a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* Trisotech Digital Enterprise Server egyszeri bejelentkezéses előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A Trisotech Digital Enterprise Server támogatja az **SP** által kezdeményezett SSO-t

* A Trisotech Digital Enterprise Server támogatja **a Just In Time** felhasználói kiépítést

## <a name="adding-trisotech-digital-enterprise-server-from-the-gallery"></a>Trisotech Digital Enterprise Server hozzáadása a galériából

A Trisotech Digital Enterprise Server Azure AD-be való integrációjának konfigurálásához hozzá kell adnia a Trisotech Digital Enterprise Server t a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha a Trisotech Digital Enterprise Server alkalmazást a gyűjteményből szeretné hozzáadni, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Trisotech Digital Enterprise Server ( Trisotech Digital Enterprise Server**) kifejezést, válassza a **Trisotech Digital Enterprise Server** elemet az eredménypanelről, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Trisotech Digital Enterprise Server az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a Trisotech Digital Enterprise Server szolgáltatással egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés működéséhez létre kell hozni egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó trisotech digital enterprise server létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a Trisotech Digital Enterprise Server szolgáltatással a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a Trisotech Digital Enterprise Server Single Sign-On --t](#configure-trisotech-digital-enterprise-server-single-sign-on)** az egyszeri bejelentkezési beállítások alkalmazásoldali konfigurálásához.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre Trisotech Digital Enterprise Server teszt felhasználó](#create-trisotech-digital-enterprise-server-test-user)** - egy megfelelője Britta Simon a Trisotech Digital Enterprise Server, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához a Trisotech Digital Enterprise Server kiszolgálóval hajtsa végre az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **Trisotech Digital Enterprise Server** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![Trisotech Digital Enterprise Server Domain és URL-címek egyszeri bejelentkezési információk](common/sp-identifier.png)

    a. A Bejelentkezés az **URL-cím** mezőbe írja be az URL-címet a következő minta használatával:`https://<companyname>.trisotech.com`

    b. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<companyname>.trisotech.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Bejelentkezési URL-címmel és azonosítóval. Lépjen kapcsolatba a [Trisotech Digital Enterprise Server ügyféltámogatási csapatával,](mailto:support@trisotech.com) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

4. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány szakaszában** kattintson a Másolás gombra az **Alkalmazásösszevonás metaadat-címének** másolásához és mentéséhez a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)

### <a name="configure-trisotech-digital-enterprise-server-single-sign-on"></a>A Trisotech Digital Enterprise Server egyszeri bejelentkezésének konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Trisotech Digital Enterprise Server Configuration vállalati webhelyére rendszergazdaként.

2. Kattintson a **Menü ikonra,** majd az **Adminisztráció gombra.**

    ![Egyszeri bejelentkezés konfigurálása](./media/trisotechdigitalenterpriseserver-tutorial/user1.png)

3. Válassza a **Felhasználószolgáltató lehetőséget**.

    ![Egyszeri bejelentkezés konfigurálása](./media/trisotechdigitalenterpriseserver-tutorial/user2.png)

4. A **Felhasználószolgáltató konfigurációja** csoportban hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/trisotechdigitalenterpriseserver-tutorial/user3.png)

    a. A **Hitelesítési módszer**legördülő menüjében válassza a **Biztonságos helyességi feltétel 2 .**

    b. A **Metaadatok URL-címmezőjébe** illessze be az **App Federation metaadat-url-címét,** amelyet az Azure Portal űrlapjának másolásával másolt.

    c. Az **Alkalmazásazonosító** mezőbe írja be az URL-címet `https://<companyname>.trisotech.com`a következő minta használatával: .

    d. Kattintson a **Mentés gombra**

    e. Írja be a tartomány nevet az **Engedélyezett tartományok (üres azt jelenti, mindenki)** szövegmezőbe, akkor automatikusan hozzárendeli a licenceket a felhasználók megfelelnek az engedélyezett tartományok

    f. Kattintson a **Mentés gombra**

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

Ebben a szakaszban engedélyezi Britta Simon számára az Azure egyszeri bejelentkezést a Trisotech Digital Enterprise Server szolgáltatáshoz való hozzáférés biztosításával.

1. Az Azure portalon válassza a **Vállalati alkalmazások**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza a **Trisotech Digital Enterprise Server lehetőséget.**

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Trisotech Digital Enterprise Server**lehetőséget.

    ![A Trisotech Digital Enterprise Server hivatkozása az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-trisotech-digital-enterprise-server-test-user"></a>Trisotech Digital Enterprise Server tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a Trisotech Digital Enterprise Server rendszerben. A Trisotech Digital Enterprise Server támogatja a just-in-time felhasználói kiépítést, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nincs műveletelem. Ha a felhasználó még nem létezik a Trisotech Digital Enterprise Server rendszerben, a hitelesítés után egy új jön létre.

>[!Note]
>Ha manuálisan kell létrehoznia egy felhasználót, forduljon a [Trisotech Digital Enterprise Server támogatási csapatához.](mailto:support@trisotech.com)

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha a Hozzáférési panelen a Trisotech Digital Enterprise Server csempére kattint, automatikusan be kell jelentkeznie arra a Trisotech Digital Enterprise Server kiszolgálóra, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


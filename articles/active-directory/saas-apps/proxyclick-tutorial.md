---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Proxyclick | Microsoft dokumentumok'
description: Ebben az oktatóanyagban megtudhatja, hogyan konfigurálhatja az Azure Active Directory és a Proxyclick közötti egyszeri bejelentkezést.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5c58a859-71c2-4542-ae92-e5f16a8e7f18
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 6a4d1c8a390ebd1194d14c057bb32d3111bf39be
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67093490"
---
# <a name="tutorial-azure-active-directory-integration-with-proxyclick"></a>Oktatóanyag: Az Azure Active Directory integrációja a Proxyclicktel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Proxyclicket az Azure Active Directoryval (Azure AD).
Ez az integráció a következő előnyöket nyújtja:

* Az Azure AD segítségével szabályozhatja, hogy ki férhet hozzá a Proxyclickhez.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve proxyclick (egyszeri bejelentkezés) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti: az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa [el az Alkalmazásokra való egyszeri bejelentkezés az Azure Active Directoryban című témakört.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció proxykattintással való konfigurálásához a következőkre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Egy Azure AD-környezettel, regisztrálhat egy [egyhónapos próbaverzióra.](https://azure.microsoft.com/pricing/free-trial/)
* Proxyclick-előfizetés, amelynek egyszeri bejelentkezése engedélyezve van.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A Proxyclick támogatja az SP által kezdeményezett és az IdP által kezdeményezett egyszeri szolgáltatót.

## <a name="add-proxyclick-from-the-gallery"></a>Proxyclick hozzáadása a gyűjteményből

A Proxyclick azure AD-be való integrálásának beállításához hozzá kell adnia a proxykattintást a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Az [Azure Portalon](https://portal.azure.com)a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget:

    ![Válassza az Azure Active Directory elemet.](common/select-azuread.png)

2. Ugrás **az Enterprise applications** > **Összes alkalmazásra:**

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

3. Alkalmazás hozzáadásához válassza az **Új alkalmazás** lehetőséget az ablak tetején:

    ![Új alkalmazás kiválasztása](common/add-new-app.png)

4. A keresőmezőbe írja be a **Proxyclick ( Proxyclick ) kifejezést.** Válassza a **Proxyclick (Proxyclick) lehetőséget** a keresési eredmények között, majd a **Hozzáadás**lehetőséget.

     ![Keresési eredmények](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést a Proxyclick segítségével egy Britta Simon nevű tesztfelhasználó használatával.
Egyszeri bejelentkezés engedélyezéséhez létre kell hoznia egy kapcsolatot egy Azure AD-felhasználó és a megfelelő felhasználó proxyclick.

Az Azure AD egyszeri bejelentkezéskonfigurálásához és teszteléséhez a Proxyclick segítségével az alábbi lépéseket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** a szolgáltatás felhasználók számára való engedélyezéséhez.
2. **[Konfigurálja a Proxyclick egyszeri bejelentkezést](#configure-proxyclick-single-sign-on)** az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználót](#create-an-azure-ad-test-user)** az Azure AD egyszeri bejelentkezésének teszteléséhez.
4. **[Rendelje hozzá az Azure AD tesztfelhasználót](#assign-the-azure-ad-test-user)** az Azure AD egyszeri bejelentkezés engedélyezéséhez a felhasználó számára.
5. **[Hozzon létre egy Proxyclick tesztfelhasználó,](#create-a-proxyclick-test-user)** amely kapcsolódik a felhasználó Azure AD-ábrázolása.
6. **[Tesztelje az egyszeri bejelentkezést](#test-single-sign-on)** a konfiguráció működésének ellenőrzéséhez.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezése a Proxyclick alkalmazásával az alábbi lépésekkel:

1. Az [Azure Portalon](https://portal.azure.com/)a Proxyclick alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget:

    ![Egyszeri bejelentkezés kiválasztása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza az **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez:

    ![Egyetlen bejelentkezési módszer kiválasztása](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához:

    ![Szerkesztés ikon](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** párbeszédpanelen, ha az alkalmazást IdP által kezdeményezett módban szeretné konfigurálni, tegye a következő lépéseket.

    ![Egyszerű SAML konfiguráció párbeszédpanel](common/idp-intiated.png)

    1. Az **Azonosító** mezőbe írjon be egy URL-címet a következő mintába:
   
       `https://saml.proxyclick.com/init/<companyId>`

    1. A **Válasz URL-címe** mezőbe írjon be egy URL-címet a következő mintába:

       `https://saml.proxyclick.com/consume/<companyId>`

5. Ha az alkalmazást SP által kezdeményezett módban szeretné konfigurálni, válassza **a További URL-címek beállítása**lehetőséget. A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő mintába:
   
   `https://saml.proxyclick.com/init/<companyId>`

    ![Proxyclick-tartomány és URL-címek egyszeri bejelentkezési adatai](common/metadata-upload-additional-signon.png)

    

    > [!NOTE]
    > Ezek az értékek helyőrzők. A tényleges azonosítót, a válasz URL-címét és a bejelentkezési URL-címet kell használnia. Az értékek elérésének lépéseit az oktatóanyag későbbi részében ismertetik.

6. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában válassza a **Tanúsítvány (Base64)** melletti **Letöltés** hivatkozást, és mentse a tanúsítványt a számítógépre:

    ![Tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

7. A **Proxyclick beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények nek megfelelően:

    ![A konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    1. **Bejelentkezési URL.**

    1. **Az Azure Hirdetési azonosítója**.

    1. **Kijelentkezési URL-cím**.

### <a name="configure-proxyclick-single-sign-on"></a>A Proxyclick egyszeri bejelentkezéskonfigurálása

1. Egy új böngészőablakban jelentkezzen be a Proxyclick vállalati webhelyére rendszergazdaként.

2. Válassza **a Fiók & beállítások lehetőséget:**

    ![Fiók & beállítások kiválasztása](./media/proxyclick-tutorial/configure1.png)

3. Görgessen le az **Integrációk** szakaszhoz, és válassza az **SAML**lehetőséget:

    ![SAML kiválasztása](./media/proxyclick-tutorial/configure2.png)

4. Az **SAML** szakaszban tegye a következő lépéseket.

    ![SAML szakasz](./media/proxyclick-tutorial/configure3.png)

    1. Másolja az **SAML-fogyasztó URL-értékét,** és illessze be a **Válasz URL-cím** mezőjébe az Azure Portal **Egyszerű SAML-konfiguráció** párbeszédpanelén.

    1. Másolja az **SAML SSO átirányítási URL-értékét,** és illessze be a **Bejelentkezési URL-címbe** és **az Azonosító** mezőbe az Azure Portal **alapszintű SAML-konfiguráció** párbeszédpanelén.

    1. Az **SAML kérelemmetódus listájában** válassza a **HTTP Redirect (HTTP Átirányítás)** lehetőséget.

    1. A **Kiállító** mezőbe illessze be az Azure AD-azonosító az Azure Portalról másolt **Azure AD-azonosító** értékét.

    1. Az **SAML 2.0 Végpont URL-címe** mezőbe illessze be az Azure Portalról másolt **bejelentkezési URL-címet.**

    1. A Jegyzettömbben nyissa meg az Azure Portalról letöltött tanúsítványfájlt. Illessze be a fájl tartalmát a **Tanúsítvány** mezőbe.

    1. Válassza a **Módosítások mentése lehetőséget.**

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű tesztfelhasználót hoz létre az Azure Portalon.

1. Az Azure Portalon válassza az **Azure Active Directory** lehetőséget a bal oldali ablaktáblában, válassza a **Felhasználók**lehetőséget, majd válassza az **Összes felhasználó**lehetőséget:

    ![Válassza a Minden felhasználó lehetőséget](common/users.png)

2. Válassza az **Új felhasználó** lehetőséget a képernyő tetején:

    ![Új felhasználó kiválasztása](common/new-user.png)

3. A **Felhasználó** párbeszédpanelen tegye a következő lépéseket.

    ![Felhasználó párbeszédpanel](common/user-properties.png)

    1. A **Név** mezőbe írja be **a BrittaSimon**értéket.
  
    1. A **Felhasználónév** mezőbe írja be **BrittaSimon@\<\< vállalattartomány>. kiterjesztés>. ** (Például.) BrittaSimon@contoso.com

    1. Válassza **a Jelszó megjelenítése**lehetőséget, majd írja le a **Jelszó** mezőbe írt értéket.

    1. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít neki a Proxyclick.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, válassza a **Minden alkalmazás**lehetőséget, majd a **Proxyclick**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Proxyclick**lehetőséget.

    ![Alkalmazások listája](common/all-applications.png)

3. A bal oldali ablaktáblában válassza a **Felhasználók és csoportok**lehetőséget:

    ![Felhasználók és csoportok kiválasztása](common/users-groups-blade.png)

4. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** **párbeszédpanelen** a Felhasználók és csoportok lehetőséget.

    ![Felhasználó hozzáadása kiválasztása](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen jelölje ki **a Britta Simon** elemet a felhasználók listájában, majd kattintson az ablak alján található **Kijelölés** gombra.

6. Ha az SAML-feltételben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából. Kattintson az ablak alján található **Kijelölés** gombra.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen válassza a **Hozzárendelés lehetőséget.**

### <a name="create-a-proxyclick-test-user"></a>Proxyclick-tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezhessenek a Proxyclickbe, hozzá kell adnia őket a Proxyclickhez. Manuálisan kell hozzáadnia őket.

Felhasználói fiók létrehozásához tegye a következőket:

1. Jelentkezzen be a Proxyclick vállalati webhelyére rendszergazdaként.

1. Válassza a **Munkatársak** elemet az ablak tetején:

    ![Munkatársak kiválasztása](./media/proxyclick-tutorial/user1.png)

1. Válassza **a Munkatárs hozzáadása**lehetőséget:

    ![Munkatárs hozzáadása kijelölése](./media/proxyclick-tutorial/user2.png)

1. A **Munkatársak hozzáadása** csoportban tegye a következő lépéseket.

    ![Munkatárs hozzáadása szakasz](./media/proxyclick-tutorial/user3.png)

    1. Az **E-mail** mezőbe írja be a felhasználó e-mail címét. Ebben az **esetben, brittasimon\@contoso.com**.

    1. Az **Utónév mezőbe** írja be a felhasználó keresztnevét. Ebben az **esetben, Britta**.

    1. A **Vezetéknév** mezőbe írja be a felhasználó vezetéknevét. Ebben az esetben, **Simon**.

    1. Válassza **a Felhasználó hozzáadása**lehetőséget.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Most kell tesztelniaz Azure AD egyszeri bejelentkezési konfiguráció a hozzáférési panel használatával.

Amikor a Hozzáférési panelen kiválasztja a Proxyclick csempét, automatikusan be kell jelentkeznie a Proxyclick-példányba, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt az [Alkalmazások portálon található Alkalmazások elérése és használata című témakörben](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)talál.

## <a name="additional-resources"></a>További források

- [Oktatóanyagok SaaS-alkalmazások az Azure Active Directoryval való integrálásához](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


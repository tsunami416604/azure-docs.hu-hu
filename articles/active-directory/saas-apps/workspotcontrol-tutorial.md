---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Workspot Control vezérlővel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directoryhoz és a Workspot-vezérlőhöz.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3ea8e4e9-f61f-4f45-b635-b0e306eda3d1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: jeedes
ms.openlocfilehash: 086ec95531b01477be56d4b1a19d189f167a020f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67086681"
---
# <a name="tutorial-azure-active-directory-integration-with-workspot-control"></a>Oktatóanyag: Az Azure Active Directory integrációja a Workspot vezérlővel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Workspot-vezérlőt az Azure Active Directoryval (Azure AD). Ha integrálja a Workspot Controlt az Azure AD-vel, a következőket teheti:

* Az Azure AD segítségével szabályozhatja, hogy ki férhet hozzá a Workspot-vezérlőhöz.
* Lehetővé teszi a felhasználók számára, hogy automatikusan jelentkezzen be a Workspot Control (egyszeri bejelentkezés [SSO]) segítségével az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen: az Azure Portalon.

Az Azure AD-vel való SaaS-alkalmazások integrációjáról az [Azure AD-ben található alkalmazásokegyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)című témakörben talál további információt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció és a Workspot Control konfigurálásához a következő dolgokra van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/)kaphat.

* Workspot Control egyszeri bejelentkezés t-enabled előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

> [!Note]
> A Workspot Control támogatja az SP által kezdeményezett és az IDP által kezdeményezett egyszeri szolgáltatót.


## <a name="adding-workspot-control-from-the-gallery"></a>Workspot-vezérlő hozzáadása a gyűjteményből

A Workspot Control azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Workspot Controlt a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha a gyűjteményből szeretné hozzáadni a Workspot vezérlőt, kövesse az alábbi lépéseket:**

1. Az [Azure Portal](https://portal.azure.com)bal oldali ablaktáblájában válassza az Azure **Active Directory**lehetőséget.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások lehetőséget,** és válassza **az Összes alkalmazás lehetőséget.**

    ![A Vállalati alkalmazások ablaktábla](common/enterprise-applications.png)

3. Válassza az **Új alkalmazás** lehetőséget az ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a Workspot Control ( **Munkapont vezérlés**) kifejezést, válassza az eredménypanelen a **Munkapont vezérlése** elemet, majd kattintson a **Hozzáadás gombra.**

     !["Hozzáadás a galériából" ablak](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést a Workspot Control egy tesztfelhasználó, Britta Simon számára.
Egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó a Workspot Control.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a Workspot-vezérlővel a következő feladatokat kell elvégeznie:

1. [Konfigurálja az Azure AD egyszeri bejelentkezést,](#configure-azure-ad-single-sign-on) hogy a felhasználók használhassák a funkciót.
2. [Konfigurálja a Workspot Control egyszeri bejelentkezést](#configure-workspot-control-single-sign-on) az alkalmazás oldalon az egyszeri bejelentkezési beállítások konfigurálásához.
3. [Hozzon létre egy Azure AD-tesztfelhasználót](#create-an-azure-ad-test-user) az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simon számára.
4. [Rendelje hozzá az Azure AD tesztfelhasználót,](#assign-the-azure-ad-test-user) hogy Britta Simon használhassa az Azure AD egyszeri bejelentkezést.
5. [Hozzon létre egy Workspot Control teszt felhasználó](#create-a-workspot-control-test-user) létrehozásához egy megfelelője Britta Simon a Workspot Control, amely kapcsolódik a felhasználó Azure AD-ábrázolása.
6. [Tesztelje az egyszeri bejelentkezést](#test-single-sign-on) a konfiguráció működésének ellenőrzéséhez.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésnek a Workspot Control vezérléssel történő konfigurálásához kövesse az alábbi lépéseket:

1. Az [Azure Portal](https://portal.azure.com/) **Workspot Control** alkalmazásintegrációs lapján válassza az Egyszeri **bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** ablakban válassza az **SAML** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési kiválasztó módszer ablak kijelölése](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** (ceruza) ikonra az **alapvető SAML-konfiguráció**eléréséhez.

    ![Az "Egyszerű SAML-konfiguráció" elem ben kiemelt szerkesztési ikon](common/edit-urls.png)

4. Ha az **egyszerű SAML-konfiguráció** szakaszban az alkalmazást IDP által kezdeményezett módban szeretné konfigurálni, kövesse az alábbi lépéseket:

    ![Workspot Vezérlő tartomány és URL egyszeri bejelentkezési információk](common/idp-intiated.png)

    1. Az **azonosító** szövegmezőjébe írjon be egy URL-címet a következő mintában:<br/>
    ***https://<<i> </i>INSTANCENAME>-saml.workspot.com/saml/metadata***

    1. A **válasz URL-címének** mezőjébe írjon be egy URL-címet a következő mintában:<br/>
    ***https://<<i> </i>INSTANCENAME>-saml.workspot.com/saml/assertion***

5. Ha az alkalmazást SP által kezdeményezett módban szeretné konfigurálni, válassza **a További URL-címek beállítása**lehetőséget.

    ![Workspot Vezérlő tartomány és URL egyszeri bejelentkezési információk](common/metadata-upload-additional-signon.png)

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő mintában:<br/>
    ***https://<<i> </i>INSTANCENAME>-saml.workspot.com/***

    > [!NOTE]
    > Ezek az értékek nem valósak. Cserélje le ezeket az értékeket a tényleges azonosítóra, a válasz URL-címére és a bejelentkezési URL-re. Ezeknek az értékeknek a beszerezéséhez lépjen kapcsolatba a [Workspot Control ügyféltámogatási csapatával.](mailto:support@workspot.com) Vagy is hivatkozhat a mintákat az Azure Portal **alapszintű SAML konfigurációs** szakaszában.

6. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában válassza a **Letöltés a** **tanúsítvány letöltéséhez (Base64)** lehetőséget a rendelkezésre álló lehetőségek közül a követelményeknek megfelelően. Mentse a számítógépre.

    ![A tanúsítvány (Base64) letöltési hivatkozása](common/certificatebase64.png)

7. A **Workspot-vezérlő beállítása** csoportban másolja a megfelelő URL-címeket az ön igényeinek megfelelően:

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    - **Bejelentkezés i URL-címe**

    - **Azure Hirdetés-azonosító**

    - **Kijelentkezés URL-címe**

### <a name="configure-workspot-control-single-sign-on"></a>A Workspot-vezérlő egyszeri bejelentkezéskonfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Workspot-vezérlőbe biztonsági rendszergazdaként.

2. A lap tetején lévő eszköztáron válassza a **Beállítás,** majd az **SAML lehetőséget.**

    ![Beállítási lehetőségek](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_setup.png)

3. A **Biztonsági helyességifeltétel-jelölő nyelv konfigurációja** ablakban hajtsa végre az alábbi lépéseket:
 
    ![Biztonsági helyességifeltétel-jelölési nyelv konfigurációja ablak](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_saml.png)

    1. Az **Entitásazonosító** mezőbe illessze be az **Azure-hirdetési azonosítót,** amelyet az Azure Portalról másolt.

    1. A **Signon Service URL-címe** mezőbe illessze be az Azure Portalról másolt **bejelentkezési URL-címet.**

    1. A **Kijelentkezési szolgáltatás URL-címe** mezőbe illessze be az Azure Portalról másolt **kijelentkezési URL-címet.**

    1. Válassza a **Fájl frissítése** az X.509-tanúsítványba az Azure Portalról letöltött base-64 kódolású tanúsítványba való feltöltéshez.

    1. Kattintson a **Mentés** gombra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban hozzon létre egy tesztfelhasználót az Azure Portalon.

1. Az Azure Portal bal oldali ablaktáblájában válassza az **Azure Active Directory**, a **Felhasználók**, majd az Összes **felhasználó lehetőséget.**

    ![A "Felhasználók és csoportok" és a "Minden felhasználó" linkek](common/users.png)

2. Válassza az **Új felhasználó** lehetőséget az ablak tetején.

    ![Az "Új felhasználó" gomb](common/new-user.png)

3. A felhasználó tulajdonságai között hajtsa végre az alábbi lépéseket:

    ![A Felhasználó tulajdonságai ablak](common/user-properties.png)

    1. A **Név** mezőbe írja be **a BrittaSimon**értéket.
  
    1. A **Felhasználónév** mezőbe írja be a **brittasimon@* yourcompanydomain.extension***. Írja be például a ** BrittaSimon@contosobeírását.<i> </i> com**.

    1. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet. Ezután írja le a **Jelszó** mezőben megjelenő értéket.

    1. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban hozzáférést biztosít Britta Simonnak a Workspot-vezérlőhöz, hogy az Azure egyszeri bejelentkezést használhassa.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**, Az **összes alkalmazás**lehetőséget, majd **a Workspot-vezérlő t.**

    ![A Vállalati alkalmazások ablaktábla](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Workspot Control lehetőséget.**

    ![A Workspot Control hivatkozás a Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Válassza a **Felhasználó hozzáadása** gombot. Ezután válassza a **Felhasználók és csoportok** lehetőséget a Hozzárendelés **hozzáadása** ablakban.

    ![A "Hozzárendelés hozzáadása" ablak](common/add-assign-user.png)

5. A **Felhasználók és csoportok** ablakban válassza **a Britta Simon** elemet a **Felhasználók** listából. Ezután kattintson a **Kiválasztás** elemre.

6. Ha az SAML-feltételben szerepkörértéket vár, válassza ki a felhasználó megfelelő szerepkörét a **Szerepkör kiválasztása** ablaklistájából. Ezután kattintson alul a **Kijelölés** gombra.

7. A **Hozzárendelés hozzáadása** ablakban válassza a **Hozzárendelés**lehetőséget.

### <a name="create-a-workspot-control-test-user"></a>Workspot-vezérlő tesztfelhasználójának létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezhessenek a Workspot-vezérlőbe, ki kell építeni őket a Workspot-vezérlőbe. A kiépítés manuális feladat.

**Felhasználói fiók kiépítéséhez kövesse az alábbi lépéseket:**

1. Jelentkezzen be a Workspot Vezérlőbe biztonsági rendszergazdaként.

2. A lap tetején lévő eszköztáron válassza a **Felhasználók,** majd a **Felhasználó hozzáadása lehetőséget.**

    !["Felhasználók" beállításai](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_adduser.png)

3. Az **Új felhasználó hozzáadása** ablakban hajtsa végre az alábbi lépéseket:

    !["Új felhasználó hozzáadása" ablak](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_addnewuser.png)

    1. A **Keresztnév** mezőbe írja be a felhasználó keresztnevét, például **Britta**.

    1. A **Vezetéknév** mezőbe írja be a felhasználó vezetéknevét, például **Simon**.

    1. Az **E-mail** mezőbe írja be a felhasználó e-mail címét, például ** Brittasimon@contoso.<i> </i> com**.

    1. Válassza ki a megfelelő felhasználói szerepkört a **Szerepkör** legördülő listából.

    1. Válassza ki a megfelelő felhasználói csoportot a **Csoport** legördülő listából.

    1. Válassza **a Felhasználó hozzáadása**lehetőséget.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban teszteljük az Azure AD egyszeri bejelentkezési konfigurációnkat a *Hozzáférési panelen*keresztül.

Amikor a Hozzáférési panelen a **Workspot Vezérlőcsempére** kattint, automatikusan be kell jelentkeznie arra a Workspot vezérlőbe, amelyhez az SSO-t beállította. További információkért lásd: [Bevezetés a Hozzáférési panel használatába](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access).

## <a name="additional-resources"></a>További források

- [Oktatóanyagok SaaS-alkalmazások az Azure Active Directoryval való integrálásához](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

- [Egyszeri bejelentkezés az alkalmazásokba az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

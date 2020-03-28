---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Percolate programmal | Microsoft dokumentumok'
description: Ebben az oktatóanyagban megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Percolate között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 355f9659-b378-44c9-aa88-236e9b529a53
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: a6c1f893757baf1e6c85420b31997a5073cff684
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67094598"
---
# <a name="tutorial-azure-active-directory-integration-with-percolate"></a>Oktatóanyag: Az Azure Active Directory integrációja a Percolate szolgáltatással

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Percolate-t az Azure Active Directoryval (Azure AD).

Ez az integráció a következő előnyöket nyújtja:

* Az Azure AD segítségével szabályozhatja, hogy ki férhet hozzá a Percolate.You can use Azure AD to control who has access to Percolate.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve percolate (egyszeri bejelentkezés) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti: az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa [el az Alkalmazásokra való egyszeri bejelentkezés az Azure Active Directoryban című témakört.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a kezdés előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a Percolate szolgáltatással rendelkeznie kell:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/)kaphat.
* Percolate előfizetés, amely egyszeri bejelentkezés engedélyezve van.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A Percolate támogatja az SP által kezdeményezett és az IdP által kezdeményezett egyszeri szolgáltatót.

## <a name="add-percolate-from-the-gallery"></a>Perkolát hozzáadása a gyűjteményből

A Percolate azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Percolate-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Az [Azure Portalon](https://portal.azure.com)a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget:

    ![Válassza az Azure Active Directory elemet.](common/select-azuread.png)

2. Ugrás **az Enterprise applications** > **Összes alkalmazásra:**

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Alkalmazás hozzáadásához válassza az **Új alkalmazás** lehetőséget az ablak tetején:

    ![Új alkalmazás kiválasztása](common/add-new-app.png)

4. A keresőmezőbe írja be a **Percolate ( Percolate**) kifejezést. A keresési eredmények között válassza a **Percolátok,** majd a **Hozzáadás**lehetőséget.

     ![Keresési eredmények](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést a Percolate-tel egy Britta Simon nevű tesztfelhasználó használatával.
Egyszeri bejelentkezés engedélyezéséhez létre kell hoznia egy azure-felhasználó és a megfelelő felhasználó percolate közötti kapcsolatot.

Az Azure AD egyszeri bejelentkezéskonfigurálásához és teszteléséhez a Percolate szolgáltatással hajtsa végre az alábbi lépéseket:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** a szolgáltatás felhasználók számára való engedélyezéséhez.
2. **[Konfigurálja perkolate egyszeri bejelentkezés](#configure-percolate-single-sign-on)** az alkalmazás oldalán.
3. **[Hozzon létre egy Azure AD-tesztfelhasználót](#create-an-azure-ad-test-user)** az Azure AD egyszeri bejelentkezésének teszteléséhez.
4. **[Rendelje hozzá az Azure AD tesztfelhasználót](#assign-the-azure-ad-test-user)** az Azure AD egyszeri bejelentkezés engedélyezéséhez a felhasználó számára.
5. **[Hozzon létre egy perkoliátteszt-felhasználót,](#create-a-percolate-test-user)** amely a felhasználó Azure AD-ábrázolásához kapcsolódik.
6. **[Tesztelje az egyszeri bejelentkezést](#test-single-sign-on)** a konfiguráció működésének ellenőrzéséhez.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezéskonfigurálásához a Percolate szolgáltatással az alábbi lépésekkel:

1. Az [Azure Portalon](https://portal.azure.com/)a **Percolate** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget:

    ![Egyszeri bejelentkezés kiválasztása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza az **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez:

    ![Egyetlen bejelentkezési módszer kiválasztása](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához:

    ![Szerkesztés ikon](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** párbeszédpanelen nem kell semmilyen műveletet végrehajtania az alkalmazás IdP által kezdeményezett módban történő konfigurálásához. Az alkalmazás már integrálva van az Azure-ral.

    ![Átjárva tartomány és URL egyszeri bejelentkezési információk](common/preintegrated.png)

5. Ha az alkalmazást SP által kezdeményezett módban szeretné konfigurálni, válassza **a További URL-címek beállítása lehetőséget,** és a **Bejelentkezési URL-cím** mezőbe írja be a következőt: **https://percolate.com/app/login**

   ![Átjárva tartomány és URL egyszeri bejelentkezési információk](common/metadata-upload-additional-signon.png)
6. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány csoportban** kattintson a **Másolás** ikonra az **Alkalmazásösszevonás metaadat-címének**másolásához. Mentse ezt az URL-címet.

    ![Az App Federation metaadatok URL-címének másolása](common/copy-metadataurl.png)

7. A **Percolate beállítása** szakaszban másolja a megfelelő URL-címeket az Ön igényei nek megfelelően.

    ![A konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    1. **Bejelentkezési URL.**

    1. **Az Azure Hirdetési azonosítója**.

    1. **Kijelentkezési URL-cím**.

### <a name="configure-percolate-single-sign-on"></a>Perkolát egyszeri bejelentkezés konfigurálása

1. Egy új böngészőablakban jelentkezzen be a Percolate alkalmazásba rendszergazdaként.

2. A kezdőlap bal oldalán válassza a **Beállítások**lehetőséget:
    
    ![Beállítások kiválasztása](./media/percolate-tutorial/configure01.png)

3. A bal oldali ablaktáblában válassza az **SSO** lehetőséget a **Szervezet**csoportban:

    ![Válassza az SSO lehetőséget a Szervezet csoportban](./media/percolate-tutorial/configure02.png)

    1. A **Bejelentkezési URL-cím** mezőbe illessze be az Azure Portalról másolt **bejelentkezési URL-címet.**

    1. Az **Entitásazonosító** mezőbe illessze be az Azure AD-azonosító az Azure Portalról másolt **Azure AD-azonosító** értékét.

    1. A Jegyzettömbben nyissa meg az Azure Portalról letöltött 64-es alapú kódolású tanúsítványt. Másolja a tartalmát, és illessze be az **x509-es tanúsítványok** mezőbe.

    1. Az **E-mail attribútum** mezőbe írja be az **e-mail címet.**

    1. Az **Identitásszolgáltató metaadat-URL-címe** nem kötelező mező. Ha egy **App Federation metaadat-URL-címet** másolt az Azure Portalról, beillesztheti ebbe a mezőbe.

    1. Az **AuthNRequests aláírása?** listában válassza a **Nem**lehetőséget.

    1. Az **Egyszeri bejelentkezés automatikus kiépítésének engedélyezése** listában válassza a **Nem**lehetőséget.

    1. Kattintson a **Mentés** gombra.

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

Ebben a szakaszban engedélyezi Britta Simon az Azure AD egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít neki a Percolate.

1. Az Azure Portalon válassza a **Nagyvállalati alkalmazások**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd a **Percolate**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Percolate**lehetőséget.

    ![Alkalmazások listája](common/all-applications.png)

3. A bal oldali ablaktáblában válassza a **Felhasználók és csoportok**lehetőséget:

    ![Felhasználók és csoportok kiválasztása](common/users-groups-blade.png)

4. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** **párbeszédpanelen** a Felhasználók és csoportok lehetőséget.

    ![Felhasználók és csoportok kiválasztása](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen jelölje ki **a Britta Simon** elemet a felhasználók listájában, majd kattintson a képernyő alján található **Kijelölés** gombra.

6. Ha az SAML-feltételben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából. Kattintson a **kijelölés** gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen válassza a **Hozzárendelés lehetőséget.**

### <a name="create-a-percolate-test-user"></a>Átjárta tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezhessenek a Percolate-be, hozzá kell adnia őket a Percolate-hoz. Manuálisan kell hozzáadnia őket.

Felhasználói fiók létrehozásához tegye a következőket:

1. Jelentkezzen be a Percolate rendszergazdaként.

2. A bal oldali ablaktáblában válassza a **Felhasználók** lehetőséget a **Szervezet**csoportban. Válassza **az Új felhasználók lehetőséget:**

    ![Új felhasználók kiválasztása](./media/percolate-tutorial/configure03.png)

3. A **Felhasználók létrehozása** lapon tegye a következő lépéseket.

    ![Felhasználók létrehozása lap](./media/percolate-tutorial/configure04.png)

    1. Az **E-mail** mezőbe írja be a felhasználó e-mail címét. Például: brittasimon@contoso.com.

    1. A **Teljes név** mezőbe írja be a felhasználó nevét. Például **Brittasimon**.

    1. Válassza **a Felhasználók létrehozása**lehetőséget.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Most kell tesztelniaz Azure AD egyszeri bejelentkezési konfiguráció a hozzáférési panel használatával.

Amikor a Hozzáférési panelen kiválasztja a Percolate csempét, automatikusan be kell jelentkeznie a Percolate példányba, amelyhez az SSO-t beállította. További információt az Alkalmazások portálon lévő [Alkalmazások elérése és használata című témakörben](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)talál.

## <a name="additional-resources"></a>További források

- [Oktatóanyagok SaaS-alkalmazások az Azure Active Directoryval való integrálásához](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
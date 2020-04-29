---
title: 'Oktatóanyag: Azure Active Directory integráció a Everbridge-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Everbridge között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 58d7cd22-98c0-4606-9ce5-8bdb22ee8b3e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.openlocfilehash: 60463a00c6864bed7b3a18e816ef0143d3573782
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "67103255"
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>Oktatóanyag: Azure Active Directory integráció a Everbridge

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Everbridge a Azure Active Directory (Azure AD) szolgáltatással.
Ha integrálja az Everbridge-t az Azure AD-vel, a következőket teheti:

* A Everbridge-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teszi, hogy a felhasználók automatikusan bejelentkezzenek a Everbridge az Azure AD-fiókjával. A hozzáférés-vezérlés neve egyszeri bejelentkezés (SSO).
* A fiókokat egy központi helyen kezelheti a Azure Portal használatával.
További információ az Azure AD-vel való szolgáltatott szoftver-(SaaS-) alkalmazások integrálásáról: [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció Everbridge való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Egyszeri bejelentkezést használó Everbridge-előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Everbridge támogatja a IDENTITÁSSZOLGÁLTATÓ által kezdeményezett egyszeri bejelentkezést.

## <a name="add-everbridge-from-the-azure-marketplace"></a>Everbridge hozzáadása az Azure Marketplace-ről

A Everbridge Azure AD-be való integrálásának konfigurálásához adja hozzá a Everbridge-t az Azure Marketplace-ből a felügyelt SaaS-alkalmazások listájához.

Ha Everbridge szeretne hozzáadni az Azure piactéren, kövesse az alábbi lépéseket.

1. A [Azure Portal](https://portal.azure.com)bal oldali navigációs paneljén válassza a **Azure Active Directory**lehetőséget.

    ![Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**elemre, majd válassza a **minden alkalmazás**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához válassza a párbeszédpanel tetején található **új alkalmazás** lehetőséget.

    ![Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Everbridge**kifejezést. Válassza az **Everbridge** lehetőséget az eredmények panelen, majd válassza a **Hozzáadás**lehetőséget.

     ![Everbridge az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az Everbridge-mel konfigurálja és teszteli a Britta Simon alapján.
Az egyszeri bejelentkezés működéséhez hozzon létre egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Everbridge-ben.

Az Azure AD egyszeri bejelentkezés Everbridge való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

- Az [Azure ad egyszeri bejelentkezés konfigurálásával](#configure-azure-ad-single-sign-on) engedélyezheti a felhasználók számára a funkció használatát.
- [Konfigurálja az Everbridge-t Everbridge Manager-portál egyszeri bejelentkezéssel](#configure-everbridge-as-everbridge-manager-portal-single-sign-on) az alkalmazás oldalának egyszeri bejelentkezési beállításainak konfigurálásához.
- [Konfigurálja az Everbridge-t Everbridge-tag portál egyszeri bejelentkezéssel](#configure-everbridge-as-everbridge-member-portal-single-sign-on) az alkalmazás oldalának egyszeri bejelentkezési beállításainak konfigurálásához.
- [Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user) az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
- [Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user) , hogy a Britta Simon engedélyezze az Azure ad egyszeri bejelentkezés használatát.
- [Hozzon létre egy Everbridge](#create-an-everbridge-test-user) , amely a felhasználó Azure ad-Britta kapcsolódó, Simon-beli, Everbridge-beli partnere.
- [Tesztelje az egyszeri bejelentkezést](#test-single-sign-on) annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés Everbridge való konfigurálásához kövesse az alábbi lépéseket.

1. A [Azure Portal](https://portal.azure.com/) **Everbridge** alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri **bejelentkezési módszer kiválasztása** párbeszédpanelen válassza ki az **SAML/ws-fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon válassza a **Szerkesztés** lehetőséget az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

    >[!NOTE]
    >Konfigurálja az alkalmazást Manager-portálként *vagy* a Azure Portal és a Everbridge portálon a tag portálként.

4. A **Everbridge** alkalmazás **Everbridge Manager-portálként**való konfigurálásához az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![Everbridge tartomány és URL-címek egyszeri bejelentkezési adatai](common/idp-intiated.png)

    a. Az **azonosító** mezőben adja meg a mintát követő URL-címet`https://sso.everbridge.net/<API_Name>`

    b. A **Válasz URL-címe** mezőbe írja be a mintát követő URL-címet`https://manager.everbridge.net/saml/SSO/<API_Name>/alias/defaultAlias`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító és a válasz URL-értékekkel. Az értékek beszerzéséhez forduljon a [Everbridge támogatási csapatához](mailto:support@everbridge.com). A Azure Portal az **alapszintű SAML-konfiguráció** szakaszának mintázatait is megtekintheti.

5. Ha a **Everbridge** alkalmazást a Everbridge- **tag portálként**szeretné konfigurálni, az **alapszintű SAML-konfiguráció** szakaszban kövesse az alábbi lépéseket:

  * Ha az alkalmazást IDENTITÁSSZOLGÁLTATÓ módban szeretné konfigurálni, kövesse az alábbi lépéseket:

     ![Everbridge tartomány-és URL-címekre vonatkozó egyszeri bejelentkezési adatok IDENTITÁSSZOLGÁLTATÓ által kezdeményezett módban](common/idp-intiated.png)

    a. Az **azonosító** mezőben adja meg a mintát követő URL-címet`https://sso.everbridge.net/<API_Name>/<Organization_ID>`

    b. A **Válasz URL-címe** mezőbe írja be a mintát követő URL-címet`https://member.everbridge.net/saml/SSO/<API_Name>/<Organization_ID>/alias/defaultAlias`

   * Ha az alkalmazást SP-kezdeményezésű módban szeretné konfigurálni, válassza a **további URL-címek beállítása** lehetőséget, és kövesse ezt a lépést:

     ![Everbridge tartomány és URL-címek egyszeri bejelentkezési információi az SP által kezdeményezett módban](common/both-signonurl.png)

     a. A **bejelentkezési URL-cím** mezőbe írja be a mintát követő URL-címet`https://member.everbridge.net/saml/login/<API_Name>/<Organization_ID>/alias/defaultAlias?disco=true`

     > [!NOTE]
     > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével, és jelentkezzen be az URL-címek értékeire. Az értékek beszerzéséhez forduljon a [Everbridge támogatási csapatához](mailto:support@everbridge.com). A Azure Portal az **alapszintű SAML-konfiguráció** szakaszának mintázatait is megtekintheti.

6. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon az **SAML aláíró tanúsítvány** szakaszban válassza a **Letöltés** lehetőséget az **összevonási metaadatok XML-fájljának**letöltéséhez. Mentse a számítógépére.

    ![Tanúsítvány letöltési hivatkozása](common/metadataxml.png)

7. A **Everbridge beállítása** szakaszban másolja a követelményekhez szükséges URL-címeket:

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    - Bejelentkezési URL
    - Azure AD-azonosító
    - Kijelentkezési URL-cím

### <a name="configure-everbridge-as-everbridge-manager-portal-single-sign-on"></a>A Everbridge konfigurálása Everbridge Manager-portál egyszeri bejelentkezéssel

Ha az egyszeri bejelentkezést a **Everbridge** **Everbridge Manager portál** alkalmazásként szeretné konfigurálni, kövesse az alábbi lépéseket.
 
1. Egy másik böngészőablakban jelentkezzen be a Everbridge rendszergazdaként.

1. A felső menüben válassza a **Beállítások** lapot. A **Biztonság**területen válassza az **egyszeri bejelentkezés**lehetőséget.
   
     ![Egyszeri bejelentkezés konfigurálása](./media/everbridge-tutorial/tutorial_everbridge_002.png)
   
     a. A **név** mezőbe írja be az azonosító szolgáltató nevét. Ilyen például a vállalat neve.
   
     b. Az **API neve** mezőbe írja be az API nevét.
   
     c. Válassza a **fájl kiválasztása** lehetőséget a Azure Portal letöltött metaadat-fájl feltöltéséhez.
   
     d. Az **SAML-identitás helyéhez**válassza az **identitás elemet a tulajdonos utasítás NameIdentifier elemében**.
   
     e. Az **Identity Provider bejelentkezési URL-címe** mezőbe illessze be a Azure Portalból másolt **bejelentkezési URL-** értéket.
   
     f. A **szolgáltató által kezdeményezett kérelem kötése**beállításnál válassza a **http-átirányítás**lehetőséget.

     g. Kattintson a **Mentés** gombra.

### <a name="configure-everbridge-as-everbridge-member-portal-single-sign-on"></a>Everbridge konfigurálása Everbridge-tag portál egyszeri bejelentkezéshez

Ha az egyszeri bejelentkezést **Everbridge** **Everbridge** szeretné konfigurálni, küldje el a letöltött **összevonási metaadatok XML-fájlját** a [Everbridge-támogató csapatnak](mailto:support@everbridge.com). Ezt a beállítást úgy állították be, hogy az SAML SSO-kapcsolatok mindkét oldalon helyesen legyenek beállítva.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása 

Az alábbi lépéseket követve hozhatja létre a Britta Simon Azure Portal.

1. A Azure Portal a bal oldali ablaktáblán válassza a **Azure Active Directory** > **felhasználók** > **minden felhasználó**lehetőséget.

    ![Felhasználók és minden felhasználó hivatkozása](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A **felhasználó** párbeszédpanelen kövesse az alábbi lépéseket.

    ![Felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A **Felhasználónév** mezőben adja meg a következőt: `brittasimon@yourcompanydomain.extension`. Például: BrittaSimon@contoso.com.

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet. Jegyezze fel a **jelszó** mezőben megjelenő értéket.

    d. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Engedélyezze a Britta Simon számára az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a Everbridge.

1. A Azure Portal válassza a **vállalati alkalmazások** > **minden alkalmazás** >**Everbridge**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Everbridge**lehetőséget.

    ![Everbridge hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![Felhasználók és csoportok hivatkozása](common/users-groups-blade.png)

4. Válassza a **felhasználó hozzáadása**elemet. A **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok**lehetőséget.

    ![Hozzárendelés hozzáadása párbeszédpanel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listában. A képernyő alján válassza a **kiválasztás** lehetőséget.

6. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából. A képernyő alján válassza a **kiválasztás** lehetőséget.

7. A **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelés**lehetőséget.

### <a name="create-an-everbridge-test-user"></a>Everbridge-teszt felhasználó létrehozása

Ebben a szakaszban a Britta Simon Everbridge-ben hozza létre a test user. Ha felhasználókat szeretne felvenni a Everbridge-platformba, működjön együtt a [Everbridge támogatási csapatával](mailto:support@everbridge.com). Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat a Everbridge-ben. 

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Tesztelje az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panel használatával.

Amikor kiválasztja a Everbridge csempét a hozzáférési panelen, automatikusan be kell jelentkeznie arra a Everbridge-fiókba, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További háttéranyagok

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


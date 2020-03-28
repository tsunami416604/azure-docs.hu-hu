---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az SAML SSO for Bitbucket for resolution GmbH-val | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az SAML SSO for Bitbucket by resolution GmbH között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: fc947df1-f24e-43ae-9a34-518293583d69
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57ae83aee563d4893f767331fff2289a4595cc60
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73157648"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bitbucket-by-resolution-gmbh"></a>Oktatóanyag: Az Azure Active Directory integrációja az SAML SSO for Bitbucket for resolution GmbH-val

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az SAML SSO for Bitbucket by resolution GmbH-t az Azure Active Directoryval (Azure AD).
Az SAML SSO integrálása a Bitbucket for resolution GmbH és az Azure AD szolgáltatáshoz a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a Bitbucket SAML SSO-jához a megoldás gmbH által.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve legyenek az SAML SSO for Bitbucket-be a Resolution GmbH (Single Sign-On) által az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához az SAML SSO for Bitbucket by resolution GmbH-val a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* SAML SSO for Bitbucket by resolution GmbH egyszeri bejelentkezéses előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* SAML SSO for Bitbucket by resolution GmbH supports **SP and IDP** initiated SSO
* SAML SSO for Bitbucket by resolution GmbH supports **Just In Time** felhasználói kiépítést


## <a name="adding-saml-sso-for-bitbucket-by-resolution-gmbh-from-the-gallery"></a>SAML SSO hozzáadása a Bitbucket-hez határozat szerint GmbH a galériából

Az SAML SSO for resolution GmbH azure AD-be történő integrálásának konfigurálásához hozzá kell adnia a SAML SSO for Bitbucket by resolution GmbH-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha saml SSO-t szeretne hozzáadni a Bitbucket by resolution GmbH-hoz a galériából, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **SAML SSO for Bitbucket by resolution GmbH parancsot,** válassza **az SAML SSO for Bitbucket by resolution GmbH** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![SAML SSO for Bitbucket by resolution GmbH az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését az SAML SSO for Bitbucket by resolution GmbH-val egy **Britta Simon**nevű tesztfelhasználó alapján.
Az egyszeri bejelentkezés működéséhez létre kell hozni egy kapcsolatkapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között az SAML SSO for Bitbucket by resolution GmbH-ban.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a MBL SSO for Bitbucket by resolution GmbH-val a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja az SAML SSO-t a Bitbucket-hez határozat szerint GmbH Single Sign-On](#configure-saml-sso-for-bitbucket-by-resolution-gmbh-single-sign-on)** - az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Saml SSO létrehozása a Bitbucket számára megoldás szerint GmbH tesztfelhasználó](#create-saml-sso-for-bitbucket-by-resolution-gmbh-test-user)** - hogy egy megfelelője Britta Simon saml SSO for Bitbucket felbontásban GmbH, amely kapcsolódik az Azure AD képviselete a felhasználó.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához a MBL SSO for Bitbucket by resolution GmbH szolgáltatással hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **SAML SSO for Bitbucket by resolution GmbH** alkalmazásintegrációs lapon válassza **az Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket, ha az alkalmazást **IDP** által kezdeményezett módban kívánja konfigurálni:

    ![SAML SSO for Bitbucket by resolution GmbH Domain és URL-ek egyszeri bejelentkezési információk](common/idp-intiated.png)

    a. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<server-base-url>/plugins/servlet/samlsso`

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://<server-base-url>/plugins/servlet/samlsso`

    c. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    ![SAML SSO for Bitbucket by resolution GmbH Domain és URL-ek egyszeri bejelentkezési információk](common/metadata-upload-additional-signon.png)

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Lépjen kapcsolatba [az SAML SSO for Bitbucket by resolution GmbH Ügyfél támogatási csapatával,](https://marketplace.atlassian.com/apps/1217045/saml-single-sign-on-sso-bitbucket?hosting=server&tab=support) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány csoportjában** kattintson a **Letöltés** gombra, ha letöltheti az **összevonási metaadat-XML-t** a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

### <a name="configure-saml-sso-for-bitbucket-by-resolution-gmbh-single-sign-on"></a>Saml SSO konfigurálása Bitbucket-hez felbontás szerint GmbH Single Sign-On

1. Jelentkezzen be a SAML SSO for Bitbucket by resolution GmbH cég webhelyére rendszergazdaként.

2. A fő eszköztár jobb oldalán kattintson a **Beállítások gombra.**

3. Ugrás a SZÁMLÁK szakaszra, kattintson az **SAML SingleSignOn gombra** a menüsávon.

    ![A Samlsingle](./media/bitbucket-tutorial/tutorial_bitbucket_samlsingle.png)

4. Az **SAML SIngleSignOn plugin konfigurációja lapon**kattintson az **IDP hozzáadása gombra.** 

    ![Az idp hozzáadása](./media/bitbucket-tutorial/tutorial_bitbucket_addidp.png)

5. Az **SAML-identitásszolgáltató kiválasztása** lapon hajtsa végre az alábbi lépéseket:

    ![Az identitásszolgáltató](./media/bitbucket-tutorial/tutorial_bitbucket_identityprovider.png)

    a. Válassza az **Idp-típust** **AZURE AD néven.**

    b. A **Név** mezőbe írja be a nevet.

    c. A **Leírás** mezőbe írja be a leírást.

    d. Kattintson a **Tovább** gombra.

6. Az **Identitásszolgáltató konfigurációja lapon**kattintson a **Tovább**gombra.

    ![A személyazonossági konfiguráció](./media/bitbucket-tutorial/tutorial_bitbucket_identityconfig.png)

7.  Az **SAML Idp metaadat-adatok importálása** lapon kattintson a **Fájl betöltése** gombra az Azure Portalról letöltött **METADATA XML-fájl** feltöltéséhez.

    ![Az idpmetadata](./media/bitbucket-tutorial/tutorial_bitbucket_idpmetadata.png)
    
8. Kattintson a **Tovább** gombra.

9. Kattintson a **Beállítások mentése** lehetőségre.

    ![A mentés](./media/bitbucket-tutorial/tutorial_bitbucket_save.png)

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

Ebben a szakaszban engedélyezi Britta Simon számára az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít az SAML SSO-hoz a Bitbucket számára a megoldás gmbH által.

1. Az Azure portalon válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza **az SAML SSO for Bitbucket by resolution GmbH lehetőséget.**

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában írja be és válassza az **SAML SSO for Bitbucket by resolution GmbH**lehetőséget.

    ![A MBBucket for resolution GmbH SAML SSO hivatkozása az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-saml-sso-for-bitbucket-by-resolution-gmbh-test-user"></a>SAML SSO létrehozása bitbucket-hez felbontás szerint GmbH tesztfelhasználó

Ennek a szakasznak az a célja, hogy létrehozzon egy Britta Simon nevű felhasználót a SAML SSO-ban a Bitbucket számára a GmbH megoldása szerint. SAML SSO for Bitbucket by resolution GmbH támogatja a just-in-time kiépítést, és a felhasználók manuálisan is létrehozhatók, forduljon [a SAML SSO-hoz a Bitbucket by resolution GmbH Ügyfél támogatási csapatához](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bitbucket/server/support) az Ön igényeinek megfelelően.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha a Hozzáférési panelen a Bitbucket by resolution GmbH lapon a SAML SSO for resolution GmbH csempére kattint, automatikusan be kell jelentkeznie az SAML SSO for Bitbucket-be a resolution GmbH által, amelyre beállította az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


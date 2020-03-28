---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az SAML SSO for Bamboo for Bamboo by resolution GmbH-val | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az SAML SSO for Bamboo by Resolution GmbH között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f00160c7-f4cc-43bf-af18-f04168d3767c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 044a2f9a9ecd0ccceb99ce7999af7e2c8578950d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67106540"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bamboo-by-resolution-gmbh"></a>Oktatóanyag: Az Azure Active Directory integrációja az SAML SSO for Bamboo for Bamboo szolgáltatással a resolution GmbH felbontásban

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az SAML SSO for Bamboo-t megoldásszerint GmbH az Azure Active Directoryval (Azure AD).
A SAML SSO for Bamboo megoldásszerint történő integrálása az Azure AD-vel a következő előnyökkel jár:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá az SAML SSO for Bamboo szolgáltatáshoz a Resolution GmbH megoldása szerint.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve legyenek az SAML SSO for Bamboo szolgáltatásba a Resolution GmbH (Single Sign-On) által az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához az SAML SSO for Bamboo by Resolution GmbH-val a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) kaphat
* SAML SSO for Bamboo by resolution GmbH egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* SAML SSO for Bamboo by resolution GmbH támogatja **az SP-t és az IDP** által kezdeményezett SSO-t
* SAML SSO for Bamboo by resolution GmbH támogatja **a Just In Time** felhasználói kiépítést

## <a name="adding-saml-sso-for-bamboo-by-resolution-gmbh-from-the-gallery"></a>SAML SSO hozzáadása a Bamboo számára határozat szerint GmbH a galériából

Az SAML SSO for Bamboo by resolution GmbH Azure AD-be történő integrálásának konfigurálásához hozzá kell adnia az SAML SSO for Bamboo by resolution GmbH-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Saml SSO for Bamboo by resolution GmbH hozzáadásához a galériából hajtsa végre a következő lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **SAML SSO for Bamboo by resolution GmbH parancsot,** válassza **az SAML SSO for Bamboo by resolution GmbH** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![SAML SSO for Bamboo felbontásban GmbH az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését az SAML SSO for Bamboo by resolution GmbH-val egy **Britta Simon**nevű tesztfelhasználó alapján.
Az egyszeri bejelentkezés hez létre kell hozni egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között az SAML SSO for Bamboo by resolution GmbH-ban.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a SAML SSO for Bamboo by resolution GmbH szolgáltatással a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja az SAML SSO for Bamboo by resolution GmbH Single Sign-On](#configure-saml-sso-for-bamboo-by-resolution-gmbh-single-sign-on)** --t az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre SAML SSO for Bamboo by resolution GmbH tesztfelhasználót](#create-saml-sso-for-bamboo-by-resolution-gmbh-test-user)** – hogy brit tasimon megfelelője legyen az SAML SSO for Bamboo by resolution GmbH-ban, amely a felhasználó Azure AD-reprezentációjához kapcsolódik.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához a SAML SSO for Bamboo by resolution GmbH szolgáltatással hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)az **SAML SSO for Bamboo by resolution GmbH** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban, ha az alkalmazást **IDP** által kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    ![SAML SSO for Bamboo by resolution GmbH Domain és URL-ek egyszeri bejelentkezési információk](common/idp-intiated.png)

    a. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<server-base-url>/plugins/servlet/samlsso`

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://<server-base-url>/plugins/servlet/samlsso`

5. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    ![SAML SSO for Bamboo by resolution GmbH Domain és URL-ek egyszeri bejelentkezési információk](common/metadata-upload-additional-signon.png)

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Lépjen kapcsolatba [az SAML SSO for Bamboo by resolution GmbH Ügyféltámogatási csapatával,](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

6. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány csoportjában** kattintson a **Letöltés** gombra, ha letöltheti az **összevonási metaadat-XML-t** a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

7. Az **SAML SSO for Bamboo by resolution GmbH** szakaszon másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-saml-sso-for-bamboo-by-resolution-gmbh-single-sign-on"></a>Saml SSO for Bamboo konfigurálása megoldás szerint GmbH Single Sign-On

1. Jelentkezzen be az SAML SSO for Bamboo-ba a resolution GmbH cég webhelyére rendszergazdaként.

1. A fő eszköztár jobb oldalán kattintson a **Beállítások** > **bővítmények gombra.**

    ![A beállítások](./media/bamboo-tutorial/tutorial_bamboo_setings.png)

1. Ugrás a BIZTONSÁG részre, kattintson az **SAML SingleSignOn gombra** a menüsávon.

    ![A Samlsingle](./media/bamboo-tutorial/tutorial_bamboo_samlsingle.png)

1. Az **SAML SIngleSignOn plugin konfigurációja lapon**kattintson az **IDP hozzáadása gombra.**

    ![Az idp hozzáadása](./media/bamboo-tutorial/tutorial_bamboo_addidp.png)

1. Az **SAML-identitásszolgáltató kiválasztása** lapon hajtsa végre az alábbi lépéseket:

    ![Az identitásszolgáltató](./media/bamboo-tutorial/tutorial_bamboo_identityprovider.png)

    a. Válassza az **Idp-típust** **AZURE AD néven.**

    b. A **Név** mezőbe írja be a nevet.

    c. A **Leírás** mezőbe írja be a leírást.

    d. Kattintson a **Tovább** gombra.

1. Az **Identitásszolgáltató konfigurációja** lapon kattintson a **Tovább gombra.**

    ![A személyazonossági konfiguráció](./media/bamboo-tutorial/tutorial_bamboo_identityconfig.png)

1. Az **SAML Idp metaadat-adatok importálása** lapon kattintson a **Fájl betöltése** gombra az Azure Portalról letöltött **METADATA XML-fájl** feltöltéséhez.

    ![Az idpmetadata](./media/bamboo-tutorial/tutorial_bamboo_idpmetadata.png)

1. Kattintson a **Tovább** gombra.

1. Kattintson a **Beállítások mentése** lehetőségre.

    ![A mentés](./media/bamboo-tutorial/tutorial_bamboo_save.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása 

Ez a szakasz célja, hogy hozzon létre egy tesztfelhasználót az Azure Portalon Britta Simon.

1. Az Azure Portalon a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd az **Összes felhasználó**lehetőséget.

    ![A "Felhasználók és csoportok" és a "Minden felhasználó" linkek](common/users.png)

2. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A Felhasználó tulajdonságokban hajtsa végre a következő lépéseket.

    ![A Felhasználó párbeszédpanel](common/user-properties.png)

    a. A **Név** mezőbe írja be **a BrittaSimon**értéket.
  
    b. A **Felhasználónév** mező `brittasimon@yourcompanydomain.extension`típusa mezőben. Például: BrittaSimon@contoso.com

    c. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket.

    d. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi Britta Simon számára az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít az SAML SSO for Bamboo-hoz a megoldás gmbH által.

1. Az Azure portalon válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza **az SAML SSO for Bamboo by resolution GmbH lehetőséget.**

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **SAML SSO for Bamboo by resolution GmbH lehetőséget.**

    ![A SAML SSO for Bamboo by resolution GmbH link az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-saml-sso-for-bamboo-by-resolution-gmbh-test-user"></a>SAML SSO for Bamboo létrehozása felbontás szerint GmbH tesztfelhasználó

Ennek a szakasznak az a célja, hogy a GmbH határozattal létrehozza a Britta Simon nevű felhasználót a SAML SSO for Bamboo-ban. A SAML SSO for Bamboo by resolution GmbH támogatja a just-in-time kiépítést, és a felhasználók manuálisan is létrehozhatók, lépjen kapcsolatba [a SAML SSO for Bamboo by resolution GmbH Ügyfél támogatási csapatával](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support) az Ön igényei nek megfelelően.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha a Hozzáférési panelen az SAML SSO for Bamboo by resolution GmbH csempére kattint, automatikusan be kell jelentkeznie az SAML SSO for Bamboo-ba a Resolution GmbH által, amelyre beállította az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

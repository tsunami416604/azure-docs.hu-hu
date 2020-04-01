---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az SAP Business ByDesign programmal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az SAP Business ByDesign között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 82938920-33ba-47cb-b141-511b46d19e66
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0b830f0760b768826b2d937b4a8b2ffbd8e9e2a2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67091670"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>Oktatóanyag: Azure Active Directory-integráció az SAP Business ByDesign megoldással

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az SAP Business ByDesign-t az Azure Active Directoryval (Azure AD).
Az SAP Business ByDesign integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá az SAP Business ByDesign programhoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve legyenek az SAP Business ByDesign (Single Sign-On) alkalmazásba az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció sap Business ByDesign alkalmazással való konfigurálásához a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) kaphat
* Az SAP Business ByDesign egyszeri bejelentkezéses előfizetése

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* Az SAP Business ByDesign támogatja az **SP** által kezdeményezett sso-t

## <a name="adding-sap-business-bydesign-from-the-gallery"></a>SAP Business ByDesign hozzáadása a gyűjteményből

Az SAP Business ByDesign Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az SAP Business ByDesign-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha az SAP Business ByDesign-t a katalógusból szeretné hozzáadni, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **SAP Business ByDesign**kifejezést, válassza az **SAP Business ByDesign** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Sap Business ByDesign az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését az SAP Business ByDesign alkalmazással egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés működéséhez létre kell hozni egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó az SAP Business ByDesign-ban.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez az SAP Business ByDesign alkalmazással a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja az SAP Business ByDesign single sign-on](#configure-sap-business-bydesign-single-sign-on)** konfigurálásához az egyszeri bejelentkezés i beállításainak konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre SAP Business ByDesign tesztfelhasználó](#create-sap-business-bydesign-test-user)** – egy megfelelője Britta Simon az SAP Business ByDesign, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához az SAP Business ByDesign alkalmazással hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)az **SAP Business ByDesign** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![Az SAP Business ByDesign tartomány és url-címek egyszeri bejelentkezési adatai](common/sp-identifier.png)

    a. A Bejelentkezés az **URL-cím** mezőbe írja be az URL-címet a következő minta használatával:`https://<servername>.sapbydesign.com`

    b. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<servername>.sapbydesign.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Bejelentkezési URL-címmel és azonosítóval. Lépjen kapcsolatba [az SAP Business ByDesign ügyféltámogatási csapatával,](https://www.sap.com/products/cloud-analytics.support.html) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

5. Az SAP Business ByDesign alkalmazás az SAML-állításokat egy adott formátumban várja. Konfigurálja az alkalmazás következő jogcímeket. Ezeknek az attribútumoknak az értékeit az alkalmazásintegrációs lap **Felhasználói attribútumok** szakaszában kezelheti. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** gombra a **Felhasználói attribútumok** párbeszédpanel megnyitásához.

    ![image](common/edit-attribute.png)

6. Kattintson a **Szerkesztés** ikonra a **Névazonosító érték**szerkesztéséhez.

    ![image](media/sapbusinessbydesign-tutorial/mail-prefix1.png)

7. A **Felhasználói jogcímek kezelése** csoportban ![hajtsa végre a következő lépéseket: image](media/sapbusinessbydesign-tutorial/mail-prefix2.png)

    a. Válassza **az Átalakítás** **forrásként**lehetőséget.

    b. Az **Átalakítás** legördülő listában válassza **a ExtractMailPrefix() (ExtractMailPrefix() (ExtractMailPrefix() (ExtractMailPrefix() (ExtractMailPrefix() (Átalakítás) legördülő listában**

    c. Az **1.** Ha például az EmployeeID azonosítót egyedi felhasználói azonosítóként szeretné használni, és az attribútumértékét az ExtensionAttribute2 attribútumban tárolta, válassza a user.extensionattribute2 lehetőséget.

    d. Kattintson a **Mentés** gombra.

8. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány csoportjában** kattintson a **Letöltés** gombra, ha letöltheti az **összevonási metaadat-XML-t** a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

9. Az **SAP Business ByDesign beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-sap-business-bydesign-single-sign-on"></a>Az SAP Business ByDesign egyszeri bejelentkezéskonfigurálása

1. Jelentkezzen be az SAP Business ByDesign portálra rendszergazdai jogokkal.

2. Nyissa meg **az Alkalmazás- és felhasználókezelés közös feladata lapot,** és kattintson az **Identitásszolgáltató** fülre.

3. Kattintson az **Új identitásszolgáltató elemre,** és válassza ki az Azure Portalról letöltött metaadat-XML-fájlt. A metaadatok importálásával a rendszer automatikusan feltölti a szükséges aláírási tanúsítványt és titkosítási tanúsítványt.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)

4. Ha a **helyességifeltétel-fogyasztói szolgáltatás URL-címét** be szeretné foglalni az SAML-kérelembe, jelölje be **a Helyességifeltétel-fogyasztói szolgáltatás URL-címének felvétele jelölőnégyzetet.**

5. Kattintson **az Egyszeri bejelentkezés aktiválása gombra.**

6. Mentse a módosításokat.

7. Kattintson a **Saját rendszer** fülre.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)

8. Az **Azure AD Sign On URL-cím** szövegdobozába illessze be **a bejelentkezési URL-értéket,** amelyet az Azure Portalról másolt.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)

9. Adja meg, hogy az alkalmazott manuálisan választhat-e a felhasználói azonosítóval és jelszóval történő bejelentkezés vagy az SSO között a **Kézi identitásszolgáltató kiválasztása**lehetőség kiválasztásával.

10. Az **SSO URL-címszakaszában** adja meg azt az URL-címet, amelyet az alkalmazottnak a rendszerbe való bejelentkezéshez használnia kell.
    Az Alkalmazottnak küldött URL-cím legördülő listában a következő lehetőségek közül választhat:

    **Nem Egyszeri cím**

    A rendszer csak a normál rendszer URL-címet küldi el az alkalmazottnak. Az alkalmazott nem írhat alá sso használatával, és jelszó vagy tanúsítvány használata helyett.

    **Sso URL-címe**

    A rendszer csak az SSO URL-címét küldi el az alkalmazottnak. Az alkalmazott az SSO használatával is aláírhat. A hitelesítési kérelem átirányítása az IdP-n keresztül történik.

    **Automatikus kijelölés**

    Ha az SSO nem aktív, a rendszer elküldi a normál rendszer URL-címét az alkalmazottnak. Ha az SSO aktív, a rendszer ellenőrzi, hogy az alkalmazott rendelkezik-e jelszóval. Ha van elérhető jelszó, az egyszeri bejelentkezés URL-címe és a nem egyszeri bejelentkezés URL-címe is elküldésre kerül az alkalmazottnak. Ha azonban az alkalmazott nem rendelkezik jelszóval, csak az SSO URL-címe kerül elküldésre az alkalmazottnak.

11. Mentse a módosításokat.

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

Ebben a szakaszban engedélyezi Britta Simon számára az Azure egyszeri bejelentkezést az SAP Business ByDesign-hoz való hozzáférés biztosításával.

1. Az Azure portalon válassza a **Vállalati alkalmazások**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza az SAP **Business ByDesign lehetőséget.**

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza az **SAP Business ByDesign lehetőséget.**

    ![Az SAP Business ByDesign hivatkozása az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-sap-business-bydesign-test-user"></a>SAP Business ByDesign tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre az SAP Business ByDesign programban. Az [SAP Business ByDesign ügyféltámogatási csapatával](https://www.sap.com/products/cloud-analytics.support.html) együttműködve adja hozzá a felhasználókat az SAP Business ByDesign platformon. 

> [!NOTE]
> Győződjön meg arról, hogy a NameID értéknek meg kell egyeznie az SAP Business ByDesign platform felhasználónév mezőjével.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha a Hozzáférési panelen az SAP Business ByDesign csempére kattint, automatikusan be kell jelentkeznie az SAP Business ByDesign programba, amelyhez beállítja az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

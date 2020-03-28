---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Zendeskkel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Zendesk között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 9d7c91e5-78f5-4016-862f-0f3242b00680
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4887457f457ff533a0eaf10be7db1627a950f5bd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74233262"
---
# <a name="tutorial-azure-active-directory-integration-with-zendesk"></a>Oktatóanyag: Az Azure Active Directory integrációja a Zendeskkel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Zendesket az Azure Active Directoryval (Azure AD).
A Zendesk integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a Zendeskhez.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezzenek a Zendeskbe (Single Sign-On) az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a Zendeskdel a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* Zendesk egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A Zendesk támogatja az **SP** által kezdeményezett SSO-t

* A Zendesk támogatja az [ **automatikus** felhasználói kiépítést](zendesk-provisioning-tutorial.md)

## <a name="adding-zendesk-from-the-gallery"></a>A Zendesk hozzáadása a galériából

A Zendesk Azure AD-be való integrációjának konfigurálásához hozzá kell adnia a Zendesket a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha a Zendesket a galériából szeretné hozzáadni, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Zendesk**kifejezést , válassza a **Zendesk** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Zendesk az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a Zendeskdel egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, egy Azure AD-felhasználó és a kapcsolódó felhasználó a Zendesk létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a Zendeskdel a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a Zendesk Single Sign-On --t](#configure-zendesk-single-sign-on)** az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalán.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre Zendesk tesztfelhasználót](#create-zendesk-test-user)** – ha a Zendeskben britta Simon megfelelője szeretne lenni, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **Zendesk** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![Zendesk Domain és URL-ek egyszeri bejelentkezési információk](common/sp-identifier.png)

    a. A Bejelentkezés az **URL-cím** mezőbe írja be az URL-címet a következő minta használatával:`https://<subdomain>.zendesk.com`

   b. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<subdomain>.zendesk.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Bejelentkezési URL-címmel és azonosítóval. Lépjen kapcsolatba [a Zendesk ügyféltámogatási csapatával,](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

5. A Zendesk alkalmazás az SAML-állításokat egy adott formátumban várja. Nincsenek kötelező SAML-attribútumok, de tetszés szerint az alkalmazásintegrációs lap **Felhasználói attribútumok** szakaszából kezelhető. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** gombra a **Felhasználói attribútumok** párbeszédpanel megnyitásához.

    ![image](common/edit-attribute.png)

6. A **Felhasználói attribútumok** párbeszédpanel **Felhasználói jogcímek** szakaszában konfigurálja az SAML token attribútumot a fenti képen látható módon, és hajtsa végre a következő lépéseket:

    a. Kattintson **az Új jogcím hozzáadása** gombra a Felhasználói **jogcímek kezelése** párbeszédpanel megnyitásához.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. A **Név** mezőbe írja be a sor attribútumnevét.

    c. Hagyja üresen a **névteret.**

    d. Válassza a Forrás **attribútumként lehetőséget.**

    e. A **Forrás attribútumlistában** válassza ki a megfelelő attribútumértéket.

    f. Kattintson **az Ok gombra**

    g. Kattintson a **Mentés** gombra.

    > [!NOTE]
    > A bővítményattribútumok használatával olyan attribútumokat adhat hozzá, amelyek alapértelmezés szerint nem szerepelnek az Azure AD-ben. Kattintson az [SAML-ben beállítható Felhasználói attribútumok](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-) elemre a **Zendesk** által által fogadott SAML-attribútumok teljes listájának levételéhez.

7. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszban másolja az **ujjlenyomatot,** és mentse a számítógépre.

    ![Ujjlenyomat másolása érték](common/copy-certificatethumbprint.png)

8. A **Zendesk beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure-hirdetésazonosító

    c. Kijelentkezés URL-címe

9. A Zendesk kétféleképpen konfigurálható - Automatikus és Manuális.
  
10. A Zendesk konfigurációjának automatizálásához telepítenie kell a **My Apps Secure Sign-in böngészőbővítményt** **a Bővítmény telepítése**gombra kattintva.

    ![image](./media/zendesk-tutorial/install_extension.png)

11. Miután hozzáadtál egy bővítményt a böngészőhöz, kattints a **Setup Zendesk gombra,** amely a Zendesk alkalmazáshoz irányítja. Itt adja meg a rendszergazdai hitelesítő adatokat a Zendeskbe való bejelentkezéshez. A böngészőbővítmény automatikusan konfigurálja az alkalmazást, és automatizálja a **Zendesk egyszeri bejelentkezés konfigurálása című szakaszt.**

    ![image](./media/zendesk-tutorial/d2_saml.png)

### <a name="configure-zendesk-single-sign-on"></a>A Zendesk egyszeri bejelentkezéskonfigurálása

1. Ha manuálisan szeretnéd beállítani a Zendesket, nyiss meg egy új böngészőablakot, és jelentkezz be rendszergazdaként a Zendesk vállalati webhelyére, és hajtsd végre a következő lépéseket:

2. Kattintson **a Rendszergazda gombra.**

3. A bal oldali navigációs ablakban kattintson a **Beállítások**gombra, majd a **Biztonság**parancsra.

4. A **Biztonság** lapon hajtsa végre az alábbi lépéseket:

    ![Biztonság](././media/zendesk-tutorial/ic773089.png "Biztonság")

    ![Egyszeri bejelentkezés](././media/zendesk-tutorial/ic773090.png "Egyszeri bejelentkezés")

    a. Kattintson a **Rendszergazda & ügyintézők** fülre.

    b. Válassza **az Egyszeri bejelentkezés (SSO) és az SAML**lehetőséget, majd az **SAML**lehetőséget.

    c. Az **SAML SSO URL-cím** szövegmezőjébe illessze be a **bejelentkezési URL-cím** értékét, amelyet az Azure Portalról másolt.

    d. A **Távoli kijelentkezés URL-cím** mezőjébe illessze be a **Kijelentkezés URL-címének** az Azure Portalról másolt értékét.

    e. A **Tanúsítványujjlenyomat** szövegmezőbe illessze be az Azure Portalról másolt tanúsítvány **ujjlenyomat-értékét.**

    f. Kattintson a **Mentés** gombra.

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

Ebben a szakaszban engedélyezi Britta Simon számára az Azure egyszeri bejelentkezést a Zendesk-hez való hozzáférés biztosításával.

1. Az Azure Portalon válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd a **Zendesk**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában írja be és válassza a **Zendesk**lehetőséget.

    ![A Zendesk hivatkozás az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-zendesk-test-user"></a>Zendesk tesztfelhasználó létrehozása

Ennek a szakasznak az a célja, hogy hozzon létre egy felhasználó nevű Britta Simon a Zendesk. A Zendesk támogatja az automatikus felhasználói kiépítést, amely alapértelmezés szerint engedélyezve van. Az automatikus felhasználói [here](Zendesk-provisioning-tutorial.md) kiépítés konfigurálásáról itt olvashat bővebben.

**Ha manuálisan kell létrehoznia a felhasználót, hajtsa végre az alábbi lépéseket:**

> [!NOTE]
> **A végfelhasználói** fiókok automatikusan kivannak építve bejelentkezéskor. **Az ügynöki** és **rendszergazdai** fiókokat bejelentkezés előtt manuálisan kell kiépíteni a **Zendeskben.**

1. Jelentkezz be a Zendesk-bérlődbe. **Zendesk**

2. Válassza a **Vevőlista** lapot.

3. Kattintson a **Felhasználó** fülre, majd a **Hozzáadás**gombra.

    ![Felhasználói csoportok](././media/zendesk-tutorial/ic773632.png "Felhasználó hozzáadása")
4. Írja be a kiépíteni kívánt meglévő Azure AD-fiók **nevét** és **e-mail címét,** majd kattintson a **Mentés gombra.**

    ![Új felhasználó](././media/zendesk-tutorial/ic773633.png "Új felhasználó")

> [!NOTE]
> A Zendesk által biztosított bármely más Zendesk felhasználói fiók-létrehozási eszközt vagy API-t használhatja az Azure AD felhasználói fiókok kiépítéséhez.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelzenére kattintasz a Zendesk csempére, automatikusan be kell jelentkezned arra a Zendeskbe, amelyhez az SSO-t beállítottad. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Felhasználói kiépítés konfigurálása](zendesk-provisioning-tutorial.md)
---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Lifesize Cloud szolgáltatással | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Lifesize Cloud között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 75fab335-fdcd-4066-b42c-cc738fcb6513
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/4/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e886a1fa3f590ac94dbf088520e6770690ee21ac
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73159573"
---
# <a name="tutorial-azure-active-directory-integration-with-lifesize-cloud"></a>Oktatóanyag: Az Azure Active Directory integrációja a Lifesize Cloud szolgáltatással

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Lifesize Cloud szolgáltatást az Azure Active Directoryval (Azure AD).
A Lifesize Cloud integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a Lifesize Cloud szolgáltatáshoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve lifesize cloud (Single Sign-On) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció és a Lifesize Cloud konfigurálásához a következő elemekre van szüksége:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* Lifesize Cloud egyszeri bejelentkezéses előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* Lifesize Cloud támogatja **SP** kezdeményezett SSO

* A Lifesize Cloud támogatja az **automatikus** felhasználói kiépítést

## <a name="adding-lifesize-cloud-from-the-gallery"></a>Lifesize Cloud hozzáadása a galériából

A Lifesize Cloud azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Lifesize Cloud-ot a katalógusból a felügyelt SaaS-alkalmazások listájához.

**A Lifesize Cloud gyűjteményből való hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a Lifesize Cloud ( **Lifesize Cloud**) kifejezést, válassza a **Lifesize Cloud** elemet az eredménypanelről, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Lifesize Cloud az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a Lifesize Cloud szolgáltatással egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, egy Azure AD-felhasználó és a kapcsolódó felhasználó a Lifesize Cloud létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a Lifesize Cloud szolgáltatással a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Állítsa be a Lifesize Cloud Single Sign-On](#configure-lifesize-cloud-single-sign-on)** --hoz konfigurálni az egyszeri bejelentkezési beállításokat az alkalmazás oldalán.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre Lifesize Cloud teszt felhasználó](#create-lifesize-cloud-test-user)** - egy megfelelője Britta Simon lifesize cloud, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához a Lifesize Cloud szolgáltatással hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **Lifesize Cloud** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![Lifesize Cloud Domain és URL-címek egyszeri bejelentkezési információk](common/sp-identifier-relay.png)

    a. A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://login.lifesizecloud.com/ls/?acs`

    b. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`https://login.lifesizecloud.com/<companyname>`

    c. Kattintson **a további URL-címek beállítása gombra.**

    d. A **Továbbítási állapot** mezőbe írjon be egy URL-címet a következő minta használatával:`https://webapp.lifesizecloud.com/?ent=<identifier>`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel, azonosítóval és továbbítási állapottal. Lépjen kapcsolatba [a Lifesize Cloud-ügyfél támogatási csapatával](https://www.lifesize.com/en/support) a bejelentkezési URL-cím és az azonosító értékek leéséhez, és lekaphatja a továbbítási állapot értékét az egyszeri bejelentkezés konfigurációjából, amelyet az oktatóanyag későbbi részében ismertetünk. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **Lifesize Cloud beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure-hirdetésazonosító

    c. Kijelentkezés URL-címe

### <a name="configure-lifesize-cloud-single-sign-on"></a>Lifesize Cloud egyszeri bejelentkezés konfigurálása

1. Az alkalmazáshoz konfigurált SSO-konfigurálásához jelentkezzen be a Lifesize Cloud alkalmazásfelügyeleti jogosultságokkal.

2. A jobb felső sarokban kattintson a nevére, majd kattintson az **Előzetes beállítások gombra.**

    ![Egyszeri bejelentkezés konfigurálása](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_06.png)

3. Az Előzetes beállítások ban kattintson az **SSO konfiguráció hivatkozásra.** Megnyílik a példány SSO-konfigurációja lapja.

    ![Egyszeri bejelentkezés konfigurálása](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_07.png)

4. Most konfigurálja a következő értékeket az SSO konfigurációs felhasználói felületen.

    ![Egyszeri bejelentkezés konfigurálása](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_08.png)

    a. Az **Identitásszolgáltató kiállítójának** szövegmezőjébe illessze be az **Azure-hirdetési azonosító** értékét, amelyet az Azure Portalról másolt.

    b.  A **Bejelentkezési URL-cím** szövegmezőbe illessze be a **bejelentkezési URL-cím** értékét, amelyet az Azure Portalról másolt.

    c. Nyissa meg az alap-64 kódolású tanúsítványt az Azure Portalról letöltött jegyzettömbben, másolja annak tartalmát a vágólapra, majd illessze be az **X.509 tanúsítvány** szövegdobozába.
  
    d. Az Utónév szövegmező SAML attribútumleképezései mezőbe írja be az értéket`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    e. A **Vezetéknév** szövegmező SAML attribútumleképezése mezőbe írja be az értéket`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    f. Az **E-mail** szöveg mező SAML attribútumleképezése mezőjébe írja be az értéket`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

5. A konfiguráció ellenőrzéséhez kattintson a **Teszt** gombra.

    >[!NOTE]
    >A sikeres teszteléshez el kell végeznie a konfigurációs varázslót az Azure AD-ben, és hozzáférést kell biztosítania a felhasználókvagy csoportok számára, akik elvégezhetik a tesztet.

6. Engedélyezze az SSO-t az SSO engedélyezése gomb on.Enable the SSO by checking on the **Enable SSO** button.

7. Most kattintson a **Frissítés** gombra, hogy az összes beállítás mentésre kerül. Ez létrehozza a RelayState értéket. Másolja a szövegmezőben generált RelayState értéket, illessze be a **Továbbítási állapot** szövegmezőbe a **Lifesize Cloud Domain és URL-címek** szakaszban.

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

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés t a Lifesize Cloud hozzáférést biztosít.

1. Az Azure Portalon válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza **a Lifesize Cloud**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Lifesize Cloud**lehetőséget.

    ![A Lifesize Cloud hivatkozás az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-lifesize-cloud-test-user"></a>Lifesize Cloud tesztfelhasználó létrehozása

Ebben a szakaszban hozzon létre egy felhasználó nevű Britta Simon lifesize cloud. Lifesize felhő támogatja az automatikus felhasználói kiépítés. Az Azure AD sikeres hitelesítése után a felhasználó automatikusan kilesz építve az alkalmazásban.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha a Hozzáférési panelen a Lifesize Cloud csempére kattint, be kell szereznie a Lifesize Cloud alkalmazás bejelentkezési lapját. Itt meg kell adnia a felhasználónevét, és ezt követően átirányítja az alkalmazás kezdőlapjára.

A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

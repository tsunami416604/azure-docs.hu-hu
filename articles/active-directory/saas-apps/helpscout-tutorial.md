---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Súgófelderítővel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Súgófelderítő között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0aad9910-0bc1-4394-9f73-267cf39973ab
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b71ccbc6cfdb9d3d37fc46b0e932fa98eee2fb43
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73159088"
---
# <a name="tutorial-azure-active-directory-integration-with-help-scout"></a>Oktatóanyag: Az Azure Active Directory integrációja a Súgófelderítővel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Súgófelderítőt az Azure Active Directoryval (Azure AD).
A Súgófelderítés integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a Súgófelderítőhöz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve legyenek a Súgófelderítőbe (Single Sign-On) az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció beállítása a Súgó felderítővel a következő elemekre van szüksége:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Segítség a Scout egyszeri bejelentkezéses előfizetéséhez

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* Segítség Scout támogatja **SP és IDP** kezdeményezett SSO
* A Súgó scout támogatja **just in time** felhasználói kiépítés

## <a name="adding-help-scout-from-the-gallery"></a>Súgó hozzáadása Scout a galériából

A Súgófelderítő Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Súgófelderítőt a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **gyűjtemény Hozzáadás szakaszába** írja be a **Súgófelderítő** kifejezést a keresőmezőbe.
1. Válaszd a **Súgó felderítés** e elemből a Súgó panelen, majd add hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezést a Súgófelderítővel egy **B.Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, az Azure AD-felhasználó és a kapcsolódó felhasználó a Súgó felderítő létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a Súgófelderítővel a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    * **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    * **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja a Súgó scout Egyszeri bejelentkezés](#configure-help-scout-sso)** - konfigurálni az egyszeri bejelentkezési beállításokat az alkalmazás oldalán.
    * **[Create Help Scout teszt felhasználó](#create-help-scout-test-user)** -, hogy egy megfelelője B.Simon a Súgó felderítő, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához a Súgófelderítővel hajtsa végre az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **Súgó felderítő** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

1. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban Ha az alkalmazást **IDP** által kezdeményezett módban kívánja konfigurálni, hajtsa végre a következő lépéseket:

    ![Segítség scout domain és URL-ek egyszeri bejelentkezési információk](common/idp-intiated.png)

    a. **Az azonosító** a Súgófelderítő **közönségURI-azonosítója (Service Provider Entity ID)**`urn:`

    b. **A válasz URL-címe** a Súgófelderítő **utólagos URL-címe (helyességi fogyasztói szolgáltatás URL-címe),** amely a következővel kezdődik:`https://` 

    > [!NOTE]
    > Az url-címekben lévő értékek csak szemléltetésre szolgálnak. Ezeket az értékeket a tényleges válasz URL-címéről és azonosítójából kell frissítenie. Ezeket az értékeket a Hitelesítés szakasz **Egyszeri bejelentkezés** lapjáról kapja, amelyet az oktatóanyag későbbi részében ismertetünk.

1. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    ![Segítség scout domain és URL-ek egyszeri bejelentkezési információk](common/metadata-upload-additional-signon.png)

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következőképpen:`https://secure.helpscout.net/members/login/`

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Súgófelderítő beállítása** csoportban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure-hirdetésazonosító

    c. Kijelentkezés URL-címe

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ez a szakasz célja, hogy hozzon létre egy tesztfelhasználót az Azure Portalon B.Simon nevű.

1. Az Azure Portalon a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd az **Összes felhasználó**lehetőséget.

    ![A "Felhasználók és csoportok" és a "Minden felhasználó" linkek](common/users.png)

2. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A Felhasználó tulajdonságokban hajtsa végre a következő lépéseket.

    ![A Felhasználó párbeszédpanel](common/user-properties.png)

    a. A **Név** mezőbe írja be a **B.Simon**mezőt.
  
    b. A **Felhasználónév** mezőbe írja be a **B.Simon\@yourcompanydomain.extension nevet.**  
    Például: B.Simon@contoso.com

    c. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket.

    d. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t, hozzáférést biztosítva a Súgó felderítő.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, válassza a **Minden alkalmazás**lehetőséget, majd válassza a **Súgófelderítés**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Súgó felderítő**lehetőséget.

    ![A Súgófelderítő hivatkozás az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-help-scout-sso"></a>Súgó felderítési sso-jának konfigurálása

1. A Súgófelderítőn belüli konfiguráció automatizálásához telepítenie kell a **My Apps Secure Sign-in böngészőbővítményt** **a Bővítmény telepítése**gombra kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

1. Hozzáadása után kiterjesztés a böngésző, kattintson a **Beállítása Súgó Scout** irányítja, hogy a Súgó Cserkész alkalmazás. Itt adja meg a rendszergazdai hitelesítő adatokat, hogy jelentkezzen be a Súgó felderítő. A böngésző bővítmény automatikusan konfigurálja az alkalmazást, és automatizálja a 3-7.

    ![Beállítási konfiguráció](common/setup-sso.png)

1. Ha manuálisan szeretné beállítani a Súgófelderítőt, nyisson meg egy új böngészőablakot, és jelentkezzen be rendszergazdaként a Súgófelderítő webhelyére, és hajtsa végre a következő lépéseket:

1. Kattintson a felső menü Kezelés menüjének **Kezelés parancsára,** majd válassza a **Legördülő** menü Vállalat parancsát.

    ![Egyszeri bejelentkezés konfigurálása](./media/helpscout-tutorial/settings1.png)

1. Válassza a **hitelesítés** lehetőséget a bal oldali navigációs ablakban.

    ![Egyszeri bejelentkezés konfigurálása](./media/helpscout-tutorial/settings2.png)

1. Ezzel az SAML-beállítások szakaszra lép, és végrehajtja a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/helpscout-tutorial/settings3.png)

    a. Másolja a **visszaküldött URL-címet (helyességi feltétel fogyasztói szolgáltatás URL-címe)** értéket, és illessze be az értéket az Azure Portal **Alapszintű SAML-konfiguráció** szakaszában a **Válasz URL-cím** mezőjébe.

    b. Másolja a **közönség URI-értékét (Service Provider Entity ID)** és illessze be az értéket az Azure Portal **Alapszintű SAML-konfiguráció** szakaszában az **Azonosító** mezőbe.

1. Kapcsolja be **az SAML engedélyezése** funkciót, és hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/helpscout-tutorial/settings4.png)

    a. Az **egyszeri bejelentkezésURL-címbe** illessze be a **bejelentkezési URL-cím**értékét, amelyet az Azure Portalról másolt.

    b. Kattintson **a Tanúsítvány feltöltése** gombra az Azure Portalról letöltött **tanúsítvány(Base64)** feltöltéséhez.

    c. Adja meg a szervezet e-mail tartomány(oka)t e.x.- `contoso.com` az **E-mail tartományok** mezőbe. Vesszővel több tartományt is elválaszthat. Amikor egy súgófelderítő felhasználója vagy rendszergazdája, aki belép az adott domainre a [Súgó felderítő bejelentkezési oldalán,](https://secure.helpscout.net/members/login/) a rendszer az Identitásszolgáltatóhoz irányítja, hogy hitelesítse magát a hitelesítő adataival.

    d. Végül, akkor válthat **Force SAML Sign-on,** ha azt szeretné, hogy a felhasználók csak jelentkezzen be, hogy Súgó Scout keresztül ezt a módszert. Ha továbbra is meg szeretné hagyni a lehetőséget, hogy bejelentkezzenek a Súgó felderítő hitelesítő adataival, akkor kikapcsolva is hagyhatja. Még ha ez engedélyezve is van, a fióktulajdonos mindig be tud jelentkezni a Súgóba a fiókjelszavával.

    e. Kattintson a **Mentés** gombra.

### <a name="create-help-scout-test-user"></a>Súgó felderítőteszt-felhasználólétrehozása

Ebben a szakaszban egy B.Simon nevű felhasználó jön létre a Súgó felderítőben. A Súgó felderítő támogatja a just-in-time felhasználói kiépítés, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nincs műveletelem. Ha a felhasználó még nem létezik a Súgófelderítőben, a hitelesítés után új felhasználó jön létre.

### <a name="test-sso"></a>SSO tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panel súgókereső csempéjére kattint, automatikusan be kell jelentkeznie a Súgókeresőbe, amelyhez beállította az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a Súgó felderítőt az Azure AD-vel](https://aad.portal.azure.com/)
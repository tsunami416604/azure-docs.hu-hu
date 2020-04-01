---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Symantec Web Security Service (WSS) szolgáltatással | Microsoft dokumentumok'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Symantec Web Security Service (WSS) között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: d6e4d893-1f14-4522-ac20-0c73b18c72a5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/25/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d496015440deb80a0159ed0ec234ae60c2c64a66
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73159944"
---
# <a name="tutorial-azure-active-directory-integration-with-symantec-web-security-service-wss"></a>Oktatóanyag: Az Azure Active Directory integrációja a Symantec Web Security Service (WSS) szolgáltatással

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Symantec Web Security Service (WSS) fiókját az Azure Active Directory (Azure AD) fiókkal, hogy a WSS saml-hitelesítéssel hitelesíthesse az Azure AD-ben kiépített végfelhasználót, és kényszeríthesse ki a felhasználói vagy csoportszintű házirend-szabályokat.

A Symantec Web Security Service (WSS) integrálása az Azure AD-vel a következő előnyökkel jár:

- A WSS-fiók által használt összes végfelhasználót és csoportot kezelheti az Azure AD-portálról.

- Lehetővé teszi a végfelhasználók számára, hogy hitelesítsék magukat a WSS-ben az Azure AD hitelesítő adataikkal.

- A WSS-fiókban definiált felhasználói és csoportszintű házirendszabályok kényszerítésének engedélyezése.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a Symantec Web Security Service (WSS) szolgáltatással a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* Symantec Web Security Service (WSS) egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A Symantec Web Security Service (WSS) támogatja az **IDP** által kezdeményezett sso-t

## <a name="adding-symantec-web-security-service-wss-from-the-gallery"></a>Symantec Web Security Service (WSS) hozzáadása a galériából

A Symantec Web Security Service (WSS) Azure AD-be való integrációjának konfigurálásához hozzá kell adnia a Symantec Web Security Service (WSS) szolgáltatást a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha a Symantec Web Security Service (WSS) szolgáltatást a katalógusból szeretné hozzáadni, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Symantec Web Security Service (WSS)** kifejezést, válassza a **Symantec Web Security Service (WSS)** elemlehetőséget az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Symantec Web Security Service (WSS) az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a Symantec Web Security Service (WSS) szolgáltatással egy **Britta Simon**nevű tesztfelhasználó alapján.
Az egyszeri bejelentkezés működéséhez létre kell hozni egy kapcsolatkapcsolatot egy Azure AD-felhasználó és a Symantec Web Security Service (WSS) kapcsolódó felhasználója között.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a Symantec Web Security Service (WSS) szolgáltatással a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **Konfigurálja a Symantec Web Security Service (WSS) egyszeri bejelentkezést** – az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre Symantec Web Security Service (WSS) tesztfelhasználót](#create-symantec-web-security-service-wss-test-user)** , hogy a Symantec Web Security Service (WSS) egy megfelelője legyen, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához a Symantec Web Security Service (WSS) szolgáltatással hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **Symantec Web Security Service (WSS)** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![Symantec Web Security Service (WSS) tartomány és URL-címek egyszeri bejelentkezési információk](common/idp-intiated.png)

    a. Az **Azonosító** mezőbe írjon be egy URL-címet:`https://saml.threatpulse.net:8443/saml/saml_realm`

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet:`https://saml.threatpulse.net:8443/saml/saml_realm/bcsamlpost`

    > [!NOTE]
    > Lépjen kapcsolatba a [Symantec Web Security Service (WSS) ügyféltámogatási csapatával](https://www.symantec.com/contact-us) f az **azonosító** és a **válasz URL-címének** értékei valamilyen okból nem működnek.. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány csoportjában** kattintson a **Letöltés** gombra, ha letöltheti az **összevonási metaadat-XML-t** a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

### <a name="configure-symantec-web-security-service-wss-single-sign-on"></a>A Symantec Web Security Service (WSS) egyszeri bejelentkezésének konfigurálása

A Symantec Web Security Service (WSS) oldalán való egyszeri bejelentkezés konfigurálásához olvassa el a WSS online dokumentációját. A letöltött **összevonási metaadat-XML-t** importálni kell a WSS-portálra. Ha segítségre van szüksége a WSS-portál konfigurációjával kapcsolatban, forduljon a [Symantec Web Security Service (WSS) támogatási csapatához.](https://www.symantec.com/contact-us)

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

Ebben a szakaszban engedélyezi Britta Simon számára az Azure egyszeri bejelentkezést a Symantec Web Security Service (WSS) szolgáltatáshoz való hozzáférés biztosításával.

1. Az Azure portalon válassza a **Vállalati alkalmazások**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza a **Symantec Web Security Service (WSS) lehetőséget.**

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában írja be és válassza a **Symantec Web Security Service (WSS) parancsot.**

    ![A Symantec Web Security Service (WSS) hivatkozása az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-symantec-web-security-service-wss-test-user"></a>Symantec Web Security Service (WSS) tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a Symantec Web Security Service (WSS) szolgáltatásban. A megfelelő végfelhasználói név manuálisan is létrehozható a WSS-portálon, vagy megvárhatja, amíg az Azure AD-ben kiépített felhasználók/csoportok néhány perc múlva (~15 perc) szinkronizálódnak a WSS-portállal. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat. A végfelhasználói számítógép nyilvános IP-címét, amely et a webhelyek böngészésére használják, a Symantec Web Security Service (WSS) portálon is ki kell építeni.

> [!NOTE]
> Kérjük, kattintson [ide,](https://www.bing.com/search?q=my+ip+address&qs=AS&pq=my+ip+a&sc=8-7&cvid=29A720C95C78488CA3F9A6BA0B3F98C5&FORM=QBLH&sp=1) hogy a gép nyilvános IPaddress.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban teszteli az egyszeri bejelentkezési funkciót, most, hogy beállította a WSS-fiókot az Azure AD SAML-hitelesítéshez való használatára.

Miután beállította a webböngészőt a WSS-be irányuló proxyforgalomra, amikor megnyitja a webböngészőt, és megpróbál egy webhelyet böngészni, akkor a rendszer átirányítja az Azure bejelentkezési oldalára. Adja meg a teszt végfelhasználó, amely ki van építve az Azure AD (azaz BrittaSimon) és a kapcsolódó jelszó. A hitelesítést követően böngészhet a kiválasztott webhelyközött. Ha házirendszabályt hoz létre a WSS oldalán, hogy megakadályozza brittaSimon böngészését egy adott webhelyre, akkor látnia kell a WSS blokk oldalát, amikor brittaSimon felhasználóként próbál meg böngészni az adott webhelyen.

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


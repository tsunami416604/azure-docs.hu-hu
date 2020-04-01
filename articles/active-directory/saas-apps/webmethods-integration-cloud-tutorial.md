---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a webMethods integration suite-mal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a webMethods Integration Suite között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 97261535-7a2d-4d73-94c8-38116b8a776e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.openlocfilehash: 30fc55cb608189e1be82c2b28393c5e242e262fd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160862"
---
# <a name="tutorial-azure-active-directory-integration-with-webmethods-integration-suite"></a>Oktatóanyag: Az Azure Active Directory integrációja a webmethods integrációs csomaggal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a webmethods integrációs csomagot az Azure Active Directoryval (Azure AD).
A webmethods integration suite integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a webmethods integrációs csomaghoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve webMethods Integration Suite (Single Sign-On) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció webMethods Integration Suite alkalmazásával való konfigurálásához a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/)egy hónapos próbaverziót kaphat.
* webMethods Integration Suite egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A webMethods Integration Suite támogatja az **SP** és **az IDP** által kezdeményezett SSO-t

* A webMethods Integration Suite támogatja **a just-in-time** felhasználói kiépítést

## <a name="adding-webmethods-integration-suite-from-the-gallery"></a>WebMethods Integration Suite hozzáadása a galériából

A webMethods Integration Suite azure AD-be való integrálásának konfigurálásához hozzá kell adnia a webMethods Integration Suite-ot a katalógusból a felügyelt SaaS-alkalmazások listájához.

**WebMethods Integration Suite hozzáadásához a galériából hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **webMethods Integration Suite**parancsot, válassza ki a **webMethods Integration Suite** elemet az eredménypanelről, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![webMethods Integrációs Csomag az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést a webMethods Integration Suite szolgáltatással egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés működéséhez létre kell hozni egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó webMethods Integration Suite létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a webMethods Integration Suite csomaggal a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a webMethods Integration Suite Single Sign-On](#configure-webmethods-integration-suite-single-sign-on)** --t az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre webMethods Integration Suite teszt felhasználó](#create-webmethods-integration-suite-test-user)** - egy megfelelője Britta Simon webMethods integration suite, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához a webMethods Integration Suite csomaggal hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **webMethods Integration Suite** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. A **webMethods Integration Cloud**konfigurálásához az **Egyszerű SAML-konfiguráció** szakaszban, ha az alkalmazást **IDP** által kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    ![a webMethods Integration Suite tartomány és url-címek egyszeri bejelentkezési információi](common/idp-intiated.png)

    a. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:

    | |
    |--|
    | `<SUBDOMAIN>.webmethodscloud.com` |
    | `<SUBDOMAIN>.webmethodscloud.eu` |
    | `<SUBDOMAIN>.webmethodscloud.de` |

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:

    | |
    |--|
    | `https://<SUBDOMAIN>.webmethodscloud.com/integration/live/saml/ssoResponse` |
    | `https://<SUBDOMAIN>.webmethodscloud.eu/integration/live/saml/ssoResponse` |
    | `https://<SUBDOMAIN>.webmethodscloud.de/integration/live/saml/ssoResponse` |

    c. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    ![a webMethods Integration Suite tartomány és url-címek egyszeri bejelentkezési információi](common/metadata-upload-additional-signon.png)

    d. A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:

    | |
    |--|
    | `https://<SUBDOMAIN>.webmethodscloud.com/integration/live/saml/ssoRequest` |
    | `https://<SUBDOMAIN>.webmethodscloud.eu/integration/live/saml/ssoRequest` |
    | `https://<SUBDOMAIN>.webmethodscloud.de/integration/live/saml/ssoRequest` |

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Lépjen kapcsolatba [a webMethods Integration Suite ügyféltámogatási csapatával,](https://empower.softwareag.com/) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

5. A **webMethods API Cloud**konfigurálásához az **Alapvető SAML-konfiguráció** szakaszban, ha az alkalmazást **IDP** által kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    ![a webMethods Integration Suite tartomány és url-címek egyszeri bejelentkezési információi](common/idp-intiated.png)

    a. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:

    | |
    |--|
    | `<SUBDOMAIN>.webmethodscloud.com` |
    | `<SUBDOMAIN>.webmethodscloud.eu` |
    | `<SUBDOMAIN>.webmethodscloud.de` |

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:

    | |
    |--|
    | `https://<SUBDOMAIN>.webmethodscloud.com/umc/rest/saml/initsso` |
    | `https://<SUBDOMAIN>.webmethodscloud.eu/umc/rest/saml/initsso` |
    | `https://<SUBDOMAIN>.webmethodscloud.de/umc/rest/saml/initsso` |

    c. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    ![a webMethods Integration Suite tartomány és url-címek egyszeri bejelentkezési információi](common/metadata-upload-additional-signon.png)

    d. A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:

    | |
    |--|
    | `https://api.webmethodscloud.com/umc/rest/saml/initsso/?tenant=<TENANTID>` |
    | `https://api.webmethodscloud.eu/umc/rest/saml/initsso/?tenant=<TENANTID>` |
    | `https://api.webmethodscloud.de/umc/rest/saml/initsso/?tenant=<TENANTID>` |

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Lépjen kapcsolatba [a webMethods Integration Suite ügyféltámogatási csapatával,](https://empower.softwareag.com/) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

6. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány csoportjában** kattintson a **Letöltés** gombra, ha letöltheti az **összevonási metaadat-XML-t** a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

7. A **Set up webMethods Integration Suite (WebMethods Integration Suite** beállítása) szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure-hirdetésazonosító

    c. Kijelentkezés URL-címe

### <a name="configure-webmethods-integration-suite-single-sign-on"></a>WebMethods Integration Suite egyszeri bejelentkezés konfigurálása

Az egyszeri bejelentkezés konfigurálásához a **webMethods Integration Suite** oldalára kell küldenie a letöltött **összevonási metaadat-XML-t** és a megfelelő másolt URL-címeket az Azure Portalról a [webMethods Integration Suite támogatási csapatának.](https://empower.softwareag.com/) Úgy állították be ezt a beállítást, hogy az SAML SSO-kapcsolat mindkét oldalon megfelelően legyen beállítva.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ez a szakasz célja, hogy hozzon létre egy tesztfelhasználót az Azure Portalon Britta Simon.

1. Az Azure Portalon a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd az **Összes felhasználó**lehetőséget.

    ![A "Felhasználók és csoportok" és a "Minden felhasználó" linkek](common/users.png)

2. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A Felhasználó tulajdonságokban hajtsa végre a következő lépéseket.

    ![A Felhasználó párbeszédpanel](common/user-properties.png)

    a. A **Név** mezőbe írja be **a BrittaSimon**értéket.
  
    b. A **Felhasználónév** mezőbe írja be a **brittasimon\@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket.

    d. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés használatával a webMethods Integration Suite hozzáférést biztosít.

1. Az Azure portalon válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza a **webMethods Integration Suite**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **webMethods Integration Suite lehetőséget.**

    ![A webMethods Integration Suite hivatkozása az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-webmethods-integration-suite-test-user"></a>WebMethods Integration Suite tesztfelhasználó létrehozása

In this section, a user called Britta Simon is created in webMethods Integration Suite. A webMethods Integration Suite támogatja a just-in-time felhasználói kiépítést, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nincs műveletelem. Ha a felhasználó már nem létezik a webMethods integration suite, egy új jön létre a hitelesítés után.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha a Access Panelen a webMethods Integration Suite csempére kattint, automatikusan be kell jelentkeznie a webMethods integrációs csomagba, amelyhez beállítja az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


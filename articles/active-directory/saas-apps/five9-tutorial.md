---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Five9 Plus adapterrel (CTI, Contact Center-ügynökök) | Microsoft dokumentumok'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Five9 Plus adapter (CTI, Contact Center-ügynökök) között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 88dc82ab-be0b-4017-8335-c47d00775d7b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/04/2019
ms.author: jeedes
ms.openlocfilehash: dd4eab2d8e877cac468097c0a6ed6aa3cfb750b9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67102449"
---
# <a name="tutorial-azure-active-directory-integration-with-five9-plus-adapter-cti-contact-center-agents"></a>Oktatóanyag: Az Azure Active Directory integrációja a Five9 Plus adapterrel (CTI, Contact Center-ügynökök)

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Five9 Plus adaptert (CTI, Contact Center-ügynökök) az Azure Active Directoryval (Azure AD).
A Five9 Plus adapter (CTI, Contact Center-ügynökök) integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a Five9 Plus adapterhez (CTI, Contact Center-ügynökök).
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve legyenek a Five9 Plus adapterre (CTI, Contact Center-ügynökök) (Single Sign-On) az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a Five9 Plus adapterrel (CTI, Contact Center-ügynökökkel) a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/)kaphat.
* Five9 Plus adapter (CTI, Contact Center Agents) egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A Five9 Plus adapter (CTI, Contact Center Agents) támogatja az **IDP** által kezdeményezett SSO-t

## <a name="adding-five9-plus-adapter-cti-contact-center-agents-from-the-gallery"></a>Five9 Plus adapter (CTI, Contact Center agents) hozzáadása a galériából

A Five9 Plus adapter (CTI, Contact Center-ügynökök) Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Five9 Plus adaptert (CTI, Kapcsolattartó központ ügynökei) a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha five9 plus adaptert (CTI, Kapcsolattartó központ ügynökeit) szeretne hozzáadni a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Five9 Plus adapter (CTI, Contact Center Agents)** kifejezést, válassza a **Five9 Plus adapter (CTI, Contact Center Agents)** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Five9 Plus adapter (CTI, Contact Center Agents) az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a Five9 Plus adapterrel (CTI, Contact Center-ügynökök) egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, az Azure AD-felhasználó és a kapcsolódó felhasználó a Five9 Plus adapter (CTI, Contact Center-ügynökök) közötti kapcsolat létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a Five9 Plus adapterrel (CTI, Contact Center-ügynökök) a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[A Five9 Plus adapter (CTI, Contact Center Agents) egyszeri bejelentkezéskonfigurálása](#configure-five9-plus-adapter-cti-contact-center-agents-single-sign-on)** – az alkalmazás oldalon az egyszeri bejelentkezés beállításainak konfigurálásához.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre Five9 Plus adapter (CTI, Contact Center Agents) teszt felhasználó](#create-five9-plus-adapter-cti-contact-center-agents-test-user)** - egy megfelelője Britta Simon five9 plus adapter (CTI, Contact Center ügynökök), amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének five9 plus adapterrel (CTI, Contact Center-ügynökök) történő konfigurálásához hajtsa végre az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **Five9 Plus adapter (CTI, Contact Center Agents)** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon hajtsa végre az alábbi lépéseket:

    ![Five9 Plus adapter (CTI, Contact Center Agents) tartomány és URL-címek egyszeri bejelentkezési információk](common/idp-intiated.png)

    a. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:
    
    |    Környezet      |       URL-cím      |
    | :-- | :-- |
    | For "Five9 Plus Adapter for Microsoft Dynamics CRM" | `https://app.five9.com/appsvcs/saml/metadata/alias/msdc` |
    | A "Five9 Plus Adapter zendeskhez" | `https://app.five9.com/appsvcs/saml/metadata/alias/zd` |
    | A "Five9 Plus adapter ügynök asztali eszközkészlet" | `https://app.five9.com/appsvcs/saml/metadata/alias/adt` |

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:

    |      Környezet     |      URL-cím      |
    | :--                  | :--           |
    | For "Five9 Plus Adapter for Microsoft Dynamics CRM" | `https://app.five9.com/appsvcs/saml/SSO/alias/msdc` |
    | A "Five9 Plus Adapter zendeskhez" | `https://app.five9.com/appsvcs/saml/SSO/alias/zd` |
    | A "Five9 Plus adapter ügynök asztali eszközkészlet" | `https://app.five9.com/appsvcs/saml/SSO/alias/adt` |

6. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

7. A **Five9 Plus adapter (CTI, Contact Center Agents) beállítása** csoportban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-five9-plus-adapter-cti-contact-center-agents-single-sign-on"></a>Five9 Plus adapter (CTI, Contact Center Agents) egyszeri bejelentkezés konfigurálása

1. A **Five9 Plus adapter (CTI, Contact Center Agents)** oldalán való egyszeri bejelentkezés konfigurálásához el kell küldenie a letöltött **tanúsítvány(Base64)** és a megfelelő másolt URL-cím(ek)et a [Five9 Plus Adapter (CTI, Contact Center Agents) támogatási csapatának.](https://www.five9.com/about/contact) Ezenkívül az SSO további konfigurálásához kövesse az adapternek megfelelő alábbi lépéseket:

    a. "Five9 Plus adapter agent asztali eszközkészlet" Felügyeleti útmutató:[https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf)
    
    b. "Five9 Plus adapter a Microsoft Dynamics CRM rendszerhez" felügyeleti útmutató:[https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf)
    
    c. "Five9 Plus Adapter zendesk" admin útmutató:[https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf)

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

Ebben a szakaszban engedélyezheti Britta Simon számára az Azure egyszeri bejelentkezést a Five9 Plus adapter (CTI, Contact Center-ügynökök) elérésének engedélyezésével.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, válassza a **Minden alkalmazás**lehetőséget, majd válassza a **Five9 Plus adapter (CTI, Contact Center-ügynökök) lehetőséget.**

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Five9 Plus adapter (CTI, Contact Center Agents) lehetőséget.**

    ![A Five9 Plus adapter (CTI, Contact Center Agents) hivatkozás a Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-five9-plus-adapter-cti-contact-center-agents-test-user"></a>Five9 Plus adapter (CTI, Contact Center Agents) tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a Five9 Plus adapterben (CTI, Contact Center-ügynökök). A [Five9 Plus adapter (CTI, Contact Center Agents) támogatási csapatával](https://www.five9.com/about/contact) együttműködve hozzáadhatja a felhasználókat a Five9 Plus Adapter (CTI, Contact Center Agents) platformon. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat. 

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha a Five9 Plus adapterre (CTI, Contact Center Agents csempe a hozzáférési panelen) kattint, automatikusan be kell jelentkeznie a Five9 Plus adapterbe (CTI, Contact Center-ügynökök), amelyhez beállítja az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


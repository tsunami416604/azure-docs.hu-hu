---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az RFPIO-val | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az RFPIO között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 87187076-7b50-4247-814f-f217b052703f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 63d7b6af8ff76c890b98c29ded0e8bdc637b45dd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67092849"
---
# <a name="tutorial-azure-active-directory-integration-with-rfpio"></a>Oktatóanyag: Az Azure Active Directory integrációja az RFPIO-val

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az RFPIO-t az Azure Active Directoryval (Azure AD).
Az RFPIO integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá az RFPIO-hoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve rfpio (Single Sign-On) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához az RFPIO-val a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) kaphat
* RFPIO egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* Az RFPIO támogatja az **SP és az IDP** által kezdeményezett SSO-t

## <a name="adding-rfpio-from-the-gallery"></a>RFPIO hozzáadása a galériából

Az RFPIO azure AD-be való integrálásának konfigurálásához hozzá kell adnia az RFPIO-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha rfpio-t szeretne hozzáadni a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **RFPIO**parancsot, válassza az **RFPIO** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![RFPIO az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését az RFPIO-val egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, az Azure AD-felhasználó és a kapcsolódó felhasználó közötti kapcsolat RFPIO létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez az RFPIO szolgáltatással a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja az RFPIO Single Sign-On](#configure-rfpio-single-sign-on)** --t az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre RFPIO-tesztfelhasználót](#create-rfpio-test-user)** – ha az RFPIO-ban britta Simon megfelelője van, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének rfpio-val való konfigurálásához hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)az **RFPIO-alkalmazásintegrációs** lapon válassza az Egyszeri **bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban, ha az alkalmazást **IDP** által kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépést:

    ![RFPIO-tartomány és URL-címek egyszeri bejelentkezési információi](common/idp-identifier.png)

    a. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`https://www.rfpio.com`

    b. Kattintson **a További URL-címek beállítása gombra.**

    c. A **Továbbítási állapot** szövegmezőbe írjon be egy karakterláncértéket. Lépjen kapcsolatba [az RFPIO támogatási csapatával](https://www.rfpio.com/contact/) az érték lekéréséhez.

    ![RFPIO-tartomány és URL-címek egyszeri bejelentkezési információi](common/idp-preintegrated-relay.png)

5. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    ![image](common/both-preintegrated-signon.png)

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://www.app.rfpio.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító és bejelentkezési URL-címmel. Lépjen kapcsolatba [az RFPIO ügyféltámogatási csapatával](https://www.rfpio.com/contact/) az értékek lekéréséhez. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

6. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány csoportjában** kattintson a **Letöltés** gombra, ha letöltheti az **összevonási metaadat-XML-t** a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

7. Az **RFPIO beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-rfpio-single-sign-on"></a>RfPIO egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be az **RFPIO** webhelyére rendszergazdaként.

1. Kattintson a bal alsó sarokban legördülő menüben.

    ![Egyszeri bejelentkezés konfigurálása](./media/rfpio-tutorial/app1.png)

1. Kattintson a **Szervezeti beállítások gombra.** 

    ![Egyszeri bejelentkezés konfigurálása](./media/rfpio-tutorial/app2.png)

1. Kattintson a **JELLEMZŐK & INTEGRÁCIÓ**.

    ![Egyszeri bejelentkezés konfigurálása](./media/rfpio-tutorial/app4.png)

1. Az **SAML SSO-konfigurációban** kattintson a **Szerkesztés gombra.**

    ![Egyszeri bejelentkezés konfigurálása](./media/rfpio-tutorial/app3.png)

1. Ebben a szakaszban hajtsa végre a következő műveleteket:

    ![Egyszeri bejelentkezés konfigurálása](./media/rfpio-tutorial/app5.png)
    
    a. Másolja a **letöltött metaadat-XML** tartalmát, és illessze be az **identitáskonfigurációs** mezőbe.

    > [!NOTE]
    >A letöltött **összevonási metaadat-XML** tartalmának másolásához használja **a Jegyzettömb++** vagy a megfelelő **XML-szerkesztőt.**

    b. Kattintson a **Validate** (Érvényesítés) elemre.

    c. Miután az **Érvényesítés**gombra kattintott, bekapcsolja az **SAML(Engedélyezve)** gombra.

    d. Kattintson a **Küldés gombra.**

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

Ebben a szakaszban engedélyezi Britta Simon az Azure egyszeri bejelentkezést az RFPIO-hoz való hozzáférés biztosításával.

1. Az Azure portalon válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza az **RFPIO**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza az **RFPIO**lehetőséget.

    ![Az RFPIO hivatkozás az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-rfpio-test-user"></a>RFPIO-tesztfelhasználó létrehozása

1. Jelentkezzen be az RFPIO vállalati webhelyére rendszergazdaként.

1. Kattintson a bal alsó sarokban legördülő menüben.

    ![Egyszeri bejelentkezés konfigurálása](./media/rfpio-tutorial/app1.png)

1. Kattintson a **Szervezeti beállítások gombra.** 

    ![Egyszeri bejelentkezés konfigurálása](./media/rfpio-tutorial/app2.png)

1. Kattintson **a TEAM MEMBERS gombra.**

    ![Egyszeri bejelentkezés konfigurálása](./media/rfpio-tutorial/app6.png)

1. Kattintson **a TAGOK HOZZÁADÁSA gombra.**

    ![Egyszeri bejelentkezés konfigurálása](./media/rfpio-tutorial/app7.png)

1. Az **Új tagok hozzáadása** szakaszban. Hajtsa végre a következő műveleteket:

    ![Egyszeri bejelentkezés konfigurálása](./media/rfpio-tutorial/app8.png)

    a. Írja be **az e-mail címet** a **Soran egy e-mail megadása** mezőbe.

    b. Kérjük, válassza **a Szerepkör** az Ön igényeinek megfelelően lehetőséget.

    c. Kattintson **a TAGOK hozzáadása gombra.**

    > [!NOTE]
    > Az Azure Active Directory-fiók tulajdonosa kap egy e-mailt, és egy hivatkozást követ, hogy erősítse meg a fiók, mielőtt aktívvá válik.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a hozzáférési panelen az RFPIO csempére kattint, automatikusan be kell jelentkeznie arra az RFPIO-ba, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


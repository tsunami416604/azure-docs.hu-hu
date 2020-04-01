---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Nimblex-szel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Nimblex között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d3e165a5-f062-4b50-ac0b-b400838e99cd
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: jeedes
ms.openlocfilehash: cd7199e94a58b3f0c121a0fd9401bff94406ed84
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67096098"
---
# <a name="tutorial-azure-active-directory-integration-with-nimblex"></a>Oktatóanyag: Az Azure Active Directory integrációja a Nimblex-szel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Nimblexet az Azure Active Directoryval (Azure AD).
A Nimblex és az Azure AD integrálása a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a Nimblexhez.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve nimblex (Single Sign-On) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció nimblex-szel való konfigurálásához a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* Nimblex egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A Nimblex támogatja az **SP** által kezdeményezett SSO-t

* A Nimblex támogatja a **Just In Time** felhasználói kiépítést

## <a name="adding-nimblex-from-the-gallery"></a>Nimblex hozzáadása a galériából

A Nimblex Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Nimblex-et a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha nimblexet szeretne hozzáadni a galériából, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Nimblex ( Nimblex**) kifejezést, válassza a **Nimblex** elemet az eredménypanelről, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Nimblex az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a Nimblex-szel egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, az Azure AD-felhasználó és a kapcsolódó felhasználó a Nimblex közötti kapcsolat létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a Nimblex segítségével a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[A Nimblex Single Sign-On konfigurálásával](#configure-nimblex-single-sign-on)** konfigurálhatja az egyszeri bejelentkezés beállításait az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre Nimblex teszt felhasználó](#create-nimblex-test-user)** - egy megfelelője Britta Simon a Nimblex, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének nimblex-szel való konfigurálásához hajtsa végre az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **Nimblex** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![Nimblex tartomány és URL egyszeri bejelentkezési információk](common/sp-identifier-reply.png)

    a. A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<YOUR APPLICATION PATH>/Login.aspx`

    b. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<YOUR APPLICATION PATH>/`

    c. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://<path-to-application>/SamlReply.aspx`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel, azonosítóval és válasz URL-címmel. Lépjen kapcsolatba [a Nimblex ügyféltámogatási csapatával,](mailto:support@ebms.com.au) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

4. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány szakaszában** kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **Nimblex beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-nimblex-single-sign-on"></a>Nimblex egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Nimblex-be biztonsági rendszergazdaként.

2. A lap jobb felső részén kattintson a **Beállítások** embléma elemre.

    ![Nimblex beállítások](./media/nimblex-tutorial/tutorial_nimblex_settings.png)

3. A **Vezérlőpult** lap **Biztonság csoportban** kattintson **az Egyszeri bejelentkezés gombra.**

    ![Nimblex beállítások](./media/nimblex-tutorial/tutorial_nimblex_single.png)

4. Az **Egyszeri bejelentkezés kezelése** lapon jelölje ki a példány nevét, és kattintson a **Szerkesztés**gombra.

    ![Nimblex saml](./media/nimblex-tutorial/tutorial_nimblex_saml.png)

5. Az **SSO-szolgáltató szerkesztése** lapon hajtsa végre az alábbi lépéseket:

    ![Nimblex saml](./media/nimblex-tutorial/tutorial_nimblex_sso.png)

    a. A **Leírás** mezőbe írja be a példány nevét.

    b. A Jegyzettömbben nyissa meg az Azure Portalról letöltött base-64 kódolású tanúsítványt, másolja a tartalmát, majd illessze be a **Tanúsítvány** mezőbe.

    c. Az **identitásszolgáltató Sso cél URL-cím** mezőbe illessze be a **bejelentkezési URL-cím**értékét, amelyet az Azure Portalról másolt.

    d. Kattintson a **Mentés** gombra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása 

Ez a szakasz célja, hogy hozzon létre egy tesztfelhasználót az Azure Portalon Britta Simon.

1. Az Azure Portalon a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd az **Összes felhasználó**lehetőséget.

    ![A "Felhasználók és csoportok" és a "Minden felhasználó" linkek](common/users.png)

2. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A Felhasználó tulajdonságokban hajtsa végre a következő lépéseket.

    ![A Felhasználó párbeszédpanel](common/user-properties.png)

    a. A **Név** mezőbe írja be **a BrittaSimon**értéket.
  
    b. A **Felhasználónév** mezőbe írja be a következőt:**brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket.

    d. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés i nimblex hozzáférést biztosít.

1. Az Azure portalon válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd a **Nimblex**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Nimblex**lehetőséget.

    ![A Nimblex hivatkozás az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-nimblex-test-user"></a>Nimblex tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a Nimblex ben. A Nimblex támogatja a just-in-time felhasználói kiépítést, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nincs műveletelem. Ha a felhasználó még nem létezik a Nimblex, egy új jön létre a hitelesítés után.

>[!Note]
>Ha manuálisan kell létrehoznia egy felhasználót, forduljon [a Nimblex ügyféltámogatási csapatához.](mailto:support@ebms.com.au)

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelNimblex csempéjére kattint, automatikusan be kell jelentkeznie arra a Nimblex-be, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


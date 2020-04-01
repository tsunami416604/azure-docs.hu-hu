---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az SCC életciklusával | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az SCC lifecycle között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9748bf38-ffc3-4d51-a1ae-207ce57104fa
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/22/2019
ms.author: jeedes
ms.openlocfilehash: 83974a57fe080e76dee26781abaddf2b0b884839
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67091495"
---
# <a name="tutorial-azure-active-directory-integration-with-scc-lifecycle"></a>Oktatóanyag: Az Azure Active Directory integrációja az SCC életciklusával

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az SCC lifecycle-t az Azure Active Directoryval (Azure AD).
Az SCC életciklusának az Azure AD-vel való integrálása a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá az SCC életciklusához.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve scc lifecycle (single sign-on) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához az SCC lifecycle-rel a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) kaphat
* SCC LifeCycle egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* Az SCC életciklusa támogatja az **SP** által kezdeményezett SSO-t

## <a name="adding-scc-lifecycle-from-the-gallery"></a>Az SCC életciklus hozzáadása a galériából

Az SCC LifeCycle azure AD-be való integrálásának konfigurálásához hozzá kell adnia az SCC LifeCycle-et a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha az SCC életciklusát a gyűjteményből szeretné hozzáadni, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **SCC LifeCycle (SCC-életciklus) kifejezést,** válassza az **SCC életciklusát** az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![SCC életciklus az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését az SCC LifeCycle szolgáltatással egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, az Azure AD-felhasználó és a kapcsolódó felhasználó között az SCC életciklusa közötti kapcsolat létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez az SCC lifecycle segítségével a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja az SCC életciklusának egyszeri bejelentkezését](#configure-scc-lifecycle-single-sign-on)** – az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre SCC lifecycle teszt felhasználó](#create-scc-lifecycle-test-user)** – egy megfelelője Britta Simon az SCC lifecycle, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához az SCC lifecycle-del hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)az **SCC életciklus-alapú alkalmazásintegrációs** lapján válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![SCC életciklus-tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-identifier.png)

    a. A Bejelentkezés az **URL-cím** mezőbe írja be az URL-címet a következő minta használatával:`https://<sub-domain>.scc.com/ic7/welcome/customer/PICTtest.aspx`

    b. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő minta használatával:
    
    | |
    |--|
    | `https://bs1.scc.com/<entity>`|
    | `https://lifecycle.scc.com/<entity>`|

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Bejelentkezési URL-címmel és azonosítóval. Lépjen kapcsolatba [az SCC életciklus-ügyfél támogatási csapatával,](mailto:lifecycle.support@scc.com) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány csoportjában** kattintson a **Letöltés** gombra, ha letöltheti az **összevonási metaadat-XML-t** a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. Az **SCC életciklusának beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-scc-lifecycle-single-sign-on"></a>Az SCC életciklusának egyetlen bejelentkezéskonfigurálása

Az egyszeri bejelentkezés konfigurálásához az **SCC lifecycle** oldalán el kell küldenie a letöltött **metaadat-XML-t** és a megfelelő másolt URL-címeket az Azure Portalról az [SCC életciklus támogatási csapatának.](mailto:lifecycle.support@scc.com) Úgy állították be ezt a beállítást, hogy az SAML SSO-kapcsolat mindkét oldalon megfelelően legyen beállítva.

   > [!NOTE]
   > Az egyszeri bejelentkezést az [SCC életciklus-támogató csapatának kell engedélyeznie.](mailto:lifecycle.support@scc.com)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ez a szakasz célja, hogy hozzon létre egy tesztfelhasználót az Azure Portalon Britta Simon.

1. Az Azure Portalon a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd az **Összes felhasználó**lehetőséget.

    ![A "Felhasználók és csoportok" és a "Minden felhasználó" linkek](common/users.png)

2. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A Felhasználó tulajdonságokban hajtsa végre a következő lépéseket.

    ![A Felhasználó párbeszédpanel](common/user-properties.png)

    a. A **Név** mezőbe írja be **a BrittaSimon**értéket.
  
    b. A **Felhasználónév** mezőtípusban`brittasimon@yourcompanydomain.extension`  
    Például: BrittaSimon@contoso.com

    c. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket.

    d. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés használatával scc életciklus ának engedélyezésével.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza az **SCC életciklusát**.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza az **SCC életciklusa**lehetőséget.

    ![Az SCC életciklusa hivatkozás az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-scc-lifecycle-test-user"></a>Az SCC életciklus-tesztfelhasználójának létrehozása

Annak érdekében, hogy az Azure AD-felhasználók bejelentkezhessenek az SCC életciklusába, ki kell építeni őket az SCC életciklusába. Nincs műveletelem, amely konfigurálhatja a felhasználó kiépítés scc életciklus.

Amikor egy hozzárendelt felhasználó megpróbál bejelentkezni az SCC életciklusába, szükség esetén automatikusan létrejön egy SCC életciklus-fiók.

> [!NOTE]
> Az Azure Active Directory-fiók tulajdonosa kap egy e-mailt, és egy hivatkozást követ, hogy erősítse meg a fiók, mielőtt aktívvá válik.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen az SCC életcikluscsempére kattint, automatikusan be kell jelentkeznie az SCC életciklusába, amelyhez beállítja az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

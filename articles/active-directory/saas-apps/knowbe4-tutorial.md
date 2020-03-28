---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a KnowBe4 biztonsági tudatossági képzésével | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a KnowBe4 biztonsági tudatossági képzés között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: b80d2212-cc5f-4adb-836c-570640810c39
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f774357dc2fd1e37466c2c338e8e8616ae599d12
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73159674"
---
# <a name="tutorial-azure-active-directory-integration-with-knowbe4-security-awareness-training"></a>Oktatóanyag: Az Azure Active Directory integrációja a KnowBe4 biztonsági tudatossággal kapcsolatos képzéssel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a KnowBe4 biztonsági tudatosságképzését az Azure Active Directoryval (Azure AD).
A KnowBe4 biztonsági tudatossági képzésintegrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a KnowBe4 biztonsági tudatossági képzéshez.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve knowbe4 biztonsági tudatosság képzés (Single Sign-On) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a KnowBe4 biztonsági tudatosságképzéssel a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* KnowBe4 biztonsági tudatosság képzés egyszeri bejelentkezéses előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A KnowBe4 biztonsági tudatossági képzés támogatja az **SP** által kezdeményezett sso-t

* A KnowBe4 biztonsági tudatossági tanfolyam támogatja **a Just In Time** felhasználói kiépítést

## <a name="adding-knowbe4-security-awareness-training-from-the-gallery"></a>KnowBe4 biztonsági tudatossági képzés hozzáadása a galériából

A KnowBe4 biztonsági tudatossági képzés azure AD-be való integrálásának konfigurálásához hozzá kell adnia a KnowBe4 biztonsági tudatossági képzését a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha a know-be4 biztonsági tudatossági oktatótanfolyamot szeretné hozzáadni a galériából, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **KnowBe4 biztonsági tudatossági képzés ,** válassza **a KnowBe4 biztonsági tudatosság képzés** az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![KnowBe4 biztonsági tudatosság képzés az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a KnowBe4 biztonsági tudatossági képzéssel egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, egy Azure AD-felhasználó és a kapcsolódó felhasználó a KnowBe4 biztonsági tudatosság képzés kapcsolatot kell létrehozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a KnowBe4 biztonsági tudatossági oktatószolgálattal a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a KnowBe4 biztonsági tudatosság képzés egyszeri bejelentkezés](#configure-knowbe4-security-awareness-training-single-sign-on)** – konfigurálni az egyszeri bejelentkezési beállításokat az alkalmazás oldalán.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre KnowBe4 security awareness training tesztfelhasználót](#create-knowbe4-security-awareness-training-test-user)** – ha a KnowBe4 biztonsági tudatossági képzésben britta Simon megfelelője kapcsolódik a felhasználó Azure AD-megjelenítéséhez.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához a KnowBe4 biztonsági tudatossági képzéssel hajtsa végre az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **KnowBe4 biztonsági tudatosságképzési** alkalmazás integrációs lapján válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![KnowBe4 biztonsági tudatosság képzési tartomány és URL egyszeri bejelentkezési információk](common/sp-identifier.png)

    a. A Bejelentkezés az **URL-cím** mezőbe írja be az URL-címet a következő minta használatával:`https://<companyname>.KnowBe4.com/auth/saml/<instancename>`

    > [!NOTE]
    > Az URL-cím bejelentkezési értéke nem valós. Frissítse ezt az értéket a tényleges Bejelentkezési URL-címmel. Lépjen kapcsolatba [a KnowBe4 biztonsági tudatossággal kapcsolatos képzési ügyféltámogatási csapatával](mailto:support@KnowBe4.com) ennek az értéknek a lefelvételéhez. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintára is hivatkozhat.

    b. Az **Azonosító (entitásazonosító)** mezőbe írja be a karakterlánc értékét:`KnowBe4`

    > [!NOTE]
    > Ez a kis- és nagybetűk et is figyelembe nem egyezik.

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **Tanúsítvány (Raw)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificateraw.png)

6. A **KnowBe4 beállítása biztonsági tudatosság képzés** e szakaszban másolja a megfelelő URL-cím(ek) a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure-hirdetésazonosító

    c. Kijelentkezés URL-címe

### <a name="configure-knowbe4-security-awareness-training-single-sign-on"></a>A KnowBe4 biztonsági tudatossággal kapcsolatos képzés konfigurálása egyszeri bejelentkezés

A **KnowBe4 biztonsági tudatossággal kapcsolatos oktatóoldalán** történő egyszeri bejelentkezés konfigurálásához el kell küldenie a letöltött **tanúsítványt (Raw)** és a megfelelő másolt URL-címeket az Azure Portalról a [KnowBe4 biztonsági tudatossággal kapcsolatos képzés támogatási csapatának.](mailto:support@KnowBe4.com) Úgy állították be ezt a beállítást, hogy az SAML SSO-kapcsolat mindkét oldalon megfelelően legyen beállítva.

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

Ebben a szakaszban engedélyezi Britta Simon számára az Azure egyszeri bejelentkezést a KnowBe4 biztonsági tudatossági képzéshez való hozzáférés biztosításával.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza a **KnowBe4 biztonsági tudatossági képzés lehetőséget.**

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **KnowBe4 biztonsági tudatossági képzés lehetőséget.**

    ![A KnowBe4 biztonsági tudatosság képzése hivatkozás az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-knowbe4-security-awareness-training-test-user"></a>KnowBe4 biztonsági tudatosságképzés-felhasználó létrehozása

A cél ebben a szakaszban az, hogy hozzon létre egy felhasználó nevű Britta Simon knowbe4 biztonsági tudatosság képzés. KnowBe4 biztonsági tudatosság képzés támogatja a just-in-time kiépítése, amely alapértelmezés szerint engedélyezve van.

Ebben a szakaszban nincs műveletelem. Új felhasználó jön létre a KnowBe4 biztonsági tudatossági képzés elérésére tett kísérlet során, ha még nem létezik.

> [!NOTE]
> Ha manuálisan kell létrehoznia egy felhasználót, kapcsolatba kell lépnie a [KnowBe4 biztonsági tudatossággal kapcsolatos képzés támogatási csapatával.](mailto:support@KnowBe4.com)

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha a Hozzáférési panel KnowBe4 biztonsági tudatossági képzéscsempéjére kattint, automatikusan be kell jelentkeznie a KnowBe4 biztonsági tudatossági képzésébe, amelyhez beállítja az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

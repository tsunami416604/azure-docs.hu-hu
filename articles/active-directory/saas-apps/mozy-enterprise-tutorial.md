---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Mozy Enterprise szolgáltatással | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Mozy Enterprise között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 489b5e62-85c2-45c9-8766-326632d48114
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: 7d3b7f8982a24856818cbb6ce86060beac969fc2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74233496"
---
# <a name="tutorial-azure-active-directory-integration-with-mozy-enterprise"></a>Oktatóanyag: Az Azure Active Directory integrációja a Mozy Enterprise szolgáltatással

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Mozy Enterprise-t az Azure Active Directoryval (Azure AD).
A Mozy Enterprise integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Mozy Enterprise.You can control in Azure AD who has access to Mozy Enterprise.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve a Mozy Enterprise (Single Sign-On) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a Mozy Enterprise-szal a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* Mozy Enterprise egyszeri bejelentkezéses előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A Mozy Enterprise támogatja az **SP** által kezdeményezett SSO-t

## <a name="adding-mozy-enterprise-from-the-gallery"></a>Mozy Enterprise hozzáadása a galériából

A Mozy Enterprise azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Mozy Enterprise-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha a Mozy Enterprise-t a galériából szeretné hozzáadni, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Mozy Enterprise**kifejezést , válassza a **Mozy Enterprise** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Mozy Enterprise az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a Mozy Enterprise-szal egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, egy Azure AD-felhasználó és a kapcsolódó felhasználó a Mozy Enterprise közötti kapcsolat létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a Mozy Enterprise szolgáltatással a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a Mozy Enterprise Single Sign-On --t](#configure-mozy-enterprise-single-sign-on)** az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre Mozy Enterprise tesztfelhasználó](#create-mozy-enterprise-test-user)** - egy megfelelője Britta Simon a Mozy Enterprise, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához a Mozy Enterprise-szal hajtsa végre az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **Mozy Enterprise** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![Mozy Enterprise Domain és URL-címek egyszeri bejelentkezési információk](common/sp-signonurl.png)

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<tenantname>.Mozyenterprise.com`

    > [!NOTE]
    > Az érték nem valós. Frissítse az értéket a tényleges bejelentkezési URL-címmel. Lépjen kapcsolatba [a Mozy Enterprise ügyféltámogatási csapatával](https://support.mozy.com/) az érték lefelvételéhez. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **Mozy Enterprise beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-mozy-enterprise-single-sign-on"></a>A Mozy Enterprise egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Mozy Enterprise vállalati webhelyére rendszergazdaként.

2. A **Konfiguráció csoportban** kattintson a **Hitelesítési házirend gombra.**
   
    ![Hitelesítési házirend](./media/mozy-enterprise-tutorial/ic777314.png "Hitelesítési házirend")

3. A **Hitelesítési házirend** szakaszban hajtsa végre az alábbi lépéseket:
   
    ![Hitelesítési házirend](./media/mozy-enterprise-tutorial/ic777315.png "Hitelesítési házirend")
   
    a. Válassza a **Címtárszolgáltatás** **szolgáltatóként**lehetőséget.
   
    b. Válassza **az LDAP-leküldéses használata**lehetőséget.
   
    c. Kattintson az **SAML-hitelesítés** fülre.
   
    d. **Illessze be a bejelentkezési URL-címet,** amelyet az Azure Portalról másolt a **hitelesítési URL-cím** mezőbe.
   
    e. Illessze be az **Azure AD-azonosítót,** amelyet az Azure Portalról másolt az **SAML-végpont** szövegdobozába.
   
    f. Nyissa meg a letöltött base-64 kódolású tanúsítványt a jegyzettömbben, másolja annak tartalmát a vágólapra, majd illessze be a teljes tanúsítványt az **SAML tanúsítvány** szövegmezőbe.
   
    g. Válassza **az SSO engedélyezése rendszergazdáknak lehetőséget a hálózati hitelesítő adatokkal való bejelentkezéshez.**
   
    h. Kattintson a **Save Changes** (Módosítások mentése) gombra.

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

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés a Mozy Enterprise hozzáférést biztosít.

1. Az Azure Portalon válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza a **Mozy Enterprise lehetőséget.**

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Mozy Enterprise**lehetőséget.

    ![A Mozy Enterprise hivatkozás az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-mozy-enterprise-test-user"></a>Mozy Enterprise tesztfelhasználó létrehozása

Annak érdekében, hogy az Azure AD-felhasználók bejelentkezhessenek a Mozy Enterprise-ba, ki kell építeni őket a Mozy Enterprise-ba. A Mozy Enterprise esetében a kiépítés manuális feladat.

>[!NOTE]
>Az Azure AD felhasználói fiókok kiépítéséhez a Mozy Enterprise felhasználói fiók létrehozásához szükséges bármely más, a Mozy Enterprise által biztosított felhasználói fiók-létrehozási eszköz használatával is használható.

**Felhasználói fiókok létesítéséhez hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **Mozy Enterprise bérlőjéhez.**

2. Kattintson **a Felhasználók**, majd **az Új felhasználó hozzáadása parancsra.**
   
    ![Felhasználók](./media/mozy-enterprise-tutorial/ic777317.png "Felhasználók")
   
    >[!NOTE]
    >Az **Új felhasználó hozzáadása** beállítás csak akkor jelenik meg, ha a **Hitelesítési házirend**ben a **Mozy** van bejelölve szolgáltatóként. Ha az SAML-hitelesítés konfigurálva van, akkor a felhasználók automatikusan hozzáadódnak az első bejelentkezéskor az egyszeri bejelentkezésen keresztül.
    
3. Az új felhasználói párbeszédpanelen hajtsa végre az alábbi lépéseket:
   
    ![Felhasználók hozzáadása](./media/mozy-enterprise-tutorial/ic777318.png "Felhasználók hozzáadása")
   
    a. A **Csoport kiválasztása** listában jelöljön ki egy csoportot.
   
    b. A **Milyen típusú felhasználói** listában válasszon ki egy típust.
   
    c. A **Felhasználónév** mezőbe írja be az Azure AD-felhasználó nevét.
   
    d. Az **E-mail** szövegmezőbe írja be az Azure AD-felhasználó e-mail címét.
   
    e. Válassza **a Felhasználói utasítás e-mail küldése**lehetőséget.
   
    f. Kattintson **a Felhasználó(k) hozzáadása gombra.**

     >[!NOTE]
     > A felhasználó létrehozása után egy e-mailt küld az Azure AD-felhasználó, amely tartalmaz egy hivatkozást, hogy erősítse meg a fiókot, mielőtt aktívvá válik.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a Mozy Enterprise csempére kattint, automatikusan be kell jelentkeznie a Mozy Enterprise-ba, amelyhez beállította az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


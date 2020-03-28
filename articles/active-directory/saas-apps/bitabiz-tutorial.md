---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a BitaBIZ-vel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a BitaBIZ között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1a51e677-c62b-4aee-9c61-56926aaaa899
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f827945cbeccacfdf048865b6e89b6947fe7de9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73159387"
---
# <a name="tutorial-azure-active-directory-integration-with-bitabiz"></a>Oktatóanyag: Az Azure Active Directory integrációja a BitaBIZ-val

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a BitaBIZ-t az Azure Active Directoryval (Azure AD).
A BitaBIZ integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a BitaBIZ-hez.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve legyenek a BitaBIZ -ba (Single Sign-On) az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a BitaBIZ-val a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* BitaBIZ egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A BitaBIZ támogatja az **SP és az IDP** által kezdeményezett SSO-t

## <a name="adding-bitabiz-from-the-gallery"></a>BitaBIZ hozzáadása a galériából

A BitaBIZ azure AD-be való integrálásának konfigurálásához hozzá kell adnia a BitaBIZ-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha a BitaBIZ-t a galériából szeretné hozzáadni, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **BitaBIZ**parancsot, válassza a **BitaBIZ** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![BitaBIZ az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a BitaBIZ-lal egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, az Azure AD-felhasználó és a kapcsolódó felhasználó a BitaBIZ-ben közötti kapcsolat létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a BitaBIZ-val a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a BitaBIZ Single Sign-On --t](#configure-bitabiz-single-sign-on)** az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre BitaBIZ teszt felhasználó](#create-bitabiz-test-user)** - egy megfelelője Britta Simon a BitaBIZ, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **BitaBIZ** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban Ha az alkalmazást **IDP** által kezdeményezett módban kívánja konfigurálni, hajtsa végre a következő lépést:

    ![BitaBIZ tartomány és URL-címek egyszeri bejelentkezési információi](common/idp-identifier.png)

    Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`https://www.bitabiz.com/<instanceId>`

    > [!NOTE]
    > A fenti URL-cím értéke csak bemutatóra szolgál. Frissítse az értéket a tényleges azonosítóval, amelyet az oktatóanyag későbbi részében ismertetünk.

5. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    ![image](common/both-preintegrated-signon.png)

    A **Bejelentkezési URL-cím** mezőbe írja be az URL-címet:`https://www.bitabiz.com/dashboard`

6. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

7. A **BitaBIZ beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure-hirdetésazonosító

    c. Kijelentkezés URL-címe

### <a name="configure-bitabiz-single-sign-on"></a>A BitaBIZ egyszeri bejelentkezéskonfigurálása

1. Egy másik böngészőablakban jelentkezzen be a BitaBIZ-bérlőhöz rendszergazdaként.

2. Kattintson a **SETUP ADMIN gombra.**

    ![BitaBIZ konfigurációja](./media/bitabiz-tutorial/settings1.png)

3. Kattintson a **Microsoft-integrációk elemre az** **Érték hozzáadása** csoportban.

    ![BitaBIZ konfigurációja](./media/bitabiz-tutorial/settings2.png)

4. Görgessen le a **Microsoft Azure AD (Egyszeri bejelentkezés engedélyezése)** szakaszhoz, és hajtsa végre a következő lépéseket:

    ![BitaBIZ konfigurációja](./media/bitabiz-tutorial/settings3.png)

    a. Másolja az értéket az **entitásazonosítóból ("azonosító" az Azure AD-ben)** szövegdobozba, és illessze be az Azure Portal **Alapszintű SAML-konfigurációs** **szakaszazonosító** szövegmezőjébe. 

    b. Az **Azure AD egyszeri bejelentkezési szolgáltatás URL-címének** szövege, illessze **be a bejelentkezési URL-címet,** amelyet az Azure Portalról másolt.

    c. Az **Azure AD SAML entitásazonosító** szövegdobozába illessze be az **Azure Hirdetési azonosítót,** amelyet az Azure Portalról másolt.

    d. Nyissa meg a letöltött **tanúsítvány(Base64)** fájlt a jegyzettömbben, másolja a tartalmát a vágólapra, majd illessze be az **Azure AD aláíró tanúsítvány (Base64 kódolású)** szövegdobozba.

    e. Adja hozzá az üzleti e-mail tartománynevét, amely mycompany.com a **Domain name** szövegmezőben, hogy az egyszeri bejelentkezést a vállalat ezen e-mail tartományával rendelkező felhasználókhoz rendelje (NEM KÖTELEZŐ).

    f. Mark **SSO engedélyezte** a BitaBIZ fiókot.

    g. Kattintson **az Azure AD-konfiguráció mentése** és aktiválása az SSO konfiguráció.

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

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés használatával hozzáférést bitabiz hozzáférést biztosít.

1. Az Azure portalon válassza az **Enterprise Applications**lehetőséget, válassza a **Minden alkalmazás**lehetőséget, majd a **BitaBIZ**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **BitaBIZ**lehetőséget.

    ![A BitaBIZ hivatkozás az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-bitabiz-test-user"></a>BitaBIZ tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezhessenek a BitaBIZ-be, ki kell építeni őket a BitaBIZ-be.  
A BitaBIZ esetében a kiépítés manuális feladat.

**Felhasználói fiók kiépítéséhez hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a BitaBIZ vállalati webhelyére rendszergazdaként.

2. Kattintson a **SETUP ADMIN gombra.**

    ![BitaBIZ Felhasználó hozzáadása](./media/bitabiz-tutorial/settings1.png)

3. Kattintson a **Felhasználók hozzáadása** **csoportban, a Szervezet** szakaszra.

    ![BitaBIZ Felhasználó hozzáadása](./media/bitabiz-tutorial/user1.png)

4. Kattintson **az Új alkalmazott hozzáadása gombra.**

    ![BitaBIZ Felhasználó hozzáadása](./media/bitabiz-tutorial/user2.png)

5. Az **Új alkalmazott hozzáadása** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![BitaBIZ Felhasználó hozzáadása](./media/bitabiz-tutorial/user3.png)

    a. Az **Utónév** mezőbe írja be a felhasználó keresztnevét, például Britta.

    b. A **Vezetéknév** mezőbe írja be a felhasználó vezetéknevét, például Simon.

    c. Az **E-mail** mezőbe írja be a Brittasimon@contoso.comfelhasználó e-mail címét, például .

    d. Válasszon egy dátumot **a Munkaalkalmazás dátuma területen.**

    e. Vannak más nem kötelező felhasználói attribútumok is, amelyek beállíthatók a felhasználó számára. További részletekért olvassa el [az alkalmazott beállítási bizonylatát.](https://help.bitabiz.dk/manage-or-set-up-your-account/on-boarding-employees/new-employee)

    f. Kattintson **az Alkalmazott mentése gombra.**

    > [!NOTE]
    > Az Azure Active Directory-fiók tulajdonosa kap egy e-mailt, és egy hivatkozást követ, hogy erősítse meg a fiók, mielőtt aktívvá válik.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelEn a BitaBIZ csempére kattint, automatikusan be kell jelentkeznie arra a BitaBIZ-be, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

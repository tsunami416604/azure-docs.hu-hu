---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az OnTrack-szel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az OnTrack között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d2cafba2-3b4a-4471-ba34-80f6a96ff2b9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/13/2019
ms.author: jeedes
ms.openlocfilehash: 371ad2a8530de7b20427327d939bf164d871ab74
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "68944040"
---
# <a name="tutorial-azure-active-directory-integration-with-ontrack"></a>Oktatóanyag: Az Azure Active Directory integrációja az OnTrack-szel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az OnTrack-et az Azure Active Directoryval (Azure AD).
Az OnTrack és az Azure AD integrálása a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá az OnTrack-hez.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve legyenek az OnTrack (Single Sign-On) szolgáltatásba az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához az OnTrack-szel a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* OnTrack egyszeri bejelentkezéssel engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* OnTrack támogatja **IDP** által kezdeményezett SSO

## <a name="adding-ontrack-from-the-gallery"></a>Az OnTrack hozzáadása a galériából

Az OnTrack Azure AD-be való integrációjának konfigurálásához hozzá kell adnia az OnTrack-et a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha az OnTrack-et a galériából szeretné hozzáadni, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **OnTrack**kifejezést, válassza **az OnTrack** elemet az eredménypanelről, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![OnTrack az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését az OnTrack-szel egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, az Azure AD-felhasználó és a kapcsolódó felhasználó az OnTrack közötti kapcsolat létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez az OnTrack-szel a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja az OnTrack Single Sign-On --t](#configure-ontrack-single-sign-on)** az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre OnTrack teszt felhasználó](#create-ontrack-test-user)** - egy megfelelője Britta Simon az OnTrack, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)az **OnTrack** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon hajtsa végre az alábbi lépéseket:

    ![OnTrack tartomány és URL egyszeri bejelentkezési információk](common/idp-intiated.png)

    a. Az **Azonosító** mezőben:

    A tesztelési környezetben írja be az URL-címet:`https://staging.insigniagroup.com/sso`

    Az éles környezetben írja be az URL-címet:`https://oeaccessories.com/sso`

    b. A **Válasz URL-címe** mezőbe:

    A tesztelési környezetben írja be az URL-címet:`https://indie.staging.insigniagroup.com/sso/autonation.aspx`

    Az éles környezetben írja be az URL-címet:`https://igaccessories.com/sso/autonation.aspx`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító és válasz URL-címével. Lépjen kapcsolatba [az OnTrack ügyféltámogatási csapatával](mailto:CustomerService@insigniagroup.com) az értékek lefelvételéhez. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

5. Az OnTrack alkalmazás az SAML-állításokat egy adott formátumban várja, amely megköveteli, hogy egyéni attribútumleképezéseket adjon hozzá az SAML token attribútumkonfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Kattintson a **Szerkesztés** ikonra a **Felhasználói attribútumok** párbeszédpanel megnyitásához.

    ![image](common/edit-attribute.png)

6. A fentieken kívül az OnTrack alkalmazás azt várja, hogy néhány további attribútum ot kell visszaadni az SAML-válaszban. A **Felhasználói attribútumok** párbeszédpanel **Felhasználói jogcímek** szakaszában hajtsa végre az alábbi lépéseket az SAML token attribútum hozzáadásához az alábbi táblázatban látható módon:

    | Név | Forrás attribútuma|
    | -------------- | ----------------|    
    | Felhasználói szerepkör      | "42F432" |
    | Hyperion-kód  | "12345" |

    > [!NOTE]
    > **A felhasználói szerepkör** és **a hyperion-kód** attribútumok az Autonation felhasználói szerepkörrel, illetve a kereskedői kóddal vannak leképezve. Ezek az értékek csak például, kérjük, használja a megfelelő kódot az integrációhoz. Ezekhez az értékekhez forduljon az [Autonation támogatási szolgálatához.](mailto:CustomerService@insigniagroup.com)

    a. Kattintson **az Új jogcím hozzáadása** gombra a Felhasználói **jogcímek kezelése** párbeszédpanel megnyitásához.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. A **Név** mezőbe írja be a sor attribútumnevét.

    c. Hagyja üresen a **névteret.**

    d. Válassza a Forrás **attribútumként lehetőséget.**

    e. A **Forrás attribútumlistában** írja be a sor attribútumértékét.

    f. Kattintson **az Ok gombra**

    g. Kattintson a **Mentés** gombra.

7. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány csoportjában** kattintson a **Letöltés** gombra, ha letöltheti az **összevonási metaadat-XML-t** a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

8. Az **OnTrack beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-ontrack-single-sign-on"></a>Egyszeri bejelentkezés konfigurálása

Az egyszeri bejelentkezés konfigurálásához az **OnTrack** oldalon el kell küldenie a letöltött **összevonási metaadat-XML-t** és a megfelelő másolt URL-címeket az Azure Portalról az [OnTrack támogatási csapatának.](mailto:CustomerService@insigniagroup.com) Úgy állították be ezt a beállítást, hogy az SAML SSO-kapcsolat mindkét oldalon megfelelően legyen beállítva.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása 

Ez a szakasz célja, hogy hozzon létre egy tesztfelhasználót az Azure Portalon Britta Simon.

1. Az Azure Portalon a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd az **Összes felhasználó**lehetőséget.

    ![A "Felhasználók és csoportok" és a "Minden felhasználó" linkek](common/users.png)

2. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A Felhasználó tulajdonságokban hajtsa végre a következő lépéseket.

    ![A Felhasználó párbeszédpanel](common/user-properties.png)

    a. A **Név** mezőbe írja be **a BrittaSimon**értéket.
  
    b. A **Felhasználónév** mezőtípusban**brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket.

    d. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés használatával az OnTrack hozzáférést biztosít.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza **az OnTrack**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza az **OnTrack**lehetőséget.

    ![Az Alkalmazások listában található OnTrack hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-ontrack-test-user"></a>OnTrack tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre az OnTrack alkalmazásban. Együttműködve [OnTrack támogatási csapatával](mailto:CustomerService@insigniagroup.com) a felhasználók hozzáadása az OnTrack platformon. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelOntrack csempéjére kattint, automatikusan be kell jelentkeznie arra az OnTrack-be, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


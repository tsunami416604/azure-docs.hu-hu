---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Citrix ShareFile fájllal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Citrix ShareFile között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: e14fc310-bac4-4f09-99ef-87e5c77288b6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71d2fe97a4af07f48038ffd88de648247b56bb80
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160029"
---
# <a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>Oktatóanyag: Az Azure Active Directory integrációja a Citrix ShareFile fájllal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Citrix ShareFile alkalmazást az Azure Active Directoryval (Azure AD).
A Citrix ShareFile integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a Citrix ShareFile fájlhoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve legyenek a Citrix ShareFile (Single Sign-On) alkalmazásba az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a Citrix ShareFile fájllal a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* Citrix ShareFile egyszeri bejelentkezéses előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A Citrix ShareFile támogatja az **SP** által kezdeményezett SSO-t

## <a name="adding-citrix-sharefile-from-the-gallery"></a>Citrix ShareFile hozzáadása a gyűjteményből

A Citrix ShareFile azure AD-be való integrációjának konfigurálásához hozzá kell adnia a Citrix ShareFile-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha a Citrix ShareFile fájlt a gyűjteményből szeretné hozzáadni, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Citrix ShareFile ( Citrix ShareFile**) parancsot, válassza a **Citrix ShareFile** elemet az eredménypanelről, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Citrix ShareFile az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a Citrix ShareFile-val egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés működéséhez létre kell hozni egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó a Citrix ShareFile-ban létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a Citrix ShareFile fájllal a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a Citrix ShareFile Single Sign-On --t](#configure-citrix-sharefile-single-sign-on)** az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre Citrix ShareFile teszt felhasználó](#create-citrix-sharefile-test-user)** – egy megfelelője Britta Simon a Citrix ShareFile, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához a Citrix ShareFile fájllal hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **Citrix ShareFile** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![Citrix ShareFile tartomány és URL-címek egyszeri bejelentkezési információi](common/sp-identifier-reply.png)

    a. A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<tenant-name>.sharefile.com/saml/login`

    b. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő minta használatával:

    | |
    |---|
    | `https://<tenant-name>.sharefile.com`|
    | `https://<tenant-name>.sharefile.com/saml/info`|
    | `https://<tenant-name>.sharefile1.com/saml/info`|
    | `https://<tenant-name>.sharefile1.eu/saml/info`|
    | `https://<tenant-name>.sharefile.eu/saml/info`|
    | |
    
    c. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:
    
    | |
    |---|
    | `https://<tenant-name>.sharefile.com/saml/acs`|
    | `https://<tenant-name>.sharefile.eu/saml/<URL path>`|
    | `https://<tenant-name>.sharefile.com/saml/<URL path>`|

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel, azonosítóval és válasz URL-címmel. Lépjen kapcsolatba [a Citrix ShareFile ügyfél támogatási csapatával,](https://www.citrix.co.in/products/citrix-content-collaboration/support.html) hogy levegye ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

4. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **Citrix ShareFile beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure-hirdetésazonosító

    c. Kijelentkezés URL-címe

### <a name="configure-citrix-sharefile-single-sign-on"></a>A Citrix ShareFile egyszeri bejelentkezéskonfigurálása

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként a **Citrix ShareFile** vállalati webhelyére.

2. A felső eszköztáron kattintson a **Rendszergazda gombra.**

3. A bal oldali navigációs ablakban válassza az **Egyszeri bejelentkezés konfigurálása**lehetőséget.
   
    ![Fiókfelügyelet](./media/sharefile-tutorial/ic773627.png "Fiókfelügyelet")

4. Az **Egyszeri bejelentkezés/ SAML 2.0 konfigurációs** párbeszédpanel **Alapbeállítások**területén hajtsa végre az alábbi lépéseket:
   
    ![Egyszeri bejelentkezés](./media/sharefile-tutorial/ic773628.png "Egyszeri bejelentkezés")
   
    a. Kattintson **az SAML engedélyezése gombra.**
    
    b. Az **IDP-kiállító/entitásazonosító** szövegdobozába illessze be az **Azure-hirdetési azonosító** értékét, amelyet az Azure Portalról másolt.

    c. Kattintson a **Módosítás** gombra az **X.509 tanúsítvány** mező mellett, majd töltse fel az Azure Portalról letöltött tanúsítványt.
    
    d. A **Bejelentkezési URL-cím** szövegmezőbe illessze be a **bejelentkezési URL-cím** értékét, amelyet az Azure Portalról másolt.
    
    e. A **Kijelentkezés URL-cím** szövegmezőjébe illessze be a **Kijelentkezés URL-címének** az Azure Portalról másolt értékét.

5. Kattintson a **Mentés** gombra a Citrix ShareFile felügyeleti portálon.

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

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés t a Citrix ShareFile hozzáférést biztosítva.

1. Az Azure portalon válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza a **Citrix ShareFile lehetőséget.**

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában írja be és válassza a **Citrix ShareFile lehetőséget.**

    ![A Citrix ShareFile hivatkozás a Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-citrix-sharefile-test-user"></a>Citrix ShareFile tesztfelhasználó létrehozása

Annak érdekében, hogy az Azure AD-felhasználók bejelentkezhessenek a Citrix ShareFile fájlba, ki kell építeni őket a Citrix ShareFile fájlba. A Citrix ShareFile esetében a kiépítés manuális feladat.

**Felhasználói fiók kiépítéséhez hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **Citrix** ShareFile-bérlőbe.

2. Kattintson **a \> Felhasználók \> kezelése kezdőlap + Alkalmazott létrehozása parancsra.**
   
    ![Alkalmazott létrehozása](./media/sharefile-tutorial/IC781050.png "Alkalmazott létrehozása")

3. Az **Alapinformáció** szakaszban hajtsa végre az alábbi lépéseket:
   
    ![Alapvető információk](./media/sharefile-tutorial/IC799951.png "Alapvető információk")
   
    a. Az **E-mail cím** mezőbe írja be Britta Simon e-mail címét **brittasimon\@contoso.com**.
   
    b. Az **Utónév** mezőbe írja be a felhasználó keresztnevét **Britta**( **keresztnév)** néven.
   
    c. A **Vezetéknév** mezőbe írja be a felhasználó **vezetéknevét** Simon ( **Utónév**) mezőbe.

4. Kattintson az **Add User** (Felhasználó hozzáadása) elemre.
  
    >[!NOTE]
    >Az Azure AD-fiók tulajdonosa kap egy e-mailt, és kövesse a hivatkozást, hogy erősítse meg a fiók, mielőtt aktívvá válik. A Citrix ShareFile felhasználói fiók létrehozásához használt bármely más, a Citrix ShareFile által biztosított API-k at használhat az Azure AD felhasználói fiókok kiépítéséhez.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a Citrix ShareFile csempére kattint, automatikusan be kell jelentkeznie arra a Citrix-megosztási fájlba, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


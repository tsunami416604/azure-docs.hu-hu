---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a SmarterU-val | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a SmarterU között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 95fe3212-d052-4ac8-87eb-ac5305227e85
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: jeedes
ms.openlocfilehash: 712e7bcf513592f97950902faff2f7754093b9fc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76549291"
---
> [!NOTE]
> A SmarterU és az Azure Active Directory integrálásának folyamatát a [SmarterU súgórendszer](https://help.smarteru.com/ID2053086)is dokumentálja és karbantartja.

# <a name="tutorial-azure-active-directory-integration-with-smarteru"></a>Oktatóanyag: Az Azure Active Directory integrációja a SmarterU szolgáltatással

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a SmarterU-t az Azure Active Directoryval (Azure AD).
A SmarterU integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a SmarterU-hoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve smarteru (single sign-on) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció és a SmarterU konfigurálásához a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* SmarterU egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A SmarterU támogatja az **IDP** által kezdeményezett sso-t

## <a name="adding-smarteru-from-the-gallery"></a>A SmarterU hozzáadása a galériából

A SmarterU azure AD-be való integrálásának konfigurálásához hozzá kell adnia a SmarterU-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha a"SmarterU-t a galériából szeretné hozzáadni" a következő lépéseket hajtsa végre:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **SmarterU**kifejezést, válassza a **SmarterU** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![SmarterU az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a SmarterU-val egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, az Azure AD-felhasználó és a kapcsolódó felhasználó a SmarterU kapcsolatát kell létrehozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a SmarterU segítségével a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja smarteru single sign-on](#configure-smarteru-single-sign-on)** - konfigurálni az egyszeri bejelentkezési beállításokat az alkalmazás oldalán.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre SmarterU tesztfelhasználót](#create-smarteru-test-user)** – ha britta Simon megfelelője van a SmarterU-ban, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához hajtsa végre az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **SmarterU** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![SmarterU domain és URL-ek egyszeri bejelentkezési információk](common/idp-identifier.png)

    Az **Azonosító** mezőbe írja be az URL-címet:`https://www.smarteru.com/`

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány csoportjában** kattintson a **Letöltés** gombra, ha letöltheti az **összevonási metaadat-XML-t** a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. A **SmarterU beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-smarteru-single-sign-on"></a>Smarteru egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a SmarterU vállalati webhelyére rendszergazdaként.

1. A felső eszköztáron kattintson a **Fiókbeállítások gombra.**

    ![Fiókbeállítások](./media/smarteru-tutorial/accountsettings.png)

1. A fiók konfigurációja lapon hajtsa végre a következő lépéseket:

    ![Külső engedélyezés](./media/smarteru-tutorial/externalauthorizationconfiguration.png) 

    a. Válassza **a Külső engedélyezés engedélyezése**lehetőséget.
  
    b. A **Fő bejelentkezésvezérlés csoportban** válassza a **SmarterU** lapot.
  
    c. A **Felhasználó alapértelmezett bejelentkezése csoportban** válassza a **SmarterU** lapot.
  
    d. Válassza **az SAML engedélyezése**lehetőséget.
  
    e. Másolja a letöltött metaadatfájl tartalmát, majd illessze be az **IdP metaadat-szövegmezőbe.**

    f. Jelöljön ki egy **azonosító attribútumot/jogcímcímet.**
  
    g. Kattintson a **Mentés** gombra.

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

Ebben a szakaszban engedélyezheti Britta Simon számára az Azure egyszeri bejelentkezést a SmarterU-hoz való hozzáférés biztosításával.

1. Az Azure Portalon válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza a **SmarterU**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **SmarterU**lehetőséget.

    ![A SmarterU hivatkozás az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-smarteru-test-user"></a>SmarterU tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezhessenek a SmarterU-ba, ki kell építeni őket a SmarterU-ba. A SmarterU esetében a kiépítés manuális feladat.

**Felhasználói fiók kiépítéséhez hajtsa végre az alábbi lépéseket:**

1. jelentkezzen be **smarteru bérlőjéhez.**

1. Nyissa meg a **Felhasználók lehetőséget.**

1. A felhasználó szakaszban hajtsa végre az alábbi lépéseket:

    ![Új felhasználó](./media/smarteru-tutorial/adduser.png)  

    a. Kattintson **a +Felhasználó gombra.**

    b. Írja be az Azure AD felhasználói fiók kapcsolódó attribútumértékeit a következő szövegmezőkbe: **Elsődleges e-mail**, **alkalmazottazonosító**, **jelszó**, **jelszó ellenőrzése**, **utónév**, **vezetéknév**.

    c. Kattintson **az Aktív gombra.**

    d. Kattintson a **Mentés** gombra.

> [!NOTE]
> A SmarterU felhasználói fiók létrehozásához használt bármely más, a SmarterU által biztosított API-k at azure AD felhasználói fiókok kiépítéséhez használhatja.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a SmarterU csempére kattint, automatikusan be kell jelentkeznie a SmarterU-ba, amelyhez beállította az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

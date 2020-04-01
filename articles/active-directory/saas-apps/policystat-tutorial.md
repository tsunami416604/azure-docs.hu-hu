---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a PolicyStat programmal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a PolicyStat között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: af5eb0f1-1c8e-4809-b0c4-8ccfb915ca42
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 327e470d60235e6bf400293e80e3aec5f6144ff4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "68943446"
---
# <a name="tutorial-azure-active-directory-integration-with-policystat"></a>Oktatóanyag: Az Azure Active Directory integrációja a PolicyStat programmal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a PolicyStatot az Azure Active Directoryval (Azure AD).
A PolicyStat integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Szabályozhatja az Azure AD-ben, aki rendelkezik hozzáféréssel a PolicyStat.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve policystat (single sign-on) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a PolicyStat,a következő elemekre van szüksége:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* PolicyStat egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A PolicyStat támogatja az **SP** által kezdeményezett SSO-t

* A PolicyStat támogatja **a Just In Time** felhasználói kiépítést

## <a name="adding-policystat-from-the-gallery"></a>PolicyStat hozzáadása a galériából

A PolicyStat azure AD-be való integrálásának konfigurálásához hozzá kell adnia a PolicyStat-ot a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha a PolicyStat-ot a katalógusból szeretné hozzáadni, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **PolicyStat**parancsot , válassza a **PolicyStat** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![PolicyStat az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a PolicyStat-mal egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés működéséhez létre kell hozni egy kapcsolat közötti kapcsolat egy Azure AD-felhasználó és a kapcsolódó felhasználó PolicyStat létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a PolicyStat segítségével a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a PolicyStat Single Sign-On --t](#configure-policystat-single-sign-on)** az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre PolicyStat teszt felhasználó](#create-policystat-test-user)** - egy megfelelője Britta Simon a PolicyStat, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **PolicyStat** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![PolicyStat tartomány és URL-címek egyszeri bejelentkezési információk](common/sp-identifier.png)

    a. A Bejelentkezés az **URL-cím** mezőbe írja be az URL-címet a következő minta használatával:`https://<companyname>.policystat.com`

    b. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<companyname>.policystat.com/saml2/metadata/`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Bejelentkezési URL-címmel és azonosítóval. Lépjen kapcsolatba [a PolicyStat ügyféltámogatási csapatával,](http://www.policystat.com/support/) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

4. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány csoportjában** kattintson a **Letöltés** gombra, ha letöltheti az **összevonási metaadat-XML-t** a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

5. A PolicyStat alkalmazás elvárja az SAML-állításokat egy adott formátumban, amely megköveteli, hogy egyéni attribútum-hozzárendelések hozzáadása az SAML token attribútumok konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Kattintson a **Szerkesztés** ikonra a **Felhasználói attribútumok** párbeszédpanel megnyitásához.

    ![image](common/edit-attribute.png)

6. A fentieken kívül a PolicyStat alkalmazás azt várja, hogy néhány további attribútum ot kell visszaadni az SAML-válaszban. A **Felhasználói attribútumok** párbeszédpanel **Felhasználói jogcímek** szakaszában hajtsa végre az alábbi lépéseket az SAML token attribútum hozzáadásához az alábbi táblázatban látható módon:

    | Név | Forrás attribútuma |
    |------------------- | -------------------- |
    | Uid | ExtractMailPrefix([mail]) |

    a. Kattintson **az Új jogcím hozzáadása** gombra a Felhasználói **jogcímek kezelése** párbeszédpanel megnyitásához.
    
    ![image](common/new-save-attribute.png)

    ![image](./media/policystat-tutorial/attribute01.png)

    b. A **Név** mezőbe írja be a sor attribútumnevét.

    c. Hagyja üresen a **névteret.**

    d. Válassza a Forrás **átalakítása**lehetőséget.

    e. Az **Átalakítás** listából írja be a sor attribútumértékét.
    
    f. Az **1.**

    g. Kattintson a **Mentés** gombra.

7. A **PolicyStat beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-policystat-single-sign-on"></a>Házirendstat egyszeri bejelentkezésének konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a PolicyStat vállalati webhelyére rendszergazdaként.

2. Kattintson a **Rendszergazda** fülre, majd a bal oldali navigációs ablak **egyszeri bejelentkezési konfigurációja** parancsra.
   
    ![Rendszergazda menü](./media/policystat-tutorial/ic808633.png "Rendszergazda menü")

3. A **Telepítés** csoportban válassza az **Egyszeri bejelentkezés engedélyezése lehetőséget.**
   
    ![Egyszeri bejelentkezés konfigurációja](./media/policystat-tutorial/ic808634.png "Egyszeri bejelentkezés konfigurációja")

4. Kattintson az **Attribútumok konfigurálása gombra,** majd az **Attribútumok konfigurálása** csoportban hajtsa végre az alábbi lépéseket:
   
    ![Egyszeri bejelentkezés konfigurációja](./media/policystat-tutorial/ic808635.png "Egyszeri bejelentkezés konfigurációja")
   
    a. A **Felhasználónév attribútum** szövegmezőbe írja be az **uid**nevet.

    b. Az **Utónév attribútum** szövegmezőbe írja be **a Britta**felhasználó **keresztnevét.**

    c. A **Vezetéknév attribútum** szövegmezőbe írja be **Simon**felhasználó **vezetéknevét.**

    d. Az **E-mail attribútum** mezőbe írja be `BrittaSimon@contoso.com`a felhasználó **e-mail címét.**

    e. Kattintson a **Save Changes** (Módosítások mentése) gombra.

5. Kattintson **az IDP metaadatai elemre,** majd az **IDP metaadatok** szakaszában hajtsa végre az alábbi lépéseket:
   
    ![Egyszeri bejelentkezés konfigurációja](./media/policystat-tutorial/ic808636.png "Egyszeri bejelentkezés konfigurációja")
   
    a. Nyissa meg a letöltött metaadatfájlt, másolja a tartalmat, majd illessze be az **Identitásszolgáltató metaadatok** szövegmezőbe.

    b. Kattintson a **Save Changes** (Módosítások mentése) gombra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása 

Ez a szakasz célja, hogy hozzon létre egy tesztfelhasználót az Azure Portalon Britta Simon.

1. Az Azure Portalon a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd az **Összes felhasználó**lehetőséget.

    ![A "Felhasználók és csoportok" és a "Minden felhasználó" linkek](common/users.png)

2. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A Felhasználó tulajdonságokban hajtsa végre a következő lépéseket.

    ![A Felhasználó párbeszédpanel](common/user-properties.png)

    a. A **Név** mezőbe írja be **a BrittaSimon**értéket.
  
    b. A **Felhasználónév** mező brittasimon@yourcompanydomain.extensiontípusa mezőben. Például: BrittaSimon@contoso.com

    c. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket.

    d. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés használatával a PolicyStat hozzáférést biztosít.

1. Az Azure portalon válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza a **PolicyStat**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **PolicyStat**lehetőséget.

    ![A PolicyStat hivatkozás az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-policystat-test-user"></a>PolicyStat tesztfelhasználó létrehozása

In this section, a user called Britta Simon is created in PolicyStat. PolicyStat támogatja a just-in-time felhasználói kiépítés, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nincs műveletelem. Ha a felhasználó már nem létezik a PolicyStat, egy új jön létre a hitelesítés után.

>[!NOTE]
>A PolicyStat által biztosított bármely más PolicyStat felhasználói fiók-létrehozási eszköz vagy API-k használatával azure AD felhasználói fiókok kiépítése.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a PolicyStat csempére kattint, automatikusan be kell jelentkeznie arra a PolicyStat-ba, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


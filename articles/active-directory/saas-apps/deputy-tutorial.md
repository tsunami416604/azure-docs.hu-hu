---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a helyettessel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Helyettes között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5665c3ac-5689-4201-80fe-fcc677d4430d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/25/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd021b9547048e9f5218ce4f2b8b110b15d05963
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80048559"
---
# <a name="tutorial-azure-active-directory-integration-with-deputy"></a>Oktatóanyag: Az Azure Active Directory integrációja helyettessel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a helyettest az Azure Active Directoryval (Azure AD).
A helyettes integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a helyetteshez.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve a helyettes (Single Sign-On) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a helyettessel a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* Egyszeri bejelentkezéssel rendelkező helyettes előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* Helyettes támogatja **az SP-t** és az **IDP** kezdeményezte az SSO-t

## <a name="adding-deputy-from-the-gallery"></a>Helyettes hozzáadása a galériából

A helyettes azure AD-be való integrálásának konfigurálásához hozzá kell adnia a katalógusból a helyettest a felügyelt SaaS-alkalmazások listájához.

**Ha helyettest szeretne hozzáadni a galériából, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **Helyettes**, Válassza **az Eredmény** panelhelyettes elemét, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Helyettes az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a Helyettessel egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, az Azure AD-felhasználó és a kapcsolódó felhasználó helyettes közötti kapcsolat létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja helyettes single sign-on](#configure-deputy-single-sign-on)** -, hogy konfigurálja az egyszeri bejelentkezés beállításait az alkalmazás oldalán.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre helyettes teszt felhasználó](#create-deputy-test-user)** -, hogy egy megfelelője Britta Simon helyettes, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **Helyettes** alkalmazásintegrációlapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban Ha az alkalmazást **IDP** által kezdeményezett módban kívánja konfigurálni, hajtsa végre a következő lépéseket:

    ![Helyettes domain és URL-ek egyszeri bejelentkezési információk](common/idp-intiated.png)

    a. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:

    |  |
    | ----|
    | `https://<subdomain>.<region>.au.deputy.com` |
    | `https://<subdomain>.<region>.ent-au.deputy.com` |
    | `https://<subdomain>.<region>.na.deputy.com`|
    | `https://<subdomain>.<region>.ent-na.deputy.com`|
    | `https://<subdomain>.<region>.eu.deputy.com` |
    | `https://<subdomain>.<region>.ent-eu.deputy.com` |
    | `https://<subdomain>.<region>.as.deputy.com` |
    | `https://<subdomain>.<region>.ent-as.deputy.com` |
    | `https://<subdomain>.<region>.la.deputy.com` |
    | `https://<subdomain>.<region>.ent-la.deputy.com` |
    | `https://<subdomain>.<region>.af.deputy.com` |
    | `https://<subdomain>.<region>.ent-af.deputy.com` |
    | `https://<subdomain>.<region>.an.deputy.com` |
    | `https://<subdomain>.<region>.ent-an.deputy.com` |
    | `https://<subdomain>.<region>.deputy.com` |

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:
    
    | |
    |----|
    | `https://<subdomain>.<region>.au.deputy.com/exec/devapp/samlacs` |
    | `https://<subdomain>.<region>.ent-au.deputy.com/exec/devapp/samlacs` |
    | `https://<subdomain>.<region>.na.deputy.com/exec/devapp/samlacs` |
    | `https://<subdomain>.<region>.ent-na.deputy.com/exec/devapp/samlacs` |
    | `https://<subdomain>.<region>.eu.deputy.com/exec/devapp/samlacs` |
    | `https://<subdomain>.<region>.ent-eu.deputy.com/exec/devapp/samlacs` |
    | `https://<subdomain>.<region>.as.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.ent-as.deputy.com/exec/devapp/samlacs` |
    | `https://<subdomain>.<region>.la.deputy.com/exec/devapp/samlacs` |
    | `https://<subdomain>.<region>.ent-la.deputy.com/exec/devapp/samlacs` |
    | `https://<subdomain>.<region>.af.deputy.com/exec/devapp/samlacs` |
    | `https://<subdomain>.<region>.ent-af.deputy.com/exec/devapp/samlacs` |
    | `https://<subdomain>.<region>.an.deputy.com/exec/devapp/samlacs` |
    | `https://<subdomain>.<region>.ent-an.deputy.com/exec/devapp/samlacs` |
    | `https://<subdomain>.<region>.deputy.com/exec/devapp/samlacs` |

5. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    ![Helyettes domain és URL-ek egyszeri bejelentkezési információk](common/metadata-upload-additional-signon.png)

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<your-subdomain>.<region>.deputy.com`

    >[!NOTE]
    > Helyettes régió utótag nem kötelező, vagy meg kell használni az egyik ilyen: au | na | eu |la |af |an |ent-au |ent-na |ent-eu |ent-as | ent-la | ent-af | ent-an

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Lépjen kapcsolatba [az ügyfél-támogató csoport helyettesével,](https://www.deputy.com/call-centers-customer-support-scheduling-software) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

6. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

7. A **Helyettes beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure-hirdetésazonosító

    c. Kijelentkezés URL-címe

### <a name="configure-deputy-single-sign-on"></a>Egyszeri bejelentkezés helyettes ének konfigurálása

1. Keresse meg a`https://(your-subdomain).deputy.com/exec/config/system_config`következő URL-címet: . Nyissa meg a **Biztonsági beállítások lapot,** és kattintson **a Szerkesztés gombra.**
   
    ![Egyszeri bejelentkezés konfigurálása](./media/deputy-tutorial/tutorial_deputy_004.png)

2. Ezen a **Biztonsági beállítások** lapon hajtsa végre az alábbi lépéseket.

    ![Egyszeri bejelentkezés konfigurálása](./media/deputy-tutorial/tutorial_deputy_005.png)
    
    a. **Közösségi bejelentkezés**engedélyezése.
   
    b. Nyissa meg az Azure Portalról letöltött Base64-alapú tanúsítványt a jegyzettömbben, másolja annak tartalmát a vágólapra, majd illessze be az **OpenSSL tanúsítvány** szövegdobozába.
   
    c. Az SAML SSO URL-cím ének szövegmezőbe írja be a`https://<your subdomain>.deputy.com/exec/devapp/samlacs?dpLoginTo=<saml sso url>`
    
    d. Az SAML SSO URL-cím `<your subdomain>` mezőjében cserélje le az altartományra.
   
    e. Az SAML SSO URL-cím `<saml sso url>` szövegmezőjében cserélje le az Azure Portalról másolt **bejelentkezési URL-címet.**
   
    f. Kattintson **a Beállítások mentése gombra.**

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

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés használatával hozzáférést biztosít a helyettes.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, válassza a **Minden alkalmazás**lehetőséget, majd válassza az **Helyettes**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Helyettes**lehetőséget.

    ![A helyettes link az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-deputy-test-user"></a>Teszthelyettes felhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezhessenek a helyettes, ki kell építeni a helyettes. Helyettes esetén a kiépítés manuális feladat.

#### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Felhasználói fiók kiépítéséhez hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be a helyettes cég honlapján, mint egy rendszergazda.

2. A felső navigációs ablakban kattintson a **Kapcsolatok**gombra.
   
    ![People](./media/deputy-tutorial/tutorial_deputy_001.png "People")

3. Kattintson a **Személyek hozzáadása** gombra, majd a Személyek **hozzáadása gombra.**
   
    ![Személyek hozzáadása](./media/deputy-tutorial/tutorial_deputy_002.png "Személyek hozzáadása")

4. Hajtsa végre az alábbi lépéseket, és kattintson **a & gombra.**
   
    ![Új felhasználó](./media/deputy-tutorial/tutorial_deputy_003.png "Új felhasználó")

    a. A **Név** mezőbe írja be a felhasználó nevét, például **BrittaSimon**.
   
    b. Az **E-mail** szövegmezőbe írja be a kiépíteni kívánt Azure AD-fiók e-mail címét.
   
    c. A **Munka a szövegmezőben** írja be a vállalkozás nevét.
   
    d. Kattintson **& Gombra, & meghívót.**

5. Az Azure AD-fiók tulajdonosa kap egy e-mailt, és egy linket követ, hogy erősítse meg a fiók, mielőtt aktívvá válik. Használhatja bármely más helyettes felhasználói fiók létrehozása eszközök vagy API-k által biztosított Helyettes azure AD felhasználói fiókok kiépítése.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen az Helyettes csempére kattint, automatikusan be kell jelentkeznie arra a helyettesre, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Mimecast felügyeleti konzollal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Mimecast Felügyeleti konzol között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 81c50614-f49b-4bbc-97d5-3cf77154305f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9edadd6462052f82f92c05c1678f845ece856cfb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160654"
---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-admin-console"></a>Oktatóanyag: Az Azure Active Directory integrációja a Mimecast felügyeleti konzollal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Mimecast Felügyeleti konzolt az Azure Active Directoryval (Azure AD).
A Mimecast Felügyeleti konzol integrálása az Azure AD-vel a következő előnyökkel jár:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a Mimecast felügyeleti konzolhoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve a Mimecast Admin Console (Single Sign-On) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció mimecast felügyeleti konzollal való konfigurálásához a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* Mimecast Admin Console egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A Mimecast Admin Console támogatja az **SP** által kezdeményezett SSO-t

## <a name="adding-mimecast-admin-console-from-the-gallery"></a>Mimecast Admin konzol hozzáadása a galériából

A Mimecast Felügyeleti konzol Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Mimecast Felügyeleti konzolt a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha a Mimecast Admin konzolt a gyűjteményből szeretné hozzáadni, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Mimecast Felügyeleti konzol**kifejezést, válassza a **Mimecast Felügyeleti konzol** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Mimecast Admin Konzol az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a Mimecast Felügyeleti konzollal egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés működéséhez létre kell hozni egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó a Mimecast Felügyeleti konzolon létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a Mimecast Felügyeleti konzollal a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a Mimecast Admin Console Single Sign-On --t](#configure-mimecast-admin-console-single-sign-on)** az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Mimecast Admin Console tesztfelhasználó](#create-mimecast-admin-console-test-user)** létrehozása – britta Simon megfelelője a Mimecast Felügyeleti konzolon, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához a Mimecast Felügyeleti konzollal hajtsa végre az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **Mimecast Felügyeleti konzol** alkalmazásintegrációs lapján válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![Mimecast felügyeleti konzoltartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-signonurl.png)

    A **Bejelentkezési URL-cím** mezőbe írja be az URL-címet:
    
    | |
    | -- |
    | `https://webmail-uk.mimecast.com`|
    | `https://webmail-us.mimecast.com`|

    > [!NOTE] 
    > Az URL-cím megjelölése régióspecifikus.

4. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **Mimecast Admin Console beállítása** csoportban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure-hirdetésazonosító

    c. Kijelentkezés URL-címe

### <a name="configure-mimecast-admin-console-single-sign-on"></a>A Mimecast Admin konzol egyszeri bejelentkezésének konfigurálása

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként a Mimecast felügyeleti konzolra.

2. Nyissa meg a ** \> Szolgáltatások alkalmazást.**

    ![Szolgáltatások](./media/mimecast-admin-console-tutorial/ic794998.png "Szolgáltatások")

3. Kattintson **a Hitelesítési profilok gombra.**

    ![Hitelesítési profilok](./media/mimecast-admin-console-tutorial/ic794999.png "Hitelesítési profilok")
    
4. Kattintson **az Új hitelesítési profil gombra.**

    ![Új hitelesítési profilok](./media/mimecast-admin-console-tutorial/ic795000.png "Új hitelesítési profilok")

5. A **Hitelesítési profil szakaszban** hajtsa végre az alábbi lépéseket:

    ![Hitelesítési profil](./media/mimecast-admin-console-tutorial/ic795015.png "Hitelesítési profil")
    
    a. A **Leírás** mezőbe írja be a konfiguráció nevét.
    
    b. Válassza **az SAML-hitelesítés kényszerítése a Mimecast felügyeleti konzolhoz**lehetőséget.
    
    c. **Szolgáltatóként**válassza az **Azure Active Directory**lehetőséget.
    
    d. Illessze be az **Azure Hirdetési azonosítót**, amelyet az Azure Portalról másolt a **Kiállító URL-címmezőjébe.**
    
    e. **Illessze be a bejelentkezési URL-címet,** amelyet az Azure Portalról másolt a **bejelentkezési URL-cím** mezőbe.

    f. Illessze be **a bejelentkezési URL-címet,** amelyet az Azure Portalról másolt a **kijelentkezési URL-cím** beszövegbe.
    
    >[!NOTE]
    >A bejelentkezési URL-érték és a kijelentkezési URL-érték megegyezik a Mimecast Admin Console-ra.
    
    g. Nyissa meg az Azure Portalról letöltött base-64-es tanúsítványt a jegyzettömbben, távolítsa el az első sort ("*--*") és az utolsó sort ("*--*"), másolja a többi tartalmát a vágólapra, majd illessze be az **identitásszolgáltatói tanúsítvány (metaadatok)** szövegdobozába.
    
    h. Válassza **az Egyszeri bejelentkezés engedélyezése**lehetőséget.
    
    i. Kattintson a **Mentés** gombra.

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

Ebben a szakaszban engedélyezi Britta Simon számára az Azure egyszeri bejelentkezést a Mimecast Felügyeleti konzolhoz való hozzáférés biztosításával.

1. Az Azure portalon válassza a **Vállalati alkalmazások**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza a **Mimecast Felügyeleti konzol**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában írja be és válassza a **Mimecast Felügyeleti konzol**t.

    ![A Mimecast Felügyeleti konzol hivatkozása az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-mimecast-admin-console-test-user"></a>Mimecast Felügyeleti konzol tesztfelhasználójának létrehozása

Annak érdekében, hogy az Azure AD-felhasználók bejelentkezhessenek a Mimecast felügyeleti konzolra, ki kell építeni őket a Mimecast felügyeleti konzolra. A Mimecast Felügyeleti konzol esetében a kiépítés manuális feladat.

* A felhasználók létrehozása előtt regisztrálnia kell egy tartományt.

**A felhasználói kiépítés konfigurálásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a **Mimecast Felügyeleti konzolra** rendszergazdaként.

2. Tovább a **könyvtárak \> belső**.
   
    ![Könyvtárak](./media/mimecast-admin-console-tutorial/ic795003.png "Könyvtárak")

3. Kattintson **az Új tartomány regisztrálása gombra.**
   
    ![Új tartomány regisztrálása](./media/mimecast-admin-console-tutorial/ic795004.png "Új tartomány regisztrálása")

4. Az új tartomány létrehozása után kattintson az **Új cím gombra.**
   
    ![Új cím](./media/mimecast-admin-console-tutorial/ic795005.png "Új cím")

5. Az új címpárbeszédpanelen hajtsa végre a következő lépéseket:
   
    ![Mentés](./media/mimecast-admin-console-tutorial/ic795006.png "Mentés")
   
    a. Írja be a kapcsolódó szövegdobozokba kiépíteni kívánt érvényes Azure AD-fiók **e-mail-címét,** **globális nevét**, **jelszavát**és **jelszó megerősítése** attribútumait.

    b. Kattintson a **Mentés** gombra.

>[!NOTE]
>A Mimecast Felügyeleti konzol felhasználói fiókkészítő eszközeinek vagy API-inak a Mimecast Felügyeleti konzol által biztosított bármely más használatával azure AD felhasználói fiókokat hozhat létre. 

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha a Hozzáférési panelen a Mimecast Felügyeleti konzol csempére kattint, automatikusan be kell jelentkeznie arra a Mimecast felügyeleti konzolra, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


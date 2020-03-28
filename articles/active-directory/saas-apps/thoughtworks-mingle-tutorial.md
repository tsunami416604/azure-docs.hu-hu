---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Thoughtworks keveredéssel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Thoughtworks keveredés között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 69d859d9-b7f7-4c42-bc8c-8036138be586
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: a12d4dca61734275ef0e56dfe2a800c64dc52540
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74233292"
---
# <a name="tutorial-azure-active-directory-integration-with-thoughtworks-mingle"></a>Oktatóanyag: Az Azure Active Directory integrációja a Thoughtworks-szel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Thoughtworks keveredést az Azure Active Directoryval (Azure AD).
A Thoughtworks keveredése az Azure AD-vel való integrálása a következő előnyöket nyújtja:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Thoughtworks keveredéshez.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve thoughtworks keveredés (Single Sign-On) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a Thoughtworks Keveredés, a következő elemekre van szüksége:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* Thoughtworks Mingle egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* Thoughtworks Mingle támogatja **SP** kezdeményezett SSO

## <a name="adding-thoughtworks-mingle-from-the-gallery"></a>Hozzáadása Thoughtworks Keveredik a galériából

A Thoughtworks-keveredés Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Thoughtworks-keveredést a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha hozzá szeretné adni a Thoughtworks Keveredést a galériából, hajtsa végre a következő lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Thoughtworks Mingle**kifejezést , válassza a **Thoughtworks Keveredés** lehetőséget az eredménypanelről, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Thoughtworks keveredik az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a Thoughtworks-keveredéssel egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, egy Azure AD-felhasználó és a kapcsolódó felhasználó a Thoughtworks mingle létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a Thoughtworks-keveredéssel a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a Thoughtworks Mingle Single Sign-On --hoz](#configure-thoughtworks-mingle-single-sign-on)** konfigurálni az egyszeri bejelentkezés beállításait az alkalmazás oldalán.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre Thoughtworks Mingle teszt felhasználó](#create-thoughtworks-mingle-test-user)** -, hogy egy megfelelője Britta Simon a Thoughtworks keveredés, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához a Thoughtworks-keveredéssel hajtsa végre az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **Thoughtworks-i egyes** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![Thoughtworks Keveredés Domain és URL-ek egyszeri bejelentkezési információk](common/sp-signonurl.png)

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<companyname>.mingle.thoughtworks.com`

    > [!NOTE]
    > Az érték nem valós. Frissítse az értéket a tényleges bejelentkezési URL-címmel. Lépjen kapcsolatba [a Thoughtworks Mingle ügyféltámogatási csapatával](https://support.thoughtworks.com/hc/categories/201743486-Mingle-Community-Support) az érték leküzdése érdekében. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány csoportjában** kattintson a **Letöltés** gombra, ha letöltheti az **összevonási metaadat-XML-t** a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. A **Thoughtworks-keveredés beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-thoughtworks-mingle-single-sign-on"></a>A Thoughtworks keveredéskonfigurálása egyszeri bejelentkezés

1. Jelentkezzen be a **Thoughtworks Mingle** cég webhelyére rendszergazdaként.

2. Kattintson a **Rendszergazda** fülre, majd az **SSO-konfiguráció gombra.**
   
    ![Rendszergazda lap](./media/thoughtworks-mingle-tutorial/ic785157.png "SSO konfiguráció")

3. Az **SSO config** szakaszban hajtsa végre a következő lépéseket:
   
    ![SSO konfiguráció](./media/thoughtworks-mingle-tutorial/ic785158.png "SSO konfiguráció")
    
    a. A metaadatfájl feltöltéséhez kattintson a **Fájl kiválasztása gombra.** 

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

Ebben a szakaszban engedélyezheti Britta Simon számára az Azure egyszeri bejelentkezést a Thoughtworks-keveredéshez való hozzáférés biztosításával.

1. Az Azure Portalon válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza a **Thoughtworks keveredés lehetőséget.**

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza **a Thoughtworks Mingle**lehetőséget.

    ![A Thoughtworks mingle link az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-thoughtworks-mingle-test-user"></a>Gondolatworks-keveredésteszt-felhasználó létrehozása

Az Azure AD-felhasználók számára, hogy képes legyen bejelentkezni, ki kell őket építeni a Thoughtworks-keveredési alkalmazás az Azure Active Directory-felhasználónevek használatával. Abban az esetben, Thoughtworks keveredés, kiépítése egy manuális feladat.

**A felhasználói kiépítés konfigurálásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a Thoughtworks Mingle cég webhelyére rendszergazdaként.

2. Kattintson **a Profil gombra.**
   
    ![Az első projekt](./media/thoughtworks-mingle-tutorial/ic785160.png "Az első projekt")

3. Kattintson a **Rendszergazda** fülre, majd a **Felhasználók gombra.**
   
    ![Felhasználók](./media/thoughtworks-mingle-tutorial/ic785161.png "Felhasználók")

4. Kattintson **az Új felhasználó gombra.**
   
    ![Új felhasználó](./media/thoughtworks-mingle-tutorial/ic785162.png "Új felhasználó")

5. Az **Új felhasználó** párbeszédpanelen hajtsa végre az alábbi lépéseket:
   
    ![Új felhasználó párbeszédpanel](./media/thoughtworks-mingle-tutorial/ic785163.png "Új felhasználó")  
 
    a. Írja be a **Bejelentkezési név**, **Megjelenítendő név**, **Jelszó kiválasztása**, Jelszó **megerősítése** egy érvényes Azure AD-fiók kiépíteni a kapcsolódó szövegdobozok. 

    b. **Felhasználó típusként**válassza a **Teljes felhasználó**lehetőséget.

    c. Kattintson **a Profil létrehozása gombra.**

>[!NOTE]
>A Thoughtworks Mingle felhasználói fiók létrehozási eszközeinek vagy API-inak a Thoughtworks Mingle által biztosított bármely más használatával azure AD felhasználói fiókok at hozhat létre.
> 

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha a Hozzáférési panelen a Thoughtworks keveredés csempére kattint, automatikusan be kell jelentkeznie a Thoughtworks keveredésbe, amelyhez beállította az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


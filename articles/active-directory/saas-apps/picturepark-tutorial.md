---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Pictureparkkal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Picturepark között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 31c21cd4-9c00-4cad-9538-a13996dc872f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.openlocfilehash: 08c5bd8da0dda74156b2d44c8106ed345ef749dd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73177020"
---
# <a name="tutorial-azure-active-directory-integration-with-picturepark"></a>Oktatóanyag: Az Azure Active Directory integrációja a Pictureparkkal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Pictureparkot az Azure Active Directoryval (Azure AD).
A Picturepark integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a Pictureparkhoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve Picturepark (Single Sign-On) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció beállítása a Pictureparkkal a következő elemekre van szüksége:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) kaphat
* Picturepark egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* Picturepark támogatja **SP** kezdeményezett SSO

## <a name="adding-picturepark-from-the-gallery"></a>Picturepark hozzáadása a galériából

A Picturepark Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Pictureparkot a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha a Képparkot a galériából szeretné hozzáadni, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Picturepark**( Picturepark , **Picturepark** ) lehetőséget az eredménypanelről, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Picturepark az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a Pictureparkkal egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, az Azure AD-felhasználó és a kapcsolódó felhasználó picturepark közötti kapcsolat létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a Pictureparksegítségével a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Állítsa be a Picturepark Single Sign-On-t](#configure-picturepark-single-sign-on)** - az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalán.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Picturepark tesztfelhasználó létrehozása](#create-picturepark-test-user)** – britta Simon megfelelője a Pictureparkban, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **Picturepark** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![Picturepark Domain és URL-ek egyszeri bejelentkezési információk](common/sp-identifier.png)

    a. A Bejelentkezés az **URL-cím** mezőbe írja be az URL-címet a következő minta használatával:`https://<companyname>.picturepark.com`

    b. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő minta használatával:

    |  |
    |--|
    | `https://<companyname>.current-picturepark.com`|
    | `https://<companyname>.picturepark.com`|
    | `https://<companyname>.next-picturepark.com`|
    | |

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Bejelentkezési URL-címmel és azonosítóval. Lépjen kapcsolatba [a Picturepark ügyfél támogatási csapatával,](https://picturepark.com/company/picturepark-customer-support) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

5. Az **SAML aláíró tanúsítvány csoportban** kattintson a **Szerkesztés** gombra az **SAML aláíró tanúsítvány** párbeszédpanel megnyitásához.

    ![SAML aláíró tanúsítvány szerkesztése](common/edit-certificate.png)

6. Az **SAML aláíró tanúsítvány szakaszban** másolja az **ujjlenyomatot,** és mentse a számítógépre.

    ![Ujjlenyomat másolása érték](common/copy-thumbprint.png)

7. A **Picturepark beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően. A **bejelentkezési URL-címhez**használja az alábbi minta értékét:`https://login.microsoftonline.com/_my_directory_id_/wsfed`

    > [!Note]
    > _my_directory_id_ az Azure AD-előfizetés bérlői azonosítója.

    ![Konfigurációs URL-címek másolása](./media/picturepark-tutorial/configurls.png)

    a. Azure Hirdetés-azonosító

    b. Kijelentkezés URL-címe

### <a name="configure-picturepark-single-sign-on"></a>Picturepark single sign-on konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Picturepark vállalati webhelyére rendszergazdaként.

2. A felső eszköztáron kattintson a **Felügyeleti eszközök**, majd a **Kezelőkonzol**parancsra.
   
    ![Kezelőkonzol](./media/picturepark-tutorial/ic795062.png "Kezelőkonzol")

3. Kattintson **a Hitelesítés**gombra, majd **az Identitásszolgáltatók parancsra.**
   
    ![Hitelesítés](./media/picturepark-tutorial/ic795063.png "Hitelesítés")

4. Az **Identitásszolgáltató konfigurációja** csoportban hajtsa végre a következő lépéseket:
   
    ![Identitásszolgáltató konfigurációja](./media/picturepark-tutorial/ic795064.png "Identitásszolgáltató konfigurációja")
   
    a. Kattintson a **Hozzáadás** gombra.
  
    b. Írja be a konfiguráció nevét.
   
    c. Válassza **a Beállítás alapértelmezettként**lehetőséget.
   
    d. A **Kiállító URI-szövegmezőbe** illessze be az Azure Portalról másolt **bejelentkezési URL-cím** értékét.
   
    e. A **Megbízható kibocsátó ujjlenyomata** szövegmezőbe illessze be az **SAML aláíró tanúsítvány** szakaszból másolt **ujjlenyomat** értékét. 

5. Kattintson **a JoinDefaultUsersGroup gombra.**

6. Ha be szeretné állítani az **E-mailcím** attribútumot a **Jogcím** szövegmezőben, írja be a Parancsot, `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` és kattintson a Mentés **gombra.**

      ![Konfiguráció](./media/picturepark-tutorial/ic795065.png "Konfiguráció")

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása 

Ez a szakasz célja, hogy hozzon létre egy tesztfelhasználót az Azure Portalon Britta Simon.

1. Az Azure Portalon a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd az **Összes felhasználó**lehetőséget.

    ![A "Felhasználók és csoportok" és a "Minden felhasználó" linkek](common/users.png)

2. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A Felhasználó tulajdonságokban hajtsa végre a következő lépéseket.

    ![A Felhasználó párbeszédpanel](common/user-properties.png)

    a. A **Név** mezőbe írja be **a BrittaSimon**értéket.
  
    b. A **Felhasználónév** mező `brittasimon@yourcompanydomain.extension`típusa mezőben. Például: BrittaSimon@contoso.com

    c. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket.

    d. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés használatával a Picturepark hozzáférést biztosít.

1. Az Azure portalon válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd a **Picturepark**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Picturepark**lehetőséget.

    ![A Picturepark hivatkozás az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-picturepark-test-user"></a>Picturepark tesztfelhasználó létrehozása

Annak érdekében, hogy az Azure AD-felhasználók bejelentkezhessenek a Pictureparkba, ki kell építeni őket a Pictureparkba. Picturepark esetén a kiépítés manuális feladat.

**Felhasználói fiók kiépítéséhez hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **Picturepark bérlőjéhez.**

1. A felső eszköztáron kattintson a **Felügyeleti eszközök**, majd a **Felhasználók parancsra.**
   
    ![Felhasználók](./media/picturepark-tutorial/ic795067.png "Felhasználók")

1. A **Felhasználók áttekintése** lapon kattintson az **Új gombra.**
   
    ![Felhasználókezelés](./media/picturepark-tutorial/ic795068.png "Felhasználókezelés")

1. A **Felhasználó létrehozása** párbeszédpanelen hajtsa végre a kiépíteni kívánt érvényes Azure Active Directory-felhasználó következő lépéseit:
   
    ![Felhasználó létrehozása](./media/picturepark-tutorial/ic795069.png "Felhasználó létrehozása")
   
    a. Az **E-mail cím** mezőbe írja be `BrittaSimon@contoso.com`a felhasználó **e-mail címét.**  
   
    b. A **Jelszó** és **jelszó megerősítése** mezőbe írja be brittaSimon **jelszavát.** 
   
    c. Az **Utónév** mezőbe írja be a Felhasználó **Keresztnevét** **Britta**. 
   
    d. A **Vezetéknév** mezőbe írja be **a Simon**felhasználó **vezetéknevét.**
   
    e. A **Vállalat** mezőbe írja be a felhasználó **vállalatnevét.** 
   
    f. Az **Ország** szövegmezőben válassza ki a felhasználó **országát/régióját.**
  
    g. Az **irányítószám** mezőbe írja be a város **irányítószámát.**
   
    h. A **Város** mezőbe írja be a felhasználó **városnevét.**

    i. Válasszon egy **nyelvet**.
   
    j. Kattintson **a Létrehozás gombra.**

>[!NOTE]
>Az Azure AD felhasználói fiókok kiépítéséhez a Picturepark felhasználói fiók-létrehozási eszközeit vagy API-jait a Picturepark által biztosított bármely más eszköz használatával is használhatja.
>

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a Picturepark csempére kattint, automatikusan be kell jelentkeznie arra a Pictureparkba, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


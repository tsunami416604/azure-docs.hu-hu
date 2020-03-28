---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a UserVoice szolgáltatással | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a UserVoice között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 684a405b-8932-46f6-b43a-4d97a42b6b87
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: 7a3302f1ca615fe5005be9ed1f09995ebf432eb7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74232009"
---
# <a name="tutorial-azure-active-directory-integration-with-uservoice"></a>Oktatóanyag: Az Azure Active Directory integrációja a UserVoice szolgáltatással

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a UserVoice-t az Azure Active Directoryval (Azure AD).
A UserVoice integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a UserVoice-hoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve a UserVoice (Single Sign-On) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció és a UserVoice konfigurálásához a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) kaphat
* A UserVoice egyszeri bejelentkezéssel rendelkező előfizetése

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* UserVoice támogatja **sp** kezdeményezett SSO

## <a name="adding-uservoice-from-the-gallery"></a>UserVoice hozzáadása a galériából

A UserVoice azure AD-be való integrálásának konfigurálásához hozzá kell adnia a UserVoice-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha a Felhasználói hangot a gyűjteményből szeretné hozzáadni, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **UserVoice**, **uservoice** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![UserVoice az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a UserVoice szolgáltatással egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, az Azure AD-felhasználó és a kapcsolódó felhasználó a UserVoice-ban közötti kapcsolat létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a UserVoice-al a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Állítsa be a UserVoice Single Sign-On --hoz](#configure-uservoice-single-sign-on)** configure a Single Sign-On beállítások at alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre UserVoice teszt felhasználó](#create-uservoice-test-user)** - egy megfelelője Britta Simon uservoice, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **UserVoice** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![A UserVoice tartomány és az URL-címek egyszeri bejelentkezési adatai](common/sp-identifier.png)

    a. A Bejelentkezés az **URL-cím** mezőbe írja be az URL-címet a következő minta használatával:`https://<tenantname>.UserVoice.com`

    b. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<tenantname>.UserVoice.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Bejelentkezési URL-címmel és azonosítóval. Lépjen kapcsolatba [a UserVoice ügyfél támogatási csapatával,](https://www.uservoice.com/) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

5. Az **SAML aláíró tanúsítvány csoportban** kattintson a **Szerkesztés** gombra az **SAML aláíró tanúsítvány** párbeszédpanel megnyitásához.

    ![SAML aláíró tanúsítvány szerkesztése](common/edit-certificate.png)

6. Az **SAML aláíró tanúsítvány szakaszban** másolja az **ujjlenyomatot,** és mentse a számítógépre.

    ![Ujjlenyomat másolása érték](common/copy-thumbprint.png)

7. A **UserVoice beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-uservoice-single-sign-on"></a>A UserVoice egyszeri bejelentkezésének konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a UserVoice vállalati webhelyére rendszergazdaként.

2. A felső eszköztáron kattintson a **Beállítások**gombra, majd válassza a menü **Webportál** parancsát.
   
    ![Beállítások szakasz az alkalmazás oldalon](./media/uservoice-tutorial/ic777519.png "Beállítások")

3. A **Webportál** lap **Felhasználói hitelesítés** csoportjában kattintson a **Szerkesztés** gombra a Felhasználói **hitelesítés szerkesztése** párbeszédpanel megnyitásához.
   
    ![Webportál lap](./media/uservoice-tutorial/ic777520.png "Webportál")

4. A **Felhasználói hitelesítés szerkesztése** párbeszédpanelen hajtsa végre az alábbi lépéseket:
   
    ![Felhasználói hitelesítés szerkesztése](./media/uservoice-tutorial/ic777521.png "Felhasználói hitelesítés szerkesztése")
   
    a. Kattintson **az Egyszeri bejelentkezés (SSO)** elemre.
 
    b. Illessze be a **bejelentkezési URL-cím** értékét, amelyet az Azure Portalról másolt az **Egyszeri bejelentkezés szolgáltatásba** való bejelentkezés szövegmezőbe.

    c. Illessze be a **kijelentkezési URL-cím** értékét, amelyet az Azure Portalról másolt az **Egyszeri bejelentkezés szolgáltatásba való kijelentkezésszövegbe.**
 
    d. Illessze be a **Ujjlenyomat** értéket, amelyet az Azure Portalról másolt az **aktuális tanúsítvány SHA1** ujjlenyomat-szövegdobozába.
    
    e. Kattintson **a Hitelesítési beállítások mentése gombra.**

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

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés használatával a UserVoice hozzáférést biztosít.

1. Az Azure Portalon válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd a **UserVoice**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **UserVoice**lehetőséget.

    ![A UserVoice hivatkozás az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-uservoice-test-user"></a>UserVoice-tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezhessenek a UserVoice-ba, ki kell építeni őket a UserVoice-ba. UserVoice esetén kiépítése a manuális feladat.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Felhasználói fiók kiépítéséhez hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be **UserVoice** a UserVoice-bérlőbe.

2. Nyissa meg a **Beállítások lapot.**
   
    ![Beállítások](./media/uservoice-tutorial/ic777811.png "Beállítások")

3. Kattintson **az Általános gombra.**

4. Kattintson **az Ügyintézők és engedélyek elemre.**
   
    ![Ügynökök és engedélyek](./media/uservoice-tutorial/ic777812.png "Ügynökök és engedélyek")

5. Kattintson **a Rendszergazdák hozzáadása gombra.**
   
    ![Rendszergazdák hozzáadása](./media/uservoice-tutorial/ic777813.png "Rendszergazdák hozzáadása")

6. A Rendszergazdák meghívása párbeszédpanelen **hajtsa** végre az alábbi lépéseket:
   
    ![Rendszergazdák meghívása](./media/uservoice-tutorial/ic777814.png "Rendszergazdák meghívása")
   
    a. Az E-mailek mezőbe írja be a kiépíteni kívánt fiók e-mail címét, majd kattintson a **Hozzáadás gombra.**
   
    b. Kattintson **a Meghívás gombra.**

> [!NOTE]
> Az Azure AD felhasználói fiókok kiépítéséhez bármely más UserVoice felhasználói fiók-létrehozási eszközt vagy API-t használhat, amelyet a UserVoice biztosít.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a UserVoice csempére kattint, automatikusan be kell jelentkeznie arra a UserVoice-ba, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


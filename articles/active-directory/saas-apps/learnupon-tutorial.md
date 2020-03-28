---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az LearnUpon programmal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az LearnUpon között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b11c6315-c79d-4f34-9610-bd17070ab7c7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecdcd8c6024b3cacb422b556718bbbdbb5d601c2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67098240"
---
# <a name="tutorial-azure-active-directory-integration-with-learnupon"></a>Oktatóanyag: Az Azure Active Directory integrációja az LearnUpon szolgáltatással

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a LearnUpon szolgáltatást az Azure Active Directoryval (Azure AD).
A LearnUpon integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a LearnUpon szolgáltatáshoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve LearnUpon (Single Sign-On) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a LearnUpon szolgáltatással a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) kaphat
* LearnUpon egyszeri bejelentkezéssel engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.


* Az LearnUpon támogatja az **IDP** által kezdeményezett SSO-t

* Az LearnUpon támogatja **a Just In Time** felhasználói kiépítést


## <a name="adding-learnupon-from-the-gallery"></a>LearnUpon hozzáadása a galériából

A LearnUpon azure AD-be való integrálásának konfigurálásához hozzá kell adnia a LearnUpon szolgáltatást a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha hozzá szeretné adni a LearnUpon-t a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **LearnUpon**( Tanulva elemet az eredménypanelről , majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához ) mezőbe írja be a **LearnUpon** parancsot.

    ![LearnUpon az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a LearnUpon szolgáltatással egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, egy Azure AD-felhasználó és a kapcsolódó felhasználó a LearnUpon közötti kapcsolat létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a LearnUpon Single Sign-On --t](#configure-learnupon-single-sign-on)** az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[LearnUpon tesztfelhasználó létrehozása](#create-learnupon-test-user)** – britta Simon megfelelőjének a LearnUpon-ban, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **LearnUpon** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![Az LearnUpon tartomány és az URL-címek egyszeri bejelentkezési adatai](common/idp-reply.png)

    A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://<companyname>.learnupon.com/saml/consumer`

    > [!NOTE]
    > Az érték nem valós. Frissítse az értéket a tényleges Válasz URL-címével. Lépjen kapcsolatba az [LearnUpon ügyféltámogatási csapatával](https://www.learnupon.com/features/support/) az érték lekéréséhez. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon keresse meg a **THUMBPRINT** - Ezt hozzáadja a LearnUpon SAML beállításaihoz.

    ![A tanúsítvány letöltési hivatkozása](common/certificateraw.png)

6. A **Tanulótanulók beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-learnupon-single-sign-on"></a>A LearnUpon egyszeri bejelentkezés konfigurálása

1. Nyisson meg egy másik böngészőpéldányt, és jelentkezzen be a LearnUpon-ba rendszergazdai fiókkal.

1. Kattintson a **beállítások** fülre.

    ![Egyszeri bejelentkezés konfigurálása](./media/learnupon-tutorial/tutorial_learnupon_06.png)

1. Kattintson **az Egyszeri bejelentkezés - SAML**gombra, majd az Általános **beállítások** parancsra az SAML-beállítások konfigurálásához.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/learnupon-tutorial/tutorial_learnupon_07.png) 

1. Az **Általános beállítások** szakaszban hajtsa végre az alábbi lépéseket:
   
    ![Egyszeri bejelentkezés konfigurálása](./media/learnupon-tutorial/tutorial_learnupon_08.png)  
  
    a. Válassza az **Engedélyezve** lehetőséget.

    b. Válassza a Version as **2.0** **lehetőséget.**

    c. Válassza **a Feltételek kihagyása** lehetőséget **nem.**

    d. Az **SAML Token Post param name (SAML Token Post param name)** mezőbe írja be a kérelem utáni paraméter nevét a fent megadott SAML fogyasztói URL-címhez, amely tartalmazza az ellenőrizendő és hitelesítendő SAML-állítást – például **az SAMLResponse .**

    e. A **Névazonosító formátuma** mezőbe írja be azt az értéket, amely azt jelzi, hogy az `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`SAML-helyességi címben hol található a felhasználói azonosító (e-mail cím) – például .
  
    f. A **Szolgáltató helyének azonosítása** mezőbe írja be azt az értéket, amely jelzi, hogy a felhasználók hová kerülnek, ha az Azure Portal bejelentkezési képernyőjén a feltöltött ikonra kattintanak.
  
    g. A **Kijelentkezés URL-cím** mezőbe illessze be a **kijelentkezési URL-cím** értékét, amelyet az Azure Portalról másolt.

    h. Kattintson **az Ujjlenyomatok kezelése gombra,** majd töltse fel a letöltött tanúsítvány ujjlenyomatát.

1. Kattintson **a Felhasználói beállítások gombra,** majd hajtsa végre az alábbi lépéseket:

     ![Egyszeri bejelentkezés konfigurálása](./media/learnupon-tutorial/tutorial_learnupon_11.png)  

    a. Az **Utónévazonosító formátuma** mezőbe írja be azt az értéket, amely megmutatja, hogy az `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`SAML-helyességi feltételben hol találhatók a felhasználók utónevei – például: . .
  
    b. A **Vezetéknévazonosító formátuma** mezőbe írja be azt az értéket, amely megmutatja, hogy az `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`SAML-helyességi feltételben hol találhatók a felhasználók vezetéknevei – például: . .

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

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés használatával hozzáférést learnupon.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza a **LearnUpon**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza az **LearnUpon**lehetőséget.

    ![A LearnUpon hivatkozás az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-learnupon-test-user"></a>LearnUpon tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a LearnUpon alkalmazásban. LearnUpon támogatja a just-in-time felhasználói kiépítés, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nincs műveletelem. Ha a felhasználó már nem létezik a LearnUpon, egy új jön létre a hitelesítés után. Ha manuálisan kell létrehoznia egy felhasználót, kapcsolatba kell lépnie az [LearnUpon támogatási csapatával.](https://www.learnupon.com/features/support/)

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelEn a LearnUpon csempére kattint, automatikusan be kell jelentkeznie az SSO-t beállító LearnUpon programba. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
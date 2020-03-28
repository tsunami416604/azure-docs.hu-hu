---
title: 'Oktatóanyag: Az Azure Active Directory integrációja Work.com | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Work.com között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 98e6739e-eb24-46bd-9dd3-20b489839076
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7a6dc16eef1bb36a5bd6cbf0502a83481230bc0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67087083"
---
# <a name="tutorial-azure-active-directory-integration-with-workcom"></a>Oktatóanyag: Az Azure Active Directory integrációja Work.com

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja Work.com az Azure Active Directoryval (Azure AD).
A Work.com integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá Work.com.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve Work.com (Single Sign-On) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához Work.com a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) kaphat
* Work.com egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* Work.com támogatja az **SP** által kezdeményezett SSO-t

## <a name="adding-workcom-from-the-gallery"></a>Work.com hozzáadása a galériából

A Work.com Azure AD-be való integrációjának konfigurálásához hozzá kell adnia Work.com a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha Work.com szeretne hozzáadni a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Work.com**, válassza **a Work.com** az eredménypanelről, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Work.com az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését Work.com egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, az Azure AD-felhasználó és a kapcsolódó felhasználó Work.com kapcsolatban kell létrehozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a Work.com a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja Work.com egyszeri bejelentkezést](#configure-workcom-single-sign-on)** - az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre Work.com tesztfelhasználót](#create-workcom-test-user)** – ha a felhasználó Azure AD-megjelenítéséhez kapcsolódó Work.com britta Simon megfelelőjével szeretne rendelkezni.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

>[!NOTE]
>Az egyszeri bejelentkezés konfigurálásához még be kell állítania egy egyéni Work.com tartománynevet. Meg kell adnia legalább egy tartománynevet, tesztelnie kell a tartománynevét, és telepítenie kell a teljes szervezetre.

Az Azure AD egyszeri bejelentkezésének Work.com konfigurálásához hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **Work.com** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![Work.com tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-signonurl.png)

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`http://<companyname>.my.salesforce.com`

    > [!NOTE]
    > Az érték nem valós. Frissítse az értéket a tényleges bejelentkezési URL-címmel. Lépjen kapcsolatba [Work.com ügyféltámogatási csapatával](https://help.salesforce.com/articleView?id=000159855&type=3) az érték lefelvételéhez. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **Beállítás Work.com** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-workcom-single-sign-on"></a>Egyszeri bejelentkezés Work.com konfigurálása

1. Jelentkezzen be a Work.com bérlőbe rendszergazdaként.

2. Nyissa meg a **telepítőt.**
   
    ![Telepítés](./media/work-com-tutorial/ic794108.png "Telepítés")

3. A bal oldali navigációs ablak **Felügyeleti** szakaszában kattintson a **Tartománykezelés gombra** a kapcsolódó szakasz kibontásához, majd a **Saját tartomány** elemre kattintva nyissa meg a Saját **tartomány** lapot. 
   
    ![Saját tartomány](./media/work-com-tutorial/ic767825.png "Saját tartomány")

4. Annak ellenőrzéséhez, hogy a tartomány megfelelően van-e beállítva, ellenőrizze, hogy az**My Domain Settings**a "**4.**
   
    ![Felhasználóra telepített tartomány](./media/work-com-tutorial/ic784377.png "Felhasználóra telepített tartomány")

5. Jelentkezzen be a Work.com bérlőbe.

6. Nyissa meg a **telepítőt.**
    
    ![Telepítés](./media/work-com-tutorial/ic794108.png "Telepítés")

7. Bontsa ki a **Biztonsági vezérlők menüt,** majd kattintson **az Egyszeri bejelentkezés beállításai parancsra.**
    
    ![Egyszeri bejelentkezés beállításai](./media/work-com-tutorial/ic794113.png "Egyszeri bejelentkezés beállításai")

8. Az **Egyszeri bejelentkezés beállításai** párbeszédpanelen hajtsa végre az alábbi lépéseket:
    
    ![SAML engedélyezve](./media/work-com-tutorial/ic781026.png "SAML engedélyezve")
    
    a. Válassza **az SAML Enabled lehetőséget.**
    
    b. Kattintson **az Új gombra.**

9. Az **SAML egyszeri bejelentkezési beállítások** szakaszában hajtsa végre az alábbi lépéseket:
    
    ![SAML egyszeri bejelentkezési beállítás](./media/work-com-tutorial/ic794114.png "SAML egyszeri bejelentkezési beállítás")
    
    a. A **Név** mezőbe írja be a konfiguráció nevét.  
       
    > [!NOTE]
    > A **Név** értékének megadása automatikusan feltölti az **API-név** szövegmezőt.
    
    b. A **Kiállító** szövegmezőbe illessze be az **Azure AD-azonosító** értékét, amelyet az Azure Portalról másolt.
    
    c. A letöltött tanúsítvány Azure Portalról való feltöltéséhez kattintson a **Tallózás gombra.**
    
    d. Az **Entitásazonosító** szövegmezőbe írja `https://salesforce-work.com`be a következőt:
    
    e. **Saml identity type (SAML identity type)** néven jelölje be a **Helyességi feltétel a Felhasználói objektum összevonási azonosítóját.**
    
    f. Saml **identity location (SAML identitáshely)** ként válassza az Identitás elem ét **a Tárgy utasítás NameIdentfier elemében**elemet.
    
    g. Az **Identitásszolgáltató bejelentkezési URL-címmezőjébe** illessze be az Azure Portalról másolt **bejelentkezési URL-cím** értékét.

    h. Az **Identitásszolgáltató kijelentkezési URL-címének** szövege mezőbe illessze be a **kijelentkezési URL-cím** értékét, amelyet az Azure Portalról másolt.
    
    i. Szolgáltatóként **kezdeményezett kérelemkötésként**válassza a **HTTP Post**lehetőséget.
    
    j. Kattintson a **Mentés** gombra.

10. A klasszikus Work.com a bal oldali navigációs ablaktáblán kattintson a **Domain Management** elemre a kapcsolódó szakasz kibontásához, majd a **Saját tartomány** elemre kattintva nyissa meg a Saját **tartomány** lapot. 
    
    ![Saját tartomány](./media/work-com-tutorial/ic794115.png "Saját tartomány")

11. A **Saját tartomány** lap **Bejelentkezési lap márkajelzése** csoportban kattintson a **Szerkesztés gombra.**
    
    ![Bejelentkezési oldal márkajelzése](./media/work-com-tutorial/ic767826.png "Bejelentkezési oldal márkajelzése")

12. A **Bejelentkezési lap márkajelzése** lap **Hitelesítési szolgáltatás** szakaszában megjelenik az **SAML SSO-beállítások** neve. Jelölje ki, majd kattintson a **Mentés gombra.**
    
    ![Bejelentkezési oldal márkajelzése](./media/work-com-tutorial/ic784366.png "Bejelentkezési oldal márkajelzése")

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

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés t, hozzáférést biztosítva Work.com.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza a **Work.com**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza **a Work.com**lehetőséget.

    ![Az alkalmazások listájában található Work.com hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-workcom-test-user"></a>Work.com tesztfelhasználó létrehozása

Ahhoz, hogy az Azure Active Directory-felhasználók be tudjanak jelentkezni, ki kell építeniőket Work.com. Work.com esetén a kiépítés manuális feladat.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>A felhasználói kiépítés konfigurálásához hajtsa végre a következő lépéseket:

1. Jelentkezzen be a Work.com vállalati webhelyére rendszergazdaként.

2. Nyissa meg a **telepítőt.**
   
    ![Telepítés](./media/work-com-tutorial/IC794108.png "Telepítés")

3. Nyissa meg a **Felhasználók \> kezelése lehetőséget.**
   
    ![Felhasználók kezelése](./media/work-com-tutorial/IC784369.png "Felhasználók kezelése")

4. Kattintson **az Új felhasználó gombra.**
   
    ![Minden felhasználó](./media/work-com-tutorial/IC794117.png "Minden felhasználó")

5. A Felhasználó szerkesztése szakaszban hajtsa végre a következő lépéseket egy érvényes Azure AD-fiók attribútumaiban, amelyet a kapcsolódó szövegdobozokba szeretne beépíteni:
   
    ![Felhasználói szerkesztés](./media/work-com-tutorial/ic794118.png "Felhasználói szerkesztés")
   
    a. Az **Utónév** mezőbe írja be a **Felhasználó Britta** **előnevét.**
    
    b. A **Vezetéknév** mezőbe írja be **a Simon**felhasználó **vezetéknevét.**
    
    c. Az **Alias** mezőbe írja be a **Felhasználó BrittaS** **nevét.**
    
    d. Az **E-mail** mezőbe írja be Brittasimon@contoso.coma felhasználó **e-mail címét.**
    
    e. A **Felhasználónév** mezőbe írja be a felhasználó Brittasimon@contoso.comnevét, például .
    
    f. A **Nick név** mezőbe írja be **a Simon**felhasználó **nicknevét.**
    
    g. Válassza a **Szerepkör,** **a Felhasználói licenc**és a Profil **lehetőséget.**
    
    h. Kattintson a **Mentés** gombra.  
      
    > [!NOTE]
    > Az Azure AD-fiók tulajdonosa kap egy e-mailt, amely egy linket, hogy erősítse meg a fiókot, mielőtt aktívvá válik.
    > 

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a hozzáférési panelen a Work.com csempére kattint, automatikusan be kell jelentkeznie az on-Work.com, amelyre az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


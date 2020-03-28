---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a HighGear-szel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a HighGear között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 55dcd2fb-96b7-46ec-9e69-eee71c535773
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ed06586435315935566ca0b1519b182d4fc47d39
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73159035"
---
# <a name="tutorial-azure-active-directory-integration-with-highgear"></a>Oktatóanyag: Az Azure Active Directory integrációja a HighGear-szel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a HighGear-t az Azure Active Directoryval (Azure AD).
A HighGear integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a HighGear-hez.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve legyenek a HighGear (Single Sign-On) szolgáltatásba az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció highgear-el való konfigurálásához a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, itt egy hónapos próbaverziót [kaphat.](https://azure.microsoft.com/pricing/free-trial/)
* Vállalati vagy korlátlan licenccel rendelkező HighGear rendszer

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban megtudhatja, hogyan konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A HighGear támogatja az **SP és az IdP** által kezdeményezett SSO-t

## <a name="adding-highgear-from-the-gallery"></a>A HighGear hozzáadása a galériából

A HighGear azure AD-be való integrálásának konfigurálásához hozzá kell adnia a HighGear-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha a HighGear-t a galériából szeretné hozzáadni, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején az **Új alkalmazás** gombra.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **HighGear**kifejezést, válassza a **HighGear** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![HighGear az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban megtudhatja, hogyan konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a HighGear rendszerrel egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, az Azure AD-felhasználó és a kapcsolódó felhasználó a HighGear-rendszer közötti kapcsolat létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a HighGear-rendszerrel a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a HighGear Single Sign-On --t](#configure-highgear-single-sign-on)** az egyszeri bejelentkezés beállításainak konfigurálásához a HighGear alkalmazás oldalán.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre HighGear teszt felhasználó](#create-highgear-test-user)** - hogy egy megfelelője Britta Simon a HighGear, amely kapcsolódik a felhasználó Azure AD ábrázolása. 
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban megtudhatja, hogyan engedélyezheti az Azure AD egyszeri bejelentkezését az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **HighGear-alkalmazásintegrációs** lapon válassza az Egyszeri **bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![HighGear Domain és URL-címek egyszeri bejelentkezési információk](common/idp-intiated.png)

    a. Az **Azonosító** szövegmezőbe illessze be a **Service Provider Entity ID** mező értékét, amely a HighGear rendszer Egyszeri bejelentkezési beállítások lapján található.

    ![A Szolgáltató entitás azonosítója mező](media/highgear-tutorial/service-provider-entity-id-field.png)
    
    > [!NOTE]
    > Az egyszeri bejelentkezési beállítások oldal eléréséhez be kell jelentkeznie a HighGear rendszerbe. Miután bejelentkezett, vigye az egeret a HighGear Adminisztráció fülére, és kattintson az Egyszeri bejelentkezési beállítások menüelemre.
    
    ![Az Egyszeri bejelentkezés beállításai menüelem](media/highgear-tutorial/single-sign-on-settings-menu-item.png)

    b. A **Válasz URL-cím** mezőjébe illessze be a **helyességifeltétel-fogyasztói szolgáltatás (ACS) URL-címét** a HighGear rendszer Egyszeri bejelentkezési beállítások lapjáról.

    ![Az egyenjogiás fogyasztói szolgáltatás (ACS) URL-címe mező](media/highgear-tutorial/assertion-consumer-service-url-field.png)

    c. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

     ![HighGear Domain és URL-címek egyszeri bejelentkezési információk](common/metadata-upload-additional-signon.png)

     A **Bejelentkezési URL-cím** mezőbe illessze be a **Service Provider Entity ID** mező értékét, amely a HighGear rendszer Egyszeri bejelentkezési beállítások lapján található. (Ez az entitásazonosító egyben a HighGear rendszer alap URL-címe is, amelyet az SP által kezdeményezett bejelentkezéshez kell használni.)

    ![A Szolgáltató entitás azonosítója mező](media/highgear-tutorial/service-provider-entity-id-field.png)

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-lel és a bejelentkezési URL-lel a HighGear **rendszer Egyszeri bejelentkezési beállítások** lapján. Ha segítségre van szüksége, kérjük, forduljon a [HighGear támogatási csapatához.](mailto:support@highgear.com)

4. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** csoportban kattintson a **Letöltés** gombra a **tanúsítvány letöltéséhez (Base64)** és a számítógépre való mentéséhez. Szüksége lesz rá az egyszeri bejelentkezési konfiguráció egy későbbi lépésében.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **HighGear beállítása** csoportban jegyezze fel a következő URL-címek helyét.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL-cím. Erre az értékre a #2 lépésrészben, a **HighGear Single Sign-On (Az** #2.

    b. Azure Hirdetési azonosító. Erre az értékre a Step #3 #3-ban, az alábbi **HighGear Single Sign-On (Regisztráció konfigurálása)** területen lesz szüksége.

    c. Kijelentkezési URL-cím. Erre az értékre a Step #4 #4 című, az alábbi **HighGear Single Sign-On (A highgear egyszeri bejelentkezés konfigurálása)** című részben talál.

### <a name="configure-highgear-single-sign-on"></a>HighGear egyszeri bejelentkezés konfigurálása

A HighGear egyszeri bejelentkezéshez való konfigurálásához jelentkezzen be a HighGear rendszerbe. Miután bejelentkezett, vigye az egeret a HighGear Adminisztráció fülére, és kattintson az Egyszeri bejelentkezési beállítások menüelemre.

![Az Egyszeri bejelentkezés beállításai menüelem](media/highgear-tutorial/single-sign-on-settings-menu-item.png)

1. Az **Identitásszolgáltató neve**mezőbe írjon be egy rövid leírást, amely megjelenik a HighGear Egyszeri bejelentkezés gombjában a Bejelentkezés oldalon. Például: Azure AD

2. A HighGear **egyszeri bejelentkezési (Egyszeri bejelentkezés) URL-címmezőjébe** illessze be az értéket az Azure **HighGear beállítása** szakaszában található **Bejelentkezési URL-cím** mezőjéből.

3. A HighGear **identitásszolgáltató entitásazonosítója** mezőjében illessze be az **azure-beli AD-azonosító** mező értékét, amely az Azure **HighGear beállítása** szakaszában található.

4. A HighGear **egykijelentkezési (SLO) URL-címmezőjébe** illessze be az értéket a **Kijelentkezés URL-címe** mezőből, amely az Azure **HighGear beállítása** szakaszában található.

5. A Jegyzettömb segítségével nyissa meg az **Azure SAML aláíró tanúsítvány szakaszából** letöltött tanúsítványt. Le kellett volna töltenie a **Tanúsítvány (Base64)** formátumot. Másolja a tanúsítvány tartalmát a Jegyzettömbből, és illessze be a HighGear **Identitásszolgáltatói tanúsítvány** mezőjébe.

6. E-mailben a [HighGear támogatási csapatának,](mailto:support@highgear.com) hogy kérje a HighGear tanúsítványát. Kövesse a tőlük kapott utasításokat a **HighGear tanúsítvány** és a **HighGear tanúsítvány jelszó** mezőinek kitöltéséhez.

7. Kattintson a **Mentés** gombra a HighGear Single Sign-On konfiguráció mentéséhez.

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

    c. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket.

    d. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi Britta Simon számára az Azure egyszeri bejelentkezést a HighGear-hez való hozzáférés biztosításával.

1. Az Azure portalon válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd a **HighGear**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **HighGear**lehetőséget .

    ![A HighGear hivatkozás az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-highgear-test-user"></a>HighGear tesztfelhasználó létrehozása

Ha highgear tesztfelhasználót szeretne létrehozni az egyszeri bejelentkezési konfiguráció teszteléséhez, jelentkezzen be a HighGear rendszerbe.

1. Kattintson az **Új névjegy létrehozása** gombra.

    ![Az Új névjegy létrehozása gomb](media/highgear-tutorial/create-new-contact-button.png)

    Megjelenik egy menü, amely lehetővé teszi a létrehozni kívánt névjegy kiválasztását.

2. HighGear-felhasználó létrehozásához kattintson az **Egyedi** menüelemre.

    Az ablaktábla a jobb oldalon csúszik ki, így beírhatja az új felhasználó adatait.  
    ![Az Új kapcsolatfelvételi űrlap](media/highgear-tutorial/new-contact-form.png)

3. A **Név** mezőbe írja be az ügyfél nevét. Például: Britta Simon

4. Kattintson a **További beállítások** menüre, és válassza a **Fiókinformáció** menüelemet.

    ![Kattintás a Fiókinformáció menüelemre](media/highgear-tutorial/account-info-menu-item.png)

5. Állítsa a **Bejelentkezés mezőt** Igen-re.

    Az **Egyszeri bejelentkezés engedélyezése** mező automatikusan Igen lesz.

6. Az **Egyszeri bejelentkezés felhasználói azonosítója** mezőbe írja be a felhasználó azonosítóját. Például:BrittaSimon@contoso.com

    A Fiókinformáció szakasznak most valahogy így kell kinéznie:  
    ![A kész Fiókinformáció szakasz](media/highgear-tutorial/finished-account-info-section.png)

7. A névjegy mentéséhez kattintson az ablaktábla alján található **Mentés** gombra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a HighGear csempére kattint, automatikusan be kell jelentkeznie arra a HighGear-be, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


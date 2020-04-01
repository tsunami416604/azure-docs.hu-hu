---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az ON24 virtuális környezet SAML kapcsolatával | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az ON24 virtuális környezet SAML-kapcsolata között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d4028fb5-b2ad-4c5d-b123-7b675c509d64
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/13/2019
ms.author: jeedes
ms.openlocfilehash: 801a631b56a11e68c444ede846ff82195cd7627f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67095728"
---
# <a name="tutorial-azure-active-directory-integration-with-on24-virtual-environment-saml-connection"></a>Oktatóanyag: Az Azure Active Directory integrációja az ON24 virtuális környezet SAML-kapcsolatával

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az ON24 virtuális környezet SAML-kapcsolatát az Azure Active Directoryval (Azure AD).
Az ON24 virtuális környezet SAML-kapcsolat integrálása az Azure AD-vel a következő előnyökkel jár:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá az ON24 virtuális környezet SAML-kapcsolathoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve legyenek az ON24 virtuális környezetSAML-kapcsolatba (Single Sign-On) az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció on24 virtuális környezet SAML-kapcsolattal való konfigurálásához a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* ON24 Virtuális környezet SAML Connection egyszeri bejelentkezéses előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* ON24 Virtuális környezet SAML connection támogatja **az SP** és **IDP** által kezdeményezett SSO

## <a name="adding-on24-virtual-environment-saml-connection-from-the-gallery"></a>ON24 virtuális környezet SAML kapcsolat hozzáadása a galériából

Az ON24 virtuális környezet SAML-kapcsolat Azure AD-be való integrációjának konfigurálásához hozzá kell adnia az ON24 virtuális környezet SAML-kapcsolatát a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha on24 virtuális környezet saml-kapcsolatát szeretné hozzáadni a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **ON24 Virtual Environment SAML Connection (Virtuális környezet SAML-kapcsolat) kifejezést,** és válassza az **ON24 virtuális környezet SAML kapcsolat elemét** az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![ON24 Virtuális környezet SAML kapcsolat az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését az ON24 virtuális környezet SAML-kapcsolatával egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, egy Azure AD-felhasználó és a kapcsolódó felhasználó közötti kapcsolat ON24 virtuális környezet SAML kapcsolat létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez az ON24 virtuális környezet SAML-kapcsolatával a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja az ON24 virtuális környezet SAML connection Single Sign-On --t](#configure-on24-virtual-environment-saml-connection-single-sign-on)** az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre ON24 virtuális környezet SAML connection teszt felhasználó](#create-on24-virtual-environment-saml-connection-test-user)** – egy megfelelője Britta Simon on24 virtuális környezet SAML kapcsolat, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének on24 virtuális környezetbeli SAML-kapcsolattal való konfigurálásához hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)az **ON24 virtuális környezet SAML connection** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban Ha az alkalmazást **IDP** által kezdeményezett módban kívánja konfigurálni, hajtsa végre a következő lépéseket:

    ![ON24 Virtuális környezet SAML kapcsolattartomány és URL-címek egyszeri bejelentkezési információk](common/idp-relay.png)

    a. Az **Azonosító** mezőbe írjon be egy URL-címet:

     **Az éles környezet URL-címe**
    
    `SAML-VSHOW.on24.com`

    `SAML-Gateway.on24.com` 

    `SAP PROD SAML-EliteAudience.on24.com` 
                
     **QA környezet URL-címe**
    
    `SAMLQA-VSHOW.on24.com` 

    `SAMLQA-Gateway.on24.com` 

    `SAMLQA-EliteAudience.on24.com`

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet:

     **Az éles környezet URL-címe**
    
    `https://federation.on24.com/sp/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1WU2hvdy5vbjI0LmNvbSJ9/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1HYXRld2F5Lm9uMjQuY29tIn0/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1FbGl0ZUF1ZGllbmNlLm9uMjQuY29tIn0/ACS.saml2`

     **QA környezet URL-címe**
    
    `https://qafederation.on24.com/sp/ACS.saml2`

    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLVZzaG93Lm9uMjQuY29tIn0/ACS.saml2`

    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLUdhdGV3YXkub24yNC5jb20ifQ/ACS.saml2`
     
    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLUVsaXRlQXVkaWVuY2Uub24yNC5jb20ifQ/ACS.saml2` 

    c. Kattintson **a További URL-címek beállítása gombra.** 

    d. A **Továbbítási állapot** mezőbe írjon be egy URL-címet:`https://vshow.on24.com/vshow/ms_azure_saml_test?r=<ID>`

5.  Ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni, hajtsa végre a következő lépést:

    ![ON24 Virtuális környezet SAML kapcsolattartomány és URL-címek egyszeri bejelentkezési információk](common/both-signonurl.png)

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://vshow.on24.com/vshow/<INSTANCENAME>`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges továbbítási állapottal és bejelentkezési URL-címmel. Lépjen kapcsolatba [az ON24 virtuális környezet SAML connection client támogatási csapatával,](https://www.on24.com/contact-us/) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

4. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány csoportjában** kattintson a **Letöltés** gombra, ha letöltheti az **összevonási metaadat-XML-t** a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. Az **ON24 virtuális környezet SAML-kapcsolat beállítása** szakaszon másolja a megfelelő URL-cím(eke)t a követelménynek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-on24-virtual-environment-saml-connection-single-sign-on"></a>AZ ON24 virtuális környezet hitelesítése SAML-kapcsolat egyszeri bejelentkezés

Az **ON24 virtuális környezet SAML kapcsolati** oldalán történő egyszeri bejelentkezés konfigurálásához el kell küldenie a letöltött **összevonási metaadat-XML-t** és a megfelelő másolt URL-címeket az Azure Portalról az [ON24 virtuális környezet SAML kapcsolat támogatási csapatának.](https://www.on24.com/about-us/support/) Úgy állították be ezt a beállítást, hogy az SAML SSO-kapcsolat mindkét oldalon megfelelően legyen beállítva.

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

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés t az ON24 virtuális környezet SAML-kapcsolat hoz való hozzáférés biztosításával.

1. Az Azure portalon válassza a **Vállalati alkalmazások**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza az **ON24 virtuális környezet SAML-kapcsolat lehetőséget.**

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza az **ON24 virtuális környezet SAML kapcsolata**lehetőséget.

    ![Az ON24 virtuális környezet SAML kapcsolathivatkozása az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-on24-virtual-environment-saml-connection-test-user"></a>ON24 virtuális környezet SAML-kapcsolatteszt-felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre az ON24 virtuális környezet SAML-kapcsolatában. Együttműködve [ON24 virtuális környezet SAML kapcsolat támogatási csapat](https://www.on24.com/about-us/support/) a felhasználók hozzáadása az ON24 virtuális környezet SAML kapcsolat platformon. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha a hozzáférési panelen az ON24 virtuális környezet SAML kapcsolat csempéjére kattint, automatikusan be kell jelentkeznie arra az ON24 virtuális környezetsaml-kapcsolatra, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


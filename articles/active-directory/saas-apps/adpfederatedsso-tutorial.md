---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az ADP-vel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és ADP között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/26/2019
ms.author: jeedes
ms.openlocfilehash: c170581496241ab3be0fe54bb484549ab4b1f976
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88542904"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adp"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az ADP-vel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az ADP-t Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja az ADP-t, a következőket teheti:

* Az ADP-hez hozzáférő Azure AD-beli vezérlés.
* Engedélyezze, hogy a felhasználók automatikusan bejelentkezzenek az ADP-be az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* ADP egyszeri bejelentkezéses (SSO) engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* Az ADP támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egyetlen bérlőn.

## <a name="adding-adp-from-the-gallery"></a>ADP hozzáadása a gyűjteményből

Az ADP Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az ADP-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be az **ADP** kifejezést a keresőmezőbe.
1. Válassza az **ADP** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-adp"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése az ADP-hez

Konfigurálja és tesztelje az Azure AD SSO-t az ADP-vel egy **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között az ADP-ben.

Az Azure AD SSO az ADP-vel való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
2. Az **[ADP SSO konfigurálása](#configure-adp-sso)** – az egyes Sign-On beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre egy ADP-teszt felhasználót](#create-adp-test-user)** – a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon (ADP).
3. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal az **ADP** Application Integration lapon kattintson a **Tulajdonságok fülre** , és hajtsa végre a következő lépéseket: 

    ![Egyszeri bejelentkezési tulajdonságok](./media/adpfederatedsso-tutorial/tutorial_adp_prop.png)

    a. Állítsa az engedélyezve beállítást a felhasználók számára a **bejelentkező** mező értékének **Igen**értékűre állításához.

    b. Másolja a **felhasználói hozzáférési URL-címet** , és illessze be a **bejelentkezési URL-cím konfigurálása szakaszba**, amelyet az oktatóanyag későbbi részében ismertet.

    c. Állítsa az **Igen**értékre a **felhasználó-hozzárendelés kötelező** mező értékét.

    d. A **Visible to Users** mező értékeként a **nem**értéket állítsa be.

1. A [Azure Portal](https://portal.azure.com/)az **ADP** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet:  `https://fed.adp.com`

4. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. Az **ADP beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** elemre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést az ADP hozzáférésének megadásával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza az **ADP**elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-adp-sso"></a>ADP SSO konfigurálása

Az egyszeri bejelentkezés **ADP** -alapú konfigurálásához fel kell töltenie a letöltött **metaadatok XML-fájlját** az [ADP webhelyén](https://adpfedsso.adp.com/public/login/index.fcc).

> [!NOTE]  
> Ez a folyamat néhány napig is eltarthat.

### <a name="configure-your-adp-services-for-federated-access"></a>Az ADP szolgáltatás (ok) konfigurálása összevont hozzáféréshez

>[!Important]
> Az ADP-szolgáltatásokhoz összevont hozzáférést igénylő alkalmazottakat hozzá kell rendelni az ADP szolgáltatás alkalmazáshoz, és ezt követően a felhasználókat újra hozzá kell rendelni az adott ADP szolgáltatáshoz.
Az ADP-képviselő jóváhagyásának kézhezvétele után konfigurálja az ADP szolgáltatás (oka) t, és rendelje hozzá a felhasználókat az adott ADP szolgáltatáshoz való hozzáférés szabályozásához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be az **ADP** kifejezést a keresőmezőbe.
1. Válassza az **ADP** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.
1. A Azure Portal az **ADP** Application Integration lapon kattintson a **Tulajdonságok fülre** , és hajtsa végre a következő lépéseket:  

    ![Egyszeri bejelentkezéshez csatolt tulajdonságok](./media/adpfederatedsso-tutorial/tutorial_adp_linkedproperties.png)

    a.  Állítsa az engedélyezve beállítást a felhasználók számára a **bejelentkező** mező értékének **Igen**értékűre állításához.

    b.  Állítsa az **Igen**értékre a **felhasználó-hozzárendelés kötelező** mező értékét.

    c.  Állítsa az **Igen**értékre a **Visible to Users** mező értékét.

1. A [Azure Portal](https://portal.azure.com/)az **ADP** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.

1. Az **egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza a **mód** **csatoltként**lehetőséget. az alkalmazás az **ADP**-vel való összekapcsolásához.

    ![Egyszeri bejelentkezés csatolva](./media/adpfederatedsso-tutorial/tutorial_adp_linked.png)

1. Navigáljon a **bejelentkezési URL-cím konfigurálása** szakaszhoz, hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés kelléke](./media/adpfederatedsso-tutorial/tutorial_adp_linkedsignon.png)

    a. Illessze be a **felhasználói hozzáférési URL-címet**, amelyet a fenti **Tulajdonságok lapról** másolt (a fő ADP-alkalmazásból).
                                                             
    b. A következő 5 alkalmazás támogatja a különböző **továbbító állapotú URL-címeket**. Az adott alkalmazáshoz tartozó megfelelő **továbbítási állapot URL-címét** manuálisan kell hozzáfűzni a **felhasználói hozzáférés URL-címéhez**.
    
    * **ADP munkaerő most**
        
        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?WFN`

    * **ADP munkaerő-kibővített idő**
        
        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?EETDC2`
    
    * **ADP Vantage HCM**
        
        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?ADPVANTAGE`

    * **ADP Enterprise HR**

        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?PORTAL`

    * **MyADP**

        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?REDBOX`

9. **Mentse** a módosításokat.

10. Az ADP-képviselő jóváhagyásának kézhezvétele után kezdjen el a tesztet egy vagy két felhasználóval.

    a. Rendeljen néhány felhasználót az ADP szolgáltatásbeli alkalmazáshoz az összevont hozzáférés teszteléséhez.

    b. A teszt sikeres, amikor a felhasználók hozzáférnek az ADP szolgáltatás alkalmazásához a katalógusban, és hozzáférhetnek az ADP szolgáltatáshoz.
 
11. A sikeres teszt megerősítése után rendelje hozzá az összevont ADP-szolgáltatást az egyes felhasználókhoz vagy felhasználói csoportokhoz, amelyeket az oktatóanyag későbbi részében talál, és adja ki az alkalmazottainak.

### <a name="create-adp-test-user"></a>ADP-tesztkörnyezet létrehozása

Ennek a szakasznak a célja, hogy létrehozzon egy B. Simon nevű felhasználót az ADP-ben. A felhasználók az ADP-fiókban való hozzáadásához az [ADP támogatási csapatával](https://www.adp.com/contact-us/overview.aspx) dolgozhat. 

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Amikor a hozzáférési panelen az ADP csempére kattint, automatikusan be kell jelentkeznie az ADP-be, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Az ADP kipróbálása az Azure AD-vel](https://aad.portal.azure.com)

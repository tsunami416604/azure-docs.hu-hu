---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az ADP-vel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az ADP között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7be5331b-0481-48f7-9d6b-619dfec657e1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/26/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b031ded2022078c31bd8570c6a6317c398715480
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "70162656"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adp"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az ADP-vel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az ADP-t az Azure Active Directoryval (Azure AD). Ha integrálja az ADP-t az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér az ADP-hez.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve az ADP az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Az ADP egyszeri bejelentkezéssel (SSO) engedélyezett előfizetéssel rendelkezik.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* Az ADP támogatja az **IDP** által kezdeményezett sso-t

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egy bérlőben.

## <a name="adding-adp-from-the-gallery"></a>ADP hozzáadása a galériából

Az ADP Azure AD-be való integrációjának konfigurálásához hozzá kell adnia az ADP-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből szakaszban** írja be az **ADP** kifejezést a keresőmezőbe.
1. Válassza az **ADP** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-adp"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése az ADP-hez

Konfigurálja és tesztelje az Azure AD SSO-t az ADP-vel egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó a ADP-ben.

Az Azure AD SSO konfigurálásához és teszteléséhez az ADP-vel hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    1. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
2. **[Konfigurálja az ADP egyszeri bejelentkezést](#configure-adp-sso)** – az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
    1. **[ADP-tesztfelhasználó létrehozása](#create-adp-test-user)** – b.Simon megfelelője az ADP-ben, amely a felhasználó Azure AD-reprezentációjához kapcsolódik.
3. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az Azure Portalon az **ADP-alkalmazások** integrációja lapon kattintson a **Tulajdonságok fülre,** és hajtsa végre a következő lépéseket: 

    ![Egyszeri bejelentkezési tulajdonságok](./media/adpfederatedsso-tutorial/tutorial_adp_prop.png)

    a. Állítsa a **felhasználók számára engedélyezve a bejelentkezési** mező értékét **Igen**értékre.

    b. Másolja a **felhasználói hozzáférési URL-címet,** és be kell illesztenie a **Bejelentkezési URL konfigurálása szakaszban,** amelyet az oktatóanyag későbbi részében ismertetünk.

    c. Állítsa a **Felhasználó hozzárendelés szükséges** mező értékét **Igen**értékre.

    d. Állítsa a **Látható a felhasználóknak** mező értékét **Nem**értékre.

1. Az [Azure Portalon](https://portal.azure.com/)az **ADP-alkalmazásintegrációs** lapon keresse meg a **Kezelés szakaszt,** és válassza **az Egyszeri bejelentkezés**lehetőséget.
1. Az **Egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet:`https://fed.adp.com`

4. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az SAML aláíró tanúsítvány szakaszban keresse meg az **összevonási** **metaadatok XML-jét,** és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. Az **ADP beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy tesztfelhasználót hoz létre az Azure Portalon B.Simon néven.

1. Az Azure Portal bal oldali ablaktáblájában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd válassza az **Összes felhasználó**lehetőséget.
1. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.
1. A **Felhasználói** tulajdonságok csoportban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőbe írja username@companydomain.extensionbe a mezőt. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet, majd írja le a **Jelszó** mezőben megjelenő értéket.
   1. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés az ADP-hez való hozzáférés biztosításával.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza az **ADP**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-adp-sso"></a>ADP-sso konfigurálása

Az egyszeri bejelentkezés a **ADP-oldalon** való konfigurálásához fel kell töltenie a letöltött **metaadat-XML-t** az [ADP webhelyére.](https://adpfedsso.adp.com/public/login/index.fcc)

> [!NOTE]  
> Ez a folyamat eltarthat néhány napig.

### <a name="configure-your-adp-services-for-federated-access"></a>Az ADP-szolgáltatás(ok) konfigurálása összevont hozzáféréshez

>[!Important]
> Az ADP-szolgáltatásokhoz összevont hozzáférést igénylő alkalmazottakat hozzá kell rendelni az ADP szolgáltatásalkalmazáshoz, és ezt követően a felhasználókat újra hozzá kell rendelni az adott ADP-szolgáltatáshoz.
Az ADP-képviselőtől kapott visszaigazolás kézhezvételét követően konfigurálja az ADP-szolgáltatás(oka)t, és rendeljen hozzá/kezelje a felhasználókat az adott ADP-szolgáltatáshoz való felhasználói hozzáférés szabályozására.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből szakaszban** írja be az **ADP** kifejezést a keresőmezőbe.
1. Válassza az **ADP** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.
1. Az Azure Portalon az **ADP-alkalmazásintegrációs** lapon kattintson a **Tulajdonságok fülre,** és hajtsa végre a következő lépéseket:  

    ![Egyszeri bejelentkezéscsatolt tulajdonságok](./media/adpfederatedsso-tutorial/tutorial_adp_linkedproperties.png)

    a.  Állítsa a **felhasználók számára engedélyezve a bejelentkezési** mező értékét **Igen**értékre.

    b.  Állítsa a **Felhasználó hozzárendelés szükséges** mező értékét **Igen**értékre.

    c.  Állítsa a **Látható a felhasználókszámára** mező értékét **Igen**értékre.

1. Az [Azure Portalon](https://portal.azure.com/)az **ADP-alkalmazásintegrációs** lapon keresse meg a **Kezelés szakaszt,** és válassza **az Egyszeri bejelentkezés**lehetőséget.

1. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza a **Mód** **csatoltként**lehetőséget. az alkalmazás **ADP-hez**való csatolásához.

    ![Egyszeri bejelentkezés csatolva](./media/adpfederatedsso-tutorial/tutorial_adp_linked.png)

1. Nyissa meg a **Bejelentkezési URL konfigurálása** szakaszt, és hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezési kellék](./media/adpfederatedsso-tutorial/tutorial_adp_linkedsignon.png)

    a. Illessze be a **felhasználói hozzáférés URL-címét**, amelyet a fenti **tulajdonságok lapról** másolt (a fő ADP alkalmazásból).
                                                             
    b. Az alábbiakban az 5 alkalmazás, amely támogatja a különböző **továbbítási állapot URL-eket.** Az adott alkalmazáshoz szükséges megfelelő **továbbítási állapot URL-értékét** manuálisan kell hozzáfűzöttítenie a **felhasználói hozzáférési URL-címhez.**
    
    * **ADP munkaerő most**
        
        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?WFN`

    * **Az ADP munkaerő mostantól megnövelt idő**
        
        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?EETDC2`
    
    * **ADP Vantage HCM**
        
        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?ADPVANTAGE`

    * **ADP Vállalati HR**

        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?PORTAL`

    * **MyADP között**

        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?REDBOX`

9. **Mentse** a módosításokat.

10. Az ADP képviselőjétől kapott visszaigazolás kézhezvételét követően kezdje meg a vizsgálatot egy vagy két felhasználóval.

    a. Néhány felhasználó hozzárendelése az ADP szolgáltatásalkalmazáshoz az összevont hozzáférés teszteléséhez.

    b. A teszt akkor sikeres, ha a felhasználók hozzáférnek az ADP szolgáltatásalkalmazáshoz a katalógusban, és hozzáférhetnek az ADP-szolgáltatásukhoz.
 
11. A sikeres teszt megerősítésekor rendelje hozzá az összevont ADP-szolgáltatást az egyes felhasználókhoz vagy felhasználói csoportokhoz, amelyeket az oktatóanyag későbbi részében ismertetünk, és ki kell adni az alkalmazottaknak.

### <a name="create-adp-test-user"></a>ADP-tesztfelhasználó létrehozása

Ennek a szakasznak az a célja, hogy hozzon létre egy B.Simon nevű felhasználót az ADP-ben. Az [ADP támogatási csapatával](https://www.adp.com/contact-us/overview.aspx) együttműködve vegye fel a felhasználókat az ADP-fiókba. 

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a hozzáférési panelen az ADP csempére kattint, automatikusan be kell jelentkeznie az adp-ba, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki az ADP-t az Azure AD-vel](https://aad.portal.azure.com)

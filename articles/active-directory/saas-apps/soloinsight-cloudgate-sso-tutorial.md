---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Soloinsight-CloudGate SSO-val | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Soloinsight-CloudGate egyszeri bejelentkezés között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 9263c241-85a4-4724-afac-0351d6275958
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b88822f164e0fe8cdf55eddfa981644f725e01f3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73159931"
---
# <a name="tutorial-integrate-soloinsight-cloudgate-sso-with-azure-active-directory"></a>Oktatóanyag: A Soloinsight-CloudGate Egyszeri szolgáltatás integrálása az Azure Active Directoryval

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Soloinsight-CloudGate egyszeri bejelentkezést az Azure Active Directoryval (Azure AD). Ha integrálja a Soloinsight-CloudGate Egyszeri szolgáltatást az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Soloinsight-CloudGate Egyszeri bejelentkezéshez.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve Soloinsight-CloudGate Egyszeri bejelentkezés az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [itt](https://azure.microsoft.com/pricing/free-trial/)egy hónapos ingyenes próbaverziót kaphat.
* Soloinsight-CloudGate Egyszeri bejelentkezés (SSO) engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben. A Soloinsight-CloudGate SSO támogatja az **SP** által kezdeményezett Egyszeri erőforrást.

## <a name="adding-soloinsight-cloudgate-sso-from-the-gallery"></a>Soloinsight-CloudGate SSO hozzáadása a galériából

A Soloinsight-CloudGate SSO azure-beli AD-be való integrálásának konfigurálásához hozzá kell adnia a Soloinsight-CloudGate Egyszeri szolgáltatásokat a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a katalógusból szakaszban** írja be a **Soloinsight-CloudGate Egyszeri bejelentkezést** a keresőmezőbe.
1. Válassza a **Soloinsight-CloudGate SSO** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD SSO-t a Soloinsight-CloudGate Egyszeri szolgáltatással egy **Britta Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az Egyszeri bejelentkezés működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Soloinsight-CloudGate Egyszeri bejelentkezésben.

Az Azure AD SSO beállítása és tesztelése a Soloinsight-CloudGate Egyszeri szolgáltatással, hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD SSO-t,](#configure-azure-ad-sso)** hogy a felhasználók használhassák ezt a funkciót.
2. **[Konfigurálja a Soloinsight-CloudGate Egyszeri szolgáltatást](#configure-soloinsight-cloudgate-sso)** az alkalmazás oldali egyszeri szolgáltatás beállításainak konfigurálásához.
3. **[Hozzon létre egy Azure AD-tesztfelhasználót](#create-an-azure-ad-test-user)** az Azure AD egyszeri bejelentkezésének britta simonnal való teszteléséhez.
4. **[Rendelje hozzá az Azure AD tesztfelhasználót,](#assign-the-azure-ad-test-user)** hogy Britta Simon használhassa az Azure AD egyszeri bejelentkezést.
5. **[Hozzon létre Soloinsight-CloudGate SSO tesztfelhasználó,](#create-soloinsight-cloudgate-sso-test-user)** hogy egy megfelelője Britta Simon soloinsight-CloudGate SSO, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Tesztelje az SSO-t,](#test-sso)** hogy ellenőrizze, működik-e a konfiguráció.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Soloinsight-CloudGate egyszeri** bejelentkezés alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza az **Egyszeri bejelentkezés**lehetőséget.
1. Az **Egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** lapon adja meg a következő mezők értékeit:

    1. A Bejelentkezés az **URL-cím** mezőbe írja be az URL-címet a következő minta használatával:`https://<SUBDOMAIN>.sigateway.com/login`

    1. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<SUBDOMAIN>.sigateway.com/process/sso`

   > [!NOTE]
   > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Bejelentkezési URL-címmel és azonosítóval, amelyet később az oktatóanyag **Soloinsight-CloudGate egyszeri bejelentkezési** szakasza ismertet.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon keresse meg az **SAML aláíró tanúsítvány szakaszát,** keresse meg a **Tanúsítvány (Base64)** lehetőséget, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

   ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Soloinsight-CloudGate Egyszeri szolgáltatás** beállítása szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

   ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-soloinsight-cloudgate-sso"></a>A Soloinsight-CloudGate egyszeri szolgáltatás konfigurálása

1. A Soloinsight-CloudGate SSO konfigurációjának automatizálásához telepítenie kell a **My Apps Secure Sign-in böngészőbővítményt** **a Bővítmény telepítése**gombra kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadja a bővítményt a böngészőhöz, kattintson a **Setup Soloinsight-CloudGate SSO-ra,** amely a Soloinsight-CloudGate SSO alkalmazáshoz irányítja. Innen adja meg a rendszergazdai hitelesítő adatokat a Soloinsight-CloudGate SSO-ba való bejelentkezéshez. A böngészőbővítmény automatikusan konfigurálja az alkalmazást, és automatizálja a 3-8.

    ![Beállítási konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a Soloinsight-CloudGate SSO-t, nyisson meg egy új böngészőablakot, és jelentkezzen be rendszergazdaként a Soloinsight-CloudGate SSO vállalati webhelyére, és hajtsa végre a következő lépéseket:

4. Ha be szeretné szerezni azokat az értékeket, amelyeket az Alapszintű SAML konfigurálása során be kell illeszteni az Azure Portalra, jelentkezzen be a CloudGate webportálra a hitelesítő adataival, majd érje el az SSO-beállításokat, amelyek a következő elérési>**felügyeleti>rendszerbeállítások>általános**elérési úton találhatók.

    ![Cloudgate SSO-beállítások](./media/soloinsight-cloudgate-sso-tutorial/sso-main-settings.png)

5. **SAML fogyasztói URL-cím**

    * Másolja a **Saml Consumer URL-címére** és az **átirányítás URL-mezőire** elérhető hivatkozásokat, és illessze be őket az Azure Portal **alapszintű SAML konfigurációs** szakaszába **az azonosító (entitásazonosító)** és a **válasz URL-mezőkhöz.**

        ![SAMLIdentifier](./media/soloinsight-cloudgate-sso-tutorial/saml-identifier.png)

6. **SAML aláíró tanúsítvány**

    * Nyissa meg az Azure Portal SAML aláíró tanúsítványlistáiról letöltött tanúsítványfájl (Base64) forrását, és kattintson rá a jobb gombbal. A listából válassza a **Szerkesztés jegyzettömb++** lehetőség kiválasztását. 

        ![SAMLtanúsítvány](./media/soloinsight-cloudgate-sso-tutorial/certificate-file.png)

    * Másolja a tartaloma tanúsítvány (Base64) Jegyzettömb ++ fájl.

        ![Tanúsítvány másolása](./media/soloinsight-cloudgate-sso-tutorial/certificate-copy.png)

    * Illessze be a tartalmat a CloudGate Web Portal SSO beállítások **tanúsítvány mezőjébe,** és kattintson a Mentés gombra.

        ![Tanúsítványportál](./media/soloinsight-cloudgate-sso-tutorial/certificate-portal.png)

7. **Alapértelmezett csoport**

    * Válassza az **Üzleti rendszergazda** lehetőséget a CloudGate webportál **Alapértelmezett csoport** beállításának legördülő listájából

        ![Alapértelmezett csoport](./media/soloinsight-cloudgate-sso-tutorial/default-group.png)

8. **AD-azonosító és bejelentkezési URL**

    * A másolt **bejelentkezési URL-t** az Azure Portal **on Set up Soloinsight-CloudGate Egyszeri** bejelentkezés konfigurációk kell adni a CloudGate WebPortal Egyszeri bejelentkezés beállításait szakaszban.

    * Illessze be a **bejelentkezési URL-címet** az Azure Portalról a CloudGate Webportál **AD bejelentkezési URL-címe** mezőbe.

    * Az **Azure AD-azonosító** hivatkozás beillesztése az Azure Portalról a CloudGate webportál **AD-azonosító mezőjébe**

        ![Hirdetés bejelentkezés](./media/soloinsight-cloudgate-sso-tutorial/ad-login.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy tesztfelhasználót hoz létre az Azure Portalon Britta Simon néven.

1. Az Azure Portal bal oldali ablaktáblájában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd válassza az **Összes felhasználó**lehetőséget.
1. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.
1. A **Felhasználói** tulajdonságok csoportban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `Britta Simon`.  
   1. A **Felhasználónév** mezőbe írja username@companydomain.extensionbe a mezőt. Például: `BrittaSimon@contoso.com`.
   1. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet, majd írja le a **Jelszó** mezőben megjelenő értéket.
   1. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi Britta Simon számára az Azure egyszeri bejelentkezést a Soloinsight-CloudGate Egyszeri bejelentkezés hez való hozzáférés biztosításával.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Soloinsight-CloudGate SSO**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a Felhasználó lista **Britta Simon** elemet, majd kattintson a képernyő alján található **Kijelölés** gombra.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-soloinsight-cloudgate-sso-test-user"></a>Soloinsight-CloudGate SSO tesztfelhasználó létrehozása

Tesztfelhasználó létrehozásához válassza ki az **Alkalmazottak lehetőséget** a CloudGate webportál főmenüjéből, és töltse ki az Új alkalmazott hozzáadása űrlapot. A hatóság szintje, hogy hozzá kell rendelni a teszt felhasználó **Üzleti Admin** Kattintson a **Create,** ha az összes szükséges mezőket kitöltötte.

![Alkalmazotti teszt](./media/soloinsight-cloudgate-sso-tutorial/employee-test.png)

### <a name="test-sso"></a>SSO tesztelése

Ha kiválasztja a Soloinsight-CloudGate SSO csempe a hozzáférési panelen, akkor automatikusan be kell jelentkeznie a Soloinsight-CloudGate Egyszeri bejelentkezés, amelyhez beállítja sso. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
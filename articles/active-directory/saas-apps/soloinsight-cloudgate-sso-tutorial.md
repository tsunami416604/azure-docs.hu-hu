---
title: 'Oktatóanyag: Azure Active Directory integráció a Soloinsight-CloudGate SSO-val | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Soloinsight – CloudGate SSO között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/06/2019
ms.author: jeedes
ms.openlocfilehash: 5ac0f0777ea341036950550e19c5d8e7fb71a91f
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88545063"
---
# <a name="tutorial-integrate-soloinsight-cloudgate-sso-with-azure-active-directory"></a>Oktatóanyag: a Soloinsight-CloudGate SSO integrálása Azure Active Directory

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Soloinsight-CloudGate SSO-t Azure Active Directory (Azure AD) használatával. Ha integrálja az Soloinsight-CloudGate SSO-t az Azure AD-vel, a következőket teheti:

* Vezérlés az Azure AD-ben, aki hozzáfér a Soloinsight-CloudGate SSO-hoz.
* Engedélyezze, hogy a felhasználók automatikusan bejelentkezzenek a Soloinsight-CloudGate SSO-ba az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [itt](https://azure.microsoft.com/pricing/free-trial/)kérhet egy hónapos ingyenes próbaverziót.
* Soloinsight – CloudGate SSO egyszeri bejelentkezés (SSO) engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben. Az Soloinsight-CloudGate SSO támogatja az **SP** által kezdeményezett egyszeri bejelentkezést.

## <a name="adding-soloinsight-cloudgate-sso-from-the-gallery"></a>Soloinsight-CloudGate SSO hozzáadása a katalógusból

A Soloinsight-CloudGate SSO Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Soloinsight-CloudGate SSO-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **SOLOINSIGHT-CloudGate SSO** kifejezést a keresőmezőbe.
1. Válassza az **Soloinsight-CLOUDGATE SSO** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Az Azure AD SSO konfigurálása és tesztelése a Soloinsight-CloudGate SSO használatával a **Britta Simon**nevű teszt felhasználóval. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Soloinsight-CloudGate SSO-ban.

Az Azure AD SSO és a Soloinsight-CloudGate SSO konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure ad SSO](#configure-azure-ad-sso)** -t, hogy a felhasználók használhatják ezt a funkciót.
2. **[Konfigurálja az Soloinsight-CLOUDGATE SSO-](#configure-soloinsight-cloudgate-sso)** t az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalán.
3. **[Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user)** az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** , hogy a Britta Simon engedélyezze az Azure ad egyszeri bejelentkezés használatát.
5. **[Hozzon létre Soloinsight-CLOUDGATE SSO-teszt felhasználót](#create-soloinsight-cloudgate-sso-test-user)** , hogy rendelkezzen a Britta Simon in Soloinsight-CloudGate SSO-vel, amely a felhasználó Azure ad-ábrázolásához van csatolva.
6. Ellenőrizze az **[SSO](#test-sso)** -t annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **SOLOINSIGHT-CloudGate SSO-** alkalmazás integrációja lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** lapon adja meg a következő mezők értékeit:

    1. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<SUBDOMAIN>.sigateway.com/login`

    1. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<SUBDOMAIN>.sigateway.com/process/sso`

   > [!NOTE]
   > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval, amelyet később az oktatóanyag **Soloinsight-CLOUDGATE SSO egyszeri bejelentkezés konfigurálása** című szakaszában talál.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

   ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Soloinsight-CLOUDGATE SSO beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmény alapján.

   ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-soloinsight-cloudgate-sso"></a>Soloinsight-CloudGate SSO konfigurálása

1. A Soloinsight-CloudGate SSO-n belüli konfiguráció automatizálásához telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése**lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **beállítás Soloinsight – a CLOUDGATE SSO** a Soloinsight-CloudGate SSO-alkalmazásra irányítja át. Itt adja meg a rendszergazdai hitelesítő adatokat a Soloinsight-CloudGate SSO-ba való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-8-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a Soloinsight-CloudGate SSO-t, nyisson meg egy új böngészőablakot, és jelentkezzen be a Soloinsight-CloudGate SSO vállalati webhelyre rendszergazdaként, és hajtsa végre a következő lépéseket:

4. Ha az alapszintű SAML konfigurálása során be szeretné illeszteni a Azure Portalbe beillesztett értékeket, jelentkezzen be a CloudGate webes portálra a hitelesítő adataival, majd nyissa meg az egyszeri bejelentkezés beállításait, amely a következő Path **Home>felügyeleti>rendszerbeállítások>általános elemnél**található.

    ![CloudGate SSO-beállítások](./media/soloinsight-cloudgate-sso-tutorial/sso-main-settings.png)

5. **SAML-fogyasztói URL-cím**

    * Másolja az elérhető hivatkozásokat a **SAML-fogyasztó URL-címére** és az **átirányítási URL** -címekre, és illessze be őket a Azure Portal **alapszintű SAML-konfigurációs** szakaszba az **azonosító (Entity ID)** és a **Válasz URL-címek** mezőihez

        ![SAMLIdentifier](./media/soloinsight-cloudgate-sso-tutorial/saml-identifier.png)

6. **SAML-aláíró tanúsítvány**

    * Keresse meg a tanúsítvány (Base64) Azure Portal SAML-aláíró tanúsítvány listájáról letöltött forrását, és kattintson rá a jobb gombbal. A listáról válassza a **Szerkesztés a Jegyzettömb + +** lehetőséggel lehetőséget. 

        ![SAMLcertificate](./media/soloinsight-cloudgate-sso-tutorial/certificate-file.png)

    * Másolja a tartalmat a tanúsítvány (Base64) Jegyzettömb + + fájljába.

        ![Tanúsítvány másolása](./media/soloinsight-cloudgate-sso-tutorial/certificate-copy.png)

    * Illessze be a tartalmat a CloudGate web Portal SSO-beállítások **tanúsítvány** mezőjébe, és kattintson a Save (Mentés) gombra.

        ![Tanúsítvány-portál](./media/soloinsight-cloudgate-sso-tutorial/certificate-portal.png)

7. **Alapértelmezett csoport**

    * Válassza az **üzleti rendszergazda** lehetőséget a CloudGate webes portálon az **alapértelmezett csoport** lehetőség legördülő listájában.

        ![Alapértelmezett csoport](./media/soloinsight-cloudgate-sso-tutorial/default-group.png)

8. **AD-azonosító és bejelentkezési URL-cím**

    * Az **Soloinsight-CLOUDGATE SSO-konfigurációk beállításakor** használt **bejelentkezési URL** -Azure Portal címet a CloudGate web Portal SSO-beállítások szakaszában kell megadni.

    * Illessze be a **bejelentkezési URL-** hivatkozást Azure Portal a CloudGate web Portal **ad login URL-címe** mezőbe.

    * Illessze be az **Azure ad-azonosító** hivatkozást Azure Portal a CloudGate webes portál **ad-azonosító** mezőjébe

        ![Ad-bejelentkezés](./media/soloinsight-cloudgate-sso-tutorial/ad-login.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `Britta Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `BrittaSimon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést az Soloinsight-CloudGate SSO hozzáférésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza az **Soloinsight-CLOUDGATE SSO**elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-soloinsight-cloudgate-sso-test-user"></a>Soloinsight-CloudGate SSO-teszt felhasználó létrehozása

Egy teszt felhasználó létrehozásához válassza az **alkalmazottak** elemet a CloudGate webportál főmenüjében, és töltse ki az új alkalmazott hozzáadása űrlapot. A tesztelési felhasználóhoz hozzárendelni kívánt szolgáltatói szint az **üzleti rendszergazda** elemre kattintva az összes kötelező mező kitöltése után kattintson a **Létrehozás** gombra.

![Alkalmazotti teszt](./media/soloinsight-cloudgate-sso-tutorial/employee-test.png)

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Amikor kiválasztja a Soloinsight-CloudGate SSO csempét a hozzáférési panelen, automatikusan be kell jelentkeznie a Soloinsight-CloudGate SSO-ba, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
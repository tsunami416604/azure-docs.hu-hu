---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a ServiceNow | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és ServiceNow között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: a5a1a264-7497-47e7-b129-a1b5b1ebff5b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 431d20c4c5ae5355d456ca3453b832e590cbb199
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69558980"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-servicenow"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a ServiceNow

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a ServiceNow a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az ServiceNow-t az Azure AD-vel, a következőket teheti:

* A ServiceNow-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a ServiceNow az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* ServiceNow egyszeri bejelentkezés (SSO) engedélyezett előfizetése.
* ServiceNow, a ServiceNow, a Calgary vagy a magasabb verziójának példánya vagy bérlője
* A ServiceNow Express esetében a ServiceNow Express, Helsinki vagy újabb verziójának példánya
* Az ServiceNow-bérlőnek engedélyezve kell lennie a [több szolgáltató egyszeri bejelentkezés beépülő moduljának](https://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) . Ezt [egy szolgáltatási kérelem](https://hi.service-now.com)elküldésével teheti meg.
* Az automatikus konfiguráláshoz engedélyezze a többszörös szolgáltató beépülő modult a ServiceNow.
* A klasszikus ServiceNow (Mobile) alkalmazás telepítéséhez lépjen a megfelelő áruházba, és keresse meg a klasszikus ServiceNow alkalmazást, és kattintson a letöltés gombra.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben. A ServiceNow támogatja az **SP** által kezdeményezett egyszeri bejelentkezést, és támogatja a [felhasználók **automatikus** kiépítési](servicenow-provisioning-tutorial.md)felállítását.

A ServiceNow klasszikus (mobil) alkalmazás mostantól konfigurálható az Azure AD-vel az egyszeri bejelentkezés engedélyezéséhez, és az **Android** és az **iOS** rendszerű felhasználókat is támogatja. Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

## <a name="adding-servicenow-from-the-gallery"></a>ServiceNow hozzáadása a gyűjteményből

A ServiceNow Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a ServiceNow a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **ServiceNow** kifejezést a keresőmezőbe.
1. Válassza ki a **ServiceNow** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-servicenow"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a ServiceNow

Konfigurálja és tesztelje az Azure AD SSO-t a ServiceNow a **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a ServiceNow-ben.

Az Azure AD SSO és a ServiceNow konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure ad SSO](#configure-azure-ad-sso)** -t, hogy a felhasználók használhatják ezt a funkciót.
    1. **[Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user)** az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** , hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
    1. Az **[Azure ad SSO konfigurálása a ServiceNow Express számára](#configure-azure-ad-sso-for-servicenow-express)** – a funkció használatának engedélyezése a felhasználók számára.
2. **[Konfigurálja a ServiceNow](#configure-servicenow)** az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalán.
    1. **[Hozzon létre ServiceNow-tesztelési felhasználót](#create-servicenow-test-user)** , hogy a ServiceNow B. Simon párja legyen, amely a felhasználó Azure ad-képviseletéhez van csatolva.
    1. **[ServiceNow expressz egyszeri bejelentkezés konfigurálása](#configure-servicenow-express-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.    
3. Ellenőrizze az **[SSO](#test-sso)** -t annak ellenőrzéséhez, hogy a konfiguráció működik-e.
4. Tesztelje az SSO-t **[a klasszikus ServiceNow (Mobile)](#test-sso-for-servicenow-classic-mobile)** annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **ServiceNow** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az alapszintű **SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az alapszintű **SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<instance-name>.service-now.com/navpage.do`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<instance-name>.service-now.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval kell frissíteni, amelyet az oktatóanyag későbbi részében ismertetünk. Az Azure Portal alapszintű **SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

   ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

   a. Kattintson a Másolás gombra az **alkalmazás-összevonási metaadatok URL-címének** másolásához és a Jegyzettömbbe való beillesztéséhez, mivel ez az alkalmazás-összevonási metaadatok URL-címe az oktatóanyag későbbi részében lesz felhasználva.

    b. Kattintson a **Letöltés** gombra a **tanúsítvány letöltéséhez (Base64)** , majd mentse a tanúsítványt a számítógépre.

1. A **ServiceNow beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

   ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza ki **új felhasználó** a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőben adja meg a username@companydomain.extensionnevet. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a ServiceNow.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **ServiceNow**lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="configure-azure-ad-sso-for-servicenow-express"></a>Az Azure AD SSO konfigurálása a ServiceNow expresshez

1. A [Azure Portal](https://portal.azure.com/) **ServiceNow** alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** ikonra az alapszintű **SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az alapszintű **SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<instance-name>.service-now.com/navpage.do`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<instance-name>.service-now.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval kell frissíteni, amelyet az oktatóanyag későbbi részében ismertetünk. Az Azure Portal alapszintű **SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

6. Az Azure AD-ben az SAML-alapú hitelesítéshez a ServiceNow automatikus konfigurálását biztosító ServiceNow-szolgáltatás konfigurálása egy kattintással. A szolgáltatás engedélyezéséhez lépjen a **ServiceNow beállítása** szakaszra, és kattintson a **részletes utasítások megtekintése** lehetőségre a bejelentkezés konfigurálása ablak megnyitásához.

    ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

7. Adja meg a ServiceNow-példány nevét, a rendszergazdai felhasználónevet és a rendszergazdai jelszót a **bejelentkezési űrlap konfigurálása** lapon, majd kattintson a **Konfigurálás most**lehetőségre. Győződjön meg arról, hogy a megadott rendszergazdai felhasználónévnek hozzá kell rendelnie a **security_admin** szerepkört a ServiceNow-ben ahhoz, hogy működjön. Ha nem szeretné manuálisan konfigurálni az Azure AD-t SAML-ServiceNow, kattintson az **egyszeri bejelentkezés manuális konfigurálása** elemre, és másolja a **KIjelentkezési URL-címet, az Azure ad-azonosítót és a bejelentkezési URL-címet** a rövid útmutató szakaszban.

    ![Alkalmazás URL-címének konfigurálása](./media/servicenow-tutorial/configure.png "Alkalmazás URL-címének konfigurálása")

## <a name="configure-servicenow"></a>ServiceNow konfigurálása

1. Jelentkezzen be a ServiceNow alkalmazásba rendszergazdaként.

2. Aktiválja az **integrációt – több szolgáltató egyszeri bejelentkezési telepítő** beépülő modulját a következő lépésekkel:

    a. A bal oldali navigációs ablaktáblán a keresősáv alatt keresse meg a rendszerdefiníció szakaszt, majd kattintson a **plugins**elemre.

    ![Beépülő modul aktiválása](./media/servicenow-tutorial/tutorial_servicenow_03.png "Beépülő modul aktiválása")

    b. Integráció keresése **– több szolgáltató egyszeri bejelentkezési telepítője**.

     ![Beépülő modul aktiválása](./media/servicenow-tutorial/tutorial_servicenow_04.png "Beépülő modul aktiválása")

    c. Válassza ki a beépülő modult. Kattintson a jobb gombbal, majd válassza az **aktiválás/frissítés**lehetőséget.

     ![Beépülő modul aktiválása](./media/servicenow-tutorial/tutorial_activate.png "Beépülő modul aktiválása")

    d. Kattintson az **aktiválás** gombra.

     ![Beépülő modul aktiválása](./media/servicenow-tutorial/tutorial_activate1.png "Beépülő modul aktiválása")

3. A bal oldali navigációs ablaktáblán keresse meg a többszolgáltatós **egyszeri bejelentkezés** szakaszt a keresősáv alatt, majd kattintson a **Tulajdonságok**elemre.

    ![Alkalmazás URL-címének konfigurálása](./media/servicenow-tutorial/tutorial_servicenow_06.png "Alkalmazás URL-címének konfigurálása")

4. A **több szolgáltató egyszeri bejelentkezés tulajdonságai** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![Alkalmazás URL-címének konfigurálása](./media/servicenow-tutorial/ic7694981.png "Alkalmazás URL-címének konfigurálása")

    * **Több szolgáltató egyszeri bejelentkezésének engedélyezése**esetén válassza az **Igen**lehetőséget.
  
    * A **felhasználók automatikus importálásának engedélyezése az összes identitás-szolgáltatótól a felhasználói táblába válassza az** **Igen**lehetőséget.

    * **A több szolgáltató egyszeri bejelentkezéses integrációjának hibakeresési naplózásának engedélyezéséhez válassza az** **Igen**lehetőséget.

    * A **felhasználó tábla...** szövegmező mezőjében írja be a következőt: Felhasználónév.
  
    * Kattintson a **Save** (Mentés) gombra.

5. A **ServiceNow** két módon konfigurálható – automatikus és manuális.

6. A **ServiceNow** automatikus konfigurálásához kövesse az alábbi lépéseket:

    * Térjen vissza a Azure Portal **ServiceNow** egyszeri bejelentkezés lapjára.

    * Az Azure AD-ben az SAML-alapú hitelesítéshez a ServiceNow automatikus konfigurálását biztosító ServiceNow-szolgáltatás konfigurálása egy kattintással. A szolgáltatás engedélyezéséhez lépjen a **ServiceNow-konfiguráció** szakaszra, kattintson a **ServiceNow konfigurálása** elemre a bejelentkezési ablak konfigurálásának megnyitásához.

        ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

    * Adja meg a ServiceNow-példány nevét, a rendszergazdai felhasználónevet és a rendszergazdai jelszót a **bejelentkezési űrlap konfigurálása** lapon, majd kattintson a **Konfigurálás most**lehetőségre. Győződjön meg arról, hogy a megadott rendszergazdai felhasználónévnek hozzá kell rendelnie a **security_admin** szerepkört a ServiceNow-ben ahhoz, hogy működjön. Ha nem szeretné manuálisan konfigurálni az Azure AD-t SAML-ServiceNow, kattintson az **egyszeri bejelentkezés manuális konfigurálása** elemre, és másolja ki a kijelentkezési **URL-címet, az SAML-entitás azonosítóját és az SAML egyszeri bejelentkezési szolgáltatás URL-címét** a gyors útmutató szakaszban.

        ![Alkalmazás URL-címének konfigurálása](./media/servicenow-tutorial/configure.png "Alkalmazás URL-címének konfigurálása")

    * Jelentkezzen be a ServiceNow alkalmazásba rendszergazdaként.

    * Az automatikus konfigurációban az összes szükséges beállítás a **ServiceNow** oldalon van konfigurálva, de az **X. 509 tanúsítvány** alapértelmezés szerint nincs engedélyezve. A ServiceNow-ben manuálisan kell leképeznie az identitás-szolgáltatónak. Kövesse az alábbi lépéseket ugyanezen:

    * A bal oldali navigációs ablaktáblán keresse meg a többszolgáltatós **egyszeri bejelentkezés** szakaszt a keresősáv alatt, majd kattintson az **identitás-szolgáltatók**elemre.

        ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/tutorial_servicenow_07.png "Egyszeri bejelentkezés konfigurálása")

    * Kattintson az automatikusan generált identitás-szolgáltatóra

        ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/tutorial_servicenow_08.png "Egyszeri bejelentkezés konfigurálása")

    *  Az **identitás-szolgáltató** szakaszban hajtsa végre a következő lépéseket:

        ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/automatic_config.png "Egyszeri bejelentkezés konfigurálása")

        * A **név** szövegmezőbe írja be a konfiguráció nevét (például **Microsoft Azure összevont egyszeri bejelentkezés**).

        * Távolítsa el a feltöltött **azonosító szolgáltató SingleLogoutRequest** értékét a szövegmezőből.

        * Másolja a **ServiceNow-Kezdőlap** értékét, illessze be a ServiceNow alapszintű SAML- **konfiguráció** szakaszának **bejelentkezési URL-címe** szövegmezőbe Azure Portal.

            > [!NOTE]
            > A ServiceNow-példány kezdőlapja a **ServiceNow-bérlő URL** -címének és **/navpage.do** összefűzése`https://fabrikam.service-now.com/navpage.do`(például:).

        * Másolja az **entitás azonosítója/kiállítója** értéket, illessze be az **azonosító** szövegmezőbe a SERVICENOW alapszintű **SAML-konfiguráció** szakaszának Azure Portal.

        * Győződjön meg arról, hogy a **NameID szabályzat** `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` értéke értékre van állítva. 

    * Görgessen le az **X. 509 tanúsítvány** szakaszhoz, majd válassza a **Szerkesztés**lehetőséget.

        ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/tutorial_servicenow_09.png "Egyszeri bejelentkezés konfigurálása")

    * Válassza ki a tanúsítványt, majd kattintson a jobbra mutató nyíl ikonra a tanúsítvány hozzáadásához.

        ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/tutorial_servicenow_11.png "Egyszeri bejelentkezés konfigurálása")

    * Kattintson a **Save** (Mentés) gombra.

    * Kattintson a lap jobb felső sarkában látható **kapcsolatok tesztelése** gombra.

        ![Beépülő modul aktiválása](./media/servicenow-tutorial/tutorial_activate2.png "Beépülő modul aktiválása")

    * Miután rákattintotta tesztre, megjelenik az előugró ablak, ahol meg kell adnia a hitelesítő adatokat, és az alábbi oldalon az eredmények láthatók. Az **SSO** -kijelentkezési A teszt eredményei hiba várható, kérjük, hagyja figyelmen kívül a hibát, és kattintson az **aktiválás** gombra.

        ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/servicenowactivate.png "Egyszeri bejelentkezés konfigurálása")
  
7. A **ServiceNow** manuális konfigurálásához kövesse az alábbi lépéseket:

    * Jelentkezzen be a ServiceNow alkalmazásba rendszergazdaként.

    * A bal oldali navigációs ablaktáblán kattintson az identitás- **szolgáltatók**elemre.

        ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/tutorial_servicenow_07.png "Egyszeri bejelentkezés konfigurálása")

    * Az **Identity Providers** párbeszédpanelen kattintson az **új**elemre.

        ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/ic7694977.png "Egyszeri bejelentkezés konfigurálása")

    * Az **Identity Providers** párbeszédpanelen kattintson az **SAML**elemre.

        ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/ic7694978.png "Egyszeri bejelentkezés konfigurálása")

    * Az **Identity Provider metaadatainak importálása** felugró ablakban végezze el a következő lépéseket:

        ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/idp.png "Egyszeri bejelentkezés konfigurálása")

        * Adja meg az **alkalmazás-összevonási metaadatok URL-címét** , amelyet a Azure Portalból másolt.

        * Kattintson az **Importálás** elemre.

    * Beolvassa a identitásszolgáltató metaadat-URL-címét, és feltölti az összes mező információit.

        ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/ic7694982.png "Egyszeri bejelentkezés konfigurálása")

        * A **név** szövegmezőbe írja be a konfiguráció nevét (például **Microsoft Azure összevont egyszeri bejelentkezés**).

        * Távolítsa el a feltöltött **azonosító szolgáltató SingleLogoutRequest** értékét a szövegmezőből.

        * Másolja a **ServiceNow-Kezdőlap** értékét, illessze be a ServiceNow alapszintű SAML- **konfiguráció** szakaszának **bejelentkezési URL-címe** szövegmezőbe Azure Portal.

            > [!NOTE]
            > A ServiceNow-példány kezdőlapja a **ServiceNow-bérlő URL** -címének és **/navpage.do** összefűzése`https://fabrikam.service-now.com/navpage.do`(például:).

        * Másolja az **entitás azonosítója/kiállítója** értéket, illessze be az **azonosító** szövegmezőbe a SERVICENOW alapszintű **SAML-konfiguráció** szakaszának Azure Portal.

        * Győződjön meg arról, hogy a **NameID szabályzat** `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` értéke értékre van állítva.

        * Kattintson a **speciális**gombra. A **felhasználói mező** szövegmezőbe írja be az **E-mail** vagy a Felhasználónév értéket attól függően, hogy melyik mezőt használja a ServiceNow-telepítésben lévő felhasználók egyedi azonosítására.

            > [!NOTE]
            > Az Azure AD-t beállíthatja úgy, hogy az Azure AD felhasználói AZONOSÍTÓját (egyszerű felhasználónév) vagy az e-mail-címet egyedi azonosítóként adja ki az SAML-jogkivonatban úgy, hogy a **ServiceNow > attribútumokat > egyszeri bejelentkezési** szakaszt, a Azure Portal és a leképezést a **NameIdentifier** attribútum kívánt mezője. Az Azure AD kiválasztott attribútumának (például az egyszerű felhasználónév) értékének meg kell egyeznie a megadott mező ServiceNow tárolt értékével (például Felhasználónév).

        * Kattintson a lap jobb felső sarkában látható **kapcsolatok tesztelése** gombra.

        * Miután rákattintotta tesztre, megjelenik az előugró ablak, ahol meg kell adnia a hitelesítő adatokat, és az alábbi oldalon az eredmények láthatók. Az **SSO** -kijelentkezési A teszt eredményei hiba várható, kérjük, hagyja figyelmen kívül a hibát, és kattintson az **aktiválás** gombra.

          ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/servicenowactivate.png "Egyszeri bejelentkezés konfigurálása")

### <a name="create-servicenow-test-user"></a>ServiceNow-tesztelési felhasználó létrehozása

Ennek a szakasznak a célja egy Britta Simon nevű felhasználó létrehozása a ServiceNow-ben. A ServiceNow támogatja az automatikus felhasználó-kiépítés használatát, amely alapértelmezés szerint engedélyezve van. További részletekért tekintse [](servicenow-provisioning-tutorial.md) meg az automatikus felhasználó-kiépítés konfigurálását ismertető témakört.

> [!NOTE]
> Ha manuálisan kell létrehoznia egy felhasználót, fel kell vennie a kapcsolatot a [ServiceNow-ügyfél támogatási csoportjával](https://www.servicenow.com/support/contact-support.html) .

### <a name="configure-servicenow-express-sso"></a>ServiceNow expressz SSO konfigurálása

1. Jelentkezzen be a ServiceNow Express alkalmazásba rendszergazdaként.

2. A bal oldali navigációs ablaktáblán kattintson az **egyszeri bejelentkezés**elemre.

    ![Alkalmazás URL-címének konfigurálása](./media/servicenow-tutorial/ic7694980ex.png "Alkalmazás URL-címének konfigurálása")

3. Az **egyszeri bejelentkezés** párbeszédpanelen kattintson a jobb felső sarokban található konfiguráció ikonra, és állítsa be a következő tulajdonságokat:

    ![Alkalmazás URL-címének konfigurálása](./media/servicenow-tutorial/ic7694981ex.png "Alkalmazás URL-címének konfigurálása")

    a. Váltás a **több szolgáltató egyszeri bejelentkezésének engedélyezése** a jobb oldalon.

    b. A **többszörös szolgáltató egyszeri bejelentkezéses integrációjának hibakeresési naplózásának engedélyezése** a jobb oldalon.

    c. A **felhasználó tábla...** szövegmező mezőjében írja be a következőt: Felhasználónév.

4. Az **egyszeri bejelentkezés** párbeszédpanelen kattintson az **új tanúsítvány hozzáadása**lehetőségre.

    ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/ic7694973ex.png "Egyszeri bejelentkezés konfigurálása")

5. Az **X. 509 tanúsítványok** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/ic7694975.png "Egyszeri bejelentkezés konfigurálása")

    a. A **név** szövegmezőbe írja be a konfiguráció nevét (például: **TestSAML2.0**).

    b. Válassza az **aktív**lehetőséget.

    c. **Formátumként**válassza a **PEM**lehetőséget.

    d. A **Típus mezőben**válassza a **megbízható tároló-tanúsítvány**lehetőséget.

    e. Nyissa meg Azure Portal a Jegyzettömbben letöltött Base64-kódolású tanúsítványt, másolja a vágólapra a tartalmát, majd illessze be a **PEM-tanúsítvány** szövegmezőbe.

    f. Kattintson a **frissítés** gombra

6. Az **egyszeri bejelentkezés** párbeszédpanelen kattintson az **új identitásszolgáltató hozzáadása**lehetőségre.

    ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/ic7694976ex.png "Egyszeri bejelentkezés konfigurálása")

7. Az **új identitás-szolgáltató hozzáadása** párbeszédpanelen az **Identitáskezelő konfigurálása**területen hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/ic7694982ex.png "Egyszeri bejelentkezés konfigurálása")

    a. A **név** szövegmezőbe írja be a konfiguráció nevét (például: **SAML 2.0**).

    b. Az **identitás-szolgáltató URL-címe** mezőben illessze be a Azure Portalból másolt **Identity Provider ID**értékét.

    c. Az **Identity Provider AuthnRequest** mezőjébe illessze be a **hitelesítési kérelem URL**-címének értékét, amelyet a Azure Portalból másolt.

    d. Az **Identity Provider SingleLogoutRequest** mezőjébe illessze be a kijelentkezési **URL-cím**értékét, amelyet a Azure Portalből másolt.

    e. **Identitás**-szolgáltatói tanúsítványként válassza ki az előző lépésben létrehozott tanúsítványt.

8. Kattintson a **Speciális beállítások**elemre, majd a **további identitás-szolgáltató tulajdonságai**területen hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/ic7694983ex.png "Egyszeri bejelentkezés konfigurálása")

    a. A **identitásszolgáltató SingleLogoutRequest** szövegmezőbe írja be a következőt: **urn: Oasis: nevek: TC: SAML: 2.0: kötések: http-átirányítás**.

    b. A **NameID házirend** szövegmezőbe írja be az **urn: Oasis: Names: TC: SAML: 1.1: NameID-Format: unmegadott**értéket.

    c. A **AuthnContextClassRef metódusban**írja be `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`a következőt:.

    d. Törölje **a AuthnContextClass létrehozása**elemet.

9. A **további szolgáltatói tulajdonságok**területen hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/ic7694984ex.png "Egyszeri bejelentkezés konfigurálása")

    a. A **ServiceNow Kezdőlap** szövegmezőbe írja be a ServiceNow-példány kezdőlapjának URL-címét.

    > [!NOTE]
    > A ServiceNow-példány kezdőlapja a **ServiceNow-bérlő URL** -címének és **/navpage.do** összefűzése `https://fabrikam.service-now.com/navpage.do`(például:).

    b. Az **entitás azonosítója/kiállító** szövegmezőbe írja be az ServiceNow-bérlő URL-címét.

    c. A **célközönség URI** szövegmezőbe írja be az ServiceNow-bérlő URL-címét.

    d. Az **óra-döntés** szövegmezőbe írja be a **60**értéket.

    e. A **felhasználói mező** szövegmezőbe írja be az **E-mail** vagy a Felhasználónév értéket attól függően, hogy melyik mezőt használja a ServiceNow-telepítésben lévő felhasználók egyedi azonosítására.

    > [!NOTE]
    > Az Azure AD-t beállíthatja úgy, hogy az Azure AD felhasználói AZONOSÍTÓját (egyszerű felhasználónév) vagy az e-mail-címet egyedi azonosítóként adja ki az SAML-jogkivonatban úgy, hogy a **ServiceNow > attribútumokat > egyszeri bejelentkezési** szakaszt, a Azure Portal és a leképezést a **NameIdentifier** attribútum kívánt mezője. Az Azure AD kiválasztott attribútumának (például az egyszerű felhasználónév) értékének meg kell egyeznie a megadott mező ServiceNow tárolt értékével (például Felhasználónév).

    f. Kattintson a **Save** (Mentés) gombra.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Amikor kiválasztja a ServiceNow csempét a hozzáférési panelen, automatikusan be kell jelentkeznie arra a ServiceNow, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="test-sso-for-servicenow-classic-mobile"></a>SSO tesztelése a klasszikus ServiceNow (mobil)

1. Nyissa meg a **ServiceNow klasszikus (mobil)** alkalmazását, és hajtsa végre a következő lépéseket:

    a. Kattintson a **Hozzáadás** szimbólumra a képernyő alatt.

    ![A bejelentkezés](./media/servicenow-tutorial/test03.png)

    b. Írja be a ServiceNow-példány nevét, és kattintson a **Folytatás**gombra.

    ![A bejelentkezés](./media/servicenow-tutorial/test04.png)

    c. A **Bejelentkezés** képernyőn hajtsa végre a következő lépéseket:

    ![A bejelentkezés](./media/servicenow-tutorial/test01.png)

    *  Írjon be egy B.simon@contoso.comnevet, például:.

    *  Kattintson a **külső bejelentkezés használata** lehetőségre, és a rendszer átirányítja az Azure ad-lapra a bejelentkezéshez.
    
    *  Adja meg a hitelesítő adatait, és ha van harmadik féltől származó hitelesítés vagy bármely más biztonsági funkció, a felhasználónak ennek megfelelően kell válaszolnia, és az alkalmazás kezdőlapja az alábbi módon jelenik meg:

        ![A Kezdőlap](./media/servicenow-tutorial/test02.png)

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [A felhasználók üzembe helyezésének konfigurálása](servicenow-provisioning-tutorial.md)

- [A ServiceNow kipróbálása az Azure AD-vel](https://aad.portal.azure.com)
---
title: 'Oktatóanyag: A servicenow segítségével az Azure Active Directory-integráció |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a ServiceNow között.
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
ms.date: 06/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ba516aa2c3d2decaa4962f1ccd0394ebe9a4a62
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706131"
---
# <a name="tutorial-integrate-servicenow-with-azure-active-directory"></a>Oktatóanyag: ServiceNow integrálása az Azure Active Directoryval

Ebben az oktatóanyagban elsajátíthatja a ServiceNow integrálása az Azure Active Directory (Azure AD) lesz. A ServiceNow és az Azure AD integrálása, akkor a következőket teheti:

* Szabályozza, ki férhet hozzá a ServiceNow Azure AD-ben.
* Engedélyezze a felhasználóknak, hogy lehet automatikusan bejelentkezett a servicenow hoz az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Előfeltételek

Első lépésként szüksége van a következő elemek:

* Az Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, hozzájuthat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* A ServiceNow egyszeri bejelentkezéses (SSO) az előfizetés engedélyezve van.
* ServiceNow, egy példány vagy a bérlő ServiceNow, Calgary verzió vagy újabb
* ServiceNow expressz, egy példányát a ServiceNow kifejezett, Helsinki verzió vagy újabb
* A ServiceNow-bérlőnek kell rendelkeznie a [több szolgáltató egyszeri bejelentkezést a beépülő modul](https://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) engedélyezve van. Ezt teheti [szolgáltatási kérelem elküldése](https://hi.service-now.com).
* Az automatikus konfiguráláshoz a többszolgáltatós beépülő modul engedélyezése servicenow-hoz készült.
* Telepítenie kell a megfelelő áruházból, és keresse meg a ServiceNow klasszikus alkalmazás megnyitásához, és kattintson a ServiceNow klasszikus (mobil) alkalmazása töltse le.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD SSO-t egy tesztkörnyezetben. Támogatja a ServiceNow **SP** által kezdeményezett egyszeri Bejelentkezést, és támogatja a [ **automatikus** felhasználókiépítés](servicenow-provisioning-tutorial.md).

ServiceNow klasszikus (mobil) alkalmazások is lehet konfigurálni az Azure AD egyszeri bejelentkezés engedélyezéséhez szükséges, valamint támogatja az mindkét **Android** és **IOS** felhasználók. Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD SSO-t egy tesztkörnyezetben.

## <a name="adding-servicenow-from-the-gallery"></a>A ServiceNow hozzáadása a katalógusból

Konfigurálhatja az Azure AD integrálása a ServiceNow, hozzá kell ServiceNow a galériából a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs ablaktáblán válassza ki a **Azure Active Directory** szolgáltatás.
1. Navigáljon a **vállalati alkalmazások** majd **minden alkalmazás**.
1. Új alkalmazás hozzáadásához válassza **új alkalmazás**.
1. Az a **Hozzáadás a katalógusból** területén írja be a **ServiceNow** kifejezést a keresőmezőbe.
1. Válassza ki **ServiceNow** az eredmények panelen, és vegye fel az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőn.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Konfigurálás és tesztelés az Azure AD SSO a servicenow segítségével egy teszt felhasználói nevű **B.Simon**. Működik az egyszeri bejelentkezés Azure AD-felhasználót és a kapcsolódó felhasználó közötti kapcsolat kapcsolat létrehozására a ServiceNow szüksége.

A servicenow segítségével az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása, hajtsa végre a következő építőelemeket:

1. **[Az Azure AD SSO konfigurálása](#configure-azure-ad-sso)**  ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Konfigurálja a ServiceNow](#configure-servicenow)**  alkalmazás oldalán az egyszeri bejelentkezési beállításainak konfigurálására.
3. **[Az Azure AD SSO konfigurálása ServiceNow expressz](#configure-azure-ad-sso-for-servicenow-express)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
4. **[ServiceNow-egyszeri bejelentkezést Express](#configure-servicenow-express-sso)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
5. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  az Azure AD egyszeri bejelentkezés az B.Simon teszteléséhez.
6. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  B.Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
7. **[A ServiceNow tesztfelhasználó létrehozása](#create-servicenow-test-user)**  van egy B.Simon megfelelője a ServiceNow, amely kapcsolódik az Azure AD felhasználói ábrázolása.
8. **[Egyszeri bejelentkezés tesztelése](#test-sso)**  ellenőrzése, hogy működik-e a konfiguráció.
9. **[Egyszeri bejelentkezés tesztelése ServiceNow klasszikus (mobil)](#test-sso-for-servicenow-classic-mobile)**  ellenőrzése, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD egyszeri bejelentkezés engedélyezése az Azure Portalon.

1. Az a [az Azure portal](https://portal.azure.com/), a a **ServiceNow** alkalmazás integráció lapon keresse meg a **kezelése** szakaszt, és válassza **egyszeri bejelentkezés**.
1. Az a **egyszeri bejelentkezés módszer** lapra, jelölje be **SAML**.
1. Az a **állítsa be egyszeri bejelentkezést az SAML** lap, kattintson a Szerkesztés/toll ikonra a **alapszintű SAML-konfigurációja** beállításait módosíthatja.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    a. Az a **bejelentkezési URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<instance-name>.service-now.com/navpage.do`

    b. Az a **azonosító (entityid)** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<instance-name>.service-now.com`

    > [!NOTE]
    > Ezek a értékei nem valódi. Az értékeket módosítsa a tényleges bejelentkezési URL-és az azonosítója, amelynek az ismertetése az oktatóanyag későbbi részében szüksége. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

1. A a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén található **tanúsítvány (Base64)** válassza **letöltése** töltse le a tanúsítványt, és menti azt a számítógépet.

   ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

   a. Kattintson a Másolás gombra, hogy **alkalmazás összevonási metaadatainak URL-címe** , és illessze be a Jegyzettömbbe, mivel az alkalmazás összevonási metaadatainak URL-címet az oktatóanyag későbbi részében fogja használni.

    b. Kattintson a **letöltése** letöltéséhez **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

1. Az a **állítsa be a ServiceNow** területén másolja a megfelelő URL-címe szerint.

   ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

### <a name="configure-servicenow"></a>A ServiceNow konfigurálása

1. Jelentkezzen be rendszergazdaként a ServiceNow alkalmazás.

2. Aktiválja a **integráció – több szolgáltató egyszeri bejelentkezéses telepítő** beépülő modul a következő lépéseket követve:

    a. A bal oldali navigációs ablaktábláján keressen **rendszer definíció** szakasz a keresősávba, és kattintson a **beépülő modulok**.

    ![Aktiválja a beépülő modul](./media/servicenow-tutorial/tutorial_servicenow_03.png "beépülő modul aktiválása")

    b. Keresse meg **integráció – több szolgáltató egyszeri bejelentkezéses telepítő**.

     ![Aktiválja a beépülő modul](./media/servicenow-tutorial/tutorial_servicenow_04.png "beépülő modul aktiválása")

    c. Válassza ki a beépülő modult. Kattintson a jobb gombbal, és válassza ki **aktiválás/frissítés**.

     ![Aktiválja a beépülő modul](./media/servicenow-tutorial/tutorial_activate.png "beépülő modul aktiválása")

    d. Kattintson a **aktiválás** gombra.

     ![Aktiválja a beépülő modul](./media/servicenow-tutorial/tutorial_activate1.png "beépülő modul aktiválása")

3. A bal oldali navigációs ablaktábláján keressen **Többszolgáltatós SSO** szakasz a keresősávba, és kattintson a **tulajdonságok**.

    ![Alkalmazás URL-cím konfigurálása](./media/servicenow-tutorial/tutorial_servicenow_06.png "alkalmazás URL-CÍMEK konfigurálása")

4. Az a **több szolgáltató egyszeri bejelentkezési tulajdonságok** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![Alkalmazás URL-cím konfigurálása](./media/servicenow-tutorial/ic7694981.png "alkalmazás URL-CÍMEK konfigurálása")

    * Mint **több szolgáltató SSO engedélyezése**válassza **Igen**.
  
    * Mint **automatikus importálás engedélyezése a felhasználók minden, a felhasználó táblába identitás-szolgáltatóktól származó**válassza **Igen**.

    * Mint **engedélyezése hibakeresési naplózás a több szolgáltató SSO integrációs**válassza **Igen**.

    * A **a mezőt a felhasználó táblával, amely...**  szövegmezőbe írja be **felhasználónév**.
  
    * Kattintson a **Save** (Mentés) gombra.

5. Kétféleképpen, amelyben **ServiceNow** konfigurálható – automatikus és kézi.

6. Konfigurálásához **ServiceNow** automatikusan, kövesse az alábbi lépéseket:

    * Lépjen vissza a **ServiceNow** egyszeri bejelentkezés az Azure Portalon az oldalon.

    * Egyetlen kattintással szolgáltatás konfigurálása kerül a servicenow-hoz készült, hogy, az Azure AD automatikus konfigurálása a ServiceNow SAML-alapú hitelesítéshez. Ahhoz, hogy ez a szolgáltatás Ugrás **ServiceNow konfigurációs** területén kattintson **konfigurálása ServiceNow** konfigurálása bejelentkezési ablak megnyitásához.

        ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

    * Adja meg a ServiceNow-példány neve, a rendszergazdai felhasználónév és a rendszergazdai jelszó a **bejelentkezés konfigurálása** alkotnak, és kattintson a **konfigurálása most**. Vegye figyelembe, hogy a megadott rendszergazdai felhasználónév kell rendelkeznie a **security_admin** ServiceNow ennek működéséhez a hozzárendelt szerepkörrel. Ellenkező esetben kézzel adja meg ServiceNow használata az Azure AD SAML identitás-szolgáltatóként, kattintson **egyszeri bejelentkezés manuális konfigurálása** , és másolja a **kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** rövid összefoglaló szakaszából.

        ![Alkalmazás URL-cím konfigurálása](./media/servicenow-tutorial/configure.png "alkalmazás URL-CÍMEK konfigurálása")

    * Jelentkezzen be rendszergazdaként a ServiceNow alkalmazás.

    * Az automatikus beállítás az összes szükséges beállításainak konfigurálása a **ServiceNow** ügyféloldali, de a **X.509-tanúsítvány** alapértelmezés szerint nincs engedélyezve. Manuálisan feleltesse meg az identitásszolgáltató a ServiceNow kell. kövesse az alábbi lépéseket ugyanazon:

    * A bal oldali navigációs ablaktábláján keressen **Többszolgáltatós SSO** szakasz a keresősávba, és kattintson a **Identitásszolgáltatók**.

        ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/tutorial_servicenow_07.png "egyszeri bejelentkezés konfigurálása")

    * Kattintson a az automatikusan létrehozott identitásszolgáltató

        ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/tutorial_servicenow_08.png "egyszeri bejelentkezés konfigurálása")

    *  Az a **identitásszolgáltató** szakaszban, hajtsa végre az alábbi lépéseket:

        ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/automatic_config.png "egyszeri bejelentkezés konfigurálása")

        * Az a **neve** szövegmezőbe írja be a konfiguráció nevét (például **a Microsoft Azure összevont egyszeri bejelentkezés**).

        * Távolítsa el a feltöltött **identitásszolgáltató SingleLogoutRequest** a szövegmező értékét.

        * Másolás **ServiceNow kezdőlap** értékét, illessze be a **bejelentkezési URL-** szövegmezőjébe **ServiceNow alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

            > [!NOTE]
            > A ServiceNow-példány kezdőlapjára összefűzésével a **ServiceNow-bérlői URL-cím** és **/navpage.do** (például:`https://fabrikam.service-now.com/navpage.do`).

        * Másolás **Entitásazonosító / kibocsátó** értékét, illessze be a **azonosító** szövegmezőjébe **ServiceNow alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

        * Győződjön meg arról, hogy **NameID házirend** értékre van állítva `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` értéket. 

    * Görgessen le a **X.509-tanúsítvány** szakaszban jelölje be **szerkesztése**.

        ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/tutorial_servicenow_09.png "egyszeri bejelentkezés konfigurálása")

    * Válassza ki a tanúsítványban, és a jobbra mutató nyíl ikonra kattintva vegye fel a tanúsítványt

        ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/tutorial_servicenow_11.png "egyszeri bejelentkezés konfigurálása")

    * Kattintson a **Save** (Mentés) gombra.

    * Kattintson a **kapcsolat tesztelése** az oldal jobb felső sarkában található.

        ![Aktiválja a beépülő modul](./media/servicenow-tutorial/tutorial_activate2.png "beépülő modul aktiválása")

    * Kattintás után a a **kapcsolat tesztelése**, kap a előugró ablak, ahol meg kell adnia hitelesítő adatait, és az alább eredményeket tartalmazó lap jelenik meg. A **Egyszeri kijelentkezési teszteredmények** várt hiba figyelmen kívül hagyhatja a hibát, és kattintson a **aktiválás** gombra.

        ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/servicenowactivate.png "egyszeri bejelentkezés konfigurálása")
  
7. Konfigurálásához **ServiceNow** manuálisan, kövesse az alábbi lépéseket:

    * Jelentkezzen be rendszergazdaként a ServiceNow alkalmazás.

    * A bal oldali navigációs panelén kattintson **Identitásszolgáltatók**.

        ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/tutorial_servicenow_07.png "egyszeri bejelentkezés konfigurálása")

    * Az a **Identitásszolgáltatók** párbeszédpanelen kattintson a **új**.

        ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/ic7694977.png "egyszeri bejelentkezés konfigurálása")

    * Az a **Identitásszolgáltatók** párbeszédpanelen kattintson a **SAML**.

        ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/ic7694978.png "egyszeri bejelentkezés konfigurálása")

    * Az a **Identity Provider metaadatok importálása** előugró ablakban hajtsa végre az alábbi lépéseket:

        ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/idp.png "egyszeri bejelentkezés konfigurálása")

        * Adja meg a **alkalmazás összevonási metaadatainak URL-címe** Azure Portalról másolt.

        * Kattintson az **Importálás** elemre.

    * Ez olvassa be az identitásszolgáltató metaadatok URL-címet, és tölti fel a mezők adatai.

        ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/ic7694982.png "egyszeri bejelentkezés konfigurálása")

        * Az a **neve** szövegmezőbe írja be a konfiguráció nevét (például **a Microsoft Azure összevont egyszeri bejelentkezés**).

        * Távolítsa el a feltöltött **identitásszolgáltató SingleLogoutRequest** a szövegmező értékét.

        * Másolás **ServiceNow kezdőlap** értékét, illessze be a **bejelentkezési URL-** szövegmezőjébe **ServiceNow alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

            > [!NOTE]
            > A ServiceNow-példány kezdőlapjára összefűzésével a **ServiceNow-bérlői URL-cím** és **/navpage.do** (például:`https://fabrikam.service-now.com/navpage.do`).

        * Másolás **Entitásazonosító / kibocsátó** értékét, illessze be a **azonosító** szövegmezőjébe **ServiceNow alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

        * Győződjön meg arról, hogy **NameID házirend** értékre van állítva `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` értéket.

        * Kattintson a **speciális**. Az a **felhasználói mező** szövegmezőbe írja be **e-mail** vagy **felhasználónév**, attól függően, hogy melyik mezőt a ServiceNow-telepítésben a felhasználók egyedi azonosítására szolgál.

            > [!NOTE]
            > Konfigurálhatja az Azure AD vagy az Azure AD-felhasználói azonosító (egyszerű felhasználónév), vagy az e-mail-cím egyedi azonosítóként a SAML-jogkivonat kibocsátható nyissa meg a **ServiceNow > attribútumok > egyszeri bejelentkezés** szakaszában az Azure Portalon leképezés a kívánt mezőt, és a **nameidentifier** attribútum. A kiválasztott attribútumnak (Ha például egyszerű felhasználónév) Azure AD-ben tárolt érték meg kell egyeznie a megadott mező (például felhasználónév) ServiceNow tárolt érték

        * Kattintson a **kapcsolat tesztelése** az oldal jobb felső sarkában található.

        * Kattintás után a a **kapcsolat tesztelése**, kap a előugró ablak, ahol meg kell adnia hitelesítő adatait, és az alább eredményeket tartalmazó lap jelenik meg. A **Egyszeri kijelentkezési teszteredmények** várt hiba figyelmen kívül hagyhatja a hibát, és kattintson a **aktiválás** gombra.

          ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/servicenowactivate.png "egyszeri bejelentkezés konfigurálása")

### <a name="configure-azure-ad-sso-for-servicenow-express"></a>A ServiceNow expressz Azure AD SSO konfigurálása

1. Az a [az Azure portal](https://portal.azure.com/), az a **ServiceNow** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezés módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    a. Az a **bejelentkezési URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<instance-name>.service-now.com/navpage.do`

    b. Az a **azonosító (entityid)** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<instance-name>.service-now.com`

    > [!NOTE]
    > Ezek a értékei nem valódi. Az értékeket módosítsa a tényleges bejelentkezési URL-és az azonosítója, amelynek az ismertetése az oktatóanyag későbbi részében szüksége. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

6. Egyetlen kattintással szolgáltatás konfigurálása kerül a servicenow-hoz készült, hogy, az Azure AD automatikus konfigurálása a ServiceNow SAML-alapú hitelesítéshez. Ahhoz, hogy ez a szolgáltatás Ugrás **állítsa be a ServiceNow** területén kattintson **részletes útmutató megtekintéséhez** konfigurálása bejelentkezési ablak megnyitásához.

    ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

7. Adja meg a ServiceNow-példány neve, a rendszergazdai felhasználónév és a rendszergazdai jelszó a **bejelentkezés konfigurálása** alkotnak, és kattintson a **konfigurálása most**. Vegye figyelembe, hogy a megadott rendszergazdai felhasználónév kell rendelkeznie a **security_admin** ServiceNow ennek működéséhez a hozzárendelt szerepkörrel. Ellenkező esetben kézzel adja meg ServiceNow használata az Azure AD SAML identitás-szolgáltatóként, kattintson **egyszeri bejelentkezés manuális konfigurálása** , és másolja a **kijelentkezési URL-címe, az Azure AD-azonosító és bejelentkezési URL-cím** származó a Gyors útmutató szakaszban.

    ![Alkalmazás URL-cím konfigurálása](./media/servicenow-tutorial/configure.png "alkalmazás URL-CÍMEK konfigurálása")

### <a name="configure-servicenow-express-sso"></a>A ServiceNow Express egyszeri bejelentkezés konfigurálása

1. Jelentkezzen be rendszergazdaként a ServiceNow Express-alkalmazás.

2. A bal oldali navigációs panelén kattintson **egyszeri bejelentkezés**.

    ![Alkalmazás URL-cím konfigurálása](./media/servicenow-tutorial/ic7694980ex.png "alkalmazás URL-CÍMEK konfigurálása")

3. Az a **egyszeri bejelentkezés** párbeszédpanelen kattintson a jobb felső részén található konfigurációs ikonra, és állítsa be a következő tulajdonságokat:

    ![Alkalmazás URL-cím konfigurálása](./media/servicenow-tutorial/ic7694981ex.png "alkalmazás URL-CÍMEK konfigurálása")

    a. Váltógomb **több szolgáltató SSO engedélyezése** jobbra.

    b. Váltógomb **engedélyezése hibakeresési naplózás a több szolgáltató SSO integrációs** jobbra.

    c. A **a mezőt a felhasználó táblával, amely...**  szövegmezőbe írja be **felhasználónév**.

4. Az a **egyszeri bejelentkezés** párbeszédpanelen kattintson a **új tanúsítvány hozzáadása**.

    ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/ic7694973ex.png "egyszeri bejelentkezés konfigurálása")

5. Az a **X.509 tanúsítványok** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/ic7694975.png "egyszeri bejelentkezés konfigurálása")

    a. Az a **neve** szövegmezőbe írja be a konfiguráció nevét (például: **TestSAML2.0**).

    b. Válassza ki **aktív**.

    c. Mint **formátum**válassza **PEM**.

    d. Mint **típusa**válassza **Store tanúsítvány megbízható**.

    e. Nyissa meg a Jegyzettömbben az Azure portálról letöltött Base64-kódolású tanúsítvány, a tartalmát a vágólapra másolja és illessze be azt a **PEM tanúsítvány** szövegmezőbe.

    f. Kattintson a **Update**

6. Az a **egyszeri bejelentkezés** párbeszédpanelen kattintson a **adja hozzá az új identitásszolgáltató**.

    ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/ic7694976ex.png "egyszeri bejelentkezés konfigurálása")

7. Az a **hozzáadása új identitásszolgáltató** párbeszédpanelen, a **identitásszolgáltató konfigurálása**, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/ic7694982ex.png "egyszeri bejelentkezés konfigurálása")

    a. Az a **neve** szövegmezőbe írja be a konfiguráció nevét (például: **SAML 2.0**).

    b. Az a **identitásszolgáltató szolgáltató URL-címe** mezőbe illessze be az értéket a **identitás Szolgáltatóazonosító**, az Azure Portalról másolt.

    c. Az a **identitásszolgáltató AuthnRequest** mezőbe illessze be az értéket a **hitelesítési kérelem URL-Címének**, az Azure Portalról másolt.

    d. Az a **identitásszolgáltató SingleLogoutRequest** mezőbe illessze be az értéket a **kijelentkezési URL-címe**, az Azure Portalról másolt

    e. Mint **szolgáltató Identitástanúsítványt**, válassza ki az előző lépésben létrehozott tanúsítványt.

8. Kattintson a **speciális beállítások**, majd a **további identitás szolgáltató tulajdonságai**, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/ic7694983ex.png "egyszeri bejelentkezés konfigurálása")

    a. Az a **protokoll kötést az Identitásszolgáltató SingleLogoutRequest** szövegmezőbe írja be **urn: oasis: nevek: tc: SAML:2.0:bindings:HTTP-átirányítási**.

    b. Az a **NameID házirend** szövegmezőbe írja be **urn: oasis: nevek: tc: SAML:1.1:nameid-formátum: nem meghatározott**.

    c. Az a **AuthnContextClassRef metódus**, típus `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`.

    d. Kapcsolja ki **hozzon létre egy AuthnContextClass**.

9. A **további szolgáltató tulajdonságai**, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/servicenow-tutorial/ic7694984ex.png "egyszeri bejelentkezés konfigurálása")

    a. Az a **ServiceNow kezdőlap** szövegmezőbe írja be a ServiceNow-példány kezdőlap URL-CÍMÉT.

    > [!NOTE]
    > A ServiceNow-példány kezdőlapjára összefűzésével a **ServiceNow-bérlői URL-cím** és **/navpage.do** (például: `https://fabrikam.service-now.com/navpage.do`).

    b. Az a **Entitásazonosító / kibocsátó** szövegmezőbe írja be a ServiceNow-bérlői URL-CÍMÉT.

    c. Az a **célközönség URI** szövegmezőbe írja be a ServiceNow-bérlői URL-CÍMÉT.

    d. A **óra tevékenységdiagramon** szövegmezőbe írja be **60**.

    e. Az a **felhasználói mező** szövegmezőbe írja be **e-mail** vagy **felhasználónév**, attól függően, hogy melyik mezőt a ServiceNow-telepítésben a felhasználók egyedi azonosítására szolgál.

    > [!NOTE]
    > Konfigurálhatja az Azure AD vagy az Azure AD-felhasználói azonosító (egyszerű felhasználónév), vagy az e-mail-cím egyedi azonosítóként a SAML-jogkivonat kibocsátható nyissa meg a **ServiceNow > attribútumok > egyszeri bejelentkezés** szakaszában az Azure Portalon leképezés a kívánt mezőt, és a **nameidentifier** attribútum. A kiválasztott attribútumnak (Ha például egyszerű felhasználónév) Azure AD-ben tárolt érték meg kell egyeznie a megadott mező (például felhasználónév) ServiceNow tárolt érték

    f. Kattintson a **Save** (Mentés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban az Azure Portalon B.Simon nevű tesztfelhasználó fog létrehozni.

1. Az Azure Portal bal oldali panelén válassza **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.
1. Válassza ki **új felhasználó** a képernyő tetején.
1. Az a **felhasználói** tulajdonságok, kövesse az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. Az a **felhasználónév** mezőbe írja be a username@companydomain.extension. Például: `B.Simon@contoso.com`.
   1. Válassza ki a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.
   1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban B.Simon által biztosított hozzáférés ServiceNow Azure egyszeri bejelentkezés használatához engedélyeznie kell.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.
1. Az alkalmazások listájában jelölje ki a **ServiceNow**.
1. Az alkalmazás áttekintése lapon keresse meg a **kezelés** szakaszt, és válassza **felhasználók és csoportok**.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza ki **felhasználó hozzáadása**, majd **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. Az a **felhasználók és csoportok** párbeszédablakban válassza **B.Simon** a felhasználók listájából, majd kattintson a **kiválasztása** gombra a képernyő alján.
1. Ha a SAML helyességi feltétel, a szerepkör értéket vár a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználóhoz a listából, és kattintson a **kiválasztása** gombra a képernyő alján.
1. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-servicenow-test-user"></a>A ServiceNow tesztfelhasználó létrehozása

Ez a szakasz célja a ServiceNow Britta Simon nevű felhasználó létrehozásához. ServiceNow támogatja a felhasználók automatikus átadása, amely alapértelmezés szerint van engedélyezve. További részleteket talál [Itt](servicenow-provisioning-tutorial.md) konfigurálásának a felhasználók automatikus átadása.

> [!NOTE]
> Hozzon létre egy felhasználót manuálisan kell, ha kapcsolatba kell [ServiceNow ügyfél-támogatási csoport](https://www.servicenow.com/support/contact-support.html)

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

A ServiceNow csempe kiválasztásakor a hozzáférési panelen, kell lennie automatikusan bejelentkezett a ServiceNow, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="test-sso-for-servicenow-classic-mobile"></a>Egyszeri bejelentkezés tesztelése ServiceNow klasszikus (mobil)

1. Nyissa meg a **ServiceNow klasszikus (mobil)** alkalmazást, és hajtsa végre az alábbi lépéseket:

    a. Kattintson a **hozzáadása** jel a képernyő alatt.

    ![A bejelentkezési](./media/servicenow-tutorial/test03.png)

    b. Írja be a ServiceNow-példány neve, és kattintson a **Folytatás**.

    ![A bejelentkezési](./media/servicenow-tutorial/test04.png)

    c. Az a **bejelentkezés** képernyőn, hajtsa végre az alábbi lépéseket:

    ![A bejelentkezési](./media/servicenow-tutorial/test01.png)

    *  Típus **felhasználónév** például B.simon@contoso.com.

    *  Kattintson a **használata külső bejelentkezés** és az Azure AD bejelentkezési oldalára irányítja.
    
    *  Adja meg hitelesítő adatait, és ha bármely harmadik fél hitelesítési vagy egyéb biztonsági funkció engedélyezve van, akkor a felhasználónak válaszolni ennek megfelelően kell, és az alkalmazás **kezdőlap** jelenik meg lent látható módon:

        ![A kezdőlap](./media/servicenow-tutorial/test02.png)

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Felhasználók átadásának konfigurálása](servicenow-provisioning-tutorial.md)
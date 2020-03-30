---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a ServiceNow-val | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a ServiceNow között.
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
ms.topic: tutorial
ms.date: 03/25/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8fe6c857e5b0c2f48f27c167c177dbf1f4651986
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384109"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-servicenow"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a ServiceNow szolgáltatással

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a ServiceNow-t az Azure Active Directoryval (Azure AD). Ha integrálja a ServiceNow-t az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a ServiceNow-hoz.
* Engedélyezze, hogy a felhasználók automatikusan bejelentkezve a ServiceNow az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen: az Azure Portalon.

Ha többet szeretne megtudni a szoftverszolgáltatásként (SaaS) alkalmazásintegrációról az Azure AD-vel, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* A ServiceNow egyszeri bejelentkezés (SSO) engedélyezett előfizetés.
* ServiceNow, egy példány vagy bérlő a ServiceNow, Calgary verzió vagy újabb.
* ServiceNow Express esetén a ServiceNow Express, helsinki vagy újabb verzió.
* A ServiceNow bérlőnek engedélyeznie kell a [többszolgáltatós egyszeri bejelentkezésbeépülő beépülő modult.](https://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) Ezt [szolgáltatási kérelem elküldésével](https://hi.service-now.com)teheti meg.
* Az automatikus konfiguráció, hogy a multi-szolgáltató plugin ServiceNow.
* A ServiceNow Classic (Mobile) alkalmazás telepítéséhez keresse meg a megfelelő áruházat, és keresse meg a ServiceNow Classic alkalmazást. Akkor töltse le.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben. 

* A ServiceNow támogatja az **SP** által kezdeményezett SSO-t.

* A ServiceNow támogatja [az automatikus felhasználói kiépítést.](servicenow-provisioning-tutorial.md)

* Miután konfigurálta a ServiceNow kényszerítheti a munkamenet-vezérlők, amelyek védik a kiszivárgás és a szervezet bizalmas adatainak valós idejű beszivárgása. A munkamenet-vezérlők a feltételes hozzáféréstől származnak. [Megtudhatja, hogy miként kényszerítheti ki a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

* Konfigurálhatja a ServiceNow Classic (Mobile) alkalmazást az Azure AD-vel az SSO engedélyezéséhez. Támogatja mind az Android, mind az iOS felhasználókat. Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

## <a name="add-servicenow-from-the-gallery"></a>ServiceNow hozzáadása a galériából

A ServiceNow azure AD-be való integrációjának konfigurálásához hozzá kell adnia a ServiceNow-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiókkal.
1. A bal oldali ablaktáblában válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a Minden **alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből** szakaszban írja be a **ServiceNow** kifejezést a keresőmezőbe.
1. Válassza a **ServiceNow** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-servicenow"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése a ServiceNow-hoz

Konfigurálja és tesztelje az Azure AD SSO szolgáltatást a ServiceNow segítségével egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a ServiceNow kapcsolódó felhasználója között.

Az Azure AD SSO servicenow-val való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. [Konfigurálja az Azure AD SSO-t,](#configure-azure-ad-sso) hogy a felhasználók használhassák ezt a funkciót.
    1. [Hozzon létre egy Azure AD-tesztfelhasználót](#create-an-azure-ad-test-user) az Azure AD egyszeri bejelentkezésének teszteléséhez B.Simon nal.
    1. [Rendelje hozzá az Azure AD tesztfelhasználót,](#assign-the-azure-ad-test-user) hogy b.Simon az Azure AD egyszeri bejelentkezést használhasson.
    1. [Konfigurálja az Azure AD SSO serviceNow Express szolgáltatásához,](#configure-azure-ad-sso-for-servicenow-express) hogy a felhasználók használhassák ezt a funkciót.
2. [Konfigurálja](#configure-servicenow) a ServiceNow szolgáltatást az alkalmazás oldali SSO-beállítások konfigurálásához.
    1. [Hozzon létre egy ServiceNow teszt felhasználó,](#create-servicenow-test-user) hogy egy megfelelője B.Simon a ServiceNow, a felhasználó Azure AD-ábrázolásához kapcsolódik.
    1. [Konfigurálja a ServiceNow Express Egyszeri bejelentkezést](#configure-servicenow-express-sso) az alkalmazás oldalon az egyszeri bejelentkezési beállítások konfigurálásához.  
3. [Tesztelje az SSO-t,](#test-sso) hogy ellenőrizze, működik-e a konfiguráció.
4. [Tesztelje az SSO servicenow classic (mobile)](#test-sso-for-servicenow-classic-mobile) tesztelését annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **ServiceNow** alkalmazásintegrációs lapon keresse meg a **Kezelés** szakaszt. Válassza **ki az egyszeri bejelentkezést**.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon válassza az **egyszerű SAML-konfiguráció** tollikonját a beállítások szerkesztéséhez.

   ![Képernyőkép: Egyszeri bejelentkezés beállítása SAML-oldallal, kiemelve a toll ikonja](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** csoportban hajtsa végre az alábbi lépéseket:

    a. A **Bejelentkezési URL-cím**mezőbe írjon be egy URL-címet, amely a következő mintát használja:`https://instance.service-now.com/login_with_sso.do?glide_sso_id=<sys_id of the sso configuration>`

    b. Az **azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet, amely a következő mintát használja:`https://<instance-name>.service-now.com`

    c. A **Válasz URL-címéhez**adja meg az alábbi URL-cím egyikét:

    |||
    |-|-|
    | `https://instancename.service-now.com/navpage.do` |
    | `https://instancename.service-now.com/customer.do` | 

    > [!NOTE]
    > Ezek az értékek nem valósak. Ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval kell frissítenie, amelyet az oktatóanyag későbbi részében ismertetünk. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában keresse meg a **Tanúsítvány (Base64) című részt.** 

   ![Képernyőkép az SAML aláíró tanúsítvány szakaszról, kiemelve a Letöltés lehetőséggel](common/certificatebase64.png)

   a. Válassza a másolás gombot az **Alkalmazásösszevonás metaadat-url-címének**másolásához, és illessze be a Jegyzettömbbe. Ez az URL-cím az oktatóanyag későbbi részében lesz használva.

    b. Válassza a **Letöltés** lehetőséget a **Tanúsítvány(Alap64)** letöltéséhez, majd mentse a tanúsítványfájlt a számítógépre.

1. A **ServiceNow beállítása** szakaszban másolja a megfelelő URL-címeket a követelmény alapján.

   ![Képernyőkép: A ServiceNow beállítása szakasz, kiemelt URL-címekkel](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy B.Simon nevű tesztfelhasználót hoz létre az Azure Portalon.

1. Az Azure Portal bal oldali ablaktáblájában válassza az **Azure Active Directory** > **felhasználói** > **minden felhasználó lehetőséget.**
1. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.
1. A **Felhasználói** tulajdonságok csoportban hajtsa végre az alábbi lépéseket:
   1. A **Név** `B.Simon`mezőbe írja be a mezőbe.  
   1. A **Felhasználónév**mezőbe username@companydomain.extensionírja be a. Például: `B.Simon@contoso.com`.
   1. Válassza **a Jelszó megjelenítése**lehetőséget, majd írja le a **Jelszó** mezőben látható értéket.
   1. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés használatával a ServiceNow hozzáférést biztosít.

1. Az Azure Portalon válassza a **Vállalati alkalmazások** > **minden alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **ServiceNow**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![Képernyőkép: Kezelés szakasz, kiemelt Felhasználók és csoportok](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget. A **Hozzárendelés hozzáadása** párbeszédpanelen válassza a **Felhasználók és csoportok**lehetőséget.

    ![Képernyőkép: Felhasználók és csoportok, kiemelt Felhasználó hozzáadása](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a Felhasználók listájában a **B.Simon** elemet, majd válassza a **Kijelölés lehetőséget.**
1. Ha az SAML-feltételben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából. Ezután kattintson a **Kiválasztás** gombra.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen válassza a **Hozzárendelés lehetőséget.**

### <a name="configure-azure-ad-sso-for-servicenow-express"></a>Az Azure AD SSO konfigurálása a ServiceNow Express szolgáltatáshoz

1. Az [Azure Portalon](https://portal.azure.com/)a **ServiceNow** alkalmazásintegrációs lapon válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Képernyőkép a ServiceNow alkalmazásintegrációs lapról, kiemelve az Egyszeri bejelentkezés](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza az **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Képernyőkép: Egyetlen bejelentkezési módszer kiválasztása kiemelt SAML-lel](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon jelölje ki a toll ikonját az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Képernyőkép: Egyszeri bejelentkezés beállítása SAML-lappal, kiemelve a toll ikonja](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** csoportban hajtsa végre az alábbi lépéseket:

    a. A **Bejelentkezés az URL-címben**a következő mintát használó URL-címet adhatja meg:`https://instance.service-now.com/login_with_sso.do?glide_sso_id=<sys_id of the sso configuration>`

    b. Az **azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet, amely a következő mintát használja:`https://<instance-name>.service-now.com`

    c. A **Válasz URL-címéhez**adja meg az alábbi URL-cím egyikét:

    |||
    |-|-|
    | `https://instancename.service-now.com/navpage.do` |
    | `https://instancename.service-now.com/customer.do` |

    > [!NOTE]
    > Ezek az értékek nem valósak. Ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval kell frissítenie, amelyet az oktatóanyag későbbi részében ismertetünk. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

5. Az **egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában válassza a **Letöltés** lehetőséget a **tanúsítvány (Base64)** letöltéséhez a megadott beállításokból, a követelménynek megfelelően. Mentse a számítógépre.

    ![Képernyőkép az SAML aláíró tanúsítvány szakaszról, kiemelve a Letöltés lehetőséggel](common/certificatebase64.png)

6. Beállíthatja, hogy az Azure AD automatikusan konfigurálja a ServiceNow szolgáltatást SAML-alapú hitelesítéshez. A szolgáltatás engedélyezéséhez nyissa meg a **ServiceNow beállítása szakaszt,** és válassza a **Lépésenkénti értesítések megtekintése** lehetőséget a **Bejelentkezés konfigurálása** ablak megnyitásához.

    ![Képernyőkép: A ServiceNow beállítása szakasz, kiemelve a Nézet lépésenkénti utasításokat](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

7. A **Bejelentkezés konfigurálása** képernyőn adja meg a ServiceNow-példány nevét, a rendszergazdai felhasználónevet és a rendszergazdai jelszót. Válassza a **Konfigurálás most**lehetőséget. A megadott rendszergazdai felhasználónévnek rendelkeznie kell a ServiceNow **security_admin** szerepkörével ahhoz, hogy ez működjön. Ellenkező esetben a ServiceNow szolgáltatás saml-identitásszolgáltatóként való használatára történő manuális konfigurálásához válassza **az Egyszeri bejelentkezés manuális konfigurálása**lehetőséget. Másolja a **kijelentkezési URL-címet, az Azure AD-azonosítót és a bejelentkezési URL-címet** a rövid útmutató szakaszból.

    ![Képernyőkép: Bejelentkezési űrlap konfigurálása, kiemelve a Beállítás most](./media/servicenow-tutorial/configure.png "Alkalmazás URL-címének konfigurálása")

## <a name="configure-servicenow"></a>ServiceNow konfigurálása

1. Jelentkezzen be a ServiceNow alkalmazásba rendszergazdaként.

1. Aktiválja az **Integráció – Több szolgáltató egyetlen bejelentkezési** beépülő modulját az alábbi lépésekkel:

    a. A bal oldali ablaktáblában keresse meg a **Rendszerdefiníció** szakaszt a keresőmezőből, majd válassza a **Bővítmények**lehetőséget.

    ![Képernyőkép a Rendszerdefiníció szakaszról, kiemelve a Rendszerdefiníció és a beépülő modulok](./media/servicenow-tutorial/tutorial_servicenow_03.png "Bővítmény aktiválása")

    b. Keresés **az integráció - Több szolgáltató egyetlen bejelentkezési telepítő**.

     ![Képernyőkép a Rendszerbeépülő modulok lapról, amelyen az Integráció – Több szolgáltató egyetlen bejelentkezési telepítője van kiemelve](./media/servicenow-tutorial/tutorial_servicenow_04.png "Bővítmény aktiválása")

    c. Válassza ki a beépülő modult. Kattintson a jobb gombbal, és válassza **az Aktiválás/frissítés parancsot.**

     ![Képernyőkép a beépülő modul jobb gombbal elérhető menüjéről, kiemelve az Aktiválás/frissítés parancs](./media/servicenow-tutorial/tutorial_activate.png "Bővítmény aktiválása")

    d. Válassza **az Aktiválás**lehetőséget.

     ![Képernyőkép: A bővítmény aktiválása párbeszédpanel kiemelt Aktiválás lehetőséggel](./media/servicenow-tutorial/tutorial_activate1.png "Bővítmény aktiválása")

1. A bal oldali ablaktáblában keresse meg a **keresősáv többszolgáltatós egyszeri bejelentkezés** szakaszát, majd válassza a **Tulajdonságok lehetőséget.**

    ![Képernyőkép a többszolgáltatós egyszeri szolgáltatóról szóló szakaszról, amelyen a többszolgáltatós egyszeri szolgáltató és a tulajdonságok ki van emelve](./media/servicenow-tutorial/tutorial_servicenow_06.png "Alkalmazás URL-címének konfigurálása")

1. A **Több szolgáltató sSO tulajdonságai** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![Képernyőkép a több szolgáltató sso-tulajdonságai párbeszédpanelről](./media/servicenow-tutorial/ic7694981.png "Alkalmazás URL-címének konfigurálása")

    * A **Több szolgáltató sso engedélyezése**jelölőnégyzetben válassza az **Igen**lehetőséget.
  
    * A **felhasználók automatikus importálásának engedélyezése az összes identitásszolgáltatóból a felhasználói táblába**lehetőséget választva válassza az **Igen**lehetőséget.

    * A **több szolgáltatós sso-integrációhibanaplózásának engedélyezése**jelölőnégyzetben válassza az **Igen**lehetőséget.

    * A **felhasználói tábla Azon mezőjébe, amely...** mezőbe írja be **az e-mailt.**
  
    * Kattintson a **Mentés** gombra.

1. A ServiceNow automatikusan vagy manuálisan is konfigurálható. A ServiceNow automatikus konfigurálásához kövesse az alábbi lépéseket:

    1. Térjen vissza a **ServiceNow** egyszeri bejelentkezési lapjára az Azure Portalon.

    1. A ServiceNow egykattintásos konfigurálási szolgáltatása biztosított. A szolgáltatás engedélyezéséhez lépjen a **ServiceNow konfigurációja** szakaszra, és válassza a **ServiceNow konfigurálása** lehetőséget a **Bejelentkezés konfigurálása** ablak megnyitásához.

        ![Képernyőkép: A ServiceNow beállítása a Nézet lépésenkénti utasításával kiemelve](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

    1. A **Bejelentkezés konfigurálása** képernyőn adja meg a ServiceNow-példány nevét, a rendszergazdai felhasználónevet és a rendszergazdai jelszót. Válassza a **Konfigurálás most**lehetőséget. A megadott rendszergazdai felhasználónévnek rendelkeznie kell a ServiceNow **security_admin** szerepkörével ahhoz, hogy ez működjön. Ellenkező esetben a ServiceNow szolgáltatás saml-identitásszolgáltatóként való használatára történő manuális konfigurálásához válassza **az Egyszeri bejelentkezés manuális konfigurálása**lehetőséget. Másolja a **kijelentkezési URL-címet, az SAML entitásazonosítót és az SAML egyszeri bejelentkezési szolgáltatás URL-címét** a rövid útmutató szakaszból.

        ![Képernyőkép: Bejelentkezési űrlap konfigurálása, kiemelve a Beállítás most](./media/servicenow-tutorial/configure.png "Alkalmazás URL-címének konfigurálása")

    1. Jelentkezzen be a ServiceNow alkalmazásba rendszergazdaként.

       * Az automatikus konfigurációban az összes szükséges beállítás a **ServiceNow** oldalon van konfigurálva, de az **X.509 tanúsítvány** alapértelmezés szerint nincs engedélyezve. Manuálisan kell leképeznie az identitásszolgáltatójához a ServiceNow-ban. Kövesse az alábbi lépéseket:

         1. A bal oldali ablaktáblában keresse meg a **többszolgáltatós egyszeri bejelentkezés** szakaszt a keresőmezőből, és válassza **az Identitásszolgáltatók**lehetőséget.

            ![Képernyőkép a többszolgáltatós egyszeri szolgáltatóról szóló szakaszról, amelyen az identitásszolgáltatók ki vannak emelve](./media/servicenow-tutorial/tutorial_servicenow_07.png "Egyszeri bejelentkezés konfigurálása")

         1. Válassza ki az automatikusan létrehozott identitásszolgáltatót.

            ![Képernyőkép az identitásszolgáltatókról, kiemelve az automatikusan létrehozott identitásszolgáltatót](./media/servicenow-tutorial/tutorial_servicenow_08.png "Egyszeri bejelentkezés konfigurálása")

         1.  Az **Identitásszolgáltató** csoportban hajtsa végre az alábbi lépéseket:

             ![Képernyőkép az Identitásszolgáltató szakaszról](./media/servicenow-tutorial/automatic_config.png "Egyszeri bejelentkezés konfigurálása")

               * A **Név mezőbe**írja be a konfiguráció nevét (például microsoft Azure single **sign-on).**

               * Távolítsa el a feltöltött **identitásszolgáltató SingleLogoutRequest** értékét a szövegdobozból.

               * Másolja a **ServiceNow kezdőlap** értékét, és illessze be **a bejelentkezési URL-címbe** az Azure Portal **ServiceNow alapszintű SAML-konfiguráció szakaszában.**

                  > [!NOTE]
                  > A ServiceNow példány kezdőlapja a **ServiceNow bérlői URL-címének** és`https://fabrikam.service-now.com/navpage.do`a **/navpage.do** (például: ) összefűzése.

              * Másolja az **entitásazonosítót / kiállítói** értéket, és illessze be az **Azonosítóba** az Azure Portal **ServiceNow alapszintű SAML-konfiguráció** szakaszában.

              * Ellenőrizze, hogy a **NameID-házirend** értékre `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` van-e állítva. 

         1. Görgessen le az **X.509 Tanúsítvány** szakaszig, és válassza a **Szerkesztés**lehetőséget.

             ![Képernyőkép az X.509 Tanúsítvány szakaszról, kiemelve a Szerkesztés lehetőséggel](./media/servicenow-tutorial/tutorial_servicenow_09.png "Egyszeri bejelentkezés konfigurálása")

         1. Jelölje ki a tanúsítványt, és a jobb oldali nyílikonra a tanúsítvány hozzáadásához

            ![A Gyűjtemény képernyőképe, kiemelve a tanúsítvány és a jobb nyíl ikonja](./media/servicenow-tutorial/tutorial_servicenow_11.png "Egyszeri bejelentkezés konfigurálása")

          1. Kattintson a **Mentés** gombra.

          1. A lap jobb felső sarkában válassza a **Kapcsolat tesztelése**lehetőséget.

             ![Képernyőkép a lapról, kiemelve a Kapcsolat tesztelése lehetőséggel](./media/servicenow-tutorial/tutorial_activate2.png "Bővítmény aktiválása")

             > [!NOTE]
             > Ha a tesztkapcsolat meghibásodik, és nem tudja aktiválni ezt a kapcsolatot, akkor a ServiceNow felajánlja a felülbírálási kapcsolót. Be kell lépned **Sys_properties. lista** a **keresési navigációban,** és megnyitja a Rendszertulajdonságok új lapját. Itt létre kell hoznia egy új tulajdonságot, amelynek neve **glide.authenticate.multisso.test.connection.mandatory** **az adattípussal** **Igaz/Hamis,** majd állítsa be az **értéket** **Hamis**értékre.

             > ![Képernyőkép a Teszteredmények lapról](./media/servicenow-tutorial/testconnection-fail.png "Egyszeri bejelentkezés konfigurálása")
        
          1. Amikor a hitelesítő adatokat kéri, adja meg őket. A következő oldal jelenik meg. Az **SSO kijelentkezési teszt eredmények** hiba várható. Hagyja figyelmen kívül a hibát, és válassza **az Aktiválás**lehetőséget.

             ![Képernyőkép a Teszteredmények lapról](./media/servicenow-tutorial/servicenowactivate.png "Egyszeri bejelentkezés konfigurálása")
  
1. A **ServiceNow** manuális konfigurálásához kövesse az alábbi lépéseket:

    1. Jelentkezzen be a ServiceNow alkalmazásba rendszergazdaként.

    1. A bal oldali ablaktáblában válassza az **Identitásszolgáltatók**lehetőséget.

        ![Képernyőkép a többszolgáltatós egyszeri szolgáltatóról, kiemelve az identitásszolgáltatókkal](./media/servicenow-tutorial/tutorial_servicenow_07.png "Egyszeri bejelentkezés konfigurálása")

    1. Az **Identitásszolgáltatók** párbeszédpanelen válassza az **Új**lehetőséget.

        ![Képernyőkép az Identitásszolgáltatók párbeszédpanelről, amelyen az Új ki van emelve](./media/servicenow-tutorial/ic7694977.png "Egyszeri bejelentkezés konfigurálása")

    1. Az **Identitásszolgáltatók** párbeszédpanelen válassza az **SAML**lehetőséget.

        ![Képernyőkép az Identitásszolgáltatók párbeszédpanelről, kiemelve az SAML-t](./media/servicenow-tutorial/ic7694978.png "Egyszeri bejelentkezés konfigurálása")

    1. Az **Import Identity Provider Metadata (Identitásszolgáltató metaadatainak importálása)** mezőben hajtsa végre az alábbi lépéseket:

        ![Képernyőkép az Identitásszolgáltató importálásának metaadatairól, kiemelt URL-címmel és importálással](./media/servicenow-tutorial/idp.png "Egyszeri bejelentkezés konfigurálása")

        1. Adja meg az Azure-portálról másolt **Alkalmazás-összevonási metaadat-url-címet.**

        1. Kattintson az **Importálás** gombra.

    1. Beolvassa az IdP metaadat-URL-címét, és feltölti az összes mezőinformációt.

        ![Képernyőkép az identitásszolgáltatóról](./media/servicenow-tutorial/ic7694982.png "Egyszeri bejelentkezés konfigurálása")

        * A **Név mezőbe**írja be a konfiguráció nevét (például microsoft Azure single **sign-on).**

        * Távolítsa el a kitöltött **identitásszolgáltató SingleLogoutRequest** értékét a szövegmezőből.

        * Másolja a **ServiceNow kezdőlap** értékére. Illessze be **a bejelentkezési URL-címbe** az Azure Portal **ServiceNow alapszintű SAML-konfiguráció** szakaszában.

            > [!NOTE]
            > A ServiceNow példány kezdőlapja a **ServiceNow bérlői URL-címének** és`https://fabrikam.service-now.com/navpage.do`a **/navpage.do** (például: ) összefűzése.

        * Másolja az **entitásazonosító / kiállító** i. Illessze be az **azonosító** az Azure Portal **ServiceNow basic SAML konfigurációs** szakaszában.

        * Ellenőrizze, hogy a **NameID-házirend** értékre `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` van-e állítva.

        * Válassza az **Advanced** (Speciális) lehetőséget. A **Felhasználó mezőbe**írja be az **e-mailt.**

            > [!NOTE]
            > Konfigurálhatja az Azure AD-t, hogy az Azure AD felhasználói azonosítót (egyszerű felhasználó nevét) vagy az e-mail címet az SAML-jogkivonat egyedi azonosítójaként adja ki. Ehhez az Azure Portal > **ServiceNow-attribútumok** > **egyetlen bejelentkezési** szakaszára, majd a kívánt mező leképezésével a **nameidentifier** attribútumhoz. **ServiceNow** Az Azure AD-ben a kiválasztott attribútum (például az egyszerű felhasználónév) tárolt értéknek meg kell egyeznie a ServiceNow-ban tárolt értékkel a megadott mezőhöz (például user_name).

        * Válassza a **Kapcsolat tesztelése** lehetőséget a lap jobb felső sarkában.

          > [!NOTE]
          > Ha a tesztkapcsolat meghibásodik, és nem tudja aktiválni ezt a kapcsolatot, akkor a ServiceNow felajánlja a felülbírálási kapcsolót. Be kell lépned **Sys_properties. lista** a **keresési navigációban,** és megnyitja a Rendszertulajdonságok új lapját. Itt létre kell hoznia egy új tulajdonságot, amelynek neve **glide.authenticate.multisso.test.connection.mandatory** **az adattípussal** **Igaz/Hamis,** majd állítsa be az **értéket** **Hamis**értékre.

          > ![Képernyőkép a Teszteredmények lapról](./media/servicenow-tutorial/testconnection-fail.png "Egyszeri bejelentkezés konfigurálása")

        * Amikor a hitelesítő adatokat kéri, adja meg őket. A következő oldal jelenik meg. Az **SSO kijelentkezési teszt eredmények** hiba várható. Hagyja figyelmen kívül a hibát, és válassza **az Aktiválás**lehetőséget.

          ![Képernyőkép a Teszteredmények lapról](./media/servicenow-tutorial/servicenowactivate.png "Egyszeri bejelentkezés konfigurálása")

### <a name="create-servicenow-test-user"></a>ServiceNow tesztfelhasználó létrehozása

A szakasz célja, hogy hozzon létre egy felhasználó nevű B.Simon servicenow. A ServiceNow támogatja az automatikus felhasználói kiépítést, amely alapértelmezés szerint engedélyezve van.

> [!NOTE]
> Ha manuálisan kell létrehoznia egy felhasználót, forduljon a [ServiceNow ügyféltámogatási csapatához.](https://www.servicenow.com/support/contact-support.html)

### <a name="configure-servicenow-express-sso"></a>ServiceNow Express SSO konfigurálása

1. Jelentkezzen be a ServiceNow Express alkalmazásba rendszergazdaként.

2. A bal oldali ablaktáblában válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Képernyőkép a ServiceNow Express alkalmazásról, kiemelve az egyszeri bejelentkezés](./media/servicenow-tutorial/ic7694980ex.png "Alkalmazás URL-címének konfigurálása")

3. Az **Egyszeri bejelentkezés** párbeszédpanelen jelölje ki a jobb felső csoport konfigurációs ikonját, és állítsa be a következő tulajdonságokat:

    ![Képernyőkép az Egyszeri bejelentkezés párbeszédpanelről](./media/servicenow-tutorial/ic7694981ex.png "Alkalmazás URL-címének konfigurálása")

    a. Váltás: **Több szolgáltató sSO** engedélyezése jobbra.

    b. Váltás: **Hibakeresési naplózás engedélyezése a jobb oldali többszolgáltatós SSO-integrációhoz.**

    c. A **felhasználói tábla Azon mezőjébe, amely...** írja be **user_name.**

4. Az **Egyszeri bejelentkezés** párbeszédpanelen válassza **az Új tanúsítvány hozzáadása**lehetőséget.

    ![Képernyőkép az Egyszeri bejelentkezés párbeszédpanelről, amelyen az Új tanúsítvány hozzáadása jelölőnégyzet ki van emelve](./media/servicenow-tutorial/ic7694973ex.png "Egyszeri bejelentkezés konfigurálása")

5. Az **X.509 Tanúsítványok** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![Képernyőkép az X.509 Tanúsítványok párbeszédpanelről](./media/servicenow-tutorial/ic7694975.png "Egyszeri bejelentkezés konfigurálása")

    a. A **Név mezőbe**írja be a konfiguráció nevét (például: **TestSAML2.0**).

    b. Válassza az **Aktív**lehetőséget.

    c. A **Formátum mezőben**válassza a **PEM**lehetőséget.

    d. A **Típus mezőben**válassza a **Megbízhatósági tároló tanúsítványa lehetőséget.**

    e. Nyissa meg az Azure Portalról letöltött Base64 kódolású tanúsítványát a Jegyzettömbben. Másolja a tartalmát a vágólapra, majd illessze be a **PEM tanúsítvány** szövegmezőjébe.

    f. **Frissítés** kiválasztása

6. Az **Egyszeri bejelentkezés** párbeszédpanelen válassza **az Új idp hozzáadása**lehetőséget.

    ![Képernyőkép az Egyszeri bejelentkezés párbeszédpanelről, amelyen az Új azonosító hozzáadása jelölőnégyzet ki van emelve](./media/servicenow-tutorial/ic7694976ex.png "Egyszeri bejelentkezés konfigurálása")

7. Az **Új identitásszolgáltató hozzáadása** párbeszédpanel **Identitásszolgáltató konfigurálása területén hajtsa**végre az alábbi lépéseket:

    ![Képernyőkép: Új identitásszolgáltató hozzáadása párbeszédpanel](./media/servicenow-tutorial/ic7694982ex.png "Egyszeri bejelentkezés konfigurálása")

    a. A **Név mezőbe**írja be a konfiguráció nevét (például **SAML 2.0).**

    b. Az **identitásszolgáltató URL-címe**esetén illessze be az Azure Portalról másolt identitásszolgáltató-azonosító értékét.

    c. Az **identitásszolgáltató AuthnRequest,** illessze be a hitelesítési kérelem URL-címét, amely et másolt az Azure Portalon.

    d. Az **identitásszolgáltató SingleLogoutRequest,** illessze be a kijelentkezési URL-címet, amely az Azure Portalról másolt értékét.

    e. Az **identitásszolgáltatói tanúsítvány**esetében válassza ki az előző lépésben létrehozott tanúsítványt.

8. Válassza a **Speciális beállítások lehetőséget**. A **További identitásszolgáltató tulajdonságai csoportban**hajtsa végre az alábbi lépéseket:

    ![Képernyőkép: Új identitásszolgáltató hozzáadása párbeszédpanel kiemelt Speciális beállításokkal](./media/servicenow-tutorial/ic7694983ex.png "Egyszeri bejelentkezés konfigurálása")

    a. Az **IDP SingleLogoutRequest protokollkötéséhez**adja meg **az urna:oázis:nevek:tc:SAML:2.0:bindings:HTTP-Redirect parancsot.**

    b. A **NameID házirend**beírásához írja be **az urna:oasis:names:tc:SAML:1.1:nameid-format:meghatározatlan**.

    c. Az **AuthnContextClassRef metódus mezőbe írja be a (AuthnContextClassRef) metódust.** `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`

    d. **AuthnContextClass létrehozása**esetén kapcsolja ki (nincs bejelölve).

9. A **További szolgáltató tulajdonságai csoportban**hajtsa végre az alábbi lépéseket:

    ![Képernyőkép: Új identitásszolgáltató hozzáadása párbeszédpanel, a kiemelt különböző tulajdonságokkal](./media/servicenow-tutorial/ic7694984ex.png "Egyszeri bejelentkezés konfigurálása")

    a. A **ServiceNow kezdőlapja**mezőbe írja be a ServiceNow-példány kezdőlapjának URL-címét.

    > [!NOTE]
    > A ServiceNow példány kezdőlapja a **ServiceNow bérlői URL-címének** és `https://fabrikam.service-now.com/navpage.do`a **/navpage.do** (például: ) összefűzése.

    b. Entitásazonosító **/ kiállító**esetén adja meg a ServiceNow-bérlő URL-címét.

    c. A **Célközönség URI,** adja meg a ServiceNow-bérlő URL-címét.

    d. Az **Óradöntés mezőbe**írja be a **60**értéket.

    e. A **Felhasználói mező mezőbe**írja be az **e-mailt.**

    > [!NOTE]
    > Konfigurálhatja az Azure AD-t, hogy az Azure AD felhasználói azonosítót (egyszerű felhasználó nevét) vagy az e-mail címet az SAML-jogkivonat egyedi azonosítójaként adja ki. Ehhez az Azure Portal > **ServiceNow-attribútumok** > **egyetlen bejelentkezési** szakaszára, majd a kívánt mező leképezésével a **nameidentifier** attribútumhoz. **ServiceNow** Az Azure AD-ben a kiválasztott attribútum (például az egyszerű felhasználónév) tárolt értéknek meg kell egyeznie a ServiceNow-ban tárolt értékkel a megadott mezőhöz (például user_name).

    f. Kattintson a **Mentés** gombra.

## <a name="test-sso"></a>SSO tesztelése

Amikor kiválasztja a ServiceNow csempe a hozzáférési panelen, akkor automatikusan be kell jelentkeznie a ServiceNow, amelyhez beállította az SSO. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="test-sso-for-servicenow-classic-mobile"></a>Teszt sso servicenow klasszikus (mobil)

1. Nyissa meg a **ServiceNow Classic (Mobile)** alkalmazást, és hajtsa végre a következő lépéseket:

    a. Jelölje ki a pluszjelet a jobb alsó sarokban.

    ![Képernyőkép a ServiceNow Classic alkalmazásról, kiemelve a pluszjellel](./media/servicenow-tutorial/test03.png)

    b. Adja meg a ServiceNow-példány nevét, és válassza **a Folytatás gombot.**

    ![Képernyőkép: Példány hozzáadása lap, kiemelt Folytatás lehetőséggel](./media/servicenow-tutorial/test04.png)

    c. A **Bejelentkezés** lapon hajtsa végre az alábbi lépéseket:

    ![Képernyőkép: Bejelentkezés lap, kiemelve a Külső bejelentkezés használata lehetőséget](./media/servicenow-tutorial/test01.png)

    *  Írja be a B.simon@contoso.com **Felhasználónevet**, például.

    *  Válassza **a KÜLSŐ BEJELENTKEZÉS HASZNÁLATA**LEHETŐSÉGET. A bejelentkezéshez a lesz átirányítva az Azure AD-oldalra.

    *  Adja meg hitelesítő adatait. Ha harmadik fél hitelesítése vagy bármely más biztonsági funkció engedélyezve van, a felhasználónak ennek megfelelően kell válaszolnia. Megjelenik az alkalmazás **kezdőlapja.**

        ![Képernyőkép az alkalmazás kezdőlapjáról](./media/servicenow-tutorial/test02.png)

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directoryval való integrálásáról szóló oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Felhasználók átadásának konfigurálása](servicenow-provisioning-tutorial.md)

- [Próbálja ki a ServiceNow szolgáltatást az Azure AD-vel](https://aad.portal.azure.com)

- [Mi a munkamenet-vezérlés a Microsoft Cloud App Security alkalmazásban?](https://docs.microsoft.com/cloud-app-security/protect-servicenow)

- [A ServiceNow védelme fejlett láthatósággal és vezérlőkkel](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
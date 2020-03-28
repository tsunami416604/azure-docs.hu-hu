---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a SignalFx programmal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a SignalFx között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6d5ab4b0-29bc-4b20-8536-d64db7530f32
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/24/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 149718dcd325ef6bd6a6754ba100ffdc34be0a07
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79136414"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-signalfx"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a SignalFx programmal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a SignalFx-et az Azure Active Directoryval (Azure AD). Ha integrálja a SignalFx-et az Azure AD-vel, a következőket teheti:

* Az Azure AD-ből, aki hozzáfér a SignalFx-hez, az Azure AD-ből való vezérlés;
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve legyenek a SignalFx-be az Azure AD-fiókjukkal; És
* A fiókok kezelése egy helyen (az Azure Portalon).

Ha többet szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, szüksége lesz:

* Azure AD-előfizetés
    * Ha nincs előfizetése, itt kaphat [ingyenes fiókot.](https://azure.microsoft.com/free/)
* SignalFx egyszeri bejelentkezés (SSO) kompatibilis előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* A SignalFx támogatja az **IDP** által kezdeményezett SSO-t
* A SignalFx támogatja **a Just In Time** felhasználói kiépítést
* A SignalFx konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlő a feltételes hozzáféréstől terjed. [Megtudhatja, hogy miként kényszerítheti ki a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)

## <a name="step-1-add-the-signalfx-application-in-azure"></a>1. lépés: A SignalFx alkalmazás hozzáadása az Azure-ban

Ezekkel az utasításokkal hozzáadhatja a SignalFx alkalmazást a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. A bal oldali navigációs ablakban válassza az **Azure Active Directory**lehetőséget.
1. Válassza **a Vállalati alkalmazások**lehetőséget, majd a Minden **alkalmazás**lehetőséget.
1. Válassza az **Új alkalmazás** lehetőséget.
1. A **Hozzáadás a galéria szakaszban,** a keresőmezőbe írja be és válassza **a SignalFx**lehetőséget .
     * Előfordulhat, hogy várnia kell néhány percet, amíg az alkalmazás hozzá kell adnia a bérlőhöz.
1. Hagyja nyitva az Azure Portalt, majd nyisson meg egy új weblapot.    

## <a name="step-2-begin-signalfx-sso-configuration"></a>2. lépés: A SignalFx SSO-konfiguráció megkezdése

Ezekkel az utasításokkal megkezdheti a SignalFx SSO konfigurációs folyamatát.

1. Az újonnan megnyitott lapon érheti el a SignalFx felhasználói felületét, és jelentkezzen be. 
1. A felső menüben kattintson az **Integrációk parancsra.** 
1. A keresőmezőbe írja be és válassza az **Azure Active Directory**t.
1. Kattintson **az Új integráció létrehozása gombra.**
1. A **Név**mezőbe írjon be egy könnyen felismerhető nevet, amelyet a felhasználók megértenek.
1. **Megjelöl megjelöl Mutat -ra logika oldal**.
    * Ez a funkció egy testreszabott gombot jelenít meg a bejelentkezési oldalon, amire a felhasználók rákattinthatnak. 
    * A **Név** mezőbe megadott adatok megjelennek a gombon. Ennek eredményeképpen adjon meg egy **nevet,** amelyet a felhasználók felismernek. 
    * Ez a beállítás csak akkor fog működni, ha egyéni aldomaint használ a SignalFx alkalmazáshoz, például **a yourcompanyname.signalfx.com.** Egyéni altartomány beszerzéséhez forduljon a SignalFx ügyfélszolgálatához. 
1. Másolja az **integrációs azonosítót**. Erre az információra egy későbbi lépésben lesz szüksége. 
1. Hagyja nyitva a SignalFx felhasználói felületét. 

## <a name="step-3-configure-azure-ad-sso"></a>3. lépés: Az Azure AD-sSO konfigurálása

Ezekkel az utasításokkal engedélyezheti az Azure AD SSO-t az Azure Portalon.

1. Térjen vissza az [Azure Portalra](https://portal.azure.com/), és a **SignalFx** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza az **Egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** toll (szerkesztés) ikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon töltse ki a következő mezőket: 

    a. Az **Azonosító**mezőbe írja `https://api.<realm>.signalfx.com/v1/saml/metadata` be `<realm>` a következő URL-címet, és cserélje le a SignalFx-birodalmát. 

    b. A **Válasz URL-ben** `https://api.<realm>.signalfx.com/v1/saml/acs/<integration ID>` adja `<realm>` meg a következő URL-t, `<integration ID>` és cserélje le a SignalFx-birodalmát, valamint a SignalFx felhasználói felületéről korábban másolt **integrációs azonosítót.**

1. A SignalFx alkalmazás az SAML-állításokat egy adott formátumban várja, amely megköveteli, hogy egyéni attribútumleképezéseket adjon hozzá az SAML token attribútumkonfigurációjához. 
    
1. Ellenőrizze és ellenőrizze, hogy a következő jogcímek megfelelnek-e az Active Directoryban feltöltött forrásattribútumokhoz. 

    | Név |  Forrás attribútuma|
    | ------------------- | -------------------- |
    | Felhasználó.Keresztnév  | user.givenname |
    | Felhasználó.e-mail  | user.mail |
    | PersonImmutableID       | user.userprincipalname    |
    | Felhasználó.Vezetéknév       | user.vezetéknév    |

    > [!NOTE]
    > Ehhez a folyamathoz az Active Directorynak legalább egy ellenőrzött egyéni tartománnyal kell konfigurálnia, valamint hozzáféréssel kell rendelkeznie a tartomány e-mail fiókjaihoz. Ha nem biztos benne, vagy segítségre van szüksége ezzel a konfigurációval kapcsolatban, kérjük, forduljon a SignalFx ügyfélszolgálatához.  

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában keresse meg a **Tanúsítvány (Base64)** elemet, és válassza a **Letöltés lehetőséget.** Töltse le a tanúsítványt, és mentse a számítógépre. Ezután másolja az **App Federation metaadat-url-címének** értékét; szüksége lesz erre az információra egy későbbi lépésben a SignalFx felhasználói felületen. 

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **SignalFx beállítása** szakaszban másolja az **Azure AD-azonosító** értékét. Erre az információra a SignalFx felhasználói felületének egy későbbi lépésében lesz szüksége. 

## <a name="step-4-create-an-azure-ad-test-user"></a>4. lépés: Azure AD-tesztfelhasználó létrehozása

Ezekkel az utasításokkal hozzon létre egy tesztfelhasználót a **B.Simon**nevű Azure portalon.

1. Az Azure Portalon a bal oldali navigációs ablakban válassza az **Azure Active Directory**lehetőséget, majd a **Felhasználók**lehetőséget, majd az **Összes felhasználó**lehetőséget.
1. A lap tetején válassza az **Új felhasználó**lehetőséget.
1. A **Felhasználó** tulajdonságai között:
   1. A **Felhasználónév** `username@companydomain.extension`mezőbe írja `b.simon@contoso.com`be a például a.
   1. Írja be `B.Simon`a **Név**mezőbe a .
   1. A **Jelszó megjelenítése megjelölése**, majd a Jelszó felirat ban megjelenő érték **másolása.** Szüksége lesz erre az információra a későbbi lépésben az integráció teszteléséhez. 
   1. Kattintson **a Létrehozás gombra.**

## <a name="step-5-assign-the-azure-ad-test-user"></a>5. lépés: Az Azure AD-teszt felhasználójának hozzárendelése

Ezekkel az utasításokkal engedélyezheti a tesztfelhasználónak, hogy egyetlen bejelentkezést használjon a SignalFx-hez.

1. Az Azure Portalon válassza a **Nagyvállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **SignalFx**lehetőséget.
1. Az alkalmazás áttekintése lapon keresse meg a **Kezelés szakaszt,** majd válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok**lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanel **Felhasználók** listájában válassza a **B.Simon**elemet, majd a lap alján kattintson a **Kijelölés gombra.**
1. Ha az SAML-feltételben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson a lap alján a **Kijelölés** gombra.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés gombra.**

## <a name="step-6-complete-the-signalfx-sso-configuration"></a>6. lépés: A SignalFx SSO konfiguráció befejezése 

1. Nyissa meg az előző lapot, és térjen vissza a SignalFx felhasználói felületére az aktuális Azure Active Directory-integrációs lap megtekintéséhez. 
1. A **Tanúsítvány (Base64)** elemre kattintson a **Fájl feltöltése**elemre, majd keresse meg az Azure Portalról korábban letöltött **Base64-alapú tanúsítványfájlt.**
1. Az **Azure AD-azonosító**mellett illessze be az **Azure AD-azonosító** értékét, amelyet korábban másolt az Azure Portalról. 
1. Az **összevonási metaadatok URL-címe**mellett illessze be az **App Federation metaadat-url-címét,** amelyet korábban másolt az Azure Portalról. 
1. Kattintson a **Mentés** gombra.

## <a name="step-7-test-sso"></a>7. lépés: SSO tesztelése

Tekintse át az alábbi információkat az Egyszeri bejelentkezés tesztelésére, valamint a SignalFx-be való első bejelentkezéssel kapcsolatos elvárásokra vonatkozóan. 

### <a name="test-logins"></a>Tesztbejelentkezések

* A bejelentkezés teszteléséhez használjon privát / inkognitóablakot, vagy jelentkezzen ki az Azure Portalon. Ha nem, az alkalmazást konfiguráló felhasználó cookie-jai zavarják és megakadályozzák a sikeres bejelentkezést a tesztfelhasználóval.

* Amikor egy új tesztfelhasználó először jelentkezik be, az Azure jelszómódosítást kényszerít. Ebben az esetben az SSO bejelentkezési folyamata nem fejeződik be; a tesztfelhasználó az Azure Portalra lesz irányítva. A hibaelhárításhoz a tesztfelhasználónak meg kell változtatnia a jelszavát, és meg kell navigálnia a SignalFx bejelentkezési lapjára vagy a hozzáférési panelre, és újra meg kell próbálnia.
    * Amikor a Hozzáférési panelen a SignalFx csempére kattint, automatikusan be kell jelentkeznie a SignalFx-be. 
        * A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

* A SignalFx alkalmazás a hozzáférési panelről vagy a szervezethez rendelt egyéni bejelentkezési oldalon keresztül érhető el. A tesztfelhasználónak tesztelnie kell az integrációt, amely ezek közül bármelyikhelyről indul.
    * A tesztfelhasználó használhatja a folyamat **b.simon@contoso.com**korábbi a.

### <a name="first-time-logins"></a>Első bejelentkezések

* Amikor egy felhasználó először jelentkezik be a SignalFx-be az SAML SSO-ból, a felhasználó egy SignalFx e-mailt kap egy linkkel. A felhasználónak hitelesítési célokra a hivatkozásra kell kattintania. Ez az e-mail ellenőrzés csak az első alkalommal felhasználók számára történik. 

* A SignalFx támogatja **a Just In Time** felhasználó létrehozását, ami azt jelenti, hogy ha a felhasználó nem létezik a SignalFx-ben, akkor a felhasználó fiókja az első bejelentkezési kísérletután jön létre.

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Mi a munkamenet-vezérlés a Microsoft Cloud App Security alkalmazásban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Próbálja ki a SignalFx-et az Azure AD-vel](https://aad.portal.azure.com/)
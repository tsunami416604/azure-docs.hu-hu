---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Trend Micro Web Security(TMWS) | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Trend Micro Web Security (TMWS) között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 827285d3-8e65-43cd-8453-baeda32ef174
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/21/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f4dc7223d64fd299da70375329260f7b4f8b322
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82083376"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-trend-micro-web-securitytmws"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Trend Micro Web Security(TMWS) rendszerrel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Trend Micro Web Security (TMWS) alkalmazást az Azure Active Directoryval (Azure AD). Ha integrálja a Trend Micro Web Security(TMWS) alkalmazást az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Trend Micro Web Security (TMWS) eléréséhez.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve trend Micro Web Security (TMWS) az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Trend Micro Web Security(TMWS) egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* A Trend Micro Web Security (TMWS) támogatja az **SP** által kezdeményezett sso-t
* A Trend Micro Web Security (TMWS) konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből terjed. [Megtudhatja, hogy miként kényszerítheti ki a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)

## <a name="adding-trend-micro-web-securitytmws-from-the-gallery"></a>Trend Micro Web Security (TMWS) hozzáadása a galériából

A Trend Micro Web Security (TMWS) Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Trend Micro Web Security (TMWS) trendet a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből szakaszban** írja be a **Trend Micro Web Security (TMWS)** kifejezést a keresőmezőbe.
1. Válassza **a Trend Micro Web Security (TMWS)** elemet az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-trend-micro-web-securitytmws"></a>Az Azure AD egyszeri bejelentkezésének konfigurálása és tesztelése a Trend Micro Web Security(TMWS) szolgáltatáshoz

Konfigurálja és tesztelje az Azure AD SSO-t a Trend Micro Web Security (TMWS) segítségével egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Trend Micro Web Security (TMWS) szolgáltatásban.

Az Azure AD SSO trendmikroweb-biztonsággal (TMWS) történő konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    1. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
    1. **[Felhasználói és csoportszinkronizálási beállítások konfigurálása az Azure AD-ben](#configure-user-and-group-synchronization-settings-in-azure-ad)** – Felhasználói és csoportszinkronizálási beállítások konfigurálása az Azure AD-ben
1. **[A Trend Micro Web Security(TMWS) egyszeri bejelentkezés konfigurálása](#configure-trend-micro-web-security-sso)** – az alkalmazás oldalon az egyszeri bejelentkezési beállítások konfigurálásához.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Trend Micro Web Security (TMWS)** alkalmazásintegrációs lapon keresse meg a Kezelés **szakaszt,** és válassza **az egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő minta használatával:`https://auth.iws-hybrid.trendmicro.com/([0-9a-f]{16})`

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet:`https://auth.iws-hybrid.trendmicro.com/simplesaml/module.php/saml/sp/saml2-acs.php/ics-sp`

    > [!NOTE]
    > Az azonosító értéke nem valós. Frissítse ezt az értéket a tényleges azonosítóval. Ezeket az értékeket **az Azure Felügyeleti portál szolgáltatásbeállításai** területen, az Azure AD **hitelesítési módszer** képernyőjén a Felügyeleti > a címtárszolgáltatások ból **szerezheti be.**

1. A Trend Micro Web Security (TMWS) alkalmazás az SAML-állításokat egy adott formátumban várja, amely megköveteli, hogy egyéni attribútumleképezéseket adjon hozzá az SAML token attribútumkonfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentieken kívül a Trend Micro Web Security (TMWS) alkalmazás arra számít, hogy néhány további attribútum ot kell visszaadni az SAML válaszban, amelyek az alábbiakban láthatók. Ezek az attribútumok is előre ki vannak töltve, de áttekintheti őket a követelmények nek megfelelően.
    
    | Name (Név) | Forrás attribútuma|
    | --------------- | --------- |
    | sAMAccountName | user.onpremisessamaccountname |
    | Upn | user.userprincipalname |

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában keresse meg a **Tanúsítvány (Base64)** lehetőséget, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Trend Micro Web Security (TMWS) beállítása** csoportban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés tanéven a Trend Micro Web Security (TMWS) használatával.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Trend Micro Web Security (TMWS) (Trend Micro Web Security(TMWS) lehetőséget.**
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="configure-user-and-group-synchronization-settings-in-azure-ad"></a>Felhasználói és csoportszinkronizálási beállítások konfigurálása az Azure AD-ben

1. A bal oldali navigációs sávon kattintson az **Azure Active Directory**elemre.

1. A **Kezelés csoportban**kattintson **az Alkalmazásregisztrációk** elemre, majd az új vállalati alkalmazáselemre a **Minden alkalmazás** területen.

1. A **Kezelés csoportban**kattintson **a Tanúsítványok & titkos kulcsok**elemre.

1. A megjelenő ügyféltitok-területen kattintson az **Új ügyféltitok**gombra.

1. A megjelenő Ügyféltitok hozzáadása képernyőn tetszés szerint adjon meg egy leírást, és válasszon egy lejárati időszakot ehhez az ügyféltitokhoz, majd kattintson a **Hozzáadás gombra.** Az újonnan hozzáadott ügyféltitok az Ügyféltitkos kulcsok területen jelenik meg.

1. Jegyezze fel az értéket. Később beírja az adatokat a TMWS-be.

1. A **Kezelés csoportban**kattintson **az API-engedélyek**elemre. 

1. A megjelenő API-engedélyek képernyőn kattintson az **Engedély hozzáadása**gombra.

1. A megjelenő API-engedélyek kérése képernyő Microsoft API-k lapján kattintson a **Microsoft Graph,** majd **az Alkalmazásengedélyek**elemre.

1. Keresse meg és adja hozzá a következő engedélyeket: 

    * Group.Read.All
    * User.Read.All

1. Kattintson **az Engedélyek hozzáadása gombra.** Megjelenik egy üzenet, amely megerősíti, hogy a beállítások mentése sikeresvolt. Az újonnan hozzáadott engedélyek megjelennek az API-engedélyek képernyőn.

1. A Támogatás hozzájárulása területen kattintson a Rendszergazdai hozzájárulás megadása a **rendszergazdai fiók < > (Alapértelmezett könyvtár)** elemre, majd az **Igen**gombra. Megjelenik egy üzenet, amely megerősíti, hogy a rendszergazda iadt meg a kért engedélyekhez való hozzájárulása sikeresen meglett adva.

1. Kattintson az **Áttekintés** elemre. 

1. A megjelenő jobb oldali ablaktáblában rögzítse az alkalmazás (ügyfél) azonosítóját és a címtár (bérlői) azonosítóját. Később beírja az adatokat a TMWS-be. Az Azure **Active Directory > Kezelése >** egyéni **tartománynevek** elemre is kattinthat, és rögzítheti a tartománynevet a jobb oldali ablaktáblában.

## <a name="configure-trend-micro-web-security-sso"></a>Trend Micro Web Biztonsági SSO konfigurálása

1. Jelentkezzen be a TMWS felügyeleti konzolra, és nyissa meg **a Felügyeleti** > **felhasználók & AUTHENTICATION** > **Directory Services**lehetőséget.

1. Kattintson ide a képernyő felső részén.

1. A megjelenő hitelesítési módszer képernyőn kattintson az **Azure AD**elemre.

1. Kattintson **a Be** vagy **ki gombra** annak eldöntéséhez, hogy a szervezet AD-felhasználói meglátogathatják-e a webhelyeket a TMWS-en keresztül, ha adataik nincsenek szinkronizálva a TMWS-sel.

    > [!NOTE]
    > Az Azure AD-ből nem szinkronizált felhasználók csak ismert TMWS-átjárókon vagy a szervezet dedikált portján keresztül hitelesíthetők.

1. Az **Identitásszolgáltató beállításai** csoportban hajtsa végre az alábbi lépéseket:

    a. A **Szolgáltatás URL-címe** mezőben illessze be a **bejelentkezési URL-értéket,** amelyet az Azure Portalról másolt.

    b. A **Bejelentkezési név attribútum** mezőbe illessze be a felhasználói jogcím nevét a **user.onpremisessamaccountname** forrásattribútummal az Azure Portalról.

    c. A **nyilvános SSL-tanúsítvány** mezőben használja a letöltött **tanúsítvány (Base64)** az Azure Portalon.

1. A **Szinkronizálási beállítások** csoportban hajtsa végre az alábbi lépéseket:

    a. A **Bérlő** mezőben használja **a Címtár (bérlői) azonosító** vagy **egyéni tartománynév** értékét az Azure Portalon.

    b. Az **Alkalmazásazonosító** mezőben az Azure Portal **alkalmazásazonosító-értéke.**

    c. Az **Ügyfél titkos** mezőjében használja az **Ügyféltitkos kulcsot** az Azure Portalról.

    d. A **Szinkronizálási ütemezés** mezőben válassza ki a szinkronizálást az Azure AD manuálisan vagy ütemezés szerint. Ha a Manuális lehetőséget választja, amikor az Active Directory felhasználói adatai módosulnak, ne felejtsen el visszatérni a Címtárszolgáltatások képernyőre, és manuális szinkronizálást végezni, hogy a TMWS-ben lévő információk naprakészek maradjanak.

    e. Kattintson **a Kapcsolat tesztelése** elemre, ha ellenőrizni szeretné, hogy az Azure AD szolgáltatás sikeresen csatlakoztatható-e. 
    
    f. Kattintson a **Save** (Mentés) gombra.
 
 > [!NOTE]
 > A Trend Micro Web Security Azure AD-vel való konfigurálásáról további információt ebben [a](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/directory-services/azure-active-directo/configuring-azure-ad.aspx) dokumentumban talál.

## <a name="test-sso"></a>SSO tesztelése 

Miután sikeresen konfigurálta az Azure AD-szolgáltatást, és megadta az Azure AD-t felhasználói hitelesítési módszerként, bejelentkezhet a TMWS proxykiszolgálóra a beállítás ellenőrzéséhez. Miután az Azure AD bejelentkezési ellenőrzése a fiók, akkor látogasson el az internetre.

> [!NOTE]
> A TMWS nem támogatja az egyszeri bejelentkezés tesztelését az Azure AD-portálról, az Áttekintés > egyszeri bejelentkezés > az egybejelentkezés beállítása az SAML-lel > az új vállalati alkalmazás tesztje.

1. Törölje a böngésző az összes cookie-kat, majd indítsa újra a böngészőt. 

1. Irányítsa a böngészőt a TMWS proxykiszolgálóra. További információt a [Pac-fájlok továbbítása](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/pac-files/traffic-forwarding-u.aspx#GUID-A4A83827-7A29-4596-B866-01ACCEDCC36B).

1. Látogasson el bármely internetes honlapon. A TMWS a TMWS fogságban lévő portáljára irányítja Önt.

1. Adjon meg egy Active Directory-fiókot (formátum: sAMAccountName@domaintartomány\sAMAccountName vagy ), vagy e-mail címet vagy ÁMN-t, majd kattintson **a Bejelentkezés gombra.** TMWS küld az Azure AD bejelentkezés.

1. Az Azure AD bejelentkezés, írja be az AD-fiók hitelesítő adatait. Sikeresen jelentkezzen be a TMWS szolgáltatásba.

## <a name="additional-resources"></a>További háttéranyagok

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a Trend Micro Web Security(TMWS) alkalmazást az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlés a Microsoft Cloud App Security alkalmazásban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [A Trend Micro Web Security (TMWS) védelme fejlett láthatósággal és vezérlőkkel](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)


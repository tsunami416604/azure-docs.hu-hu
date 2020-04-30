---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Trend Micro Web Security (TMWS) szolgáltatással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a Trend Micro Web Security (TMWS) között.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82083376"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-trend-micro-web-securitytmws"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Trend Micro Web Security (TMWS) szolgáltatással

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Trend Micro Web Security (TMWS) Azure Active Directory (Azure AD) szolgáltatással. A Trend Micro Web Security (TMWS) és az Azure AD integrálásával a következőket teheti:

* A Trend Micro Web Security (TMWS) szolgáltatáshoz hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Trend Micro Web Security (TMWS) szolgáltatásba az Azure AD-fiókokkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* A Trend Micro Web Security (TMWS) egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Trend Micro Web Security (TMWS) támogatja az **SP** által kezdeményezett SSO-t
* A Trend Micro Web Security (TMWS) konfigurálását követően kényszerítheti a munkamenet-vezérlést, amely valós időben biztosítja a szervezet bizalmas adatainak kiszűrése és beszivárgását. A munkamenet-vezérlő kiterjeszthető a feltételes hozzáférésből. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-trend-micro-web-securitytmws-from-the-gallery"></a>Trend Micro Web Security (TMWS) hozzáadása a gyűjteményből

A Trend Micro Web Security (TMWS) Azure AD-ba való integrálásának konfigurálásához hozzá kell adnia a Trend Micro Web Security (TMWS) szolgáltatást a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Trend Micro Web Security (TMWS)** kifejezést a keresőmezőbe.
1. Válassza a **Trend Micro Web Security (TMWS)** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-trend-micro-web-securitytmws"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a Trend Micro Web Security (TMWS) szolgáltatásban

Konfigurálja és tesztelje az Azure AD SSO-t a Trend Micro Web Security (TMWS) segítségével egy **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Trend Micro Web Security (TMWS) szolgáltatásban.

Az Azure AD SSO és a Trend Micro Web Security (TMWS) konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
    1. **[Felhasználói és csoport-szinkronizálási beállítások konfigurálása az Azure ad-ben](#configure-user-and-group-synchronization-settings-in-azure-ad)** – felhasználói és csoportos szinkronizálási beállítások konfigurálása az Azure ad-ben
1. A **[Trend Micro Web Security (TMWS) egyszeri](#configure-trend-micro-web-security-sso)** bejelentkezésének konfigurálása – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **Trend Micro Web Security (TMWS)** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://auth.iws-hybrid.trendmicro.com/([0-9a-f]{16})`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet:`https://auth.iws-hybrid.trendmicro.com/simplesaml/module.php/saml/sp/saml2-acs.php/ics-sp`

    > [!NOTE]
    > Az azonosító értéke nem valódi. Frissítse ezt az értéket a tényleges azonosítóval. Ezeket az értékeket az Azure AD-hez készült **hitelesítési módszer** képernyőn, az Azure Active **Directory szolgáltatásban a felügyeleti > címtárszolgáltatások** **lapon** érheti el.

1. A Trend Micro Web Security (TMWS) alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentieken kívül a Trend Micro Web Security (TMWS) alkalmazás néhány további attribútumot vár az SAML-válaszokban, amelyek alább láthatók. Ezek az attribútumok előre fel vannak töltve, de a követelményeinek megfelelően áttekintheti őket.
    
    | Name (Név) | Forrás attribútum|
    | --------------- | --------- |
    | sAMAccountName | User. onpremisessamaccountname |
    | uPN | User. userPrincipalName |

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Trend Micro Web Security (TMWS) beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőben adja meg a username@companydomain.extensionnevet. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás**gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést a Trend Micro Web Security (TMWS) elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **Trend Micro Web Security (TMWS)** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="configure-user-and-group-synchronization-settings-in-azure-ad"></a>Felhasználói és csoport szinkronizálási beállításainak konfigurálása az Azure AD-ben

1. A bal oldali navigációs sávon kattintson a **Azure Active Directory**elemre.

1. A **kezelés**alatt kattintson a **Alkalmazásregisztrációk** elemre, majd kattintson az új vállalati alkalmazásra a **minden alkalmazás** területen.

1. A **kezelés**alatt kattintson a **tanúsítványok & Secrets**elemre.

1. A megjelenő ügyfél-titkok területen kattintson az **új ügyfél titka**elemre.

1. A megjelenő ügyfél titkos kulcs hozzáadása képernyőn opcionálisan hozzáadhat egy leírást, és kiválaszthat egy lejárati időszakot az ügyfél titkos kódjához, majd kattintson a **Hozzáadás**gombra. Az újonnan hozzáadott ügyfél titkos kulcsa az ügyfél titkai területen jelenik meg.

1. Jegyezze fel az értéket. Később beírhatja az adatokat a TMWS-be.

1. A **kezelés**területen kattintson az **API-engedélyek**elemre. 

1. A megjelenő API-engedélyek képernyőn kattintson az **engedély hozzáadása**lehetőségre.

1. A megjelenő API-k kérése képernyő Microsoft API-k lapján kattintson a **Microsoft Graph** , majd az **alkalmazás engedélyei**elemre.

1. Keresse meg és adja hozzá a következő engedélyeket: 

    * Group.Read.All
    * Felhasználó. Read. All

1. Kattintson az **engedélyek hozzáadása**lehetőségre. Megjelenik egy üzenet, amely megerősíti, hogy a beállítások mentése sikeresen megtörtént. Az újonnan hozzáadott engedélyek az API-engedélyek képernyőn jelennek meg.

1. A jogosultság engedélyezése területen kattintson a **rendszergazdai jóváhagyás megadása lehetőségre < a rendszergazda fiókja > (alapértelmezett könyvtár)** elemre, majd az **Igen**gombra. Megjelenik egy üzenet, amely megerősíti, hogy a kért engedélyek rendszergazdai jóváhagyása sikeresen megtörtént.

1. Kattintson az **Áttekintés** elemre. 

1. A megjelenő jobb oldali ablaktáblában jegyezze fel az alkalmazás (ügyfél) AZONOSÍTÓját és könyvtárát (bérlői AZONOSÍTÓját). Később beírhatja az adatokat a TMWS-be. Az Azure Active Directory az **Egyéni tartománynevek** lehetőségre kattintva **> kezelheti** és rögzítheti a tartománynevet a jobb oldali ablaktáblán.

## <a name="configure-trend-micro-web-security-sso"></a>A Trend Micro Web Security SSO konfigurálása

1. Jelentkezzen be a TMWS felügyeleti konzolba, és lépjen az **adminisztrációs** > **felhasználók & Authentication** > **Directory Services**elemre.

1. Kattintson ide a képernyő felső részén.

1. A megjelenő hitelesítési módszer képernyőn kattintson az **Azure ad**elemre.

1. A **be** vagy **ki** lehetőségre kattintva eldöntheti, hogy engedélyezi-e a szervezet ad felhasználói számára, hogy TMWS a webhelyekre, ha az adataik nincsenek szinkronizálva a TMWS.

    > [!NOTE]
    > Az Azure AD-ből nem szinkronizált felhasználók csak az ismert TMWS-átjárók vagy a szervezet dedikált portja alapján hitelesíthetők.

1. Az **Identity Provider beállításai** szakaszban hajtsa végre a következő lépéseket:

    a. A **szolgáltatás URL-címe** mezőben illessze be a **bejelentkezési URL-címet** , amelyet átmásolt Azure Portal

    b. A **bejelentkezési név attribútum** mezőben illessze be a felhasználói jogcím nevét a **User. onpremisessamaccountname** forrás attribútummal a Azure Portal.

    c. A **nyilvános SSL-tanúsítvány** mezőben használja a letöltött **tanúsítványt (Base64)** a Azure Portal.

1. A **szinkronizálási beállítások** szakaszban hajtsa végre a következő lépéseket:

    a. A **bérlő** mezőben adja meg a **könyvtár (bérlő) azonosítóját** vagy az **Egyéni tartománynév** értékét a Azure Portalból.

    b. Az alkalmazás **azonosítója** mezőben az **alkalmazás (ügyfél) azonosítójának** értéke a Azure Portal.

    c. Az **ügyfél titkos kulcsa** mezőben használja az Azure Portal az **ügyfél titkos kulcsát** .

    d. A **szinkronizálási ütemterv** mezőben válassza az az Azure ad-vel manuálisan vagy az ütemterv szerint való szinkronizálást. Ha a manuálisan lehetőséget választja, valahányszor Active Directory felhasználói adatokat módosítanak, ne felejtsen vissza a címtárszolgáltatások képernyőre, és végezze el a manuális szinkronizálást, hogy a TMWS lévő információk továbbra is naprakészek maradjanak.

    e. Kattintson a **kapcsolat tesztelése** elemre annak ellenőrzéséhez, hogy az Azure ad szolgáltatás sikeresen csatlakoztatható-e. 
    
    f. Kattintson a **Save** (Mentés) gombra.
 
 > [!NOTE]
 > A Trend Micro Web Security Azure AD-vel való konfigurálásával kapcsolatos további információkért tekintse meg [ezt](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/directory-services/azure-active-directo/configuring-azure-ad.aspx) a dokumentumot.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Miután sikeresen konfigurálta az Azure AD szolgáltatást, és az Azure AD-t felhasználói hitelesítési módszerként adta meg, bejelentkezhet a TMWS a telepítés ellenőrzéséhez. Miután az Azure AD-bejelentkezés ellenőrizte a fiókját, látogasson el az internetre.

> [!NOTE]
> A TMWS nem támogatja az egyszeri bejelentkezés tesztelését az Azure AD-portálról, az Áttekintés > egyszeri bejelentkezés > az új vállalati alkalmazás SAML-> tesztelésével egyszeri bejelentkezést állíthat be.

1. Törölje az összes cookie böngészőjét, majd indítsa újra a böngészőt. 

1. Irányítsa a böngészőt a TMWS-proxy kiszolgálóra. Részletekért lásd: a [PAC Files használatával továbbított forgalom](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/pac-files/traffic-forwarding-u.aspx#GUID-A4A83827-7A29-4596-B866-01ACCEDCC36B).

1. Látogasson el bármelyik internetes webhelyre. A TMWS a TMWS kötött portálra irányítja.

1. Adja meg az Active Directory fiókot (Format: domain\sAMAccountName sAMAccountName@domainvagy), vagy e-mail-címet vagy egyszerű felhasználónevet, majd kattintson **a bejelentkezés**gombra. A TMWS elküldi az Azure AD-bejelentkezést.

1. Az Azure AD-bejelentkezésnél írja be az AD-fiók hitelesítő adatait. A TMWS-be való bejelentkezés sikeres.

## <a name="additional-resources"></a>További háttéranyagok

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [A Trend Micro Web Security (TMWS) kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [A Trend Micro Web Security (TMWS) védelme speciális láthatósággal és vezérlőkkel](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)


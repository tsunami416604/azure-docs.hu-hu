---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Palo Alto Networksrel – Admin UI | Microsoft dokumentumok'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Palo Alto Networks – Admin ui között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: a826eaec-15af-4c85-8855-8a3374d1efb9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/12/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f2fc9a23a8b0ef69760867dd5768510397f8570e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80129777"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---admin-ui"></a>Oktatóanyag: Az Azure Active Directory integrációja a Palo Alto-hálózatokkal – felügyeleti felhasználói felület

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Palo Alto Networks – Admin UI szolgáltatást az Azure Active Directoryval (Azure AD).
A Palo Alto Networks integrálása – A rendszergazdai felhasználói felület az Azure AD-vel a következő előnyökkel jár:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Palo Alto Networks - Admin UI.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve palo alto hálózatok - admin ui (single sign-on) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a Palo Alto Networks – Admin UI szolgáltatással a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* Palo Alto Networks - Admin UI egyszeri bejelentkezéses előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* Palo Alto Networks - Admin UI támogatja **SP** kezdeményezett SSO
* Palo Alto Networks - Admin UI támogatja **just in time** felhasználói kiépítés

## <a name="adding-palo-alto-networks---admin-ui-from-the-gallery"></a>Palo Alto Networks hozzáadása - Admin UI a galériából

A Palo Alto Networks - Admin UI azure AD-be való integrációjának konfigurálásához hozzá kell adnia a Palo Alto Networks - Admin UI-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből szakaszban** írja be a **Palo Alto Networks - Admin Felhasználói felület** kifejezést a keresőmezőbe.
1. Válassza a **Palo Alto Networks - Admin UI** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a Palo Alto Networks - Admin UI-val egy **B.Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Palo Alto Networks – Admin uI létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a Palo Alto Networks – Admin UI szolgáltatással a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    * **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    * **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja a Palo Alto Networks - Admin UI SSO](#configure-palo-alto-networks---admin-ui-sso)** -- konfigurálása az egyszeri bejelentkezési beállításokat az alkalmazás oldalán.
    * **[Hozzon létre Palo Alto Networks - Admin UI teszt felhasználó](#create-palo-alto-networks---admin-ui-test-user)** -, hogy egy megfelelője B.Simon palo alto hálózatok - admin felhasználói felület, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához a Palo Alto Networks – Admin Felhasználói felülettel hajtsa végre az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **Palo Alto Networks – Admin UI** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

1. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    a. A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<Customer Firewall FQDN>/php/login.php`

    b. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<Customer Firewall FQDN>:443/SAML20/SP`

    c. A **Válasz URL-cím** mezőjébe írja be a helyességifeltétel-fogyasztói szolgáltatás (ACS) URL-címét a következő formátumban:`https://<Customer Firewall FQDN>:443/SAML20/SP/ACS`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel, azonosítóval és válasz URL-címmel. Lépjen kapcsolatba [a Palo Alto Networks – Admin UI ügyféltámogatási csapatával,](https://support.paloaltonetworks.com/support) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.
    >
    > A 443-as port szükséges az **azonosítón** és a **Válasz URL-címen,** mivel ezek az értékek a Palo Alto tűzfalba vannak kódolva. Ha eltávolítja a portszámot, hiba lép fel a bejelentkezés során, ha eltávolítja őket.

    > A 443-as port szükséges az **azonosítón** és a **Válasz URL-címen,** mivel ezek az értékek a Palo Alto tűzfalba vannak kódolva. Ha eltávolítja a portszámot, hiba lép fel a bejelentkezés során, ha eltávolítja őket.

1. A PureCloud by Genesys alkalmazás az SAML-állításokat egy adott formátumban várja, amely megköveteli, hogy egyéni attribútumleképezéseket adjon hozzá az SAML token attribútumok konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

   > [!NOTE]
   > Mivel az attribútumértékek csak példák, rendelje hozzá a *felhasználónévhez* és a *rendszergazdai szerepkörhöz megfelelő értékeket.* Van egy másik választható attribútum, *accessdomain*, amely korlátozza a rendszergazdai hozzáférést a tűzfal adott virtuális rendszereihez.

1. A fentieken kívül a PureCloud by Genesys alkalmazás arra számít, hogy néhány további attribútum ot kell visszaadni az SAML válaszban, amelyek az alábbiakban láthatók. Ezek az attribútumok is előre ki vannak töltve, de áttekintheti őket a követelmények nek megfelelően.

    | Név |  Forrás attribútuma|
    | --- | --- |
    | felhasználónév | user.userprincipalname |
    | rendszergazdai szerepkör | customadmin |
    | | |

    > [!NOTE]
    > Az attribútumokról további információt az alábbi cikkekben talál:
    > * [Felügyeleti szerepkör-profil a felügyeleti felhasználói felülethez (adminrole)](https://www.paloaltonetworks.com/documentation/80/pan-os/pan-os/firewall-administration/manage-firewall-administrators/configure-an-admin-role-profile)
    > * [A felügyeleti felhasználói felület eszközelérési tartománya (accessdomain)](https://www.paloaltonetworks.com/documentation/80/pan-os/web-interface-help/device/device-access-domain)

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány csoportjában** kattintson a **Letöltés** gombra, ha letöltheti az **összevonási metaadat-XML-t** a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A **Palo Alto Networks beállítása – Felügyeleti felhasználói felület** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure-hirdetésazonosító

    c. Kijelentkezés URL-címe

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t a Palo Alto Networks - Admin UI hozzáférést biztosítva.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Palo Alto Networks - Admin UI**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="configure-palo-alto-networks---admin-ui-sso"></a>Palo Alto Networks konfigurálása - Admin UI SSO

1. Nyissa meg a Palo Alto Networks firewall felügyeleti felhasználói felületét rendszergazdaként egy új ablakban.

2. Válassza az **Eszköz** lapot.

    ![Az Eszköz lap](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin1.png)

3. A bal oldali ablaktáblában válassza az **SAML identitásszolgáltató**lehetőséget, majd az **Importálás** lehetőséget a metaadatfájl importálásához.

    ![A Metaadatfájl importálása gomb](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin2.png)

4. Az **SAML Azonosítsa a szolgáltatókiszolgálói profil importálását** ablakban tegye a következőket:

    ![Az "SAML azonosító szolgáltató kiszolgálói profilimportálása" ablak](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp.png)

    a. A **Profilnév** mezőben adjon meg egy nevet (például **AzureAD admin felhasználói felület).**

    b. Az **Identitásszolgáltató metaadatai csoportban**válassza a **Tallózás**lehetőséget, és jelölje ki a korábban letöltött metadata.xml fájlt az Azure Portalról.

    c. Törölje a jelet az **Identitásszolgáltató tanúsítványának ellenőrzése** jelölőnégyzetből.

    d. Válassza **az OK gombot.**

    e. A tűzfal on-in található konfigurációk véglegesítéséhez válassza a **Véglegesítés**lehetőséget.

5. A bal oldali ablaktáblában válassza az **SAML-identitásszolgáltató**lehetőséget, majd válassza ki az előző lépésben létrehozott SAML-identitásszolgáltatói profilt (például **Az AzureAD felügyeleti felhasználói felületét).**

    ![Az SAML identitásszolgáltató profilja](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp_select.png)

6. Az **SAML-identitásszolgáltató kiszolgálói profiljának ablakban** tegye a következőket:

    ![Az "SAML identity provider server profile" ablak](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_slo.png)
  
    a. Az **Identitásszolgáltató SLO URL-címe** mezőben cserélje le a korábban importált SLO URL-címet a következő URL-címre:`https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0`
  
    b. Válassza **az OK gombot.**

7. A Palo Alto Networks tűzfal felügyeleti felhasználói felületén válassza **az Eszköz**lehetőséget, majd a **Rendszergazdai szerepkörök**lehetőséget.

8. Kattintson a **Hozzáadás** gombra.

9. A **Rendszergazdai szerepkörprofil** ablak **Név** mezőjében adja meg a rendszergazdai szerepkör nevét (például **fwadmin).** A rendszergazdai szerepkör nevének meg kell egyeznie az identitásszolgáltató által küldött SAML felügyeleti szerepkör attribútumnevével. A rendszergazdai szerepkör neve és értéke az Azure Portal **Felhasználói attribútumok** szakaszában jött létre.

    ![Palo Alto Networks felügyeleti szerepkör konfigurálása](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_adminrole.png)
  
10. A tűzfal felügyeleti felhasználói felületén válassza az **Eszköz**lehetőséget, majd a **Hitelesítési profil lehetőséget.**

11. Kattintson a **Hozzáadás** gombra.

12. A **Hitelesítési profil ablakban** tegye a következőket: 

    ![A "Hitelesítési profil" ablak](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authentication_profile.png)

    a. A **Név** mezőben adjon meg egy nevet (például **AzureSAML_Admin_AuthProfile).**

    b. A **Típus** legördülő listában válassza az **SAML**lehetőséget. 

    c. Az **IdP-kiszolgálóprofil** legördülő listájában válassza ki a megfelelő SAML-identitásszolgáltató-kiszolgálóprofilt (például **AzureAD admin felhasználói felületet).**

    c. Jelölje be az **Egy kijelentkezés engedélyezése** jelölőnégyzetet.

    d. A **Felügyeleti szerepkör attribútuma** mezőbe írja be az attribútum nevét (például **adminrole).**

    e. Válassza a **Speciális** lapot, majd a **Lista engedélyezése csoportban**válassza a **Hozzáadás**lehetőséget.

    ![A Speciális lap Hozzáadás gombja](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_allowlist.png)

    f. Jelölje be a **Mind** jelölőnégyzetet, vagy jelölje be azokat a felhasználókat és csoportokat, amelyek ezzel a profillal hitelesíthetők.  
    Amikor egy felhasználó hitelesíti magát, a tűzfal megegyezik a társított felhasználónévvel vagy csoporttal a listában szereplő bejegyzésekkel. Ha nem ad hozzá bejegyzéseket, egyetlen felhasználó sem hitelesítheti magát.

    g. Válassza **az OK gombot.**

13. Ha engedélyezni szeretné, hogy a rendszergazdák saml sso-t használhassanak az Azure használatával, válassza **az Eszközbeállítása** > **Setup**lehetőséget. A **Telepítés** ablaktáblán válassza a **Kezelés** lapot, majd a **Hitelesítési beállítások**csoportban kattintson a **Beállítások** ("fogaskerék") gombra.

    ![A Beállítások gomb](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsetup.png)

14. Válassza ki a hitelesítési profil ablakban létrehozott SAML hitelesítési profilt (például **AzureSAML_Admin_AuthProfile).**

    ![A Hitelesítési profil mező](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsettings.png)

15. Válassza **az OK gombot.**

16. A konfiguráció véglegesítéséhez válassza a **Véglegesítés lehetőséget.**

### <a name="create-palo-alto-networks---admin-ui-test-user"></a>Palo Alto Networks létrehozása – Admin UI tesztfelhasználó

Palo Alto Networks – Admin UI támogatja a just-in-time felhasználói kiépítés. Ha a felhasználó még nem létezik, akkor a sikeres hitelesítés után automatikusan létrejön a rendszerben. A felhasználó létrehozásához nincs szükség műveletre.

### <a name="test-sso"></a>SSO tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha a Hozzáférési panelpalo Alto Networks - Admin UI csempéjére kattint, automatikusan be kell jelentkeznie a Palo Alto Networks - Admin felhasználói felületre, amelyhez beállítja az egyszeri bejelentkezést. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a Palo Alto Networks - Admin UI szolgáltatást az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlés a Microsoft Cloud App Security alkalmazásban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Hogyan védjük meg a Palo Alto Networks - Admin Felhasználói felületet a fejlett láthatóságés ellenőrzések](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
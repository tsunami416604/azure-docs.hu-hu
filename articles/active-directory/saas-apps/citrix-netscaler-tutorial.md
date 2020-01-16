---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses integráció a Citrix NetScaler (Kerberos-alapú hitelesítéssel) | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést (SSO) a Azure Active Directory és a Citrix NetScaler között Kerberos-alapú hitelesítés használatával.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: af501bd0-8ff5-468f-9b06-21e607ae25de
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/13/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 785242a2cf51571a6d13b2b4691d33e46369bf94
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75977913"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-citrix-netscaler-kerberos-based-authentication"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses integráció a Citrix NetScaler (Kerberos-alapú hitelesítés)

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Citrix NetScaler Azure Active Directory (Azure AD) használatával. Ha a Citrix NetScaler-t az Azure AD-vel integrálja, a következőket teheti:

* A Citrix NetScaler elérését biztosító Azure AD-beli vezérlés.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Citrix NetScaler-ba az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni a szolgáltatott szoftver (SaaS) alkalmazás Azure AD-integrációval kapcsolatban, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)használatával című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Citrix NetScaler egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben. Az oktatóanyag a következő forgatókönyveket tartalmazza:

* **SP – kezdeményezett** Egyszeri bejelentkezés a Citrix NetScaler

* **Felhasználói üzembe helyezés a Citrix** NetScaler

* [Kerberos-alapú hitelesítés Citrix NetScaler](#publish-the-web-server)

* [Fejléc-alapú hitelesítés Citrix NetScaler](header-citrix-netscaler-tutorial.md#publish-the-web-server)

## <a name="add-citrix-netscaler-from-the-gallery"></a>Citrix NetScaler hozzáadása a katalógusból

A Citrix NetScaler Azure AD-val való integrálásához először adja hozzá a Citrix NetScaler a felügyelt SaaS-alkalmazások listájához a katalógusból:

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).

1. A bal oldali menüben válassza a **Azure Active Directory**lehetőséget.

1. Lépjen a **vállalati alkalmazások**elemre, majd válassza a **minden alkalmazás**lehetőséget.

1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.

1. A **Hozzáadás a** katalógusból szakaszban adja meg a **Citrix NetScaler** kifejezést a keresőmezőbe.

1. Az eredmények között válassza ki a **Citrix NetScaler**elemet, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-citrix-netscaler"></a>Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a Citrix NetScaler-hez

Konfigurálja és tesztelje az Azure AD SSO-t a Citrix NetScaler egy **B. Simon**nevű tesztelési felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Citrix NetScaler-ben.

Az Azure AD SSO és a Citrix NetScaler konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az [Azure ad SSO konfigurálása](#configure-azure-ad-sso) – a funkció használatának engedélyezése a felhasználók számára.

    1. [Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user) – az Azure ad SSO teszteléséhez B. Simon használatával.

    1. [Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user) – hogy a B. Simon engedélyezze az Azure ad SSO használatát.

1. A [Citrix NETSCALER SSO konfigurálása](#configure-citrix-netscaler-sso) – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.

    * [Hozzon létre egy Citrix NetScaler-teszt felhasználót](#create-a-citrix-netscaler-test-user) – a felhasználó Azure ad-beli képviseletéhez kapcsolódó B. Simon-partneri kapcsolathoz a Citrix NetScaler.

1. [SSO tesztelése](#test-sso) – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Ha az Azure AD SSO-t a Azure Portal használatával szeretné engedélyezni, hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)a **Citrix NetScaler** Application Integration panel **kezelés**területén válassza az **egyszeri bejelentkezés**lehetőséget.

1. Az **egyszeri bejelentkezési módszer kiválasztása** panelen válassza az **SAML**lehetőséget.

1. Az **egyszeri bejelentkezés az SAML-vel** panelen való beállítása lapon válassza a toll **szerkesztése** ikont az **alapszintű SAML-konfigurációhoz** a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban az alkalmazás **identitásszolgáltató-kezdeményezésű** módban való konfigurálásához:

    1. Az **azonosító** szövegmezőben adjon meg egy URL-címet, amely a következő mintával rendelkezik: `https://<Your FQDN>`

    1. A **Válasz URL-címe** szövegmezőben adjon meg egy URL-címet, amely a következő mintával rendelkezik: `https://<Your FQDN>/CitrixAuthService/AuthService.asmx`

1. Az alkalmazás **SP-kezdeményezésű** módban való konfigurálásához válassza a **további URL-címek beállítása** lehetőséget, és végezze el a következő lépést:

    * A **bejelentkezési URL** szövegmezőben adjon meg egy URL-címet, amely a következő mintával rendelkezik: `https://<Your FQDN>/CitrixAuthService/AuthService.asmx`

    > [!NOTE]
    > * Az ebben a szakaszban használt URL-címek nem valós értékek. Frissítse ezeket az értékeket az azonosító, a válasz URL-cím és a bejelentkezési URL-cím tényleges értékeivel. Az értékek lekéréséhez forduljon a [Citrix NetScaler ügyfél-támogatási csapatához](https://www.citrix.com/contact/technical-support.html) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.
    > * Az egyszeri bejelentkezés beállításához az URL-címeknek elérhetőnek kell lenniük a nyilvános webhelyekről. Engedélyeznie kell a tűzfalat vagy más biztonsági beállításokat a Citrix NetScaler oldalon ahhoz, hogy enble az Azure AD-t, hogy elküldhesse a tokent a konfigurált URL-címen.

1. Az **egyszeri bejelentkezés SAML-panelen való beállításához** az **SAML aláíró tanúsítvány** szakaszban az **alkalmazás-összevonási metaadatok URL-címéhez**másolja ki az URL-címet, és mentse azt a Jegyzettömbben.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Citrix NetScaler beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali menüjében válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**lehetőséget, majd válassza a **minden felhasználó**lehetőséget.

1. Válassza az **új felhasználó** elemet a panel tetején.

1. A **felhasználói** tulajdonságok területen hajtsa végre a következő lépéseket:

   1. A **név**mezőbe írja be a következőt: `B.Simon`.  

   1. A **Felhasználónév**mezőbe írja be a következőt: _username@companydomain.extension_ . Például: `B.Simon@contoso.com`.

   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd jegyezze fel vagy másolja a **jelszóban**megjelenő értéket.

   1. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban engedélyezheti a B. Simon felhasználó számára az Azure SSO használatát azáltal, hogy hozzáférést biztosít a felhasználónak a Citrix NetScaler-hoz.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.

1. Az alkalmazások listában válassza a **Citrix NetScaler**elemet.

1. Az alkalmazás áttekintés területén a **kezelés**területen válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **Felhasználó hozzáadása** elemet. Ezután a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok**lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza ki az **B. Simon** elemet a **felhasználók** listából. Válassza a **Kiválasztás** lehetőséget

1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd válassza a **kiválasztás**lehetőséget.

1. A **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelés**lehetőséget.

## <a name="configure-citrix-netscaler-sso"></a>A Citrix NetScaler SSO konfigurálása

Válasszon egy hivatkozást a konfigurálni kívánt hitelesítési típushoz:

- [Citrix NetScaler SSO konfigurálása Kerberos-alapú hitelesítéshez](#publish-the-web-server)

- [A Citrix NetScaler SSO konfigurálása a fejléc-alapú hitelesítéshez](header-citrix-netscaler-tutorial.md#publish-the-web-server)

### <a name="publish-the-web-server"></a>A webkiszolgáló közzététele 

Virtuális kiszolgáló létrehozása:

1. Válassza a **Traffic Management** ** > terheléselosztás** > **szolgáltatások**lehetőséget.
    
1. Válassza a **Hozzáadás** lehetőséget.

    ![Citrix NetScaler Configuration-Services ablaktábla](./media/citrix-netscaler-tutorial/web01.png)

1. Állítsa be az alábbi értékeket az alkalmazásokat futtató webkiszolgálóra:

   * **Szolgáltatás neve**
   * **Kiszolgáló IP-címe/meglévő kiszolgáló**
   * **Protocol (Protokoll)**
   * **Port**

### <a name="configure-the-load-balancer"></a>A terheléselosztó konfigurálása

A terheléselosztó konfigurálása:

1. Lépjen a **Traffic Management** > **terheléselosztás** > **virtuális kiszolgálók**szolgáltatáshoz.

1. Válassza a **Hozzáadás** lehetőséget.

1. Állítsa be az alábbi értékeket a következő képernyőképen leírtak szerint:

    * **Name (Név)**
    * **Protocol (Protokoll)**
    * **IP-cím**
    * **Port**

1. Kattintson az **OK** gombra.

    ![Citrix NetScaler-konfiguráció – alapszintű beállítások panel](./media/citrix-netscaler-tutorial/load01.png)

### <a name="bind-the-virtual-server"></a>A virtuális kiszolgáló kötése

A terheléselosztó kötése a virtuális kiszolgálóval:

1. A **szolgáltatások és szolgáltatások csoport** ablaktáblán válassza a **nincs terheléselosztás virtuális kiszolgáló szolgáltatás kötése**elemet.

   ![Citrix NetScaler-konfiguráció – a virtuális kiszolgáló szolgáltatás kötési paneljének terheléselosztása](./media/citrix-netscaler-tutorial/bind01.png)

1. Ellenőrizze a beállításokat az alábbi képernyőképen látható módon, majd kattintson a **Bezárás**gombra.

   ![Citrix NetScaler konfiguráció – a Virtual Server Services-kötés ellenőrzése](./media/citrix-netscaler-tutorial/bind02.png)

### <a name="bind-the-certificate"></a>A tanúsítvány kötése

A szolgáltatás SSL-ként történő közzétételéhez kösse a kiszolgálói tanúsítványt, majd tesztelje az alkalmazást:

1. A **tanúsítvány**területen válassza a **nincs kiszolgálói tanúsítvány**lehetőséget.

   ![Citrix NetScaler-konfiguráció – kiszolgálói tanúsítvány panel](./media/citrix-netscaler-tutorial/bind03.png)

1. Ellenőrizze a beállításokat az alábbi képernyőképen látható módon, majd kattintson a **Bezárás**gombra.

   ![Citrix NetScaler konfiguráció – a tanúsítvány ellenőrzése](./media/citrix-netscaler-tutorial/bind04.png)

## <a name="citrix-adc-saml-profile"></a>Citrix ADC SAML-profil

A Citrix ADC SAML-profil konfigurálásához végezze el a következő szakaszt.

### <a name="create-an-authentication-policy"></a>Hitelesítési házirend létrehozása

Hitelesítési házirend létrehozása:

1. Nyissa meg a **biztonsági** > **AAA – alkalmazás-adatforgalom** > **házirendek** > a **hitelesítési** > **hitelesítési szabályzatok**lehetőséget.

1. Válassza a **Hozzáadás** lehetőséget.

1. A **hitelesítési házirend létrehozása** panelen adja meg vagy válassza ki a következő értékeket:

    * **Név**: adja meg a hitelesítési házirend nevét.
    * **Művelet**: írja be az **SAML**-t, majd kattintson a **Hozzáadás**gombra.
    * **Kifejezés**: adja meg az **igaz**értéket.     
    
    ![Citrix NetScaler konfiguráció – hitelesítési házirend létrehozása panel](./media/citrix-netscaler-tutorial/policy01.png)

1. Kattintson a **Létrehozás** gombra.

### <a name="create-an-authentication-saml-server"></a>Hitelesítési SAML-kiszolgáló létrehozása

A hitelesítési SAML-kiszolgáló létrehozásához lépjen a **hitelesítés SAML-kiszolgáló létrehozása** panelre, majd hajtsa végre a következő lépéseket:

1. A **név**mezőben adja meg a hitelesítési SAML-kiszolgáló nevét.

1. Az **SAML-metaadatok exportálása**területen:

   1. Jelölje be a **Metaadatok importálása** jelölőnégyzetet.

   1. Adja meg az összevonási metaadatok URL-címét a korábban másolt Azure SAML felhasználói felületről.
    
1. A **kiállító neve**mezőbe írja be a megfelelő URL-címet.

1. Kattintson a **Létrehozás** gombra.

![Citrix NetScaler Configuration – hitelesítés SAML-kiszolgáló létrehozása panel](./media/citrix-netscaler-tutorial/server01.png)

### <a name="create-an-authentication-virtual-server"></a>Hitelesítési virtuális kiszolgáló létrehozása

Hitelesítési virtuális kiszolgáló létrehozása:

1.  Lépjen a **biztonság** > **AAA-alkalmazás forgalmára** > **szabályzatok** > **Authentication** > a **hitelesítési virtuális kiszolgálók**elemre.

1.  Válassza a **Hozzáadás**lehetőséget, majd hajtsa végre a következő lépéseket:

    1. A **név**mezőben adja meg a hitelesítési virtuális kiszolgáló nevét.

    1. Jelölje be a **nem címezhető** jelölőnégyzetet.

    1. A **protokoll**területen válassza az **SSL**lehetőséget.

    1. Kattintson az **OK** gombra.
    
1. Válassza a **Folytatás** elemet.

### <a name="configure-the-authentication-virtual-server-to-use-azure-ad"></a>A hitelesítési virtuális kiszolgáló konfigurálása az Azure AD használatára

Két rész módosítása a hitelesítési virtuális kiszolgálóhoz:

1.  A **speciális hitelesítési házirendek** panelen válassza a **nincs hitelesítési házirend**elemet.

    ![Citrix NetScaler-konfiguráció – speciális hitelesítési házirendek panel](./media/citrix-netscaler-tutorial/virtual01.png)

1. A **házirend-kötés** ablaktáblán válassza ki a hitelesítési házirendet, majd válassza a **kötés**lehetőséget.

    ![Citrix NetScaler-konfiguráció – házirend-kötési panel](./media/citrix-netscaler-tutorial/virtual02.png)

1. Az **űrlap alapú virtuális kiszolgálók** ablaktáblán válassza a **nincs terheléselosztás virtuális kiszolgáló**elemet.

    ![Citrix NetScaler-konfiguráció – űrlap alapú virtuális kiszolgálók ablaktábla](./media/citrix-netscaler-tutorial/virtual03.png)

1. A **hitelesítési FQDN**mezőben adjon meg egy teljes tartománynevet (FQDN) (kötelező).

1. Válassza ki azt a terheléselosztási virtuális kiszolgálót, amelyet az Azure AD-hitelesítéssel szeretne védelemmel ellátni.

1. Válassza a **kötés**lehetőséget.

    ![Citrix NetScaler-konfiguráció – virtuális kiszolgáló kötési paneljének terheléselosztása](./media/citrix-netscaler-tutorial/virtual04.png)

    > [!NOTE]
    > Ügyeljen rá, hogy a **hitelesítés virtuális kiszolgáló konfigurációja** ablaktáblán válassza a **kész** lehetőséget.

1. A módosítások ellenőrzéséhez a böngészőben nyissa meg az alkalmazás URL-címét. A korábban látott nem hitelesített hozzáférés helyett a bérlő bejelentkezési lapját kell látnia.

    ![Citrix NetScaler Configuration – bejelentkezési oldal egy böngészőben](./media/citrix-netscaler-tutorial/virtual05.png)

## <a name="configure-citrix-netscaler-sso-for-kerberos-based-authentication"></a>Citrix NetScaler SSO konfigurálása Kerberos-alapú hitelesítéshez

### <a name="create-a-kerberos-delegation-account-for-citrix-adc"></a>Kerberos-delegálási fiók létrehozása Citrix ADC-hez

1. Hozzon létre egy felhasználói fiókot (ebben a példában a _AppDelegation_-t használjuk).

    ![Citrix NetScaler-konfiguráció-Tulajdonságok panel](./media/citrix-netscaler-tutorial/kerberos01.png)

1. GAZDAGÉP SPN beállítása ehhez a fiókhoz. 

    Például: `setspn -S HOST/AppDelegation.IDENTT.WORK identt\appdelegation`
    
    Ebben a példában:

    * `IDENTT.WORK` a tartomány teljes tartományneve.
    * `identt` a tartomány NetBIOS-neve.
    * `appdelegation` a delegálási felhasználói fiók neve.

1. Konfigurálja a webkiszolgáló delegálását az alábbi képernyőképen látható módon:
 
    ![Citrix NetScaler-konfiguráció – delegálás a Tulajdonságok panelen](./media/citrix-netscaler-tutorial/kerberos02.png)

    > [!NOTE]
    > A képernyőképen az a belső webkiszolgáló neve, amely a Windows integrált hitelesítés (WIA) helyet futtatja, a _CWEB2_.

### <a name="citrix-netscaler-aaa-kcd-kerberos-delegation-accounts"></a>Citrix NetScaler AAA KCD (Kerberos-delegálási fiókok)

A Citrix NetScaler AAA KCD fiók konfigurálása:

1.  Nyissa meg a **Citrix Gateway** > **AAA KCD (Kerberos által korlátozott delegálás) fiókokat**.

1.  Válassza a **Hozzáadás**lehetőséget, majd adja meg vagy válassza ki a következő értékeket:

    * **Név**: adja meg a KCD-fiók nevét.

    * **Tartomány: írja**be a tartományt és a bővítményt nagybetűvel.

    * **Szolgáltatás SPN**: `http/<host/fqdn>@<DOMAIN.COM>`.
    
        > [!NOTE]
        > `@DOMAIN.COM` szükséges, és csak nagybetűs lehet. Példa: `http/cweb2@IDENTT.WORK`.

    * **Delegált felhasználó**: adja meg a delegált felhasználó nevét.

    * Jelölje be a **delegált felhasználó jelszava** jelölőnégyzetet, majd írja be és erősítse meg a jelszót.

1. Kattintson az **OK** gombra.
 
    ![Citrix NetScaler konfiguráció – KCD-fiók konfigurálása panel](./media/citrix-netscaler-tutorial/kerberos03.png)

### <a name="citrix-traffic-policy-and-traffic-profile"></a>Citrix Traffic Policy és Traffic profil

A Citrix forgalmi házirend és a forgalmi profil konfigurálása:

1.  Lépjen a **biztonság** > **AAA-alkalmazás forgalmára** > **házirendek** > **forgalmi szabályzatok, profilok és űrlapos egyszeri bejelentkezéses ProfilesTraffic szabályzatok**.

1.  Válassza a **forgalmi profilok**lehetőséget.

1.  Válassza a **Hozzáadás** lehetőséget.

1.  A forgalmi profil konfigurálásához adja meg vagy válassza ki a következő értékeket.

    * **Név**: adja meg a forgalmi profil nevét.

    * **Egyszeri bejelentkezés**: válassza **a**be lehetőséget.

    * **KCD fiók**: válassza ki az előző szakaszban létrehozott KCD-fiókot.

1. Kattintson az **OK** gombra.

    ![Citrix NetScaler konfiguráció – forgalmi profil konfigurálása panel](./media/citrix-netscaler-tutorial/kerberos04.png)
 
1.  Válassza a **forgalmi szabályzat**lehetőséget.

1.  Válassza a **Hozzáadás** lehetőséget.

1.  A forgalmi szabályzatok konfigurálásához adja meg vagy válassza ki a következő értékeket:

    * **Név**: adja meg a forgalmi szabályzat nevét.

    * **Profil**: válassza ki az előző szakaszban létrehozott forgalmi profilt.

    * **Kifejezés**: adja meg az **igaz**értéket.

1. Kattintson az **OK** gombra.

    ![Citrix NetScaler konfiguráció – forgalmi szabályzat konfigurálása panel](./media/citrix-netscaler-tutorial/kerberos05.png)

### <a name="bind-a-traffic-policy-to-a-virtual-server-in-citrix"></a>Forgalmi szabályzat kötése Citrix virtuális kiszolgálóhoz

Forgalmi szabályzat kötése egy virtuális kiszolgálóhoz a grafikus felhasználói felület használatával:

1. Lépjen a **Traffic Management** > **terheléselosztás** > **virtuális kiszolgálók**szolgáltatáshoz.

1. A virtuális kiszolgálók listájában válassza ki azt a virtuális kiszolgálót, amelyhez az Újraírási házirendet kötni szeretné, majd válassza a **Megnyitás**lehetőséget.

1. A terheléselosztási **virtuális kiszolgáló** ablaktábla **Speciális beállítások**területén válassza a **házirendek**elemet. Az NetScaler-példányhoz konfigurált összes házirend megjelenik a listában.
 
    ![Citrix NetScaler-konfiguráció – virtuális kiszolgáló terheléselosztása panel](./media/citrix-netscaler-tutorial/kerberos06.png)

    ![Citrix NetScaler-konfiguráció – házirendek párbeszédpanel](./media/citrix-netscaler-tutorial/kerberos07.png)

1.  Jelölje be a virtuális kiszolgálóhoz kötni kívánt szabályzat neve melletti jelölőnégyzetet.
 
    ![Citrix NetScaler konfiguráció – virtuális kiszolgáló forgalmi házirendjének kötési paneljének terheléselosztása](./media/citrix-netscaler-tutorial/kerberos09.png)

1. A **típus kiválasztása** párbeszédpanelen:

    1. A **házirend kiválasztása**lapon válassza a **forgalom**lehetőséget.

    1. Válassza a **típus lehetőséget**, majd a **kérelem**lehetőséget.

    ![Citrix NetScaler konfiguráció – a típus panel kiválasztása](./media/citrix-netscaler-tutorial/kerberos08.png)

1. Ha a szabályzat kötve van, válassza a **kész**lehetőséget.
 
    ![Citrix NetScaler-konfiguráció – házirendek ablaktábla](./media/citrix-netscaler-tutorial/kerberos10.png)

1. Tesztelje a kötést a WIA webhely használatával.

    ![Citrix NetScaler Configuration – tesztoldal a böngészőben](./media/citrix-netscaler-tutorial/kerberos11.png)    

### <a name="create-a-citrix-netscaler-test-user"></a>Citrix NetScaler-teszt felhasználó létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználó jön létre a Citrix NetScaler. A Citrix NetScaler az igény szerinti felhasználói üzembe helyezést támogatja, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nem végezhető művelet. Ha egy felhasználó még nem létezik a Citrix NetScaler-ben, a rendszer egy újat hoz létre a hitelesítés után.

> [!NOTE]
> Ha manuálisan kell létrehoznia egy felhasználót, forduljon a [Citrix NetScaler ügyfél-támogatási csapatához](https://www.citrix.com/contact/technical-support.html).

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD SSO konfigurációját a hozzáférési panel használatával teszteli.

Amikor kiválasztja a Citrix NetScaler csempét a hozzáférési panelen, automatikusan be kell jelentkeznie a Citrix NetScaler, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [A Citrix NetScaler kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

- [A Citrix NetScaler egyszeri bejelentkezés konfigurálása a fejléc-alapú hitelesítéshez](header-citrix-netscaler-tutorial.md)

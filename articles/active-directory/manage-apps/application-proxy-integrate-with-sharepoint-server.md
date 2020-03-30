---
title: Távoli hozzáférés engedélyezése a SharePointhoz – Azure AD alkalmazásproxy
description: A helyszíni SharePoint-kiszolgáló azure AD alkalmazásproxyval való integrálásának alapjait ismerteti.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 983470994c103cb25d0d2aff96ae8544080e6288
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481296"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Távoli hozzáférés engedélyezése a SharePointhoz az Azure AD-alkalmazásproxy segítségével

Ez a részletes útmutató bemutatja, hogyan integrálható egy helyszíni SharePoint-farm az Azure Active Directory (Azure AD) alkalmazásproxyval.

## <a name="prerequisites"></a>Előfeltételek

A konfiguráció végrehajtásához a következő erőforrásokra van szükség:
- SharePoint 2013-farm vagy újabb.
- Egy Azure AD-bérlő egy csomag, amely tartalmazza az alkalmazásproxy. További információ az [Azure AD-csomagokról és a díjszabásról.](https://azure.microsoft.com/pricing/details/active-directory/)
- Egy [egyéni, ellenőrzött tartomány](../fundamentals/add-custom-domain.md) az Azure AD-bérlőben.
- A helyszíni Active Directory szinkronizálva van az Azure AD Connecttel, amelyen keresztül a felhasználók [bejelentkezhetnek az Azure-ba.](../hybrid/plan-connect-user-signin.md)
- Alkalmazásproxy-összekötő, amely a vállalati tartományon belüli számítógépen van telepítve és fut.

A SharePoint alkalmazásproxyval történő konfigurálásához két URL-re van szükség:
- Egy külső URL-cím, amely látható a végfelhasználók számára, és az Azure AD határozza meg. Ez az URL-cím egyéni tartományt használhat. További információ az [egyéni tartományok azure AD alkalmazásproxyban való együttműködéséről.](application-proxy-configure-custom-domain.md)
- Belső URL-cím, amely csak a vállalati tartományon belül ismert, és soha nem használt közvetlenül.

> [!IMPORTANT]
> A hivatkozások megfelelő leképezésének biztosításához kövesse a belső URL-re vonatkozó alábbi javaslatokat:
> - Https használata.
> - Ne használjon egyéni portokat.
> - A vállalati tartománynévrendszerben (DNS) hozzon létre egy állomást (A), amely a SharePoint WFE (vagy terheléselosztó) pontra mutat, nem pedig aliasra (CName).

Ez a cikk a következő értékeket használja:
- Belső URL:Belső URL:Internal URL:`https://sharepoint`
- Külső URL:External URL:`https://spsites-demo1984.msappproxy.net/`
- Alkalmazáskészlet-fiók a SharePoint-webalkalmazáshoz:`Contoso\spapppool`

## <a name="step-1-configure-an-application-in-azure-ad-that-uses-application-proxy"></a>1. lépés: Alkalmazásproxyt használó alkalmazás konfigurálása az Azure AD-ben

Ebben a lépésben hozzon létre egy alkalmazást az Azure Active Directory-bérlő, amely alkalmazásproxyt használ. Beállíthatja a külső URL-címet, és megadhatja a belső URL-címet, amelyet a SharePoint később használ.

1. Hozza létre az alkalmazást a következő beállításokkal leírtak szerint. Részletes útmutatást az Azure [AD alkalmazásproxyval használó alkalmazások közzététele.](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)
   * **Belső URL:** SharePoint belső URL-címe, amelykésőbb `https://sharepoint`be lesz állítva a SharePointban, például .
   * **Előhitelesítés**: Az Azure Active Directory
   * **URL-ek fordítása a Fejlécekben:** Nem
   * **URL-ek fordítása az alkalmazástörzsben:** Nem

   ![SharePoint közzététele alkalmazásként](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

1. Az alkalmazás közzététele után az alábbi lépésekkel konfigurálhatja az egyszeri bejelentkezési beállításokat:

   1. A portál alkalmazáslapján válassza az **Egyszeri bejelentkezés**lehetőséget.
   1. **Az egyszeri bejelentkezési mód esetén**válassza az Integrált **Windows-hitelesítés**lehetőséget.
   1. Állítsa a **belső alkalmazás spn-t** a korábban beállított értékre. Ebben a példában `HTTP/sharepoint`az érték .
   1. A **Delegált bejelentkezési identitás csoportban**válassza ki az Active Directory erdőkonfigurációjához legmegfelelőbb beállítást. Ha például egyetlen Active Directory-tartomány van az erdőben, válassza **a Helyszíni SAM-fiók nevét** (ahogy az a következő képernyőképen látható). Ha azonban a felhasználók nem ugyanabban a tartományban vannak, mint a SharePoint és az Application Proxy Connector kiszolgálók, válassza a **Helyszíni egyszerű felhasználónevet** (nem jelenik meg a képernyőképen).

   ![Integrált Windows-hitelesítés konfigurálása az sso-hoz](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

1. Az alkalmazás beállításának befejezéséhez lépjen a **Felhasználók és csoportok** szakaszra, és rendelje hozzá a felhasználókat az alkalmazás eléréséhez. 

## <a name="step-2-configure-the-sharepoint-web-application"></a>2. lépés: A SharePoint-webalkalmazás konfigurálása

A SharePoint-webalkalmazást a Kerberos-szal és a megfelelő alternatív hozzáférési leképezésekkel kell konfigurálni az Azure AD alkalmazásproxyval való megfelelő munka érdekében. Két lehetőség közül választhat:

- Hozzon létre egy új webalkalmazást, és csak az Alapértelmezett zónát használja. Ez az előnyben részesített lehetőség, mivel a SharePoint szolgáltatással a legjobb élményt nyújtja (például a SharePoint által létrehozott e-mail-riasztásokban szereplő hivatkozások mindig az Alapértelmezett zónára mutatnak).
- Meglévő webalkalmazás bővítése a Kerberos nem alapértelmezett zónában történő konfigurálásához.

> [!IMPORTANT]
> A használt zónától függetlenül a SharePoint-webalkalmazás alkalmazáskészlet-fiókjának tartományfióknak kell lennie ahhoz, hogy a Kerberos megfelelően működjön.

### <a name="provision-the-sharepoint-web-application"></a>A SharePoint-webalkalmazás kiépítése

- Ha új webalkalmazást hoz létre, és csak az Alapértelmezett zónát használja (előnyben részesített beállítás):

    1. Indítsa el a **SharePoint Felügyeleti rendszerhéjat,** és futtassa a következő parancsfájlt:

       ```powershell
       # This script creates a web application and configures the Default zone with the internal/external URL needed to work with Azure AD Application Proxy
       # Edit variables below to fit your environment. Note that the managed account must exist and it must be a domain account
       $internalUrl = "https://sharepoint"
       $externalUrl = "https://spsites-demo1984.msappproxy.net/"
       $applicationPoolManagedAccount = "Contoso\spapppool"
            
       $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
       $wa = New-SPWebApplication -Name "SharePoint - AAD Proxy" -Port 443 -SecureSocketsLayer -URL $externalUrl -ApplicationPool "SharePoint - AAD Proxy" -ApplicationPoolAccount (Get-SPManagedAccount $applicationPoolManagedAccount) -AuthenticationProvider $winAp
       New-SPAlternateURL -Url $internalUrl -WebApplication $wa -Zone Default -Internal
       ```

    2. Nyissa meg a **SharePoint központi felügyelet** webhelyét.
    1. A **Rendszerbeállítások**csoportban válassza **az Alternatív hozzáférési hozzárendelések konfigurálása**lehetőséget. Megnyílik **az Alternatív hozzáférés-hozzárendelési gyűjtemény** mező.
    1. Szűrje a kijelzőt az új webalkalmazással, és erősítse meg, hogy valami ilyesmit lát:

       ![A webalkalmazás alternatív hozzáférés-leképezései](./media/application-proxy-integrate-with-sharepoint-server/new-webapp-aam.png)

- Ha egy meglévő webalkalmazást új zónára terjeszt ki (abban az esetben, ha nem tudja használni az Alapértelmezett zónát):

    1. Indítsa el a SharePoint Felügyeleti rendszerhéjat, és futtassa a következő parancsfájlt:

       ```powershell
       # This script extends an existing web application to Internet zone with the internal/external URL needed to work with Azure AD Application Proxy
       # Edit variables below to fit your environment
       $webAppUrl = "http://spsites/"
       $internalUrl = "https://sharepoint"
       $externalUrl = "https://spsites-demo1984.msappproxy.net/"
       
       $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
       $wa = Get-SPWebApplication $webAppUrl
       New-SPWebApplicationExtension -Name "SharePoint - AAD Proxy" -Identity $wa -SecureSocketsLayer -Zone Extranet -Url $externalUrl -AuthenticationProvider $winAp
       New-SPAlternateURL -Url $internalUrl -WebApplication $wa -Zone Extranet -Internal
       ```

    2. Nyissa meg a **SharePoint központi felügyelet** webhelyét.
    1. A **Rendszerbeállítások**csoportban válassza **az Alternatív hozzáférési hozzárendelések konfigurálása**lehetőséget. Megnyílik **az Alternatív hozzáférés-hozzárendelési gyűjtemény** mező.
    1. Szűrje a kijelzőt a kiterjesztett webalkalmazással, és erősítse meg, hogy valami ilyesmit lát:

        ![Bővített alkalmazás alternatív hozzáférés-leképezései](./media/application-proxy-integrate-with-sharepoint-server/extend-webapp-aam.png)

### <a name="make-sure-the-sharepoint-web-application-is-running-under-a-domain-account"></a>Annak megkelljejetennie, hogy a SharePoint-webalkalmazás tartományi fiók alatt fusson

A SharePoint-webalkalmazás alkalmazáskészletét futtató fiók azonosításához és tartományi fiók biztosításához kövesse az alábbi lépéseket:

1. Nyissa meg a **SharePoint központi felügyelet** webhelyét.
1. Nyissa meg a **Biztonság** lehetőséget, és válassza **a Szolgáltatásfiókok konfigurálása**lehetőséget.
1. Válassza ki **a webalkalmazás-készletet - YourWebApplicationName**.

   ![Szolgáltatási fiók konfigurálásának lehetőségei](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

1. Válassza **ki a fiókot ehhez az összetevőhöz** visszaad egy tartományi fiókot, és ne feledje, mivel a következő lépésben szükség lesz rá.

### <a name="make-sure-that-an-https-certificate-is-configured-for-the-iis-site-of-the-extranet-zone"></a>Győződjön meg arról, hogy az Extranet zóna IIS-helyéhez HTTPS-tanúsítvány van konfigurálva

Mivel a belső URL`https://SharePoint/`HTTPS protokollt ( használja ), a tanúsítványt az Internet Information Services (IIS) webhelyen kell beállítani.

1. Nyissa meg a Windows PowerShell konzolt.
1. Futtassa a következő parancsfájlt egy önaláírt tanúsítvány létrehozásához, és adja hozzá a számítógép SAJÁT tárolójához:

   ```powershell
   # Replace "SharePoint" with the actual hostname of the Internal URL of your Azure AD proxy application
   New-SelfSignedCertificate -DnsName "SharePoint" -CertStoreLocation "cert:\LocalMachine\My"
   ```

   > [!IMPORTANT]
   > Az önaláírt tanúsítványok csak tesztelési célokra alkalmasak. Éles környezetben azt javasoljuk, hogy használja a hitelesítésszolgáltató által kiállított tanúsítványokhelyett.

1. Nyissa meg az Internet Information Services Manager konzolt.
1. Bontsa ki a kiszolgálót a fanézetben, bontsa ki a **Webhelyek csomópontot,** jelölje ki a **SharePoint – AAD proxy** webhelyet, és válassza a **Kötések**lehetőséget.
1. Válassza a **https binding** lehetőséget, majd a **Szerkesztés**lehetőséget.
1. A TLS/SSL tanúsítvány mezőben válassza a **SharePoint-tanúsítvány** lehetőséget, majd kattintson az **OK gombra.**

Most már elérheti a SharePoint-webhely külsőleg az Azure AD alkalmazásproxy.

## <a name="step-3-configure-kerberos-constrained-delegation"></a>3. lépés: A Kerberos korlátozott delegálásának konfigurálása

A felhasználók először hitelesíti az Azure AD, majd a SharePoint kerberos használatával az Azure AD proxy összekötőn keresztül. Ahhoz, hogy az összekötő kerberos-jogkivonatot szerezzen be az Azure AD-felhasználó nevében, konfigurálnia kell a Kerberos korlátozott delegálást (KCD) protokollátmenettel. Ha többet szeretne megtudni a KCD-ről, olvassa el a [Kerberos korlátozott delegálás – áttekintés című témakört.](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11))

### <a name="set-the-spn-for-the-sharepoint-service-account"></a>A SharePoint szolgáltatásfiók spéjének beállítása

Ebben a cikkben a `https://sharepoint`belső URL-cím a , és `HTTP/sharepoint`így a szolgáltatásegyszerű neve (SPN) a . Ezeket az értékeket a környezetnek megfelelő értékekre kell cserélnie.
Ha regisztrálni `HTTP/sharepoint` szeretné az SPN-t a SharePoint alkalmazáskészlet-fiókhoz, `Contoso\spapppool`futtassa a következő parancsot a parancssorból a tartomány rendszergazdájaként:

`setspn -S HTTP/sharepoint Contoso\spapppool`

A `Setspn` parancs megkeresi az spn-t, mielőtt hozzáadja. Ha az spn már létezik, **ismétlődő SPN-érték** hiba jelenik meg. Ebben az esetben fontolja meg a meglévő spn eltávolítását, ha nincs beállítva a megfelelő alkalmazáskészlet-fiók alatt. Az spn hozzáadását ellenőrizheti, ha `Setspn` a parancsot a -L kapcsolóval futtatja. A parancsról további információ: [Setspn](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc731241(v=ws.11)).

### <a name="make-sure-the-connector-is-trusted-for-delegation-to-the-spn-that-was-added-to-the-sharepoint-application-pool-account"></a>Annak biztosítása, hogy az összekötő megbízható a SharePoint-alkalmazáskészletfiókhoz hozzáadott SPN-ben való delegáláshoz

Konfigurálja a KCD-t úgy, hogy az Azure AD alkalmazásproxy szolgáltatás képes felhasználói identitásokat delegálni a SharePoint-alkalmazáskészlet-fiókba. Konfigurálja a KCD-t azáltal, hogy engedélyezi az Alkalmazásproxy-összekötő kerberos-jegyek beolvasását az Azure AD-ben hitelesített felhasználók számára. Ezután a kiszolgáló átadja a környezetet a célalkalmazásnak (ebben az esetben a SharePoint).

A KCD konfigurálásához kövesse az alábbi lépéseket minden egyes összekötő gépen:

1. Jelentkezzen be a tartományvezérlőre tartományi rendszergazdaként, majd nyissa meg az Active Directory – felhasználók és számítógépek beépülő modult.
1. Keresse meg az Azure AD-proxy összekötőt futtató számítógépet. Ebben a példában maga a SharePoint-kiszolgáló.
1. Kattintson duplán a számítógépre, majd válassza a **Delegálás** lapot.
1. Győződjön meg arról, hogy a delegálási beállítások a **Megbízható számítógép csak a megadott szolgáltatásokra való delegáláshoz**beállításra vannak állítva. Ezután válassza **a Bármely hitelesítési protokoll használata lehetőséget.**
1. Kattintson a **Hozzáadás** gombra, válassza a **Felhasználók vagy számítógépek**lehetőséget, és keresse meg a SharePoint alkalmazáskészlet-fiókot. Például: `Contoso\spapppool`.
1. Az SPN-ek listájában válassza ki azt, amelyet korábban létrehozott a szolgáltatásfiókhoz.
1. A módosítások mentéséhez válassza **az OK** gombot, majd ismét az **OK** gombot.
  
   ![Delegálási beállítások](./media/application-proxy-integrate-with-sharepoint-server/delegation-box2.png)

Most már készen áll a SharePointba való bejelentkezésre a külső URL-cím használatával, és az Azure-ral való hitelesítéshez.

## <a name="troubleshoot-sign-in-errors"></a>Bejelentkezési hibák elhárítása

Ha a webhelyre való bejelentkezés nem működik, az összekötőnaplókban további információt kaphat a problémáról: Az összekötőt futtató gépről nyissa meg az eseménynaplót, nyissa meg a**Microsoft** > **AadApplicationProxy** > **Connector**alkalmazások és szolgáltatások **naplója** > lapot, és vizsgálja meg a **felügyeleti** naplót.

## <a name="next-steps"></a>További lépések

* [Egyéni tartományok használata az Azure AD alkalmazásproxyban](application-proxy-configure-custom-domain.md)
* [Az Azure AD alkalmazásproxy-összekötők megismerése](application-proxy-connectors.md)

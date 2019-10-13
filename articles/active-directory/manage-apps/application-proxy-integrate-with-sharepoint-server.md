---
title: Távoli hozzáférés engedélyezése a SharePointhoz az Azure AD Application Proxy használatával | Microsoft Docs
description: Ismerteti a helyszíni SharePoint Server és az Azure AD Application Proxy integrálásának alapjait.
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
ms.openlocfilehash: a8f9cba1072866a3efdeb7b99981d0bfda22ab00
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286110"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Távoli hozzáférés engedélyezése a SharePointhoz az Azure AD-alkalmazásproxy segítségével

Ez a lépésenkénti útmutató ismerteti, hogyan integrálhat egy helyszíni SharePoint-farmot Azure Active Directory (Azure AD) alkalmazásproxy használatával.

## <a name="prerequisites"></a>Előfeltételek

A konfiguráció végrehajtásához a következő erőforrásokra van szükség:
- Egy SharePoint 2013-Farm vagy újabb.
- Egy Azure Active Directory bérlő egy olyan csomaggal, amely alkalmazásproxy-t tartalmaz. További információ a [Azure Active Directory-csomagokról és a díjszabásról](https://azure.microsoft.com/pricing/details/active-directory/).
- Egy [Egyéni ellenőrzött tartomány](../fundamentals/add-custom-domain.md) az Azure ad-bérlőben.
- A helyszíni Active Directory szinkronizálva Azure AD Connectekkel, akik képesek [bejelentkezni az Azure](../hybrid/plan-connect-user-signin.md)-ba.
- Egy, a vállalati tartományon belüli gépen telepített és futó alkalmazásproxy-összekötő.

A SharePoint és az alkalmazásproxy konfigurálásához két URL-cím szükséges:
- Egy külső URL-cím, amely a végfelhasználók számára látható és Azure Active Directoryban van meghatározva. Ez az URL-cím egyéni tartományt is használhat. További információ az [Egyéni tartományok Azure ad Application proxy-beli használatáról](application-proxy-configure-custom-domain.md).
- Belső URL-cím, amely csak a vállalati tartományon belül ismert, és soha nem használható közvetlenül.

> [!IMPORTANT]
> A hivatkozások megfelelő leképezésének biztosításához kövesse az alábbi javaslatokat a belső URL-címhez:
> - HTTPS használata
> - Ne használjon egyéni portokat
> - A vállalati DNS-ben hozzon létre egy gazdagépet (A), hogy a SharePoint-WFE (vagy a Load Balancer) mutasson, és ne egy aliast (CName).

Ebben a cikkben a következő értékeket fogjuk használni:
- Belső URL-cím: `https://sharepoint`
- Külső URL-cím: `https://spsites-demo1984.msappproxy.net/`
- Alkalmazáskészlet-fiók a SharePoint-webalkalmazáshoz: `Contoso\spapppool`

## <a name="step-1-configure-an-application-in-azure-ad-that-uses-application-proxy"></a>1\. lépés: alkalmazás konfigurálása az Azure AD-ben, amely alkalmazásproxy-t használ

Ebben a lépésben létrehoz egy alkalmazást a Azure Active Directory-bérlőben, amely az alkalmazásproxy használatát fogja használni. A külső URL-címet kell megadnia, és meg kell adnia a belső URL-címet, amelyet a SharePoint később használ.

1. Hozza létre az alkalmazást a következő beállításokkal leírtak szerint. Részletes útmutatásért lásd: [alkalmazások közzététele az Azure ad Application proxy használatával](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).
   * **Belső URL-cím**: a SharePoint belső URL-címe, amelyet később a SharePointban kell beállítani, például `https://sharepoint`.
   * **Előhitelesítési módszer**: Azure Active Directory
   * **URL-cím lefordítása a fejlécekben**: nem
   * **URL-cím fordítása az alkalmazás törzsében**: nem

   ![SharePoint alkalmazásként való közzététele](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

1. Miután közzétette az alkalmazást, konfigurálja az egyszeri bejelentkezési beállításokat a következő lépésekkel:

   1. A portál alkalmazás lapján válassza az **egyszeri bejelentkezés**lehetőséget.
   1. **Egyszeri bejelentkezési mód**esetén válassza az **integrált Windows-hitelesítés**lehetőséget.
   1. A **belső alkalmazás SPN** beállítása a korábban megadott értékre. Ebben a példában az érték `HTTP/sharepoint`.
   1. A **delegált bejelentkezési identitás**területen válassza ki a Active Directory erdő konfigurációjának legmegfelelőbb lehetőségét. Ha például egyetlen AD-tartománya van az erdőben, válassza ki **a helyszíni Sam-fiók nevét** (az alább látható módon), de ha a felhasználók nem ugyanabban a tartományban találhatók, mint a SharePoint és az alkalmazásproxy-összekötő kiszolgálók, válassza **a helyszíni felhasználói tag lehetőséget. név** (nem látható).

   ![Integrált Windows-hitelesítés konfigurálása egyszeri bejelentkezéshez](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

1. Az alkalmazás beállításának befejezéséhez nyissa meg a **felhasználók és csoportok** szakaszt, és rendelje hozzá a felhasználókat az alkalmazás eléréséhez. 

## <a name="step-2-configure-the-sharepoint-web-application"></a>2\. lépés: a SharePoint-webalkalmazás konfigurálása

A SharePoint-webalkalmazást Kerberos-kapcsolattal kell konfigurálni, valamint a megfelelő alternatív hozzáférés-leképezéseket, hogy megfelelően működjenek az Azure AD Application Proxy. Két lehetőség közül választhat:

1. Hozzon létre egy új webalkalmazást, és csak az alapértelmezett zónát használja. Ez az előnyben részesített lehetőség, hogy a SharePoint (például a SharePoint által létrehozott e-mail-riasztások hivatkozásai mindig az alapértelmezett zónára mutassanak) a lehető leghatékonyabbak legyenek.
1. Meglévő webalkalmazás kiterjesztése a Kerberos konfigurálására nem alapértelmezett zónában.

> [!IMPORTANT]
> A használni kívánt zónától függetlenül a SharePoint-webalkalmazás alkalmazáskészlet-fiókjának egy tartományi fióknak kell lennie a Kerberos megfelelő működéséhez.

### <a name="provision-the-sharepoint-web-application"></a>A SharePoint-webalkalmazás kiépítése

- Ha új webalkalmazást hoz létre, és csak az alapértelmezett zónát használja (előnyben részesített beállítás):

    1. Indítsa el a **SharePoint felügyeleti rendszerhéjat** , és futtassa a következő parancsfájlt:

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

    1. Nyissa meg a **SharePoint központi felügyeleti** webhelyet.
    1. A **Rendszerbeállítások**területen válassza a **másodlagos hozzáférés-leképezések konfigurálása**elemet. Megnyílik a másodlagos hozzáférés-leképezések mező.
    1. Szűrje a kijelzőt az új webalkalmazással, és erősítse meg, hogy a következőképpen néz ki:

       ![Webalkalmazás másodlagos hozzáférés-leképezései](./media/application-proxy-integrate-with-sharepoint-server/new-webapp-aam.png)

- Ha egy meglévő webalkalmazást új zónára terjeszt (ha nem tudja használni az alapértelmezett zónát):

    1. Indítsa el a **SharePoint felügyeleti rendszerhéjat** , és futtassa a következő parancsfájlt:

       ```powershell
       # This scripts extends an existing web application to Internet zone with the internal/external URL needed to work with Azure AD Application Proxy
       # Edit variables below to fit your environment
       $webAppUrl = "http://spsites/"
       $internalUrl = "https://sharepoint"
       $externalUrl = "https://spsites-demo1984.msappproxy.net/"
       
       $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
       $wa = Get-SPWebApplication $webAppUrl
       New-SPWebApplicationExtension -Name "SharePoint - AAD Proxy" -Identity $wa -SecureSocketsLayer -Zone Extranet -Url $externalUrl -AuthenticationProvider $winAp
       New-SPAlternateURL -Url $internalUrl -WebApplication $wa -Zone Extranet -Internal
       ```

    1. Nyissa meg a **SharePoint központi felügyeleti** webhelyet.
    1. A **Rendszerbeállítások**területen válassza a **másodlagos hozzáférés-leképezések konfigurálása**elemet. Megnyílik a másodlagos hozzáférés-leképezések mező.
    1. Szűrje a kijelzőt a kibővített webalkalmazással, és erősítse meg, hogy a következőhöz hasonló:

        ![Kiterjesztett alkalmazás másodlagos hozzáférés-leképezései](./media/application-proxy-integrate-with-sharepoint-server/extend-webapp-aam.png)

### <a name="ensure-that-the-sharepoint-web-application-is-running-under-a-domain-account"></a>Győződjön meg arról, hogy a SharePoint-webalkalmazás egy tartományi fiók alatt fut

A következő lépésekkel azonosíthatja a SharePoint-webalkalmazás alkalmazáskészletét futtató fiókot, és biztosíthatja, hogy az tartományi fiók legyen:

1. Nyissa meg a **SharePoint központi felügyeleti** webhelyet.
1. Lépjen a **Biztonság** elemre, és válassza a **szolgáltatásfiókok konfigurálása**lehetőséget.
1. Válassza a **webalkalmazás-készlet – YourWebApplicationName**elemet.

   ![A szolgáltatásfiók konfigurálásának lehetőségei](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

1. Győződjön meg arról, hogy az **összetevőhöz tartozó fiók kiválasztása** tartományi fiókot ad vissza, és jegyezze meg, mert a következő lépésben szükség lesz rá.

### <a name="ensure-that-an-https-certificate-is-configured-for-the-iis-site-of-the-extranet-zone"></a>Győződjön meg arról, hogy a HTTPS-tanúsítvány konfigurálva van az extranet zóna IIS-helyéhez

Mivel a belső URL-cím HTTPS protokollt használ (`https://SharePoint/`), egy tanúsítványt kell beállítani az IIS-helyen.

1. Nyissa meg a Windows PowerShell-konzolt.
1. Futtassa az alábbi szkriptet egy önaláírt tanúsítvány létrehozásához, és adja hozzá a saját tárolóhoz:

   ```powershell
   # Replace "SharePoint" with the actual hostname of the Internal URL of your Azure AD proxy application
   New-SelfSignedCertificate -DnsName "SharePoint" -CertStoreLocation "cert:\LocalMachine\My"
   ```

   > [!IMPORTANT]
   > Az önaláírt tanúsítványok csak tesztelési célokra használhatók. Éles környezetekben a hitelesítésszolgáltató által kiadott tanúsítványok használata erősen ajánlott.

1. Nyissa meg a "Internet Information Services Manager" konzolt.
1. Bontsa ki a kiszolgálót a fanézetben, bontsa ki a "helyek" elemet, válassza ki a "SharePoint-HRE proxy" helyet, és kattintson a **kötések**elemre.
1. Válassza a https-kötés lehetőséget, majd kattintson a **Szerkesztés...** elemre.
1. Az SSL-tanúsítvány mezőben válassza a **SharePoint** -tanúsítvány lehetőséget, majd kattintson az OK gombra.

A SharePoint-webhelyet az Azure AD Application Proxy használatával kívül is elérheti.

## <a name="step-3-configure-kerberos-constrained-delegation-kcd"></a>3\. lépés: a Kerberos által korlátozott delegálás konfigurálása (KCD)

A felhasználók először az Azure AD proxy-összekötőn keresztül hitelesítik magukat Azure Active Directory, majd a SharePoint használatával a Kerberos segítségével. Ahhoz, hogy az összekötő megszerezzen egy Kerberos-jogkivonatot a Azure Active Directory felhasználó nevében, meg kell adnia a Kerberos által korlátozott delegálást a protokollok átváltásával. További információ a KCD: a [Kerberos által korlátozott delegálás áttekintése](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11)).

### <a name="set-the-service-principal-name-for-the-sharepoint-service-account"></a>Egyszerű szolgáltatásnév beállítása a SharePoint-szolgáltatásfiók számára

Ebben a cikkben a belső URL-cím `https://sharepoint`, így az egyszerű szolgáltatásnév `HTTP/sharepoint`. Ezeket az értékeket a környezetének megfelelő értékekkel kell helyettesítenie.
Ha a SharePoint-alkalmazáskészlethez tartozó SPN @no__t – 0 értéket szeretné regisztrálni a (z) `Contoso\spapppool` számára, futtassa a következő parancsot egy parancssorból a tartomány rendszergazdájaként:

`setspn -S HTTP/sharepoint Contoso\spapppool`

A Setspn parancs a Hozzáadás előtt megkeresi az egyszerű szolgáltatásnevet. Ha már létezik, **ismétlődő SPN-érték** jelenik meg. Ebben az esetben érdemes eltávolítani a meglévő SPN-t, ha az nem a megfelelő alkalmazáskészlet-fiókban van beállítva.  
A Setspn parancs az-L kapcsolóval való futtatásával ellenőrizheti, hogy az egyszerű szolgáltatásnév sikeresen hozzá lett-e adva. További információ a parancsról: [Setspn](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc731241(v=ws.11)).

### <a name="ensure-that-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-sharepoint-application-pool-account"></a>Gondoskodjon arról, hogy az összekötő megbízható legyen a SharePoint-alkalmazáskészlet fiókhoz hozzáadott egyszerű szolgáltatásnév delegálásához

Konfigurálja úgy a KCD, hogy az Azure AD Application Proxy szolgáltatás delegálja a felhasználói identitásokat a SharePoint-alkalmazáskészlet fiókjának. Konfigurálja a KCD úgy, hogy engedélyezi az alkalmazásproxy-összekötőnek, hogy beolvassa a Kerberos-jegyeket az Azure AD-ben hitelesített felhasználók számára. Ezután a kiszolgáló átadja a környezetet a célalkalmazás vagy a SharePoint számára ebben az esetben.

A KCD konfigurálásához ismételje meg az alábbi lépéseket minden összekötő-gépen:

1. Jelentkezzen be tartományi rendszergazdaként egy tartományvezérlőre, majd nyissa meg **Active Directory felhasználókat és számítógépeket**.
1. Keresse meg az Azure AD proxy-összekötőt futtató számítógépet. Ebben a példában ez a SharePoint-kiszolgáló.
1. Kattintson duplán a számítógépre, majd a **delegálás** fülre.
1. Győződjön meg arról, hogy a delegálási beállítások úgy vannak beállítva, hogy a **számítógép csak a megadott szolgáltatások delegálására legyen megbízható**. Ezután válassza **a bármely hitelesítési protokoll használata**lehetőséget.
1. Kattintson a **Hozzáadás** gombra, kattintson a **felhasználók vagy számítógépek**elemre, és keresse meg a SharePoint-alkalmazáskészlet fiókját, például `Contoso\spapppool`.
1. Az SPN-lista listában válassza ki azt, amelyet korábban hozott létre a szolgáltatásfiók számára.
1. Kattintson az **OK** gombra. Kattintson ismét az **OK** gombra a módosítások mentéséhez.
  
   ![Delegálási beállítások](./media/application-proxy-integrate-with-sharepoint-server/delegation-box2.png)

Most már készen áll arra, hogy a külső URL-lel jelentkezzen be a SharePointba, és hitelesítse magát az Azure-ban.

## <a name="troubleshoot-sign-in-errors"></a>Bejelentkezési hibák elhárítása

Ha a webhelyre való bejelentkezés nem működik, további információt kaphat az összekötő naplóiban található hibáról: az összekötőt futtató gépről nyissa meg az eseménynaplót, lépjen az **alkalmazások és szolgáltatások naplóiba** > **Microsoft** >  **AadApplicationProxy** > **összekötőt**, és ellenőrizze a **felügyeleti** naplót.

## <a name="next-steps"></a>Következő lépések

* [Egyéni tartományok használata az Azure-ban AD Application Proxy](application-proxy-configure-custom-domain.md)
* [Az Azure AD Application Proxy-összekötők ismertetése](application-proxy-connectors.md)

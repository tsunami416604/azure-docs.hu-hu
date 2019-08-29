---
title: Távoli hozzáférés a Sharepointhoz, az Azure AD-alkalmazásproxy engedélyezése |} A Microsoft Docs
description: Egy helyszíni SharePoint-kiszolgáló integrálása az Azure AD-alkalmazásproxy alapjait ismerteti.
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
ms.date: 08/28/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1265341ecfdb7f418ea89bb0ec848a20c6b430cd
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70127687"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Távoli hozzáférés a Sharepointhoz, az Azure AD-alkalmazásproxy engedélyezése

Ez a cikk ismerteti, hogyan lehet egy helyszíni SharePoint-kiszolgáló integrálása az Azure Active Directory (Azure AD) alkalmazásproxy használatával.

Távoli hozzáférés a Sharepointhoz, az Azure AD-alkalmazásproxy engedélyezéséhez kövesse az ebben a cikkben lépésről lépésre szakaszok.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy már rendelkezik a SharePoint 2013-as vagy újabb a környezetben. Ezenkívül vegye figyelembe a következő előfeltételek vonatkoznak:

* A SharePoint natív Kerberos-támogatást tartalmaz. Ezért hozzáférő felhasználók belső helyek távolról az Azure AD-alkalmazásproxyn keresztül feltételezheti, hogy az egyszeri bejelentkezés (SSO) felhasználói élményt.
* Ebben a forgatókönyvben a SharePoint-kiszolgáló konfigurációs módosításait tartalmazza. Azt javasoljuk, hogy egy átmeneti környezet használatával. Ezzel a módszerrel, először az átmeneti kiszolgálón hajtsa végre frissítéseket, és ezután megkönnyítik az éles környezetben való elhelyezés előtt tesztelési ciklust.
* A közzétett URL-cím SSL szükség van. A belső URL-címen is szükség van az SSL használatára, hogy a hivatkozások megfelelően legyenek elküldve/leképezve.

> [!NOTE]
> Ajánlott eljárásként használjon egyéni tartományokat, amikor csak lehetséges. Egyéni tartománnyal a belső URL-cím és a külső URL-cím is megadható. Ezután ugyanazt a hivatkozást használhatja az alkalmazás eléréséhez a hálózatán belül és kívül is. Ez a konfiguráció optimalizálja az alkalmazáshoz hozzáférő felhasználók és más alkalmazások élményét. További információ az [Egyéni tartományok Azure ad Application proxy-](application-proxy-configure-custom-domain.md)beli használatáról.

## <a name="step-1-configure-kerberos-constrained-delegation-kcd"></a>1\. lépés: A Kerberos által korlátozott delegálás konfigurálása (KCD)

Windows-hitelesítést használó helyszíni alkalmazások akkor érhető el, egyszeri bejelentkezés (SSO) a Kerberos hitelesítési protokoll és a Kerberos által korlátozott delegálás (KCD) szolgáltatás. KCD, ha konfigurálva van, lehetővé teszi, hogy egy felhasználó egy Windows-jogkivonat beszerzése az Application Proxy connector akkor is, ha a felhasználó még nem jelentkezett be a Windows közvetlenül. KCD kapcsolatos további információkért lásd: [Kerberos által korlátozott delegálás áttekintése](https://technet.microsoft.com/library/jj553400.aspx).

Egy SharePoint-kiszolgáló beállítása a kcd Szolgáltatáshoz, az eljárásokkal a következő egymást követő szakaszokban:

### <a name="ensure-that-sharepoint-web-application-is-running-under-a-domain-account"></a>Győződjön meg arról, hogy a SharePoint-webalkalmazás egy tartományi fiók alatt fut

Először győződjön meg arról, hogy a SharePoint-webalkalmazás egy tartományi fiókban fut – nem helyi rendszer, helyi szolgáltatás vagy hálózati szolgáltatás. Ezt úgy teheti meg, hogy egyszerű szolgáltatásnevet (SPN-ket) társít ehhez a fiókhoz. SPN-EK, hogyan azonosítja a Kerberos protokoll a különböző szolgáltatásokat. És a használatával konfigurálja a Kerberos-fiók szükséges.

> [!NOTE]
> Szüksége lesz egy korábban létrehozott Azure AD-fiókot a szolgáltatáshoz. Javasoljuk, hogy engedélyezze az automatikus jelszóváltoztatáshoz. A lépések és hibaelhárítási problémák teljes készletével kapcsolatos további információkért lásd: az [automatikus jelszó-módosítás konfigurálása a SharePointban](https://technet.microsoft.com/library/ff724280.aspx).

Győződjön meg arról, hogy a helyek egy meghatározott szolgáltatás fiók alatt futnak, hajtsa végre az alábbi lépéseket:

1. Nyissa meg a **SharePoint központi felügyeleti** webhelyet.
1. Lépjen a **biztonsági** válassza **szolgáltatásfiókok konfigurálása**.
1. Válassza ki **webalkalmazás-készlet - SharePoint – 80-as**. A beállítások némileg eltérőek lehetnek a webalkalmazás-készlet neve alapján, vagy ha a webes készletet használja az SSL-alapértelmezés szerint.

   ![Lehetőségek a szolgáltatásfiók konfigurálása](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

1. Ha **válassza ki a fiókot ehhez az összetevőhöz** mező értéke **helyi szolgáltatás** vagy **hálózati szolgáltatás**, hozzon létre egy fiókot kell. Ha nem, akkor ezzel végzett, és áthelyezheti a következő szakaszra.
1. Válassza ki **új felügyelt fiók regisztrálása**. Miután létrehozta a fiókot, be kell állítania **webes alkalmazáskészlet** a fiók használata előtt.

### <a name="set-a-service-principal-name-for-the-sharepoint-service-account"></a>Állítsa be a szolgáltatásnév a SharePoint-szolgáltatásfiókhoz

A KCD konfigurálása előtt a következőket kell tennie:

* Azonosítsa az Azure AD proxy által elérhetővé tenni kívánt SharePoint-webalkalmazást futtató tartományi fiókot.
* Válasszon egy belső URL-címet, amely az Azure AD proxyban és a SharePointban is konfigurálva lesz. Ez a belső URL-cím nem használható fel a webalkalmazásban, és soha nem fog megjelenni a böngészőben.

Ha feltételezi, hogy a <https://sharepoint>belső URL-cím van kiválasztva, akkor az egyszerű szolgáltatásnév:

```
HTTP/SharePoint
```

> [!NOTE]
> Kövesse az alábbi ajánlásokat a belső URL-címhez:
> * HTTPS használata
> * Ne használjon egyéni portokat
> * A DNS-ben hozzon létre egy gazdagépet (A), hogy a SharePoint-WFE (vagy a Load Balancer) mutasson, és ne egy aliast (CName).

Az egyszerű szolgáltatásnév regisztrálásához futtassa a következő parancsot a parancssorból a tartomány rendszergazdájaként:

```
setspn -S HTTP/SharePoint demo\spAppPoolAccount
```

Ezzel a paranccsal állítható be az SPN _http/SharePoint_ a SharePoint-alkalmazáskészlet fiókjának _demo\spAppPoolAccount_.

Cserélje le a _http/SharePoint_ és a belső URL-cím SPN-jét a saját környezetében lévő alkalmazáskészlet-fiók _demo\spAppPoolAccount_ . A Setspn parancs keres az egyszerű Szolgáltatásnevet, mielőtt hozzáadja azt. Ebben az esetben már létezik egy **duplikált SPN-érték** hibaüzenet. Ebben az esetben érdemes eltávolítani a meglévő SPN-t, ha az nem a megfelelő alkalmazáskészlet-fiókban van beállítva.

A Setspn parancs a-L kapcsolóval való futtatásával ellenőrizheti, hogy az egyszerű szolgáltatásnév hozzá lett-e adva. Ezzel a paranccsal kapcsolatos további információkért lásd: [Setspn](https://technet.microsoft.com/library/cc731241.aspx).

### <a name="ensure-that-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-sharepoint-application-pool-account"></a>Gondoskodjon arról, hogy az összekötő megbízható legyen a SharePoint-alkalmazáskészlet fiókhoz hozzáadott egyszerű szolgáltatásnév delegálásához

Konfigurálja úgy a KCD, hogy az Azure AD Application Proxy szolgáltatás delegálja a felhasználói identitásokat a SharePoint-alkalmazáskészlet fiókjának. Konfigurálja a kcd Szolgáltatáshoz beolvasni a felhasználók, akik az Azure ad-ben hitelesített Kerberos-jegyet az Application Proxy connector engedélyezésével. Majd, hogy a kiszolgáló továbbítja a környezetet a célalkalmazás vagy a SharePoint ebben az esetben.

Konfigurálja a Kerberos, minden összekötő géphez ismételje meg a következő lépéseket:

1. Jelentkezzen be egy tartományvezérlőre tartományi rendszergazdaként, és nyissa meg **Active Directory – felhasználók és számítógépek**.
1. Az összekötőt futtató számítógépen található. Ebben a példában ugyanazon a SharePoint-kiszolgálón.
1. Kattintson duplán arra a számítógépre, és kattintson a **delegálás** fülre.
1. Győződjön meg arról, hogy a delegálási beállítások beállítása **számítógépen csak a megadott szolgáltatások delegálhatók**. Ezután válassza ki **bármely hitelesítési protokoll**.
1. Kattintson a **Hozzáadás** gombra, kattintson a **felhasználók vagy számítógépek**elemre, és keresse meg a SharePoint-alkalmazáskészlet fiókját, például _demo\spAppPoolAccount_.
1. SPN-ek listájában válassza ki a korábban létrehozott szolgáltatásfiók.
1. Kattintson az **OK** gombra. Kattintson a **OK** újra, hogy a módosítások mentéséhez.
  
   ![A delegálási beállítások](./media/application-proxy-integrate-with-sharepoint-server/delegation-box2.png)

## <a name="step-2-configure-azure-ad-proxy"></a>2\. lépés: Az Azure AD proxy konfigurálása

Most, hogy konfigurálta a KCD, készen áll az Azure AD Application Proxy konfigurálására.

1. Közzététel a SharePoint-webhely a következő beállításokkal. Lépésenkénti útmutatásért lásd: [közzététel az Azure AD-alkalmazásproxy használatával](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).
   * **Belső URL-cím**: A korábban kiválasztott SharePoint-alapú belső URL-cím **<https://SharePoint/>** , például:.
   * **Előhitelesítési módszer**: Azure Active Directory
   * **URL-cím lefordítása a fejlécekben**: NO

   > [!TIP]
   > A SharePoint használja a _állomásfejléc_ érték keresse ki a helyet. Ez az érték alapján hivatkozásokat is állít elő. Az eredő hatás, hogy minden olyan hivatkozás, amely a SharePoint hoz létre egy közzétett URL-címet, amely a külső URL-cím használata megfelelően vannak beállítva. Az érték **Igen** is lehetővé teszi, hogy az összekötő továbbítja a kérést a háttéralkalmazás. Azonban érték beállítása **nem** azt jelenti, hogy az összekötő nem küld a belső neve. Ehelyett az összekötő küld az állomásfejléc közzétett URL-címként a háttéralkalmazás.

   ![Alkalmazás közzététele SharePoint](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

1. Az alkalmazás közzététele után konfigurálja az egyszeri bejelentkezés beállításai az alábbi lépéseket:

   1. Az alkalmazás oldalán a portálon, válassza **egyszeri bejelentkezési**.
   1. Válassza az egyszeri bejelentkezési mód, **integrált Windows-hitelesítés**.
   1. Belső alkalmazás egyszerű Szolgáltatásnevét, amely a korábban beállított értékre állítva. Ebben a példában a **http/SharePoint**lenne.
   1. A "delegált bejelentkezési azonosító" területen válassza ki a legmegfelelőbb lehetőséget a Active Directory erdő konfigurációjában. Ha például egyetlen AD-tartománya van az erdőben, válassza ki **a helyszíni Sam-fiók nevét** (az alább látható módon), de ha a felhasználók nem ugyanabban a tartományban találhatók, mint a SharePoint és az alkalmazásproxy-összekötő kiszolgálók, akkor válassza **a helyszíni egyszerű felhasználónév lehetőséget.** (nem látható).

   ![Egyszeri bejelentkezés az integrált Windows-hitelesítés konfigurálása](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

1. Az alkalmazás beállításának befejezéséhez, nyissa meg a **felhasználók és csoportok** szakaszt, és hozzárendelhet felhasználókat az alkalmazás eléréséhez. 

## <a name="step-3-configure-sharepoint-to-use-kerberos-and-azure-ad-proxy-urls"></a>3\. lépés: A SharePoint konfigurálása a Kerberos és az Azure AD proxy URL-címeinek használatára

A következő lépés a SharePoint-webalkalmazás kiterjesztése egy új zónára, amely Kerberos-hitelesítéssel van konfigurálva, és a megfelelő alternatív hozzáférés-leképezés lehetővé teszi, hogy a SharePoint kezelje a belső URL-címre küldött bejövő kéréseket, és válaszoljon a külső URL-címhez létrehozott hivatkozásokra.

1. Indítsa el a **SharePoint felügyeleti**rendszerhéjat.
1. Futtassa a következő szkriptet a webalkalmazás az extranetes zónára történő kiterjesztéséhez és a Kerberos-hitelesítés engedélyezéséhez:

   ```powershell
   # Replace "http://spsites/" with the URL of your web application
   # Replace "https://sharepoint-f128.msappproxy.net/" with the External URL in your Azure AD proxy application
   $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
   Get-SPWebApplication "http://spsites/" | New-SPWebApplicationExtension -Name "SharePoint - AAD Proxy" -SecureSocketsLayer -Zone "Extranet" -Url "https://sharepoint-f128.msappproxy.net/" -AuthenticationProvider $winAp
   ```

1. Nyissa meg a **SharePoint központi felügyeleti** webhelyet.
1. A **rendszerbeállítások**válassza **konfigurálása másodlagos címek leképezése**. Megnyílik a másodlagos címek leképezése mezőbe.
1. Válassza ki a webhelyet, például **SharePoint-80**. Ebben az esetben az extranetes zóna még nem rendelkezik megfelelően beállított belső URL-címmel:

   ![A másodlagos hozzáférés-leképezések mező megjelenítése](./media/application-proxy-integrate-with-sharepoint-server/alternate-access1.png)

1. Kattintson a **belső URL-címek hozzáadása**lehetőségre.
1. Az **URL protokoll, a gazdagép és a port** szövegmezőbe írja be például <https://SharePoint/>az Azure ad proxyn konfigurált **belső URL-címet** .
1. Válassza a zóna **extranet** elemet a legördülő listából.
1. Kattintson a **Save** (Mentés) gombra.
1. A másodlagos hozzáférés-hozzárendeléseknek most így kell kinéznie:

    ![Alternatív hozzáférés-hozzárendelések helyes kiosztása](./media/application-proxy-integrate-with-sharepoint-server/alternate-access3.png)

## <a name="step-4-ensure-that-an-https-certificate-is-configured-for-the-iis-site-of-the-extranet-zone"></a>4\. lépés: Győződjön meg arról, hogy a HTTPS-tanúsítvány konfigurálva van az extranet zóna IIS-helyéhez

A SharePoint-konfiguráció sikeresen befejeződött, de mivel az extranetes zóna <https://SharePoint/>belső URL-címe, a helyhez be kell állítani egy tanúsítványt.

1. Nyissa meg a Windows PowerShell-konzolt.
1. Futtassa az alábbi szkriptet egy önaláírt tanúsítvány létrehozásához, és adja hozzá a saját tárolóhoz:

   ```powershell
   # Replace "SharePoint" with the actual hostname of the Internal URL of your Azure AD proxy application
   New-SelfSignedCertificate -DnsName "SharePoint" -CertStoreLocation "cert:\LocalMachine\My"
   ```

   > [!NOTE]
   > Az önaláírt tanúsítványok csak tesztelési célokra használhatók. Éles környezetekben a hitelesítésszolgáltató által kiadott tanúsítványok használata erősen ajánlott.

1. Nyissa meg a "Internet Information Services Manager" konzolt.
1. Bontsa ki a kiszolgálót a fanézetben, bontsa ki a "helyek" elemet, válassza ki a "SharePoint-HRE proxy" helyet, és kattintson a kötések elemre.
1. Válassza a https-kötés lehetőséget, majd kattintson a **Szerkesztés...** elemre.
1. Az SSL-tanúsítvány mezőben válassza a **SharePoint** -tanúsítvány lehetőséget, majd kattintson az OK gombra.

Most már elérheti a SharePoint-webhely külsőleg Azure AD-alkalmazásproxy használatával.

## <a name="next-steps"></a>További lépések

* [Egyéni tartományok használata az Azure AD-alkalmazásproxy](application-proxy-configure-custom-domain.md)
* [Az Azure AD-alkalmazásproxy-összekötők ismertetése](application-proxy-connectors.md)

---
title: Meglévő helyszíni proxykiszolgálók és az Azure AD használata | Microsoft dokumentumok
description: A meglévő helyszíni proxykiszolgálókkal való munka lefedi.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fe3a63e119fed6825982b9de13bc78cb7da5415
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481398"
---
# <a name="work-with-existing-on-premises-proxy-servers"></a>Meglévő helyszíni proxykiszolgálók használata

Ez a cikk bemutatja, hogyan konfigurálhatja az Azure Active Directory (Azure AD) alkalmazásproxy-összekötők a kimenő proxykiszolgálók. Olyan hálózati környezetben rendelkező ügyfelek számára készült, amelyek rendelkeznek proxykkal.

Kezdjük a fő telepítési forgatókönyvek vizsgálatával:

* Konfigurálja az összekötőket a helyszíni kimenő proxyk megkerülésére.
* Konfigurálja az összekötők egy kimenő proxy azure AD alkalmazásproxy eléréséhez.

Az összekötők működéséről az [Azure AD alkalmazásproxy-összekötők ismertetése](application-proxy-connectors.md)című témakörben talál további információt.

## <a name="bypass-outbound-proxies"></a>Kimenő proxyk megkerülése

Az összekötők olyan operációsrendszer-összetevőket tartalmaznak, amelyek kimenő kérelmeket tesznek ki. Ezek az összetevők automatikusan megkísérlik megtalálni a proxykiszolgálót a hálózaton a Web Proxy Auto-Discovery (WPAD) segítségével.

Az operációs rendszer összetevői a wpad.domainsuffix DNS-keresése segítségével próbálnak meg proxykiszolgálót keresni. Ha a keresése feloldódik a DNS-ben, a rendszer HTTP-kérelmet küld a wpad.dat IP-címére. Ez a kérelem lesz a proxy konfigurációs parancsfájl a környezetben. Az összekötő ezzel a parancsfájllal választ ki egy kimenő proxykiszolgálót. Előfordulhat azonban, hogy az összekötő forgalom továbbra sem megy át a proxyn szükséges további konfigurációs beállítások miatt.

Konfigurálhatja az összekötőt úgy, hogy megkerülje a helyszíni proxyt, és győződjön meg arról, hogy az közvetlen kapcsolatot használ az Azure-szolgáltatásokhoz. Ezt a módszert javasoljuk, feltéve, hogy a hálózati házirend lehetővé teszi, mert ez azt jelenti, hogy eggyel kevesebb konfigurációt kell karbantartania.

Az összekötő kimenő proxyhasználatának letiltásához szerkessze a C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config fájlt, és adja hozzá az ebben a kódmintában látható *system.net* szakaszt:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.net>
    <defaultProxy enabled="false"></defaultProxy>
  </system.net>
  <runtime>
    <gcServer enabled="true"/>
  </runtime>
  <appSettings>
    <add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```

Annak érdekében, hogy a Connector Updater szolgáltatás is megkerülje a proxyt, módosítsa az ApplicationProxyConnectorUpdaterService.exe.config fájlt. Ez a fájl a C:\Program Files\Microsoft AAD App Proxy Connector Updater mappában található.

Ügyeljen arra, hogy másolatot készítsen az eredeti fájlokról, ha vissza kell térnie az alapértelmezett .config fájlokhoz.

## <a name="use-the-outbound-proxy-server"></a>A kimenő proxykiszolgáló használata

Egyes környezetekben kivétel nélkül minden kimenő forgalomnak egy kimenő proxyn keresztül kell mennie. Ennek eredményeképpen a proxy megkerülése nem lehetséges.

Beállíthatja, hogy az összekötő forgalma a kimenő proxyn keresztül haladjon, ahogy az az alábbi ábrán látható:

 ![Összekötőforgalom konfigurálása egy kimenő proxyn keresztül az Azure AD alkalmazásproxyhoz](./media/application-proxy-configure-connectors-with-proxy-servers/configure-proxy-settings.png)

Annak eredményeként, hogy csak a kimenő forgalom, nincs szükség a bejövő hozzáférés konfigurálása a tűzfalakon keresztül.

> [!NOTE]
> Az alkalmazásproxy nem támogatja a más proxyk hitelesítését. Az összekötő/frissítő hálózati szolgáltatás fiókok képesnek kell lennie arra, hogy csatlakozzon a proxy hitelesítés nélkül.

### <a name="step-1-configure-the-connector-and-related-services-to-go-through-the-outbound-proxy"></a>1. lépés: Konfigurálja az összekötőt és a kapcsolódó szolgáltatásokat a kimenő proxyn keresztül

Ha a WPAD engedélyezve van a környezetben, és megfelelően van konfigurálva, az összekötő automatikusan felderíti a kimenő proxykiszolgálót, és megpróbálja használni. Azonban explicit módon konfigurálhatja az összekötőt egy kimenő proxyn keresztül.

Ehhez szerkessze a C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config fájlt, és adja hozzá *a* system.net szakaszt, amely ebben a kódmintában látható. Módosítsa a *proxykiszolgáló:8080 módosítását* úgy, hogy az tükrözze a helyi proxykiszolgáló nevét vagy IP-címét, valamint azt a portot, amelyen figyel. Az értéknek rendelkeznie kell a http:// előtaggal, még akkor is, ha IP-címet használ.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.net>  
    <defaultProxy>   
      <proxy proxyaddress="http://proxyserver:8080" bypassonlocal="True" usesystemdefault="True"/>   
    </defaultProxy>  
  </system.net>
  <runtime>
    <gcServer enabled="true"/>
  </runtime>
  <appSettings>
    <add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```

Ezután konfigurálja úgy az Összekötő frissítő szolgáltatást, hogy a proxyt használja a C:\Program Files\Microsoft AAD App Proxy Connector Updater\ApplicationProxyConnectorUpdaterService.exe.config fájl hasonló módosításával.

### <a name="step-2-configure-the-proxy-to-allow-traffic-from-the-connector-and-related-services-to-flow-through"></a>2. lépés: Konfigurálja a proxyt úgy, hogy az összekötőés a kapcsolódó szolgáltatások forgalma átfolyhasson

A kimenő proxynál négy szempontot kell figyelembe venni:

* Proxy kimenő szabályai
* Proxy hitelesítése
* Proxyportok
* TLS-ellenőrzés

#### <a name="proxy-outbound-rules"></a>Proxy kimenő szabályai

Hozzáférés engedélyezése a következő URL-ekhez:

| URL-cím | Hogyan használják |
| --- | --- |
| \*msappproxy.net.<br>\*servicebus.windows.net | Az összekötő és az alkalmazásproxy felhőszolgáltatása közötti kommunikáció |
| mscrl.microsoft.com:80<br>crl.microsoft.com:80<br>ocsp.msocsp.com:80<br>www.microsoft.com:80 | Az összekötő ezeket az URL-címeket használja a tanúsítványok ellenőrzéséhez |
| login.windows.net<br>secure.aadcdn.microsoftonline-p.com<br>*.microsoftonline.com<br>*.microsoftonline-p.com<br>*.msauth.net<br>*.msauthimages.net<br>*.msecnd.net<br>*.msftauth.net<br>*.msftauthimages.net<br>*.phonefactor.net<br>enterpriseregistration.windows.net<br>management.azure.com<br>policykeyservice.dc.ad.msft.net<br>ctdl.windowsupdate.com:80 | Az összekötő ezeket az URL-címeket használja a regisztrációs folyamat során. |

Ha a tűzfal vagy a proxy lehetővé teszi a \*DNS \*engedélyezési listák konfigurálását, engedélyezheti a kapcsolatokat a .msappproxy.net és .servicebus.windows.net. Ha nem, engedélyeznie kell a hozzáférést az [Azure DataCenter IP-tartományaihoz.](https://www.microsoft.com/download/details.aspx?id=41653) Az IP-tartományok hetente frissülnek.

Ha nem tudja engedélyezni a teljes tartománytávolságú kapcsolat tartományonkénti kapcsolatot, és helyette IP-tartományokat kell megadnia, használja az alábbi beállításokat:

* Az összekötő kimenő hozzáférésének engedélyezése az összes célhoz.
* Engedélyezze az összekötő kimenő hozzáférését az [Azure-adatközpont összes IP-tartományához.](https://www.microsoft.com//download/details.aspx?id=41653) Az Azure-adatközpont IP-tartományainak használatával az a kihívás, hogy hetente frissül. Be kell állítania egy folyamatot annak érdekében, hogy a hozzáférési szabályok ennek megfelelően frissüljenek. Csak az IP-címek egy részhalmazának használata okozhatja a konfiguráció megszakadását.

#### <a name="proxy-authentication"></a>Proxy hitelesítése

A proxyhitelesítés jelenleg nem támogatott. Jelenlegi javaslatunk az, hogy az összekötő névtelen hozzáférést biztosítson az internetes célállomásokhoz.

#### <a name="proxy-ports"></a>Proxyportok

Az összekötő kimenő TLS-alapú kapcsolatokat hoz létre a CONNECT metódus használatával. Ez a módszer lényegében egy alagutat állít be a kimenő proxyn keresztül. Állítsa be úgy a proxykiszolgálót, hogy engedélyezze a bújtatást a 443-as és 80-as portokhoz.

> [!NOTE]
> Amikor a Service Bus HTTPS-en keresztül fut, a 443-as portot használja. Alapértelmezés szerint azonban a Service Bus megkísérli a közvetlen TCP-kapcsolatokat, és csak akkor áll vissza a HTTPS-kapcsolatra, ha a közvetlen kapcsolat meghibásodik.

#### <a name="tls-inspection"></a>TLS-ellenőrzés

Ne használja a TLS-ellenőrzést az összekötő forgalmához, mert problémákat okoz az összekötő forgalmának. Az összekötő egy tanúsítványt használ az alkalmazásproxy-szolgáltatás hitelesítéséhez, és ez a tanúsítvány elveszhet a TLS-ellenőrzés során.

## <a name="troubleshoot-connector-proxy-problems-and-service-connectivity-issues"></a>Összekötőproxyval kapcsolatos problémák és szolgáltatáscsatlakozási problémák elhárítása

Most látnia kell az összes forgalom áramlik át a proxy. Ha problémája van, az alábbi hibaelhárítási információk segíthetnek.

Az összekötő kapcsolódási problémáinak azonosításának és elhárításának legjobb módja a hálózati rögzítés az összekötő szolgáltatás indítása kor. Íme néhány gyors tipp a hálózati nyomkövetések rögzítéséhez és szűréséhez.

Használhatja az Ön által választott felügyeleti eszközt. A cikk alkalmazásában a Microsoft Message Analyzer programot használtuk. Letöltheti [a Microsoft .](https://www.microsoft.com/download/details.aspx?id=44226)

A következő példák az Üzenetelemzőre vonatkoznak, de az alapelvek bármely elemzőeszközre alkalmazhatók.

### <a name="take-a-capture-of-connector-traffic"></a>Rögzítse az összekötő forgalmát

A kezdeti hibaelhárításhoz hajtsa végre az alábbi lépéseket:

1. A services.msc szolgáltatásból állítsa le az Azure AD alkalmazásproxy-összekötő szolgáltatást.

   ![Az Azure AD alkalmazásproxy-összekötő szolgáltatás a services.msc-ban](./media/application-proxy-configure-connectors-with-proxy-servers/services-local.png)

1. Futtassa az Üzenetelemzőt rendszergazdaként.
1. Válassza **a Helyi nyomkövetés indítása**lehetőséget.
1. Indítsa el az Azure AD alkalmazásproxy-összekötő szolgáltatást.
1. Állítsa le a hálózati rögzítést.

   ![A képernyőképen a Hálózatrögzítés leállítása gomb látható](./media/application-proxy-configure-connectors-with-proxy-servers/stop-trace.png)

### <a name="check-if-the-connector-traffic-bypasses-outbound-proxies"></a>Ellenőrizze, hogy az összekötő forgalma megkerüli-e a kimenő proxykat

Ha úgy állította be az alkalmazásproxy-összekötőt, hogy megkerülje a proxykiszolgálókat, és közvetlenül csatlakozzon az alkalmazásproxy-szolgáltatáshoz, a hálózati rögzítésben meg szeretné keresni a sikertelen TCP-csatlakozási kísérleteket.

Az Üzenetelemző szűrő segítségével azonosíthatja ezeket a kísérleteket. Írja `property.TCPSynRetransmit` be a szűrőmezőbe, és válassza **az Alkalmaz**lehetőséget.

A SYN-csomag az első tcp-kapcsolat létrehozására küldött csomag. Ha ez a csomag nem ad vissza választ, a syn-t újra megkísérli. Az előző szűrő segítségével megtekintheti az újraküldött SYN-eket. Ezután ellenőrizheti, hogy ezek a syn-ek megfelelnek-e az összekötőkhöz kapcsolódó forgalomnak.

Ha azt várja, hogy az összekötő közvetlen kapcsolatot létesítsen az Azure-szolgáltatásokkal, a SynRetransmit válaszok a 443-as porton azt jelzik, hogy hálózati vagy tűzfal-problémája van.

### <a name="check-if-the-connector-traffic-uses-outbound-proxies"></a>Annak ellenőrzése, hogy az összekötőforgalom kimenő proxykat használ-e

Ha úgy állította be az alkalmazásproxy-összekötő forgalmát, hogy a proxykiszolgálókon haladjon át, meg szeretné keresni a proxyhoz sikertelen https-kapcsolatokat.

A hálózati rögzítés szűréséhez írja `(https.Request or https.Response) and tcp.port==8080` be az Üzenetelemző szűrőbe, és cserélje le a 8080-as t a proxyszolgáltatás-portra. A szűrő eredmények megtekintéséhez válassza az **Alkalmaz** lehetőséget.

Az előző szűrő csak a HTTPs-kérelmeket és válaszokat jeleníti meg a proxyportra/a proxyportról. A proxykiszolgálóval való kommunikációt mutató CONNECT-kérelmeket keresi. Sikeres esetben http OK (200) választ kap.

Ha más válaszkódokat lát, például a 407-es vagy az 502-es, az azt jelenti, hogy a proxy hitelesítést igényel, vagy valamilyen más okból nem engedélyezi a forgalmat. Ezen a ponton, akkor vegyenek részt a proxy szerver támogatási csapat.

## <a name="next-steps"></a>További lépések

* [Az Azure AD alkalmazásproxy-összekötők megismerése](application-proxy-connectors.md)
* Ha problémái vannak az összekötő kapcsolódási problémáival, tegye fel kérdését az [Azure Active Directory fórumon,](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD&forum=WindowsAzureAD) vagy hozzon létre egy jegyet támogatási csapatunkkal.

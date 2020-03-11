---
title: Meglévő helyszíni proxykiszolgálók és Azure AD használata | Microsoft Docs
description: Ismerteti, hogyan használható a meglévő helyszíni proxykiszolgálók használata.
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
ms.openlocfilehash: 5d7c7d9f6d59ffd57ddb14f7c060d0a3f6f2a6eb
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2020
ms.locfileid: "78967756"
---
# <a name="work-with-existing-on-premises-proxy-servers"></a>Meglévő helyszíni proxykiszolgálók használata

Ez a cikk bemutatja, hogyan konfigurálhatja a Azure Active Directory (Azure AD) alkalmazásproxy-összekötőket a kimenő proxykiszolgálók működéséhez. Olyan hálózati környezetű ügyfelek számára készült, amelyek meglévő proxykkal rendelkeznek.

Első lépésként tekintse meg a következő központi telepítési forgatókönyveket:

* Konfigurálja az összekötőket a helyszíni kimenő proxyk megkerüléséhez.
* Konfigurálja az összekötőket egy kimenő proxy használatára az Azure AD Application Proxy eléréséhez.

További információ az összekötők működéséről: az [Azure ad Application proxy-összekötők ismertetése](application-proxy-connectors.md).

## <a name="bypass-outbound-proxies"></a>Kimenő proxyk megkerülése

Az összekötők olyan operációsrendszer-összetevőkkel rendelkeznek, amelyek kimenő kérelmeket hajtanak végre. Ezek az összetevők automatikusan megpróbálnak megkeresni egy proxykiszolgálót a hálózaton a webproxy automatikus felderítése (WPAD) használatával.

Az operációs rendszer összetevői a WPAD. domainsuffix DNS-címkeresés végrehajtásával kísérlik meg a proxykiszolgáló megkeresését. Ha a keresés a DNS-ben oldódik fel, akkor a WPAD. dat IP-címére kell HTTP-kérelmet készíteni. Ez a kérelem a proxy konfigurációs parancsfájlja lesz a környezetben. Az összekötő ezt a parancsfájlt használja a kimenő proxykiszolgáló kiválasztásához. Előfordulhat azonban, hogy az összekötői forgalom továbbra sem halad át a proxyn szükséges további konfigurációs beállítások miatt.

Az összekötőt úgy is beállíthatja, hogy megkerülje a helyszíni proxyt, hogy az az Azure-szolgáltatásokhoz közvetlen kapcsolatot használjon. Ezt a megközelítést javasoljuk, ha a hálózati házirend lehetővé teszi a számára, mert az azt jelenti, hogy egy kisebb konfigurációt kell fenntartania.

Az összekötő kimenő proxy használatának letiltásához szerkessze a C:\Program Files\Microsoft HRE app proxy Connector\ApplicationProxyConnectorService.exe.config fájlt, és adja hozzá a *System.net* szakaszt a következő példában látható módon:

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

Annak biztosítása érdekében, hogy a Connector Updater szolgáltatás a proxyt is megkerüljék, végezze el a ApplicationProxyConnectorUpdaterService. exe. config fájl hasonló módosítását. Ez a fájl a C:\Program Files\Microsoft HRE app proxy Connector Updater található.

Készítsen másolatot az eredeti fájlokról, ha az alapértelmezett. config fájlokra kell visszaállítania.

## <a name="use-the-outbound-proxy-server"></a>A kimenő proxykiszolgáló használata

Egyes környezetekben az összes kimenő forgalomnak a kimenő proxyn keresztül kell haladnia, kivétel nélkül. Ennek eredményeképpen a proxy mellőzése nem választható.

Az összekötő adatforgalmát beállíthatja úgy, hogy a kimenő proxyn Haladjon végig, ahogy az alábbi ábrán is látható:

 ![Az összekötő-forgalom konfigurálása egy kimenő proxyn keresztüli váltáshoz az Azure AD Application Proxy](./media/application-proxy-configure-connectors-with-proxy-servers/configure-proxy-settings.png)

A csak kimenő forgalom miatt nem kell konfigurálnia a tűzfalon keresztüli bejövő hozzáférést.

> [!NOTE]
> Az alkalmazásproxy nem támogatja más proxyk hitelesítését. Az összekötő/Updater hálózati szolgáltatás fiókjainak képesnek kell lenniük a proxyhoz való csatlakozásra anélkül, hogy a hitelesítésre kellene figyelni.

### <a name="step-1-configure-the-connector-and-related-services-to-go-through-the-outbound-proxy"></a>1\. lépés: az összekötő és a kapcsolódó szolgáltatások konfigurálása a kimenő proxyn való átugráshoz

Ha a WPAD engedélyezve van a környezetben, és megfelelően van konfigurálva, az összekötő automatikusan felfedi a kimenő proxykiszolgálót, és megpróbálja használni azt. Az összekötőt azonban explicit módon úgy konfigurálhatja, hogy átugorjon egy kimenő proxyn.

Ehhez szerkessze a C:\Program Files\Microsoft HRE app proxy Connector\ApplicationProxyConnectorService.exe.config fájlt, és adja hozzá a *System.net* szakaszt a kódban. Módosítsa a *ProxyServer: 8080* , hogy tükrözze a helyi proxykiszolgáló nevét vagy IP-címét, valamint azt a portot, amelyen a figyel. Az értéknek az előtag http://akkor is szerepelnie kell, ha IP-címet használ.

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

Ezután konfigurálja a Connector Updater szolgáltatást a proxy használatára úgy, hogy hasonló módosítást végez a C:\Program Files\Microsoft HRE app proxy Connector Updater\ApplicationProxyConnectorUpdaterService.exe.config fájlhoz.

### <a name="step-2-configure-the-proxy-to-allow-traffic-from-the-connector-and-related-services-to-flow-through"></a>2\. lépés: konfigurálja a proxyt úgy, hogy az összekötő és a kapcsolódó szolgáltatások forgalmát a

A kimenő proxyn négy szempontot kell figyelembe venni:

* Proxy kimenő szabályai
* Proxy hitelesítése
* Proxy portok
* SSL-ellenőrzés

#### <a name="proxy-outbound-rules"></a>Proxy kimenő szabályai

A következő URL-címek elérésének engedélyezése:

| URL-cím | Használatuk módja |
| --- | --- |
| \*. msappproxy.net<br>\*. servicebus.windows.net | Kommunikáció az összekötő és az alkalmazásproxy Cloud Service között |
| mscrl.microsoft.com:80<br>crl.microsoft.com:80<br>ocsp.msocsp.com:80<br>www.microsoft.com:80 | Az összekötő ezeket az URL-eket használja a tanúsítványok ellenőrzéséhez |
| login.windows.net<br>secure.aadcdn.microsoftonline-p.com<br>*. microsoftonline.com<br>* . microsoftonline-p.com<br>*. msauth.net<br>* . msauthimages.net<br>*. msecnd.net<br>* . msftauth.net<br>*. msftauthimages.net<br>* . phonefactor.net<br>enterpriseregistration.windows.net<br>management.azure.com<br>policykeyservice.dc.ad.msft.net<br>ctdl.windowsupdate.com:80 | Az összekötő ezeket az URL-címeket használja a regisztrációs folyamat során. |

Ha a tűzfal vagy a proxy lehetővé teszi a DNS engedélyezési listája konfigurálását, engedélyezheti a kapcsolódást \*. msappproxy.net és a \*. servicebus.windows.net. Ha nem, engedélyeznie kell az [Azure Datacenter IP-tartományokhoz](https://www.microsoft.com/download/details.aspx?id=41653)való hozzáférést. Az IP-címtartományok hetente frissülnek.

Ha a teljes tartománynév nem engedélyezhető, és az IP-címtartományok megadására van szükség, használja a következő beállításokat:

* Az összekötő kimenő hozzáférésének engedélyezése az összes célhelyre.
* Az összekötő kimenő hozzáférésének engedélyezése az összes [Azure Datacenter IP-tartományhoz](https://www.microsoft.com//download/details.aspx?id=41653). Az Azure Datacenter IP-címtartományok listájának használatával kapcsolatos kihívás az, hogy hetente frissül. Egy olyan folyamatot kell létrehoznia, amely biztosítja, hogy a hozzáférési szabályok megfelelően frissüljenek. A konfigurációt csak az IP-címek egy részhalmaza használatával lehet megszakítani.

#### <a name="proxy-authentication"></a>Proxy hitelesítése

A proxy hitelesítés jelenleg nem támogatott. A jelenlegi Javaslatunk lehetővé teszi, hogy az összekötő névtelen hozzáférést biztosítson az internetes célhelyekhez.

#### <a name="proxy-ports"></a>Proxy portok

Az összekötő az SSL-alapú kapcsolatokat a KAPCSOLÓDÁSi módszer használatával teszi elérhetővé. Ez a metódus lényegében egy alagutat állít be a kimenő proxyn keresztül. Konfigurálja a proxykiszolgálót a 443-es és a 80-es portok bújtatásának engedélyezéséhez.

> [!NOTE]
> Ha Service Bus a HTTPS-en keresztül fut, a 443-es portot használja. Alapértelmezés szerint azonban Service Bus megkísérli a közvetlen TCP-kapcsolatokat, és csak akkor térhet vissza a HTTPS-re, ha a közvetlen kapcsolat meghiúsul.

#### <a name="ssl-inspection"></a>SSL-ellenőrzés

Ne használja az SSL-ellenőrzést az összekötő forgalmához, mert az az összekötő forgalmával kapcsolatos problémákat okoz. Az összekötő tanúsítvány használatával hitelesíti az alkalmazásproxy szolgáltatását, és a tanúsítvány elvész az SSL-ellenőrzés során.

## <a name="troubleshoot-connector-proxy-problems-and-service-connectivity-issues"></a>Az összekötő-proxyval kapcsolatos problémák elhárítása és a szolgáltatás csatlakozási problémái

Most látnia kell a proxyn keresztül áramló összes forgalmat. Ha problémák merülnek fel, a következő hibaelhárítási információk segíthetnek.

Az összekötők kapcsolódási problémáinak azonosítására és elhárítására a legjobb módszer, ha az összekötő szolgáltatás elindítása közben hálózati rögzítést végez. Íme néhány gyors tipp a hálózati nyomkövetés rögzítéséről és szűréséről.

Az Ön által választott figyelési eszközt használhatja. A cikk értelmében a Microsoft Message Analyzert használtuk. [Letöltheti a Microsofttól](https://www.microsoft.com/download/details.aspx?id=44226).

Az alábbi példák az üzenetsor-kezelőre vonatkoznak, de az alapelvek bármely Analysis Tool eszközre alkalmazhatók.

### <a name="take-a-capture-of-connector-traffic"></a>Összekötő-forgalom rögzítésének elvégzése

A kezdeti hibaelhárításhoz hajtsa végre a következő lépéseket:

1. A Services. msc ablakból állítsa le az Azure AD Application Proxy Connector szolgáltatást.

   ![Azure AD Application Proxy Connector szolgáltatás a Services. msc-ben](./media/application-proxy-configure-connectors-with-proxy-servers/services-local.png)

1. Futtassa a Message Analyzert rendszergazdaként.
1. Válassza a **helyi Nyomkövetés indítása**lehetőséget.
1. Indítsa el az Azure AD Application Proxy Connector szolgáltatást.
1. Állítsa le a hálózati rögzítést.

   ![A képernyőfelvétel a hálózati rögzítés leállítása gombra mutat.](./media/application-proxy-configure-connectors-with-proxy-servers/stop-trace.png)

### <a name="check-if-the-connector-traffic-bypasses-outbound-proxies"></a>Ellenőrizze, hogy az összekötő-forgalom megkerüli-e a kimenő proxykat

Ha az alkalmazásproxy-összekötőt úgy konfigurálta, hogy kihagyja a proxykiszolgálót, és közvetlenül kapcsolódjon az alkalmazásproxy szolgáltatáshoz, akkor a sikertelen TCP-kapcsolódási kísérletek hálózati rögzítését kell megkeresnie.

A próbálkozások azonosításához használja az üzenetsor-elemző szűrőt. Adja meg `property.TCPSynRetransmit` a szűrő mezőben, majd kattintson az **alkalmaz**gombra.

Egy SYN-csomag az első, TCP-kapcsolat létesítéséhez küldött csomag. Ha a csomag nem ad vissza választ, a rendszer újrapróbálkozik a SYN-vel. Az előző szűrő használatával megtekintheti az újraküldött SYNs. Ezután megtekintheti, hogy ezek a SYNs megfelelnek-e az összekötővel kapcsolatos adatforgalomnak.

Ha arra számít, hogy az összekötő közvetlen kapcsolattal csatlakozik az Azure-szolgáltatásokhoz, az 443-as porton SynRetransmit válaszok jelzik, hogy van hálózati vagy tűzfallal kapcsolatos probléma.

### <a name="check-if-the-connector-traffic-uses-outbound-proxies"></a>Ellenőrizze, hogy az összekötő forgalma kimenő proxykat használ-e

Ha az alkalmazásproxy-összekötő forgalmát úgy konfigurálta, hogy a proxykiszolgálót átugorja, a sikertelen HTTPS-kapcsolatokat szeretné keresni a proxyhoz.

Ha szűrni szeretné a kapcsolódási kísérletek hálózati rögzítését, írja be a `(https.Request or https.Response) and tcp.port==8080` értéket az üzenet-elemző szűrőbe, majd cserélje le a 8080-et a proxy Service-portra. Válassza az **alkalmaz** lehetőséget a szűrő eredményeinek megtekintéséhez.

Az előző szűrő csak a HTTPs-kérelmeket és a proxy portra küldött válaszokat jeleníti meg. A proxykiszolgáló kommunikációját bemutató csatlakozási kérelmeket keresi. A művelet sikeressége után egy HTTP OK (200) választ kap.

Ha más válasz kódokat (például 407 vagy 502) lát, ez azt jelenti, hogy a proxy hitelesítést igényel, vagy más okból nem engedélyezi a forgalmat. Ezen a ponton a proxykiszolgálót támogató csapatot kell felvennie.

## <a name="next-steps"></a>Következő lépések

* [Az Azure AD Application Proxy-összekötők ismertetése](application-proxy-connectors.md)
* Ha problémája van az összekötő kapcsolódási problémáinak megoldásával, kérdezze meg kérdéseit a [Azure Active Directory fórumon](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD&forum=WindowsAzureAD) , vagy hozzon létre egy jegyet a támogatási csapatával.

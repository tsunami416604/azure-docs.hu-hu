---
title: Munka a meglévő helyszíni proxykiszolgálók és az Azure AD |} A Microsoft Docs
description: Ismerteti a meglévő helyszíni proxykiszolgálók használata.
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 09/12/2018
ms.author: celested
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0b4cb1f6cc3da5230f510f57a56c7297341f82f3
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56175574"
---
# <a name="work-with-existing-on-premises-proxy-servers"></a>Meglévő helyszíni proxykiszolgálók használata

Ez a cikk ismerteti az Azure Active Directory (Azure AD) alkalmazásproxy-összekötők dolgozhat a kimenő proxy-kiszolgálók konfigurálása. Az ügyfelek számára hálózati környezetekben, ahol a meglévő proxyk szolgál.

Megnézzük a fő üzembe helyezési forgatókönyvekben kezdődik meg:
* Konfigurálja az összekötőket a helyszíni kimenő proxyk kihagyásához.
* Konfigurálja az összekötőket egy kimenő proxy használatát az Azure AD-alkalmazásproxy eléréséhez.

Összekötők működése kapcsolatos további információkért lásd: [megismerheti az Azure AD-alkalmazásproxy összekötőit](application-proxy-connectors.md).

## <a name="bypass-outbound-proxies"></a>Kimenő Proxy megkerülése

Összekötők alapjául szolgáló operációs rendszer összetevőket kimenő kérelmek rendelkezik. Ezeket az összetevőket automatikusan megpróbálja megkeresni egy proxykiszolgálót a hálózati Proxy automatikus felderítési WPAD (Web) használatával.

Az operációs rendszer összetevők megpróbálja megkeresni a proxykiszolgálót egy DNS-címkeresése wpad.domainsuffix elvégzésével. Ha a keresési DNS-ben, egy HTTP-kérelem majd kérés érkezett wpad.dat IP-címét. A kérelem válik a proxy konfigurációs parancsprogramja a környezetben. Az összekötő válassza ki a kívánt kimenő proxykiszolgáló használja ezt a szkriptet. Azonban összekötő forgalom előfordulhat, hogy továbbra is halad át, a proxy szükség további konfigurációs beállítások miatt.

Beállíthatja, hogy az összekötő, győződjön meg arról, hogy az Azure-szolgáltatásokhoz való közvetlen kapcsolódás használ a helyszíni proxy megkerülése. Javasoljuk, hogy ez a megközelítés mindaddig, amíg a hálózati házirend lehetővé teszi, hogy, mert azt jelenti, hogy egy kisebb konfigurációs fenntartásához.

Kimenő proxy használatát az összekötő letiltásához módosítsa a C:\Program Files\Microsoft AAD alkalmazás Proxy Connector\ApplicationProxyConnectorService.exe.config fájlt, és adja hozzá a *system.net* szakasz ebben a kódmintában látható:

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
Győződjön meg arról, hogy az összekötő frissítési szolgáltatást is megkerüli a proxy, egy hasonló módosítást a ApplicationProxyConnectorUpdaterService.exe.config fájlt. Ez a fájl található: C:\Program Files\Microsoft AAD alkalmazás Proxy Connector Updater.

Ügyeljen arra, hogy az eredeti fájlok másolatait abban az esetben meg kell visszaállítania az alapértelmezett .config fájl.

## <a name="use-the-outbound-proxy-server"></a>A kimenő proxykiszolgáló használata

Bizonyos környezetekben szükséges haladhat végig egy kimenő proxy, kivétel nélkül minden kimenő forgalmat. Ennek eredményeképpen a proxy megkerülése lehetőség nem.

Az összekötő adatforgalom haladjon végig a kimenő proxy konfigurálhatja az alábbi ábrán látható módon:

 ![Nyissa meg egy kimenő proxy használatával az Azure AD-alkalmazásproxy-összekötő forgalom konfigurálása](./media/application-proxy-configure-connectors-with-proxy-servers/configure-proxy-settings.png)

Miatt kellene csak a kimenő forgalmat, nincs szükség a tűzfalon keresztül bejövő hozzáférés konfigurálásához.

>[!NOTE]
>Az alkalmazásproxy nem támogatja a más proxyk-hitelesítést. Az összekötő/updater hálózati szolgáltatásfiókok proxykiszolgálóhoz való csatlakozáshoz az éppen lekéri a hitelesítés nélküli képesnek kell lennie.

### <a name="step-1-configure-the-connector-and-related-services-to-go-through-the-outbound-proxy"></a>1. lépés: Az összekötő és a kapcsolódó szolgáltatások haladhat végig a kimenő proxy konfigurálása

Ha WPAD engedélyezve van a környezetben, és megfelelően konfigurálta, az összekötő automatikusan felderíti a kimenő proxy server és a használata azt. Azonban explicit módon konfigurálhatja az összekötő haladhat végig egy kimenő proxy.

Ehhez módosítsa a C:\Program Files\Microsoft AAD alkalmazás Proxy Connector\ApplicationProxyConnectorService.exe.config fájlt, és adja hozzá a *system.net* szakasz ebben a kódmintában látható. Változás *proxyserver:8080* , hogy a helyi proxykiszolgáló nevét vagy IP-címet, és a portot, amelyet figyel a következőn:. Az érték a előtag http:// kell rendelkeznie, még akkor is, ha egy IP-címet használ.

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

Ezután konfigurálja a összekötő frissítési szolgáltatást használja a proxy hasonló módosítást végez a C:\Program Files\Microsoft AAD alkalmazás Proxy Connector Updater\ApplicationProxyConnectorUpdaterService.exe.config fájlt.

### <a name="step-2-configure-the-proxy-to-allow-traffic-from-the-connector-and-related-services-to-flow-through"></a>2. lépés: Az összekötő és a kapcsolódó szolgáltatások keresztül érkező adatforgalom engedélyezéséhez a proxy konfigurálása

Négy szempontot kell figyelembe venni, a kimenő proxy:
* Proxy kimenő szabályok
* Proxy hitelesítése
* Proxy-portok
* SSL-ellenőrzés

#### <a name="proxy-outbound-rules"></a>Proxy kimenő szabályok
A következő URL-hozzáférés engedélyezése:

| URL-cím | Hogyan használja fel azokat |
| --- | --- |
| \*.msappproxy.net<br>\*.servicebus.windows.net | Az összekötő és az alkalmazásproxy-felhőszolgáltatás közötti kommunikáció |
| mscrl.microsoft.com:80<br>crl.microsoft.com:80<br>ocsp.msocsp.com:80<br>www.microsoft.com:80 | Az Azure az alábbi URL-címek segítségével tanúsítványok |
| login.windows.net<br>login.microsoftonline.com | Az összekötő URL-használ a regisztrációs folyamat során. |

Ha a tűzfal vagy proxy lehetővé teszi a DNS-engedélyezési, engedélyezett kapcsolatokat is \*. msappproxy.net és \*. servicebus.windows.net. Ha nem, engedélyeznie kell a hozzáférést a [Azure DataCenter IP-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653). Az IP-címtartományok minden héten frissülnek.


Ha nem engedélyezi a csatlakozást a teljes tartománynév alapján, és meg kell adnia ehelyett az IP-címtartományok, használja az alábbi beállításokat:

* Az összes destinations összekötő kimenő elérését teszi lehetővé.
* Az összekötő kimenő hozzáférést minden olyan a [Azure adatközpont IP-címtartományait](https://www.microsoft.com//download/details.aspx?id=41653). Az Azure-adatközpont IP-címtartományok lista segítségével a kihívás abban áll, hogy hetente kell frissíteni. Kell helyezni egy folyamatot annak érdekében, hogy az, hogy a hozzáférési szabályok ennek megfelelően frissülnek. Csak egy részét az IP-címek okozhat a konfigurációt, kezdetét.

#### <a name="proxy-authentication"></a>Proxy hitelesítése

Proxy hitelesítése jelenleg nem támogatott. Az aktuális ajánljuk, hogy az összekötő az internetes célhelyeket névtelen hozzáférés engedélyezése.

#### <a name="proxy-ports"></a>Proxy-portok

Az összekötő lehetővé teszi a kimenő SSL-alapú kapcsolatokat a CONNECT módszer használatával. Ez a módszer lényegében állít be egy alagutat a kimenő proxyn keresztül. A proxykiszolgáló, hogy a 443-as és a 80-as bújtatás konfigurálása.

>[!NOTE]
>A Service Bus fut a HTTPS-kapcsolaton keresztül, használja a 443-as porton. Azonban alapértelmezés szerint a Service Bus megkísérli közvetlen TCP-kapcsolatokat, és csak akkor, ha közvetlen kapcsolódás sikertelen visszavált HTTPS.

#### <a name="ssl-inspection"></a>SSL-ellenőrzés
Ne használjon SSL-ellenőrzés az összekötő-forgalom esetén, mert az összekötő forgalom problémákat okoz. Az összekötő tanúsítványt használ hitelesítésre az alkalmazásproxy-szolgáltatás, és ezt a tanúsítványt az SSL-ellenőrzés során elvesznek. 

## <a name="troubleshoot-connector-proxy-problems-and-service-connectivity-issues"></a>Összekötő proxy problémák és a szolgáltatás kapcsolódási problémák elhárítása
Most már megtekintheti a proxyn keresztül áramló teljes forgalomra. Ha problémába ütközik, a következő hibaelhárítási információk webhelyünkre.

Összekötő csatlakozási problémák azonosítása és elhárítása a legjobb módszer, hogy az összekötő-szolgáltatás indítása közben hálózati rögzítőeszközt igénybe vehet. Az alábbiakban néhány gyors tippek rögzítése és hálózati nyomkövetés szűrés.

A figyelési választott eszközzel, használhatja. Ez a cikk az alkalmazásában a Microsoft Message Analyzer használtuk. Is [töltse le a Microsoft](https://www.microsoft.com/download/details.aspx?id=44226).

Az alábbi példák konkrétan a Message Analyzer, de az elvek alkalmazhatók minden olyan elemző eszközt.

### <a name="take-a-capture-of-connector-traffic"></a>Igénybe vehet egy összekötő forgalom rögzítése

Kezdeti hibaelhárítási, hajtsa végre az alábbi lépéseket:

1. A services.msc az Azure AD alkalmazásproxy-összekötő szolgáltatás leállítása.

   ![Az Azure AD alkalmazásproxy-összekötő szolgáltatást a Services.msc alkalmazással](./media/application-proxy-configure-connectors-with-proxy-servers/services-local.png)

2. Az Üzenetelemző futtassa rendszergazdaként.
3. Válassza ki **helyi nyomkövetés indítása**.

   ![Hálózati Rögzítés indítása](./media/application-proxy-configure-connectors-with-proxy-servers/start-local-trace.png)

3. Indítsa el az Azure AD alkalmazásproxy-összekötő szolgáltatást.
4. A hálózati Rögzítés leállítása

   ![Hálózati Rögzítés leállítása](./media/application-proxy-configure-connectors-with-proxy-servers/stop-trace.png)

### <a name="check-if-the-connector-traffic-bypasses-outbound-proxies"></a>Ellenőrizze, hogy ha az összekötő forgalom megkerüli a kimenő proxy

Ha konfigurálta az Application Proxy connector a proxykiszolgálót, és közvetlenül csatlakozhat az alkalmazásproxy-szolgáltatás, tekintse meg a hálózati rögzítés sikertelen TCP-kapcsolati kísérletek az szeretné. 

A Message Analyzer szűrő használatával azonosíthatja ezeket a kísérleteket. Adja meg `property.TCPSynRetransmit` a Szűrő mezőbe, és válasszon **alkalmaz**. 

Egy külön csomagot küld a TCP-kapcsolatot létesítsen az első csomag. Ha a csomag nem adott vissza választ, a szinkronizálás a mi reattempted van. A fenti szűrőt használhatja bármely újraküldött SYNs megtekintéséhez. Ezután ellenőrizheti, hogy ezek SYNs megfelelnek-e minden olyan összekötő kötődő forgalmat.

Ha várhatóan a connector közvetlen kapcsolat az Azure-szolgáltatások, SynRetransmit válaszok 443-as porton arra utal, hogy a hálózati vagy a tűzfal hibája.

### <a name="check-if-the-connector-traffic-uses-outbound-proxies"></a>Ellenőrizze, hogy az összekötő-forgalmat használ-e a kimenő proxy

Ha konfigurálta az Application Proxy connector adatforgalom haladhat végig a proxykiszolgálók, keresse meg a proxy sikertelen https-kapcsolatok szeretné. 

Adja meg a hálózati rögzítés a csatlakozási kísérleteket, a szűrési, `(https.Request or https.Response) and tcp.port==8080` a Message Analyzer szűrő cseréje a 8080-as a proxy portja. Válassza ki **alkalmaz** megtekintéséhez az eredmények szűréséhez. 

A fenti szűrő csak a HTTPs-kérelmek és válaszok látható és-tárolókról a proxy portja. A csatlakozási kérelmek azt mutatják be a proxykiszolgáló folytatott kommunikáció keres. Sikeres, Befejezés esetén (200-as) HTTP-OK választ kap.

Ha más válaszkódokat, mint a 407-es vagy 502-es, ez azt jelenti, hogy a proxy hitelesítést igénylő, illetve nem engedélyezi a forgalmat a valamilyen más okból. Ezen a ponton vegye fel a kapcsolatot a proxy server támogatási csapatával.

## <a name="next-steps"></a>További lépések

- [Az Azure AD-alkalmazásproxy-összekötők ismertetése](application-proxy-connectors.md)

- Ha problémába ütközik az összekötő kapcsolódási problémák, tegye fel a kérdését a [Azure Active Directory-fórum](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD&forum=WindowsAzureAD) vagy az ügyfélszolgálati csapatunkhoz jegy létrehozása.

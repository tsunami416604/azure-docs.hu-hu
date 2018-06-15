---
title: Munka a meglévő helyszíni proxykiszolgálókra és az Azure AD |} Microsoft Docs
description: Bemutatja, hogyan adhat az meglévő helyszíni proxy kiszolgálóhoz.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 0456a46262c6ad27b19487a6e4b2d1b6a139bbc3
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34162028"
---
# <a name="work-with-existing-on-premises-proxy-servers"></a>A meglévő helyszíni proxykiszolgálókkal működik

Ez a cikk ismerteti, hogyan konfigurálja az Azure Active Directory (Azure AD) alkalmazásproxy összekötőket az kimenőproxy-kiszolgálóhoz. Az ügyfelek számára hálózati környezetekben, ahol a meglévő proxyk szolgál.

Először fő központi telepítési forgatókönyvek alapján:
* Konfigurálja az összekötőket a helyszíni kimenő proxyk kihagyásához.
* Konfigurálja az összekötőket egy kimenő proxy használatát az Azure AD-alkalmazásproxy eléréséhez.

Összekötők működésével kapcsolatos további információkért lásd: [megértéséhez Azure AD-alkalmazásproxy összekötők](application-proxy-connectors.md).

## <a name="bypass-outbound-proxies"></a>Kimenő proxyk figyelmen kívül hagyása

Összekötők rendelkezik az operációs rendszer alapösszetevőt kimenő kérelmeket. Ezeket az összetevőket automatikusan megpróbálja megkeresni a proxykiszolgálót a hálózaton, webes Proxy automatikus felderítését a lekérdezés (WPA) segítségével.

Az operációs rendszer összetevőit megpróbálja megkeresni a proxykiszolgáló wpad.domainsuffix a DNS-címkeresést elvégzésével. Ha a keresési oldja fel a DNS-ben, HTTP-kérelem majd történik wpad.dat IP-címét. A kérelem válik a proxy konfigurációs parancsprogramja a környezetben. Az összekötő jelöljön ki egy kimenő proxy használja ezt a parancsfájlt. Azonban összekötő forgalom előfordulhat, hogy még nem halad át, a proxy szükség további konfigurációs beállítások miatt.

Beállíthatja, hogy az összekötő a helyszíni proxy győződjön meg arról, hogy használ-e közvetlen kapcsolat az Azure-szolgáltatásokhoz való kihagyásához. Ezt a módszert használja, azt javasoljuk, mindaddig, amíg a hálózati házirend lehetővé teszi, mert az azt jelenti, hogy rendelkezik-e egy kisebb konfigurációs fenntartásához.

Tiltsa le az összekötő kimenő proxy használatát, a C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config fájl szerkesztésével, és adja hozzá a *System.NET névtérbeli* látható a fenti szakaszban:

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
Győződjön meg arról, hogy az összekötő frissítési szolgáltatást is megkerüli a proxy, egy hasonló módosítást a ApplicationProxyConnectorUpdaterService.exe.config fájlba. Ez a fájl itt található: C:\Program Files\Microsoft AAD App Proxy Connector Frissítőjének.

Ügyeljen arra, hogy készítsen másolatot az eredeti fájlok abban az esetben meg kell visszaállítania az alapértelmezett .config kiterjesztésű fájlokat.

## <a name="use-the-outbound-proxy-server"></a>A kimenő proxykiszolgáló használata

Egyes környezetekben megkövetelése minden kimenő forgalom haladhat végig az kimenő proxy, kivételhiba jelentkezése nélkül. Ennek eredményeképpen a proxy megkerülése lehetőség nem érhető el.

Nyissa meg a kimenő proxy connector forgalmat is konfigurálhatja, az alábbi ábrán látható módon:

 ![Egy kimenő proxyn keresztül történő Ugrás az Azure AD-alkalmazásproxy-összekötő forgalom konfigurálása](./media/application-proxy-configure-connectors-with-proxy-servers/configure-proxy-settings.png)

Miatt, amelyek csak a kimenő forgalom, nincs szükség a tűzfalon keresztüli bejövő hozzáférés konfigurálásához.

>[!NOTE]
>Alkalmazásproxy nem támogatja a hitelesítési más proxyk számára. Az összekötő/frissítési hálózati szolgáltatásfiókokat kell kapcsolódnia kell a proxy éppen lekéri a hitelesítés nélkül.

### <a name="step-1-configure-the-connector-and-related-services-to-go-through-the-outbound-proxy"></a>1. lépés: Az összekötő és a kapcsolódó szolgáltatások haladhat végig a kimenő proxy konfigurálása

WPAD van engedélyezve a környezetben, és megfelelő konfigurálása, ha az összekötő automatikusan észleli a kimenő proxykiszolgáló és kísérlet rá. Azonban explicit módon konfigurálhatja az összekötő egy kimenő proxy végrehajtania.

Ehhez az szükséges, módosítsa a C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config fájlt, és adja hozzá a *System.NET névtérbeli* látható a fenti szakaszban. Változás *proxyserver:8080* a helyi proxykiszolgáló nevét vagy IP-cím és a portot, amelyet figyeli az megfelelően.

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

Ezután konfigurálja az összekötőt frissítési szolgáltatást hasonló módosítást végez a C:\Program Files\Microsoft AAD App Proxy Connector Updater\ApplicationProxyConnectorUpdaterService.exe.config fájl által a proxy használatára.

### <a name="step-2-configure-the-proxy-to-allow-traffic-from-the-connector-and-related-services-to-flow-through"></a>2. lépés: Az összekötő és a kapcsolódó szolgáltatások keresztül érkező forgalmat engedélyezi a proxy konfigurálása

Négy szempontot kell figyelembe venni a kimenő proxynál:
* Proxy kimenő szabályok
* A proxy hitelesítése
* Proxy portok
* SSL-ellenőrzést

#### <a name="proxy-outbound-rules"></a>Proxy kimenő szabályok
Az összekötő szolgáltatás hozzáférés a következő végpontok hozzáférés engedélyezése:

* *.msappproxy.net
* *.servicebus.windows.net

A kezdeti regisztráció a következő végpontok hozzáférés engedélyezése:

* login.windows.net
* login.microsoftonline.com

Ha nem engedélyezi a csatlakozást a teljes tartománynév alapján és IP-címtartományok Ehelyett meg kell adni, használja ezeket a beállításokat:

* Az összes cél összekötő kimenő hozzáférés engedélyezése.
* Az összekötő kimenő hozzáférést minden olyan a [Azure datacenter IP-címtartományok](https://www.microsoft.com/en-gb/download/details.aspx?id=41653). Használatával a Azure datacenter IP-címtartománylista ellenőrző, hogy hetente kell frissíteni. Győződjön meg arról, hogy a hozzáférési szabályok megfelelően vannak-e frissíti a folyamat bevezetni kell. Csak egy részét az IP-címek alkalmazásakor megszünteti a konfigurációt.

#### <a name="proxy-authentication"></a>A proxy hitelesítése

A proxy hitelesítése jelenleg nem támogatott. Az aktuális javasoljuk, hogy az összekötő a Internet célhelyre névtelen hozzáférés engedélyezése.

#### <a name="proxy-ports"></a>Proxy portok

Az összekötő lehetővé teszi a kimenő SSL-alapú kapcsolatok KAPCSOLATI módszer használatával. Ez a módszer lényegében állít be egy alagúton, a kimenő proxyn keresztül. Engedélyezi a 443-as és a 80-as portra tunneling a proxykiszolgáló konfigurálása.

>[!NOTE]
>A Service Bus a HTTPS-KAPCSOLATON keresztül futtatása 443-as portot használja. Azonban az alapértelmezés szerint a Service Bus kísérletet tett a közvetlen TCP-kapcsolatok, és csak akkor, ha közvetlen kapcsolatot sikertelen visszaáll HTTPS.

#### <a name="ssl-inspection"></a>SSL-ellenőrzést
Ne használjon SSL-ellenőrzést az összekötő-forgalom esetén, mert az összekötő forgalom problémákat okoz. Az összekötő az alkalmazásproxy-szolgáltatással való hitelesítésre szolgáló tanúsítványt használ, és lehet, hogy a tanúsítvány számára elveszett SSL-ellenőrzés során. 

## <a name="troubleshoot-connector-proxy-problems-and-service-connectivity-issues"></a>Összekötő proxy problémák és a szolgáltatás kapcsolódási problémák elhárítása
Most már megtekintheti az összes forgalom a proxyn keresztül történő továbbítására. Ha problémába ütközik, a következő hibaelhárítási információk segítségül szolgálhat.

A legjobb azonosítása és összekötő csatlakozási problémák módja az összekötő szolgáltatás indítása közben hálózati rögzítőeszközt érvénybe. Az alábbiakban a hálózati nyomkövetés szűrésére és rögzítésére gyors tippek.

A felügyeleti eszköz az Ön által választott használható. Ez a cikk alkalmazásában Microsoft Message Analyzert használtuk. Is [töltse le a Microsoft](https://www.microsoft.com/download/details.aspx?id=44226).

Az alábbi példák jellemzőek Message Analyzer, de bármely eszköz alkalmazhatja ezeket az alapelveket.

### <a name="take-a-capture-of-connector-traffic"></a>Az összekötő forgalom rögzítés igénybe

A kezdeti hibaelhárítási, hajtsa végre az alábbi lépéseket:

1. A "Services.msc" parancsot állítsa le az Azure AD alkalmazásproxy-összekötő szolgáltatást.

   ![A "Services.msc" parancsot az Azure AD alkalmazásproxy-összekötő szolgáltatás](./media/application-proxy-configure-connectors-with-proxy-servers/services-local.png)

2. Message Analyzer futtatása rendszergazdaként.
3. Válassza ki **helyi nyomkövetés indításához**.

   ![Indítsa el a hálózati rögzítési](./media/application-proxy-configure-connectors-with-proxy-servers/start-local-trace.png)

3. Indítsa el az Azure AD alkalmazásproxy-összekötő szolgáltatást.
4. Állítsa le a hálózati rögzítést.

   ![Hálózati rögzítési leállítása](./media/application-proxy-configure-connectors-with-proxy-servers/stop-trace.png)

### <a name="check-if-the-connector-traffic-bypasses-outbound-proxies"></a>Ha az összekötő forgalom megkerüli a kimenő proxyk ellenőrzése

Ha konfigurálta az alkalmazásproxy-összekötő a proxykiszolgálót, és közvetlenül kapcsolódjon az alkalmazásproxy-szolgáltatás, a hálózati rögzítőeszközt a TCP-kapcsolat sikertelen kísérletek a megkeresni kívánt. 

A Message Analyzer szűrő használatával azonosíthatja ezeket a kísérleteket. Adja meg `property.TCPSynRetransmit` a Szűrő mezőbe, majd válassza ki a **alkalmaz**. 

SZIN csomagot küldött egy TCP-kapcsolatot létesíteni az első csomag. Ez a csomag a válasz nem ad vissza, ha a szinkronizálás a mi reattempted van. Az előző szűrő segítségével bármely újraküldött SYNs láthatja. Ezután ellenőrizheti, hogy ezek SYNs megfelelnek-e a connector kapcsolatos forgalommal.

Ha várhatóan az Azure-szolgáltatásokhoz való közvetlen kapcsolat az összekötőt, SynRetransmit válaszok 443-as porton arra utal, hogy rendelkezik-e a hálózati vagy probléma.

### <a name="check-if-the-connector-traffic-uses-outbound-proxies"></a>Ha az összekötő forgalom használja-e a kimenő proxyk ellenőrzése

Ha konfigurálta a Application Proxy connector forgalmat nyissa meg a webproxy-kiszolgálókon keresztül, keresse meg a proxy nem sikerült https-kapcsolatok szeretné. 

Adja meg a hálózati rögzítőeszközt a kapcsolódási kísérletek a szűrésére, `(https.Request or https.Response) and tcp.port==8080` a Message Analyzer szűrőben cseréje a 8080-as a proxy portja. Válassza ki **alkalmaz** a szűrő eredmények megtekintése érdekében. 

Az előző szűrő csak a HTTPs-kéréseket és a válaszok jeleníti meg a proxykiszolgáló port és a. A CONNECT-kérelmeket, amelyek megjelenítik a proxy-kiszolgálóval folytatott kommunikációhoz van szüksége. Sikeres, akkor egy HTTP-OK (200-as) választ kapott.

Ha megjelenik a többi válaszkódnál, mint a 407 vagy 502-es, ez azt jelenti, hogy a proxy hitelesítést igénylő vagy nem engedélyezi a forgalom valamilyen más okból. Ezen a ponton bevonása, akik a proxy server támogatási csoportjához.

## <a name="next-steps"></a>További lépések

- [Az Azure AD-alkalmazásproxy összekötők ismertetése](application-proxy-connectors.md)

- Ha problémába ütközik az összekötő csatlakozási problémák, a kérdés feltevése a [Azure Active Directory fórum](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD&forum=WindowsAzureAD) vagy jegy létrehozása és a támogatási csapatával.

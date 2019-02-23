---
title: Az Azure Application Gateway összetevők
description: Ez a cikk a különféle összetevők Application Gateway ismertetése
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: 14f400a1b85e213496ac98996d6c2378cf559026
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2019
ms.locfileid: "56675801"
---
# <a name="application-gateway-components"></a>Application Gateway összetevők

 Egy Application Gateway-ügyfelek a kapcsolódási pont szolgál. Az alkalmazások bejövő forgalmának több háttérkészletek, például az Azure virtuális gépek, VMSS, App Services vagy a helyszíni vagy külső kiszolgálók között osztja el. Ehhez használja az alábbiakban néhány összetevők:

![application-gateway-components](.\media\application-gateway-components\application-gateway-components.png)

## <a name="frontend-ip"></a>Előtérbeli IP-címet

Fronted IP-cím része az Alkalmazásátjáró (az Internet Protocol) IP-címére. Konfigurálhatja az Application Gateway, vagy hogy a nyilvános IP-cím vagy egy magánhálózati IP-címet, vagy mindkettőt. Csak egy nyilvános IP-cím egy application gateway esetében támogatott. A virtuális hálózat és a nyilvános IP-címet az Application Gateway ugyanazon a helyen kell lennie.

### <a name="static-vs-dynamic-public-ip"></a>Statikus és dinamikus nyilvános IP-cím

A v1 Termékváltozatot támogatja a statikus belső IP-címek, de nem támogatja a statikus nyilvános IP-címek. A VIP-címet módosíthatja, ha az application gateway leállítása és elindítása. Az application gateway társított DNS-név nem változik az átjáró életciklusa. Ezért ajánlott egy CNAME-aliast használni, és az application gateway DNS-címére mutasson.

Az Application Gateway v2 szintű Termékváltozatot belső IP-címek statikus nyilvános IP-címek és egyben támogatja. Csak egy nyilvános IP-cím egy application gateway esetében támogatott.

Miután létrehozott egy előtérbeli IP-címet, hozzá rendelve *figyelői* amely ellenőrizze, hogy a bejövő kéréseket az előtérbeli IP-címen.

## <a name="listeners"></a>Figyelők

Az Application Gateway használata előtt hozzá kell adnia egy vagy több figyelő. Egy figyelő használatával, amely az Application Gateway ellenőrzi a kapcsolatot a bejövő kérelmek használatával a protokoll és port, amelyet konfigurálnia folyamat során a rendszer. Miután a figyelő azt észleli, hogy az ügyfelektől érkező bejövő kéréseket, az Application Gateway irányítja. ezeket a kérelmeket a háttérkiszolgálók, a háttérkészletben használatával kell megadni a figyelő, amely a bejövő kérelem érkezett kérés útválasztási szabályokat.

Figyelők a következő portokat és protokollokat támogatja:

### <a name="ports"></a>Portok

Ez az a port, amelyen a figyelő az ügyfél kérés figyel. A port konfigurációját a következő tartományban van engedélyezve: 1-65535

### <a name="protocols"></a>Protokollok

Az Application Gateway az alábbi 4 protokollokat támogatja: HTTP, HTTPS, HTTP/2, Websocket

A figyelő konfigurálása, közben kell a HTTP és HTTPS protokoll közül választhat. Az Application Gateway natív támogatást nyújt a websockets protokoll és a HTTP/2 protokoll. A HTTP és HTTPS-figyelőként való használja a websockets protokollt. 

HTTP/2 protokoll támogatása az application gateway figyelői-ügyfelekhez érhető el. A háttér-kiszolgálókészlethez kommunikációs HTTP/1.1 felett van. Alapértelmezés szerint a HTTP/2-támogatás le van tiltva. A következő Azure PowerShell-lel kódrészlet példakód bemutatja, hogyan tehető lehetővé, hogy:

```azurepowershell
$gw = Get-AzureRmApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzureRmApplicationGateway -ApplicationGateway $gw
```

Alapértelmezés szerint engedélyezve van a Websocket-támogatás. Kizárólag WebSocket-támogatásra vonatkozó felhasználói beállítás nem létezik.

Használhat egy HTTPS-figyelő fog kiszervezheti a munkát, titkosítási és visszafejtési az Application Gateway az, hogy a webkiszolgálók sikerült megszabaduljanak a költséges titkosítási és visszafejtési terhelés, és az alkalmazások összpontosíthat SSL-lezárást az üzleti logikát. Legalább egy SSL-kiszolgálótanúsítványt kell telepítenie egy HTTPS-figyelő esetében. További információkért lásd: [SSL-lezárást konfigurálása](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)

### <a name="custom-error-pages"></a>Egyéni hibalapok

Az Application Gatewayjel testreszabhatók a hibaoldalak. Az egyéni hibaoldalakon feltüntetheti saját védjegyeit, és egyéni elrendezést használhat. Az Application gateway is egy egyéni hibalap megjelenítése, amikor a kérelem nem érhető el a háttérrendszert. További információkért lásd: [egyéni hibalapok az Application Gateway számára](https://docs.microsoft.com/azure/application-gateway/custom-error)

### <a name="types-of-listeners"></a>Figyelők típusai

Figyelők két típusa van:

- **Alapszintű**: Ez a figyelő típusú egyetlen tartományt helyre, ahol az Application Gateway IP-címre egyetlen DNS-leképezést rendelkezik figyeli. Ez a figyelő konfigurációra szükség, ha egy Application gateway mögött egyetlen hely
- **Többhelyes**: A figyelő konfigurációra szükség, ha konfigurál egynél több webalkalmazás ugyanazon application gateway-példányon. Ez lehetővé teszi a hatékonyabb topológiát beállításához ad hozzá egy application gateway akár 100 webhelyet. Mindegyik webhelyet a saját háttérkészletéhez lehet irányítani. Példa:  A három altartományok – abc.alpha.com, xyz.alpha.com és az Application Gateway IP-címre mutató pqr.alpha.com. Hozzon létre 3 figyelői "többhelyes" típusú, és a megfelelő port és protokoll beállítását minden egyes figyelő konfigurálása. 

Miután létrehozott egy figyelőt, társítsa azt egy kérés útválasztási szabályt, amely meghatározza, hogy hogyan a kérelem érkezett a figyelő a háttérrendszer lesznek irányítva.

Figyelők láthatók a rendelés feldolgozása történik. Ezért ha egy alapszintű figyelő megfelel egy bejövő kérésnek feldolgozza a először. Többhelyes figyelőket konfigurálni kell egy alapszintű figyelő annak biztosítása érdekében az adatforgalmat a megfelelő háttér előtt.

## <a name="request-routing-rule"></a>Kérelemirányítási szabály

Ez az Application Gateway a legfontosabb összetevője, amely azt határozza meg, hogyan társított Ez a szabály a figyelőt a forgalmat át szeretné irányítani. A szabály köti a figyelőt, a háttérkiszolgáló-készlet és a háttérbeli HTTP-beállítások, és meghatározza, melyik háttérkiszolgáló-készlethez átirányítva a forgalom kell lennie, ha elér egy adott figyelőt. Egy figyelő csak egy szabályt lehet rendelni.

Kérelem-útválasztási szabályok két típusú lehet:

- **Alapszintű:** A kapcsolódó figyelőt az összes kérelem (Példa: blog.contoso.com/*) a társított háttérkészlet, a kapcsolódó HTTP-beállítás használatával továbbítja.
- **Útvonalalapú:** Az ilyen típusú szabály lehetővé teszi a kérelmek átirányítása a a kapcsolódó figyelőt, hogy egy adott háttérkészlet, a kérelem URL-cím alapján. Ha a kérelem URL-cím elérési útja megegyezik egy útvonalalapú szabály az elérésiút-minta, a irányítja a kérelmeket, hogy a szabály használatával. Az elérési út mintája csak az elérési utat az URL-cím, hogy a lekérdezési paraméterek nem érvényes. 

Az elérési útját egy figyelőt a kérelem URL-címe nem egyezik a útvonalalapú szabály, akkor a rendszer ilyenkor úgy irányítja a kérést a *alapértelmezett* háttérkészlet és a *alapértelmezett* HTTP-beállítások.

Szabályok feldolgozása a sorrendben vannak konfigurálva. Javasoljuk, hogy a többhelyes szabályok előtt alapvető szabályok csökkenti annak esélyét, hogy a forgalom lesz irányítva a nem megfelelő háttérrendszer és az alapszintű szabályt szeretne megegyeznek a forgalmat a port előtt a kiértékelt többhelyes szabály alapján vannak konfigurálva.

### <a name="redirection-support"></a>Redirection Support

A kérelem-útválasztási szabály lehetővé teszi az Application gatewayen forgalom átirányítása. Ez egy általános átirányítási mechanizmus, így a szabályokkal bármilyen megadott portról és portra átirányíthat. Ez segíthet engedélyezze az automatikus HTTP – HTTPS átirányításról, győződjön meg arról, alkalmazás és a felhasználók közötti minden kommunikáció titkosított elérési útnak keresztül történik. Megadhatja a célfigyelőt, vagy külső helyre, amelyhez átirányítás van szükség. A konfigurációs elem támogatja a beállítások engedélyezéséhez, Hozzáfűzés, az URI elérési út és a lekérdezési karakterláncot az átirányított URL-címet is. Azt is beállíthatja, hogy átirányítása egy ideiglenes (HTTP-állapotkód: 302) vagy egy Állandó átirányítás (HTTP-állapotkód 301)-e. Alapszintű szabály használata esetén az átirányítási konfiguráció társítva a forrás-figyelő és globális átirányítást. Útvonalalapú szabály használata esetén az átirányítási konfiguráció meg az URL-Címtérkép van meghatározva. Ezért csak érvényes egy helyet a megadott elérési út területéhez. További információkért lásd: [átirányítja a forgalmat az Application Gateway](https://docs.microsoft.com/azure/application-gateway/redirect-overview).

### <a name="http-headers"></a>HTTP-fejlécek

Az Application Gateway x – továbbított – a x továbbított protokollverzió és x továbbított port fejlécek szúr be a háttérrendszere továbbítja a kérelmet. Az x-továbbított – a fejléc formátuma IP:Port vesszővel tagolt listája. Az x továbbított proto érvényes értékei a következők: http vagy HTTPS protokollt. X továbbított port portot határozza meg, amelyen a kérelem eléri az application gatewayben. Az Application Gateway is szúr be a X-eredeti-állomásfejlécet, amely tartalmazza az eredeti állomásfejlécet, amelyhez a kérés érkezett. Ezt a fejlécet hasznos olyan szituációkra, mint az Azure-webhely-integráció, ahol a bejövő állomásfejléc módosította-e előtt az adatforgalmat, a háttérkiszolgáló.

#### <a name="rewrite-http-headers"></a>HTTP-fejlécek átírása

A kérés útválasztási szabályok hozzáadása, eltávolítása vagy frissíteni a HTTP (S)-kérelmek és válaszfejlécek, ha az ügyfél és a háttérkiszolgáló közötti áthelyezése a kérések és válaszok csomagok keresztül az Application Gateway-készletek használatával. A fejlécek csak nem állítható be statikus értékre, hanem és egyéb fontos kiszolgálói változókat. Ez segít elvégezni néhány fontos alkalmazási, például az ügyfelek, eltávolítja a háttérrendszer bizalmas információ hozzáadása a további biztonsági intézkedéseket, és így tovább IP-címének beolvasása. További információkért lásd:[Újraírási HTTP-fejlécek az Application Gateway-en](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)

## <a name="http-settings"></a>HTTP-beállítások

Az Application Gateway irányítja a forgalmat a háttérkiszolgálókhoz, a portszám, protokoll és egyéb ehhez az erőforráshoz megadott beállításokat. 

A háttérkészletek a következő portokat és protokollokat támogatja:

### <a name="ports"></a>Portok

Ez az a port, amelyen a háttérkiszolgálók figyelik a az Application Gateway érkező forgalomra. A port konfigurációját a következő tartományban van engedélyezve: 1-65535

### <a name="protocols"></a>Protokollok

Az Application Gateway támogatja a HTTP és HTTPS protokollok útválasztási kérelmek a háttérkiszolgálókhoz.

Ha a HTTP protokollt választja, majd forgalom folyamatok nem titkosított a háttérkiszolgálókhoz. 

Ezekben az esetekben, ahol a háttérkiszolgálók felé irányuló titkosítatlan kommunikáció lehetőség nem elfogadható HTTPS protokollt kell kiválasztani. Ezt a beállítást, a figyelő a HTTPS protokoll kiválasztásával együtt lehetővé teszi, hogy engedélyezze [teljes körű SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview). Amelyek engedélyezik küldheti a bizalmas adatokat a háttérkiszolgálók titkosítva. A biztonságos kommunikációhoz tanúsítványt kell konfigurálni a teljes körű SSL-lel engedélyezett háttérkészlet minden egyes háttérkiszolgálójához.

A HTTP-beállítás használatával különböző képességeket használhatja:

### <a name="cookie-based-session-affinity"></a>Cookie-alapú munkamenet-affinitás

Ez a funkció akkor hasznos, ha meg szeretné tartani a felhasználói munkamenet ugyanazon a kiszolgálón. Az átjáróval kezelt cookie-k használatával az Application Gateway képes egy felhasználói munkamenet minden újabb forgalmát ugyanarra a kiszolgálóra irányítani feldolgozásra. Ez a funkció olyan esetekben lehet fontos, amelyekben egy felhasználói munkamenethez tartozó munkamenet-állapotot helyileg ment a rendszer a kiszolgálón.

Ha az alkalmazás nem tudja kezelni a cookie-alapú affinitás, majd nem tudja használni ezt a funkciót

### <a name="connection-draining"></a>Kapcsolatkiürítés

A kapcsolatkiürítéssel zökkenőmentesen végrehajtható a háttérkészlettagok eltávolítása a tervezett szolgáltatásfrissítések során. Ez a beállítás háttérkészlet összes tagjára alkalmazhatók szabály létrehozása során. Az engedélyezést követően az Application Gateway gondoskodik arról, hogy azon háttérkészletbeli példányok, amelyek regisztrációja megszüntetés alatt áll, ne fogadjanak új kéréseket, a meglévő kérések viszont végre legyenek hajtva a megadott időkorláton belül. Ez egyaránt vonatkozik azokra a háttérpéldányokra, amelyek API-hívással, explicit módon lettek eltávolítva a háttérkészletből, és azokra is, amelyeket az állapot-mintavételek „Nem kifogástalan” állapotúnak minősítenek.

### <a name="override-backend-path"></a>Háttéralkalmazás elérési útjának felülbírálása

Ez a funkció lehetővé teszi, hogy az elérési utat az URL-cím felülbírálása a úgy, hogy a megadott elérési útra vonatkozó kérelmek átirányíthatók egy másik elérési utat a. Például ha kíván irányíthatja a kérelmeket contoso.com/images alapértelmezett, majd adja meg a '/' ebbe a szövegmezőbe, és ezt követően csatlakoztassa a szabályhoz társított contoso.com/images a HTTP-beállítás.

### <a name="pick-host-name-from-a-backend-address"></a>Válasszon ki egy címet állomás nevét

Ez a funkció beállítja a *gazdagép* a háttérkészlet (IP vagy FQDN) állomásneve a kérelemben szereplő tartományfejléc. Ez hasznos az esetekben, ahol a tartománynév, a háttérrendszer eltér az alkalmazás, például egy forgatókönyvet, ahol az Azure App Service háttérrendszer szolgál a DNS-nevét. Ennek oka az, mivel az Azure App Service egy több-bérlős környezet használatával egy megosztott terület egyetlen IP-címmel, egy App Service-ben elérhető a gazdagép neve, az egyéni tartomány beállításai csak a. Alapértelmezés szerint "example.azurewebsites.net", és ha szeretne hozzáférni az App Service használatával Alkalmazásátjáró egy állomásnevet, az App Service-ben nem regisztrált vagy az Application Gateway FQDN, bírálja felül az eredeti kérést, az alkalmazásnak az állomásnevet kell Szolgáltatás állomásneve.

### <a name="host-override"></a>**Gazdagép felülbírálása**

Ez a lehetőség váltja fel a *gazdagép* a bejövő kérelem, az Application Gateway az itt megadott állomás nevét a fejlécet. 

Miután létrehozott egy HTTP-beállítás, kell társítani egy vagy több kérés útválasztási szabályokat.

## <a name="backend-pool"></a>Háttérkészlet

A háttérkészlet irányíthatja a kérelmeket a háttérkiszolgálók, amely a kérés kiszolgálása lesz használatos. Háttérkészletek összeállítható a hálózati adapterek, a virtual machine scale sets, nyilvános IP-címek, belső IP-címek, teljesen minősített neve (FQDN), és több-bérlős háttéralkalmazások hasonlóan az Azure App Service-ben. Application Gateway háttérkészlet érintett tagjai nem egy rendelkezésre állási csoporthoz vannak társítva. Háttérkészletek tagjai lehetnek, fürtök és adatközpontok között, vagy Azure-on kívül mindaddig, amíg az IP-kapcsolattal rendelkeznek. A kérelmek különböző típusú különböző háttérkészletek hozhat létre. Hozzon létre például egy háttérkészlet általános kérelmek és a kéréseket, a mikroszolgáltatások az alkalmazás más háttérkészlet.

Háttérkészlet létrehozását követően társítsa azt egy vagy több kérés útválasztási szabályokat kell. Szükség minden egyes háttérkészlethez állapotfigyelő mintavételezőket konfigurálása az Application Gateway-en. Amikor egy kérés útválasztási szabály a feltétel teljesül, az Application Gateway továbbítja a forgalmat a kifogástalan állapotú kiszolgálók a megfelelő háttérkészlet (határoz meg az állapotmintákat).

## <a name="health-probes"></a>Állapotminták

Az Azure Application Gateway alapértelmezés szerint a háttérkészlet összes erőforrásának állapotát figyeli, és automatikusan eltávolítja az összes erőforrást a készletből sérültnek. Az Application Gateway továbbra is figyeli a nem megfelelő állapotú példányokat, és hozzáadja őket a megfelelő háttér-készlet számára, elérhetővé válnak, és a állapotadat-mintavételek válaszolni. Az Application gateway küld az állapotadat-mintavételek a definiált a háttér-HTTP-beállítások ugyanazt a portot.

Alapértelmezett mintavételi állapotfigyelés használatán is testreszabhatja az állapotminta az alkalmazás követelményeinek megfelelően. Az egyéni minták lehetővé teszik egy részletesebb felügyelheti a Szolgáltatásállapot-figyelést. Egyéni mintavétel használata esetén konfigurálhatja úgy a mintavételi időköz, az URL-cím és teszteléséhez elérési út és a háttérkészlet-példány állapota nem megfelelőként jelölés elfogadásának hány sikertelen válaszokat. Azt javasoljuk, hogy konfigurálja-e az egyéni minták minden háttérkészlet állapotának monitorozásához. További információkért lásd: [figyelhető az Application gateway állapota](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview)

## <a name="next-steps"></a>További lépések

Miután megismerkedett a Application Gateway-összetevők, [hozhat létre egy Application Gateway az Azure Portalon](quick-create-portal.md) vagy egy [hozzon létre egy Application Gateway PowerShell-lel](quick-create-powershell.md) vagy [hozzon létre egy Azure CLI használatával az Application Gateway](quick-create-cli.md).
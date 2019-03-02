---
title: Az Azure Application Gateway összetevők
description: Ez a cikk a különféle összetevők Application Gateway ismertetése
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: a7b4dd14cd6270838fde33b0b62ec5adeceb3434
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2019
ms.locfileid: "57247018"
---
# <a name="application-gateway-components"></a>Application gateway összetevők

 Egy application gateway-ügyfelek a kapcsolódási pont szolgál. Az alkalmazások bejövő forgalmának több háttérkészletek, például az Azure virtuális gépek, a virtual machine scale sets, App Services vagy a helyszíni vagy külső kiszolgálók között osztja el. Ehhez használja az ebben a cikkben leírt több összetevőt.

![application-gateway-components](.\media\application-gateway-components\application-gateway-components.png)

## <a name="frontend-ip-address"></a>Előtérbeli IP-cím

Egy előtérbeli IP-cím pedig az application gateway társított IP-címe. Konfigurálhatja az application gateway, vagy hogy a nyilvános IP-címmel vagy magánhálózati IP-cím, vagy mindkettőt. Csak egy nyilvános IP-cím egy application gateway esetében támogatott. A virtuális hálózat és a nyilvános IP-címet az application gateway ugyanazon a helyen kell lennie.

### <a name="static-vs-dynamic-public-ip-address"></a>Static vs dynamic public IP address

A v1 Termékváltozatot statikus belső IP-címeket támogatja, de nem támogatja a statikus nyilvános IP-címeket. A VIP-címet módosíthatja, ha az application gateway leállítása és elindítása. Az application gateway társított DNS-név nem változik az átjáró életciklusa. Ezért ajánlott egy CNAME-aliast használni, és az application gateway DNS-címére mutasson.

Az Application Gateway v2 szintű Termékváltozatot támogatja a statikus nyilvános IP-címeket, valamint a statikus belső IP-címeket. Csak egy nyilvános IP-cím vagy egy magánhálózati IP-cím támogatott.

Egy előtérbeli IP-cím társítva egy *figyelő* létrehozása után. Egy a figyelő ellenőrzi az előtérbeli IP-cím a bejövő kérelmeket.

## <a name="listeners"></a>Figyelők

Az application gateway használata előtt hozzá kell adnia egy vagy több figyelő. Egy figyelő olyan logikai entitás, amely ellenőrzi a bejövő kapcsolódási kérelmeket és a kéréseket fogad el, ha a protokoll, port, állomás és IP-cím egyezik a figyelő konfigurációjába. Az application gatewayhez csatolt több kérésfigyelőt is lehet, és a egy protokoll használható. Miután a figyelő azt észleli, hogy az ügyfelektől érkező bejövő kéréseket, az Application Gateway irányítja. ezeket a kérelmeket a háttérkiszolgálók, a háttérkészletben használatával kell megadni a figyelő, amely a bejövő kérelem érkezett kérés útválasztási szabályokat.

Figyelők a következő portokat és protokollokat támogatja:

### <a name="ports"></a>Portok

Ez az a port, amelyen a figyelő az ügyfélkérelem figyel. Konfigurálhatja a beállításnak 1 és közötti 65502 V1 Termékváltozatot és 1 a 65199 V2 szintű Termékváltozatot a portokat.

### <a name="protocols"></a>Protokollok

Az Application gateway az alábbi négy protokollokat támogatja: HTTP, HTTPS, HTTP/2 és WebSocket

A figyelő konfigurálásakor kell a HTTP és HTTPS protokollok közül választhat. Az Application gateway natív támogatást nyújt a websockets protokoll és a HTTP/2 protokoll. A HTTP és HTTPS-figyelőként való használja a websockets protokollt.

HTTP/2 protokoll támogatása az application gateway figyelői-ügyfelekhez érhető el. A háttér-kiszolgálókészlethez kommunikációs HTTP/1.1 felett van. Alapértelmezés szerint a HTTP/2-támogatás le van tiltva. A következő Azure PowerShell-lel kódrészlet példakód bemutatja, hogyan tehető lehetővé, hogy:

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzApplicationGateway -ApplicationGateway $gw
```

Alapértelmezés szerint engedélyezve van a Websocket-támogatás. Kizárólag WebSocket-támogatásra vonatkozó felhasználói beállítás nem létezik.

SSL-lezárást egy HTTPS-figyelőre is használhat. Egy HTTPS-figyelő kiszervezi a titkosítás és a visszafejtés, az application gateway, hogy a webkiszolgálók nem rójanak úgy, hogy a terhelést. Az alkalmazások majd szabadon az üzleti logikára összpontosíthat.

Legalább egy SSL-kiszolgálótanúsítványt kell telepítenie egy HTTPS-figyelő esetében. További információkért lásd: [SSL-lezárást konfigurálása](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)

### <a name="custom-error-pages"></a>Egyéni hibalapok

Az Application gateway lehetővé teszi, hogy hozzon létre egyéni hibalapok alapértelmezett hibák részleteiről szóló lap megjelenítése helyett. Az egyéni hibaoldalakon feltüntetheti saját védjegyeit, és egyéni elrendezést használhat. Az Application gateway is egy egyéni hibalap megjelenítése, amikor a kérelem nem érhető el a háttérrendszert. További információkért lásd: [egyéni hibalapok az Application Gateway számára](https://docs.microsoft.com/azure/application-gateway/custom-error).

### <a name="types-of-listeners"></a>Figyelők típusai

Figyelők két típusa van:

- **Alapszintű**: Ez a figyelő típusú egyetlen tartományt helyre, ahol az application Gateway IP-címre egyetlen DNS-leképezést rendelkezik figyeli. A figyelő konfigurációra szükség, ha egy application gateway mögött egyetlen hely.
- **Többhelyes**: Ez a figyelő konfigurációra szükség, egynél több webalkalmazás ugyanazon alkalmazásátjárókhoz való konfigurálásakor. Ez lehetővé teszi a hatékonyabb topológiát beállításához ad hozzá egy application gateway akár 100 webhelyet. Mindegyik webhelyet a saját háttérkészletéhez lehet irányítani. Példa:  A három altartományok – abc.contoso.com, xyz.contoso.com és az application Gateway IP-címre mutató pqr.contoso.com. Hozzon létre három figyelői típusú *többhelyes* és a megfelelő port minden egyes figyelő konfigurálása és a protokoll beállítása.

Miután létrehozott egy figyelőt, társítsa azt egy kérés útválasztási szabályt, amely meghatározza, hogy hogyan a kérelem érkezett a figyelő a háttérrendszer lesznek irányítva.

Figyelők láthatók a rendelés feldolgozása történik. Ezért ha egy alapszintű figyelő megfelel egy bejövő kérésnek feldolgozza a először. Többhelyes figyelőket konfigurálni kell egy alapszintű figyelő annak biztosítása érdekében az adatforgalmat a megfelelő háttér előtt.

## <a name="request-routing-rule"></a>Kérelemirányítási szabály

Ez az application gateway a legfontosabb összetevője, amely meghatározza, hogy a társított Ez a szabály a figyelőt az adatforgalmat. A szabály köti a figyelőt, a háttérkiszolgáló-készlet és a háttérbeli HTTP-beállítások. Azt határozza meg, melyik háttérkiszolgáló-készlethez átirányítva a forgalom kell lennie, ha elér egy adott figyelőt. Egy figyelő csak egy szabályt lehet rendelni.

Kérelem-útválasztási szabályok két típusú lehet:

- **Alapszintű:** A kapcsolódó figyelőt az összes kérelem (Példa: blog.contoso.com/*) a társított háttérkészlet, a kapcsolódó HTTP-beállítás használatával továbbítja.
- **Útvonalalapú:** Ezt a szabálytípust lehetővé teszi a kérelmek átirányítása a a kapcsolódó figyelőt, hogy egy adott háttérkészlet, a kérelem URL-cím alapján. Ha a kérelem URL-cím elérési útja megegyezik egy útvonalalapú szabály az elérésiút-minta, a irányítja a kérelmeket, hogy a szabály használatával. Az elérési út mintája csak az elérési utat az URL-cím, hogy a lekérdezési paraméterek nem érvényes.

Az elérési útját egy figyelőt a kérelem URL-címe nem egyezik a útvonalalapú szabály, akkor a rendszer ilyenkor úgy irányítja a kérést a *alapértelmezett* háttérkészlet és a *alapértelmezett* HTTP-beállítások.

Szabályok konfigurálják a rendelés feldolgozása. Javasoljuk, hogy a többhelyes szabályok előtt alapvető szabályok csökkenti annak esélyét, hogy a forgalom lesz irányítva a nem megfelelő háttérrendszer és az alapszintű szabályt szeretne megegyeznek a forgalmat a port előtt a kiértékelt többhelyes szabály alapján vannak konfigurálva.

### <a name="redirection-support"></a>Átirányítási támogatás

A kérelem-útválasztási szabály lehetővé teszi az application gatewayen forgalom átirányítása. Ez egy általános átirányítási mechanizmus, így a szabályokkal bármilyen megadott portról és portra átirányíthat. Ez segíthet engedélyezze az automatikus HTTP – HTTPS átirányításról, annak érdekében, hogy az alkalmazások és a felhasználók között minden kommunikáció titkosított elérési útnak keresztül történik. Megadhatja a célfigyelőt, vagy a kívánt átirányítás külső helyre. A konfigurációs elem támogatja a beállítások engedélyezéséhez, Hozzáfűzés, az URI elérési út és a lekérdezési karakterláncot az átirányított URL-címet is. Azt is beállíthatja, hogy átirányítása egy ideiglenes (HTTP-állapotkód: 302) vagy egy Állandó átirányítás (HTTP-állapotkód 301)-e. Alapszintű szabály használata esetén az átirányítási konfiguráció társítva a forrás-figyelő és globális átirányítást. Útvonalalapú szabály használata esetén az átirányítási konfiguráció meg az URL-Címtérkép van meghatározva. Ezért csak érvényes egy helyet a megadott elérési út területéhez. További információkért lásd: [átirányítja a forgalmat az Application Gateway](https://docs.microsoft.com/azure/application-gateway/redirect-overview).

### <a name="http-headers"></a>HTTP-fejlécek

Az Application gateway x – továbbított – a x továbbított protokollverzió és x továbbított port fejlécek szúr be a háttérrendszere továbbítja a kérelmet. Az x-továbbított – a fejléc formátuma IP:port vesszővel tagolt listája. Az x továbbított proto érvényes értékei a következők: HTTP vagy HTTPS PROTOKOLLT. X továbbított port portot határozza meg, amelyen a kérelem eléri az application gatewayben. Az Application gateway is szúr be a X-eredeti-állomásfejlécet, amely tartalmazza az eredeti állomásfejlécet, amelyhez a kérés érkezett. Ezt a fejlécet hasznos olyan szituációkra, mint az Azure-webhely-integráció, ahol a bejövő állomásfejléc módosította-e előtt az adatforgalmat, a háttérkiszolgáló.

#### <a name="rewrite-http-headers"></a>HTTP-fejlécek átírása

A kérés útválasztási szabályok hozzáadása, eltávolítása vagy módosítása a HTTP (S)-kérelem használatával, és a válaszfejlécet a kérelem során, és a válasz-csomagok áthelyezése az ügyfél és a háttérkiszolgáló-készletek, az application gateway-n keresztül. A fejlécek csak nem állítható be statikus értékre, hanem és egyéb fontos kiszolgálói változókat. Ez segít elvégezni néhány fontos alkalmazási, például az ügyfelek, eltávolítja a háttérrendszer bizalmas információ hozzáadása a további biztonsági intézkedéseket, és így tovább IP-címének beolvasása. További információkért lásd: [Újraírási HTTP-fejlécek az application gateway-en](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).

## <a name="http-settings"></a>HTTP-beállítások

Az application gateway irányítja a forgalmat a háttérkiszolgálókhoz, a portszám, protokoll és egyéb ehhez az erőforráshoz megadott beállításokat.

A háttérkészletek a következő portokat és protokollokat támogatja:

### <a name="ports"></a>Portok

Ez az a port, amelyet a háttérkiszolgálók figyelik a az Application Gateway érkező forgalomra. Konfigurálhatja a beállításnak 1 és 65535 közötti port.

### <a name="protocols"></a>Protokollok

Az Application gateway támogatja a HTTP és HTTPS protokollok útválasztási kérelmek a háttérkiszolgálókhoz.

Ha a HTTP protokollt választja, majd forgalom folyamatok nem titkosított a háttérkiszolgálókhoz.

Ezekben az esetekben, ahol a háttérkiszolgálók felé irányuló titkosítatlan kommunikáció lehetőség nem elfogadható akkor a HTTPS protokollt kell választania. Ezt a beállítást, a figyelő a HTTPS protokoll kiválasztásával együtt lehetővé teszi, hogy engedélyezze [teljes körű SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview). Lehetővé teszi a küldheti a bizalmas adatokat, a háttérkiszolgáló titkosítva. A biztonságos kommunikációhoz tanúsítványt kell konfigurálni a teljes körű SSL-lel engedélyezett háttérkészlet minden egyes háttérkiszolgálójához.

Több HTTP-beállítási lehetőségeket is használhat.

### <a name="cookie-based-session-affinity"></a>Cookie-alapú munkamenet-affinitás

Ez a funkció akkor hasznos, ha meg szeretné tartani a felhasználói munkamenet ugyanazon a kiszolgálón. Az átjáróval kezelt cookie-k használatával az Application Gateway képes egy felhasználói munkamenet minden újabb forgalmát ugyanarra a kiszolgálóra irányítani feldolgozásra. Ez a funkció olyan esetekben lehet fontos, amelyekben egy felhasználói munkamenethez tartozó munkamenet-állapotot helyileg ment a rendszer a kiszolgálón.

Ha az alkalmazás nem tudja kezelni a cookie-alapú affinitás, majd nem tudja használni ezt a funkciót

### <a name="connection-draining"></a>Kapcsolatkiürítés

A kapcsolatkiürítéssel zökkenőmentesen végrehajtható a háttérkészlettagok eltávolítása a tervezett szolgáltatásfrissítések során. Ez a beállítás háttérkészlet összes tagjára alkalmazhatók szabály létrehozása során. Ha engedélyezve van, az application gateway biztosítja, hogy, hogy háttérkészlet megszüntetéséhez regisztrálása példányai nem kap új kérések miközben lehetővé teszi a meglévő kérések végrehajtásához beállított időkorláton belül. Ez egyaránt vonatkozik azokra a háttérpéldányokra, amelyek API-hívással, explicit módon lettek eltávolítva a háttérkészletből, és azokra is, amelyeket az állapot-mintavételek „Nem kifogástalan” állapotúnak minősítenek.

### <a name="override-backend-path"></a>Háttéralkalmazás elérési útjának felülbírálása

Ez a funkció lehetővé teszi, hogy az elérési utat az URL-cím felülbírálása a úgy, hogy a megadott elérési útra vonatkozó kérelmek átirányíthatók egy másik elérési utat a. Például ha kíván irányíthatja a kérelmeket contoso.com/images alapértelmezett, majd adja meg a '/' ebbe a szövegmezőbe, és ezt követően csatlakoztassa a szabályhoz társított contoso.com/images a HTTP-beállítás.

### <a name="pick-host-name-from-a-backend-address"></a>Válasszon ki egy címet állomás nevét

Ez a funkció beállítja a *gazdagép* egy IP-cím vagy teljes tartománynév - FQDN háttérkészlethez állomás nevét a kérelemben szereplő tartományfejléc. Ez hasznos az esetekben, ahol a tartománynév, a háttérrendszer eltér az alkalmazás, például egy forgatókönyvet, ahol a háttérrendszer szolgál az Azure App Service DNS-nevét. Mivel az Azure App Service egy több-bérlős környezet használatával egy megosztott terület egyetlen IP-címmel, egy App Service-ben a gazdagép neve, az egyéni tartomány beállításai csak a érhető el. Alapértelmezés szerint a *example.azurewebsites.net*. Ha szeretne hozzáférni az App Service használatával az Alkalmazásátjáró egy állomásnevet, az App Service-ben nem regisztrált vagy az Application Gateway FQDN, akkor bírálja felül az eredeti kérést, az App Service-gazdagépnév az állomásnevet.

### <a name="host-override"></a>**Gazdagép felülbírálása**

Ez a lehetőség váltja fel a *gazdagép* a bejövő kérelem, az application gateway az itt megadott állomás nevét a fejlécet.

Miután létrehozott egy HTTP-beállítás, kell társítani egy vagy több kérés útválasztási szabályokat.

## <a name="backend-pool"></a>Háttérkészlet

A háttérkészlet segítségével irányíthatja a kérelmeket a háttérkiszolgálók, amelyeket teljesíteni a kérést. Háttérkészletek összeállítható a hálózati adapterek, a virtuális gép méretezése a készletek, nyilvános IP címeket, belső IP címeket, teljes tartománynév és több-bérlős háttérrendszereket, például az Azure App Service-ben. Application gateway háttérkészlet érintett tagjai nem egy rendelkezésre állási csoporthoz vannak társítva. Háttérkészletek tagjai lehetnek, fürtök és adatközpontok között, vagy Azure-on kívül mindaddig, amíg az IP-kapcsolattal rendelkeznek. A kérelmek különböző típusú különböző háttérkészletek hozhat létre. Hozzon létre például egy háttérkészlet általános kérelmek és a kéréseket, a mikroszolgáltatások az alkalmazás más háttérkészlet.

Miután létrehozta a háttérkészlet, társíthatja azt egy vagy több kérés útválasztási szabályokat szeretne. Szükség minden egyes háttérkészlethez állapotfigyelő mintavételezőket konfigurálása az application gateway-en. Amikor egy kérés útválasztási szabály a feltétel teljesül, az application gateway továbbítja a forgalmat a kifogástalan állapotú kiszolgálók a megfelelő háttérkészlet (határoz meg az állapotmintákat).

## <a name="health-probes"></a>Állapotminták

Az Application gateway alapértelmezés szerint a háttérkészlet összes erőforrásának állapotát figyeli, és automatikusan eltávolítja az összes erőforrást a készletből sérültnek. Az Application gateway továbbra is figyeli a nem megfelelő állapotú példányokat, és hozzáadja őket a megfelelően működő háttér-készlet számára, elérhetővé válnak, és a állapotadat-mintavételek válaszolni. Az Application gateway küld az állapotminták ugyanazt a portot, amely a HTTP-háttérbeállítások van definiálva.

Alapértelmezett mintavételi állapotfigyelés használatán is testreszabhatja az állapotminta az alkalmazás követelményeinek megfelelően. Az egyéni minták lehetővé teszik egy részletesebb felügyelheti a Szolgáltatásállapot-figyelést. Egyéni mintavétel használata esetén konfigurálhatja úgy a mintavételi időköz, az URL-cím és elérési útja, teszteléséhez és hány sikertelen válaszokat elfogadja a háttérkészlet-példány állapotúként van megjelölve. Azt javasoljuk, hogy konfigurálja-e az egyéni minták minden háttérkészlet állapotának monitorozásához. További információkért lásd: [figyelhető az Application gateway állapota](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview).

## <a name="next-steps"></a>További lépések

Miután megismerkedett az Application Gateway-összetevők, a következőket teheti:
* [Application Gateway létrehozása az Azure Portalon](quick-create-portal.md)
* [Hozzon létre egy Application Gatewayen a PowerShell használatával](quick-create-powershell.md)
* [Hozzon létre egy Application Gateway az Azure CLI-vel](quick-create-cli.md).
<properties
   pageTitle="Az Application Gateway bemutatása | Microsoft Azure"
   description="Ez az oldal a 7. réteg terheléselosztását segítő Application Gateway szolgáltatás áttekintését tartalmazza, beleértve az átjárók méretét, a HTTP-terheléselosztást, a cookie-alapú munkamenet-affinitást és az SSL-alapú kiszervezést."
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/26/2016"
   ms.author="gwallace"/>


# <a name="application-gateway-overview"></a>Az Application Gateway áttekintése

## <a name="what-is-application-gateway"></a>Mi az Application Gateway?

A Microsoft Azure Application Gateway egy alkalmazáskézbesítési vezérlőszolgáltatást (ADC) biztosít, amely számos képességgel rendelkezik a 7. rétegbeli terheléselosztáshoz. Ez azt jelenti, hogy fogadja a bejövő forgalmat, és a benne foglalt szabályok alapján a megfelelő háttérkiszolgáló-példányokhoz irányítja azt.

Az alkalmazások terheléselosztásával a rendszergazdák és a fejlesztők útválasztási szabályokat hozhatnak létre a hálózat forgalmához a HTTP protokoll alapján.  Az Application Gateway magas rendelkezésre állású és részletesen mérhető szolgáltatás. A szolgáltatási szerződést és az árakat az [SLA](https://azure.microsoft.com/support/legal/sla/) és az [Díjszabás](https://azure.microsoft.com/pricing/details/application-gateway/) oldalon találja.

Az Application Gateway alkalmazza az útválasztási szabályokat a HTTP-forgalomra, biztosítva ezáltal a 7. réteg (HTTP) terheléselosztását. Amikor alkalmazásátjárót hoz létre, a rendszer egy végpontot (VIP) rendel hozzá, amely nyilvános IP-címként szolgál a bejövő hálózati forgalomnak. Az Azure az Azure Load Balancer által biztosítja a 4. réteg terheléselosztását az átviteli szinten (TCP/UDP), és a teljes bejövő hálózati forgalom terheléselosztását az Application Gateway szolgáltatás végzi el. Az Application Gateway az alapján irányítja a HTTP-forgalmat, hogy az átjáró virtuális gépként, felhőszolgáltatásként vagy külső IP-címként lett-e megadva.

## <a name="features"></a>Szolgáltatások

Az Application Gateway az alábbi funkciókkal támogatja a 7. rétegbeli alkalmazásszolgáltatást:

- **[Webalkalmazási tűzfal (előzetes verzió)](application-gateway-webapplicationfirewall-overview.md)** – Az Application Gateway webalkalmazási tűzfala (WAF) megvédi a webalkalmazásokat az ismert webalapú támadásoktól, például az SQL-injektálástól; a helyközi, szkriptet alkalmazó támadásoktól és a munkamenet-eltérítésektől.
- **HTTP-terheléselosztás** – Az Application Gateway ciklikus időszeleteléses terheléselosztást biztosít. A terheléselosztás a 7. rétegben történik, és kizárólag a HTTP(S)-forgalmat érinti.
- **Cookie-alapú munkamenet-affinitás** – Ez a funkció akkor hasznos, ha egy felhasználói munkamenetet egy adott háttérkiszolgálón szeretne tartani. Az átjáróval kezelt cookie-k használatával az Application Gateway képes egy felhasználói munkamenet minden újabb forgalmát ugyanarra a háttérkiszolgálóra irányítani feldolgozásra. Ez a funkció olyan esetekben lehet fontos, amelyekben egy felhasználói munkamenethez tartozó munkamenet-állapotot helyileg ment a rendszer a háttérkiszolgálón.
- **[A biztonságos szoftvercsatorna (SSL) kiszervezése](application-gateway-ssl-arm.md)** – Ez a funkció leveszi a HTTPS-forgalom visszafejtése költséges feladatának terhét az Ön webkiszolgálóiról. Ha lezárja az SSL-kapcsolatot az Application Gatewayben, és a kérést titkosítás nélkül irányítja át a kiszolgálóra, a webkiszolgálónak nem kell feloldania a titkosítást.  Az Application Gateway újratitkosítja a választ, mielőtt visszaküldené az ügyfélnek. Ez a funkció akkor hasznos, ha a háttérkiszolgáló ugyanazon a biztonságos virtuális hálózaton van, mint az Azure Application Gateway.
- **[Végpontok közötti SSL](application-gateway-backend-ssl.md)** – Az Application Gateway támogatja a forgalom végpontok közötti titkosítását. Az Application Gateway ehhez lezárja az SSL-kapcsolatot az alkalmazásátjáróban. Az átjáró ezután alkalmazza az útválasztási szabályokat a forgalomra, újratitkosítja a csomagot, majd a megadott útválasztási szabályok alapján továbbítja azt a megfelelő háttérkiszolgálóra. A webkiszolgáló esetleges válasza ugyanilyen módon jut el a végfelhasználóhoz.
- **[URL-alapú tartalom-útválasztás](application-gateway-url-route-overview.md)** – Ez a funkció lehetővé teszi, hogy különböző jellegű forgalmakat adott háttérkiszolgálókhoz irányítson. Egy webkiszolgálón lévő mappához vagy egy CDN-hez kapcsolódó forgalom például egy másik háttérkiszolgálóhoz irányítható, csökkentve azoknak a háttérkiszolgálóknak a terhelését, amelyek nem speciális tartalmat szolgálnak ki.
- **[Többhelyes útválasztás](application-gateway-multi-site-overview.md)** - Az Application Gateway szolgáltatással akár 20 webhelyet is összevonhat egyetlen alkalmazásátjáróba.
- **[WebSocket-támogatás](application-gateway-websocket.md)** – Az Application Gateway egy másik nagyszerű szolgáltatása a natív WebSocket-támogatás.
- **[Állapotfigyelés](application-gateway-probe-overview.md)** – Az Application Gateway alapértelmezés szerint végzi a háttérerőforrások állapotfigyelését, és egyéni mintavételek létrehozását teszi lehetővé a különlegesebb esetek megfigyelésére.

## <a name="benefits"></a>Előnyök

Az Application Gateway az alábbi esetekben hasznos:

- Olyan alkalmazásokhoz, amelyekben egy adott ügyfél- vagy felhasználói munkamenettől érkező kérésnek mindig ugyanarra a virtuális háttérgépre kell eljutnia. Ilyenek a bevásárlókocsi-alkalmazások vagy a webes levelezőkiszolgálók.
- Olyan alkalmazásokhoz, amelyek célja, hogy mentesítsék a webkiszolgálófarmokat az SSL-lezárással járó többletterhelés alól.
- Olyan alkalmazásokhoz, például a tartalomkézbesítési hálózatokhoz, amelyeken egy hosszú ideig futó TCP-kapcsolat HTTP-kéréseit különböző háttérkiszolgálókra kell irányítani, terheléselosztását azokon elvégezni.
- Olyan alkalmazásokhoz, amelyek támogatják a WebSocket-forgalmat.
- A webalkalmazások ismert webalapú támadásoktól, például az SQL-injektálástól, a helyközi, szkriptet alkalmazó támadásoktól és a munkamenet-eltérítésektől való megvédéséhez.

[AZURE.INCLUDE [load-balancer-compare-tm-ag-lb-include.md](../../includes/load-balancer-compare-tm-ag-lb-include.md)]

## <a name="gateway-sizes-and-instances"></a>Átjáróméretek és -példányok

Az Application Gateway jelenleg három méretben érhető el: Kicsi, Közepes és Nagy. A Kicsi méret ideális fejlesztési és tesztelési célokra.

Az Application Gatewaynek jelenleg két termékváltozata van: WAF és Standard.

Előfizetésenként 50 alkalmazásátjárót hozhat létre, egyenként 10 példánnyal. Egy alkalmazásátjáró 20 HTTP-figyelőből állhat. Az Azure által felügyelt Application Gateway terheléselosztási szolgáltatás egy 7. rétegbeli, az Azure szoftveres terheléselosztója mögött működő terheléselosztó üzembe helyezését teszi lehetővé.

Az alábbi táblázatban az egyes alkalmazásátjárókhoz tartozó átlagos átviteli sebességek szerepelnek:

| A háttérkiszolgáló lapválasza | Kicsi | Közepes | Nagy|
|---|---|---|---|
| 6K | 7,5 Mbps | 13 Mbps | 50 Mbps |
|100K | 35 Mbps | 100 Mbps| 200 Mbps |

>[AZURE.NOTE] Ezek az értékek az alkalmazásátjáró hozzávetőleges átviteli sebességét jelzik. A tényleges átvitel számos környezeti tényezőtől függ, például az átlagos lapmérettől, a háttérpéldányok helyétől és a lapkiszolgálás feldolgozási időtartamától. A teljesítményre vonatkozó pontos adatokat úgy kaphatja meg, ha saját teszteket futtat le, hiszen az itt szereplő adatok csak a kapacitástervezést segítik.

## <a name="health-monitoring"></a>Állapotfigyelés

Az Azure Application Gateway automatikusan végzi a háttérpéldányok állapotfigyelését egyszerű vagy egyéni állapotmintákkal. Ha állapotmintákat használ, biztosíthatja, hogy csak kifogástalan állapotú gazdagépek válaszoljanak a forgalomra. További információt az [Application Gateway-állapotfigyelés – áttekintés](application-gateway-probe-overview.md) című témakörben talál.

## <a name="configuring-and-managing"></a>Beállítás és felügyelet

Az alkalmazásátjáró végpontjához nyilvános és privát IP-címet is hozzárendelhet, vagy akár mindkettőt, ha a beállítások engedik. Az Application Gateway beállítása egy virtuális hálózaton történik, egy saját alhálózaton. Az alkalmazásátjáró számára létrehozott vagy használt alhálózaton nem lehet más típusú erőforrás, kizárólag más alkalmazásátjárók. A háttérerőforrások védelmét úgy biztosíthatja, hogy a háttérkiszolgálókat a virtuális hálózat egy olyan alhálózatán üzemelteti, amely különbözik az alkalmazásátjáró alhálózatától. Ilyen további alhálózat a háttéralkalmazásokhoz nem szükséges – amíg az alkalmazásátjáró el tudja érni az IP-címet, addig biztosítja az ADC-képességeket a háttérkiszolgálóknak.

Alkalmazásátjárókat REST API-k, PowerShell-parancsmagok, az Azure parancssori felülete vagy az [Azure Portal](https://portal.azure.com/) segítségével hozhat lére.

## <a name="next-steps"></a>Következő lépések

Ha elsajátította a szükséges ismereteket, [hozzon létre egy alkalmazásátjárót](application-gateway-create-gateway-portal.md), vagy [hozzon létre egy SSL-kiszervező alkalmazásátjárót](application-gateway-ssl-arm.md) a HTTPS-kapcsolatok teherelosztása érdekében.

Ha szeretné megtudni, hogyan hozhat létre egy URL-alapú tartalom-útválasztást használó alkalmazásátjárót, bővebb tájékoztatást a [Create an application gateway using URL-based routing](application-gateway-create-url-route-arm-ps.md) (URL-alapú tartalom-útválasztást használó alkalmazásátjáró létrehozása) című témakörben talál.




<!--HONumber=Oct16_HO3-->



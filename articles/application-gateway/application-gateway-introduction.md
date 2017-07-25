---
title: "Bevezetés az Azure Application Gateway használatába | Microsoft Docs"
description: "Ez az oldal a 7. réteg terheléselosztását segítő Application Gateway szolgáltatás áttekintését tartalmazza, beleértve az átjárók méretét, a HTTP-terheléselosztást, a cookie-alapú munkamenet-affinitást és az SSL-alapú kiszervezést."
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: b37a2473-4f0e-496b-95e7-c0594e96f83e
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 07/10/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: c3ea7cfba9fbf1064e2bd58344a7a00dc81eb148
ms.openlocfilehash: 17fc7e6a19b835e08360d1bf638d69aabb14e121
ms.contentlocale: hu-hu
ms.lasthandoff: 07/19/2017

---
# <a name="overview-of-application-gateway"></a>Application Gateway – áttekintés

A Microsoft Azure Application Gateway egy alkalmazáskézbesítési vezérlőszolgáltatást (ADC) biztosító dedikált virtuális berendezés, amely számos 7. rétegbeli terheléselosztási lehetőséget nyújt alkalmazásának. Az ügyfelek optimalizálhatják a webfarmok termelékenységét a processzorigényes SSL-lezárások Application Gateway felé történő kiszervezésével. Ezen túlmenően egyéb 7. rétegbeli útválasztási lehetőségeket is kínál, beleértve a bejövő forgalom ciklikus időszeleteléses elosztását, a cookie-alapú munkamenet-affinitást, az URL-alapú útválasztást, valamint egyetlen Application Gateway mögött több webhelyet is üzemeltethet. Az Application Gateway WAF termékváltozata tartalmaz egy webapp tűzfalat is, amely védelmet biztosít a webappok számára a gyakori internetes biztonsági rések és az azokat kihasználó támadások ellen. Az Application Gateway szolgáltatást internetes átjáróként, csak belső használatú átjáróként vagy a kettő kombinációjaként lehet konfigurálni. 

![forgatókönyv](./media/application-gateway-introduction/scenario.png)

## <a name="features"></a>Szolgáltatások

Az Application Gateway jelenleg a következő képességeket biztosítja:


* **[Webalkalmazási tűzfal](application-gateway-webapplicationfirewall-overview.md)** – Az Application Gateway webalkalmazási tűzfala (WAF) megvédi a webalkalmazásokat az ismert webalapú támadásoktól, például az SQL-injektálástól; a helyközi, szkriptet alkalmazó támadásoktól és a munkamenet-eltérítésektől.
* **HTTP-terheléselosztás** – Az Application Gateway ciklikus időszeleteléses terheléselosztást biztosít. A terheléselosztás a 7. rétegben történik, és kizárólag a HTTP(S)-forgalmat érinti.
* **Cookie-alapú munkamenet-affinitás** – Ez a funkció akkor hasznos, ha egy felhasználói munkamenetet egy adott háttérkiszolgálón szeretne tartani. Az átjáróval kezelt cookie-k használatával az Application Gateway képes egy felhasználói munkamenet minden újabb forgalmát ugyanarra a háttérkiszolgálóra irányítani feldolgozásra. Ez a funkció olyan esetekben lehet fontos, amelyekben egy felhasználói munkamenethez tartozó munkamenet-állapotot helyileg ment a rendszer a háttérkiszolgálón.
* **[A biztonságos szoftvercsatorna (SSL) kiszervezése](application-gateway-ssl-arm.md)** – Ez a funkció leveszi a HTTPS-forgalom visszafejtése költséges feladatának terhét az Ön webkiszolgálóiról. Ha lezárja az SSL-kapcsolatot az Application Gatewayben, és a kérést titkosítás nélkül irányítja át a kiszolgálóra, a webkiszolgálónak nem kell feloldania a titkosítást.  Az Application Gateway újratitkosítja a választ, mielőtt visszaküldené az ügyfélnek. Ez a funkció akkor hasznos, ha a háttérkiszolgáló ugyanazon a biztonságos virtuális hálózaton van, mint az Azure Application Gateway.
* **[Végpontok közötti SSL](application-gateway-backend-ssl.md)** – Az Application Gateway támogatja a forgalom végpontok közötti titkosítását. Az Application Gateway ehhez lezárja az SSL-kapcsolatot az alkalmazásátjáróban. Az átjáró ezután alkalmazza az útválasztási szabályokat a forgalomra, újratitkosítja a csomagot, majd a megadott útválasztási szabályok alapján továbbítja azt a megfelelő háttérkiszolgálóra. A webkiszolgáló esetleges válasza ugyanilyen módon jut el a végfelhasználóhoz.
* **[URL-alapú tartalom-útválasztás](application-gateway-url-route-overview.md)** – Ez a funkció lehetővé teszi, hogy különböző jellegű forgalmakat adott háttérkiszolgálókhoz irányítson. Egy webkiszolgálón lévő mappához vagy egy CDN-hez kapcsolódó forgalom például egy másik háttérkiszolgálóhoz irányítható, csökkentve azoknak a háttérkiszolgálóknak a terhelését, amelyek nem adott tartalmat szolgálnak ki.
* **[Többhelyes útválasztás](application-gateway-multi-site-overview.md)** - Az Application Gateway szolgáltatással akár 20 webhelyet is összevonhat egyetlen alkalmazásátjáróba.
* **[WebSocket-támogatás](application-gateway-websocket.md)** – Az Application Gateway egy másik nagyszerű szolgáltatása a natív WebSocket-támogatás.
* **[Állapotfigyelés](application-gateway-probe-overview.md)** – Az Application Gateway alapértelmezés szerint végzi a háttérerőforrások állapotfigyelését, és egyéni mintavételek létrehozását teszi lehetővé a különlegesebb esetek megfigyelésére.
* **[Átirányítás kérése](application-gateway-redirect-overview.md)** – Ez a szolgáltatás lehetővé teszi, hogy a HTTP-kéréseket egy HTTPS-figyelőre irányítsa át.
* **[Speciális diagnosztika](application-gateway-diagnostics.md)** – Az Application Gateway teljes diagnosztikát és hozzáférési naplókat biztosít. A tűzfalnaplók olyan Application Gateway-erőforrásokhoz érhetők el, amelyekhez engedélyezve van a WAF.

## <a name="benefits"></a>Előnyök

Az Application Gateway az alábbi esetekben hasznos:

* Olyan alkalmazásokhoz, amelyekben egy adott ügyfél- vagy felhasználói munkamenettől érkező kérésnek mindig ugyanarra a virtuális háttérgépre kell eljutnia. Ilyenek például a bevásárlókocsi-alkalmazások vagy a webes levelezőkiszolgálók.
* Webkiszolgálófarmok mentesítése az SSL-lezárással járó többletterhelés alól.
* Olyan alkalmazásokhoz, például a tartalomkézbesítési hálózatokhoz, amelyeken egy hosszú ideig futó TCP-kapcsolat HTTP-kéréseit különböző háttérkiszolgálókra kell irányítani, terheléselosztását azokon elvégezni.
* Olyan alkalmazásokhoz, amelyek támogatják a WebSocket-forgalmat.
* A webalkalmazások ismert webalapú támadásoktól, például az SQL-injektálástól, a helyközi, szkriptet alkalmazó támadásoktól és a munkamenet-eltérítésektől való megvédéséhez.
* A forgalom logikai elosztása a különböző útválasztási kritériumok alapján, mint például az URL-elérési út vagy a tartományfejlécek.

Az Application Gateway egy teljes körűen felügyelt Azure-szolgáltatás, amely skálázható és magas rendelkezésre állást kínál. Diagnosztikai és naplózási képességek széles skáláját biztosítja a jobb kezelhetőség érdekében. Amikor alkalmazásátjárót hoz létre, a rendszer egy végpontot (nyilvános virtuális IP-cím vagy belső ILB IP) rendel hozzá, amely a bejövő hálózati forgalom kezelésére szolgál. A virtuális IP-cím vagy ILB IP biztosításáért az Azure Load Balancer felel az átviteli szinten (TCP/UDP), és a teljes bejövő hálózati forgalom terheléselosztását az Application Gateway feldolgozópéldányai végzik el. Az Application Gateway az alapján irányítja a HTTP/HTTPS-forgalmat, hogy az átjáró virtuális gépként, felhőszolgáltatásként, illetve belső vagy külső IP-címként lett-e megadva.

Az Azure által felügyelt Application Gateway terheléselosztási szolgáltatás egy 7. rétegbeli, az Azure szoftveres terheléselosztója mögött működő terheléselosztó üzembe helyezését teszi lehetővé. A Traffic Managerrel a következő képen látható módon valósítható meg a forgatókönyv, ahol a Traffic Manager átirányítást és különböző régiókban lévő több Application Gatewayre irányuló forgalom elérhetőségét biztosít, míg az Application Gateway régiók közötti, 7. rétegbeli terheléselosztást nyújt. A forgatókönyv példája a következő témakörben található: [Terheléselosztási szolgáltatások használata az Azure-felhőben](../traffic-manager/traffic-manager-load-balancing-azure.md)

![traffic manager- és application gateway-forgatókönyv](./media/application-gateway-introduction/tm-lb-ag-scenario.png)

[!INCLUDE [load-balancer-compare-tm-ag-lb-include.md](../../includes/load-balancer-compare-tm-ag-lb-include.md)]

## <a name="gateway-sizes-and-instances"></a>Átjáróméretek és -példányok

Az Application Gateway jelenleg három méretben érhető el: **Kicsi**, **Közepes** és **Nagy**. A Kicsi méret ideális fejlesztési és tesztelési célokra.

Előfizetésenként 50 alkalmazásátjárót hozhat létre, egyenként 10 példánnyal. Egy alkalmazásátjáró 20 HTTP-figyelőből állhat. Az Application Gateway korlátainak teljes listáját lásd: [Az Application Gateway szolgáltatási korlátozásai](../azure-subscription-service-limits.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#application-gateway-limits).

Az alábbi táblázatban az egyes SSL-alapú kiszervezéshez engedélyezett alkalmazásátjárókhoz tartozó átlagos átviteli sebességek szerepelnek:

| A háttérkiszolgáló lapválasza | Kicsi | Közepes | Nagy |
| --- | --- | --- | --- |
| 6K |7,5 Mbps |13 Mbps |50 Mbps |
| 100K |35 Mbps |100 Mbps |200 Mbps |

> [!NOTE]
> Ezek az értékek az alkalmazásátjáró hozzávetőleges átviteli sebességét jelzik. A tényleges átvitel számos környezeti tényezőtől függ, például az átlagos lapmérettől, a háttérpéldányok helyétől és a lapkiszolgálás feldolgozási időtartamától. A pontos teljesítményszámokhoz saját teszteket kell futtatnia. Ezek az értékek csupán útmutatóul szolgálnak a kapacitástervezéshez.

## <a name="health-monitoring"></a>Állapotfigyelés

Az Azure Application Gateway automatikusan végzi a háttérpéldányok állapotfigyelését egyszerű vagy egyéni állapotmintákkal. Ha állapotmintákat használ, biztosíthatja, hogy csak kifogástalan állapotú gazdagépek válaszoljanak a forgalomra. További információt az [Application Gateway-állapotfigyelés – áttekintés](application-gateway-probe-overview.md) című témakörben talál.

## <a name="configuring-and-managing"></a>Beállítás és felügyelet

Az alkalmazásátjáró végpontjához nyilvános és privát IP-címet is hozzárendelhet, vagy akár mindkettőt, ha a beállítások engedik. Az Application Gateway beállítása egy virtuális hálózaton történik, egy saját alhálózaton. Az alkalmazásátjáró számára létrehozott vagy használt alhálózaton nem lehet más típusú erőforrás, kizárólag más alkalmazásátjárók. A háttérerőforrások védelmét úgy biztosíthatja, hogy a háttérkiszolgálókat a virtuális hálózat egy olyan alhálózatán üzemelteti, amely különbözik az alkalmazásátjáró alhálózatától. Ilyen további alhálózat a háttéralkalmazásokhoz nem szükséges – amíg az alkalmazásátjáró el tudja érni az IP-címet, addig biztosítja az ADC-képességeket a háttérkiszolgálóknak. 

Alkalmazásátjárókat REST API-k, PowerShell-parancsmagok, az Azure parancssori felülete vagy az [Azure Portal](https://portal.azure.com/) segítségével hozhat lére. Az Application Gatewayhez kapcsolódó további gyakori kérdések listáját itt találja meg: [Application Gateway – gyakori kérdések](application-gateway-faq.md).

## <a name="pricing"></a>Díjszabás

A díjszabás az átjárópéldányok óránkénti díján és az adatfeldolgozási díjon alapul. A WAF termékváltozathoz tartozó óránkénti átjáródíj eltér a normál termékváltozat díjaitól, és az [Application Gateway díjszabását](https://azure.microsoft.com/pricing/details/application-gateway/) ismertető webhelyen tekinthető meg. Az adatfeldolgozási díjak nem változnak.

## <a name="faq"></a>GYIK

Az Application Gatewayre vonatkozó gyakori kérdésekért lásd: [Application Gateway – gyakori kérdések](application-gateway-faq.md).

## <a name="next-steps"></a>Következő lépések

Ha elsajátította a szükséges ismereteket, [hozzon létre egy alkalmazásátjárót](application-gateway-create-gateway-portal.md), vagy [hozzon létre egy SSL-kiszervező alkalmazásátjárót](application-gateway-ssl-arm.md) a HTTPS-kapcsolatok teherelosztása érdekében.

Ha szeretné megtudni, hogyan hozhat létre egy URL-alapú tartalom-útválasztást használó alkalmazásátjárót, bővebb tájékoztatást a [Create an application gateway using URL-based routing](application-gateway-create-url-route-arm-ps.md) (URL-alapú tartalom-útválasztást használó alkalmazásátjáró létrehozása) című témakörben talál.


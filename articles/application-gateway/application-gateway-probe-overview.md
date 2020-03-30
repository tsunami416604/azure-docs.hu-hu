---
title: Az Azure Application Gateway állapotfigyelésének áttekintése
description: Az Azure Application Gateway figyeli a háttérkészletében lévő összes erőforrás állapotát, és automatikusan eltávolítja a készletből a nem megfelelőnek ítélt erőforrásokat.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2020
ms.author: victorh
ms.openlocfilehash: c5a53167c6a4ca6c886b858a1608eaa173185bd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335856"
---
# <a name="application-gateway-health-monitoring-overview"></a>Alkalmazásátjáró állapotfigyelésének áttekintése

Az Azure Application Gateway alapértelmezés szerint figyeli a háttérkészletében lévő összes erőforrás állapotát, és automatikusan eltávolítja a készletből a nem megfelelőnek ítélt erőforrásokat. Az Application Gateway továbbra is figyeli a nem megfelelő állapotú példányokat, és ha elérhetővé válnak, és megfelelő választ adnak az állapotfelmérésre, akkor visszahelyezi őket a megfelelő állapotú háttérrendszeri készletbe. Az alkalmazásátjáró a háttérhttp-beállításokban definiált porttal küldi el az állapotmintákat. Ez a konfiguráció biztosítja, hogy a mintavétel ugyanazt a portot teszteli, amelyet az ügyfelek a háttérrendszerhez való csatlakozáshoz használnak. 

Az Application Gateway által az állapotmintakhoz használt forrás IP-cím a háttérkészlettől függ:
 
- Ha a háttérkészlet nyilvános végpont, akkor a forráscím az alkalmazás átjáró előtér nyilvános IP-címe.
- Ha a háttérkészlet egy privát végpont, majd a forrás IP-cím az alkalmazás átjáró alhálózat ának privát IP-címterületéről származik.


![példa alkalmazásátjáró-mintavételre][1]

Az alapértelmezett állapotvizsgáló figyelése mellett testre is szabhatja az állapotminta az alkalmazás igényeinek megfelelően. Ebben a cikkben az alapértelmezett és az egyéni állapotminta is szerepel.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-health-probe"></a>Alapértelmezett állapotminta

Az alkalmazásátjáró automatikusan konfigurálja az alapértelmezett állapotminta, ha nem állít be egyéni mintavételi konfigurációt. A figyelési viselkedés úgy működik, hogy HTTP-kérelmet küld a háttérkészlethez konfigurált IP-címekre. Az alapértelmezett mintavételek, ha a háttérrendszer http-beállítások HTTPS konfigurálva vannak, a mintavétel https protokollt is használ a háttérrendszerek állapotának teszteléséhez.

Például: Az alkalmazásátjárót úgy konfigurálja, hogy az A, B és C háttérkiszolgálókat használjon a HTTP hálózati forgalom fogadására a 80-as porton. Az alapértelmezett állapotfigyelés 30 másodpercenként teszteli a három kiszolgálót a megfelelő HTTP-válasz érdekében. A kifogástalan HTTP-válasz [állapotkódja](https://msdn.microsoft.com/library/aa287675.aspx) 200 és 399 között van.

Ha az A kiszolgáló alapértelmezett mintavételes ellenőrzése sikertelen, az alkalmazásátjáró eltávolítja azt a háttérkészletéből, és a hálózati forgalom nem áramlik a kiszolgálóra. Az alapértelmezett mintavétel továbbra is 30 másodpercenként ellenőrzi az A kiszolgálót. Amikor az A kiszolgáló sikeresen válaszol egy alapértelmezett állapotminta egyik kérésére, a rendszer kifogástalan állapotúként adja vissza a háttérkészlethez, és a forgalom ismét a kiszolgálóra áramlik.

### <a name="probe-matching"></a>A szonda egyeztetése

Alapértelmezés szerint a 200 és 399 közötti állapotkóddal rendelkező HTTP(S) válasz kifogástalannak minősül. Egyéni állapotminta további támogatja a két megfeleltetési feltételt. Az egyeztetési feltételek segítségével tetszés szerint módosíthatja a kifogástalan választ eredményező adatok alapértelmezett értelmezését.

A következő feltételek nek felelnek meg: 

- **HTTP-válasz állapotkód-egyezése** – A felhasználó által megadott http-válaszkód vagy válaszkódtartományok elfogadására vonatkozó mintavételezési feltétel. Az egyes vesszővel tagolt válaszállapot-kódok vagy az állapotkódok tartománya támogatott.
- **HTTP-választörzs egyezése** – A MINTAVÉTEL-egyeztetési feltétel, amely megvizsgálja a HTTP-választörzset, és megegyezik a felhasználó által megadott karakterlánccal. Az egyezés csak a felhasználó által megadott karakterlánc jelenlétét keresi a választörzsben, és nem egy teljes reguláris kifejezésegyezés.

Az egyezési feltételek `New-AzApplicationGatewayProbeHealthResponseMatch` a parancsmag használatával adhatók meg.

Példa:

```azurepowershell
$match = New-AzApplicationGatewayProbeHealthResponseMatch -StatusCode 200-399
$match = New-AzApplicationGatewayProbeHealthResponseMatch -Body "Healthy"
```
Miután a megfeleltetési feltételek meg van `-Match` adva, a PowerShell egy paraméterhasználatával csatlakoztatható a mintavételi konfigurációhoz.

### <a name="default-health-probe-settings"></a>Alapértelmezett állapotminta-beállítások

| Szonda tulajdonság | Érték | Leírás |
| --- | --- | --- |
| Mintavétel URL-címe |http://127.0.0.1:\<port\>/ |URL elérési útja |
| Intervallum |30 |A következő állapotminta elküldése előtt másodpercben meg kell várnia az időt.|
| Időtúllépés |30 |Az alkalmazásátjáró másodpercben megvárja a mintavételi választ, mielőtt a mintavételt nem kifogástalanállapotúnak jelölné. Ha egy mintavétel kifogástalan állapotúként ad vissza, a megfelelő háttérterület azonnal kifogástalanként lesz megjelölve.|
| Nem kifogástalan állapot küszöbértéke |3 |Szabályozza, hogy hány szondát kell küldeni, ha a rendszeres állapotvizsgálat meghibásodik. Ezek a további állapot-mintavételek gyors egymásutánban küldve a háttérrendszer állapotának gyors meghatározásához, és ne várja meg a mintavételi időközt. Ez a behaivor csak v1 SKU. V2 Termékváltozat esetén az állapotpróbák megvárják az intervallumot. A háttérkiszolgáló le van jelölve, miután az egymást követő mintavételi hibák száma eléri a nem megfelelő küszöbértéket. |

> [!NOTE]
> A port ugyanaz a port, mint a háttérHTTP-beállításokkal.

Az alapértelmezett mintavétel csak\/a http: /127.0.0.1:\<porton\> keresi meg az állapotot. Ha az állapotmintát egyéni URL-cím hez való megugrásához vagy bármely más beállítás módosításához konfigurálnia kell, egyéni mintavételeket kell használnia.

### <a name="probe-intervals"></a>Mintavételi időközök

Az Application Gateway minden példánya egymástól függetlenül vizsgálja meg a háttérrendszert. Ugyanaz a mintavételi konfiguráció vonatkozik minden Application Gateway-példányra. Ha például a mintavételkonfiguráció 30 másodpercenként állapotmintavételeket küld, és az alkalmazásátjáró nak két példánya van, akkor mindkét példány 30 másodpercenként küldi el az állapotmintát.

Akkor is, ha több figyelők, majd minden figyelő a háttér-függetlenak egymástól függetlenül. Például ha két figyelő pont az azonos háttérkészlet két különböző portokon (két háttérhttp-beállítások), majd minden figyelő mintavételezi ugyanazt a háttérrendszer egymástól függetlenül. Ebben az esetben a két figyelők minden egyes alkalmazásátjáró-példány két mintavételes. Ha ebben a forgatókönyvben az alkalmazásátjáró két példánya van, a háttérrendszer virtuális gépe a konfigurált mintavételi időközönként négy mintavételt fog látni.

## <a name="custom-health-probe"></a>Egyéni állapotminta

Az egyéni mintavételek lehetővé teszik, hogy részletesebben szabályozhassa az állapotfigyelést. Egyéni mintavételek használatakor konfigurálhatja a mintavételi időközt, a tesztelendő URL-címet és elérési utat, valamint azt, hogy hány sikertelen választ kell elfogadnia, mielőtt a háttérkészlet-példányt nem kifogástalanállapotúnak jelölné.

### <a name="custom-health-probe-settings"></a>Egyéni állapotminta beállításai

Az alábbi táblázat definíciókat tartalmaz egy egyéni állapotminta tulajdonságait.

| Szonda tulajdonság | Leírás |
| --- | --- |
| Név |A szonda neve. Ez a név a háttérHTTP-beállításokban szereplő mintavételre hivatkozik. |
| Protocol (Protokoll) |A szonda küldéséhez használt protokoll. A mintavétel a háttérHTTP-beállításokban definiált protokollt használja |
| Gazdagép |A mintavétel elküldéséhez állomásnév. Csak akkor alkalmazható, ha többhely van konfigurálva az Application Gateway, egyébként használja a "127.0.0.1". Ez az érték eltér a virtuális gép állomásnevétől. |
| Útvonal |A szonda relatív útvonala. Az érvényes elérési út a következőtől indul: '/'. |
| Intervallum |Mintavételi időköz másodpercben. Ez az érték két egymást követő mintavétel közötti időintervallum. |
| Időtúllépés |A szonda időmeghosszabbítása másodpercben. Ha ez az időbeli elállási időszakon belül nem érkezik érvényes válasz, a mintavétel sikertelenként van megjelölve.  |
| Nem kifogástalan állapot küszöbértéke |A mintavétel újrapróbálkozások száma. A háttérkiszolgáló le van jelölve, miután az egymást követő mintavételi hibák száma eléri a nem megfelelő küszöbértéket. |

> [!IMPORTANT]
> Ha az Application Gateway egyetlen helyhez van konfigurálva, alapértelmezés szerint az állomásnevet "127.0.0.1"-ként kell megadni, kivéve, ha az egyéni mintavételben másként van konfigurálva.
> A referenciaként egyéni mintavételt\>\<küld\>\<a\>\< \<\>protokollnak a protokollnak :// host : port elérési útja . A használt port ugyanaz lesz, mint a háttérHTTP-beállításokban meghatározott port.

## <a name="nsg-considerations"></a>NSG szempontok

Engedélyeznie kell a bejövő internetes forgalmat a 65503-65534-es TCP-portokon az Application Gateway v1 Termékváltozat, és a 65200-65535 TCP-portok a v2 termékváltozatesetében, amelynek célalhálózata **bármilyen,** a forrás pedig **GatewayManager** szolgáltatáscímkeként. Ez a porttartomány az Azure-infrastruktúra kommunikációjához szükséges.

Emellett a kimenő internetkapcsolat nem tiltható le, és az **AzureLoadBalancer** címkéjből érkező bejövő forgalmat engedélyezni kell.

További információt az [Application Gateway konfigurációjának áttekintése című témakörben talál.](configuration-overview.md#network-security-groups-on-the-application-gateway-subnet)

## <a name="next-steps"></a>További lépések
Miután megismerkedett az Application Gateway állapotfigyelésével, konfigurálhat egy [egyéni állapotminta](application-gateway-create-probe-portal.md) az Azure Portalon, vagy egy [egyéni állapotminta](application-gateway-create-probe-ps.md) a PowerShell és az Azure Resource Manager telepítési modell használatával.

[1]: ./media/application-gateway-probe-overview/appgatewayprobe.png

---
title: Az Azure Application Gateway állapot-figyelésének áttekintése
description: Az Azure Application Gateway figyeli a háttér-készlet összes erőforrásának állapotát, és automatikusan eltávolítja a készletből a nem megfelelőnek ítélt erőforrásokat.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2020
ms.author: victorh
ms.openlocfilehash: e1afc389508eb75313d046b759bcc9c03a50daad
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648407"
---
# <a name="application-gateway-health-monitoring-overview"></a>Application Gateway állapot figyelésének áttekintése

Az Azure Application Gateway alapértelmezés szerint a háttér-készlet összes erőforrásának állapotát figyeli, és automatikusan eltávolítja a készletből a nem megfelelőnek ítélt erőforrásokat. Az Application Gateway továbbra is figyeli a nem megfelelő állapotú példányokat, és ha elérhetővé válnak, és megfelelő választ adnak az állapotfelmérésre, akkor visszahelyezi őket a megfelelő állapotú háttérrendszeri készletbe. Az Application Gateway az állapot-mintavételt ugyanazzal a porttal küldi el, mint amely a háttérbeli HTTP-beállításokban van meghatározva. Ez a konfiguráció biztosítja, hogy a mintavétel ugyanazt a portot vizsgálja, amelyet az ügyfelek a háttérhez való csatlakozáshoz használnak. 

Az állapot-mintavételhez használt forrás IP-cím Application Gateway a háttér-készlettől függ:
 
- Ha a háttér-készlet nyilvános végpont, akkor a forrás címe az Application Gateway előtér nyilvános IP-címe.
- Ha a háttér-készlet privát végpont, akkor a forrás IP-cím az Application Gateway alhálózatának magánhálózati IP-címe.


![Application Gateway mintavételi példa][1]

Amellett, hogy az alapértelmezett állapot mintavételi figyelését is használja, testre is szabhatja az állapot-mintavételt az alkalmazás követelményeinek megfelelően. Ebben a cikkben az alapértelmezett és az egyéni állapot-mintavétel is szerepel.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-health-probe"></a>Alapértelmezett állapot mintavétele

Az Application Gateway automatikusan beállítja az alapértelmezett állapot-mintavételt, ha nem állít be egyéni mintavételi konfigurációt. A figyelési viselkedés úgy működik, hogy HTTP-kérést küld a háttér-készlethez konfigurált IP-címekre. Az alapértelmezett mintavétel esetén, ha a háttérbeli http-beállítások HTTPS-re vannak konfigurálva, a mintavétel HTTPS-t használ, valamint a háttérrendszer állapotának teszteléséhez.

Például: úgy konfigurálhatja az Application Gatewayt, hogy az A, B és C háttér-kiszolgálókat használja a HTTP hálózati forgalom fogadásához a 80-es porton. Az alapértelmezett állapot-figyelés a három kiszolgálót 30 másodpercenként teszteli a kifogástalan HTTP-válaszhoz. Egy kifogástalan HTTP-válasz 200 és 399 közötti [állapotkódot](https://msdn.microsoft.com/library/aa287675.aspx) tartalmaz.

Ha az A kiszolgáló esetében az alapértelmezett mintavétel sikertelen, az Application Gateway eltávolítja azt a háttér-készletből, és a hálózati forgalom leáll a kiszolgálón. Az alapértelmezett mintavétel továbbra is mindig 30 másodpercenként ellenőrzi a kiszolgálót. Ha az A kiszolgáló sikeresen válaszol az egyik alapértelmezett állapot-mintavételi kérelemre, akkor a rendszer a háttérbeli készletnek megfelelően visszaadja a biztonsági mentést, és a forgalom újra áramlik a kiszolgálóra.

### <a name="probe-matching"></a>Mintavétel egyeztetése

Alapértelmezés szerint a 200 és 399 közötti állapotkódot biztosító HTTP (S) válaszok kifogástalannak számítanak. Az egyéni állapotú mintavételek emellett két megfelelő feltételt is támogatnak. Az egyeztetési feltételekkel opcionálisan módosítható az az alapértelmezett értelmezés, hogy mi teszi az egészséges választ.

Az alábbi feltételeknek megfelelő feltételek: 

- **Http-válasz állapotkód egyeztetése** – mintavétel egyeztetési feltétele a felhasználó által megadott http-válasz kód vagy válasz kód tartományának elfogadásához. A vesszővel elválasztott válaszokhoz tartozó állapotkódok vagy az állapotkód egy tartománya támogatott.
- **Http-válasz törzsének egyeztetése** – a mintavételi egyeztetési feltétel, amely a http-válasz törzsét keresi, és megegyezik egy felhasználó által megadott karakterlánccal. Az egyeztetés csak a felhasználó által megadott karakterlánc jelenlétét keresi a válasz törzsében, és nem teljes reguláris kifejezésnek felel meg.

Az egyeztetési feltételek a parancsmag használatával adhatók meg `New-AzApplicationGatewayProbeHealthResponseMatch` .

Például:

```azurepowershell
$match = New-AzApplicationGatewayProbeHealthResponseMatch -StatusCode 200-399
$match = New-AzApplicationGatewayProbeHealthResponseMatch -Body "Healthy"
```
Miután megadta a megfeleltetési feltételeket, a PowerShellben található paraméterrel lehet csatolni a mintavételi konfigurációhoz `-Match` .

### <a name="default-health-probe-settings"></a>Alapértelmezett állapot mintavételi beállításai

| Mintavételi tulajdonság | Érték | Description |
| --- | --- | --- |
| Mintavételi URL-cím |http://127.0.0.1:\<port\>/ |URL-cím elérési útja |
| Intervallum |30 |Az az időtartam másodpercben, ameddig a következő állapotú mintavétel elküldése előtt várni kell.|
| Időtúllépés |30 |Az az időtartam másodpercben, ameddig az Application Gateway megvárja a mintavételi választ, mielőtt a mintavételt nem Kifogástalan állapotba kívánja megjelölni. Ha a mintavétel kifogástalan állapotot ad vissza, a megfelelő háttér azonnal kifogástalan állapotú lesz.|
| Nem kifogástalan állapot küszöbértéke |3 |Azt szabályozza, hogy hány mintavételt kell elküldeni, ha hiba történt a normál állapotú mintavétel során. Ezeket a további állapot-mintavételeket gyors egymásutánban kell elküldeni a háttér állapotának meghatározásához, és ne várjon a mintavételi intervallumra. Ez a viselkedés csak v1 SKU. V2 SKU esetén az állapot-mintavétel megvárja az intervallumot. A háttér-kiszolgáló az egymást követő mintavételi hibák számának elérésekor a nem megfelelő állapotú küszöbértéket éri el. |

> [!NOTE]
> A port ugyanaz a port, mint a háttérbeli HTTP-beállítások.

Az alapértelmezett mintavétel csak a http: \/ /127.0.0.1: \< porton jelenik \> meg az állapot meghatározásához. Ha az állapot-mintavételt úgy kell konfigurálnia, hogy az egyéni URL-címre váltson, vagy más beállításokat módosítson, egyéni mintavételt kell használnia. További információ a HTTP-mintavételekről: [a TLS-megszakítás áttekintése és a végpontok közötti TLS és a Application Gateway](ssl-overview.md#for-probe-traffic).

### <a name="probe-intervals"></a>Mintavételi időközök

Application Gateway mintavétel minden példánya egymástól független. Ugyanaz a mintavételi konfiguráció az egyes Application Gateway példányokra vonatkozik. Ha például a mintavételi konfiguráció 30 másodpercenként elküldi az állapot-mintavételt, és az Application Gateway két példányban van, akkor mindkét példány 30 másodpercenként elküldi az állapot-mintavételt.

Ha több figyelő is van, akkor minden figyelő a háttértől függetlenül ellenőrzi a hátteret. Ha például két figyelő ugyanarra a háttér-készletre mutat két különböző porton (két háttérbeli http-beállítással konfigurálva), akkor minden figyelő egymástól függetlenül ellenőrzi a hátteret. Ebben az esetben a két figyelő mindegyik Application Gateway-példánya két mintavételt biztosít. Ha ebben az esetben az Application Gateway két példányban szerepel, a háttérbeli virtuális gép a beállított mintavételi intervallumban négy mintavételt fog látni.

## <a name="custom-health-probe"></a>Egyéni állapot mintavétele

Az egyéni mintavételek segítségével részletesebben szabályozhatja az állapot figyelését. Egyéni mintavételek használatakor beállíthatja a mintavételi intervallumot, a teszt URL-címét és elérési útját, valamint azt, hogy hány sikertelen választ fogadjon el a háttérbeli készlet példányának nem megfelelőként való megjelölése előtt.

### <a name="custom-health-probe-settings"></a>Egyéni állapot mintavételi beállításai

Az alábbi táblázat az egyéni állapotú mintavétel tulajdonságaira vonatkozó definíciókat tartalmazza.

| Mintavételi tulajdonság | Description |
| --- | --- |
| Name |A mintavétel neve. Ez a név szolgál a mintavételre a háttérbeli HTTP-beállításokban. |
| Protokoll |A mintavétel küldéséhez használt protokoll. A mintavétel a háttérbeli HTTP-beállításokban definiált protokollt használja. |
| Gazdagép |A mintavétel elküldésére szolgáló állomásnév. Csak akkor alkalmazható, ha a többhelyes Application Gateway van konfigurálva, ellenkező esetben a "127.0.0.1"-t használja. Ez az érték különbözik a virtuális gép gazdagépének nevétől. |
| Elérési út |A mintavétel relatív elérési útja. Az érvényes elérési út "/" karakterrel kezdődik. |
| Intervallum |Mintavételi időköz másodpercben. Ez az érték a két egymást követő mintavétel közötti időtartam. |
| Időtúllépés |Mintavétel időtúllépése másodpercben. Ha nem érkezik érvényes válasz ezen az időkorláton belül, a mintavétel sikertelenként van megjelölve.  |
| Nem kifogástalan állapot küszöbértéke |Újrapróbálkozások száma. A háttér-kiszolgáló az egymást követő mintavételi hibák számának elérésekor a nem megfelelő állapotú küszöbértéket éri el. |

> [!IMPORTANT]
> Ha a Application Gateway egyetlen helyhez van konfigurálva, alapértelmezés szerint az állomásnevet "127.0.0.1"-ként kell megadni, kivéve, ha az egyéni mintavétel másként van konfigurálva.
> A hivatkozáshoz egyéni mintavételt kell elküldeni az \< \> :// \< Host \> : \< port \> \< elérési útjának \> . A használt port a háttérbeli HTTP-beállításokban definiált port lesz.

## <a name="nsg-considerations"></a>NSG szempontok

Engedélyeznie kell a bejövő internetes forgalmat a 65503-65534 **-as TCP** -portokon a Application Gateway v1 SKU-hoz, valamint a 65200-65535-es TCP-portokat a v2 SKU-hoz a célként megadott alhálózattal a **GatewayManager** szolgáltatás címkéjének megfelelően. Ez a porttartomány az Azure-infrastruktúra kommunikációja esetén szükséges.

Emellett a kimenő internetkapcsolatot nem lehet letiltani, a **AzureLoadBalancer** címkétől érkező bejövő forgalmat pedig engedélyezni kell.

További információ: [Application Gateway konfiguráció áttekintése](configuration-overview.md#network-security-groups-on-the-application-gateway-subnet).

## <a name="next-steps"></a>További lépések
Miután megtanulta Application Gateway Health monitoringot, beállíthatja az [Egyéni állapot](application-gateway-create-probe-portal.md) -mintavételt a Azure Portalban vagy egy [Egyéni állapot](application-gateway-create-probe-ps.md) -mintavételt a PowerShell és a Azure Resource Manager üzembe helyezési modell használatával.

[1]: ./media/application-gateway-probe-overview/appgatewayprobe.png

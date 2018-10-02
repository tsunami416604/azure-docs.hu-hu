---
title: Az Azure Application Gateway állapotfigyelés – áttekintés
description: A figyelési képességek az Azure Application Gateway ismertetése
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: article
ms.date: 8/6/2018
ms.author: victorh
ms.openlocfilehash: f9bd0288d4009af536bdc8f45cbaed4b3f1eee18
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2018
ms.locfileid: "48018709"
---
# <a name="application-gateway-health-monitoring-overview"></a>Application Gateway állapotfigyelés – áttekintés

Az Azure Application Gateway alapértelmezés szerint a háttérkészlet összes erőforrásának állapotát figyeli, és automatikusan eltávolítja az összes erőforrást a készletből sérültnek. Az Application Gateway továbbra is figyeli a nem megfelelő állapotú példányokat, és hozzáadja őket a megfelelő háttér-készlet számára, elérhetővé válnak, és a állapotadat-mintavételek válaszolni. Az Application gateway küld az állapotadat-mintavételek a definiált a háttér-HTTP-beállítások ugyanazt a portot. Ez a konfiguráció biztosítja, hogy a mintavétel ellenőrzi, hogy az ügyfelek szokott használni a háttérrendszerhez való csatlakozáshoz ugyanazt a portot.

![Application gateway értékként megadhat például][1]

Alapértelmezett mintavételi állapotfigyelés használatán is testreszabhatja az állapotminta az alkalmazás követelményeinek megfelelően. Ebben a cikkben alapértelmezett és egyéni állapotmintákat is vonatkozik.

## <a name="default-health-probe"></a>Alapértelmezett állapotadat-mintavétel

Egy application gateway automatikusan úgy konfigurálja az alapértelmezett állapotvizsgálat, konfigurálásakor kell minden olyan egyéni mintavétel konfigurálása nem. A figyelési viselkedését, így egy HTTP-kérelem a háttér-készlet konfigurált IP-címek működik. Az alapértelmezett mintavételek a http-háttérbeállítások vannak konfigurálva, HTTPS, ha a mintavétel HTTPS PROTOKOLLT használ, valamint a háttérrendszerek állapotának ellenőrzéséhez.

Például: konfigurálja az application gateway 80-as port HTTP-hálózati forgalom fogadására A, B és C háttér-kiszolgálók használatával. A három kiszolgáló az alapértelmezett állapotfigyelés teszteli a megfelelő HTTP-válasz 30 másodpercenként. Rendelkezik megfelelő HTTP-válasz egy [állapotkód](https://msdn.microsoft.com/library/aa287675.aspx) 200-as és 399 között.

Ha a kiszolgáló az alapértelmezett mintavétel-ellenőrzés sikertelen, az application gateway automatikusan eltávolítja a háttérkészlet, és a hálózati forgalom megakadályozza, hogy elérjék a ezen a kiszolgálón. Az alapértelmezett mintavétel továbbra is keresni a kiszolgáló egy 30 másodpercenként továbbra is. Ha a kiszolgáló válaszol sikeresen egy kérést az alapértelmezett állapotvizsgálat, hozzáadódik ismét kifogástalan állapotú a háttérkészlet, és adatforgalom megindul a kiszolgálón újra.

### <a name="probe-matching"></a>Mintavételi egyeztetése

Alapértelmezés szerint a 200-as állapotkód HTTP (S) választ kifogástalan számít. Egyéni állapot-mintavételei emellett támogatja a két folyamatmegfeleltetési feltételek. Megadott feltételeknek megfelelő segítségével szükség esetén módosítsa az alapértelmezett értelmezésének mi számít egy megfelelő választ.

A következő vannak egyező feltételek: 

- **HTTP válasz állapota kód egyezés** - mintavétel megfelelő elfogadására vonatkozó feltétel a felhasználó által meghatározott http válasz kódja vagy válasz állapotkód-tartományok. Az egyes vesszővel elválasztott állapotkódok vagy állapotkód számos támogatott.
- **HTTP válasz törzsében egyezés** - mintavétel megfelelő feltétel, hogy megállapítja, hogy a HTTP-válasz törzsében és a egy felhasználó-egyezések a megadott karakterlánc. A match csak megkeresi a megadott felhasználó jelenléte karakterlánc-válasz törzsében, és nem egy teljes reguláris kifejezés egyeztetése.

Egyezés kritériumok adhatók meg a `New-AzureRmApplicationGatewayProbeHealthResponseMatch` parancsmagot.

Példa:

```
$match = New-AzureRmApplicationGatewayProbeHealthResponseMatch -StatusCode 200-399
$match = New-AzureRmApplicationGatewayProbeHealthResponseMatch -Body "Healthy"
```
Után az egyezési feltételek meg van adva, akkor lehet csatolni, mintavételi konfiguráció használatával, egy `-Match` paraméter a PowerShellben.

### <a name="default-health-probe-settings"></a>Alapértelmezett állapot mintavételi beállítások

| Mintavétel tulajdonság | Érték | Leírás |
| --- | --- | --- |
| Teszt URL-címe |http://127.0.0.1:\<port\>/ |URL-cím |
| Időköz |30 |Mennyi ideig várjon a következő állapotadat-mintavétel előtt zajlik.|
| Időtúllépés |30 |Idő másodpercben az application gateway probe válaszra vár a mintavétel nem kifogástalanként való megjelölése előtt. Mintavétel kifogástalan állapotúként adja vissza, ha a megfelelő háttér azonnal van megjelölve kifogástalan.|
| Nem kifogástalan állapot küszöbértéke |3 |Szabályozza, hogy hány mintavételek küldése abban az esetben az rendszeres állapotminta hiba történik. Ezek további állapotadat-mintavételek meghatározni a a háttérrendszer gyorsan egymás után gyorsan küldik, és nem várja meg a mintavételi időköz. A háttérkiszolgáló-van megjelölve, miután az egymást követő mintavételi hiba száma eléri a nem kifogástalan állapot küszöbértéke. |

> [!NOTE]
> A port, a háttér-HTTP-beállítások ugyanazt a portot.

Az alapértelmezett mintavétel úgy tűnik, csak http://127.0.0.1:\<port\> állapot meghatározásához. Az állapotminta nyissa meg egy egyéni URL-címet, vagy módosíthatja a további beállításokat konfigurálni kell, ha az egyéni minták kell használnia.

### <a name="probe-intervals"></a>Mintavételi időköz

Az Application Gateway összes példányát a egymástól független háttérrendszerhez mintavételi. Ugyanaz a mintavételi konfiguráció minden egyes Application Gateway-példányra vonatkozik. Például ha a mintavételi konfigurációban küldése állapotadat-mintavételek 30 másodpercenként, és az application gateway két példánnyal rendelkezik, majd példány is küldhet az állapotminta Ez lehet 30 másodperc.

Is ha több kérésfigyelőt, majd minden egyes figyelő mintavételek a háttérrendszer egymástól független. Például ha a két különböző porttal (két http-háttérbeállítások által konfigurált) ugyanazon háttérkészletéhez mutató két figyelői majd minden egyes figyelő mintavételek ugyanarra a háttérrendszerre egymástól függetlenül. Ebben az esetben a két figyelők esetében minden egyes alkalmazásátjárókhoz két mintavételezői vannak. Ha az application gateway ebben a forgatókönyvben két példánya, a háttérbeli virtuális gép jelennének meg négy mintavételek száma a konfigurált mintavételi időköz.

## <a name="custom-health-probe"></a>Egyéni állapotadat-mintavétel

Az egyéni minták lehetővé teszik egy részletesebb felügyelheti a Szolgáltatásállapot-figyelést. Egyéni mintavétel használata esetén konfigurálhatja úgy a mintavételi időköz, az URL-cím és teszteléséhez elérési út és a háttérkészlet-példány állapota nem megfelelőként jelölés elfogadásának hány sikertelen válaszokat.

### <a name="custom-health-probe-settings"></a>Egyéni állapot-mintavételi beállítások

Az alábbi táblázat biztosít jelentésdefiníciókat egyéni állapotmintát tulajdonságait.

| Mintavétel tulajdonság | Leírás |
| --- | --- |
| Name (Név) |A mintavétel neve. Ez a név segítségével tekintse meg a mintavétel a háttér-HTTP-beállítások. |
| Protokoll |A mintavétel küldéséhez használt protokoll. A mintavétel a háttér-HTTP-beállítások meghatározott protokollt használ. |
| Gazdagép |Állomásnév küldeni a Hálózatfigyelő. Alkalmazható csak akkor, ha a többhelyes konfigurálva van az Application Gatewayen, ellenkező esetben használja a "127.0.0.1". Ez az érték eltér a virtuális gép állomásnevét. |
| Útvonal |A mintavétel relatív elérési útja. Az érvényes elérési utat indul (/). |
| Időköz |Mintavétel gyakorisága másodpercben. Ez az érték két egymást követő mintavételek közötti időintervallum. |
| Időtúllépés |Mintavétel időkorlátja másodpercben. Ha az időkorláton belül nem érkezik meg egy érvényes válasz, a mintavétel van megjelölve nem sikerült.  |
| Nem kifogástalan állapot küszöbértéke |Mintavételi újrapróbálkozások számát. A háttérkiszolgáló-van megjelölve, miután az egymást követő mintavételi hiba száma eléri a nem kifogástalan állapot küszöbértéke. |

> [!IMPORTANT]
> Application Gateway egy adott hely van konfigurálva, ha a gazdagépen alapértelmezés szerint nevét kell megadni, "127.0.0.1", kivéve, ha más módon konfigurált egyéni mintavétel.
> Referencia az egyéni mintavétel küldendő \<protokoll\>://\<gazdagép\>:\<port\>\<elérési út\>. Használt port lesz ugyanazt a portot, ahogyan az a háttér-HTTP-beállítások.

## <a name="nsg-considerations"></a>NSG-t kapcsolatos szempontok

Alkalmazásátjáró-alhálózat egy hálózati biztonsági csoport (NSG) van, ha alkalmazásport-tartományok 65503 – 65534 kell megnyitni a bejövő forgalom az application gateway alhálózatának. Ezek a portok szükségesek a háttérkiszolgáló állapotadatainak API-t.

Ezenkívül nem blokkolja a kimenő internetkapcsolat, és engedélyezni kell az AzureLoadBalancer címkét érkező bejövő forgalmat.

## <a name="next-steps"></a>További lépések
Miután megismerkedett az Application Gateway-állapotfigyelés, konfigurálhat egy [egyéni állapotadat-mintavétel](application-gateway-create-probe-portal.md) az Azure Portalon, vagy egy [egyéni állapotadat-mintavétel](application-gateway-create-probe-ps.md) PowerShell és az Azure Resource Manager használatával üzembe helyezési modell.

[1]: ./media/application-gateway-probe-overview/appgatewayprobe.png

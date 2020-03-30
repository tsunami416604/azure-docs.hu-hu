---
title: Azure Cloud Services Def. LoadBalancerProbe séma | Microsoft dokumentumok
description: Az ügyfél által definiált LoadBalancerProbe a szerepkörpéldányok végpontjainak állapotminta. Egy szolgáltatásdefiníciós fájlban lévő webes vagy feldolgozói szerepkörökkel kombinálva.
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 14
author: georgewallace
ms.author: tagore
ms.openlocfilehash: 6d0e84b6724d9df4162d4be3e06a9952087a53a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537346"
---
# <a name="azure-cloud-services-definition-loadbalancerprobe-schema"></a>Az Azure Cloud Services definíciós loadbalancerprobe-sémája
A terheléselosztó mintavétel egy ügyfél által definiált állapotminta UDP-végpontok és a szerepkörpéldányok végpontok. Az `LoadBalancerProbe` nem önálló elem; egy szolgáltatásdefiníciós fájl webes vagy feldolgozói szerepkörrel kombinálva. A `LoadBalancerProbe` egynél több szerepkörrel is használható.

A szolgáltatásdefiníciós fájl alapértelmezett kiterjesztése .csdef.

## <a name="the-function-of-a-load-balancer-probe"></a>A terheléselosztó szonda funkciója
Az Azure Load Balancer felelős a bejövő forgalom útválasztása a szerepkörpéldányok. A terheléselosztó határozza meg, hogy mely példányok fogadhatnak forgalmat rendszeresen az egyes példányok állapotának meghatározásához. A terheléselosztó percenként többször is mintavételez minden példányt. Két különböző lehetőség van a példányállapot biztosítására a terheléselosztó számára – az alapértelmezett terheléselosztó mintavétel, vagy egy egyéni terheléselosztó mintavétel, amely a .csdef fájlban a LoadBalancerProbe meghatározásával valósítható meg.

Az alapértelmezett terheléselosztó mintavétel a vendégügynököt használja a virtuális gépen belül, amely csak akkor figyel és válaszol egy HTTP 200 OK válaszra, ha a példány Ready állapotban van (például amikor a példány nem foglalt, újrahasznosítás, leállítás stb. állapotban van). Ha a vendégügynök nem válaszol a HTTP 200 OK, az Azure Load Balancer megjelöli a példányt nem válaszol, és leállítja a forgalom küldését az adott példányra. Az Azure Load Balancer továbbra is pingeli a példányt, és ha a vendégügynök http 200-as használatával válaszol, az Azure Load Balancer ismét küld forgalmat az adott példányra. Webes szerepkör használata esetén a webhely kódja általában a w3wp.exe webhelyen fut, amelyet az Azure-háló vagy a vendégügynök nem figyel, ami a w3wp.exe (pl. hibák) hibáit jelenti. HTTP 500 válaszok) nem jelentik a vendégügynök, és a terheléselosztó nem tudja, hogy vegye ki a példány rotációs.

Az egyéni terheléselosztó mintavétel felülbírálja az alapértelmezett vendégügynök-mintavételt, és lehetővé teszi, hogy saját egyéni logikát hozzon létre a szerepkörpéldány állapotának meghatározásához. A terheléselosztó rendszeresen vizsgálja a végpontot (alapértelmezés szerint 15 másodpercenként), és a példány rotációban lesz figyelembe véve, ha az időtúlidőn belül tcp ACK vagy HTTP 200-as választ ad(default 31 másodperc). Ez hasznos lehet a saját logikájának megvalósításához a példányok terheléselosztó elforgatásából való eltávolításához, például nem 200 állapot ot ad vissza, ha a példány 90% feletti CPU felett van. A w3wp.exe programot használó webes szerepkörök esetében ez azt is jelenti, hogy automatikusan figyeli a webhelyét, mivel a webhelykódjában lévő hibák nem 200-as állapotot adnak vissza a terheléselosztó mintavételnek. Ha nem ad meg LoadBalancerProbe-ot a .csdef fájlban, akkor a rendszer az alapértelmezett terheléselosztó viselkedését használja (ahogy azt korábban leírtuk).

Ha egyéni terheléselosztó mintavételt használ, győződjön meg arról, hogy a logika figyelembe veszi a RoleEnvironment.OnStop metódust. Az alapértelmezett terheléselosztó mintavétel használatakor a példány kikerül az elforgatásból az OnStop hívása előtt, de egy egyéni terheléselosztó mintavétel továbbra is 200 OK-t adhat vissza az OnStop esemény során. Ha az OnStop eseményt használja a gyorsítótár karbantartásához, a szolgáltatás leállításához vagy más olyan módosítások hozására, amelyek befolyásolhatják a szolgáltatás futásidejű viselkedését, akkor meg kell győződnie arról, hogy az egyéni terheléselosztó mintavételi logikája eltávolítja a példányt az elforgatásból.

## <a name="basic-service-definition-schema-for-a-load-balancer-probe"></a>Alapvető szolgáltatásdefiníciós séma egy terheléselosztó-mintavevőszámára
 A terheléselosztó mintavételt tartalmazó szolgáltatásdefiníciós fájl alapformátuma a következő.

```xml
<ServiceDefinition …>
   <LoadBalancerProbes>
      <LoadBalancerProbe name="<load-balancer-probe-name>" protocol="[http|tcp]" path="<uri-for-checking-health-status-of-vm>" port="<port-number>" intervalInSeconds="<interval-in-seconds>" timeoutInSeconds="<timeout-in-seconds>"/>
   </LoadBalancerProbes>
</ServiceDefinition>
```

## <a name="schema-elements"></a>Sémaelemek
A `LoadBalancerProbes` szolgáltatásdefiníciós fájl eleme a következő elemeket tartalmazza:

- [LoadBalancerProbes elem](#LoadBalancerProbes)
- [LoadBalancerProbe elem](#LoadBalancerProbe)

##  <a name="loadbalancerprobes-element"></a><a name="LoadBalancerProbes"></a>LoadBalancerProbes elem
Az `LoadBalancerProbes` elem a terheléselosztó mintavételek gyűjteményét írja le. Ez az elem a [LoadBalancerProbe elem](#LoadBalancerProbe)szülőeleme. 

##  <a name="loadbalancerprobe-element"></a><a name="LoadBalancerProbe"></a>LoadBalancerProbe elem
Az `LoadBalancerProbe` elem határozza meg a modell állapotminta. Több terheléselosztó-mintavételt is definiálhat. 

Az alábbi táblázat az `LoadBalancerProbe` elem jellemzőit ismerteti:

|Attribútum|Típus|Leírás|
| ------------------- | -------- | -----------------|
| `name`              | `string` | Kötelező. A terheléselosztó szonda neve. A névnek egyedinek kell lennie.|
| `protocol`          | `string` | Kötelező. Megadja a végpont protokollját. A lehetséges értékek: `http` és `tcp`. Ha `tcp` meg van adva, a mintavétel sikerességéhez fogadott ACK szükséges. Ha `http` meg van adva, a mintavétel sikerességéhez a megadott URI-ból 200 OK válasz szükséges.|
| `path`              | `string` | Az állapotkéréshez használt URI-t a virtuális gép. `path`szükséges, `protocol` ha a `http`értéke . Ellenkező esetben ez nem megengedett.<br /><br /> Nincs alapértelmezett érték.|
| `port`              | `integer` | Választható. A szonda kommunikációjának portja. Ez nem kötelező bármely végpont, mivel ugyanazt a portot fogja használni a mintavétel. A szondázáshoz másik portot is konfigurálhat. A lehetséges értékek 1 és 65535 között mozognak, beleértve a értéket is.<br /><br /> Az alapértelmezett értéket a végpont állítja be.|
| `intervalInSeconds` | `integer` | Választható. Az időköz, másodpercben, hogy milyen gyakran a végpont az állapot. Az időköz általában valamivel kevesebb, mint a kiosztott időbeli elállási időszak fele (másodpercben), amely lehetővé teszi két teljes mintavételt, mielőtt a példányt elforgatná.<br /><br /> Az alapértelmezett érték 15, a minimális érték 5.|
| `timeoutInSeconds`  | `integer` | Választható. Az időbeli eltöltött idő másodpercben alkalmazva a mintavételre, ahol nincs válasz azt eredményezi, hogy megakadályozza a további forgalmat a végpontra történő szállításhoz. Ez az érték lehetővé teszi, hogy a végpontok gyorsabban vagy lassabban legyenek kivéve a rotációból, mint az Azure-ban (amelyek az alapértelmezettek).<br /><br /> Az alapértelmezett érték 31, a minimális érték 11.|

## <a name="see-also"></a>Lásd még:
[Felhőszolgáltatás (klasszikus) definícióséma](schema-csdef-file.md)
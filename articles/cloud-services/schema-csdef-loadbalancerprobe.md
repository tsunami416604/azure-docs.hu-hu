---
title: Az Azure Cloud Services def LoadBalancerProbe séma |} A Microsoft Docs
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: 113374a8-8072-4994-9d99-de391a91e6ea
caps.latest.revision: 14
author: jpconnock
ms.author: jeconnoc
manager: timlt
ms.openlocfilehash: f7b0ba3b4797149798037dee0188850eff6baf1d
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39003288"
---
# <a name="azure-cloud-services-definition-loadbalancerprobe-schema"></a>Azure Cloud Services – definíciós LoadBalancerProbe séma
A terheléselosztói mintavételezők egy ügyfél meghatározott állapotadat-mintavétel az UDP-beli és a szerepkörpéldányok végpontokat. A `LoadBalancerProbe` nem egy önálló elemet; a webes vagy feldolgozói szerepkör a szolgáltatásdefiníciós fájlban kombinálva. A `LoadBalancerProbe` egynél több szerepkör által használható.

Az alapértelmezett kiterjesztése a szolgáltatásdefiníciós fájl .csdef esetében.

## <a name="the-function-of-a-load-balancer-probe"></a>A terheléselosztói mintavételezők funkcióját
Az Azure Load Balancer felelős bejövő forgalom útválasztását a szerepkörpéldányon. A load balancer határozza meg, melyik példányok képes forgalom fogadására által rendszeresen tesztelés minden példány annak érdekében, hogy meghatározni annak a példánynak az állapotát. A load balancer mintavételek minden példány percenként több alkalommal. Két különböző beállításokat, amelyek biztosítják az a load balancer – alapértelmezett tartozó terheléselosztói szonda példány állapotát, vagy egyéni load balancer mintavételi, ami a LoadBalancerProbe meghatározása a .csdef fájlban valósul meg.

Az alapértelmezett terheléselosztói mintavételező használja a Vendégügynököt a virtuális gépen, amely figyeli és reagál egy HTTP 200 OK válasz csak akkor, ha a példány (például ha a példány nem szerepel a foglalt, majd újrahasznosítás, leállítása, állam stb.) a kész állapotban van. Ha a Vendégügynök nem válaszol a HTTP 200 OK, az Azure Load Balancer jelöli meg a példány nem válaszol, és nem irányít több forgalmat-példányhoz. Az Azure Load Balancer pingelni a példány folytatja, és ha a Vendégügynök válaszol egy HTTP 200-as, az Azure Load Balancer forgalmat küld példányhoz újra. A webes szerepkör használata esetén webhely általában fut a nem figyelt az Azure-hálót w3wp.exe vagy vendégügynök, ami azt jelenti, w3wp.exe hibáinak (pl. 500-as HTTP-válaszok) nem kell jelenteni a Vendég ügynök és a load balancer nem tudja annak a példánynak a rotációból érvénybe.

Egyéni tartozó terheléselosztói szonda felülbírálja az alapértelmezett Vendég ügynök mintavétel, és lehetővé teszi, hogy hozzon létre saját egyéni logikát meghatározni a szerepkörpéldány állapotát. A load balancer rendszeresen a végpont (alapértelmezés szerint minden 15 másodperc) és a példány mintavételek figyelembe kell venni Elforgatás Ha, 200-as HTTP vagy TCP ACK válaszol az időkorláton (alapértelmezés szerint 31 másodperc) belül. Ez lehet hasznos, ha szeretné saját logikát példányt eltávolítja a load balancer rotációból, például adatszolgáltató nem 200 állapotba, ha a példány meghaladja a 90 %-ot. A w3wp.exe használatával a webes szerepkörök esetében ez azt is jelenti automatikus kap a webhelyeket, mivel a webhely kódban hibák nem 200 állapotba térjen vissza a terheléselosztói mintavételezők figyelését. Ha nem definiál egy LoadBalancerProbe a .csdef fájlban, akkor az alapértelmezett terheléselosztó viselkedését (korábban ismertetett) használható.

Ha egyéni terheléselosztói mintavételezők használja, biztosítania kell, hogy a logikai figyelembe veszi a RoleEnvironment.OnStop metódust. Az alapértelmezett terheléselosztói mintavételező használatakor a példány a rotációból hívott OnStop előtt, de egyéni terheléselosztói mintavételezők 200-as rendben visszaadása az OnStop esemény során továbbra is. Ha az OnStop esemény használ a gyorsítótár karbantartása, állítsa le a szolgáltatást, vagy egyéb módon történő a szolgáltatás működését érintő módosításokat, majd meg kell győződjön meg arról, hogy az egyéni load balancer mintavételi logikát eltávolítja az előfordulást a rotációból.

## <a name="basic-service-definition-schema-for-a-load-balancer-probe"></a>Alapszintű szolgáltatásdefiníciós sémában terheléselosztói mintavételezők
 A terheléselosztói mintavételezők tartalmazó szolgáltatásdefiníciós fájl alapvető formátuma a következő.

```xml
<ServiceDefinition …>
   <LoadBalancerProbes>
      <LoadBalancerProbe name="<load-balancer-probe-name>" protocol="[http|tcp]" path="<uri-for-checking-health-status-of-vm>" port="<port-number>" intervalInSeconds="<interval-in-seconds>" timeoutInSeconds="<timeout-in-seconds>"/>
   </LoadBalancerProbes>
</ServiceDefinition>
```

## <a name="schema-elements"></a>Séma elemei
A `LoadBalancerProbes` eleme a szolgáltatásdefiníciós fájlt az alábbi elemeket tartalmazza:

- [LoadBalancerProbes elem](#LoadBalancerProbes)
- [LoadBalancerProbe elem](#LoadBalancerProbe)

##  <a name="LoadBalancerProbes"></a> LoadBalancerProbes elem
A `LoadBalancerProbes` elem a gyűjteményben, a load balancer vizsgálatok írja le. Az elem azon a [LoadBalancerProbe elem](#LoadBalancerProbe). 

##  <a name="LoadBalancerProbe"></a> LoadBalancerProbe elem
A `LoadBalancerProbe` elem definiálja az állapotminta egy modell. Több load balancer vizsgálatok határozhatja meg. 

A következő táblázat ismerteti az attribútumai a `LoadBalancerProbe` elem:

|Attribútum|Típus|Leírás|
| ------------------- | -------- | -----------------|
| `name`              | `string` | Kötelező. A terheléselosztói mintavételezők neve. A névnek egyedinek kell lennie.|
| `protocol`          | `string` | Kötelező. Adja meg a végpont által használt protokoll. A lehetséges értékek: `http` és `tcp`. Ha `tcp` van megadva, a fogadott ACK megadása kötelező a mintavétel a sikeres. Ha `http` van megadva, a megadott URI-ból 200 OK válasz a mintavétel a sikeres megadása kötelező.|
| `path`              | `string` | A virtuális gép állapotának lekérdezéséhez használt URI azonosító. `path` kötelező megadni, ha `protocol` értékre van állítva `http`. Ellenkező esetben nem engedélyezett.<br /><br /> Nincs alapértelmezett érték.|
| `port`              | `integer` | Választható. A kommunikáció a mintavételi port. Ez nem kötelező, minden végpont, mivel ugyanazt a portot a mintavétel majd fogja használni. Egy másik portot a tesztelés, valamint konfigurálhatja. A lehetséges értékek tartomány 1 és 65535 között, a határokat is beleértve.<br /><br /> Az alapértelmezett érték a végpont állítja be.|
| `intervalInSeconds` | `integer` | Választható. Az időköz (másodpercben), hogy milyen gyakran történjen a végpont az állapot-mintavételi. Az intervallum általában valamivel kisebb, mint fele a lefoglalt időkorlát (másodpercben), amely lehetővé teszi, hogy a két teljes vizsgálatok előtt a rotációból példány.<br /><br /> Az alapértelmezett érték 15, minimális értéke 5.|
| `timeoutInSeconds`  | `integer` | Választható. Az időkorlát (másodpercben), ahol nincs válasz eredményez további leállítása folyamatban van a végpont célszolgáltatásnak érkező forgalmat a mintavételi alkalmazza. Ez az érték engedélyezi a végpontok végrehajtandó rotációból gyorsabb vagy lassabb, mint a tipikus alkalommal használja az Azure-ban (amelyek az alapértelmezett érték).<br /><br /> A minimális érték az alapértelmezett értéke 31, 11.|

## <a name="see-also"></a>Lásd még:
[Cloud Service (klasszikus) definíciós séma](schema-csdef-file.md)
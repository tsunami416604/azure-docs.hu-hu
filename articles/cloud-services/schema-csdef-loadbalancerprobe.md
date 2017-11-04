---
title: "Azure Cloud Services alapértelmezett LoadBalancerProbe séma |} Microsoft Docs"
ms.custom: 
ms.date: 04/14/2015
ms.prod: azure
ms.reviewer: 
ms.service: cloud-services
ms.suite: 
ms.tgt_pltfrm: 
ms.topic: reference
ms.assetid: 113374a8-8072-4994-9d99-de391a91e6ea
caps.latest.revision: "14"
author: thraka
ms.author: adegeo
manager: timlt
ms.openlocfilehash: 31c974c5a4b9dc9cff882ff42b73ee023fc4ad9b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cloud-services-definition-loadbalancerprobe-schema"></a>Azure Cloud Services – Definition LoadBalancerProbe séma
A terheléselosztói mintavétel egy ügyfél meghatározott állapotmintáihoz UDP végpontok és a szerepkörpéldányok végpontok. A `LoadBalancerProbe` van nem egy önálló elem; a webes vagy feldolgozói szerepkör a szolgáltatásdefiníciós fájlban van kombinálva. A `LoadBalancerProbe` egynél több szerepkör is használható.

Az alapértelmezett kiterjesztése a szolgáltatásdefiníciós fájl .csdef.

## <a name="the-function-of-a-load-balancer-probe"></a>Terheléselosztói mintavétel funkciót
Az Azure Load Balancer felelős útválasztási bejövő forgalom a szerepkörpéldányok számára. A load balancer meghatározza, hogy előfordulások forgalom fogadására által rendszeresen probing feltünteti, hogy a példány állapotának meghatározásához. A load balancer vizsgálat minden példány többször / perc. A példány állapotát a terheléselosztó – az alapértelmezett terheléselosztói mintavétel, így két különböző lehetőségek közül, vagy egy egyéni terheléselosztó mintavételi, amely megvalósítja a LoadBalancerProbe meghatározása a .csdef fájlban.

Az alapértelmezett terheléselosztói mintavétel használja a Vendégügynököt a virtuális gépen belül, amely figyeli és reagál HTTP 200 OK válasz csak akkor, ha a példány (például ha a példány nincs a foglalt, az újrahasznosítás, leállítása, állapotok stb.) a kész állapotban van. Ha a Vendégügynök nem válaszol a HTTP 200 OK, az Azure Load Balancer jelöli meg a példány válaszol, és leállítja a forgalom küldése annak a példánynak. Az Azure Load Balancer Pingelje meg a példány folytatja, és ha a Vendégügynök egy HTTP 200 válaszol, az Azure Load Balancer küld forgalom példánynak újra. A webes szerepkör használata esetén webhely általában fut a nem figyelt az Azure-hálót w3wp.exe vagy vendégügynök, ami azt jelenti, w3wp.exe hibáinak (pl. 500-as HTTP-válaszok) nem fog szerepelni a vendégügynök és a betöltés terheléselosztó nem tudja kívül Elforgatás példánynak érvénybe.

Az egyéni terheléselosztói mintavétel felülbírálja az alapértelmezett Vendég ügynök mintavétel, és lehetővé teszi a saját egyéni logika a szerepkör-példány állapotának létrehozását. A load balancer rendszeresen mintavételt a végpont (alapértelmezés szerint minden 15 másodperc) és a példány figyelembe kell venni Elforgatás Ha azt 200-as HTTP vagy TCP Nyugtázási válaszol az időkorláton (alapértelmezett 31 másodperc) belül. Ennek megvalósításához a saját logikát távolítsa el a terhelés terheléselosztó elforgatás, például vissza nem 200 állapotba, ha a példány CPU 90 % feletti példányait hasznos lehet. A webes szerepkörök w3wp.exe használ, ez azt is jelenti kap automatikus figyelési a webhely, mivel a webhely kódban hibák nem 200 állapot térjen vissza a terheléselosztói mintavétel. Ha nem definiál egy LoadBalancerProbe a .csdef fájlban, akkor az alapértelmezett betöltési terheléselosztó viselkedés (korábban leírt) használható.

Ha egyéni terheléselosztói mintavétel használ, győződjön meg róla, hogy a logika figyelembe veszi a RoleEnvironment.OnStop metódust. Az alapértelmezett terheléselosztói mintavétel használatakor a példány használatban van-e előtt hívták OnStop Elforgatás kívül, de egyéni terheléselosztói mintavétel továbbra is a 200 OK vissza a OnStop esemény során. Ha az OnStop esemény segítségével gyorsítótár karbantartása, akkor állítsa le a szolgáltatást, vagy egyéb módosítások, amelyek hatással lehetnek a szolgáltatás működését, majd szeretné győződjön meg arról, hogy a egyéni betöltési terheléselosztói mintavétel logika Elforgatás eltávolítja a példányt.

## <a name="basic-service-definition-schema-for-a-load-balancer-probe"></a>Terheléselosztói mintavétel alapszintű service definition sémája
 A szolgáltatásdefiníciós fájlt tartalmazó terheléselosztói mintavétel alapvető formátuma a következő.

```xml
<ServiceDefinition …>
   <LoadBalancerProbes>
      <LoadBalancerProbe name="<load-balancer-probe-name>" protocol="[http|tcp]" path="<uri-for-checking-health-status-of-vm>" port="<port-number>" intervalInSeconds="<interval-in-seconds>" timeoutInSeconds="<timeout-in-seconds>"/>
   </LoadBalancerProbes>
</ServiceDefinition>
```

## <a name="schema-elements"></a>Séma elemei
A `LoadBalancerProbes` eleme a szolgáltatásdefiníciós fájlban a következő elemeket tartalmazza:

- [LoadBalancerProbes elem](#LoadBalancerProbes)
- [LoadBalancerProbe elem](#LoadBalancerProbe)

##  <a name="LoadBalancerProbes"></a>LoadBalancerProbes elem
A `LoadBalancerProbes` elem írja le a load balancer mintavételt gyűjteménye. Az elem azon a [LoadBalancerProbe elem](#LoadBalancerProbe). 

##  <a name="LoadBalancerProbe"></a>LoadBalancerProbe elem
A `LoadBalancerProbe` elem definiálja a modell állapotmintáihoz. Több load balancer mintavételt adhat meg. 

A következő táblázat ismerteti az attribútumai a `LoadBalancerProbe` elem:

|Attribútum|Típus|Leírás|
| ------------------- | -------- | -----------------|
| `name`              | `string` | Kötelező. A terheléselosztói mintavétel neve. A nevének egyedinek kell lennie.|
| `protocol`          | `string` | Kötelező. A végpont protokollt adja meg. A lehetséges értékek: `http` és `tcp`. Ha `tcp` van megadva, a fogadott nyugtát KAPNI szükség ahhoz, hogy sikeresen mintavétel. Ha `http` van megadva, a megadott URI azonosító 200 OK válaszára szükség ahhoz, hogy sikeresen mintavétel.|
| `path`              | `string` | Az URI-állapot kér a virtuális gép használja. `path`szükség, ha `protocol` értéke `http`. Ellenkező esetben nem engedélyezett.<br /><br /> Nincs alapértelmezett értéke.|
| `port`              | `integer` | Választható. Kommunikáció a mintavételi portot. Ez nem kötelező a tetszőleges végpontot, mivel ugyanazt a portot használja a mintavétel. Konfigurálhatja a probing, valamint más portot. A lehetséges értékek közé 1 és 65535 között, a határokat is beleértve.<br /><br /> Az alapértelmezett beállítás a végpont által.|
| `intervalInSeconds` | `integer` | Választható. Az időköz másodpercben, hogy milyen gyakran mintavételi állapotadatok végpont. Az időköz általában valamivel kisebb, mint fele a lefoglalt időkorlát (másodpercben), amely lehetővé teszi, hogy a két teljes mintavételt a példány Elforgatás kívül helyezése előtt.<br /><br /> Az alapértelmezett érték 15, minimális értéke 5.|
| `timeoutInSeconds`  | `integer` | Választható. Az időkorlát (másodpercben), ahol nincs válasz eredményez további kézbesítését, hogy a végpont-forgalom leállítása a mintavétel alkalmazza. Ezt az értéket lehetővé teszi a végrehajtását végpontok kívül Elforgatás gyorsabb vagy alacsonyabb, mint a szokásos alkalommal használja az Azure-ban (amelyek az alapértelmezett érték).<br /><br /> A minimális érték az alapértelmezett értéke 31, 11.|

## <a name="see-also"></a>Lásd még:
[Cloud Service (klasszikus) Definition séma](schema-csdef-file.md)
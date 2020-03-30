---
title: Az Azure-beli adatle-szolgáltatás tcp-alaphelyzetbe állítása
titleSuffix: Azure Load Balancer
description: Ebből a cikkből megtudhatja, hogy az Azure Load Balancer kétirányú TCP RST-csomagokkal rendelkezik az írásbeli időtúloldalon.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2019
ms.author: allensu
ms.openlocfilehash: d3d836ddea8d07a25ad09e6f19d9f17a680decd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294404"
---
# <a name="load-balancer-with-tcp-reset-on-idle"></a>Terheléselosztó TCP alaphelyzetbe állítással az idle-n

[A Standard Load Balancer](load-balancer-standard-overview.md) segítségével kiszámíthatóbb alkalmazásviselkedést hozhat létre a forgatókönyvekhez, ha engedélyezi a TCP reset-et az idle-ben egy adott szabályhoz. A terheléselosztó alapértelmezett viselkedése az, hogy csendben eldobja a folyamatokat, amikor eléri a folyamat tétlen időtúltartományát.  Ha engedélyezi ezt a szolgáltatást, a terheléselosztó kétirányú TCP-alaphelyzetbe állítást (TCP RST-csomagot) küld az alapjárati időtúloldalon.  Ez tájékoztatja az alkalmazás végpontjait, hogy a kapcsolat túllépte az időtúllépés, és már nem használható.  A végpontok szükség esetén azonnal létrehozhatnak egy új kapcsolatot.

![Terheléselosztó TCP alaphelyzetbe állítása](media/load-balancer-tcp-reset/load-balancer-tcp-reset.png)
 
Módosítja ezt az alapértelmezett viselkedést, és engedélyezi a TCP-alapbeállítások küldését a bejövő nat-szabályokon, terheléselosztási szabályokon és [kimenő szabályokon.](https://aka.ms/lboutboundrules)  Ha szabályonként engedélyezve van, a terheléselosztó kétirányú TCP reset (TCP RST-csomagok) küld az ügyfél- és a kiszolgálóvégpontoknak az összes egyező folyamat tétlen időtúlterhelésének időpontjában.

A TCP RST-csomagokat fogadó végpontok azonnal lezárják a megfelelő szoftvercsatornát. Ez azonnali értesítést küld a végpontok számára arról, hogy a kapcsolat kiadása megtörtént, és az ugyanazon a TCP-kapcsolaton lévő jövőbeli kommunikáció sikertelen lesz.  Az alkalmazások kiüríthetik a kapcsolatokat, amikor a szoftvercsatorna bezárul, és szükség szerint újra létre tudják hozni a kapcsolatokat anélkül, hogy megvárnák a TCP-kapcsolat időtúllépési idejét.

Számos esetben ez csökkentheti a TCP (vagy alkalmazásréteg) keepalives küldésének szükségességét a folyamat tétlen időhaigényének frissítéséhez. 

Ha az alapjárati időtartamok meghaladják a konfiguráció által engedélyezett időtartamokat, vagy az alkalmazás nemkívánatos viselkedést mutat, ha a TCP-alapbeállítások engedélyezve vannak, előfordulhat, hogy továbbra is tcp keepalives (vagy alkalmazásréteg keepalives) kell használnia a TCP-kapcsolatok élésének figyeléséhez.  Továbbá, keepalives is hasznos marad, ha a kapcsolat proxied valahol az utat, különösen az alkalmazás réteg keepalives.  

Gondosan vizsgálja meg a teljes végpontok közötti forgatókönyvet annak eldöntéséhez, hogy a TCP-beállítások engedélyezése, az alapjárati idő-szám módosítása és további lépések szükségesek-e az alkalmazás kívánt viselkedésének biztosításához.

## <a name="enabling-tcp-reset-on-idle-timeout"></a>A TCP alaphelyzetbe állításának engedélyezése tétlen időkitöltéskor

A 2018-07-01 API-verzió használatával szabályonként engedélyezheti a kétirányú TCP-alapbeállítások küldését tétlen időszinten:

```json
      "loadBalancingRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

```json
      "inboundNatRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

```json
      "outboundRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

## <a name="region-availability"></a><a name="regions"></a>A régió elérhetősége

Minden régióban elérhető.

## <a name="limitations"></a>Korlátozások

- A TCP RST-t csak a TCP-kapcsolat során, ESTABLISHED állapotban küldve.

## <a name="next-steps"></a>További lépések

- További információ a [Standard Load Balancer](load-balancer-standard-overview.md)- összegről.
- További információ a [kimenő szabályokról.](load-balancer-outbound-rules-overview.md)
- [TCP RST konfigurálása tétlen időkitöltéskor](load-balancer-tcp-idle-timeout.md)

---
title: Load Balancer TCP alaphelyzetbe állítása üresjáratban az Azure-ban
titlesuffix: Azure Load Balancer
description: A terheléselosztó kétirányú TCP ÜZE csomagokkal az üresjárat időkorlátja
services: load-balancer
documentationcenter: na
author: KumudD
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/29/2019
ms.author: kumud
ms.openlocfilehash: 18e4a7ae5010730054dd110828c63e8418b93f39
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55296919"
---
# <a name="load-balancer-with-tcp-reset-on-idle-public-preview"></a>Load Balancer a TCP üresjárati (nyilvános előzetes verzió) alaphelyzetbe állítása

Használhat [Standard Load Balancer](load-balancer-standard-overview.md) hozhat létre egy kiszámíthatóbb alkalmazások viselkedése a használati eseteihez egy adott szabály engedélyezése a TCP üresjárati a alaphelyzetbe állítása. Load Balancer alapértelmezett viselkedését, hogy csendes dobja el a flow, ha eléri a folyamat az üresjárati időkorlátot.  A funkció engedélyezése Load Balancer küldjön a kétirányú TCP (TCP ÜZE csomagot) alaphelyzetbe állítja az üresjárati időkorlát miatt.  Ez tájékoztatja a végpontjainak, hogy a kapcsolat túllépte az időkorlátot, és már nem használható.  Végpontok azonnal is létrehozhat egy új kapcsolatot, szükség esetén.

![Load Balancer TCP alaphelyzetbe állítása](media/load-balancer-tcp-reset/load-balancer-tcp-reset.png)

>[!NOTE] 
>Load Balancer a TCP alaphelyzetbe állítása az üresjárati időkorlát funkcióiról jelenleg nyilvános előzetesként elérhető, és korlátozott számú elérhető [régiók](#regions). Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
Módosíthatja az alapértelmezett viselkedését és alaphelyzetbe állítja a TCP üresjárati időkorlát a bejövő NAT-szabályokat, a terheléselosztási szabályokat és küldése engedélyezése és [kimenő szabályok](https://aka.ms/lboutboundrules).  Egy szabályban engedélyezésekor a Load Balancer küld-e az ügyfél- és végpontok összes forgalommal egyező üresjárati időkorlát időpontjában kétirányú TCP alaphelyzetbe állítása (TCP ÜZE csomagok).

TCP ÜZE csomagok fogadása végpontok zárja be a megfelelő szoftvercsatorna azonnal. Ez lehetővé teszi az azonnali értesítések a végpontokra, amely a kiadás a kapcsolat történt, és ugyanazt a TCP-kapcsolatot minden jövőbeli kommunikáció sikertelen lesz.  Alkalmazások is kiürítheti kapcsolatok, ha a szoftvercsatorna bezárja, majd helyreállítani a kapcsolatok nem várja meg a TCP-kapcsolatot, végül időtúllépés igény szerint.

A sok esetben ez csökkentheti a küldési TCP (vagy alkalmazásréteg) életben frissítése egy folyamatot az üresjárati időkorlátot. 

Ha az inaktivitási időtartam mérete meghaladja a konfiguráció által engedélyezett, vagy az alkalmazás látható, a nem kívánt viselkedést TCP visszaállítja engedélyezve van, előfordulhat, hogy továbbra is szeretné TCP életben (vagy alkalmazás réteg életben) használatával a TCP-kapcsolatok a liveness figyeli.  További életben is maradhat, ha a kapcsolat nem használ proxyt valahol az útvonalban, különösen az alkalmazás réteg életben hasznos.  

Gondosan vizsgálja meg a teljes végpontok közötti forgatókönyv eldönteni, hogy engedélyezése a TCP visszaállítja az időtúllépést módosítani közül, és ha további lépésekre lehet szükség ahhoz, hogy az alkalmazás kívánt viselkedése.

## <a name="enabling-tcp-reset-on-idle-timeout"></a>Az üresjárati időkorlát engedélyezése a TCP alaphelyzetbe állítása

2018-07-01-es verziójú API-val, engedélyezheti a küldését a kétirányú alaphelyzetbe állítja a TCP üresjárati időtúllépés miatt egy szabály alapján:

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

## <a name="regions"></a> Régiónkénti elérhetőség

Minden régióban elérhető.

## <a name="limitations"></a>Korlátozások

- Portál konfigurálása, illetve megtekintheti a TCP alaphelyzetbe állítása nem használható.  Ehelyett használja a sablonok, REST API-t, Az CLI 2.0-val vagy PowerShell.

## <a name="next-steps"></a>További lépések

- Ismerje meg [a Standard Load Balancer](load-balancer-standard-overview.md).
- Ismerje meg [kimenő szabályok](load-balancer-outbound-rules-overview.md).

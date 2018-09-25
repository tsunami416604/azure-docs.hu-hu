---
title: Üresjárati időkorlát terheléselosztó TCP alaphelyzetbe terhelésének |} A Microsoft Docs
description: A terheléselosztó kétirányú TCP ÜZE csomagokkal az üresjárat időkorlátja
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/21/2018
ms.author: kumud
ms.openlocfilehash: b33c701bde082404ea86c9882dcb7bf50d1f1df9
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47036175"
---
# <a name="load-balancer-with-tcp-reset-on-idle-timeout-public-preview"></a>Load Balancer visszaállítással TCP üresjárati időtúllépés miatt (nyilvános előzetes verzió)

Használhat [Standard Load Balancer](load-balancer-standard-overview.md) hozhat létre egy kiszámíthatóbb alkalmazások viselkedése a kétirányú TCP (TCP ÜZE csomagot) alaphelyzetbe állítja a használati eseteihez minden állítható be üresjárati időkorlát.  Load Balancer alapértelmezett viselkedését, hogy csendes dobja el a flow, ha eléri a folyamat az üresjárati időkorlátot.

![Load Balancer TCP alaphelyzetbe állítása](media/load-balancer-tcp-reset/load-balancer-tcp-reset.png)

>[!NOTE] 
>Load Balancer a TCP alaphelyzetbe állítása az üresjárati időkorlát funkcióiról jelenleg nyilvános előzetesként elérhető, és korlátozott számú elérhető [régiók](#regions). Ebben az előzetes verzióban egy szolgáltatásiszint-szerződés nélkül, és nem javasolt éles számítási feladatok esetében. Bizonyos szolgáltatások nem támogatott, vagy korlátozott képességekkel rendelkezik. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
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

Ezt a paramétert a következő régiók jelenleg hatékony.  A régióban nem szerepel itt a paraméter nem befolyásolja.

| Régió |
|---|
| USA 2. keleti régiója |
| USA északi régiója |
| USA nyugati régiója |

Ez a táblázat frissíti, az Előnézet ki van bontva, régióiban.  

## <a name="limitations"></a>Korlátozások

- Korlátozott [régiók rendelkezésre állása](#regions).
- Portál konfigurálása, illetve megtekintheti a TCP alaphelyzetbe állítása nem használható.  Ehelyett használja a sablonok, REST API-t, Az CLI 2.0-val vagy PowerShell.

## <a name="next-steps"></a>További lépések

- Ismerje meg [a Standard Load Balancer](load-balancer-standard-overview.md).
- Ismerje meg [kimenő szabályok](https://aka.ms/lboutboundrules).
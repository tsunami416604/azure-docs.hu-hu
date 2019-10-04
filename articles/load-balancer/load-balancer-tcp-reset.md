---
title: Load Balancer TCP alaphelyzetbe állítása üresjáratban az Azure-ban
titlesuffix: Azure Load Balancer
description: Load Balancer a kétirányú TCP első csomagokkal üresjárati időkorláton
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
ms.openlocfilehash: 8485f4b6e8d4ff55de4930b3cfb7a07802cf1d41
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274163"
---
# <a name="load-balancer-with-tcp-reset-on-idle-public-preview"></a>Load Balancer a TCP alaphelyzetbe állítása üresjáratban (nyilvános előzetes verzió)

A [standard Load Balancer](load-balancer-standard-overview.md) használatával kiszámítható alkalmazás-viselkedést hozhat létre a forgatókönyvek esetében, ha engedélyezi a TCP alaphelyzetbe állítását egy adott szabály esetében. Load Balancer alapértelmezett viselkedése a folyamatok csendes eldobása, amikor a folyamat üresjárati időkorlátja eléri a folyamatot.  Ha engedélyezi ezt a funkciót, a Load Balancer a kétirányú TCP-alaphelyzetbe (TCP első csomag) küldi az üresjárati időkorlátot.  Ez tájékoztatni fogja az alkalmazás-végpontokat arról, hogy a kapcsolatok túllépték az időkorlátot, és már nem használható.  Ha szükséges, a végpontok azonnal létrehozhatnak egy új kapcsolatot.

![Load Balancer TCP alaphelyzetbe állítása](media/load-balancer-tcp-reset/load-balancer-tcp-reset.png)

>[!NOTE] 
>Az üresjárat időkorlátjának TCP-visszaállítási funkciójának Load Balancer jelenleg nyilvános előzetes verzióként érhető el. Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
Ezt az alapértelmezett viselkedést kell módosítania, és engedélyezni kell a TCP alaphelyzetbe állítását a bejövő NAT-szabályok, a terheléselosztási szabályok és a [Kimenő szabályok](https://aka.ms/lboutboundrules)üresjárati időkorlátján.  Ha engedélyezve van a szabály, a Load Balancer a kétirányú TCP-visszaállítást (TCP első csomagokat) küldi az ügyfél-és a kiszolgálói végpontoknak az összes egyező folyamat üresjárati időkorlátjának időpontjában.

Az első TCP-csomagokat fogadó végpontok azonnal lezárták a megfelelő szoftvercsatornát. Ez azonnali értesítést küld a végpontokról, amelyeken a kapcsolat megjelent, és az azonos TCP-kapcsolaton folytatott jövőbeli kommunikáció meghiúsul.  Az alkalmazások kitörölhetik a kapcsolatokat, amikor a szoftvercsatorna lezárult, és szükség esetén újra létrehozza a kapcsolatokat, anélkül, hogy a TCP-kapcsolatra kellene várnia.

Számos esetben előfordulhat, hogy a TCP (vagy az alkalmazási réteg) Keepalives egy folyamat üresjárati időkorlátjának frissítéséhez szükséges. 

Ha az üresjárati időtartama meghaladja a konfiguráció által engedélyezett beállításokat, vagy az alkalmazás nem kívánt viselkedést jelenít meg a TCP-visszaállítások engedélyezésével, akkor továbbra is a TCP-Keepalives (vagy az alkalmazás rétegének Keepalives) kell használnia a TCP-kapcsolatok élő állapotának figyeléséhez.  Emellett a Keepalives is hasznos lehet, ha a Kapcsolódás az elérési útban van, különösen az alkalmazás rétegbeli Keepalives.  

Alaposan vizsgálja meg a teljes végpontok közötti forgatókönyvet, és döntse el, hogy kihasználja-e a TCP alaphelyzetbe állítását, az üresjárat időkorlátjának módosítását, és ha további lépések szükségesek a kívánt alkalmazás működésének biztosításához.

## <a name="enabling-tcp-reset-on-idle-timeout"></a>A TCP alaphelyzetbe állításának engedélyezése üresjárati időkorláton

Az API 2018-07-01-es verziójának használatával engedélyezheti a kétirányú TCP-visszaállítások küldését a következő üresjárati időkorláton:

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

## <a name="regions"></a>Régió elérhetősége

Minden régióban elérhető.

## <a name="limitations"></a>Korlátozások

- A portál nem használható a TCP-visszaállítás konfigurálására vagy megtekintésére.  Ehelyett használja a sablonok, REST API-t, Az CLI 2.0-val vagy PowerShell.
- Az első TCP-t a rendszer csak a TCP-kapcsolatok során, a létesített állapotban küldik el.

## <a name="next-steps"></a>További lépések

- Ismerje meg [a Standard Load Balancer](load-balancer-standard-overview.md).
- További információ a [kimenő szabályokról](load-balancer-outbound-rules-overview.md).

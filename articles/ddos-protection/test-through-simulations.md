---
title: Azure DDoS Protection szimuláció tesztelése
description: Tudnivalók a szimulációk teszteléséről
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: eff738e24b3abce52e80291c55a3ae64c3c8c853
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/28/2020
ms.locfileid: "92905494"
---
# <a name="test-through-simulations"></a>Tesztelés szimulációk használatával

Érdemes tesztelni a feltételezéseket arról, hogy a szolgáltatások hogyan reagálnak a támadásokra az időszakos szimulációk végrehajtásával. A tesztelés során ellenőrizze, hogy a szolgáltatások vagy alkalmazások továbbra is a várt módon működnek-e, és nincs-e fennakadás a felhasználói élményben. Azonosítsa a technológiai és a feldolgozási szempontból mutatkozó hiányosságokat, és foglalja bele őket a DDoS-válasz stratégiájába. Javasoljuk, hogy hajtsa végre az ilyen teszteket átmeneti környezetekben vagy nem csúcsidőben, hogy csökkentse az éles környezetre gyakorolt hatást.

A [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud)-t, egy önkiszolgáló forgalmi generátort hoztunk létre egy olyan felület létrehozásához, amelyben az Azure-ügyfelek a szimulációk DDoS Protection használatára képes nyilvános végpontokon keresztül forgalmat generálnak. A szimulációval a következőket végezheti el:

- Annak ellenőrzése, hogy a Azure DDoS Protection Hogyan védi az Azure-erőforrásokat a DDoS-támadásokkal szemben.
- Optimalizálja az incidensek megválaszolásának folyamatát a DDoS-támadás alatt.
- A DDoS-megfelelőség dokumentálása.
- A hálózati biztonsági csapatok betanítása.

## <a name="prerequisites"></a>Előfeltételek

- Az oktatóanyag lépéseinek elvégzéséhez először létre kell hoznia egy [Azure DDoS standard Protection-csomagot](manage-ddos-protection.md) a védett nyilvános IP-címekkel.
- Először létre kell hoznia egy fiókot a [BreakingPoint-felhővel](http://breakingpoint.cloud/). 

## <a name="configure-a-ddos-test-attack"></a>DDoS-teszt támadás konfigurálása

1. Adja meg vagy válassza ki a következő értékeket, majd válassza a **teszt indítása** lehetőséget:

    |Beállítás        |Érték                                              |
    |---------      |---------                                          |
    |Cél IP-címe           | Adja meg a tesztelni kívánt nyilvános IP-cím egyikét.                     |
    |Portszám   | Adja meg a _443_ értéket.                       |
    |DDoS-profil | Válassza a **TCP SYN FLOOD** lehetőséget.|
    |Teszt mérete       | Válassza **a több mint 200.000 PPS, 100 Mbps és 8 forrás IP-** címek elemet.                                  |
    |Teszt időtartama | Válasszon **10 percet** .|

Ekkor az alábbihoz hasonlónak kell megjelennie:

![DDoS Attack – szimulációs példa: BreakingPoint Cloud](./media/ddos-attack-simulation/ddos-attack-simulation-example-1.png)

## <a name="monitor-and-validate"></a>Figyelés és ellenőrzés

1. Jelentkezzen be, https://portal.azure.com és nyissa meg az előfizetését.
1. Válassza ki azt a nyilvános IP-címet, amelyen a támadást tesztelte.
1. A **Figyelés** alatt kattintson a **Metrikák** elemre.
1. A **metrika** beállításnál válassza a _DDoS Attack vagy a not (nem_ ) lehetőséget.

Ha az erőforrás támadás alatt áll, látnia kell, hogy az érték **0** és **1** között változik, az alábbi képhez hasonlóan:

![DDoS Attack – szimulációs példa: portál](./media/ddos-attack-simulation/ddos-attack-simulation-example-2.png)

## <a name="next-steps"></a>Következő lépések

- Megtudhatja [, hogyan tekintheti meg és konfigurálhatja a DDoS Protection telemetria](telemetry-monitoring-alerting.md).
- Megtudhatja, hogyan [konfigurálhatja a DDOS-támadások enyhítésére vonatkozó jelentéseket és a folyamat naplófájljait](reports-and-flow-logs.md).
- Ismerje meg, hogyan [vehet fel DDoS gyors választ](ddos-rapid-response.md).
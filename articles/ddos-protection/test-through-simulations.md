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
ms.openlocfilehash: e3a665e3615c9ff3a68cf13eeaef5e8f41632f6a
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2021
ms.locfileid: "97900360"
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
    |DDoS-profil | A lehetséges értékek közé tartozik a **DNS-árvíz**, a **NTPv2-árvíz**, az **SSDP-árvíz**, a **TCP syn-árvíz**, az **UDP 64b**-árvíz, az **UDP-128B**, az UDP- **256B** árvíz, az **UDP 512B**-árvíz, az **UDP 1024B**-árvíz, az **UDP 1514B Flood** **, UDP-** **töredezettség**|
    |Teszt mérete       | A lehetséges értékek a következők lehetnek: **100k PPS, 50 Mbps és 4 forrás IP**-cím, **több mint 200.000 PPS, 100 Mbps és 8 forrás IP**-cím, **400K PPS, MB/s és 16 forrás IP**-cím, **800K PPS, 400 Mbps és 32 forrás IP**-címek.                                  |
    |Teszt időtartama | A lehetséges értékek a következők lehetnek: **10 perc**, **15 perc**, **20 perc**, **25 perc**, **30 perc**.|

Ekkor az alábbihoz hasonlónak kell megjelennie:

![DDoS Attack – szimulációs példa: BreakingPoint Cloud](./media/ddos-attack-simulation/ddos-attack-simulation-example-1.png)

## <a name="monitor-and-validate"></a>Figyelés és ellenőrzés

1. Jelentkezzen be, https://portal.azure.com és nyissa meg az előfizetését.
1. Válassza ki azt a nyilvános IP-címet, amelyen a támadást tesztelte.
1. A **Figyelés** területen kattintson a **Metrikák** elemre.
1. A **metrika** beállításnál válassza a _DDoS Attack vagy a not (nem_) lehetőséget.

Ha az erőforrás támadás alatt áll, látnia kell, hogy az érték **0** és **1** között változik, az alábbi képhez hasonlóan:

![DDoS Attack – szimulációs példa: portál](./media/ddos-attack-simulation/ddos-attack-simulation-example-2.png)

### <a name="breakingpoint-cloud-api-script"></a>BreakingPoint Cloud API-szkript

Ezzel az [API-szkripttel](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Breaking%20Point%20SDK) automatizálhatja a DDoS-teszteket, ha egyszer vagy a cron használatával futtatja a normál teszteket. Ez akkor hasznos, ha ellenőrizni szeretné, hogy a naplózás megfelelően van-e konfigurálva, és hogy az észlelési és a reagálási eljárások érvényesek-e A szkriptek Linux operációs rendszerre (Ubuntu 18,04 LTS) és Python 3-ra van szükség. Telepítse az előfeltételeket és az API-ügyfelet a mellékelt parancsfájllal vagy a [BreakingPoint Cloud](http://breakingpoint.cloud/) webhelyén található dokumentáció használatával.

## <a name="next-steps"></a>Következő lépések

- Megtudhatja [, hogyan tekintheti meg és konfigurálhatja a DDoS Protection telemetria](telemetry.md).
- Megtudhatja, hogyan [tekintheti meg és konfigurálhatja a DDoS diagnosztikai naplózást](diagnostic-logging.md).
- Ismerje meg, hogyan [vehet fel DDoS gyors választ](ddos-rapid-response.md).

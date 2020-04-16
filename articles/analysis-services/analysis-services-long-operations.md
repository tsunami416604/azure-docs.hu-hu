---
title: Gyakorlati tanácsok az Azure Analysis Services hosszú ideig futó műveleteihez | Microsoft dokumentumok
description: Ez a cikk a hosszú ideig futó műveletek ajánlott eljárásait ismerteti.
author: minewiskan
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: owend
ms.openlocfilehash: 3f6b2194cc422a827bbc7a15c012173b3f814b52
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428107"
---
# <a name="best-practices-for-long-running-operations"></a>Gyakorlati tanácsok a hosszú ideig futó műveletekhez

Az Azure Analysis Services-ben egy *csomópont* egy olyan gazdavirtuális gépet jelöl, amelyben egy kiszolgálói erőforrás fut. Egyes műveletek, például a hosszú ideig futó lekérdezések, a frissítési műveletek és a lekérdezési horizontális felskálázás sikertelenek lehetnek, ha egy kiszolgálói erőforrás egy másik csomópontra kerül. Ebben a forgatókönyvben gyakori hibaüzenetek a következők:

- "Hiba történt egy hosszú ideig futó XMLA-kérelem keresése közben. Lehet, hogy a kérést a szolgáltatás frissítése vagy a kiszolgáló újraindítása szakította meg."
- A "Model '<guid><database>' azonosítóval rendelkező feladat szolgáltatáshiba (inaktivitás) miatt megszakadt a "Frissítési kérelem megszakítása, mivel frissítés nélkül ragadt". Ez egy belső szolgáltatási probléma. Kérjük, küldje el újra a feladatot, vagy nyújtson be egy jegyet, hogy segítséget kapjon, ha a probléma ismételten előfordul."

Számos oka lehet annak, hogy a hosszú ideig futó műveletek megszakadhatnak. Például az Azure-ban, például: 
- Az operációs rendszer javításai 
- Biztonsági frissítések
- Az Azure Analysis Services szolgáltatásfrissítései
- Service Fabric-frissítések. A Service Fabric egy platformösszetevő, amelyet számos Microsoft felhőszolgáltatás, köztük az Azure Analysis Services használ.

A szolgáltatásban előforduló frissítések mellett a szolgáltatások természetes mozgása van a csomópontok között a terheléselosztás miatt. A csomópontmozgások egy felhőszolgáltatás várható részét képezik. Az Azure Analysis Services megpróbálja minimalizálni a csomópontmozgásokból származó hatásokat, de lehetetlen teljesen kiküszöbölni őket. 

A csomópontmozgások mellett más hibák is előfordulhatnak. Előfordulhat például, hogy egy adatforrás-adatbázis-rendszer offline állapotban van, vagy a hálózati kapcsolat megszakad. Ha a frissítés során egy partíció 10 millió sort tartalmaz, és hiba történik a 9 milliomodik sorban, nincs mód a frissítés újraindítására a hibapont. A szolgáltatásnak az elejétől kell újrakezdenie. 

## <a name="refresh-rest-api"></a>Rest API frissítése

A szolgáltatás megszakítása kihívást jelenthet a hosszú ideig futó műveletek, például az adatfrissítés esetén. Az Azure Analysis Services tartalmaz egy REST API-t, amely segít enyhíteni a szolgáltatás megszakításai ból eredő negatív hatásokat. További információ: [Aszinkron frissítés a REST API-val című témakörben.](analysis-services-async-refresh.md)
 
A REST API mellett más módszerek keltheti a hosszú ideig futó frissítési műveletek során felmerülő problémák minimalizálására is. A fő cél az, hogy ne kelljen újraindítani a frissítési műveletet az elejétől, és ehelyett kisebb kötegekben hajtson végre frissítéseket, amelyek szakaszokban véglegesíthetők. 
 
A REST API lehetővé teszi az ilyen újraindítást, de nem teszi lehetővé a partíció létrehozásának és törlésének teljes rugalmasságát. Ha egy forgatókönyv összetett adatkezelési műveleteket igényel, a megoldásnak tartalmaznia kell valamilyen kötegelési formát a logikájában. Ha például tranzakciókat használ az adatok több ként történő feldolgozásához, a különálló kötegek lehetővé teszik, hogy a hiba ne az elejétől, hanem egy köztes ellenőrzőpontról igényeljen újraindítást. 
 
## <a name="scale-out-query-replicas"></a>Kibővített lekérdezési replikák

Akár REST, akár egyéni logikát használ, az ügyfélalkalmazás-lekérdezések továbbra is inkonzisztens vagy köztes eredményeket adhatnak vissza a kötegek feldolgozása közben. Ha az ügyfélalkalmazás-lekérdezések által visszaadott konzisztens adatokra van szükség a feldolgozás során, és a modelladatok köztes állapotban vannak, [használhatja a horizontális felskálázást](analysis-services-scale-out.md) csak olvasható lekérdezésreplikákkal.

Írásvédett lekérdezési replikák használatával, miközben a frissítések kötegekben vannak végrehajtva, az ügyfélalkalmazás felhasználói továbbra is lekérdezhetik az írásvédett kópiák adatainak régi pillanatképét. A frissítések befejezése után egy Szinkronizálási művelet hajtható végre az írásvédett kópiák naprakészen írása érdekében.


## <a name="next-steps"></a>További lépések

[Aszinkron frissítés a REST API-val](analysis-services-async-refresh.md)  
[Az Azure Analysis Services horizontális felskálázása](analysis-services-scale-out.md)  
[Analysis Services magas rendelkezésre állás](analysis-services-bcdr.md)  
[Az Azure-szolgáltatások újrapróbálkozási útmutatója](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific)   


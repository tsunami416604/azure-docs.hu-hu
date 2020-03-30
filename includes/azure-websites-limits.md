---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 03/04/2020
ms.author: jroth
ms.openlocfilehash: 505e2d8eec20853fba3743b40cbe289585d14d61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78305003"
---
| Erőforrás | Ingyenes | Megosztott | Basic | Standard | Prémium (v2) | Elkülönített </th> |
| --- | --- | --- | --- | --- | --- | --- |
| [Webes, mobil- vagy API-alkalmazások](https://azure.microsoft.com/services/app-service/) [az Azure App Service 1 csomagonként](../articles/app-service/overview-hosting-plans.md)<sup>1</sup> |10 |100 |Korlátlan<sup>2</sup> |Korlátlan<sup>2</sup> |Korlátlan<sup>2</sup> |Korlátlan<sup>2</sup>|
| [App Szolgáltatási csomag](../articles/app-service/overview-hosting-plans.md) |10 régiónként |10 erőforráscsoportonként |100 erőforráscsoportonként |100 erőforráscsoportonként |100 erőforráscsoportonként |100 erőforráscsoportonként|
| Számítási példány típusa |Megosztott |Megosztott |Dedikált<sup>3</sup> |Dedikált<sup>3</sup> |Dedikált<sup>3</sup></p> |Dedikált<sup>3</sup>|
| [Horizontális felskálázás](../articles/app-service/manage-scale-up.md) (maximális példányok) |1 megosztva |1 megosztva |3 dedikált<sup>3</sup> |10 dedikált<sup>3</sup> |30 dedikált<sup>3</sup>|100 dedikált<sup>4</sup>|
| Tárolás<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |250 GB<sup>5</sup></p> |1 TB<sup>5</sup>|
| CPU idő (5 perc)<sup>6</sup> |3 perc |3 perc |Korlátlan, fizessen normál [áron](https://azure.microsoft.com/pricing/details/app-service/)</a> |Korlátlan, fizessen normál [áron](https://azure.microsoft.com/pricing/details/app-service/)</a> |Korlátlan, fizessen normál [áron](https://azure.microsoft.com/pricing/details/app-service/)</a> |Korlátlan, fizessen normál [áron](https://azure.microsoft.com/pricing/details/app-service/)</a>|
| PROCESSZOR idő (nap)<sup>6</sup> |60 perc |240 perc |Korlátlan, fizessen normál [áron](https://azure.microsoft.com/pricing/details/app-service/)</a> |Korlátlan, fizessen normál [áron](https://azure.microsoft.com/pricing/details/app-service/)</a> |Korlátlan, fizessen normál [áron](https://azure.microsoft.com/pricing/details/app-service/)</a> |Korlátlan, fizessen normál [áron](https://azure.microsoft.com/pricing/details/app-service/)</a> |
| Memória (1 óra) |1024 MB/App Service-csomag |1024 MB alkalmazásonként |N/A |N/A |N/A |N/A |
| Sávszélesség |165 MB |Korlátlan, [adatátviteli díjak](https://azure.microsoft.com/pricing/details/data-transfers/) érvényesek |Korlátlan, [adatátviteli díjak](https://azure.microsoft.com/pricing/details/data-transfers/) érvényesek |Korlátlan, [adatátviteli díjak](https://azure.microsoft.com/pricing/details/data-transfers/) érvényesek |Korlátlan, [adatátviteli díjak](https://azure.microsoft.com/pricing/details/data-transfers/) érvényesek |Korlátlan, [adatátviteli díjak](https://azure.microsoft.com/pricing/details/data-transfers/) érvényesek |
| Alkalmazásarchitektúra |32 bites |32 bites |32 bites/64 bites |32 bites/64 bites |32 bites/64 bites |32 bites/64 bites |
| Webszoftver-szoftvercsatornák példányonként<sup>7</sup> |5 |35 |350 |Korlátlan |Korlátlan |Korlátlan |
| IP-kapcsolatok | 600 | 600 | A példány méretétől függ<sup>8</sup> | A példány méretétől függ<sup>8</sup> | A példány méretétől függ<sup>8</sup> | 16000 |
| Egyidejű [hibakereső-kapcsolatok](../articles/app-service/troubleshoot-dotnet-visual-studio.md) alkalmazásonként |1 |1 |1 |5 |5 |5 |
| App Service-tanúsítványok előfizetésenként<sup>9</sup>| Nem támogatott | Nem támogatott |10 |10 |10 |10 |
| Egyéni tartományok alkalmazásonként</a> |0 (csak azurewebsites.net altartomány)|500 |500 |500 |500 |500 |
| Egyéni tartomány [SSL-támogatása](../articles/app-service/configure-ssl-certificate.md) |Nem támogatott, a *.azurewebsites.net helyettesítő tanúsítványa alapértelmezés szerint elérhető|Nem támogatott, a *.azurewebsites.net helyettesítő tanúsítványa alapértelmezés szerint elérhető|Korlátlan SNI SSL-kapcsolatok |Korlátlan SNI SSL és 1 IP SSL kapcsolat |Korlátlan SNI SSL és 1 IP SSL kapcsolat | Korlátlan SNI SSL és 1 IP SSL kapcsolat|
| Hibrid kapcsolatok tervenként | | | 5 | 25 | 200 | 200 |
| Integrált terheléselosztó | |X |X |X |X |X<sup>10</sup> |
| [Mindig bekapcsolva](../articles/app-service/configure-common.md) | | |X |X |X |X |
| [Ütemezett biztonsági mentések](../articles/app-service/manage-backup.md) | | | | Ütemezett biztonsági mentések 2 óránként, legfeljebb 12 biztonsági mentés naponta (manuális + ütemezett) | Ütemezett biztonsági mentések óránként, legfeljebb 50 biztonsági mentés naponta (kézi + ütemezett) | Ütemezett biztonsági mentések óránként, legfeljebb 50 biztonsági mentés naponta (kézi + ütemezett) |
| [Automatikus méretezés](../articles/app-service/manage-scale-up.md) | | | |X |X |X |
| [WebJobs](../articles/app-service/webjobs-create.md)<sup>11</sup> |X |X |X |X |X |X |
| [Végpontfigyelés](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| [Átmeneti bővítőhelyek](../articles/app-service/deploy-staging-slots.md) alkalmazásonként| | | |5 |20 |20 |
| SLA | |  |99.95%|99.95%|99.95%|99.95%|  

<sup>1 1</sup> Az alkalmazások és a tárolási kvóták alkalmazáscsomagonként vannak, hanincs másképp feltüntetve.  
<sup>2.</sup> Az ezeken a gépeken üzemeltethető alkalmazások tényleges száma az alkalmazások tevékenységétektől, a géppéldányok méretétől és a megfelelő erőforrás-kihasználtságtól függ.  
<sup>3. 20 0</sup> A dedikált példányok különböző méretűek lehetnek. További információ: [App Service-díjszabás](https://azure.microsoft.com/pricing/details/app-service/).  
<sup>4.</sup> Kérésre további akta is megengedett.  
<sup>5.</sup> A tárolási korlát az azonos alkalmazásszolgáltatási csomagban lévő összes alkalmazás teljes tartalommérete. Az összes alkalmazás teljes tartalommérete egyetlen erőforráscsoportban és régióban az összes alkalmazáscsomagban nem haladhatja meg az 500 GB-ot.  
<sup>6.</sup> Ezeket az erőforrásokat a dedikált példányok fizikai erőforrásai korlátozzák (a példány mérete és a példányok száma).  
<sup>7.</sup> Ha az alapszintű rétegben egy alkalmazást két példányra skáláz, akkor a két példány mindegyikéhez 350 egyidejű kapcsolattal rendelkezik. Standard szintű és újabb szintű, nincsenek elméleti korlátok web sockets, de más tényezők korlátozhatják a webes szoftvercsatornák száma. Például a maximális egyidejű kérelmek engedélyezett `maxConcurrentRequestsPerCpu`(által meghatározott): 7500 kis virtuális gép, 15 000 közepes virtuális gép (7500 x 2 mag), és 75 000 nagy virtuális gép (18750 x 4 mag).  
<sup>8.</sup> A maximális IP-kapcsolatok példányonként, és függ a példány mérete: 1920/B1/S1/P1V2 példány, 3968 per B2/S2/P2V2 példány, 8064 per B3/S3/P3V2 példány.  
<sup>9.</sup> Az App Service-tanúsítvány kvótakorlát előfizetésenként támogatási kérelemmel legfeljebb 200-ra növelhető.  
<sup>10.</sup> Az App Service-alapú skus-ok belső terheléselosztással (ILB) az Azure Load Balancer segítségével is használhatók, így nincs nyilvános kapcsolat az internetről. Ennek eredményeképpen az ILB-elkülönítésű appszolgáltatás egyes funkcióit olyan gépekről kell használni, amelyek közvetlen hozzáféréssel rendelkeznek az ILB-hálózati végponthoz.  
<sup>11.</sup> Egyéni végrehajtható fájlok és/vagy parancsfájlok futtatása igény szerint, ütemezés szerint vagy folyamatosan háttérfeladatként az App Service-példányon belül. A folyamatos WebJobs-végrehajtáshoz mindig be van kapcsolva. Nincs előre meghatározott korlát az App Service-példányban futtatható WebJobs-ok számában. Vannak gyakorlati korlátok, amelyek attól függenek, hogy az alkalmazáskód mit próbál tenni.  

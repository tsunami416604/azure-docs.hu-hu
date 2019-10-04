---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 08/09/2019
ms.author: jroth
ms.openlocfilehash: 8cdfe82888cca471aa5aae6717a16f62000c4ad5
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71839206"
---
| Resource | Free | Megosztott | Alapszintű | Standard | Prémium (v2) | Izolált </th> |
| --- | --- | --- | --- | --- | --- | --- |
| [Web-, mobil-vagy API-alkalmazások](https://azure.microsoft.com/services/app-service/) [Azure app Service](../articles/app-service/overview-hosting-plans.md)<sup>1</sup> . csomag |10 |100 |Korlátlan<sup>2</sup> |Korlátlan<sup>2</sup> |Korlátlan<sup>2</sup> |Korlátlan<sup>2</sup>|
| [App Service-csomag](../articles/app-service/overview-hosting-plans.md) |10 régiónként |10/erőforráscsoport |100/erőforráscsoport |100/erőforráscsoport |100/erőforráscsoport |100/erőforráscsoport|
| Számítási példány típusa |Megosztott |Megosztott |<sup>3</sup> . dedikált |<sup>3</sup> . dedikált |<sup>3</sup> . dedikált</p> |<sup>3</sup> . dedikált|
| Vertikális [felskálázás](../articles/app-service/manage-scale-up.md) (példányok maximális száma) |1 megosztott |1 megosztott |3 dedikált<sup>3</sup> |10 dedikált<sup>3</sup> |20 dedikált<sup>3</sup>|100 dedikált<sup>4</sup>|
| Storage<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |250 GB<sup>5</sup></p> |1 TB<sup>5</sup>|
| CPU-idő (5 perc)<sup>6</sup> |3 perc |3 perc |Korlátlan, standard [díjszabás](https://azure.microsoft.com/pricing/details/app-service/) szerint fizetve</a> |Korlátlan, standard [díjszabás](https://azure.microsoft.com/pricing/details/app-service/) szerint fizetve</a> |Korlátlan, standard [díjszabás](https://azure.microsoft.com/pricing/details/app-service/) szerint fizetve</a> |Korlátlan, standard [díjszabás](https://azure.microsoft.com/pricing/details/app-service/) szerint fizetve</a>|
| CPU-idő (nap)<sup>6</sup> |60 perc |240 perc |Korlátlan, standard [díjszabás](https://azure.microsoft.com/pricing/details/app-service/) szerint fizetve</a> |Korlátlan, standard [díjszabás](https://azure.microsoft.com/pricing/details/app-service/) szerint fizetve</a> |Korlátlan, standard [díjszabás](https://azure.microsoft.com/pricing/details/app-service/) szerint fizetve</a> |Korlátlan, standard [díjszabás](https://azure.microsoft.com/pricing/details/app-service/) szerint fizetve</a> |
| Memória (1 óra) |1 024 MB/App Service csomag |1 024 MB/alkalmazás |– |N/A |N/A |– |
| A sávszélesség |165 MB |Korlátlan [adatátviteli díjszabás](https://azure.microsoft.com/pricing/details/data-transfers/) érvényes |Korlátlan [adatátviteli díjszabás](https://azure.microsoft.com/pricing/details/data-transfers/) érvényes |Korlátlan [adatátviteli díjszabás](https://azure.microsoft.com/pricing/details/data-transfers/) érvényes |Korlátlan [adatátviteli díjszabás](https://azure.microsoft.com/pricing/details/data-transfers/) érvényes |Korlátlan [adatátviteli díjszabás](https://azure.microsoft.com/pricing/details/data-transfers/) érvényes |
| Alkalmazásarchitektúra |32 bites |32 bites |32 bites/64 bites |32 bites/64 bites |32 bites/64 bites |32 bites/64 bites |
| Web Sockets/instance<sup>7</sup> |5 |35 |350 |Korlátlan |Korlátlan |Korlátlan |
| Egyidejű [hibakereső kapcsolatok](../articles/app-service/troubleshoot-dotnet-visual-studio.md) alkalmazása |1 |1 |1 |5 |5 |5 |
| Tanúsítványok App Service<sup>10</sup> /előfizetés esetén| Nem támogatott | Nem támogatott |10 |10 |10 |10 |
| Egyéni tartományok/alkalmazás</a> |0 (azurewebsites.net subdomain only)|500 |500 |500 |500 |500 |
| Egyéni tartomány [SSL-támogatása](../articles/app-service/app-service-web-tutorial-custom-ssl.md) |Nem támogatott, a (*. azurewebsites.net) helyettesítő tanúsítványa alapértelmezés szerint elérhető|Nem támogatott, a (*. azurewebsites.net) helyettesítő tanúsítványa alapértelmezés szerint elérhető|Korlátlan SNI SSL kapcsolatok |Korlátlan számú SNI SSL és 1 IP SSL kapcsolat található |Korlátlan számú SNI SSL és 1 IP SSL kapcsolat található | Korlátlan számú SNI SSL és 1 IP SSL kapcsolat található|
| Integrált Load Balancer | |X |X |X |X |X<sup>9</sup> |
| [Always on](../articles/app-service/configure-common.md) | | |X |X |X |X |
| [Ütemezett biztonsági mentések](../articles/app-service/manage-backup.md) | | | | Ütemezett biztonsági mentések 2 óránként, legfeljebb 12 biztonsági mentés naponta (manuális + ütemezett) | Ütemezett biztonsági mentések óránként, legfeljebb 50 biztonsági mentéssel (manuális + ütemezett) | Ütemezett biztonsági mentések óránként, legfeljebb 50 biztonsági mentéssel (manuális + ütemezett) |
| [Automatikus méretezés](../articles/app-service/manage-scale-up.md) | | | |X |X |X |
| [WebJobs](../articles/app-service/webjobs-create.md)<sup>8</sup> |X |X |X |X |X |X |
| [Azure Scheduler](https://azure.microsoft.com/services/scheduler/) -támogatás | |X |X |X |X |X |
| [Végpontmonitoring](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| [Átmeneti tárolóhelyek](../articles/app-service/deploy-staging-slots.md) | | | |5 |20 |20 |
| SLA | |  |99.95%|99.95%|99.95%|99.95%|  

<sup>1</sup> Az alkalmazások és a tárolási kvóták App Service csomaggal rendelkeznek, hacsak másként nincs jelezve.  
<sup>2</sup> Az ezeken a gépeken üzemeltethető alkalmazások tényleges száma az alkalmazások tevékenységéből, a gépi példányok méretétől és a megfelelő erőforrás-felhasználástól függ.  
<sup>3</sup> A dedikált példányok különböző méretűek lehetnek. További információ: [app Service díjszabása](https://azure.microsoft.com/pricing/details/app-service/).  
<sup>4</sup> További kérések esetén engedélyezett.  
<sup>5</sup> A tárolási korlát a teljes tartalom mérete az adott app Service-csomagban lévő összes alkalmazásban. Az egyetlen erőforráscsoport és régió összes app Service-csomagjában lévő alkalmazások teljes tartalmának mérete nem haladhatja meg az 500 GB-ot.  
<sup>6</sup> Ezeket az erőforrásokat a dedikált példányokon található fizikai erőforrások korlátozzák (a példány mérete és a példányok száma).  
<sup>7</sup> Ha az alkalmazást az alapszintű csomagból két példányra méretezi, akkor a két példány esetében 350 egyidejű kapcsolat. A standard szinthez és a fentiekhez nem tartoznak elméleti korlátok a webes szoftvercsatornák számára, más tényezők pedig korlátozhatják a webes szoftvercsatornák számát. Például a maximálisan megengedett kérelmek (a által `maxConcurrentRequestsPerCpu`definiált) a következők: 7 500/kisméretű VM, 15 000/közepes méretű virtuális gép (7 500 x 2 mag) és 75 000/nagyméretű virtuális gép (18 750 x 4 mag).  
<sup>8</sup> Az egyéni végrehajtható fájlokat és/vagy parancsfájlokat igény szerint, ütemezés szerint, vagy a App Service-példányon belüli háttérbeli feladatként futtathatja. A folyamatos WebJobs-végrehajtáshoz folyamatos üzem szükséges. Az ütemezett WebJobs-végrehajtás csak az Azure Scheduler Ingyenes vagy Standard rétegével használható. Nincs előre meghatározott korlát a App Service-példányban futtatható webjobs-feladatok számánál. Az alkalmazás kódjának működéséhez szükséges gyakorlati korlátozások is megváltoznak.  
<sup>9</sup> A izolált App Service SKU-k belsőleg terheléselosztást (ILB) is használhatnak a Azure Load Balancer, így nincs nyilvános kapcsolat az internetről. Ezért a belső terheléselosztású izolált App Service egyes funkcióit csak olyan gépekről lehet elérni, amelyek közvetlen hozzáféréssel rendelkeznek az ILB-hálózat végpontjához.  
<sup>10</sup> Az előfizetések App Service-tanúsítvány kvóta-korlátja egy támogatási kérelem alapján növelhető, amely legfeljebb 200 lehet.  
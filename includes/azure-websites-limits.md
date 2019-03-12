---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: c2db5740d6373c8f6be5dabe0260fe7f99575641
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553773"
---
| Erőforrás | Ingyenes | Közös | Alapszintű | Standard | Prémium szintű (v2) | Izolált </th> |
| --- | --- | --- | --- | --- | --- | --- |
| [Webes, mobil-, és API apps](https://azure.microsoft.com/services/app-service/) kiszolgálónként [Azure App Service-csomag](../articles/app-service/overview-hosting-plans.md)<sup>1</sup> |10 |100 |Korlátlan számú<sup>2</sup> |Korlátlan számú<sup>2</sup> |Korlátlan számú<sup>2</sup> |Korlátlan számú<sup>2</sup>|
| [App Service-csomag](../articles/app-service/overview-hosting-plans.md) |10 régiónként |az adott erőforráscsoport esetében 10 |az adott erőforráscsoport esetében 100 |az adott erőforráscsoport esetében 100 |az adott erőforráscsoport esetében 100 |az adott erőforráscsoport esetében 100|
| Számítási példány típusa |Közös |Közös |Dedikált<sup>3</sup> |Dedikált<sup>3</sup> |Dedikált<sup>3</sup></p> |Dedikált<sup>3</sup>|
| [Horizontális felskálázás](../articles/app-service/web-sites-scale.md) (példányok maximális száma) |a megosztott 1 |a megosztott 1 |3 dedikált<sup>3</sup> |dedikált 10<sup>3</sup> |dedikált 20<sup>3</sup>|dedikált 100<sup>4</sup>|
| Storage<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |250 GB<sup>5</sup></p> |1 TB<sup>5</sup>|
| CPU-idő (5 perc)<sup>6</sup> |3 perc |3 perc |Azért kell fizetnie, a standard szintű, korlátlan [díjszabása](https://azure.microsoft.com/pricing/details/app-service/)</a> |Azért kell fizetnie, a standard szintű, korlátlan [díjszabása](https://azure.microsoft.com/pricing/details/app-service/)</a> |Azért kell fizetnie, a standard szintű, korlátlan [díjszabása](https://azure.microsoft.com/pricing/details/app-service/)</a> |Azért kell fizetnie, a standard szintű, korlátlan [díjszabása](https://azure.microsoft.com/pricing/details/app-service/)</a>|
| Processzoridő (nap)<sup>6</sup> |60 perc |240 perc |Azért kell fizetnie, a standard szintű, korlátlan [díjszabása](https://azure.microsoft.com/pricing/details/app-service/)</a> |Azért kell fizetnie, a standard szintű, korlátlan [díjszabása](https://azure.microsoft.com/pricing/details/app-service/)</a> |Azért kell fizetnie, a standard szintű, korlátlan [díjszabása](https://azure.microsoft.com/pricing/details/app-service/)</a> |Azért kell fizetnie, a standard szintű, korlátlan [díjszabása](https://azure.microsoft.com/pricing/details/app-service/)</a> |
| Memória (1 óra) |App Service-csomag 1 024 MB |Az alkalmazásonkénti 1 024 MB |– |N/A |N/A |– |
| Bandwidth |165 MB |Korlátlan, [adatátviteli díjak](https://azure.microsoft.com/pricing/details/data-transfers/) alkalmazása |Korlátlan, [adatátviteli díjak](https://azure.microsoft.com/pricing/details/data-transfers/) alkalmazása |Korlátlan, [adatátviteli díjak](https://azure.microsoft.com/pricing/details/data-transfers/) alkalmazása |Korlátlan, [adatátviteli díjak](https://azure.microsoft.com/pricing/details/data-transfers/) alkalmazása |Korlátlan, [adatátviteli díjak](https://azure.microsoft.com/pricing/details/data-transfers/) alkalmazása |
| Alkalmazásarchitektúra |32 bites |32 bites |32 bites vagy 64 bites |32 bites vagy 64 bites |32 bites vagy 64 bites |32 bites vagy 64 bites |
| Webes szoftvercsatornák példányonként<sup>7</sup> |5 |35 |350 |Korlátlan |Korlátlan |Korlátlan |
| Egyidejű [ladicího programu kapcsolatok](../articles/app-service/troubleshoot-dotnet-visual-studio.md) alkalmazásonként |1 |1 |1 |5 |5 |5 |
| App Service-tanúsítványok száma előfizetésenként<sup>10</sup>| Nem támogatott | Nem támogatott |10 |10 |10 |10 |
| Egyéni tartományok alkalmazásonként</a> |0 (azurewebsites.net subdomain only)|500 |500 |500 |500 |500 |
| Egyéni tartomány [SSL-támogatás](../articles/app-service/app-service-web-tutorial-custom-ssl.md) |Nem támogatott, a helyettesítő tanúsítványt *. azurewebsites.net alapértelmezés szerint elérhető|Nem támogatott, a helyettesítő tanúsítványt *. azurewebsites.net alapértelmezés szerint elérhető|Korlátlan számú SNI SSL-kapcsolatok |Korlátlan számú SNI SSL és 1 IP SSL-kapcsolatot tartalmaz |Korlátlan számú SNI SSL és 1 IP SSL-kapcsolatot tartalmaz | Korlátlan számú SNI SSL és 1 IP SSL-kapcsolatot tartalmaz|
| Integrált load balancer | |X |X |X |X |X<sup>9</sup> |
| [Always On](../articles/app-service/web-sites-configure.md) | | |X |X |X |X |
| [Ütemezett biztonsági mentések](../articles/app-service/manage-backup.md) | | | | Ütemezett biztonsági mentések 2 óránként legfeljebb 12 biztonsági másolat (manuális + ütemezett) naponta | Ütemezett biztonsági mentések óránként legfeljebb 50 biztonsági másolat (manuális + ütemezett) naponta | Ütemezett biztonsági mentések óránként legfeljebb 50 biztonsági másolat (manuális + ütemezett) naponta |
| [Automatikus méretezés](../articles/app-service/web-sites-scale.md) | | | |X |X |X |
| [WebJobs](../articles/app-service/webjobs-create.md)<sup>8</sup> |X |X |X |X |X |X |
| [Az Azure Scheduler](https://azure.microsoft.com/services/scheduler/) támogatása | |X |X |X |X |X |
| [Végpontmonitoring](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| [előkészítési pontok](../articles/app-service/deploy-staging-slots.md) | | | |5 |20 |20 |
| SLA | |  |99,9% |99.95%|99.95%|99.95%|  

<sup>1</sup>alkalmazások és a tárolási kvótákat van egy App Service-csomag hacsak másként nem jelezzük.  
<sup>2</sup>alkalmazásokat, amelyek az ezeken a gépeken üzemeltetheti tényleges számát a tevékenység az alkalmazások, a gép példány és a megfelelő erőforrás-használat méretétől függ.  
<sup>3</sup>dedikált példányok különböző méretű lehet. További információkért lásd: [App Service díjszabását](https://azure.microsoft.com/pricing/details/app-service/).  
<sup>4</sup>több kérés esetén engedélyezett.  
<sup>5</sup>az App Service-csomag a megadott tárolási kapacitása az összes alkalmazása tekintetében a teljes tartalom mérete.  
<sup>6</sup>ezek előrébb által (a példány mérete és példányok) dedikált példányokon fizikai erőforrásokat.  
<sup>7</sup>Ha egy alkalmazás két példányok alapszintű csomag, 350 egyidejű kapcsolatok az egyes a két példánnyal rendelkezik.  
<sup>8</sup>egyéni végrehajtható fájlok és/vagy parancsprogramok futtatása igény szerint, ütemezve vagy folyamatosan, az App Service háttérfeladatként. A folyamatos WebJobs-végrehajtáshoz folyamatos üzem szükséges. Az ütemezett WebJobs-végrehajtás csak az Azure Scheduler Ingyenes vagy Standard rétegével használható. Nincs előre meghatározott korlátozva a webjobs-feladatok, amely képes futni az App Service-példányok számát. Mi az alkalmazáskód próbál végezni függő gyakorlati korlátozva van.  
<sup>9</sup>app Service izolált változatában elérhető a belső terheléselosztás terheléselosztásos (ILB) rendelkező Azure Load Balancer, így nincs az internetről származó nyilvános kapcsolat lehetővé teszi. Ezért a belső terheléselosztású izolált App Service egyes funkcióit csak olyan gépekről lehet elérni, amelyek közvetlen hozzáféréssel rendelkeznek az ILB-hálózat végpontjához.  
<sup>10</sup>az App Service-tanúsítvány kvóta korlátja előfizetésenként legfeljebb 200 támogatási kérelmet keresztül növelhető.  
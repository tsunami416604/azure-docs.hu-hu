---
title: Az Azure felhőszolgáltatásokra hatást gyakorló Azure-szolgáltatászavar kezelése
titleSuffix: Azure Cloud Services
description: Ismerje meg, mi a teendő az Azure felhőszolgáltatásokra hatást gyakorló Azure-szolgáltatáskimaradás esetén.
services: cloud-services
documentationcenter: ''
author: mmccrory
ms.service: cloud-services
ms.workload: cloud-services
ms.topic: article
ms.date: 04/04/2017
ms.author: memccror
ms.openlocfilehash: e3f0fd88eb302dac208f43d0622ae28b31dcddc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77157506"
---
# <a name="what-to-do-in-the-event-of-an-azure-service-disruption-that-impacts-azure-cloud-services"></a>Mi a teendő az Azure Cloud Servicest befolyásoló Azure szolgáltatás kiesése esetén?
A Microsoft nál keményen dolgozunk azon, hogy szolgáltatásaink mindig elérhetők legyenek az Ön számára, amikor szüksége van rájuk. A rajtunk kívül álló erők néha olyan módon hatnak ránk, amely nem tervezett szolgáltatászavarokat okoz.

A Microsoft szolgáltatásiszint-szerződést (SLA) biztosít a szolgáltatásaihoz, mint az üzemidő és a kapcsolat kötelezettségvállalását. Az egyes Azure-szolgáltatások SLA-ja az [Azure szolgáltatásiszint-szerződésekkel](https://azure.microsoft.com/support/legal/sla/)érhető el.

Az Azure már számos beépített platformfunkcióval rendelkezik, amelyek támogatják a magas rendelkezésre állású alkalmazásokat. Ezekről a szolgáltatásokról további információért olvassa el [a vészhelyreállítást és az Azure-alkalmazások magas rendelkezésre állását.](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)

Ez a cikk egy valódi vész-helyreállítási forgatókönyvet ismerteti, amikor egy egész régió súlyos természeti katasztrófa vagy a szolgáltatás széles körű megszakítása miatt kimaradást tapasztal. Ezek ritka előfordulások, de fel kell készülnie arra a lehetőségre, hogy egy egész régió kimaradása van. Ha egy teljes régió szolgáltatáskimaradást tapasztal, az adatok helyileg redundáns másolatai átmenetileg nem érhetők el. Ha engedélyezte a georeplikációt, az Azure Storage-blobok és -táblák további három példányát egy másik régióban tárolja. Abban az esetben, ha egy teljes regionális kimaradás vagy katasztrófa, amelyben az elsődleges régió nem helyreállítható, az Azure újraleképezi az összes DNS-bejegyzéseket a georeplikált régióba.

> [!NOTE]
> Ne feledje, hogy nincs semmilyen ellenőrzést a folyamat felett, és ez csak akkor fordul elő, az adatközpont-szintű szolgáltatás kimaradások. Emiatt a legmagasabb szintű rendelkezésre állás eléréséhez más alkalmazásspecifikus biztonsági mentési stratégiákra is támaszkodnia kell. További információt a [Vészhelyreállítás és a Microsoft Azure-ra épülő alkalmazások magas rendelkezésre állása című témakörben talál.](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md) Ha szeretné, hogy hatással legyen a saját feladatátvétel, érdemes megfontolni az [olvasási hozzáférésű georedundáns tárolás (RA-GRS),](../storage/common/storage-redundancy.md)amely létrehoz egy írásvédett másolatot az adatok egy másik régióban.
>
>


## <a name="option-1-use-a-backup-deployment-through-azure-traffic-manager"></a>1. lehetőség: Biztonsági mentési telepítés használata az Azure Traffic Manageren keresztül
A legrobusztusabb vész-helyreállítási megoldás magában foglalja az alkalmazás több központi telepítésének karbantartása a különböző régiókban, majd az [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) használatával irányítja a forgalmat közöttük. Az Azure Traffic Manager több [útválasztási módszert](../traffic-manager/traffic-manager-routing-methods.md)biztosít, így kiválaszthatja, hogy elsődleges/biztonsági mentési modell használatával kezelje-e a központi telepítéseket, vagy felosztja a forgalmat közöttük.

![Az Azure Cloud Services régiók közötti kiegyensúlyozása az Azure Traffic Manager rel](./media/cloud-services-disaster-recovery-guidance/using-azure-traffic-manager.png)

A régió elvesztésére adott leggyorsabb válasz érdekében fontos, hogy konfigurálja a Traffic Manager [végpontfigyelését.](../traffic-manager/traffic-manager-monitoring.md)

## <a name="option-2-deploy-your-application-to-a-new-region"></a>2. lehetőség: Az alkalmazás telepítése új régióba
Több aktív központi telepítés karbantartása az előző beállításban leírtak szerint további folyamatos költségekkel jár. Ha a helyreállítási idő célkitűzés (RTO) elég rugalmas, és rendelkezik az eredeti kódot, vagy lefordított Cloud Services-csomag, létrehozhat egy új példányt az alkalmazás egy másik régióban, és frissítse a DNS-rekordokat, hogy pont az új központi telepítés.

A felhőalapú szolgáltatásalkalmazások létrehozásáról és üzembe helyezéséről további részleteket [a Felhőszolgáltatás létrehozása és üzembe helyezése](cloud-services-how-to-create-deploy-portal.md)című témakörben tartalmaz.

Az alkalmazás adatforrásaitól függően előfordulhat, hogy ellenőriznie kell az alkalmazás adatforrásának helyreállítási eljárásait.

* Az Azure Storage-adatforrások, tekintse meg az [Azure Storage redundancia](../storage/common/storage-redundancy.md) az alkalmazás hoz kiválasztott redundanciamodell alapján elérhető lehetőségek ellenőrzése.
* SQL Database-források esetén olvassa el [az Áttekintés: Felhőbeli üzletmenet-folytonosság és az SQL Database-lel való adatbázis-vészhelyreállítás](../sql-database/sql-database-business-continuity.md) című témakört az alkalmazás kiválasztott replikációs modellje alapján elérhető beállítások ellenőrzéséhez.


## <a name="option-3-wait-for-recovery"></a>3. lehetőség: Várakozás a helyreállításra
Ebben az esetben nincs szükség műveletre az Ön részéről, de a szolgáltatás nem lesz elérhető, amíg a régió helyre nem áll. Az aktuális szolgáltatásállapotát az [Azure Service Health Dashboard (Az Azure Service Health Dashboard) láthatja.](https://azure.microsoft.com/status/)

## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni avész-helyreállítási és magas rendelkezésre állási stratégia megvalósításáról, olvassa el a [Vész-helyreállítási és az Azure-alkalmazások magas rendelkezésre állású témakört.](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)

A felhőplatform képességeinek részletes technikai megértéséhez olvassa el [az Azure rugalmassági technikai útmutatóját.](/azure/architecture/checklist/resiliency-per-service)
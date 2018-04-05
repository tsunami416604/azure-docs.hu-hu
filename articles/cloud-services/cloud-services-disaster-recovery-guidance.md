---
title: Mi a teendő, ha az Azure szolgáltatás, amely hatással van az Azure Felhőszolgáltatások megszűnésének |} Microsoft Docs
description: Ismerje meg, mi a teendő, hatással van az Azure Cloud Services Azure-szolgáltatások becsukódjon.
services: cloud-services
documentationcenter: ''
author: mmccrory
manager: timlt
editor: ''
ms.assetid: e52634ab-003d-4f1e-85fa-794f6cd12ce4
ms.service: cloud-services
ms.workload: cloud-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: mmccrory
ms.openlocfilehash: 7028417c95aa6969793c00d0bb270c96e56164fb
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="what-to-do-in-the-event-of-an-azure-service-disruption-that-impacts-azure-cloud-services"></a>Mi a teendő, ha az Azure szolgáltatás megszűnésének, amely hatással van az Azure Cloud Services csomag
A Microsoft dolgozunk merevlemez győződjön meg arról, hogy a szolgáltatások mindig rendelkezésre álló újból. Kényszeríti a befolyásolható néha hatással velünk, hogy a nem tervezett szolgáltatás megszakadása miatt.

A Microsoft egy szolgáltatási szint szerződés (SLA) biztosít hasznos üzemidő és csatlakozási kötelezettségvállalást a hozzá tartozó szolgáltatások. Az SLA-t az egyes Azure-szolgáltatások található [Azure szolgáltatói szerződések](https://azure.microsoft.com/support/legal/sla/).

Azure már számos beépített platform szolgáltatást, amely támogatja a magas rendelkezésre állású alkalmazások. További információk a szolgáltatásokról, olvassa el a [vész-helyreállítási és magas rendelkezésre állás a Azure-alkalmazások](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Ez a cikk egy valódi katasztrófa utáni helyreállítása vonatkozik, amikor egy teljes régió kimaradás jelentős természeti katasztrófa vagy a széles körű szolgáltatás megszakítás miatt. Ezek ritkán fordul elő eseményeket, de előbb elő kell készítenie az, hogy van-e egy teljes terület kimaradás lehetőséget. Ha egy teljes terület a szolgáltatás szüneteltetése, a helyileg redundáns másolatait az adatok akkor ideiglenesen nem érhető el. Ha engedélyezett a georeplikáció, az Azure Storage blobs és táblák három további másolatot tárol egy másik régióban. Teljes regionális kimaradás vagy az elsődleges régióban nincs helyreállítható katasztrófa esetén, Azure amelyek összes DNS-bejegyzéseket, a georeplikált régióban.

> [!NOTE]
> Vegye figyelembe, hogy nem tudja befolyásolni bármely ezt a folyamatot, és csak megtörténik az Adatközpont kiterjedő szolgáltatás üzemzavarokhoz vezethet. Ebből kifolyólag kell is használ, más alkalmazás-specifikus biztonsági stratégiák a legmagasabb rendelkezésre állásának eléréséhez. További információkért lásd: [vész-helyreállítási és magas rendelkezésre állás a Microsoft Azure épülő alkalmazások](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md). Ha szeretné tudni a saját feladatátvételi hatással, érdemes használni a [írásvédett georedundáns tárolás (RA-GRS)](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage), amely az adatok írásvédett másolatot készít egy másik régióban.
>
>


## <a name="option-1-use-a-backup-deployment-through-azure-traffic-manager"></a>1. lehetőség: A biztonsági mentési központi telepítés keresztül Azure Traffic Manager használata
A legtöbb hatékony vész-helyreállítási megoldást magában foglalja a karbantartása több különböző régiókban az alkalmazás központi telepítését, majd használatával [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) át tudja irányítani a forgalmat közöttük. Az Azure Traffic Manager biztosít több [útválasztási metódusait](../traffic-manager/traffic-manager-routing-methods.md), így dönthet úgy, hogy az elsődleges, illetve biztonsági mentési modell segítségével központi telepítések felügyeletéhez szükséges, vagy ossza fel a forgalom közöttük.

![Azure Cloud Services az Azure Traffic Manager régiók közötti terheléselosztás](./media/cloud-services-disaster-recovery-guidance/using-azure-traffic-manager.png)

A leggyorsabb válasz egy régiót elvész, fontos, hogy konfigurálja a Traffic Manager [végpontmonitoring kijelző](../traffic-manager/traffic-manager-monitoring.md).

## <a name="option-2-deploy-your-application-to-a-new-region"></a>2. lehetőség: Az alkalmazás egy új régióban üzembe helyezése
Több aktív központi telepítés fenntartása, lásd: az előző beállítás azt eredményezi, a folyamatban lévő többletköltséggel azok háromszorosa. Ha a helyreállítási idő célkitűzése (RTO) elég rugalmas, és az eredeti kódja vagy lefordított Cloud Services csomag van, az az alkalmazás egy új példányának létrehozása egy másik régióban, és frissítse a DNS-rekordokat úgy, hogy az új központi telepítés mutasson.

Egy felhőalapú szolgáltatás alkalmazás létrehozását és telepítését kapcsolatos további részletekért lásd: [létrehozásáról és központi telepítése egy felhőalapú szolgáltatás](cloud-services-how-to-create-deploy-portal.md).

Attól függően, hogy az alkalmazás-adatforrások szükség lehet az alkalmazási adatforrás esetében a helyreállítási eljárások ellenőrzése.

* Az Azure Storage adatforrásokat, lásd: [Azure Storage replikációs](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) ellenőrizni a rendelkezésre álló lehetőségek az alkalmazáshoz kiválasztott replikációs minta alapján.
* SQL-adatbázis adatforrások, olvassa el a [áttekintése: üzleti folytonossági és az adatbázis katasztrófa utáni helyreállítás az SQL Database Felhőbeli](../sql-database/sql-database-business-continuity.md) ellenőrizni a rendelkezésre álló lehetőségek alapján az alkalmazás a választott replikációs modell.


## <a name="option-3-wait-for-recovery"></a>3. lehetőség: Várjon, amíg a helyreállítás
Ebben az esetben nincs teendő beavatkozást, de a szolgáltatás nem lesz elérhető a régió visszaállításáig. A szolgáltatás jelenlegi állapota tekintheti meg a [Azure az állapotjelző irányítópulthoz](https://azure.microsoft.com/status/).

## <a name="next-steps"></a>További lépések
A vész-helyreállítási és a magas rendelkezésre állási stratégiájának megvalósításához kapcsolatos további információkért lásd: [vész-helyreállítási és magas rendelkezésre állás a Azure-alkalmazások](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

A felhő platform képességei részletes műszaki megértése elkészítéséhez lásd [Azure rugalmassági műszaki útmutatót](../resiliency/resiliency-technical-guidance.md).
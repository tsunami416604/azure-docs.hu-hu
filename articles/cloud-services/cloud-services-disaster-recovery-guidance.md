---
title: Mi a teendő, ha meghibásodik egy Azure-szolgáltatás megszakadása, ami hatással van az Azure Cloud Servicesben |} A Microsoft Docs
description: Ismerje meg, mi a teendő az Azure szolgáltatáskimaradás, amely hatással van az Azure Cloud Services működésében.
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
ms.author: memccror
ms.openlocfilehash: 976bb43fd3e6d6fdb19c733affd4afa2e49e482c
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2019
ms.locfileid: "65967685"
---
# <a name="what-to-do-in-the-event-of-an-azure-service-disruption-that-impacts-azure-cloud-services"></a>Mi a teendő, ha meghibásodik egy Azure-szolgáltatás megszakadása, ami hatással van az Azure Cloud Services
A Microsoft dolgozunk, hogy szolgáltatásaink mindig elérhető, amikor szüksége van rájuk. Kényszeríti a befolyásolható néha hatással velünk a kapcsolatot, hogy a nem tervezett szolgáltatáskimaradás miatt.

A Microsoft szolgáltatói szerződés (SLA) biztosít a szolgáltatások üzemidejével és elérhetőségével vonatkozó kötelezettségvállalást. A szolgáltatói szerződés tartalmazza az egyes Azure-szolgáltatások található [Azure szolgáltatásiszint-szerződései](https://azure.microsoft.com/support/legal/sla/).

Az Azure már számos beépített platformfunkciók támogatja a magas rendelkezésre állású alkalmazások. Ezek a szolgáltatások kapcsolatos további információkért olvassa el a [vészhelyreállítás és magas rendelkezésre állás az Azure-alkalmazások](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Ez a cikk ismerteti a valódi vész-helyreállítási helyzetekre, amikor egy teljes régió kimaradás jelentős természeti katasztrófa vagy széles körű szolgáltatás megszakadása miatt. Ezek ritkán fordul elő, de a lehetőségét, hogy nincs-e egy teljes régióra kimaradás elő kell készítenie. Ha egy teljes régió a szolgáltatáskimaradás, az adatokat a helyileg redundáns másolatait szeretné ideiglenesen nem érhető el. Ha engedélyezte a georeplikáció, az Azure Storage-blobok és táblák három további másolatot készít egy másik régióban vannak tárolva. Az Azure teljes regionális kimaradás vagy vészhelyzet az elsődleges régió nem helyreállítható, ha ismételten leképezett összes DNS-bejegyzéseket, a georeplikált régióba.

> [!NOTE]
> Vegye figyelembe, hogy nem kell minden olyan ezt a folyamatot szabályozhatja, akkor történik az adatközpontra kiterjedő szolgáltatáskimaradás. Ez az oka meg kell is támaszkodik más alkalmazás-specifikus biztonsági mentési stratégia a legmagasabb szintű rendelkezésre állás elérése érdekében. További információkért lásd: [vészhelyreállítás és magas rendelkezésre állás a Microsoft Azure-alapú alkalmazásokhoz](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md). Ha meg szeretné tudni a saját feladatátvételi hatással, érdemes használja a [írásvédett georedundáns tárolás (RA-GRS)](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage), amely az adatok írásvédett másolatot készít egy másik régióban.
>
>


## <a name="option-1-use-a-backup-deployment-through-azure-traffic-manager"></a>Option 1: Azure Traffic Manager biztonsági mentési központi telepítése
A legtöbb robusztus vészhelyreállítási megoldás magában foglalja a fenntartja az alkalmazás különböző régiókban lévő több központi telepítést, majd a használatával [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) forgalomnak közöttük. Az Azure Traffic Manager biztosít több [útválasztási metódusait](../traffic-manager/traffic-manager-routing-methods.md), így kiválaszthatja, hogy e felosztása közöttük a forgalom vagy az elsődleges, illetve biztonsági mentési modell segítségével központi telepítések felügyeletéhez szükséges.

![Azure Cloud Services terheléselosztási régióban az Azure Traffic Managerrel](./media/cloud-services-disaster-recovery-guidance/using-azure-traffic-manager.png)

A leggyorsabb választ, egy régióban elvesztése, fontos, hogy a Traffic Manager konfigurálása [végpont-monitorozás](../traffic-manager/traffic-manager-monitoring.md).

## <a name="option-2-deploy-your-application-to-a-new-region"></a>Option 2: Az alkalmazás egy új régióban üzembe helyezéséhez
Több aktív üzemelő példányok fenntartása az előző beállítás leírtak szerint tekintetében további folyamatos költségeket. Ha a helyreállítási időre vonatkozó célkitűzés (RTO) elég rugalmas, és az eredeti kódot vagy a lefordított Cloud Services csomag rendelkezik, hozzon létre egy új példányát az alkalmazás egy másik régióban, és a DNS-rekordok átirányítása az új üzemelő példány frissítése.

Hozzon létre, és a egy cloud service-alkalmazás üzembe helyezése kapcsolatos további részletekért lásd: [hogyan hozhat létre és telepíthet egy felhőszolgáltatást](cloud-services-how-to-create-deploy-portal.md).

Az alkalmazás-adatforrások, attól függően szükség lehet az alkalmazás adatforrásának helyreállítási eljárások ellenőrzése.

* Azure Storage-adatforrások esetében lásd: [Azure Storage replikáció](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) ellenőrizze az elérhető lehetőségek a kiválasztott replikációs modell az alkalmazás alapján.
* SQL-adatbázis-forrásokhoz, olvassa el a [áttekintése: A felhő üzleti folytonossági és adatbázis katasztrófa utáni helyreállítás az SQL Database](../sql-database/sql-database-business-continuity.md) ellenőrizze az elérhető lehetőségek a kiválasztott replikációs modell az alkalmazás alapján.


## <a name="option-3-wait-for-recovery"></a>3. lehetőség: Várjon, amíg a helyreállítási
Ebben az esetben az Ön részéről semmit nem kell, de a szolgáltatás nem lesz elérhető a régióban visszaállításáig. A szolgáltatás aktuális állapotát megtekintheti a [Azure szolgáltatásállapot-irányítópult](https://azure.microsoft.com/status/).

## <a name="next-steps"></a>További lépések
A vészhelyreállítás és magas rendelkezésre állási stratégiájának megvalósításához kapcsolatos további információkért lásd: [vészhelyreállítás és magas rendelkezésre állás az Azure-alkalmazások](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Egy cloud-platform képességei részletes műszaki ismerete fejlesztése, lásd: [műszaki útmutatást az Azure rugalmassága](../resiliency/resiliency-technical-guidance.md).
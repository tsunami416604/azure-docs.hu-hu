---
title: "Azure virtuális gépek vész-helyreállítási eljárással |} Microsoft Docs"
description: "Ismerje meg, mi a teendő arra az esetre, ha egy Azure szolgáltatás megszűnésének hatással van az Azure virtuális gépeken."
services: virtual-machines
documentationcenter: 
author: kmouss
manager: timlt
editor: 
ms.assetid: 65272148-ff06-4bce-91f1-851d706d4d40
ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2017
ms.author: kmouss;aglick
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fb986a41e33501ee71c93a48457ac4114e33c671
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="what-to-do-in-the-event-that-an-azure-service-disruption-impacts-azure-vms"></a>Mi a teendő arra az esetre, ha egy Azure szolgáltatás megszűnésének hatással van az Azure virtuális gépeken
A Microsoft dolgozunk merevlemez győződjön meg arról, hogy a szolgáltatások mindig rendelkezésre álló újból. Kényszeríti a befolyásolható néha hatással velünk, hogy a nem tervezett szolgáltatás megszakadása miatt.

A Microsoft egy szolgáltatási szint szerződés (SLA) biztosít hasznos üzemidő és csatlakozási kötelezettségvállalást a hozzá tartozó szolgáltatások. Az SLA-t az egyes Azure-szolgáltatások található [Azure szolgáltatói szerződések](https://azure.microsoft.com/support/legal/sla/).

Azure már számos beépített platform szolgáltatást, amely támogatja a magas rendelkezésre állású alkalmazások. További információk a szolgáltatásokról, olvassa el a [vész-helyreállítási és magas rendelkezésre állás a Azure-alkalmazások](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Ez a cikk egy valódi katasztrófa utáni helyreállítása vonatkozik, amikor egy teljes régió kimaradás jelentős természeti katasztrófa vagy a széles körű szolgáltatás megszakítás miatt. Ezek ritkán fordul elő eseményeket, de előbb elő kell készítenie az, hogy van-e egy teljes terület kimaradás lehetőséget. Ha egy teljes terület a szolgáltatás szüneteltetése, a helyileg redundáns másolatait az adatok akkor ideiglenesen nem érhető el. Ha engedélyezett a georeplikáció, az Azure Storage blobs és táblák három további másolatot tárol egy másik régióban. Teljes regionális kimaradás vagy az elsődleges régióban nincs helyreállítható katasztrófa esetén, Azure amelyek összes DNS-bejegyzéseket, a georeplikált régióban.

Segítséget az ilyen ritka események kezelésére, az Azure virtuális gépek esetén a szolgáltatás szüneteltetése a teljes régió, ahol a virtuális gép az Azure-alkalmazás központi telepítése a következő útmutatást nyújtunk.

## <a name="option-1-initiate-a-failover-by-using-azure-site-recovery"></a>1. lehetőség: A feladatátvétel kezdeményezése Azure Site Recovery segítségével
Azure Site Recovery konfigurálhatja a virtuális gépek, így helyreállíthatja az alkalmazás, függetlenül attól, hogy az egyetlen kattintással perc alatt. Azure-régió, az Ön által választott képes replikálni, és párosított régiók sincs korlátozva. Elkezdheti által [a virtuális gépek replikálásához](https://aka.ms/a2a-getting-started). Is [helyreállítási terv létrehozása](../site-recovery/site-recovery-create-recovery-plans.md) , hogy a teljes feladatátvételi folyamat automatizálható az alkalmazás. Is [a feladatátvételi tesztek](../site-recovery/site-recovery-test-failover-to-azure.md) előre éles alkalmazásokban vagy a folyamatban lévő replikáció befolyásolása nélkül. Egy elsődleges régió becsukódjon most [kezdeményezzen feladatátvételt](../site-recovery/site-recovery-failover.md) , és az alkalmazás cél régióban.


## <a name="option-2-wait-for-recovery"></a>2. lehetőség: Várjon, amíg a helyreállítás
Ebben az esetben a letöltés intézkedés nem szükséges. Tudja, hogy jelenleg dolgozunk gondossággal szolgáltatás rendelkezésre állása visszaállítására. A szolgáltatás jelenlegi állapota tekintheti meg a [Azure az állapotjelző irányítópulthoz](https://azure.microsoft.com/status/).

Ez a lehetőség ajánlott, ha nem állított be Azure Site Recovery, írásvédett georedundáns tárolás vagy a megszakítás előtt georedundáns tárolást. Ha állított be georedundáns tárolás vagy írásvédett georedundáns tárolás a tárfiók a virtuális gép virtuális merevlemezek (VHD) tároló, keresse meg az alapjául szolgáló lemezképhez virtuális merevlemez helyreállítása, és próbálkozzon, egy új virtuális gép kiépítése. Ez a beállítás nem előnyben részesített nem garantálja az adatok szinkronizálását, mert. Ezért ezt a beállítást nem biztos, hogy működni.


> [!NOTE]
> Vegye figyelembe, hogy nem tudja befolyásolni bármely ezt a folyamatot, és akkor történik a régió kiterjedő szolgáltatás üzemzavarokhoz vezethet. Ebből kifolyólag kell is használ, más alkalmazás-specifikus biztonsági stratégiák a legmagasabb rendelkezésre állásának eléréséhez. További információkért lásd [vész-helyreállítási adatok stratégiák](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications#data-strategies-for-disaster-recovery).
>
>

## <a name="next-steps"></a>Következő lépések

- Start [védelme az Azure virtuális gépeken futó alkalmazások](https://aka.ms/a2a-getting-started) Azure Site Recovery segítségével

- A vész-helyreállítási és a magas rendelkezésre állási stratégiájának megvalósításához kapcsolatos további információkért lásd: [vész-helyreállítási és magas rendelkezésre állás a Azure-alkalmazások](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

- A felhő platform képességei részletes műszaki megértése elkészítéséhez lásd [Azure rugalmassági műszaki útmutatót](../resiliency/resiliency-technical-guidance.md).


- Ha utasításokat ne törölje a jelet, vagy ha szeretné, hogy a Microsoft az Ön nevében műveletek végrehajtásához, forduljon a [ügyfél-támogatási](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

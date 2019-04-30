---
title: Az Azure virtuális gépek vész-helyreállítási helyzetekben |} A Microsoft Docs
description: Ismerje meg, mi a teendő abban az esetben, ha egy Azure-szolgáltatáskimaradás hatással van az Azure-beli virtuális gépek.
services: virtual-machines
documentationcenter: ''
author: kmouss
manager: jeconnoc
editor: ''
ms.assetid: 65272148-ff06-4bce-91f1-851d706d4d40
ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2017
ms.author: kmouss;aglick
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: dc71e8564b35f4fdd4153a04c66a3d8c5df88c30
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61478844"
---
# <a name="what-to-do-in-the-event-that-an-azure-service-disruption-impacts-azure-vms"></a>Mi a teendő abban az esetben, ha egy Azure-szolgáltatáskimaradás hatással van az Azure virtuális gépek
A Microsoft dolgozunk, hogy szolgáltatásaink mindig elérhető, amikor szüksége van rájuk. Kényszeríti a befolyásolható néha hatással velünk a kapcsolatot, hogy a nem tervezett szolgáltatáskimaradás miatt.

A Microsoft szolgáltatói szerződés (SLA) biztosít a szolgáltatások üzemidejével és elérhetőségével vonatkozó kötelezettségvállalást. A szolgáltatói szerződés tartalmazza az egyes Azure-szolgáltatások található [Azure szolgáltatásiszint-szerződései](https://azure.microsoft.com/support/legal/sla/).

Az Azure már számos beépített platformfunkciók támogatja a magas rendelkezésre állású alkalmazások. Ezek a szolgáltatások kapcsolatos további információkért olvassa el a [vészhelyreállítás és magas rendelkezésre állás az Azure-alkalmazások](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Ez a cikk ismerteti a valódi vész-helyreállítási helyzetekre, amikor egy teljes régió kimaradás jelentős természeti katasztrófa vagy széles körű szolgáltatás megszakadása miatt. Ezek ritkán fordul elő, de a lehetőségét, hogy nincs-e egy teljes régióra kimaradás elő kell készítenie. Ha egy teljes régió a szolgáltatáskimaradás, az adatokat a helyileg redundáns másolatait szeretné ideiglenesen nem érhető el. Ha engedélyezte a georeplikáció, az Azure Storage-blobok és táblák három további másolatot készít egy másik régióban vannak tárolva. Az Azure teljes regionális kimaradás vagy vészhelyzet az elsődleges régió nem helyreállítható, ha ismételten leképezett összes DNS-bejegyzéseket, a georeplikált régióba.

Könnyebben kezelni ezeket ritkán fordul elő, az alábbi útmutató Azure virtuális gépek esetén a teljes régió, ahol az Azure virtuális gép alkalmazás üzemel egy szolgáltatáskimaradás biztosítunk.

## <a name="option-1-initiate-a-failover-by-using-azure-site-recovery"></a>Option 1: Kezdeményezzen feladatátvételt az Azure Site Recovery használatával
A virtuális gépek Azure Site Recovery úgy, hogy helyre tudja állítani az alkalmazás, függetlenül attól, hogy egyetlen kattintással perc konfigurálható. Tetszőleges Azure-régióba képes replikálni, és nem korlátozódik a társított két régió bármelyikén. Úgy kezdheti [a virtuális gépek replikálásához](https://aka.ms/a2a-getting-started). Is [helyreállítási terv létrehozása](../site-recovery/site-recovery-create-recovery-plans.md) úgy, hogy a teljes feladatátvételi folyamat automatizálható az alkalmazás. Is [tesztelheti a feladatátvételeket](../site-recovery/site-recovery-test-failover-to-azure.md) leltárkezelők éles alkalmazásokban vagy a folyamatban lévő replikáció befolyásolása nélkül. Egy elsődleges régióban bekövetkező szolgáltatáskimaradás esetén csak [feladatátvétel](../site-recovery/site-recovery-failover.md) és az alkalmazás a célrégióban.


## <a name="option-2-wait-for-recovery"></a>Option 2: Várjon, amíg a helyreállítási
Ebben az esetben az Ön részéről semmit nem kell. Tudja meg, hogy folyamatban van térségekre való kiterjesztésén szolgáltatás rendelkezésre állásának visszaállítása érdekében. A szolgáltatás aktuális állapotát megtekintheti a [Azure szolgáltatásállapot-irányítópult](https://azure.microsoft.com/status/).

Ez a legjobb lehetőség, ha nem állított be az Azure Site Recovery, írásvédett georedundáns tárolás vagy georedundáns tárolást megelőzően a megszakítások időtartamát. Ha állított be georedundáns tárolás vagy írásvédett georedundáns tárolás a tárfiók a virtuális gép virtuális merevlemezek (VHD-k) tárolására, keresse meg az alaprendszerképet virtuális merevlemez helyreállítása, és próbálja meg azt az új virtuális gép kiépítése. Ez a nem előnyben részesített lehetőség, mert az adatok szinkronizálásának garanciát nem jelentenek. Ennek következtében ezt a beállítást nem garantált, hogy működjön.


> [!NOTE]
> Vegye figyelembe, hogy nem kell minden olyan ezt a folyamatot szabályozhatja, és ez csak akkor történik meg a régióra kiterjedő szolgáltatáskimaradás. Ez az oka meg kell is támaszkodik más alkalmazás-specifikus biztonsági mentési stratégia a legmagasabb szintű rendelkezésre állás elérése érdekében. További információkért lásd a szakasz [vész-helyreállítási stratégiát adatok](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications#data-strategies-for-disaster-recovery).
>
>

## <a name="next-steps"></a>További lépések

- Indítsa el [védelme az Azure-beli virtuális gépeken futó alkalmazások](https://aka.ms/a2a-getting-started) Azure Site Recovery használatával

- A vészhelyreállítás és magas rendelkezésre állási stratégiájának megvalósításához kapcsolatos további információkért lásd: [vészhelyreállítás és magas rendelkezésre állás az Azure-alkalmazások](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

- Egy cloud-platform képességei részletes műszaki ismerete fejlesztése, lásd: [műszaki útmutatást az Azure rugalmassága](../resiliency/resiliency-technical-guidance.md).


- Ha utasításokat ne törölje a jelet, vagy ha szeretné, hajtsa végre a műveleteket az Ön nevében más biztosít a Microsoftnak, lépjen kapcsolatba [ügyfél-támogatási](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

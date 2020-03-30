---
title: Vész-helyreállítási forgatókönyvek
description: Ismerje meg, mi a teendő abban az esetben, ha egy Azure-szolgáltatáskimaradás hatással van az Azure virtuális gépeire.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.date: 05/31/2017
ms.author: cynthn
ms.openlocfilehash: f2dc43e1f07d449bf2f8ed39ce4523c99b551dae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77115611"
---
# <a name="what-if-an-azure-service-disruption-impacts-azure-vms"></a>Mi a teendő, ha egy Azure-szolgáltatás kimaradása hatással van az Azure virtuális gépeire?
A Microsoft nál keményen dolgozunk azon, hogy szolgáltatásaink mindig elérhetők legyenek az Ön számára, amikor szüksége van rájuk. A rajtunk kívül álló erők néha olyan módon hatnak ránk, amely nem tervezett szolgáltatászavarokat okoz.

A Microsoft szolgáltatásiszint-szerződést (SLA) biztosít a szolgáltatásaihoz, mint az üzemidő és a kapcsolat kötelezettségvállalását. Az egyes Azure-szolgáltatások SLA-ja az [Azure szolgáltatásiszint-szerződésekkel](https://azure.microsoft.com/support/legal/sla/)érhető el.

Az Azure már számos beépített platformfunkcióval rendelkezik, amelyek támogatják a magas rendelkezésre állású alkalmazásokat. Ezekről a szolgáltatásokról további információért olvassa el [a vészhelyreállítást és az Azure-alkalmazások magas rendelkezésre állását.](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)

Ez a cikk egy valódi vész-helyreállítási forgatókönyvet ismerteti, amikor egy egész régió súlyos természeti katasztrófa vagy a szolgáltatás széles körű megszakítása miatt kimaradást tapasztal. Ezek ritka előfordulások, de fel kell készülnie arra a lehetőségre, hogy egy egész régió kimaradása van. Ha egy teljes régió szolgáltatáskimaradást tapasztal, az adatok helyileg redundáns másolatai átmenetileg nem érhetők el. Ha engedélyezte a georeplikációt, az Azure Storage-blobok és -táblák további három példányát egy másik régióban tárolja. Abban az esetben, ha egy teljes regionális kimaradás vagy katasztrófa, amelyben az elsődleges régió nem helyreállítható, az Azure újraleképezi az összes DNS-bejegyzéseket a georeplikált régióba.

Ezek nek a ritka eseteknek a kezeléséhez a következő útmutatást biztosítjuk az Azure virtuális gépekhez abban az esetben, ha az Azure virtuálisgép-alkalmazás üzembe helyezése a teljes régióban szolgáltatáskimaradás.

## <a name="option-1-initiate-a-failover-by-using-azure-site-recovery"></a>1. lehetőség: Feladatátvétel kezdeményezése az Azure Site Recovery használatával
Konfigurálhatja az Azure Site Recovery a virtuális gépek, így az alkalmazás egyetlen kattintással percek alatt helyreállíthatja. Replikálhatja az Azure-régióban az Ön által kiválasztott, és nem korlátozódik a párosított régiók. A [virtuális gépek replikálásával](https://aka.ms/a2a-getting-started)első lehet. Létrehozhat [egy helyreállítási tervet,](../site-recovery/site-recovery-create-recovery-plans.md) hogy automatizálhassa az alkalmazás teljes feladatátvételi folyamatát. A [feladatátvételeket](../site-recovery/site-recovery-test-failover-to-azure.md) előzetesen tesztelheti anélkül, hogy befolyásolna az éles alkalmazást vagy a folyamatos replikációt. Abban az esetben, ha egy elsődleges régió zavar, csak [kezdeményezzen egy feladatátvételt,](../site-recovery/site-recovery-failover.md) és hozza az alkalmazást a célrégióban.


## <a name="option-2-wait-for-recovery"></a>2. lehetőség: Várakozás a helyreállításra
Ebben az esetben nincs szükség beavatkozásra az Ön részéről. Tudja, hogy szorgalmasan dolgozunk a szolgáltatás rendelkezésre állásának helyreállításán. Az aktuális szolgáltatásállapotát az [Azure Service Health Dashboard oldalon láthatja.](https://azure.microsoft.com/status/)

Ez a legjobb megoldás, ha nem állította be az Azure Site Recovery, az olvasási hozzáférésű georedundáns tárolás vagy a georedundáns tárolás a megszakítás előtt. Ha georedundáns tárolást vagy olvasási hozzáférésű georedundáns tárolást állított be a virtuális gép virtuális merevlemezeit (VHD-ket) tároló tárfiókhoz, megkeresheti az alaplemezkép virtuális merevlemezét, és megpróbálhat kiépíteni belőle egy új virtuális gépet. Ez nem ajánlott lehetőség, mert nincs garancia az adatok szinkronizálására. Következésképpen ez a lehetőség nem garantált, hogy működik.


> [!NOTE]
> Ne feledje, hogy nincs semmilyen ellenőrzése alatt ezt a folyamatot, és ez csak akkor fordul elő, a régiószintű szolgáltatás kimaradások. Emiatt a legmagasabb szintű rendelkezésre állás eléréséhez más alkalmazásspecifikus biztonsági mentési stratégiákra is támaszkodnia kell. További információt a [vész-helyreállítási adatstratégiák című szakaszban talál.](https://docs.microsoft.com/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan)
>
>

## <a name="next-steps"></a>További lépések

- Az Azure Site Recovery használatával [megkezdheti az Azure virtuális gépeken futó alkalmazások védelmét](https://aka.ms/a2a-getting-started)

- Ha többet szeretne megtudni avész-helyreállítási és magas rendelkezésre állási stratégia megvalósításáról, olvassa el a [Vész-helyreállítási és az Azure-alkalmazások magas rendelkezésre állású témakört.](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)

- A felhőplatform képességeinek részletes technikai megértéséhez olvassa el [az Azure rugalmassági technikai útmutatóját.](/azure/data-lake-store/data-lake-store-disaster-recovery-guidance)


- Ha az utasítások nem egyértelműek, vagy ha azt szeretné, hogy a Microsoft végezze el a műveleteket az Ön nevében, forduljon az [ügyfélszolgálathoz.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)

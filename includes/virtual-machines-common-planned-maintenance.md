---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: include
ms.date: 8/22/2019
ms.author: shants
ms.custom: include file
ms.openlocfilehash: b2a7bbef2c421281780c0191fa32381468899bbf
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2019
ms.locfileid: "70020335"
---
Az Azure rendszeresen frissíti a platformját a virtuális gépek gazdagép-infrastruktúrájának megbízhatóságának, teljesítményének és biztonságának javítása érdekében. Ezeknek a frissítéseknek az a célja, hogy az üzemeltetési környezetben lévő, a hálózati összetevők vagy a hardver leszerelését javító szoftver-összetevőkből kiterjedően frissítse a frissítéseket. 

A frissítések ritkán érintik az üzemeltetett virtuális gépeket. Ha a frissítések hatással vannak, az Azure kiválasztja a legkevésbé hatásos módszert a frissítésekhez:

- Ha a frissítés nem igényel újraindítást, a rendszer szünetelteti a virtuális gépet a gazdagép frissítésekor, vagy a virtuális gép egy már frissített gazdagépre van áttelepítve.

- Ha a karbantartás újraindítást igényel, értesítést kap a tervezett karbantartásról. Az Azure egy időablakot is biztosít, amelyben saját maga is elindíthatja a karbantartást. Az önkarbantartási időszak általában 35 nap, kivéve, ha a karbantartás nem sürgős. Az Azure olyan technológiákat fektet be, amelyek csökkentik azon esetek számát, amelyekben a tervezett platform karbantartásához szükség van a virtuális gépek újraindítására. 

Ez a lap leírja, hogy az Azure hogyan hajtja végre mindkét típusú karbantartást. További információ a nem tervezett eseményekről (kimaradások): [a Windows rendszerű virtuális gépek rendelkezésre állásának kezelése](../articles/virtual-machines/windows/manage-availability.md) , illetve a megfelelő [Linux](../articles/virtual-machines/linux/manage-availability.md)-cikk.

A virtuális gépeken belül a Windows vagy [Linux](../articles/virtual-machines/linux/scheduled-events.md)rendszerhez [készült Scheduled Events használatával](../articles/virtual-machines/windows/scheduled-events.md) értesítéseket kaphat a közelgő karbantartásról.

A tervezett karbantartás kezelésével kapcsolatos útmutatásért lásd: a [tervezett karbantartási értesítések kezelése a Linux](../articles/virtual-machines/linux/maintenance-notifications.md) rendszerhez [](../articles/virtual-machines/windows/maintenance-notifications.md)vagy a kapcsolódó cikk a Windowshoz.

## <a name="maintenance-that-doesnt-require-a-reboot"></a>Újraindítást nem igénylő karbantartás

Ahogy korábban már említettük, a legtöbb platform frissítései nem érintik az ügyfél virtuális gépeket. Ha egy nem befolyásoló frissítés nem lehetséges, az Azure kiválasztja a legkevésbé érintett frissítési mechanizmust az ügyfél virtuális gépei számára. 

A legtöbb nem nulla hatású karbantartás kevesebb, mint 10 másodpercig szünetelteti a virtuális gépet. Bizonyos esetekben az Azure memóriát használ, és megőrzi a karbantartási mechanizmusokat. Ezek a mechanizmusok akár 30 másodpercig szüneteltetik a virtuális gépet, és megőrzik a memóriát a memóriában. A virtuális gép ezután folytatódik, és az órája automatikusan szinkronizálva lesz. 

Memória – a karbantartási munkálatok az Azure-beli virtuális gépek több mint 90 százalékára is használhatók. A G, M, N és H sorozat esetében nem működik. Az Azure egyre inkább élő áttelepítési technológiákat használ, és javítja a memória-megőrzést biztosító karbantartási mechanizmusokat a szüneteltetési időtartam csökkentése érdekében.  

Az újraindítást nem igénylő karbantartási műveletek egyszerre csak egy tartalék tartományt alkalmaznak. Ezek akkor állnak le, ha figyelmeztetési állapotra utaló jeleket kapnak. 

Az ilyen típusú frissítések hatással lehetnek egyes alkalmazásokra. Ha a virtuális gép élő áttelepítése egy másik gazdagépre történik, egyes érzékeny munkaterhelések némi teljesítménybeli romlást jelezhetnek néhány perc alatt, ami a virtuális gép szüneteltetését eredményezi. A virtuális gépek karbantartásának előkészítéséhez és az Azure-karbantartás során felmerülő hatás csökkentéséhez próbálkozzon az [Scheduled Events Windows](../articles/virtual-machines/windows/scheduled-events.md) vagy [Linux](../articles/virtual-machines/linux/scheduled-events.md) rendszerű alkalmazásokkal való használatával. Az Azure az [Azure dedikált gazdagépeken](../articles/virtual-machines/windows/dedicated-hosts.md) és az [elkülönített virtuális gépeken](../articles/security/fundamentals/isolation-choices.md)is teljes körűen szabályozhatja az ilyen nem nulla platform-karbantartást. A karbantartási vezérlés funkció előzetes verzióban érhető el, és a [regisztrációs űrlap](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR6lJf7DwiQxNmz51ksQvxV9UNUM3UllWUjBMTFZQUFhHUDI0VTBPQlJFNS4u)elküldésével kérhet hozzáférést. Lehetőség van a nem nulla hatású platform frissítéseinek kihagyása és a frissítések batchként való alkalmazására egy 35 napos időszakon belül.

### <a name="live-migration"></a>Élő áttelepítés

Az élő áttelepítés olyan művelet, amely nem igényel újraindítást, és megőrzi a virtuális gép memóriáját. Szüneteltető vagy befagyasztást okoz, ami általában legfeljebb 5 másodperc. A G, M, N és H sorozat kivételével az összes infrastruktúra-szolgáltatási (IaaS) virtuális gép jogosult az élő áttelepítésre. A jogosult virtuális gépek az Azure-flottába telepített IaaS-alapú virtuális gépek több mint 90 százalékát képviselik. 

Az Azure platform a következő helyzetekben elindítja az élő áttelepítést:
- Tervezett karbantartás
- Hardverhiba
- Foglalási optimalizálások

Egyes tervezett karbantartási forgatókönyvek élő áttelepítést használnak, és az élő áttelepítési műveletek elindulásakor a Scheduled Events segítségével előre is tájékozódhat.

Az élő áttelepítés a virtuális gépek áthelyezésére is használható, ha Azure Machine Learning algoritmusok egy közelgő hardverhiba előrejelzését vagy a virtuálisgép-foglalások optimalizálását szeretnék. A csökkentett teljesítményű hardvereket észlelő prediktív modellezéssel kapcsolatos további információkért lásd: [Az Azure-beli virtuális gépek rugalmasságának növelése a prediktív gépi tanulással és az élő](https://azure.microsoft.com/blog/improving-azure-virtual-machine-resiliency-with-predictive-ml-and-live-migration/?WT.mc_id=thomasmaurer-blog-thmaure)áttelepítéssel. Az élő áttelepítési értesítések megjelennek a figyelő Azure Portalban, valamint a Service Health naplók és a Scheduled Events esetén, ha ezeket a szolgáltatásokat használja.

## <a name="maintenance-that-requires-a-reboot"></a>Újraindítást igénylő karbantartás

Azon ritka esetekben, amikor a virtuális gépeket újra kell indítani a tervezett karbantartáshoz, a rendszer előzetes értesítést küld. A tervezett karbantartásnak két fázisa van: az önkiszolgáló fázis és egy ütemezett karbantartási fázis.

Az *önkiszolgáló fázisban*, amely általában négy hétig tart, elindítja a karbantartást a virtuális gépeken. Az önkiszolgáló szolgáltatás részeként lekérdezheti az egyes virtuális gépek állapotát, és megtekintheti az utolsó karbantartási kérelem eredményét.

Az önkiszolgáló karbantartás indításakor a virtuális gép egy már frissített csomópontra lesz telepítve. Mivel a virtuális gép újraindul, az ideiglenes lemez elvész, és a virtuális hálózati adapterhez társított dinamikus IP-címek frissülnek.

Ha hiba merül fel az önkiszolgáló karbantartás során, a művelet leáll, a virtuális gép nem frissül, és a lehetőséggel megpróbálkozhat az önkiszolgáló karbantartással. 

Az önkiszolgáló fázis végén az *ütemezett karbantartási fázis* kezdődik. Ebben a fázisban továbbra is lekérdezhető a karbantartási fázis, de a karbantartás nem indítható el.

Az újraindítást igénylő karbantartás kezelésével kapcsolatos további információkért lásd: a [tervezett karbantartási értesítések kezelése a Linux](../articles/virtual-machines/linux/maintenance-notifications.md) rendszerhez vagy a [Windows](../articles/virtual-machines/windows/maintenance-notifications.md)megfelelő cikke. 

### <a name="availability-considerations-during-scheduled-maintenance"></a>A rendelkezésre állással kapcsolatos megfontolások az ütemezett karbantartás során 

Ha úgy dönt, hogy megvárja az ütemezett karbantartási szakaszt, néhány dolgot figyelembe kell vennie a virtuális gépek legmagasabb rendelkezésre állásának fenntartása érdekében. 

#### <a name="paired-regions"></a>Párosított régiók

Minden egyes Azure-régió párosítva van egy másik régióval, amely ugyanazon a földrajzi helyen található. Együttesen egy régió párokat alkotnak. Az ütemezett karbantartási fázisban az Azure csak a régió pár egyetlen régiójában frissíti a virtuális gépeket. Ha például az USA északi középső régiójában frissíti a virtuális gépet, akkor az Azure nem frissíti az USA déli középső régiójában lévő virtuális gépeket. Azonban más régiók (például Észak-Európa) karbantarthatók ugyanabban az időben, mint az USA keleti régiója. Annak megértése, hogy a régió-párok hogyan segíthetnek a virtuális gépek régiók közötti jobb elosztásában. További információ: [Azure region Pairs](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

#### <a name="availability-sets-and-scale-sets"></a>Rendelkezésre állási készletek és méretezési csoportok

Az Azure-beli virtuális gépeken futó számítási feladatok üzembe helyezésekor létrehozhatók a *rendelkezésre állási csoporton* belüli virtuális gépek, amelyek magas rendelkezésre állást biztosítanak az alkalmazás számára. A rendelkezésre állási csoportok használata esetén gondoskodhat arról, hogy az újraindítást igénylő leállás vagy karbantartási események során legalább egy virtuális gép elérhető legyen.

Egy rendelkezésre állási csoporton belül az egyes virtuális gépek akár 20 frissítési tartományon (frissítési) keresztül oszlanak meg. Az ütemezett karbantartás során egy adott időpontban csak egy UD frissül. A frissítési nem feltétlenül frissülnek egymás után. 

A virtuálisgép -méretezési csoportok egy olyan Azure-beli számítási erőforrás, amellyel azonos virtuális gépek egyetlen erőforrásként való üzembe helyezésére és kezelésére használhatók. A méretezési csoport automatikusan települ a frissítési között, például a virtuális gépeket egy rendelkezésre állási csoportba. A rendelkezésre állási csoportokhoz hasonlóan a méretezési csoportok használatakor a rendszer az ütemezett karbantartás során egy adott időpontban csak egy UD frissítést végez.

A virtuális gépek magas rendelkezésre állásra való beállításával kapcsolatos további információkért lásd: a [Windows rendszerű virtuális gépek rendelkezésre állásának kezelése](../articles/virtual-machines/windows/manage-availability.md) , vagy a megfelelő [Linux](../articles/virtual-machines/linux/manage-availability.md)-cikk.

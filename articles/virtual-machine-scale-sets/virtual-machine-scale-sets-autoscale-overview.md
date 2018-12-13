---
title: Az automatikus méretezés Azure-beli virtuálisgép-méretezési csoportok áttekintése |} A Microsoft Docs
description: Ismerje meg, hogy egy Azure-beli virtuálisgép-méretezési alapján meghatározott ütemezés szerint vagy a teljesítményre automatikusan skálázhatja a különböző módokon
services: virtual-machine-scale-sets
documentationcenter: ''
author: zr-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: d29a3385-179e-4331-a315-daa7ea5701df
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: zarhoads
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 18be454c93c32e0902a55a08748045c26d649ce7
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53322039"
---
# <a name="overview-of-autoscale-with-azure-virtual-machine-scale-sets"></a>Beállítja az Azure virtuálisgép-méretezési csoport automatikus méretezés áttekintése
Egy Azure-beli virtuálisgép-méretezési csoportot automatikusan növelheti vagy csökkentheti az alkalmazást futtató Virtuálisgép-példányok számát. Az automatikus és rugalmas viselkedést csökkenti a kezelési megfigyelése és optimalizálása az alkalmazás teljesítményét. Létrehozhat szabályokat, amelyek meghatározzák az elfogadható teljesítményt egy pozitív felhasználói élmény. Teljesülnek a meghatározott küszöbértékeket, az automatikus skálázási szabályok beletelhet művelet a méretezési csoport kapacitásának módosításához. Események automatikusan növelhető vagy csökkenését, a méretezési készlet kapacitásának rögzített többször is ütemezheti. Ez a cikk áttekintést, amelyek metrikák érhetők el, és milyen műveletek automatikus méretezési funkciójával hajthat végre.


## <a name="benefits-of-autoscale"></a>Automatikus skálázási előnyei
Az alkalmazás növekvő igényeivel párhuzamosan a méretezési csoportban lévő virtuálisgép-példányok terhelése is nő. Ha a megnövekedett terhelés állandó, nem csak pillanatnyi igény, akkor megadhatja, hogy az automatikus skálázási szabály növelje meg a virtuálisgép-példányok számát a méretezési csoportban.

Ezen virtuálisgép-példányok létrehozását és az alkalmazások telepítését követően a méretezési csoport megkezdi a forgalom elosztását közöttük a terheléselosztón keresztül. Azt szabályozza, hogy milyen metrikákat kíván monitorozni – például CPU és memória, mennyi ideig a az alkalmazás terhelésének elérnie egy megadott küszöbértéket kell és hány Virtuálisgép-példányt a méretezési csoport hozzáadása beállítása.

Az este vagy a hétvége folyamán az alkalmazás igényei csökkenhetnek. Ha a csökkent terhelés egy adott időtartam alatt állandó, akkor megadhatja, hogy az automatikus skálázási szabály csökkentse a virtuálisgép-példányok számát a méretezési csoportban. A horizontális leskálázási művelet csökkenti a méretezési csoport futtatásának költségeit, mivel csak az aktuális igényt kielégítő számú példányt futtat.


## <a name="use-host-based-metrics"></a>Használja a gazdagépalapú mérőszámok
A Virtuálisgép-példányok az automatikus skálázási szabályok adott beépített gazda mérőszámok rendelkezésre álló hozhat létre. Gazdagép mérőszámok segítségével betekintést a Virtuálisgép-példányok méretezési csoportban nem szükséges telepíteni vagy konfigurálni, további ügynökök és az adatok gyűjtemények. Ezeket a metrikákat használó automatikus skálázási szabályok skálázhatja fel- vagy Virtuálisgép-példányok Processzorhasználat, memóriaigény vagy lemezelérést használati adott válaszként.

A gazdagépalapú mérőszámokat használó automatikus méretezési szabályok a következő eszközökkel hozhatók létre:

- [Azure Portal](virtual-machine-scale-sets-autoscale-portal.md)
- [Azure PowerShell](tutorial-autoscale-powershell.md)
- [Azure CLI](tutorial-autoscale-cli.md)
- [Az Azure-sablon](tutorial-autoscale-template.md)

Részletes teljesítmény-mérőszámokat használó automatikus skálázási szabályok létrehozásához is [telepítése és konfigurálása az Azure diagnosztikai bővítmény](#in-guest-vm-metrics-with-the-azure-diagnostics-extension) Virtuálisgép-példányokon, vagy [konfigurálása az alkalmazás használata az App Insights](#application-level-metrics-with-app-insights).

A Vendég virtuális gép metrikái az Azure diagnosztikai bővítmény, az App Insights automatikus méretezési szabályokhoz gazdagépalapú mérőszámokat használó használhatja a következő konfigurációs beállítások.

### <a name="metric-sources"></a>Metrika források
Az automatikus skálázási szabályok mérőszámok használatát a következő forrásokból származó:

| Metrikaforrás        | Használati eset                                                                                                                     |
|----------------------|------------------------------------------------------------------------------------------------------------------------------|
| Méretezési csoport aktuális    | A gazdagépalapú mérőszámok, amelyek nem igényelnek további ügynököket telepíteni vagy konfigurálni.                                  |
| Tárfiók      | Az Azure diagnosztikai bővítményét ír majd elindítani az automatikus skálázási szabályok felhasznált az Azure storage teljesítmény-mérőszámon. |
| Service Bus-üzenetsor    | Az alkalmazás vagy más összetevők továbbít egy Azure Service Bus üzenetsor eseményindító szabályokra.                   |
| Application Insights | Egy kialakítási csomagot az alkalmazásban, közvetlenül az alkalmazásból metrikák streamelő telepítve.                         |


### <a name="autoscale-rule-criteria"></a>Automatikus skálázási szabály feltételei
A következő mérőszámok automatikus skálázási szabályok létrehozása esetén érhetők el használatra. Az Azure diagnosztikai bővítmény vagy az App Insights használja, mely metrikák figyelése és az automatikus skálázási szabályok használata határozza meg.

| Metrika neve               |
|---------------------------|
| Százalékos processzorhasználat            |
| Hálózat bejövő adatforgalma                |
| Hálózat kimenő adatforgalma               |
| Lemezről beolvasott bájtok           |
| Lemezre írt bájtok          |
| Lemezolvasási művelet/s  |
| Lemezre írási művelet/s |
| Fennmaradó processzorkreditek     |
| Felhasznált processzorkreditek      |

Egy metrika figyelése az automatikus skálázási szabályok létrehozásakor a szabályok tekintse meg a következő metrikák összesítési műveletek egyikét:

| Összesítés típusa |
|------------------|
| Átlag          |
| Minimális          |
| Maximum          |
| Összes            |
| Vezetéknév             |
| Darabszám            |

Az automatikus skálázási szabályok vannak majd aktiválódik, ha a metrikák összehasonlítja a meghatározott küszöbértéket a következő operátorokat egyikével:

| Művelet                 |
|--------------------------|
| Nagyobb, mint             |
| Nagyobb vagy egyenlő |
| Kisebb mint                |
| Kisebb vagy egyenlő    |
| Egyenlő                 |
| Nem egyenlő             |


### <a name="actions-when-rules-trigger"></a>Amikor szabályok aktiválása műveletek
Ha az automatikus skálázási szabály aktiválásakor a méretezési automatikusan méretezheti a következő módszerek valamelyikével:

| Skálázási művelet     | Használati eset                                                                                                                               |
|---------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| Mennyiség növelése a következővel:   | Hozzon létre Virtuálisgép-példányok rögzített számú. A kisebb számú virtuális gépek méretezési hasznos.                                           |
| Százalék növelése a következővel: | Virtuálisgép-példányok százalékskálájú növelését. Megfelelő választás a nagyobb léptékben állítja be, ahol egy rögzített növekedése nem észrevehetően javíthatja a teljesítményt. |
| Mennyiség növelése a következőre:   | Hozzon létre több Virtuálisgép-példányok egy kívánt maximális összeg elérni kötelezőként.                                                            |
| Mennyiség csökkentése a következővel:   | Az eltávolítandó Virtuálisgép-példányok rögzített számú. A kisebb számú virtuális gépek méretezési hasznos.                                           |
| Százalék csökkentése a következővel: | Virtuálisgép-példányok százalékskálájú csökkentheti. Megfelelő választás a nagyobb léptékben állítja be, ahol egy rögzített növekedése nem észrevehetően csökkenthetik erőforrás-használat és költségek. |
| Mennyiség csökkentése a következőre:   | Távolítsa el a Virtuálisgép-példányok elérni kívánt minimálisan szükséges.                                                            |


## <a name="in-guest-vm-metrics-with-the-azure-diagnostics-extension"></a>A Vendég virtuális gép metrikák az Azure diagnosztikai bővítmény
Az Azure diagnosztikai bővítmény olyan ügynök, amely egy Virtuálisgép-példány fut. Az ügynök figyeli, és menti az Azure storage teljesítmény-mérőszámon. Ezeket a teljesítmény-mérőszámok tartalmaznak a virtuális gép állapota részletesebb információkat, például *AverageReadTime* lemezek vagy *PercentIdleTime* processzor. Automatikus méretezési szabályok egy részletesebb tájékoztatás a virtuális gép teljesítmény, a CPU-használat vagy a memória-használat százalékos alapján hozhat létre.

Az Azure diagnosztikai bővítmény használatához kell a Virtuálisgép-példányok az Azure storage-fiókok létrehozása, az Azure diagnostics-ügynök telepítése, majd konfigurálja a virtuális gépek stream teljesítményszámlálókat a storage-fiókba.

További információt az Azure diagnosztikai bővítmény [Linux virtuális gépen](../virtual-machines/extensions/diagnostics-linux.md) vagy [Windows virtuális gépen](../virtual-machines/extensions/diagnostics-windows.md) való engedélyezésével kapcsolatos cikkekben talál.


## <a name="application-level-metrics-with-app-insights"></a>Alkalmazás-szintű metrikákat az App insights segítségével
Az alkalmazások teljesítményét, a további információhoz juthat, az Application Insights is használhatja. Telepít egy kis méretű kialakítási csomagot az alkalmazásban, amely figyeli az alkalmazást, és telemetriai adatokat küld az Azure-bA. Figyelheti a mérőszámokat, például az alkalmazás, a lapbetöltés teljesítménye, a válaszok gyorsaságát, és a munkamenetek számlálják. Alkalmazás metrikák az automatikus skálázási szabályok létrehozása a részletes és beágyazott szintjén indít el a gyakorlatban is használható, amely hatással lehet a felhasználói élmény szabályokat mivel használható.

Az App Insights programmal kapcsolatos további információért lásd: [Mi az Application Insights?](../application-insights/app-insights-overview.md).


## <a name="scheduled-autoscale"></a>Ütemezett automatikus méretezési funkció
Ütemezés alapján automatikus méretezési szabályokat is létrehozhat. Ezek a szabályok ütemezésalapú automatikus méretezési csoport Virtuálisgép-példányok, rögzített alkalommal engedélyezése. Teljesítmény-alapú szabályokkal lehet a teljesítményre gyakorolt hatás az alkalmazás az automatikus skálázási szabályok eseményindító előtt, és az új Virtuálisgép-példányok törlődnek. Ha ilyen igény szerint is várhatóan, további Virtuálisgép-példányok kiépített és készen áll a további ügyfelek használata és az alkalmazás igényeinek megfelelően.

Az alábbi példák forgatókönyvek, amelyek esetleg ütemezésalapú automatikus skálázási szabályok használata:

- Automatikusan skálázhatja fel horizontálisan a elején a munkahelyi nap, amikor az ügyfelek igényei szerint növeli a Virtuálisgép-példányok számát. A munkahelyi nap végén automatikus skálázása minimálisra csökkentése érdekében erőforrásköltségek éjjelente Ha kevés a alkalmazások által használható Virtuálisgép-példányok számát.
- Ha egy részleg alkalmazás erősen használ, a hónap vagy pénzügyi ciklus egyes részeit, automatikus méretezése Virtuálisgép-példányok a további igényeinek megfelelően.
- Ha egy marketing esemény, az előléptetés vagy a szünnap értékesítés, automatikusan skálázhatja a várható vásárlói kereslet Virtuálisgép-példányok számát. 


## <a name="next-steps"></a>További lépések
A következő eszközök egyikét gazdagépalapú mérőszámokat használó automatikus skálázási szabályokat hozhat létre:

- [Azure PowerShell](tutorial-autoscale-powershell.md)
- [Azure CLI](tutorial-autoscale-cli.md)
- [Az Azure-sablon](tutorial-autoscale-template.md)

Ez az Áttekintés részletes automatikus skálázási szabályok használatával horizontális skálázásra és növelheti vagy csökkentheti a *szám* a méretezési csoportban lévő Virtuálisgép-példányok beállítása. Is skálázhatja vertikálisan növelése vagy csökkentése érdekében a Virtuálisgép-példány *mérete*. További információkért lásd: [vertikális automatikus méretezés a Virtual Machine Scale sets](virtual-machine-scale-sets-vertical-scale-reprovision.md).

A Virtuálisgép-példányok kezeléséről további információért lásd: [kezelése virtuális gép méretezési csoportok az Azure PowerShell-lel](virtual-machine-scale-sets-windows-manage.md).

Riasztást generál, ha az automatikus méretezési szabályok eseményindító kezelésével kapcsolatos információkért lásd: [küldése e-mailt és webhookot riasztási értesítéseket az Azure Monitor automatikus skálázási műveletek használatával](../azure-monitor/platform/autoscale-webhook-email.md). Emellett [használati naplók küldése e-mailt és webhookot riasztási értesítéseket az Azure monitorban](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md).

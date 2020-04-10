---
title: Az Azure-beli virtuálisgép-méretezési csoportokkal végzett automatikus skálázás áttekintése
description: Ismerje meg, hogy milyen különböző módokon skálázhatja automatikusan az Azure virtuálisgép-méretezési készletet teljesítmény vagy rögzített ütemezés alapján
author: ju-shim
tags: azure-resource-manager
ms.assetid: d29a3385-179e-4331-a315-daa7ea5701df
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: jushiman
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8b3af40de107fd9273ec50425c58afc9772ea426
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010341"
---
# <a name="overview-of-autoscale-with-azure-virtual-machine-scale-sets"></a>Az Azure-beli virtuálisgép-méretezési csoportokkal végzett automatikus skálázás áttekintése
Az Azure virtuálisgép-méretezési csoport automatikusan növelheti vagy csökkentheti az alkalmazást futtató virtuálisgép-példányok számát. Ez az automatizált és rugalmas viselkedés csökkenti a felügyeleti terhelést az alkalmazás teljesítményének figyeléséhez és optimalizálásához. Olyan szabályokat hozhat létre, amelyek meghatározzák a pozitív felhasználói élmény elfogadható teljesítményét. A megadott küszöbértékek elérésekor az automatikus skálázási szabályok lépéseket tesznek a méretezési készlet kapacitásának módosítására. Az eseményeket úgy is ütemezheti, hogy automatikusan növeljék vagy csökkentsék a méretezési készlet kapacitását rögzített időpontokban. Ez a cikk áttekintést nyújt arról, hogy mely teljesítménymutatók érhetők el, és milyen műveleteket hajthat végre az automatikus skálázás.


## <a name="benefits-of-autoscale"></a>Az automatikus skálázás előnyei
Az alkalmazás növekvő igényeivel párhuzamosan a méretezési csoportban lévő virtuálisgép-példányok terhelése is nő. Ha a megnövekedett terhelés állandó, nem csak pillanatnyi igény, akkor megadhatja, hogy az automatikus skálázási szabály növelje meg a virtuálisgép-példányok számát a méretezési csoportban.

Ezen virtuálisgép-példányok létrehozását és az alkalmazások telepítését követően a méretezési csoport megkezdi a forgalom elosztását közöttük a terheléselosztón keresztül. Szabályozhatja, hogy milyen metrikákat kell figyelni, például a CPU vagy a memória, mennyi ideig kell az alkalmazás terhelése egy adott küszöbértéket, és hány virtuálisgép-példányok hozzáadni a méretezési csoporthoz.

Az este vagy a hétvége folyamán az alkalmazás igényei csökkenhetnek. Ha a csökkent terhelés egy adott időtartam alatt állandó, akkor megadhatja, hogy az automatikus skálázási szabály csökkentse a virtuálisgép-példányok számát a méretezési csoportban. A horizontális leskálázási művelet csökkenti a méretezési csoport futtatásának költségeit, mivel csak az aktuális igényt kielégítő számú példányt futtat.


## <a name="use-host-based-metrics"></a>Gazdagépalapú mérőszámok használata
Automatikus skálázási szabályokat hozhat létre, amelyek beépített gazdagép-metrikák érhetők el a virtuálisgép-példányok. A gazdagép-metrikák betekintést nyújtanak a virtuálisgép-példányok teljesítményébe egy méretezési csoportban anélkül, hogy további ügynököket és adatgyűjtéseket kellene telepítenie vagy konfigurálnia. A metrikákat használó automatikus skálázási szabályok a processzorhasználat, a memóriaigény vagy a lemezhozzáférés válaszul a virtuálisgép-példányok számával horizontálisan kiméretezhetők.

A gazdagépalapú mérőszámokat használó automatikus méretezési szabályok a következő eszközökkel hozhatók létre:

- [Azure Portal](virtual-machine-scale-sets-autoscale-portal.md)
- [Azure PowerShell](tutorial-autoscale-powershell.md)
- [Azure CLI](tutorial-autoscale-cli.md)
- [Azure-sablon](tutorial-autoscale-template.md)

Részletesebb teljesítménymutatókat használó automatikus skálázási szabályok létrehozásához [telepítheti és konfigurálhatja az Azure diagnosztikai bővítményt](#in-guest-vm-metrics-with-the-azure-diagnostics-extension) virtuálisgép-példányokon, vagy [konfigurálhatja az alkalmazás használatát az App Insights használatával.](#application-level-metrics-with-app-insights)

Automatikus skálázási szabályok, amelyek gazdagép-alapú metrikák, vendéges virtuális gép metrikák az Azure diagnosztikai bővítmény, és az App Insights a következő konfigurációs beállításokat használhatja.

### <a name="metric-sources"></a>Metrikus források
Az automatikus skálázási szabályok a következő forrásokból származó mérőszámokat használhatnak:

| Metrikaforrás        | Használati eset                                                                                                                     |
|----------------------|------------------------------------------------------------------------------------------------------------------------------|
| Aktuális méretezési készlet    | Olyan gazdagépalapú metrikák esetén, amelyek nem igényelnek további ügynököket telepíteni vagy konfigurálni.                                  |
| Tárfiók      | Az Azure diagnosztikai bővítmény teljesítménymetrikákat ír az Azure storage-ba, amely et aztán automatikus skálázási szabályok aktiválásához használ. |
| Service Bus-üzenetsor    | Az alkalmazás vagy más összetevők üzeneteket továbbíthatnak az Azure Service Bus várólistán a szabályok aktiválásához.                   |
| Application Insights | Az alkalmazásba telepített instrumentation csomag, amely közvetlenül az alkalmazásból továbbítja a mutatókat.                         |


### <a name="autoscale-rule-criteria"></a>Automatikus skálázási szabály feltételei
A következő gazdagép-alapú metrikák állnak rendelkezésre az automatikus skálázási szabályok létrehozásakor. Ha az Azure diagnosztikai bővítmény vagy az App Insights, megadhatja, hogy mely metrikák figyelése és használata automatikus skálázási szabályok.

| Metrika neve               |
|---------------------------|
| Százalékos processzorhasználat            |
| Bejövő hálózat                |
| Kimenő hálózat               |
| Lemezolvasási bájtok           |
| Lemezírási bájtok          |
| Lemezolvasási műveletek/mp  |
| Lemezírási műveletek/mp |
| Fennmaradó CPU-kreditek     |
| Cpu-kreditek felhasznált      |

Amikor automatikus skálázási szabályokat hoz létre egy adott metrika figyelésére, a szabályok az alábbi metrikák összesítési műveletek egyikét vizsgálják:

| Összesítés típusa |
|------------------|
| Átlag          |
| Minimális          |
| Maximum          |
| Összesen            |
| Utolsó             |
| Darabszám            |

Az automatikus skálázási szabályok akkor aktiválódnak, amikor a metrikákat összehasonlítja a megadott küszöbértékkel a következő operátorok egyikével:

| Művelet                 |
|--------------------------|
| Nagyobb, mint             |
| Nagyobb vagy egyenlő |
| Kisebb mint                |
| Kisebb vagy egyenlő    |
| Egyenlő                 |
| Nem egyenlő             |


### <a name="actions-when-rules-trigger"></a>Műveletek, amikor a szabályok aktiválódnak
Automatikus skálázási szabály aktiválásakor a méretezési készlet automatikusan skálázható az alábbi módok egyikével:

| Méretezési művelet     | Használati eset                                                                                                                               |
|---------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| Szám növelése a következővel   | A létrehozandó virtuálisgép-példányok rögzített száma. Kisebb számú virtuális géptel rendelkező méretezési készletekben hasznos.                                           |
| Százalékos növekedés | A virtuálisgép-példányok százalékos alapú növekedése. Jó nagyobb méretű készletek, ahol a fix növekedés nem észrevehetően javítja a teljesítményt. |
| Szám növelése   | Hozzon létre annyi virtuálisgép-példányszükséges a kívánt maximális összeg eléréséhez.                                                            |
| Szám csökkentése a következővel   | Az eltávolítandó virtuálisgép-példányok rögzített száma. Kisebb számú virtuális géptel rendelkező méretezési készletekben hasznos.                                           |
| Százalékcsökkentés | A virtuálisgép-példányok százalékos alapú csökkenése. Jó a nagyobb méretezési csoportok, ahol a fix növekedés nem észrevehetően csökkenti az erőforrás-felhasználás és a költségek. |
| A szám csökkentése   | Távolítsa el annyi virtuálisgép-példány szükséges, hogy elérje a kívánt minimális összeget.                                                            |


## <a name="in-guest-vm-metrics-with-the-azure-diagnostics-extension"></a>Vendég virtuális gép metrikái az Azure diagnosztikai bővítmény
Az Azure diagnosztikai bővítmény egy ügynök, amely egy virtuális gép példányon belül fut. Az ügynök figyeli és menti a teljesítménymutatókat az Azure storage-ba. Ezek a teljesítménymutatók részletesebb információkat tartalmaznak a virtuális gép állapotáról, például a lemezek *átlagos readtime-ja* vagy a *PercentIdleTime* a CPU-hoz. Automatikus skálázási szabályokat hozhat létre a virtuális gép teljesítményének részletesebb ismertségét, nem csak a CPU-használat vagy a memóriafelhasználás százalékos arányát.

Az Azure diagnosztikai bővítmény használatához létre kell hoznia az Azure storage-fiókokat a virtuálisgép-példányok, telepítse az Azure diagnosztikai ügynök, majd konfigurálja a virtuális gépek et, hogy a tárfiókadott teljesítményszámlálók streamelése.

További információt az Azure diagnosztikai bővítmény [Linux virtuális gépen](../virtual-machines/extensions/diagnostics-linux.md) vagy [Windows virtuális gépen](../virtual-machines/extensions/diagnostics-windows.md) való engedélyezésével kapcsolatos cikkekben talál.


## <a name="application-level-metrics-with-app-insights"></a>Alkalmazásszintű mérőszámok az App Insights segítségével
Az alkalmazások teljesítményének jobb láthatósága érdekében használhatja az Application Insightsot. Egy kis instrumentációs csomagot telepít az alkalmazásba, amely figyeli az alkalmazást, és telemetriai adatokat küld az Azure-ba. Figyelheti a metrikákat, például az alkalmazás válaszidejét, az oldal betöltési teljesítményét és a munkamenetek számát. Ezek az alkalmazásmetrikák segítségével hozhat létre automatikus skálázási szabályokat egy részletes és beágyazott szinten, ahogy a szabályok aktiválása alapján végrehajtható információk, amelyek hatással lehetnek az ügyfélélményt.

Az App Insights programmal kapcsolatos további információért lásd: [Mi az Application Insights?](../azure-monitor/app/app-insights-overview.md).


## <a name="scheduled-autoscale"></a>Ütemezett automatikus skálázás
Ütemezések alapján automatikus skálázási szabályokat is létrehozhat. Ezek az ütemezés-alapú szabályok lehetővé teszik, hogy automatikusan méretezze a virtuálisgép-példányok rögzített időpontokban. A teljesítményalapú szabályok, teljesítményhatással lehet az alkalmazásra, mielőtt az automatikus skálázási szabályok eseményindító és az új virtuálisgép-példányok kivannak építve. Ha előre tudja látni az ilyen igényeket, a további virtuálisgép-példányok ki vannak építve, és készen állnak a további ügyfél-használatra és alkalmazásigényre.

A következő példák olyan esetek, amelyek az ütemezésalapú automatikus skálázási szabályok használatára válhatnak:

- Automatikusan horizontális felskálázás a virtuálisgép-példányok száma a munkanap kezdetén, amikor az ügyfél igénye nő. A munkanap végén automatikusan méretezze a virtuálisgép-példányok számát, hogy minimalizálja az erőforrásköltségeket egyik napról a másikra, ha az alkalmazás használata alacsony.
- Ha egy részleg a hónap vagy a pénzügyi ciklus bizonyos részein erősen használ egy alkalmazást, automatikusan méretezze a virtuálisgép-példányok számát a további igényeknek megfelelően.
- Ha van egy marketingesemény, promóció vagy üdülési értékesítés, automatikusan skálázhatja a virtuálisgép-példányok számát a várható ügyféligények előtt. 


## <a name="next-steps"></a>További lépések
Automatikus skálázási szabályokat hozhat létre, amelyek gazdagépalapú metrikákat használnak az alábbi eszközök egyikével:

- [Azure PowerShell](tutorial-autoscale-powershell.md)
- [Azure CLI](tutorial-autoscale-cli.md)
- [Azure-sablon](tutorial-autoscale-template.md)

Ez az áttekintés részletesen ismerteti, hogyan használhatja az automatikus skálázási szabályok horizontális méretezése és a virtuálisgép-példányok *számának* növelése vagy csökkentése a méretezési csoportban. Függőlegesen is méretezheti a virtuálisgép-példány *méretét.* További információ: [Vertical autoscale with Virtual Machine Scale sets](virtual-machine-scale-sets-vertical-scale-reprovision.md).

A virtuálisgép-példányok kezeléséről a [Virtuálisgép-méretezési csoportok kezelése az Azure PowerShell használatával című](virtual-machine-scale-sets-windows-manage.md)témakörben talál további információt.

Ha meg szeretné tudni, hogyan hozhat létre riasztásokat, amikor az automatikus skálázási szabályok aktiválódnak, olvassa [el az Automatikus skálázási műveletek használata e-mail- és webhook-riasztási értesítések küldéséhez az Azure Monitorban.](../azure-monitor/platform/autoscale-webhook-email.md) A [naplónaplók segítségével e-maileket és webhook-riasztásokat is küldhet az Azure Monitorban.](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md)

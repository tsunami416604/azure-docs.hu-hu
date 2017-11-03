---
title: "Az Azure virtuálisgép-méretezési csoportok automatikusan skálázva áttekintése |} Microsoft Docs"
description: "A különböző módjait, hogy egy Azure virtuálisgép-méretezési beállítása alapján, a teljesítményre vagy meghatározott ütemezés szerint automatikusan méretezheti"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: d29a3385-179e-4331-a315-daa7ea5701df
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 868523a3aca441a47218297be2ce9f9e46dd84a1
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/20/2017
---
# <a name="overview-of-autoscale-with-azure-virtual-machine-scale-sets"></a>Beállítja az Azure virtuálisgép-méretezési automatikusan skálázva áttekintése
Egy Azure virtuálisgép-méretezési csoport automatikusan növelhető és csökkenthető az alkalmazást futtató Virtuálisgép-példányok számát. Automatizált és rugalmas mindez csökkenti a felügyeleti figyelését, valamint az alkalmazás teljesítményének optimalizálásához. Egy pozitív felhasználói élmény a minimálisan elfogadható teljesítményt meghatározó szabályok létrehozása. Ha meghatározott küszöbértékeket mindegyike teljesül, az automatikus skálázási szabályok úgy, hogy a kapacitás, a méretezési művelet igénybe vehet. Események megadásával automatikusan növelhető vagy csökkentse a kapacitás, a méretezési rögzített többször is ütemezhető. Ez a cikk ismerteti, amelyek metrikák érhetők el áttekintése és milyen műveletek automatikus skálázás hajthat végre.


## <a name="benefits-of-autoscale"></a>Automatikus skálázás előnyei
Az alkalmazás igény szerinti egyenes arányban növekszik, ha a Virtuálisgép-példány a skála terhelését növeli állítsa be. Ha ez a megnövekedett terhelés egységes, ahelyett, hogy csak egy rövid igény szerinti, konfigurálhatja az automatikus skálázási szabályok a méretezési csoportban lévő Virtuálisgép-példányok számának növeléséhez.

Ezek a Virtuálisgép-példányok jönnek létre, és az alkalmazások vannak telepítve, a méretezési hozzákezd terjesztése azokra a terheléselosztó forgalmát. Milyen metrikák figyeléséhez, mint a CPU vagy memória, mennyi ideig alkalmazásterhelés meg kell felelnie a megadott küszöbértéket, és hány Virtuálisgép-példányok a skála hozzáadása set szabályozhatja.

Egy este vagy hétvégi az alkalmazás igény szerinti csökkenhet. Ha egy meghatározott időtartamra vonatkozóan ez csökkentheti a betöltés egységes, konfigurálhatja az automatikus skálázási szabályok segítségével csökkentheti a méretezési csoportban lévő Virtuálisgép-példányok számát. A skálázási művelet csökkenti a költségeket a méretezési készletben a futtatásakor csak az aktuális igény kielégítésére szükséges példányok futtatásához.


## <a name="use-host-based-metrics"></a>Gazdagép-alapú metrikák használata
A Virtuálisgép-példányok az automatikus skálázási szabályok adott beépített állomás voltak elérhetők metrikák hozhat létre. Gazdagép-metrikák biztosítanak a Virtuálisgép-példányok teljesítményének láthatósága egy méretezési nem telepítéséhez és konfigurálásához további ügynökök és az adatok gyűjteményeket kell beállítani. Kimenő, vagy a CPU kihasználtsága, a memória igény szerint vagy a lemezekhez való hozzáférés Virtuálisgép-példányok száma, amelyek ezeket a mérési szabályok automatikus skálázási méretezhető.

A gazdagépalapú mérőszámokat használó automatikus méretezési szabályok a következő eszközökkel hozhatók létre:

- [Azure Portal](virtual-machine-scale-sets-autoscale-portal.md)
- [Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md)
- [Azure CLI 2.0](virtual-machine-scale-sets-autoscale-cli.md)

Részletesebb teljesítménymutatók használó automatikus skálázási szabályok létrehozására lehet [telepítése és konfigurálása az Azure diagnostics bővítmény](#in-guest-vm-metrics-with-the-azure-diagnostics-extension) a Virtuálisgép-példányok vagy [konfigurálhatja az alkalmazás App Insights](#application-level-metrics-with-app-insights).

A Vendég virtuális gép metrikák az Azure diagnosztikai-bővítményt, és App Insights állomásalapú metrikák használó automatikus skálázási szabályok, használhatja a következő konfigurációs beállításokat.

### <a name="metric-sources"></a>Metrika források
Automatikus skálázási szabályok a következő forrásokból származó metrikák használatával:

| Mérték forrása        | Használati eset                                                                                                                     |
|----------------------|------------------------------------------------------------------------------------------------------------------------------|
| Aktuális méretezési csoport    | A gazdagép-alapú metrikákat, nincs szükség további ügynökök telepítve vagy konfigurálva.                                  |
| Tárfiók      | Az Azure diagnosztikai bővítmény teljesítménymutatók ír majd felhasznált való automatikus skálázási szabályok az Azure storage. |
| Service Bus-üzenetsorba    | Az alkalmazás vagy többi összetevő tud továbbítani több, az Azure Service Bus-üzenetsorba, eseményindító szabályok üzeneteket.                   |
| Application Insights | Egy instrumentation csomagot, az alkalmazás, amely az adatfolyamokat, közvetlenül az alkalmazásból metrikák telepítve.                         |


### <a name="autoscale-rule-criteria"></a>Automatikus skálázási szabály feltételei
A következő állomásalapú metrikák használhatók automatikus skálázási szabályok létrehozásakor. Ha az Azure diagnosztikai bővítmény vagy App Insights használ, mely metrikák figyelése és az automatikus skálázási szabályok használata határozza meg.

| Metrika neve               |
|---------------------------|
| Processzor-százalékos aránya            |
| A hálózati                |
| Kimenő hálózati               |
| Lemez olvasott bájtok           |
| Lemez írt bájtok száma          |
| Lemezolvasási műveletek másodpercenkénti száma  |
| Lemez írási művelet/mp |
| Fennmaradó CPU kreditek     |
| Felhasznált CPU kreditek      |

Egy metrika figyelése automatikus skálázási szabályok létrehozásakor a szabályok nézze meg a következő metrikák összesítési műveletek egyikét:

| Aggregáció típusa |
|------------------|
| Átlagos          |
| Minimális          |
| Maximális          |
| Összes            |
| utolsó             |
| Darabszám            |

Az automatikus skálázási szabályok majd által kiváltott, ha a metrikák összehasonlítja a megadott küszöbértéket meghaladó valamelyik az alábbi műveleteket:

| Operátor                 |
|--------------------------|
| Nagyobb mint             |
| Nagyobb vagy egyenlő |
| Kisebb mint                |
| Kisebb vagy egyenlő, mint    |
| Egyenlő                 |
| Nem egyenlő             |


### <a name="actions-when-rules-trigger"></a>Amikor a szabályok indítás, műveletek
Ha az automatikus skálázási szabály eseményindítók, a méretezési automatikusan át tudja méretezni a következő módszerek valamelyikével:

| Skálázási művelet     | Használati eset                                                                                                                               |
|---------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| Növelje a száma szerint   | Virtuálisgép-példányok létrehozása rögzített száma. Hasznos a virtuális gépek kisebb számú méretezési készlet.                                           |
| Növekmény százaléka alapján | Virtuálisgép-példányok százalékos alapú növelését. Jó nagyobb bővített állítja be, amelyben a rögzített növelését nem feltétlenül javíthatja a teljesítményt. |
| Növelje a számát, hogy   | Hozzon létre több Virtuálisgép-példányok szükséges elérni a kívánt maximális memóriamennyiséget.                                                            |
| Csökkentse a számát, hogy   | Távolítsa el a Virtuálisgép-példányok rögzített száma. Hasznos a virtuális gépek kisebb számú méretezési készlet.                                           |
| Százalék által csökkentése | Virtuálisgép-példányok százalékos alapú csökken. Jó nagyobb bővített állítja be, amelyben a rögzített növelését előfordulhat, hogy nem feltétlenül erőforrás-felhasználás és költségek csökkentése érdekében. |
| Csökkentse a számát, hogy   | Távolítsa el a Virtuálisgép-példányok kívánt minimális eléréséhez szükséges.                                                            |


## <a name="in-guest-vm-metrics-with-the-azure-diagnostics-extension"></a>A Vendég virtuális gép metrikák az Azure diagnostics-bővítmény
Az Azure diagnostics kiterjesztés olyan ügynök, amely belül egy Virtuálisgép-példány fut. Az ügynök figyeli, és az Azure storage teljesítménymutatók menti. A metrikák tartalmazhat részletesebben olvashat a virtuális gép állapotát, például *AverageReadTime* lemezek vagy *PercentIdleTime* processzor. Létrehozhat egy részletesebb tájékoztatást nyújthatnak a virtuális gép teljesítményét, nem csak a használati vagy memória processzorhasználat százalékos értéke alapján automatikus skálázási szabályok.

Az Azure diagnostics-bővítmény használatához meg kell az Azure storage-fiókok létrehozása a Virtuálisgép-példányok, az Azure diagnosztikai ügynök telepítése, majd adatfolyam egyes teljesítményszámlálókat a tárfiók a virtuális gép konfigurálása.

További információt az Azure diagnosztikai bővítmény [Linux virtuális gépen](../virtual-machines/linux/diagnostic-extension.md) vagy [Windows virtuális gépen](../virtual-machines/windows/ps-extensions-diagnostics.md) való engedélyezésével kapcsolatos cikkekben talál.


## <a name="application-level-metrics-with-app-insights"></a>App Insights alkalmazás szintű metrikák
Ahhoz, hogy az alkalmazások teljesítménye további láthatóságot, használhatja az Application insights szolgáltatással. Egy kis instrumentation csomag telepítése az alkalmazásban, az alkalmazás figyeli, és telemetriai adatokat küld az Azure-bA. Hatékonyan nyomon követheti a válaszidők az alkalmazás, a lap betöltése teljesítményét, például metrikákat, és a munkamenet-adatokra. Alkalmazás metrikákat, indít el a szabályokat, amely hatással lehet a felhasználói élmény gyakorlatban használható elemzések alapján automatikus skálázási szabályok létrehozásához a részletes és beágyazott szinten használható.

Az App Insights programmal kapcsolatos további információért lásd: [Mi az Application Insights?](../application-insights/app-insights-overview.md).


## <a name="scheduled-autoscale"></a>Ütemezett automatikus skálázás
Ütemezés alapján automatikus skálázás szabályokat is létrehozhat. Ütemezésalapú szabályok automatikus méretezése a Virtuálisgép-példányok száma rögzített alkalommal lehetővé teszik. A teljesítmény-alapú szabályokat lehet a teljesítményre gyakorolt hatás az automatikus skálázási szabályok eseményindító előtt az alkalmazást, és az új Virtuálisgép-példányok törlődnek. Ilyen igény szerint is várhatóan, a további Virtuálisgép-példányok esetén kiosztott és készen áll a további használatát és az alkalmazás keresletének.

A következő példák előfordulhat, hogy ütemezés alapján automatikus skálázási szabályok használatának előnyeit kihasználó forgatókönyvek:

- Ha növeli a kereslet munkanap elején Virtuálisgép-példányok száma automatikusan kiterjesztése. A munkahelyi nap végén automatikus méretezése minimálisra csökkentése érdekében erőforrás költségek éjszaka alacsony alkalmazás használata esetén a Virtuálisgép-példányok számát.
- Ha egy osztály az alkalmazást, a hónap vagy pénzügyi ciklus fokozottan használó, automatikus méretezése a további iránti igények kielégítése érdekében olyan Virtuálisgép-példányok számát.
- Ha van egy marketing esemény, előléptetést vagy szünnap pénztári automatikusan méretezheti előre várható keresletének Virtuálisgép-példányok számát. 


## <a name="next-steps"></a>Következő lépések
Az alábbi eszközök egyikével állomásalapú metrikák használó automatikus skálázási szabályok hozhat létre:

- [Azure Portal](virtual-machine-scale-sets-autoscale-portal.md)
- [Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md)
- [Azure CLI 2.0](virtual-machine-scale-sets-autoscale-cli.md)

Ez az Áttekintés részletes automatikus skálázási szabályok segítségével horizontálisan méretezhető és növeli vagy csökkenti a *szám* állítsa be a skálázási Virtuálisgép-példánya. Is méretezheti függőleges növelheti vagy csökkentheti a Virtuálisgép-példány *mérete*. További információkért lásd: [virtuálisgép-méretezési csoportok függőleges skálázva](virtual-machine-scale-sets-vertical-scale-reprovision.md).

A Virtuálisgép-példányok kezeléséről további információért lásd: [kezelése virtuálisgép-méretezési beállítja az Azure PowerShell](virtual-machine-scale-sets-windows-manage.md).

Az automatikus skálázási szabályok eseményindító riasztást generáljon, lásd: [automatikus skálázási műveletek segítségével e-mailek és a webhook riasztási értesítések küldése az Azure-figyelő](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md). Emellett [használata naplókat küldeni e-mailek és a webhook riasztási értesítések az Azure-figyelő](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md).
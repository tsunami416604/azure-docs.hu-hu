---
title: Függőségelemzés az Azure Áttelepítési kiszolgáló értékelésében
description: Bemutatja, hogyan használhatja a függőségi elemzést az Azure Áttelepítési kiszolgáló értékelése használatával.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: f0b956620895ae2264b53916015d440f5e586eb2
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024761"
---
# <a name="dependency-analysis"></a>Függőségi elemzés

Ez a cikk az Azure Áttelepítés:Kiszolgáló értékelése függőségi elemzést ismerteti.

## <a name="overview"></a>Áttekintés

A függőségi elemzés segítségével azonosíthatja a helyszíni gépek közötti függőségeket, amelyeket fel szeretne mérni, és át szeretne térni az Azure-ba. 

- Az Azure Migrate:Server Assessment, gépeket gyűjt egy csoportba, majd értékelje a csoportot. Függőségi elemzés segít a gépek csoportosítása pontosabban, nagy megbízhatósággal az értékeléshez.
- A függőségi elemzés lehetővé teszi az együtt áttelepítendő gépek azonosítását. Azonosíthatja, hogy a gépek használatban vannak-e, vagy az áttelepítés helyett leszerelhetők.You can identify whether machines are in use, or if they can be decommissioned instead remigrated.
- A függőségek elemzése segít biztosítani, hogy semmi sem maradjon hátra, és elkerülheti a váratlan kimaradásokat a migráció során.
- Az elemzés különösen akkor hasznos, ha nem biztos abban, hogy a gépek egy azure-ba áttelepíteni kívánt alkalmazásközponti telepítés részét képezik-e.
- [Tekintse át](common-questions-discovery-assessment.md#what-is-dependency-visualization) a függőségelemzéssel kapcsolatos gyakori kérdéseket.

A függőségelemzés üzembe helyezésének két lehetősége van.

- **Ügynökalapú**: Ügynök-alapú függőségelemzés megköveteli, hogy az ügynökök minden olyan helyszíni gépen telepítve legyen, amelyet elemezni szeretne.
- **Ügynök nélküli:** Ügynök nélküli elemzéssel nem kell ügynököket telepítenie azátveire kívánt gépekre. Ez a beállítás jelenleg előzetes verzióban érhető el, és csak vmware virtuális gépek esetén érhető el.

> [!NOTE]
> Az ügynökalapú függőségi elemzés nem érhető el az Azure Government ben. Használhatja ügynök nélküli függőségi elemzés.

## <a name="agentless-analysis"></a>Ügynök nélküli elemzés

Az ügynök nélküli függőségelemzés úgy működik, hogy rögzíti a TCP-kapcsolat adatait olyan gépekről, amelyekhez engedélyezve van. Nincsenek ügynökök telepítve az elemezni kívánt gépeken.

### <a name="collected-data"></a>Összegyűjtött adatok

A függőségfelderítés megkezdése után a készülék ötpercenként lekérdezi a gépek adatait az adatok összegyűjtéséhez. Ezeket az adatokat a vendég virtuális gépekről a vCenter Server en keresztül gyűjtjük, vSphere API-k használatával. Az összegyűjtött adatok feldolgozása az Azure Migrate készülék, az identitásadatok kikövetkeztetése, és hatóránként küldi el az Azure Migrate.

A lekérdezés gépekről gyűjti ezeket az adatokat: 
- Az aktív kapcsolatokkal működő folyamatok neve.
- Az aktív kapcsolattal működő folyamatokat futó alkalmazás neve.
- Célport az aktív kapcsolatokon.

## <a name="agent-based-analysis"></a>Ügynök-alapú elemzés

Ügynökalapú elemzéshez a Kiszolgálói értékelés az Azure Monitor [Szolgáltatástérkép-megoldását](../azure-monitor/insights/service-map.md) használja a függőségi vizualizáció és elemzés engedélyezéséhez. A [Microsoft Monitoring Agent/Log Analytics ügynök](../azure-monitor/platform/agents-overview.md#log-analytics-agent) és a [függőségi ügynök](../azure-monitor/platform/agents-overview.md#dependency-agent), telepíteni kell minden gépen elemezni szeretné.

### <a name="collected-data"></a>Összegyűjtött adatok

Az ügynökalapú elemzéshez a következő adatokat gyűjtjük:

- A forrásgép kiszolgálójának neve, folyamata, alkalmazásneve.
- A célszámítógép kiszolgálójának neve, folyamata, alkalmazásneve és portja.
- A kapcsolatok száma, a késés és az adatátviteli adatok összegyűjtése és elérhető log analytics-lekérdezések. 


## <a name="compare-agentless-and-agent-based"></a>Ügynök nélküli és ügynökalapú összehasonlítása

Az ügynök nélküli vizualizáció és az ügynökalapú megjelenítés közötti különbségeket a táblázat foglalja össze.

**Követelmény** | **Ügynök nélküli** | **Ügynök-alapú**
--- | --- | ---
Támogatás | Ez a beállítás jelenleg előzetes verzióban érhető el, és csak vmware virtuális gépek esetén érhető el. [Tekintse át a](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) támogatott operációs rendszereket. | Általános anamvaló (GA).
Ügynök | Nem kell ügynököket telepíteni azátveire telepíteni kívánt gépekre. | Az elemezni kívánt minden helyszíni gépen telepítendő ügynökök: A [Microsoft Monitoring ügynök (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)és a [Függőségi ügynök](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent). 
Log Analytics | Nem szükséges. | Az Azure Migrate a [Service Map-megoldást](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) használja az [Azure Monitor naplóiban](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) a függőségelemzéshez. 
Működés | A tcp-kapcsolat adatait rögzíti a függőségi megjelenítéshez engedélyezett gépeken. A felderítés után ötperces időközönként gyűjti az adatokat. | A számítógépre telepített Szolgáltatástérkép-ügynökök adatokat gyűjtenek a TCP-folyamatokról és a bejövő/kimenő kapcsolatokról az egyes folyamatokhoz.
Adatok | A forrásgép kiszolgálójának neve, folyamata, alkalmazásneve.<br/><br/> A célszámítógép kiszolgálójának neve, folyamata, alkalmazásneve és portja. | A forrásgép kiszolgálójának neve, folyamata, alkalmazásneve.<br/><br/> A célszámítógép kiszolgálójának neve, folyamata, alkalmazásneve és portja.<br/><br/> A kapcsolatok száma, a késés és az adatátviteli adatok összegyűjtése és elérhető log analytics-lekérdezések. 
Vizualizáció | Az egykiszolgálós függőségi térkép egy óra és 30 nap között tekinthető meg. | Egyetlen kiszolgáló függőségi térképe.<br/><br/> A térkép csak egy óra alatt tekinthető meg.<br/><br/> Kiszolgálók egy csoportjának függőségi térképe.<br/><br/> A csoport kiszolgálóinak hozzáadása és eltávolítása a térképnézetből.
Adatexportálás | Jelenleg nem tölthető le táblázatos formátumban. | Az adatok lekérdezhetők a Log Analytics segítségével.



## <a name="next-steps"></a>További lépések
- Tekintse át a [VMware virtuális gépek,](migrate-support-matrix-vmware.md#agent-based-dependency-analysis-requirements)a [fizikai kiszolgálók](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements)és a [hyper-v virtuális gépek](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements)ügynökalapú elemzésének beállítására vonatkozó követelményeket.
- [Tekintse át](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) a VMware virtuális gépek ügynök nélküli elemzésére vonatkozó követelményeket.
- Ügynökalapú függőségi megjelenítés [beállítása](how-to-create-group-machine-dependencies.md)
- [Próbálja ki](how-to-create-group-machine-dependencies-agentless.md) a VMware virtuális gépek ügynök nélküli függőségi vizualizációjával.
- Tekintse át a függőségi vizualizációval [kapcsolatos gyakori kérdéseket.](common-questions-discovery-assessment.md#what-is-dependency-visualization)



---
title: Függőségek elemzése Azure Migrate kiszolgáló értékelése során
description: Ismerteti, hogyan használható a függőségi elemzés az értékeléshez Azure Migrate Server Assessment használatával.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: f0b956620895ae2264b53916015d440f5e586eb2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82024761"
---
# <a name="dependency-analysis"></a>Függőségek elemzése

Ez a cikk a függőségek elemzését ismerteti Azure Migrateban: kiszolgáló értékelése.

## <a name="overview"></a>Áttekintés

A függőségek elemzése segít azonosítani az Azure-ba felmérni és áttelepíteni kívánt helyszíni gépek közötti függőségeket. 

- Azure Migrate: a kiszolgáló értékelése során a gépeket egy csoportba gyűjti, majd értékeli a csoportot. A függőségek elemzésével pontosabban csoportosíthatja a gépeket, és magas megbízhatóságot biztosít az értékeléshez.
- A függőségek elemzése lehetővé teszi azoknak a számítógépeknek az azonosítását, amelyeket együtt kell áttelepíteni. Megadhatja, hogy a gépek használatban vannak-e, illetve hogy nem telepíthetők-e le Migrálás helyett.
- A függőségek elemzésével biztosítható, hogy a rendszer ne maradjon hátra, és elkerülje a kiesést az áttelepítés során.
- Az elemzés különösen akkor hasznos, ha nem biztos benne, hogy a gépek olyan alkalmazás-telepítés részét képezik-e, amelyet át szeretne telepíteni az Azure-ba.
- [Tekintse át](common-questions-discovery-assessment.md#what-is-dependency-visualization) a függőségek elemzésével kapcsolatos gyakori kérdéseket.

A függőségi elemzések üzembe helyezésére két lehetőség áll rendelkezésre.

- **Ügynök**alapú: az ügynök-alapú függőségek elemzése megköveteli, hogy az ügynökök az elemezni kívánt helyszíni gépekre legyenek telepítve.
- **Ügynök**nélkül: ügynök nélküli elemzéssel nem kell ügynököt telepítenie az áttekinteni kívánt gépekre. Ez a beállítás jelenleg előzetes verzióban érhető el, és csak a VMware virtuális gépekhez használható.

> [!NOTE]
> Az ügynök-alapú függőség elemzése nem érhető el Azure Governmentban. Az ügynök nélküli függőségek elemzését is használhatja.

## <a name="agentless-analysis"></a>Ügynök nélküli elemzés

Az ügynök nélküli függőség elemzése a TCP-kapcsolatok adatainak rögzítésével működik olyan gépekről, amelyeken engedélyezve van. Nincsenek ügynökök telepítve az elemezni kívánt gépeken.

### <a name="collected-data"></a>Összegyűjtött adatok

A függőségi felderítés elindítása után a készülék öt percenként lekérdezi az adatokat a gépekről az adatok összegyűjtéséhez. Ezeket az adatokat a vendég virtuális gépek a vSphere API-k használatával gyűjtik vCenter Serveron keresztül. Az összegyűjtött adatokat a rendszer a Azure Migrate berendezésen dolgozza fel, az azonosító adatok leforgása érdekében, és hat óránként Azure Migrate küldi el.

A lekérdezés összegyűjti ezeket az adatokat a gépekről: 
- Az aktív kapcsolatokkal rendelkező folyamatok neve.
- Az aktív kapcsolatokkal rendelkező folyamatokat futtató alkalmazás neve.
- Az aktív kapcsolatok célport.

## <a name="agent-based-analysis"></a>Ügynök-alapú elemzés

Az ügynök-alapú elemzéshez a Server Assessment a [Service Map megoldást](../azure-monitor/insights/service-map.md) használja Azure monitor a függőségi vizualizáció és az elemzés engedélyezéséhez. Az elemezni kívánt gépeken telepíteni kell a [Microsoft monitoring Agent/log Analytics ügynököt](../azure-monitor/platform/agents-overview.md#log-analytics-agent) és a [függőségi ügynököt](../azure-monitor/platform/agents-overview.md#dependency-agent).

### <a name="collected-data"></a>Összegyűjtött adatok

Az ügynök-alapú elemzések esetében a rendszer a következő adatokat gyűjti össze:

- Forrásoldali gép kiszolgálójának neve, folyamata, alkalmazás neve.
- Célszámítógép kiszolgálójának neve, folyamat, alkalmazás neve és port.
- A kapcsolatok száma, a késés és az adatátviteli adatok összegyűjtése és Log Analytics lekérdezések számára elérhetők. 


## <a name="compare-agentless-and-agent-based"></a>Ügynök nélküli és ügynök szerinti összehasonlítás

Az ügynök nélküli vizualizáció és az ügynök-alapú vizualizáció közötti különbségeket a táblázat foglalja össze.

**Követelmény** | **Ügynök nélküli** | **Ügynök-alapú**
--- | --- | ---
Támogatás | Ez a beállítás jelenleg előzetes verzióban érhető el, és csak a VMware virtuális gépekhez használható. [Tekintse át](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) a támogatott operációs rendszereket. | Általánosan elérhető (GA).
Ügynök | Nem kell telepítenie az ügynököket az áttekinteni kívánt gépekre. | Az elemezni kívánt helyszíni gépekre telepítendő ügynökök: a [Microsoft monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)és a [függőségi ügynök](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent). 
Log Analytics | Nem kötelező. | Azure Migrate a függőségi elemzéshez a [Service Map](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) megoldást használja [Azure monitor naplókban](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) . 
Működés | Rögzíti a TCP-kapcsolatok mennyiségét a függőségi vizualizációhoz engedélyezett gépeken. A felderítést követően öt percen belül gyűjti az adatokat. | A gépen telepített Service Map ügynökök gyűjtenek adatokat a TCP-folyamatokról és a bejövő/kimenő kapcsolatokról az egyes folyamatokhoz.
Adatok | Forrásoldali gép kiszolgálójának neve, folyamata, alkalmazás neve.<br/><br/> Célszámítógép kiszolgálójának neve, folyamat, alkalmazás neve és port. | Forrásoldali gép kiszolgálójának neve, folyamata, alkalmazás neve.<br/><br/> Célszámítógép kiszolgálójának neve, folyamat, alkalmazás neve és port.<br/><br/> A kapcsolatok száma, a késés és az adatátviteli adatok összegyűjtése és Log Analytics lekérdezések számára elérhetők. 
Vizualizáció | Az önálló kiszolgálók függőségi térképe egy óra és 30 nap közötti időtartamon belül megtekinthető. | Egyetlen kiszolgáló függőségi térképe.<br/><br/> A Térkép csak egy órán át jeleníthető meg.<br/><br/> Kiszolgálók egy csoportjának függőségi térképe.<br/><br/> Kiszolgálók hozzáadása és eltávolítása a Térkép nézetből.
Adatexportálás | Jelenleg nem tölthető le táblázatos formátumban. | Az adatlekérdezés a Log Analytics használatával kérdezhető le.



## <a name="next-steps"></a>További lépések
- Tekintse át a [VMWare virtuális gépek](migrate-support-matrix-vmware.md#agent-based-dependency-analysis-requirements), a [fizikai kiszolgálók](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements)és a [Hyper-V virtuális gépek](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements)ügynök-alapú elemzésének követelményeit.
- [Tekintse át](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) a VMWare virtuális gépek ügynök nélküli elemzésének követelményeit.
- Ügynök-alapú függőségi vizualizáció [beállítása](how-to-create-group-machine-dependencies.md)
- [Próbálja ki](how-to-create-group-machine-dependencies-agentless.md) az ügynök nélküli függőségi vizualizációt a VMWare virtuális gépekhez.
- Tekintse át a függőségi vizualizációval kapcsolatos [gyakori kérdéseket](common-questions-discovery-assessment.md#what-is-dependency-visualization) .



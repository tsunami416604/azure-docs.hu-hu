---
title: Függőségek vizualizációja az Azure Migrate-ben
description: Áttekintést nyújt az értékelési számításokról a Azure Migrate Server Assessment Service-ben
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: fd069ed98fa34fd6f281c98a061925f96c7bb2cd
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79269704"
---
# <a name="dependency-visualization"></a>Függőségek vizualizációja

Ez a cikk a függőségi vizualizációt ismerteti Azure Migrateban: kiszolgáló értékelése.

## <a name="what-is-dependency-visualization"></a>Mi a függőségi vizualizáció?

A függőségi vizualizáció segítségével azonosíthatja az Azure-ba felmérni és áttelepíteni kívánt helyszíni gépek közötti függőségeket. 

- Azure Migrate: a kiszolgáló értékelése során a gépeket egy csoportba gyűjti, majd értékeli a csoportot. A függőségi vizualizációk segítségével pontosabban csoportosíthatja a gépeket, és magas megbízhatóságot biztosít az értékeléshez.
- A függőségi vizualizáció lehetővé teszi azoknak a gépeknek az azonosítását, amelyeket együtt kell áttelepíteni. Megadhatja, hogy a gépek használatban vannak-e, illetve hogy nem telepíthetők-e le Migrálás helyett.
- A függőségek megjelenítésével gondoskodhat arról, hogy semmi ne maradjon hátra, és elkerülje a kiesést az áttelepítés során.
- A vizualizáció különösen akkor hasznos, ha nem biztos benne, hogy a gépek olyan alkalmazás-telepítés részét képezik-e, amelyet át szeretne telepíteni az Azure-ba.


> [!NOTE]
> A függőségi vizualizáció nem érhető el Azure Governmentban.

## <a name="agent-basedagentless-visualization"></a>Ügynök-alapú/ügynök nélküli vizualizáció

A függőségi vizualizáció üzembe helyezésének két lehetősége van:

- **Ügynök**alapú: az ügynök-alapú függőségi vizualizáció megköveteli, hogy az ügynökök az elemezni kívánt helyszíni gépekre legyenek telepítve.
- **Ügynök**nélkül: Ha ezt a lehetőséget választja, nem kell ügynököt telepítenie az átnézni kívánt gépekre. Ez a beállítás jelenleg előzetes verzióban érhető el, és csak a VMware virtuális gépekhez használható.


## <a name="agent-based-visualization"></a>Ügynök alapú vizualizáció

**Követelmény** | **Részletek** | **További információ**
--- | --- | ---
**Üzembe helyezés előtt** | Azure Migrate-projektet kell megadnia, és a Azure Migrate: kiszolgáló-értékelési eszközzel hozzáadva a projekthez.<br/><br/>  A függőségi vizualizációt egy Azure Migrate berendezés beállítása után telepítheti a helyszíni gépek felderítése érdekében. | [Ismerje meg, hogyan](create-manage-projects.md) hozhat létre egy projektet első alkalommal.<br/><br/> [Megtudhatja, hogyan](how-to-assess.md) adhat hozzá egy értékelési eszközt egy meglévő projekthez.<br/><br/> Ismerje meg, hogyan állíthatja be a Azure Migrate készüléket a [Hyper-V](how-to-set-up-appliance-hyper-v.md), [VMware](how-to-set-up-appliance-vmware.md)vagy fizikai kiszolgálók értékeléséhez.
**Szükséges ügynökök** | Telepítse az alábbi ügynököket minden egyes elemezni kívánt gépen:<br/><br/> A [Microsoft monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).<br/><br/> A [függőségi ügynök](../azure-monitor/platform/agents-overview.md#dependency-agent).<br/><br/> Ha a helyszíni gépek nem csatlakoznak az internethez, le kell töltenie és telepítenie kell Log Analytics-átjárót. | További információ a [függőségi ügynök](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) és az [MMA](how-to-create-group-machine-dependencies.md#install-the-mma)telepítéséről.
**Log Analytics** | A Azure Migrate a [Service Map](../operations-management-suite/operations-management-suite-service-map.md) megoldást használja [Azure monitor naplókban](../log-analytics/log-analytics-overview.md) a függőségi vizualizációhoz.<br/><br/> Új vagy meglévő Log Analytics munkaterületet társít egy Azure Migrate projekthez. Egy Azure Migrate projekt munkaterülete nem módosítható a hozzáadása után. <br/><br/> A munkaterületnek ugyanahhoz az előfizetéshez kell tartoznia, mint a Azure Migrate projektnek.<br/><br/> A munkaterületnek az USA keleti régiójában, Délkelet-Ázsiában vagy Nyugat-európai régióban kell lennie. Más régiókban lévő munkaterületek nem társíthatók projekthez.<br/><br/> A munkaterületnek olyan régióban kell lennie, amelyben a [Service Map támogatott](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites).<br/><br/> Log Analytics a Azure Migratehoz társított munkaterület az áttelepítési projekt kulcsával és a projekt nevével van megjelölve.
**Költségek** | Az Service Map-megoldás nem számít fel díjat az első 180 napra (az Log Analytics munkaterület Azure Migrate projekthez való hozzárendelésének napjától számítva)/<br/><br/> 180 nap után a standard Log Analytics díjak érvényesek lesznek.<br/><br/> A társított Log Analytics-munkaterületen a Service Maptól eltérő bármely megoldás használata esetén a Log Analytics [standard díjait](https://azure.microsoft.com/pricing/details/log-analytics/) kell fizetnie.<br/><br/> A Azure Migrate-projekt törlésekor a munkaterület nem törlődik vele együtt. A projekt törlését követően a Service Map használat nem ingyenes, és az egyes csomópontok a Log Analytics munkaterület fizetős szintjének megfelelően lesznek felszámítva.<br/><br/>Ha olyan projektekkel rendelkezik, amelyeket a Azure Migrate általános elérhetősége előtt hozott létre (GA-28. február 2018.), előfordulhat, hogy további Service Map díjat is felmerült. Ahhoz, hogy a fizetés csak 180 nap után legyen elérhető, javasoljuk, hogy hozzon létre egy új projektet, mivel a már meglévő munkaterületek továbbra is díjkötelesek.
**Felügyeleti** | Amikor ügynököt regisztrál a munkaterületre, a Azure Migrate-projekt által megadott azonosítót és kulcsot használja.<br/><br/> A Log Analytics munkaterületet Azure Migraten kívül is használhatja.<br/><br/> Ha törli a társított Azure Migrate projektet, a munkaterület nem törlődik automatikusan. [Törölje manuálisan](../azure-monitor/platform/manage-access.md).<br/><br/> Ne törölje a Azure Migrate által létrehozott munkaterületet, hacsak nem törli a Azure Migrate projektet. Ha így tesz, a függőségi vizualizáció funkció nem a várt módon fog működni.

## <a name="agentless-visualization"></a>Ügynök nélküli vizualizáció


**Követelmény** | **Részletek** | **További információ**
--- | --- | ---
**Üzembe helyezés előtt** | Azure Migrate-projektet kell megadnia, és a Azure Migrate: kiszolgáló-értékelési eszközzel hozzáadva a projekthez.<br/><br/>  A függőségi vizualizációt egy Azure Migrate berendezés beállítása után telepítheti a helyszíni VMWare-gépek felderítése érdekében. | [Ismerje meg, hogyan](create-manage-projects.md) hozhat létre egy projektet első alkalommal.<br/><br/> [Megtudhatja, hogyan](how-to-assess.md) adhat hozzá egy értékelési eszközt egy meglévő projekthez.<br/><br/> Ismerje meg, hogyan állíthatja be a Azure Migrate berendezést a [VMware](how-to-set-up-appliance-vmware.md) virtuális gépek kiértékeléséhez.
**Szükséges ügynökök** | Nem szükséges ügynök az elemezni kívánt gépeken.
**Támogatott operációs rendszerek** | Tekintse át az ügynök nélküli vizualizációhoz támogatott [operációs rendszereket](migrate-support-matrix-vmware.md#agentless-dependency-visualization) .
**Virtuális gépek** | **Vmware-eszközök**: az elemezni kívánt virtuális gépeken telepíteni és futtatni kell a VMware-eszközöket.<br/><br/> **Fiók**: a Azure Migrate készüléken hozzá kell adnia egy olyan felhasználói fiókot, amely használható a virtuális gépek elemzéshez való eléréséhez.<br/><br/> **Windows rendszerű virtuális gépek**: a felhasználói fióknak helyi vagy tartományi rendszergazdának kell lennie a gépen.<br/><br/> **Linuxos virtuális gépek**: a fiókhoz rendszergazdai jogosultság szükséges. A felhasználói fióknak ezt a két funkciót kell megadnia a/bin/netstat és a/bin/ls fájlokhoz: CAP_DAC_READ_SEARCH és CAP_SYS_PTRACE. | [Ismerkedjen meg](migrate-appliance.md) az Azure Migrate berendezéssel.
**VMware** | **vCenter**: a készüléknek szüksége van egy vCenter Server fiókra, amely csak olvasási hozzáféréssel rendelkezik, és Virtual Machines > vendég műveletekhez engedélyezett jogosultságokkal rendelkezik.<br/><br/> **ESXi-gazdagépek**: az elemezni kívánt virtuális gépeket futtató ESXi-gazdagépeken a Azure Migrate készüléknek képesnek kell lennie csatlakozni a 443-es TCP-porthoz.
**Összegyűjtött adatok** |  Az ügynök nélküli függőség elemzése úgy működik, hogy a TCP-kapcsolatok adatait olyan gépekről rögzíti, amelyeken engedélyezve van. A függőségi felderítés engedélyezése után a készülék 5 percenként gyűjti a TCP-kapcsolatok adatait a vendég virtuális gépekről. Ezek az adatok a vendég virtuális gépekről vCenter Server a vSphere API-k használatával kerülnek begyűjtésre. A rendszer feldolgozza az összegyűjtött adatokat a készüléken a függőségi információk leforgása és a Azure Migrate 6 óránként történő elküldéséhez. A következő adatokat gyűjti össze az egyes gépekről: <br/> – Aktív kapcsolatokkal rendelkező folyamatok neve.<br/> -Az aktív kapcsolatokkal rendelkező folyamatot futtató alkalmazások nevei.<br/> – A cél port az aktív kapcsolatokon.


## <a name="next-steps"></a>Következő lépések
- [Függőségi vizualizáció beállítása](how-to-create-group-machine-dependencies.md)
- [Próbálja ki az ügynök nélküli függőségi vizualizációt](how-to-create-group-machine-dependencies-agentless.md) a VMWare virtuális gépekhez.
- Tekintse át a függőségi vizualizációval kapcsolatos [gyakori kérdéseket](common-questions-discovery-assessment.md#what-is-dependency-visualization) .



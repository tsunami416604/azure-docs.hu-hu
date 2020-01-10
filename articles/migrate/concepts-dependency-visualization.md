---
title: Függőségek vizualizációja az Azure Migrate-ben
description: Áttekintést nyújt az értékelési számításokról a Azure Migrate Server Assessment Service-ben
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: hamusa
ms.openlocfilehash: 75b2120b9fef904114d532e83d571c08e1a1034d
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75772312"
---
# <a name="dependency-visualization"></a>Függőségek vizualizációja

Ez a cikk a függőségek vizualizációs szolgáltatását ismerteti Azure Migrate: Server Assessment.

A függőségi vizualizáció segítségével megismerheti a felmérni és áttelepíteni kívánt gépek közötti függőségeket. Jellemzően függőségi leképezést használ, ha a magasabb szintű megbízhatóságú gépeket szeretné felmérni.

- Azure Migrate: a kiszolgáló értékelése során összegyűjtheti a gépeket csoportokba az értékeléshez. A csoportok általában az áttelepíteni kívánt gépekből állnak, és a függőségek vizualizációja segít a számítógépek függőségeinek átadásában, hogy pontosan csoportosítsa a gépeket.
- A vizualizáció használatával felderítheti azokat az egymástól függő rendszereket, amelyeknek együtt kell áttelepíteniük. Meghatározhatja, hogy a futó rendszerek továbbra is használatban vannak-e, illetve hogy a rendszerek levonhatók-e az áttelepítés helyett.
- A függőségek megjelenítésével gondoskodhat arról, hogy semmi ne maradjon hátra, és elkerülje a kiesést az áttelepítés során.
- Ez a funkció különösen akkor hasznos, ha nem teljes mértékben ismeri az alkalmazások részét képező gépeket, ezért az Azure-ba kell migrálni.


> [!NOTE]
> A függőségi vizualizáció funkció Azure Governmentban nem érhető el.

## <a name="agent-based-and-agentless"></a>Ügynök-alapú és ügynök nélküli

A függőségi vizualizáció üzembe helyezésének két lehetősége van:

- **Ügynök nélküli függőségi vizualizáció**: Ez a beállítás jelenleg előzetes verzióban érhető el, és csak a VMWare virtuális gépek esetében érhető el. Nem szükséges ügynököket telepíteni a gépekre. 
    - Úgy működik, hogy rögzíti a TCP-kapcsolatok adatait azokról a gépekről, amelyeken engedélyezve van. [További információk](how-to-create-group-machine-dependencies-agentless.md).
A függőségi felderítés elindítása után a készülék öt perces lekérdezési időköz alapján gyűjt adatokat a gépekről.
    - A rendszer a következő adatokat gyűjti össze:
        - TCP-kapcsolatok
        - Aktív kapcsolattal rendelkező folyamatok nevei
        - A fenti folyamatokat futtató telepített alkalmazások nevei
        - Nem. az összes lekérdezési időszakban észlelt kapcsolatok
- **Ügynök-alapú függőségi vizualizáció**: az ügynök-alapú függőségi vizualizáció használatához le kell töltenie és telepítenie kell az alábbi ügynököket minden olyan helyszíni gépen, amelyet elemezni szeretne.  
    - A [Microsoft Monitoring Agentet (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows) minden gépre fel kell telepíteni. [További](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#install-the-mma) információ az MMA-ügynök telepítéséről.
    - A [függőségi ügynököt](../azure-monitor/platform/agents-overview.md#dependency-agent) minden gépen telepíteni kell. [További](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#install-the-dependency-agent) információ a függőségi ügynök telepítéséről.
    - Továbbá ha olyan gépekkel rendelkezik, amelyeken nincs internetkapcsolat, ezekre le kell töltenie és telepítenie kell a Log Analytics-átjárót.

## <a name="agent-based-requirements"></a>Ügynök-alapú követelmények

### <a name="what-do-i-need-to-deploy-dependency-visualization"></a>Mire van szükség a függőségi vizualizáció üzembe helyezéséhez?

A függőségi vizualizáció üzembe helyezése előtt rendelkeznie kell egy Azure Migrate-projekttel, és a Azure Migrate: Server Assessment Tool hozzáadva a projekthez. A függőségi vizualizációt egy Azure Migrate berendezés beállítása után telepítheti a helyszíni gépek felderítése érdekében.

[További](how-to-assess.md) információ az eszköz hozzáadásáról, valamint egy, a [Hyper-V](how-to-set-up-appliance-hyper-v.md), a [VMware](how-to-set-up-appliance-vmware.md)vagy a fizikai kiszolgálókhoz tartozó berendezések üzembe helyezéséről.


### <a name="how-does-it-work"></a>Hogyan működik?

A Azure Migrate a [Service Map](../operations-management-suite/operations-management-suite-service-map.md) megoldást használja [Azure monitor naplókban](../log-analytics/log-analytics-overview.md) a függőségi vizualizációhoz.

- A függőségi vizualizáció kihasználása érdekében hozzá kell rendelnie egy Log Analytics munkaterületet (új vagy meglévő) egy Azure Migrate projekthez.
- A munkaterületnek ugyanahhoz az előfizetéshez kell tartoznia, mint amelyben a Azure Migrate projektet hozza létre.
- Azure Migrate az USA keleti régiójában, Délkelet-Ázsiában és Nyugat-Európában található munkaterületeket támogatja. Más régiókban lévő munkaterületek nem társíthatók projekthez. Azt is vegye figyelembe, hogy a munkaterületnek olyan régióban kell lennie, amelyben a [Service Map támogatott](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites).
- Egy Azure Migrate projekt munkaterülete nem módosítható a hozzáadása után.
- Log Analytics a Azure Migratehoz társított munkaterület az áttelepítési projekt kulcsával és a projekt nevével van megjelölve.

    ![Log Analytics munkaterület navigálása](./media/concepts-dependency-visualization/oms-workspace.png)



### <a name="do-i-need-to-pay-for-it"></a>Kell fizetnem?

A függőségi vizualizációhoz Service Map és társított Log Analytics munkaterület szükséges. 

- Az Service Map-megoldás nem számít fel díjat az első 180 nap során. Ez az a nap, amikor a Log Analytics munkaterületet a Azure Migrate projekttel társította.
- 180 nap után a standard Log Analytics díjak érvényesek lesznek.
- A társított Log Analytics-munkaterületen a Service Maptól eltérő bármely megoldás használata esetén a rendszer [standard log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) díjat számít fel.
- A Azure Migrate-projekt törlésekor a munkaterület nem törlődik vele együtt. A projekt törlése után Service Map használat nem ingyenes, és az egyes csomópontok a Log Analytics munkaterület fizetős szintjéhez hasonlóan lesznek felszámítva.

További tudnivalókat az Azure Migrate díjszabásáról [itt](https://azure.microsoft.com/pricing/details/azure-migrate/) talál.

> [!NOTE]
> Ha olyan projektekkel rendelkezik, amelyeket az általános rendelkezésre állás Azure Migrate a 2018. február 28. előtt hozott létre, előfordulhat, hogy további Service Map díjat is felmerült. Annak biztosítása érdekében, hogy csak 180 nap után fizessen, javasoljuk, hogy hozzon létre egy új projektet, mivel a meglévő munkaterületek továbbra is felszámítva maradnak.



### <a name="how-do-i-manage-the-workspace"></a>Hogyan felügyeli a munkaterületet?

- Amikor ügynököt regisztrál a munkaterületre, a Azure Migrate-projekt által megadott azonosítót és kulcsot használja.
- A Log Analytics munkaterületet Azure Migraten kívül is használhatja.
- Ha törli a társított Azure Migrate projektet, a munkaterület nem törlődik automatikusan. [Manuálisan kell törölnie](../azure-monitor/platform/manage-access.md).
- Ne törölje a Azure Migrate által létrehozott munkaterületet, hacsak nem törli a Azure Migrate projektet. Ha így tesz, a függőségi vizualizáció funkció nem a várt módon fog működni.

## <a name="next-steps"></a>További lépések
- [Gépek csoportosítása számítógép-függőségek használatával](how-to-create-group-machine-dependencies.md)
- [További](common-questions-discovery-assessment.md#what-is-dependency-visualization) információ a függőségi vizualizációval kapcsolatos gyakori kérdésekért.



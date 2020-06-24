---
title: A fizikai kiszolgáló értékelésének támogatása Azure Migrate
description: Tudnivalók a fizikai kiszolgálók értékelésének támogatásáról Azure Migrate Server Assessment szolgáltatással
ms.topic: conceptual
ms.date: 06/03/2020
ms.openlocfilehash: e60dc12b1f710fe771b90352872eb7113951a65d
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2020
ms.locfileid: "85080505"
---
# <a name="support-matrix-for-physical-server-assessment"></a>A fizikai kiszolgáló értékelésének támogatási mátrixa 

Ez a cikk az előfeltételeket és a támogatási követelményeket összegzi, ha a fizikai kiszolgálókat az Azure-ba való áttelepítésre értékeli a [Azure Migrate: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) Tool használatával. Ha fizikai kiszolgálókat szeretne áttelepíteni az Azure-ba, tekintse át az [áttelepítési támogatási mátrixot](migrate-support-matrix-physical-migration.md).


A fizikai kiszolgálók értékeléséhez létre kell hoznia egy Azure Migrate projektet, és hozzá kell adnia a kiszolgáló-értékelési eszközt a projekthez. Az eszköz hozzáadása után üzembe helyezi a [Azure Migrate berendezést](migrate-appliance.md). A készülék folyamatosan felfedi a helyszíni gépeket, és számítógép-metaadatokat és teljesítményadatokat küld az Azure-nak. A felderítés befejezése után összegyűjtheti a felderített gépeket csoportokba, és értékelést futtathat egy csoport számára.


## <a name="limitations"></a>Korlátozások

**Támogatás** | **Részletek**
--- | ---
**Értékelési korlátok** | Akár 35 000 fizikai kiszolgálót is felfedezheti és felderítheti egyetlen [Azure Migrate projektben](migrate-support-matrix.md#azure-migrate-projects).
**Projekt korlátai** | Egy Azure-előfizetésben több projektet is létrehozhat. A fizikai kiszolgálókon kívül egy projekt is tartalmazhatja a VMware virtuális gépeket és a Hyper-V virtuális gépeket, az egyes alkalmazásokra vonatkozó értékelési korlátokig.
**Felfedezés** | A Azure Migrate berendezés akár 1000 fizikai kiszolgálót is képes észlelni.
**Assessment** | Egyetlen csoportban legfeljebb 35 000 gépet adhat hozzá.<br/><br/> Egyetlen értékeléssel akár 35 000 gépet is megvizsgálhat.

[További](concepts-assessment-calculation.md) információ az értékelésekről.

## <a name="physical-server-requirements"></a>Fizikai kiszolgáló követelmények

| **Támogatás**                | **Részletek**               
| :-------------------       | :------------------- |
| **Fizikai kiszolgáló üzembe helyezése**       | A fizikai kiszolgáló önálló vagy fürtben is üzembe helyezhető. |
| **Engedélyek**           | **Windows:** A felderíteni kívánt Windows-kiszolgálókon tartományi rendszergazdának vagy helyi rendszergazdának kell lennie. A felhasználói fiókot hozzá kell adni a következő csoportokhoz: távfelügyeleti felhasználók, Teljesítményfigyelő felhasználók és Teljesítménynapló felhasználói. <br/><br/> **Linux:** Szüksége lesz egy rendszergazdai fiókra a felderíteni kívánt Linux-kiszolgálókon. |
| **Operációs rendszer** | Az összes operációs rendszer kiértékelése áttelepítésre végezhető. |


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate-berendezés követelményei

A Azure Migrate a [Azure Migrate berendezést](migrate-appliance.md) használja a felderítéshez és értékeléshez. A fizikai kiszolgálók berendezése virtuális gépen vagy fizikai gépen is futtatható. 

- Ismerje meg a fizikai kiszolgálók [készülékre vonatkozó követelményeit](migrate-appliance.md#appliance---physical) .
- Ismerje meg azokat az URL-címeket, amelyekhez a készüléknek [nyilvános](migrate-appliance.md#public-cloud-urls) és [kormányzati](migrate-appliance.md#government-cloud-urls) felhőkben kell hozzáférnie.
- A készüléket a Azure Portalból letöltött [PowerShell-parancsfájl](how-to-set-up-appliance-physical.md) használatával állíthatja be.
A Azure Government-ben telepítse a készüléket a [parancsfájl használatával](deploy-appliance-script-government.md).

## <a name="port-access"></a>Port-hozzáférés

A következő táblázat összefoglalja az értékeléshez szükséges portokra vonatkozó követelményeket.

**Eszköz** | **Kapcsolat**
--- | ---
**Berendezés** | Bejövő kapcsolatok a 3389-as TCP-porton, hogy engedélyezze a távoli asztali kapcsolatokat a berendezéssel.<br/><br/> Bejövő kapcsolatok a 44368-as porton a berendezés-kezelő alkalmazás távoli eléréséhez az URL-cím használatával:``` https://<appliance-ip-or-name>:44368 ```<br/><br/> Kimenő kapcsolatok a 443-as porton (HTTPS), a felderítési és a teljesítménybeli metaadatok küldéséhez Azure Migrate.
**Fizikai kiszolgálók** | **Windows:** Bejövő csatlakozás a WinRM port 5985 (HTTP) szolgáltatásban a konfiguráció és a teljesítmény metaadatainak lekéréséhez Windows-kiszolgálókról. <br/><br/> **Linux:**  Bejövő kapcsolatok a 22-es porton (TCP) a konfiguráció és a teljesítmény metaadatainak lekéréséhez Linux-kiszolgálókról. |

## <a name="agent-based-dependency-analysis-requirements"></a>Ügynök-alapú függőségek elemzésének követelményei

A függőségek [elemzése](concepts-dependency-visualization.md) segít azonosítani az Azure-ba felmérni és áttelepíteni kívánt helyszíni gépek közötti függőségeket. A táblázat összefoglalja az ügynök-alapú függőségi elemzés beállításának követelményeit. A fizikai kiszolgálók jelenleg csak az ügynök-alapú függőségek elemzését támogatják.

**Követelmény** | **Részletek** 
--- | --- 
**Üzembe helyezés előtt** | Rendelkeznie kell egy Azure Migrate-projekttel, és a kiszolgáló-értékelési eszközzel hozzáadva a projekthez.<br/><br/>  A függőségi vizualizáció üzembe helyezése egy Azure Migrate berendezés beállítása után a helyszíni gépek felderítése érdekében<br/><br/> [Ismerje meg, hogyan](create-manage-projects.md) hozhat létre egy projektet első alkalommal.<br/> [Megtudhatja, hogyan](how-to-assess.md) adhat hozzá egy értékelési eszközt egy meglévő projekthez.<br/> Ismerje meg, hogyan állíthatja be a Azure Migrate készüléket a [Hyper-V](how-to-set-up-appliance-hyper-v.md), [VMware](how-to-set-up-appliance-vmware.md)vagy fizikai kiszolgálók értékeléséhez.
**Azure Government** | A függőségi vizualizáció nem érhető el Azure Governmentban.
**Log Analytics** | A Azure Migrate a [Service Map](../operations-management-suite/operations-management-suite-service-map.md) megoldást használja [Azure monitor naplókban](../log-analytics/log-analytics-overview.md) a függőségi vizualizációhoz.<br/><br/> Új vagy meglévő Log Analytics munkaterületet társít egy Azure Migrate projekthez. Egy Azure Migrate projekt munkaterülete nem módosítható a hozzáadása után. <br/><br/> A munkaterületnek ugyanahhoz az előfizetéshez kell tartoznia, mint a Azure Migrate projektnek.<br/><br/> A munkaterületnek az USA keleti régiójában, Délkelet-Ázsiában vagy Nyugat-európai régióban kell lennie. Más régiókban lévő munkaterületek nem társíthatók projekthez.<br/><br/> A munkaterületnek olyan régióban kell lennie, amelyben a [Service Map támogatott](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites).<br/><br/> Log Analytics a Azure Migratehoz társított munkaterület az áttelepítési projekt kulcsával és a projekt nevével van megjelölve.
**Szükséges ügynökök** | Telepítse az alábbi ügynököket minden egyes elemezni kívánt gépen:<br/><br/> A [Microsoft monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).<br/> A [függőségi ügynök](../azure-monitor/platform/agents-overview.md#dependency-agent).<br/><br/> Ha a helyszíni gépek nem csatlakoznak az internethez, le kell töltenie és telepítenie kell Log Analytics-átjárót.<br/><br/> További információ a [függőségi ügynök](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) és az [MMA](how-to-create-group-machine-dependencies.md#install-the-mma)telepítéséről.
**Log Analytics-munkaterület** | A munkaterületnek ugyanahhoz az előfizetéshez kell tartoznia, mint a Azure Migrate projektnek.<br/><br/> Azure Migrate az USA keleti régiójában, Délkelet-Ázsiában és Nyugat-Európában található munkaterületeket támogatja.<br/><br/>  A munkaterületnek olyan régióban kell lennie, amelyben a [Service Map támogatott](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites).<br/><br/> Egy Azure Migrate projekt munkaterülete nem módosítható a hozzáadása után.
**Költségek** | Az Service Map-megoldás nem számít fel díjat az első 180 napra (az Log Analytics munkaterület Azure Migrate projekthez való hozzárendelésének napjától számítva)/<br/><br/> A 180 nap elteltével a standard Log Analytics-díjszabás vonatkozik rá.<br/><br/> A társított Log Analytics-munkaterületen a Service Maptól eltérő bármely megoldás használata esetén a Log Analytics [standard díjait](https://azure.microsoft.com/pricing/details/log-analytics/) kell fizetnie.<br/><br/> A Azure Migrate-projekt törlésekor a munkaterület nem törlődik vele együtt. A projekt törlését követően a Service Map használat nem ingyenes, és az egyes csomópontok a Log Analytics munkaterület fizetős szintjének megfelelően lesznek felszámítva.<br/><br/>Ha olyan projektekkel rendelkezik, amelyeket a Azure Migrate általános elérhetősége előtt hozott létre (GA-28. február 2018.), előfordulhat, hogy további Service Map díjat is felmerült. Ahhoz, hogy a fizetés csak 180 nap után legyen elérhető, javasoljuk, hogy hozzon létre egy új projektet, mivel a már meglévő munkaterületek továbbra is díjkötelesek.
**Felügyelet** | Amikor ügynököt regisztrál a munkaterületre, a Azure Migrate-projekt által megadott azonosítót és kulcsot használja.<br/><br/> A Log Analytics munkaterületet Azure Migraten kívül is használhatja.<br/><br/> Ha törli a társított Azure Migrate projektet, a munkaterület nem törlődik automatikusan. [Törölje manuálisan](../azure-monitor/platform/manage-access.md).<br/><br/> Ne törölje a Azure Migrate által létrehozott munkaterületet, hacsak nem törli a Azure Migrate projektet. Ha így tesz, a függőségi vizualizáció funkció nem a várt módon fog működni.
**Internetkapcsolat** | Ha a gépek nem kapcsolódnak az internethez, akkor telepítenie kell a Log Analytics átjárót.
**Azure Government** | Az ügynök-alapú függőség elemzése nem támogatott.

## <a name="next-steps"></a>További lépések

[Felkészülés a fizikai kiszolgáló értékelésére](tutorial-prepare-physical.md).

---
title: Fizikai kiszolgáló-felmérés támogatása az Azure Áttelepítésben
description: További információ a fizikai kiszolgálók értékelésének támogatásáról az Azure Migrate Server Assessment segítségével
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: ae76a6b570ec58e71a8a1728a2a601728030f58c
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538154"
---
# <a name="support-matrix-for-physical-server-assessment"></a>Támogatási mátrix a fizikai kiszolgáló konkretinációs rendszeréhez 

Ez a cikk összegzi az előfeltételeket és a támogatási követelményeket, amikor az [Azure Áttelepítés:Kiszolgáló értékelés](migrate-services-overview.md#azure-migrate-server-assessment-tool) eszköz használatával értékeli az Azure-ba való áttelepítés fizikai kiszolgálóit. Ha fizikai kiszolgálókat szeretne áttelepíteni az Azure-ba, tekintse át az [áttelepítéstámogatási mátrixot.](migrate-support-matrix-physical-migration.md)


A fizikai kiszolgálók értékeléséhez hozzon létre egy Azure Migrate projektet, és adja hozzá a kiszolgálóértékelés eszközt a projekthez. Az eszköz hozzáadása után telepíti az [Azure Migrate készüléket.](migrate-appliance.md) A készülék folyamatosan felderíti a helyszíni gépeket, és elküldi a gép metaadatait és teljesítményadatait az Azure-ba. A felderítés befejezése után a felderített gépeket csoportokba gyűjti, és egy csoport ra vonatkozó értékelést futtat.


## <a name="limitations"></a>Korlátozások

**Támogatás** | **Részletek**
--- | ---
**Értékelési határértékek** | Egyetlen [Azure Migrate projektben](migrate-support-matrix.md#azure-migrate-projects)akár 35 000 fizikai kiszolgálót is felderíthet és értékelhet.
**Projektkorlátok** | Azure-előfizetésben több projektet is létrehozhat. A fizikai kiszolgálók mellett a projekt tartalmazhat VMware virtuális gépeket és hyper-v vm-eket is, az egyes értékelési korlátokig.
**Felfedezés** | Az Azure Migrate készülék legfeljebb 250 fizikai kiszolgálót fedezhet fel.
**Értékelés** | Egy csoportban legfeljebb 35 000 gépet adhat hozzá.<br/><br/> Egyetlen értékelés alatt akár 35 000 gépet is felmérhet.

[További információ](concepts-assessment-calculation.md) az értékelésekről.

## <a name="physical-server-requirements"></a>Fizikai kiszolgáló követelmények

| **Támogatás**                | **Részletek**               
| :-------------------       | :------------------- |
| **Fizikai kiszolgáló telepítése**       | A fizikai kiszolgáló lehet önálló, vagy fürtben van telepítve. |
| **Engedélyek**           | **Windows:** Helyi vagy tartományi felhasználói fiókra van szüksége az összes felderíteni kívánt Windows-kiszolgálón. A felhasználói fiókot hozzá kell adni a következő csoportokhoz: Távoli asztali felhasználók, Teljesítményfigyelő-felhasználók és Teljesítménynapló-felhasználók. <br/><br/> **Linux:** Szüksége van egy gyökérfiókra a Linux-kiszolgálókon, amelyeket fel szeretne deríteni. |
| **Operációs rendszer** | Az Azure által támogatott összes [Windows-](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) és [Linux-operációs](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) rendszer, kivéve a Windows Server 2003 és a SUSE Linux rendszert.|


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate-berendezés követelményei

Az Azure Migrate az [Azure Migrate készüléket](migrate-appliance.md) használja felderítéshez és értékeléshez. A fizikai kiszolgálók hoz a készülék futtatható virtuális gépen vagy fizikai gépen. Az Azure Portalról letöltött PowerShell-parancsfájl használatával állíthatja be a készüléket.

- További információ a fizikai kiszolgálók [ra vonatkozó berendezésekre vonatkozó követelményekről.](migrate-appliance.md#appliance---physical)
- Ismerje meg azokat az URL-címeket, amelyekhez a készüléknek [nyilvános](migrate-appliance.md#public-cloud-urls) és [kormányzati](migrate-appliance.md#government-cloud-urls) felhőkben kell hozzáférnie.

## <a name="port-access"></a>Port-hozzáférés

Az alábbi táblázat összefoglalja a port értékelési követelményeit.

**Eszköz** | **Kapcsolat**
--- | ---
**Készülék** | Bejövő kapcsolatok a 3389-es TCP-porton, hogy lehetővé tegyék a távoli asztali kapcsolatokat a készülékhez.<br/><br/> Bejövő kapcsolatok a 44368-as porton, hogy távolról is elérhesse a készülékkezelő alkalmazást az URL-cím használatával:``` https://<appliance-ip-or-name>:44368 ```<br/><br/> Kimenő kapcsolatok a 443-as (HTTPS) portokon, hogy felderítési és teljesítménymetaadatokat küldjön az Azure Migrate.Outbound connections on ports 443 (HTTPS), to send discovery and performance metadata to Azure Migrate.
**Fizikai kiszolgálók** | **Windows:** Bejövő kapcsolatok az 5985 (HTTP) és az 5986 (HTTPS) rendszerű WinRM-portokon a konfigurációs és teljesítménymetaadatok Windows-kiszolgálókról történő lekérése érdekében. <br/><br/> **Linux:**  Bejövő kapcsolatok a 22-es porton (UDP), a konfiguráció és a teljesítmény metaadatainak lekérése linuxos kiszolgálókról. |

## <a name="agent-based-dependency-analysis-requirements"></a>Ügynökalapú függőségelemzési követelmények

[A függőségi elemzés](concepts-dependency-visualization.md) segítségével azonosíthatja a helyszíni gépek közötti függőségeket, amelyeket fel szeretne mérni, és át szeretne térni az Azure-ba. A tábla összefoglalja az ügynökalapú függőségelemzés beállításának követelményeit. Jelenleg csak ügynökalapú függőségelemzés támogatott fizikai kiszolgálókon.

**Követelmény** | **Részletek** 
--- | --- 
**Telepítés előtt** | Rendelkeznie kell egy Azure Áttelepítési projekt a helyén, a kiszolgálóértékelése eszköz hozzá a projekthez.<br/><br/>  Függőségi vizualizációt üzembe helyez, miután létrehozott egy Azure Migrate-berendezést a helyszíni gépek felderítéséhez<br/><br/> [Ismerje meg, hogyan](create-manage-projects.md) hozhat létre első alkalommal projektet.<br/> [További információ arról, hogyan](how-to-assess.md) adhat hozzá értékelési eszközt egy meglévő projekthez.<br/> Ismerje meg, hogyan állíthatja be az Azure Migrate készüléket a [Hyper-V,](how-to-set-up-appliance-hyper-v.md) [VMware](how-to-set-up-appliance-vmware.md)vagy fizikai kiszolgálók értékeléséhez.
**Azure Government** | Függőségi megjelenítés nem érhető el az Azure Government.
**Log Analytics** | Az Azure Migrate a [Service Map-megoldást](../operations-management-suite/operations-management-suite-service-map.md) használja az [Azure Monitor naplóiban](../log-analytics/log-analytics-overview.md) a függőségi vizualizációhoz.<br/><br/> Egy új vagy meglévő Log Analytics-munkaterületet társít egy Azure Migrate projekthez. Az Azure Migrate projekt munkaterülete nem módosítható a hozzáadása után. <br/><br/> A munkaterületnek ugyanabban az előfizetésben kell lennie, mint az Azure Migrate projektnek.<br/><br/> A munkaterületnek az USA keleti régiójában, Délkelet-Ázsiában vagy Nyugat-Európa régióiban kell lennie. Más régiók munkaterületei nem társíthatók projekthez.<br/><br/> A munkaterületnek olyan régióban kell lennie, ahol [a Szolgáltatástérkép támogatott.](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites)<br/><br/> A Log Analytics, a munkaterület társított Azure Áttelepítés van címkézve a migration projekt kulcs, és a projekt neve.
**Kötelező ügynökök** | Minden elemezni kívánt gépen telepítse a következő ügynököket:<br/><br/> A [Microsoft Monitoring ügynök (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).<br/> A [függőségi ügynök](../azure-monitor/platform/agents-overview.md#dependency-agent).<br/><br/> Ha a helyszíni gépek nem csatlakoznak az internethez, le kell töltenie és telepítenie kell rájuk a Log Analytics átjárót.<br/><br/> További információ a [függőségi ügynök](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) és az [MMA](how-to-create-group-machine-dependencies.md#install-the-mma)telepítéséről.
**Log Analytics-munkaterület** | A munkaterületnek ugyanabban az előfizetésben kell lennie, mint az Azure Migrate projektnek.<br/><br/> Az Azure Migrate támogatja az USA keleti régiójában, Délkelet-Ázsiában és Nyugat-Európa régióiban található munkaterületeket.<br/><br/>  A munkaterületnek olyan régióban kell lennie, ahol [a Szolgáltatástérkép támogatott.](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites)<br/><br/> Az Azure Migrate projekt munkaterülete nem módosítható a hozzáadása után.
**Költségek** | A Szolgáltatástérkép-megoldás nem számít fel díjat az első 180 napra vonatkozóan (attól a naptól számítva, amikor a Log Analytics-munkaterületet társítja az Azure Migrate projekthez)/<br/><br/> A 180 nap elteltével a standard Log Analytics-díjszabás vonatkozik rá.<br/><br/> A társított Log Analytics-munkaterületen a Szolgáltatástérképen kívül bármilyen más megoldás használata esetén a Log Analytics [általános díjakat](https://azure.microsoft.com/pricing/details/log-analytics/) számít fel.<br/><br/> Az Azure Migrate projekt törlésekor a munkaterület nem törlődik vele együtt. A projekt törlése után a Szolgáltatástérkép használata nem ingyenes, és minden csomópontnak a Log Analytics-munkaterület fizetett szintje szerint kell fizetnie.<br/><br/>Ha olyan projektjei vannak, amelyeket az Azure Migrate általános elérhetősége előtt hozott létre (GA– 2018. február 28.), előfordulhat, hogy további szolgáltatástérkép-díjak merültek fel. Annak érdekében, hogy a kifizetés csak 180 nap után, azt javasoljuk, hogy hozzon létre egy új projektet, mivel a meglévő munkaterületek előtt GA továbbra is felszámítható.
**Felügyelet** | Amikor ügynököket regisztrál a munkaterületre, az Azure Migrate projekt által biztosított azonosítót és kulcsot használja.<br/><br/> Használhatja a Log Analytics munkaterületet az Azure Migrate-en kívül.<br/><br/> Ha törli a társított Azure Migrate projektet, a munkaterület nem törlődik automatikusan. [Törölje manuálisan](../azure-monitor/platform/manage-access.md).<br/><br/> Ne törölje az Azure Migrate által létrehozott munkaterületet, hacsak nem törli az Azure Migrate projektet. Ha így tesz, a függőségi vizualizációs funkció nem a várt módon fog működni.
**Internetkapcsolat** | Ha a gépek nem csatlakoznak az internethez, telepítenie kell rájuk a Log Analytics-átjárót.
**Azure Government** | Az ügynökalapú függőségelemzés nem támogatott.

## <a name="next-steps"></a>További lépések

[Felkészülés a fizikai kiszolgáló értékelésére](tutorial-prepare-physical.md).

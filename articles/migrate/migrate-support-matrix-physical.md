---
title: A fizikai kiszolgáló értékelésének támogatása a Azure Migrate
description: Ismerje meg a fizikai kiszolgálók értékelésének támogatását a Azure Migrate.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: f2698d0ff046147599a8c5c791a0980a54090932
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990334"
---
# <a name="support-matrix-for-physical-server-assessment"></a>A fizikai kiszolgáló értékelésének támogatási mátrixa 

A [Azure Migrate szolgáltatással](migrate-overview.md) a gépeket kivizsgálhatja és áttelepítheti a Microsoft Azure felhőbe. Ez a cikk a helyszíni fizikai kiszolgálók értékelésére és áttelepítésére vonatkozó támogatási beállításokat és korlátozásokat foglalja össze.


## <a name="overview"></a>Áttekintés

Ha a helyszíni gépeket az Azure-ba való áttelepítésre szeretné felmérni, adja hozzá a Azure Migrate: Server Assessment eszközt egy Azure Migrate projekthez. Üzembe helyezi a [Azure Migrate berendezést](migrate-appliance.md). A készülék folyamatosan felfedi a helyszíni gépeket, és konfigurációs és teljesítményadatokat küld az Azure-nak. A gép felderítése után összegyűjtheti a felderített gépeket csoportokba, és értékelést futtathat egy csoport számára.

## <a name="limitations"></a>Korlátozások

**Támogatás** | **Részletek**
--- | ---
**Értékelési korlátok**| Akár 35 000 fizikai kiszolgálót is felderítheti és felbecsülheti egyetlen [projektben](migrate-support-matrix.md#azure-migrate-projects).
**Projekt korlátai** | Egy Azure-előfizetésben több projektet is létrehozhat. A projektek tartalmazhatják a VMware virtuális gépeket, a Hyper-V virtuális gépeket és a fizikai kiszolgálókat, akár az értékelési korlátokig is.
**Felfedezés** | A Azure Migrate berendezés akár 250 fizikai kiszolgálót is képes észlelni.
**Értékelés** | Egyetlen csoportban legfeljebb 35 000 gépet adhat hozzá.<br/><br/> Egyetlen értékeléssel akár 35 000 gépet is megvizsgálhat.

[További](concepts-assessment-calculation.md) információ az értékelésekről.




## <a name="physical-server-requirements"></a>A fizikai kiszolgáló követelményei

| **Támogatás**                | **Részletek**               
| :-------------------       | :------------------- |
| **Fizikai kiszolgáló üzembe helyezése**       | A fizikai kiszolgáló önálló vagy egy fürtben is üzembe helyezhető. |
| **Engedélyek**           | **Windows:** Állítson be egy helyi vagy tartományi felhasználói fiókot a felderítésbe felvenni kívánt Windows-kiszolgálókon. A felhasználói fiókot fel kell venni a következő csoportokhoz: Távoli asztal felhasználók, Teljesítményfigyelő felhasználók és Teljesítménynapló felhasználói. <br/> **Linux:** Szüksége lesz egy rendszergazdai fiókra a felderíteni kívánt Linux-kiszolgálókon. |
| **Operációs rendszer** | Az összes [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) -és [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) -operációs rendszer támogatott, a következők kivételével:<br/> Windows Server 2003 <br/> SUSE Linux|


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate-berendezés követelményei

A Azure Migrate a [Azure Migrate berendezést](migrate-appliance.md) használja a felderítéshez és értékeléshez. A fizikai kiszolgálók berendezése virtuális gépen vagy fizikai gépen is futtatható. A Azure Portalból letöltött PowerShell-parancsfájl használatával állíthatja be.

- Ismerje meg a fizikai kiszolgálók [készülékre vonatkozó követelményeit](migrate-appliance.md#appliance---physical) .
- További információ a készülék eléréséhez szükséges [URL-címekről](migrate-appliance.md#url-access) .

## <a name="port-access"></a>Port-hozzáférés

A következő táblázat összefoglalja az értékeléshez szükséges portokra vonatkozó követelményeket.

**Eszköz** | **Kapcsolat**
--- | ---
**Berendezés** | Bejövő kapcsolatok a 3389-as TCP-porton, hogy engedélyezze a távoli asztali kapcsolatokat a berendezéssel.<br/> Bejövő kapcsolatok a 44368-as porton a berendezés-kezelő alkalmazás távoli eléréséhez az URL-cím használatával: ``` https://<appliance-ip-or-name>:44368 ```<br/> A 443 (HTTPS), 5671 és 5672 (AMQP) portok kimenő kapcsolatainak felderítési és teljesítménybeli metaadatok küldésére Azure Migrate.
**Fizikai kiszolgálók** | **Windows:** A WinRM-portok 5985 (HTTP) és 5986 (HTTPS) bejövő kapcsolatai a konfiguráció és a teljesítmény metaadatainak lekérésére a Windows-kiszolgálókról. <br/> **Linux:**  Bejövő kapcsolatok a 22-es porton (UDP) a konfiguráció és a teljesítmény metaadatainak lekéréséhez Linux-kiszolgálókról. |

## <a name="agent-based-dependency-visualization"></a>Ügynök-alapú függőségi vizualizáció

A függőségi [vizualizáció](concepts-dependency-visualization.md) segítséget nyújt a függőségek megjelenítéséhez a felmérni és áttelepíteni kívánt gépek között. Az ügynök-alapú vizualizációk, követelmények és korlátozások összegzése az alábbi táblázatban látható.


**Követelmény** | **Részletek**
--- | ---
**Üzembe helyezés** | A függőségi vizualizáció üzembe helyezése előtt rendelkeznie kell egy Azure Migrate-projekttel, és a Azure Migrate: Server Assessment Tool hozzáadva a projekthez. A függőségi vizualizációt egy Azure Migrate berendezés beállítása után telepítheti a helyszíni gépek felderítése érdekében.<br/><br/> A függőségi vizualizáció nem érhető el Azure Governmentban.
**Szolgáltatástérkép** | Az ügynök-alapú függőségi vizualizáció a [Service Map](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) megoldást használja [Azure monitor naplókban](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).<br/><br/> Az üzembe helyezéshez új vagy meglévő Log Analytics munkaterületet társít egy Azure Migrate projekthez.
**Log Analytics-munkaterület** | A munkaterületnek ugyanahhoz az előfizetéshez kell tartoznia, mint a Azure Migrate projektnek.<br/><br/> Azure Migrate az USA keleti régiójában, Délkelet-Ázsiában és Nyugat-Európában található munkaterületeket támogatja.<br/><br/>  A munkaterületnek olyan régióban kell lennie, amelyben a [Service Map támogatott](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites).<br/><br/> Egy Azure Migrate projekt munkaterülete nem módosítható a hozzáadása után.
**Díjak** | Az Service Map-megoldás nem számít fel díjat az első 180 napra (a Log Analytics munkaterülethez a Azure Migrate projekttel társított naptól számítva).<br/><br/> 180 nap után a standard Log Analytics díjak érvényesek lesznek.<br/><br/> A társított Log Analytics-munkaterületen a Service Maptól eltérő bármely megoldás használata esetén a rendszer standard Log Analytics díjat számít fel.<br/><br/> Ha törli a Azure Migrate projektet, a munkaterület nincs törölve. A projekt törlése után Service Map nem ingyenes, és az egyes csomópontok Log Analytics munkaterület fizetős szintjéhez hasonlóan lesznek felszámítva.
**Ügynökök** | Az ügynök-alapú függőségi vizualizációhoz két ügynököt kell telepíteni az elemezni kívánt gépeken.<br/><br/> - [Microsoft monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)<br/><br/> - [függőségi ügynök](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent). 
**Internetkapcsolat** | Ha a gépek nem kapcsolódnak az internethez, akkor telepítenie kell a Log Analytics átjárót.

## <a name="next-steps"></a>Következő lépések

[Felkészülés a fizikai kiszolgáló értékelésére](tutorial-prepare-physical.md).

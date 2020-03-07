---
title: Gyakori kérdések – felderítés, értékelés és függőség elemzése Azure Migrate
description: Választ kaphat a felderítéssel, értékeléssel és függőségi elemzéssel kapcsolatos gyakori kérdésekre Azure Migrateban.
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 7edc73742b61e4e5e94431c50d14d263bbbea641
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78362029"
---
# <a name="common-questions-about-discovery-assessment-and-dependency-analysis"></a>Gyakori kérdések a felderítéssel, az értékeléssel és a függőségek elemzésével kapcsolatban

Ez a cikk a felderítéssel, értékeléssel és függőségi elemzéssel kapcsolatos gyakori kérdésekre ad választ a Azure Migrateban. Ha további kérdései vannak, tekintse át a következő cikkeket:

- [Általános kérdések](resources-faq.md) a Azure Migrateról.
- Az Azure Migrate berendezéssel kapcsolatos [kérdések](common-questions-appliance.md) .
- A kiszolgálók áttelepítésével kapcsolatos [kérdések](common-questions-server-migration.md) .
- Kérdések közzététele a [Azure Migrate fórumban](https://aka.ms/AzureMigrateForum)



## <a name="how-many-vms-can-i-discover-with-an-appliance"></a>Hány virtuális gépet tudok felderíteni egy berendezéssel?

Akár 10 000 VMware virtuális gép, akár 5 000 Hyper-V virtuális gép, valamint akár 250 fizikai kiszolgáló is felderíthető egyetlen berendezéssel. Ha több géppel rendelkezik, tekintse át a [Hyper-V](scale-hyper-v-assessment.md), a [VMware](scale-vmware-assessment.md)és a [fizikai kiszolgáló](scale-physical-assessment.md) értékelésével kapcsolatos cikkeket.



## <a name="vm-size-changed-can-i-run-an-assessment-again"></a>A virtuális gép mérete megváltozott. Újraértékelést is Futtathatok?

Az Azure Migrate készülék folyamatosan gyűjti a helyszíni gépekre vonatkozó információkat, és az értékelés egy időponthoz kapcsolódó pillanatkép. Ha módosítja egy olyan virtuális gép beállításait, amelyet fel szeretne mérni, használja az újraszámítási lehetőséget az értékelés frissítéséhez a legújabb módosításokkal.

## <a name="how-do-i-discover-vms-in-a-multitenant-environment"></a>Hogyan a virtuális gépek felderítését több-bérlős környezetben?

- **VMware**: Ha egy környezet megosztott a bérlők között, és nem szeretné felderíteni a bérlő virtuális gépei egy másik bérlő előfizetésében, hozzon létre vCenter Server hitelesítő adatokat, amelyek csak a felderíteni kívánt virtuális gépekhez férhetnek hozzá. Ezután használja ezeket a hitelesítő adatokat, amikor elindítja a felderítést a Azure Migrate berendezésben.
- **Hyper-v**: a felderítés a Hyper-v gazdagép hitelesítő adatait használja. Ha a virtuális gépek ugyanazt a Hyper-V-gazdagépet használják, jelenleg nincs lehetőség a felderítés elválasztására.  


## <a name="do-i-need-vcenter-server"></a>Szükségem van vCenter Serverre?

Igen, Azure Migrate a felderítést a VMware-környezetben vCenter Server kell végrehajtania. Nem támogatja az vCenter Server által nem felügyelt ESXi-gazdagépek felderítését.


## <a name="whats-are-the-sizing-options"></a>Mik a méretezési lehetőségek?

Helyszíni méretezés esetén a Azure Migrate nem veszi figyelembe a virtuális gépek teljesítményadatait az értékeléshez. A helyszíni konfiguráció alapján felméri a virtuális gépek méretét. A teljesítmény-alapú méretezéssel a méretezés a kihasználtsági adatain alapul.

- Ha például egy helyszíni virtuális gép 4 maggal és 8 GB memóriával rendelkezik a 50%-os CPU-kihasználtság és a 50% memória kihasználtsága esetén:
    - A helyszíni méretezés olyan Azure VM SKU-t javasol, amely négy maggal és 8 GB memóriával rendelkezik.
    - A teljesítmény-alapú méretezés olyan virtuálisgép-SKU-t javasol, amely két maggal és 4 GB memóriával rendelkezik, mert a kihasználtság százalékos arányát veszi figyelembe.

- Hasonlóképpen, a lemezek méretezése a méretezési feltételektől és a tárolási típustól függ.
    - Ha a méretezési feltétel teljesítmény-alapú, és a tárolási típus automatikus, Azure Migrate a lemez IOPS és átviteli értékeit veszi figyelembe, amikor azonosítja a céllemez típusát (standard vagy prémium).
    - Ha a méretezési feltétel teljesítmény-alapú, és a tárolási típus prémium, Azure Migrate a helyszíni lemez méretétől függően a prémium szintű lemezes SKU-t javasolja. Ugyanez a logikát alkalmazza a lemez méretezésére, amikor a méretezés a helyszínen történik, és a tárolási típus a standard vagy a prémium.

## <a name="does-performance-historyutilization-impact-sizing"></a>A teljesítmény-előzmények/kihasználtság befolyásolja a méretezést?

Ez a tulajdonságok csak a teljesítményalapú méretezés esetén számítanak.

- Azure Migrate gyűjti a helyszíni gépek teljesítményének előzményeit, és azt használja a virtuális gép méretének és a lemez típusának az Azure-ban való használatát.
- A berendezés folyamatosan a helyszíni környezetet, a valós idejű kihasználtsági adatok gyűjtését végzi 20 másodpercenként.
- A készülék összesíti a 20 másodperces mintákat, és 15 percenként létrehoz egyetlen adatpontot.
- Az adatpont létrehozásához a berendezés a 20 másodperces mintákból kiválasztja a csúcsérték értékét.
- A készülék elküldi ezt az adatpontot az Azure-nak.

Amikor értékelést hoz létre az Azure-ban, a teljesítmény időtartama és a teljesítményi előzmények percentilis értéke alapján Azure Migrate kiszámítja a tényleges kihasználtság értékét, és azt használja a méretezéshez.

- Ha például egy napra állítja be a teljesítmény időtartamát, a percentilis értékét pedig 95 percentilis értékre, Azure Migrate rendezi a gyűjtő által az elmúlt napra vonatkozóan a növekvő sorrendben eljuttatott 15 perces mintavételi pontokat, és a 95. percentilis értékét a hatályos értékre választja. kihasználtsága.
- A 95. percentilis értékének használata esetén a rendszer figyelmen kívül hagyja a kiugró értékeket. A kiugró értékek a esetek 99% percentilis használata esetén szerepelhetnek. Ha ki szeretné választani az időszak maximális kihasználtságát anélkül, hogy a kiugró értékek hiányoznak, válassza ki a esetek 99% percentilis értéket.

## <a name="what-is-dependency-visualization"></a>Mi a függőségi vizualizáció?

A függőségi vizualizáció használatával felmérhetővé teheti a virtuális gépek csoportjait a nagyobb megbízhatóság érdekében. A függőségi vizualizációk átvizsgálják a számítógép függőségeit az értékelés futtatása előtt. Ezzel biztosítható, hogy semmi ne maradjon hátra, így elkerülhető a váratlan kimaradások elkerülése az Azure-ba való Migrálás során. Azure Migrate a függőségi vizualizáció engedélyezéséhez a Azure Monitor Service Map megoldását használja. [További információ](concepts-dependency-visualization.md).

> [!NOTE]
> A függőségi vizualizáció nem érhető el Azure Governmentban.

## <a name="whats-the-difference-between-agent-based-and-agentless"></a>Mi a különbség az ügynök-alapú és az ügynök nélkül?

Az ügynök nélküli vizualizáció és az ügynök-alapú vizualizáció közötti különbségeket a táblázat foglalja össze.

**Követelmény** | **Ügynök nélküli** | **Ügynök-alapú**
--- | --- | ---
Támogatás | Ez a beállítás jelenleg előzetes verzióban érhető el, és csak a VMware virtuális gépekhez használható. [Tekintse át](migrate-support-matrix-vmware.md#agentless-dependency-visualization) a támogatott operációs rendszereket. | Általánosan elérhető (GA).
Ügynök | Nem kell telepítenie az ügynököket az áttekinteni kívánt gépekre. | Az elemezni kívánt helyszíni gépekre telepítendő ügynökök: a [Microsoft monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)és a [függőségi ügynök](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent). 
Előfeltételek | [Tekintse át](concepts-dependency-visualization.md#agentless-visualization) az előfeltételeket és az üzembe helyezésre vonatkozó követelményeket. | [Tekintse át](concepts-dependency-visualization.md#agent-based-visualization) az előfeltételeket és az üzembe helyezésre vonatkozó követelményeket.
Log Analytics | Nem kötelező. | A Azure Migrate a [Service Map](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) megoldást használja [Azure monitor naplókban](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) a függőségi vizualizációhoz. [További információk](concepts-dependency-visualization.md#agent-based-visualization).
Működés | Rögzíti a TCP-kapcsolatok mennyiségét a függőségi vizualizációhoz engedélyezett gépeken. A felderítést követően öt percen belül gyűjti az adatokat. | A gépen telepített Service Map ügynökök gyűjtenek adatokat a TCP-folyamatokról és a bejövő/kimenő kapcsolatokról az egyes folyamatokhoz.
Adatok | Forrásoldali gép kiszolgálójának neve, folyamata, alkalmazás neve.<br/><br/> Célszámítógép kiszolgálójának neve, folyamat, alkalmazás neve és port. | Forrásoldali gép kiszolgálójának neve, folyamata, alkalmazás neve.<br/><br/> Célszámítógép kiszolgálójának neve, folyamat, alkalmazás neve és port.<br/><br/> A kapcsolatok száma, a késés és az adatátviteli adatok összegyűjtése és Log Analytics lekérdezések számára elérhetők. 
Megjelenítés | Az önálló kiszolgálók függőségi térképe egy óra és 30 nap közötti időtartamon belül megtekinthető. | Egyetlen kiszolgáló függőségi térképe.<br/><br/> A Térkép csak egy órán át jeleníthető meg.<br/><br/> Kiszolgálók egy csoportjának függőségi térképe.<br/><br/> Kiszolgálók hozzáadása és eltávolítása a Térkép nézetből.
Adatexportálás | Jelenleg nem tölthető le táblázatos formátumban. | Az adatlekérdezés a Log Analytics használatával kérdezhető le.




## <a name="do-i-pay-for-dependency-visualization"></a>Fizetnem kell a függőségi vizualizációért?
Nem. [Itt talál további információkat](https://azure.microsoft.com/pricing/details/azure-migrate/) az Azure Migrate díjszabásáról.

## <a name="what-do-i-install-for-agent-based-dependency-visualization"></a>Mit kell telepíteni az ügynök-alapú függőségi vizualizációhoz?

Az ügynök-alapú függőségi vizualizációk használatához le kell töltenie és telepítenie kell az ügynököket minden olyan helyszíni gépen, amelyet ki szeretne értékelni.

Telepítse a következő ügynököket mindegyik gépen:
- [Microsoft monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).
- [Függőségi ügynök](../azure-monitor/platform/agents-overview.md#dependency-agent).
- Ha olyan gépekkel rendelkezik, amelyeknek nincs internetkapcsolata, le kell töltenie és telepítenie kell Log Analytics-átjárót.

Ezek az ügynökök nem szükségesek, kivéve, ha ügynök-alapú függőségi vizualizációt használ.

## <a name="can-i-use-an-existing-workspace"></a>Használhatok egy meglévő munkaterületet?

Igen, az ügynök-alapú függőségi vizualizációhoz csatolhat egy meglévő munkaterületet az áttelepítési projekthez, és használhatja azt a függőségi vizualizációhoz. 

## <a name="can-i-export-the-dependency-visualization-report"></a>Exportálhatók a függőségi vizualizációs jelentés?

Nem, az ügynök-alapú vizualizációban nem lehet exportálni a függőség vizualizációs jelentését. A Azure Migrate azonban Service Mapt használ, és az [Service Map REST API](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) használatával kérheti le a függőségeket JSON formátumban.

## <a name="can-i-automate-agent-installation"></a>Automatizálható az ügynök telepítése?
Az ügynök-alapú függőségi vizualizációk esetében az alábbi módon automatizálható:

- Ezzel [a parancsfájllal telepítheti a függőségi ügynököt](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples).
- Az MMA esetében kövesse az alábbi [utasításokat](../azure-monitor/platform/log-analytics-agent.md#installation-and-configuration) a parancssor vagy az Automation használatához, vagy használja [ezt a parancsfájlt](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).
- A parancsfájlok mellett olyan központi telepítési eszközöket is használhat, mint például a Microsoft Endpoint Configuration Manager és a [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) az ügynökök üzembe helyezéséhez.


## <a name="what-operating-systems-does-mma-support"></a>Milyen operációs rendszereket támogat az MMA?

- Tekintse meg az [MMA által támogatott Windows operációs rendszerek](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)listáját.
- Megtekintheti az [MMA által támogatott Linux operációs rendszerek](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems)listáját.

## <a name="can-i-visualize-dependencies-for-more-than-an-hour"></a>Megjeleníthető-e a függőségek több mint egy óráig?
Az ügynök-alapú vizualizációk esetében akár egy óráig is megjelenítheti a függőségeket. Visszatérhet egy adott dátumhoz az előzményekben, egy hónapra visszamenőleg, de a vizualizációk maximális időtartama egy óra. Használhatja például az időtartamot a függőségi térképen a tegnapi függőségek megjelenítéséhez, de csak egyórás időszakra vonatkozóan tekintheti meg a függőségeket. A [függőségi adat](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) hosszabb időtartamon keresztül történő lekérdezéséhez azonban Azure monitor naplókat is használhat.

Az ügynök nélküli vizualizációk esetében egy adott kiszolgáló függőségi térképét egy óra és 30 nap közötti időtartamra tekintheti meg.


## <a name="can-visualize-dependencies-for-groups-of-more-than-10-vms"></a>Képes megjeleníteni a több mint 10 virtuális gépen álló csoportok függőségeit?
A legfeljebb 10 virtuális gépet tartalmazó csoportok [függőségeit megjelenítheti](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) . Ha több mint 10 virtuális géppel rendelkező csoporttal rendelkezik, javasoljuk, hogy ossza szét a csoportot kisebb csoportokba, majd jelenítse meg a függőségeket.




## <a name="next-steps"></a>Következő lépések
Olvassa el a [Azure Migrate áttekintést](migrate-services-overview.md).

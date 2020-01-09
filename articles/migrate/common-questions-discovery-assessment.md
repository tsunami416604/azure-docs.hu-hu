---
title: Gyakori kérdések – felderítés, értékelés és függőség elemzése Azure Migrate
description: Választ kaphat a felderítéssel, értékeléssel és függőségi elemzéssel kapcsolatos gyakori kérdésekre Azure Migrateban.
ms.topic: conceptual
ms.date: 12/29/2019
ms.openlocfilehash: 0b7bb425bff1d26c2a0d6feeaf70be826eab2b9a
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/31/2019
ms.locfileid: "75564048"
---
# <a name="common-questions-about-discovery-assessment-and-dependency-analysis"></a>Gyakori kérdések a felderítéssel, az értékeléssel és a függőségek elemzésével kapcsolatban

Ez a cikk a felderítéssel, értékeléssel és függőségi elemzéssel kapcsolatos gyakori kérdésekre ad választ a Azure Migrateban. Ha további lekérdezésekre van szüksége a cikk elolvasása után, tegye fel őket a [Azure Migrate fórumba](https://aka.ms/AzureMigrateForum). Ha további kérdései vannak, tekintse át a következő cikkeket:

- [Általános kérdések](resources-faq.md) a Azure Migrateról.
- Az Azure Migrate berendezéssel kapcsolatos [kérdések](common-questions-appliance.md) .


## <a name="how-many-vms-can-i-discover-with-an-appliance"></a>Hány virtuális gépet tudok felderíteni egy berendezéssel?

Akár 10 000 VMware virtuális gépet és akár 5 000 Hyper-V virtuális gépet is felfedezhet egyetlen berendezéssel. Ha több géppel rendelkezik a helyszíni környezetben, olvassa el a [Hyper-V](scale-hyper-v-assessment.md) és a [VMware](scale-vmware-assessment.md) értékelésének méretezését ismertető részt.



## <a name="vm-size-changed-can-i-run-an-assessment-again"></a>A virtuális gép mérete megváltozott. Újraértékelést is Futtathatok?

A Azure Migrate berendezés folyamatosan gyűjt információkat a helyszíni környezetről. Az értékelés azonban a helyszíni virtuális gépekre vonatkozó időponthoz kapcsolódó pillanatkép. Ha módosítja egy olyan virtuális gép beállításait, amelyet fel szeretne mérni, használja az újraszámítási lehetőséget az értékelés frissítéséhez a legújabb módosításokkal.

### <a name="how-do-i-discover-vms-in-a-multitenant-environment"></a>Hogyan a virtuális gépek felderítését több-bérlős környezetben?

- A VMware esetében, ha a környezet a bérlők között van megosztva, és nem szeretné felderíteni egy másik bérlő előfizetésében lévő egyik bérlő virtuális gépeket, hozzon létre vCenter Server hitelesítő adatokat, amelyek csak a felderíteni kívánt virtuális gépeket érhetik el. Ezután használja ezeket a hitelesítő adatokat, amikor elindítja a felderítést a Azure Migrate berendezésben.
- A Hyper-V esetében a felderítés a Hyper-V gazdagép hitelesítő adatait használja. Ha a virtuális gépek ugyanazt a Hyper-V-gazdagépet használják, jelenleg nincs lehetőség a felderítés elválasztására.  


### <a name="do-i-need-vcenter-server-for-vmware-vm-discovery"></a>Szükség van vCenter Server a VMWare virtuális gépek felderítéséhez?

Igen, Azure Migrate a felderítést a VMware-környezetben vCenter Server kell végrehajtania. Nem támogatja az vCenter Server által nem felügyelt ESXi-gazdagépek felderítését.


## <a name="whats-the-difference-sizing-options"></a>Mi a különbség a méretezési lehetőségek között?

Helyszíni méretezés esetén a Azure Migrate nem veszi figyelembe a virtuális gépek teljesítményadatait az értékeléshez. A helyszíni konfiguráció alapján felméri a virtuális gépek méretét. A teljesítmény-alapú méretezéssel a méretezés a kihasználtsági adatain alapul.

- Ha például egy helyszíni virtuális gép 4 maggal és 8 GB memóriával rendelkezik, 50%-os CPU-kihasználtsággal és 50%-os memória-kihasználtsággal, a következők történnek:
    - A helyszíni méretezési szolgáltatás olyan Azure VM SKU-t javasol, amely 4 maggal és 8 GB memóriával rendelkezik.
    - A teljesítmény-alapú méretezés olyan virtuálisgép-SKU-t javasol, amely 2 maggal és 4 GB memóriával rendelkezik, mert a kihasználtság százalékos arányát veszi figyelembe.

- Hasonlóképpen, a lemez méretezése két értékelési tulajdonságtól függ: a méretezési feltételektől és a tárolási típustól.
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

## <a name="do-i-pay-for-dependency-visualization"></a>Fizetnem kell a függőségi vizualizációért?
Nem. [Itt talál további információkat](https://azure.microsoft.com/pricing/details/azure-migrate/) az Azure Migrate díjszabásáról.

## <a name="what-do-i-install-for-dependency-visualization"></a>Mit kell telepíteni a függőségi vizualizációhoz?

A függőségi vizualizáció használatához le kell töltenie és telepítenie kell az ügynököket minden olyan helyszíni gépen, amelyet ki szeretne értékelni.

A következő ügynököket kell telepítenie mindegyik gépre:
- [Microsoft monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).
- [Függőségi ügynök](../azure-monitor/platform/agents-overview.md#dependency-agent).
- Ha olyan gépekkel rendelkezik, amelyeknek nincs internetkapcsolata, le kell töltenie és telepítenie kell Log Analytics-átjárót.

Ezek az ügynökök csak akkor szükségesek, ha függőségi vizualizációt használ.

## <a name="can-i-use-an-existing-workspace"></a>Használhatok egy meglévő munkaterületet?

Igen, csatlakoztathat egy meglévő munkaterületet az áttelepítési projekthez, és használhatja azt a függőségi vizualizációhoz. [További információk](concepts-dependency-visualization.md#how-does-it-work).

## <a name="can-i-export-the-dependency-visualization-report"></a>Exportálhatók a függőségi vizualizációs jelentés?

Nem, a függőségi vizualizációt nem lehet exportálni. A Azure Migrate azonban Service Mapt használ, és az [Service Map REST API](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) használatával kérheti le a függőségeket JSON formátumban.

## <a name="can-i-automate--mmadependency-agent-installation"></a>Automatizálható az MMA/függőségi ügynök telepítése?

Ezzel [a parancsfájllal telepítheti a függőségi ügynököt](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples). Kövesse ezeket az [utasításokat az MMA telepítéséhez](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) a parancssor vagy az Automation használatával. Az MMA esetében használja [ezt a parancsfájlt](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).

A parancsfájlok mellett olyan központi telepítési eszközöket is használhat, mint a System Center Configuration Manager és a [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) az ügynökök üzembe helyezéséhez.


## <a name="what-operating-systems-does-mma-support"></a>Milyen operációs rendszereket támogat az MMA?

- Tekintse meg az [MMA által támogatott Windows operációs rendszerek](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)listáját.
- Megtekintheti az [MMA által támogatott Linux operációs rendszerek](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems)listáját.

## <a name="can-i-visualize-dependencies-for-more-than-an-hour"></a>Megjeleníthető-e a függőségek több mint egy óráig?
Nem. A függőségeket akár egy óráig is megjelenítheti. Visszatérhet egy adott dátumhoz az előzményekben, egy hónapra visszamenőleg, de a vizualizációk maximális időtartama egy óra. Használhatja például az időtartamot a függőségi térképen a tegnapi függőségek megjelenítéséhez, de csak egyórás időszakra vonatkozóan tekintheti meg a függőségeket. A [függőségi adat](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) hosszabb időtartamon keresztül történő lekérdezéséhez azonban Azure monitor naplókat is használhat.

## <a name="can-visualize-dependencies-for-groups-of-more-than-10-vms"></a>Képes megjeleníteni a több mint 10 virtuális gépen álló csoportok függőségeit?
A legfeljebb 10 virtuális gépet tartalmazó csoportok [függőségeit megjelenítheti](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) . Ha több mint 10 virtuális géppel rendelkező csoporttal rendelkezik, javasoljuk, hogy ossza szét a csoportot kisebb csoportokba, majd jelenítse meg a függőségeket.




## <a name="next-steps"></a>Következő lépések
Olvassa el a [Azure Migrate áttekintést](migrate-services-overview.md).

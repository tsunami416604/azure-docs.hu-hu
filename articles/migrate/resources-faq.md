---
title: Gyakori kérdések a Azure Migrate
description: Választ kaphat a Azure Migrate szolgáltatással kapcsolatos gyakori kérdésekre.
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: snehaa
ms.openlocfilehash: dc7dff0119ec849b447754ae54a45911038f6c48
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74284470"
---
# <a name="azure-migrate-common-questions"></a>Azure Migrate: gyakori kérdések

Ez a cikk a Azure Migrateával kapcsolatos gyakori kérdésekre ad választ. Ha további lekérdezésekre van szüksége a cikk elolvasása után, tegye fel őket a [Azure Migrate fórumba](https://aka.ms/AzureMigrateForum).

## <a name="general"></a>Általános kérdések

### <a name="which-azure-geographies-are-supported"></a>Mely Azure földrajzi területek támogatottak?

Tekintse át a [VMWare virtuális gép](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) és a [Hyper-V virtuális gépek](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects)Azure Migrate támogatott földrajzi területeit.

### <a name="whats-the-difference-between-azure-migrate-and-site-recovery"></a>Mi a különbség a Azure Migrate és az Site Recovery között?

Azure Migrate központosított központot biztosít a helyszíni gépek és számítási feladatok Azure-ba való felderítésének, értékelésének és áttelepítésének kezeléséhez és nyomon követéséhez. [Azure site Recovery](../site-recovery/site-recovery-overview.md) vész-helyreállítási megoldás. Azure Migrate: a kiszolgáló áttelepítési eszköze néhány háttérrendszer-Site Recovery funkciót használ a helyszíni gépek áttelepítéséhez.

### <a name="how-do-i-delete-an-azure-migrate-project"></a>Hogyan törölni egy Azure Migrate projektet?

A projektek törléséhez kövesse az [alábbi utasításokat](how-to-delete-project.md) . [Tekintse át azokat az erőforrásokat](how-to-delete-project.md#created-resources) , amelyeket a rendszer egy Azure Migrate projektben felderített, értékel és telepít át gépeket és munkaterheléseket.


## <a name="azure-migrate-appliance"></a>Azure Migrate-berendezés

### <a name="how-does-the-appliance-connect-to-azure"></a>Hogyan kapcsolódik a készülék az Azure-hoz?

A kapcsolat lehet az interneten keresztül, vagy használhatja az Azure ExpressRoute nyilvános/Microsoft-partnerekkel.

### <a name="what-network-connectivity-is-needed-for-azure-migrate-server-assessment-and-migration"></a>Milyen hálózati kapcsolatra van szükség a Azure Migrate-kiszolgáló felméréséhez és áttelepítéséhez?

Tekintse át a [VMware](migrate-support-matrix-vmware.md) és a [Hyper-V](migrate-support-matrix-hyper-v.md) támogatási mátrixait azokról az URL-címekről és portokról, amelyek szükségesek az Azure-hoz való kommunikáció Azure Migrateához.

### <a name="can-i-harden-the-appliance-vm-created-with-the-template"></a>Megerősíthető a sablonnal létrehozott készülék virtuális gépe?

Hozzáadhat összetevőket (például víruskeresőt) a sablonhoz, feltéve, hogy a Azure Migrate berendezéshez szükséges kommunikációs és tűzfalszabályok megmaradnak.

### <a name="what-data-is-collected-by-the-azure-migrate-appliance"></a>Milyen adatokat gyűjtenek a Azure Migrate berendezés?

Tekintse át a készülék által gyűjtött adatokat a következőképpen:
- [Teljesítményadatokat](migrate-appliance.md#collected-performance-data-vmware) és [metaadatokat](migrate-appliance.md#collected-metadata-vmware) a VMWare virtuális gépekhez.
- [Teljesítményadatokat](migrate-appliance.md#collected-performance-data-hyper-v) és [metaadatokat](migrate-appliance.md#collected-metadata-hyper-v) a Hyper-V virtuális gépekhez.


### <a name="does-appliance-analysis-impact-performance"></a>Befolyásolja a készülék elemzése a teljesítményt?

A Azure Migrate berendezés a helyszíni gépeket folyamatosan a virtuális gép teljesítményadatait méri. Ez a profilkészítés szinte semmilyen hatással nincs a Hyper-V/ESXi-gazdagépekre vagy vCenter Serverra.

### <a name="where-and-how-long-is-collected-data-stored"></a>Hol és mennyi ideig tárolja a rendszer az összegyűjtött adatokat?

Az Azure Migrate készülék által gyűjtött adatokat az Azure-beli helyen tárolja, amelyet az áttelepítési projekt létrehozásakor választott ki. Az adattárolást a Microsoft-előfizetések biztonságosan tárolják, és törlődnek a Azure Migrate projekt törlésekor.

A függőségi vizualizációk esetében az összegyűjtött adatokat az Azure-előfizetésben létrehozott Log Analytics munkaterület Egyesült Államok tárolja. Ezek az adatai törlődnek, amikor törli a Log Analytics munkaterületet az előfizetésében. [További](concepts-dependency-visualization.md) információ a függőségi vizualizációról.

### <a name="what-volume-of-data-is-uploaded-during-continuous-profiling"></a>Milyen mennyiségű adattal töltődik fel a rendszer a folyamatos profilkészítés során?

A Azure Migrate elküldett adatok mennyisége több paramétertől függ. Annak érdekében, hogy a kötet értelmet nyújtson, a 10 gépet tartalmazó Azure Migrate-projektek (amelyek mindegyike egy lemezzel és egy hálózati adapterrel) naponta 50 MB-ot küld. Ez az érték közelítő, és a hálózati adapterek és a lemezek adatpontjainak száma alapján változik. A továbbított adatmennyiség növekedése nem lineáris, ha a gépek, hálózati adapterek vagy lemezek száma növekszik.

### <a name="is-the-data-encrypted-at-rest-and-in-transit"></a>Titkosítva vannak-e az adatok a nyugalmi állapotban és az átvitel során?

Igen, mindkettő.
- A metaadatokat a rendszer biztonságosan továbbítja a Azure Migrate szolgáltatásnak az interneten keresztül, HTTPS-kapcsolaton keresztül.
- A metaadatok egy [Azure Cosmos adatbázis](../cosmos-db/database-encryption-at-rest.md) -adatbázisban és az [Azure Blob Storage](../storage/common/storage-service-encryption.md) -ban tárolódnak egy Microsoft-előfizetésben. A metaadatok titkosítva vannak a REST-ben.
- A függőségek elemzéséhez használt adatforgalom is titkosítva van (biztonságos HTTPS). Ez az előfizetés egy Log Analytics munkaterületén van tárolva. Emellett a REST-titkosítás is titkosítva van.

### <a name="how-does-the-appliance-communicate-with-vcenter-server-and-azure-migrate"></a>Hogyan kommunikál a készülék a vCenter Server és Azure Migrate?

1. A berendezés a berendezés beállításakor megadott hitelesítő adatok használatával csatlakozik a vCenter Serverhoz (443-es port).
2. A készülék VMware PowerCLI-t használ a vCenter Server lekérdezéséhez, hogy metaadatokat gyűjtsön a vCenter Server által felügyelt virtuális gépekről. Konfigurációs adatokat gyűjt a virtuális gépekről (magok, memória, lemezek, hálózati adapterek) és az összes virtuális gép teljesítménybeli előzményeiről az elmúlt hónapban.
3. Ezután az összegyűjtött metaadatokat a rendszer a Azure Migrate: Server Assessment (az interneten keresztül HTTPS-kapcsolaton keresztül) értékelés céljából elküldi.

### <a name="can-i-connect-the-same-appliance-to-multiple-vcenter-server-instances"></a>Több vCenter Server példányhoz is csatlakozhatok ugyanahhoz a berendezéshez?

Nem. A készülék és a vCenter Server között egy-az-egyhez típusú hozzárendelés található. Több vCenter Server példányon futó virtuális gépek felderítéséhez több berendezést kell telepítenie.


### <a name="machine-size-changed-can-i-run-the-assessment-again"></a>A gép mérete megváltozott. Futtathatom újra az értékelést?

A Azure Migrate berendezés folyamatosan gyűjt információkat a helyszíni környezetről. Az értékelés azonban a helyszíni virtuális gépekre vonatkozó időponthoz kapcsolódó pillanatkép. Ha módosítja egy olyan virtuális gép beállításait, amelyet fel szeretne mérni, használja az újraszámítási lehetőséget az értékelés frissítéséhez a legújabb módosításokkal.

### <a name="how-can-i-perform-discovery-in-a-multitenant-environment"></a>Hogyan végezhető el a felderítés több-bérlős környezetben?

- A VMware esetében, ha a környezet a bérlők között van megosztva, és nem szeretné felderíteni egy másik bérlő előfizetésében lévő egyik bérlő virtuális gépeket, hozzon létre vCenter Server hitelesítő adatokat, amelyek csak a felderíteni kívánt virtuális gépeket érhetik el. Ezután használja ezeket a hitelesítő adatokat, amikor elindítja a felderítést a Azure Migrate berendezésben.
- A Hyper-V esetében a felderítés a Hyper-V gazdagép hitelesítő adatait használja. Ha a virtuális gépek ugyanazt a Hyper-V-gazdagépet használják, jelenleg nincs lehetőség a felderítés elválasztására.  

### <a name="how-many-vms-can-i-discover-with-a-single-appliance"></a>Hány virtuális gépet lehet felderíteni egyetlen készülékkel?

Akár 10 000 VMware virtuális gépet és akár 5 000 Hyper-V virtuális gépet is felfedezhet egyetlen áttelepítési berendezéssel. Ha több géppel rendelkezik a helyszíni környezetben, Ismerje meg, hogyan méretezheti a [Hyper-V](scale-hyper-v-assessment.md) és a [VMware](scale-vmware-assessment.md) értékelését.

### <a name="can-i-delete-the-azure-migrate-appliance-from-the-project"></a>Törölhetem a Azure Migrate készüléket a projektből?

A berendezés jelenleg a projektből való törlése nem támogatott.

- A készülék törlésének egyetlen módja a berendezéshez társított Azure Migrate projektet tartalmazó erőforráscsoport törlése.
- Az erőforráscsoport törlése azonban törli a többi regisztrált készüléket, a felderített leltárt, az értékeléseket és az erőforráscsoport projekthez társított összes többi Azure-összetevőt is.

## <a name="azure-migrate-server-assessment"></a>Azure Migrate kiszolgáló értékelése


### <a name="does-azure-migrate-need-vcenter-server-for-vmware-vm-discovery"></a>A VMWare virtuális gépek felderítéséhez Azure Migrate szükséges vCenter Server?

Igen, Azure Migrate a felderítést a VMware-környezetben vCenter Server kell végrehajtania. Nem támogatja az vCenter Server által nem felügyelt ESXi-gazdagépek felderítését.

### <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Mi a különbség a Azure Migrate Server Assessment és a MAP Toolkit között?

A kiszolgáló értékelése értékelést nyújt az áttelepítésre való felkészüléshez és az Azure-ba való áttelepítéshez szükséges munkaterhelések kiértékeléséhez. A [Microsoft felmérési és tervezési (MAP) eszközkészlete](https://www.microsoft.com/download/details.aspx?id=7826) más feladatokkal is segít, beleértve a Windows ügyfél/kiszolgáló operációs rendszerek újabb verzióinak áttelepítésének megtervezését és a szoftverek használatának nyomon követését. Ezekben a forgatókönyvekben folytassa a MAP Toolkit használatát.

### <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>Mi a különbség a kiszolgálók értékelése és a Site Recovery Deployment Planner között?

A kiszolgáló értékelése egy áttelepítési tervező eszköz. A Site Recovery Deployment Planner vész-helyreállítási tervezési eszköz.

- Helyszíni **áttelepítés megtervezése az Azure-** ba: Ha a helyszíni kiszolgálókat az Azure-ba szeretné áttelepíteni, használja az áttelepítés megtervezésére szolgáló kiszolgáló-értékelést. Felméri a helyszíni számítási feladatokat, és útmutatást és eszközöket biztosít a Migrálás megkönnyítéséhez. Az áttelepítési terv betartása után az eszközöket, például Azure Migrate kiszolgáló áttelepítését is használhatja a gépek Azure-ba való áttelepítéséhez.
- Vész- **helyreállítási terv az Azure**-ba: Ha a helyszíni rendszerről az Azure-ba történő vész-helyreállítást tervezi a site Recovery használatával, használja a site Recovery Deployment Planner. A Deployment Planner a helyi környezet alapos, Site Recovery-specifikus értékelését biztosítja a vész-helyreállítási célra. Javaslatokat nyújt a vész-helyreállítási megoldásokról, például a replikálásról és a feladatátvételről.

### <a name="whats-the-difference-between-as-on-premises-and-performance-based-sizing"></a>Mi a különbség a helyszíni és a teljesítmény-alapú méretezés között?

Helyszíni méretezés esetén a Azure Migrate nem veszi figyelembe a virtuális gépek teljesítményadatait az értékeléshez. A helyszíni konfiguráció alapján felméri a virtuális gépek méretét. A teljesítmény-alapú méretezéssel a méretezés a kihasználtsági adatain alapul.

- Ha például egy helyszíni virtuális gép 4 maggal és 8 GB memóriával rendelkezik, 50%-os CPU-kihasználtsággal és 50%-os memória-kihasználtsággal, a következők történnek:
    - A helyszíni méretezés olyan Azure VM SKU-t javasol, amely négy maggal és 8 GB memóriával rendelkezik.
    - A teljesítmény-alapú méretezés olyan virtuálisgép-SKU-t javasol, amely két maggal és 4 GB memóriával rendelkezik, mert a kihasználtság százalékos arányát veszi figyelembe.

- Hasonlóképpen, a lemez méretezése két értékelési tulajdonságtól függ: a méretezési feltételektől és a tárolási típustól.
    - Ha a méretezési feltétel teljesítmény-alapú, és a tárolási típus automatikus, Azure Migrate a lemez IOPS és átviteli értékeit veszi figyelembe, amikor azonosítja a céllemez típusát (standard vagy prémium).
    - Ha a méretezési feltétel teljesítmény-alapú, és a tárolási típus prémium, Azure Migrate a helyszíni lemez méretétől függően a prémium szintű lemezes SKU-t javasolja. Ugyanez a logikát alkalmazza a lemez méretezésére, amikor a méretezés a helyszínen történik, és a tárolási típus a standard vagy a prémium.

### <a name="does-performance-history-and-utilization-percentile-impact-size-recommendations"></a>A teljesítmény-előzmények és a kihasználtság százalékos arányának hatása a javaslatok?

Ez a tulajdonságok csak a teljesítményalapú méretezés esetén számítanak.

- Azure Migrate gyűjti a helyszíni gépek teljesítményének előzményeit, és azt használja a virtuális gép méretének és a lemez típusának az Azure-ban való használatát.
- A berendezés folyamatosan a helyszíni környezetet, a valós idejű kihasználtsági adatok gyűjtését végzi 20 másodpercenként.
- A készülék összesíti a 20 másodperces mintákat, és 15 percenként létrehoz egyetlen adatpontot.
- Az adatpont létrehozásához a berendezés a 20 másodperces mintákból kiválasztja a csúcsérték értékét.
- A készülék elküldi ezt az adatpontot az Azure-nak.

Amikor értékelést hoz létre az Azure-ban, a teljesítmény időtartama és a teljesítményi előzmények percentilis értéke alapján Azure Migrate kiszámítja a tényleges kihasználtság értékét, és azt használja a méretezéshez.

- Ha például egy napra állítja be a teljesítmény időtartamát, a percentilis értékét pedig 95 percentilis értékre, Azure Migrate rendezi a gyűjtő által az elmúlt napra vonatkozóan a növekvő sorrendben eljuttatott 15 perces mintavételi pontokat, és a 95. percentilis értékét a hatályos értékre választja. kihasználtsága.
- A 95. percentilis értékének használata esetén a rendszer figyelmen kívül hagyja a kiugró értékeket. A kiugró értékek a esetek 99% percentilis használata esetén szerepelhetnek. Ha ki szeretné választani az időszak maximális kihasználtságát anélkül, hogy a kiugró értékek hiányoznak, válassza ki a esetek 99% percentilis értéket.

## <a name="server-assessment---dependency-visualization"></a>Kiszolgáló értékelése – függőségi vizualizáció


### <a name="what-is-dependency-visualization"></a>Mi a függőségi vizualizáció?

A függőségi vizualizáció használatával felmérhetővé teheti a virtuális gépek csoportjait a nagyobb megbízhatóság érdekében. A függőségi vizualizációk átvizsgálják a számítógép függőségeit az értékelés futtatása előtt. Ezzel biztosítható, hogy semmi ne maradjon hátra, így elkerülhető a váratlan kimaradások elkerülése az Azure-ba való Migrálás során. Azure Migrate a függőségi vizualizáció engedélyezéséhez a Azure Monitor Service Map megoldását használja. [További információ](concepts-dependency-visualization.md).

> [!NOTE]
> A függőségi vizualizáció nem érhető el Azure Governmentban.

### <a name="do-i-need-to-pay-to-use-dependency-visualization"></a>Fizetnem kell a függőségi vizualizáció használatához?
Nem. [Itt talál további információkat](https://azure.microsoft.com/pricing/details/azure-migrate/) az Azure Migrate díjszabásáról.

### <a name="do-i-need-to-install-anything-for-dependency-visualization"></a>Telepíteni kell bármit a függőségi vizualizációhoz?

A függőségi vizualizáció használatához le kell töltenie és telepítenie kell az ügynököket minden olyan helyszíni gépen, amelyet ki szeretne értékelni.

A következő ügynököket kell telepítenie mindegyik gépre:
- [Microsoft monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).
- [Függőségi ügynök](../azure-monitor/platform/agents-overview.md#dependency-agent).
- Ha olyan gépekkel rendelkezik, amelyeknek nincs internetkapcsolata, le kell töltenie és telepítenie kell Log Analytics-átjárót.

Ezek az ügynökök csak akkor szükségesek, ha függőségi vizualizációt használ.

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>Használhatok egy meglévő munkaterületet a függőségi vizualizációhoz?

Igen, csatlakoztathat egy meglévő munkaterületet az áttelepítési projekthez, és használhatja azt a függőségi vizualizációhoz. [További információ](concepts-dependency-visualization.md#how-does-it-work).

### <a name="can-i-export-the-dependency-visualization-report"></a>Exportálhatók a függőségi vizualizációs jelentés?

Nem, a függőségi vizualizációt nem lehet exportálni. A Azure Migrate azonban Service Mapt használ, és az [Service Map REST API](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) használatával kérheti le a függőségeket JSON formátumban.

### <a name="how-can-i-automate-the-installation-of-microsoft-monitoring-agent-mma-and-the-dependency-agent"></a>Hogyan automatizálható a Microsoft monitoring Agent (MMA) és a függőségi ügynök telepítése?

Ezzel [a parancsfájllal telepítheti a függőségi ügynököt](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples). Kövesse ezeket az [utasításokat az MMA telepítéséhez](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) a parancssor vagy az Automation használatával. Az MMA esetében használja [ezt a parancsfájlt](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).

A parancsfájlok mellett olyan központi telepítési eszközöket is használhat, mint a System Center Configuration Manager és a [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) az ügynökök üzembe helyezéséhez.


### <a name="what-operating-systems-are-supported-by-mma"></a>Milyen operációs rendszereket támogat az MMA?

- Tekintse meg az [MMA által támogatott Windows operációs rendszerek](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)listáját.
- Megtekintheti az [MMA által támogatott Linux operációs rendszerek](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems)listáját.

### <a name="can-i-visualize-dependencies-for-more-than-an-hour"></a>Megjeleníthető-e a függőségek több mint egy óráig?
Nem. A függőségeket akár egy óráig is megjelenítheti. Visszatérhet egy adott dátumhoz az előzményekben, egy hónapra visszamenőleg, de a vizualizációk maximális időtartama egy óra. Használhatja például az időtartamot a függőségi térképen a tegnapi függőségek megjelenítéséhez, de csak egyórás időszakra vonatkozóan tekintheti meg a függőségeket. A [függőségi adat](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) hosszabb időtartamon keresztül történő lekérdezéséhez azonban Azure monitor naplókat is használhat.

### <a name="can-i-use-dependency-visualization-for-groups-of-more-than-10-vms"></a>Használhatom a függőségi vizualizációt több mint 10 virtuális gépet használó csoportok esetében?
A legfeljebb 10 virtuális gépet tartalmazó csoportok [függőségeit megjelenítheti](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) . Ha több mint 10 virtuális géppel rendelkező csoporttal rendelkezik, javasoljuk, hogy ossza szét a csoportot kisebb csoportokba, majd jelenítse meg a függőségeket.

## <a name="azure-migrate-server-migration"></a>Azure Migrate kiszolgáló áttelepítése

### <a name="whats-the-difference-between-azure-migrate-server-migration-and-site-recovery"></a>Mi a különbség a Azure Migrate kiszolgáló áttelepítése és Site Recovery között?

- A helyszíni VMware virtuális gépek ügynök nélküli áttelepítése Azure Migrate kiszolgáló áttelepítésen belül natív.
- A Hyper-V virtuális gépek, a fizikai kiszolgálók és az ügynökön alapuló VMware virtuális gépek áttelepítéséhez Azure Migrate kiszolgáló áttelepítése a Azure Site Recovery replikációs motort használja.


## <a name="next-steps"></a>Következő lépések
Olvassa el a [Azure Migrate áttekintést](migrate-services-overview.md).

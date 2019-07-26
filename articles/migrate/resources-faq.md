---
title: Azure Migrate – gyakori kérdések (GYIK) | Microsoft Docs
description: A Azure Migrateekkel kapcsolatos gyakori kérdések címei
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: snehaa
ms.openlocfilehash: 0708502087ae6880d9559cf17f0ba9982b2ba040
ms.sourcegitcommit: 57a7d4f67635212f5bf0c56e58fd87c8ec366f2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68372486"
---
# <a name="azure-migrate---frequently-asked-questions-faq"></a>Azure Migrate – gyakran ismételt kérdések (GYIK)

Ez a cikk Azure Migrateokkal kapcsolatos gyakori kérdéseket tartalmaz. Ha további lekérdezésekre van szüksége a cikk elolvasása után, tegye közzé őket a [Azure Migrate fórumon](https://aka.ms/AzureMigrateForum).

## <a name="general"></a>Általános

### <a name="which-azure-geographies-are-supported-by-azure-migrate"></a>Mely Azure-földrajzi területek támogatottak Azure Migrate?

Itt megtalálja a [VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) listáját és a [Hyper-V-t itt](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects).

### <a name="how-is-azure-migrate-different-from-azure-site-recovery"></a>Miben különbözik Azure Migrate Azure Site Recovery?

Azure Migrate központosított hubot biztosít a gépek és a számítási feladatok Azure-ba való felderítésének, értékelésének és áttelepítésének elindításához, végrehajtásához és nyomon követéséhez. [Azure site Recovery](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure) vész-helyreállítási megoldás. A Azure Migrate-kiszolgáló áttelepítése a háttérbeli Azure Site Recovery segítségével lehetővé teszi az áttelepítési forgatókönyvek használatát a helyszíni gépek áttelepítéséhez.

## <a name="azure-migrate-appliance-vmwarephysical-servers"></a>Azure Migrate berendezés (VMware/fizikai kiszolgálók)

### <a name="how-does-the-azure-migrate-appliance-connect-to-azure"></a>Hogyan kapcsolódik az Azure Migrate készülék az Azure-hoz?

A kapcsolat lehet az interneten keresztül, vagy használhatja a ExpressRoute nyilvános/Microsoft-partnerekkel.

### <a name="what-network-connectivity-requirements-are-needed-for-azure-migrate-server-assessment-and-migration"></a>Milyen hálózati kapcsolati követelmények szükségesek Azure Migrate kiszolgáló értékeléséhez és áttelepítéséhez

A Azure Migrate az Azure-ban való kommunikációhoz szükséges URL-címek és portok esetében tekintse át a [VMware](migrate-support-matrix-vmware.md) és a [Hyper-V](migrate-support-matrix-hyper-v.md) támogatási mátrixait.

### <a name="can-i-harden-the-appliance-vm-i-set-up-with-the-template"></a>Megkeményítem a készüléket a sablonnal beállított virtuális gépen?

A sablonba további összetevők (például víruskereső) is hozzáadhatók, feltéve, hogy a Azure Migrate berendezéshez szükséges kommunikációs és tűzfalszabályok a következő módon maradnak.   

### <a name="what-data-is-collected-by-azure-migrate-appliance"></a>Milyen adatokat gyűjtenek Azure Migrate készülék?

Azure Migrate készülék által gyűjtött adatokról [itt](https://docs.microsoft.com/azure/migrate/migrate-appliance#collected-performance-data-vmware)olvashat részletesen.

### <a name="is-there-any-performance-impact-on-the-analyzed-vmware-or-hyper-v-environment"></a>Hatással van-e az elemzett VMware vagy Hyper-V környezet teljesítményére?

A teljesítményadatok folyamatos profilkészítésével a Azure Migrate berendezés a helyszíni gépeket a virtuális gép teljesítményadatait méri. Ez szinte nulla teljesítménnyel kapcsolatos hatással van a Hyper-V/ESXi gazdagépekre, valamint a vCenter Serverra.

### <a name="where-is-the-collected-data-stored-and-for-how-long"></a>Hol tárolódnak a gyűjtött adatok és mennyi ideig?

Az Azure Migrate készülék által gyűjtött adatokat az Azure-beli helyen tárolja a rendszer, amelyet az áttelepítési projekt létrehozásakor adott meg. Az adattárolást a Microsoft-előfizetések biztonságosan tárolják, és törlődnek a Azure Migrate projekt törlésekor.

A függőségi vizualizációk esetében, ha ügynököket telepít a virtuális gépekre, a függőségi ügynökök által gyűjtött adatokat az Egyesült Államokban tárolja az Azure-előfizetésben létrehozott Log Analytics munkaterületen. Ezek az adatai törlődnek, amikor törli a Log Analytics munkaterületet az előfizetésében. [További információk](concepts-dependency-visualization.md).

### <a name="what-is-the-volume-of-data-uploaded-by-azure-migrate-appliance-during-continuous-profiling"></a>Milyen mennyiségű adatmennyiséget töltöttek fel Azure Migrate berendezés a folyamatos profilkészítés során?

A Azure Migrate elküldett adatok mennyisége több paraméter alapján változhat. Ha egy indikatív számot szeretne adni, egy 10 gépet tartalmazó Azure Migrate-projekt (mindkettő egy lemezzel és egy hálózati adapterrel) körülbelül 50 MB-ot küld naponta. Ez egy hozzávetőleges érték, amely a hálózati adapterek és lemezek adatpontjainak száma alapján változik (az elküldhető adat nem lineáris, ha a gépek, hálózati adapterek vagy lemezek száma nő).

### <a name="is-the-data-encrypted-at-rest-and-in-transit"></a>Titkosítva van-e az adatok a REST és a átvitel során?

Igen, mindkettőhöz. A metaadatokat a rendszer biztonságosan továbbítja a Azure Migrate szolgáltatásnak az interneten keresztül, HTTPS-kapcsolaton keresztül. A metaadatok tárolása egy [Cosmos db](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest), az [Azure Blob Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) -ban pedig egy Microsoft-előfizetésben történik, és a rendszer a REST-alapú titkosítást tárolja.

A függőségi ügynökök által gyűjtött adatokat a rendszer az átvitel során is titkosítja (Secure HTTPS), és a felhasználói előfizetés Log Analytics munkaterületén tárolja. Emellett titkosítva van.

### <a name="how-does-the-azure-migrate-appliance-communicate-with-the-vcenter-server-and-the-azure-migrate-service"></a>Hogyan kommunikál a Azure Migrate készülék a vCenter Server és a Azure Migrate szolgáltatással?

A berendezés a berendezés beállításakor megadott hitelesítő adatok használatával csatlakozik a vCenter Serverhoz (443-es port). A VMware PowerCLI használatával kérdezi le a vCenter Server a vCenter Server által felügyelt virtuális gépekkel kapcsolatos metaadatok gyűjtéséhez. A rendszer a virtuális gépek (magok, memória, lemezek, hálózati adapterek stb.) konfigurációs adatait, valamint az előző hónap egyes virtuális gépei teljesítménybeli előzményeit gyűjti. Ezután az összegyűjtött metaadatokat a rendszer a Azure Migrate Server Assessment (az interneten keresztül HTTPS) használatával továbbítja az értékeléshez.

### <a name="can-i-connect-the-same-appliance-to-multiple-vcenter-servers"></a>Csatlakozhatok ugyanahhoz a berendezéshez több vCenter-kiszolgálóhoz is?

Igen, egyetlen Azure Migrate berendezés használatával több vCenter-kiszolgáló is felderíthető, de nem párhuzamosan. A felfedezéseket egy másik után kell futtatnia.

### <a name="i-changed-my-machine-size-can-i-rerun-an-assessment"></a>Módosítottam a gép méretét. Újrajátszható egy Értékelés?

A Azure Migrate berendezés folyamatosan gyűjt információkat a helyszíni környezetről. Az értékelés azonban a helyszíni virtuális gépekre vonatkozó időponthoz kapcsolódó pillanatkép. Ha módosítja egy felmérni kívánt virtuális gép beállításait, az "újraszámítás" lehetőség használatával frissítse az értékelést a legújabb módosításokkal.

### <a name="how-can-i-discover-a-multi-tenant-environment-in-azure-migrate-server-assessment"></a>Hogyan deríthető fel több-bérlős környezet a Azure Migrate Server Assessment szolgáltatásban?

VMware esetén, ha olyan környezettel rendelkezik, amely a bérlők között van megosztva, és nem szeretné felderíteni egy másik bérlő előfizetésében lévő egyik bérlő virtuális gépeket, hozzon létre vCenter Server hitelesítő adatokat csak a felderíteni kívánt virtuális gépekhez. Ezután használja a hitelesítő adatokat, amikor kirúgja a felderítést a Azure Migrate berendezésben.

A Hyper-V esetében a felderítés a Hyper-V gazdagép hitelesítő adatait használja, ha a virtuális gépek ugyanazt a Hyper-V-gazdagépet használják, jelenleg nincs lehetőség a felderítés elválasztására.  

### <a name="how-many-vms-can-be-discovered-using-a-single-migration-appliance"></a>Hány virtuális gépet lehet felderíteni egyetlen áttelepítési készülék használatával?

Akár 10 000 VMware virtuális gép és akár 5 000 Hyper-V virtuális gép is felderíthető egyetlen áttelepítési berendezés használatával.  Ha több géppel rendelkezik a helyszíni környezetben, Ismerje meg, hogyan méretezheti a [Hyper-V](scale-hyper-v-assessment.md) és a [VMware](scale-vmware-assessment.md) értékelését.

## <a name="azure-migrate-server-assessment"></a>Azure Migrate: Server Assessment

### <a name="does-azure-migrate-server-assessment-support-assessment-of-physical-servers"></a>Azure Migrate: A kiszolgáló értékelése támogatja a fizikai kiszolgálók értékelését?

Nem, Azure Migrate jelenleg nem támogatja a fizikai kiszolgálók értékelését.

### <a name="does-azure-migrate-need-vcenter-server-to-discover-a-vmware-environment"></a>A VMware-környezet felderítéséhez Azure Migrate szükséges vCenter Server?

Igen, Azure Migrate a VMware-környezet felderítéséhez vCenter Server szükséges. Nem támogatja az vCenter Server által nem felügyelt ESXi-gazdagépek felderítését.

### <a name="whats-the-difference-between-using-azure-migrate-server-assessment-and-the-map-toolkit"></a>Mi a különbség a Azure Migrate használata esetén: Kiszolgáló értékelése és a Map Toolkit?

Azure Migrate: A kiszolgáló értékelése áttelepítési értékelést biztosít az áttelepítés készültségének biztosításához, valamint az Azure-ba való áttelepítéshez szükséges munkaterhelések kiértékeléséhez. A [Microsoft Assessment and Planning (MAP) eszközkészlet](https://www.microsoft.com/download/details.aspx?id=7826) más funkciókkal is rendelkezik, például a Windows ügyfél és kiszolgáló operációs rendszereinek újabb verzióinak áttelepítésének megtervezése, valamint a szoftverek használatának nyomon követése. Ilyen esetekben folytassa a MAP Toolkit használatát.

### <a name="how-is-azure-migrate-server-assessment-different-from-azure-site-recovery-deployment-planner"></a>Azure Migrate: A kiszolgáló értékelése eltér a Azure Site Recovery Deployment Plannertól?

Azure Migrate: A kiszolgáló értékelése egy áttelepítési tervező eszköz. Azure Site Recovery Deployment Planner a vész-helyreállítási tervezési eszköz.

- **Áttelepítés VMware/Hyper-V-ről az Azure-** ba: Ha a helyszíni kiszolgálókat az Azure-ba kívánja áttelepíteni, használja a Azure Migrate: Kiszolgáló-értékelési eszköz az áttelepítés megtervezéséhez. Az eszköz felméri a helyszíni számítási feladatokat, és útmutatást, megállapításokat és mechanizmusokat biztosít az Azure-ba való Migrálás támogatásához. Ha készen áll az áttelepítési tervre, olyan eszközöket is használhat, mint például a Azure Migrate: A kiszolgálók áttelepítése a gépek Azure-ba való áttelepítéséhez.
- Vész- **helyreállítás a VMware/Hyper-V-ről az Azure-** ba: Az Azure-ban Site Recovery használatával történő vész-helyreállításhoz használja a Site Recovery Deployment Planner a vész-helyreállítás megtervezéséhez. Site Recovery Deployment Planner a helyszíni környezet alapos, Site Recovery-specifikus értékelését végzi el. Ajánlásokat tesz Site Recovery számára a sikeres vészhelyzeti műveletekhez, például a replikáláshoz és a virtuális gépek feladatátvételéhez.

### <a name="does-azure-migrate-support-enterprise-agreement-ea-based-cost-estimation"></a>Támogatja a Azure Migrate Nagyvállalati Szerződés (EA) alapú költségbecslés?

Azure Migrate jelenleg nem támogatja [nagyvállalati szerződés ajánlat](https://azure.microsoft.com/offers/enterprise-agreement-support/)költségbecslés. A megkerülő megoldás az, ha az ajánlat szerint az utólagos elszámolású opciót választja, és manuálisan megadja a kedvezmény százalékos arányát (az előfizetésre érvényes) az értékelési tulajdonságok kedvezmény mezőjében.

  ![Kedvezmény](./media/resources-faq/discount.png)

### <a name="whats-the-difference-between-as-on-premises-sizing-and-performance-based-sizing"></a>Mi a különbség a helyszíni méretezés és a teljesítmény-alapú méretezés között?

- A helyszíni méretezés során a Azure Migrate nem veszi figyelembe a virtuális gépek teljesítményadatait. A virtuális gépeket a helyszíni konfiguráció alapján méretezi. – A teljesítmény-alapú méretezés során a méretezés a kihasználtsági adatain alapul.
- Ha például egy helyszíni virtuális gép 4 maggal és 8 GB memóriával rendelkezik, 50%-os CPU-kihasználtsággal és 50%-os memóriával, a helyszíni méretezés azt javasolja, hogy egy Azure-beli VM-SKU négy maggal és 8 GB memóriával rendelkezik. A teljesítmény-alapú méretezés azonban azt ajánlja, hogy a virtuális gép SKU-jának két magot és 4 GB-ot használ, mivel a kihasználtság százalékos arányát veszi figyelembe.
- Hasonlóképpen, a lemez méretezése két értékelési tulajdonságtól függ: a méretezési feltételtől és a tárolási típustól.
= Ha a méretezési feltétel a teljesítmény-alapú, és a tárolási típus automatikus, a rendszer a lemez IOPS és átviteli sebességét veszi figyelembe a céllemez típusának (standard vagy prémium) azonosítása során.
- Ha a méretezési feltétel teljesítmény-alapú, és a tárolási típus prémium, ajánlott prémium szintű lemez. A prémium szintű lemez SKU a helyszíni lemez mérete alapján van kiválasztva. Ugyanez a logikát használja a lemezek méretezésére, ha a méretezési feltétel a helyszíni méretezés, a tárolási típus pedig standard vagy prémium.

### <a name="what-impact-does-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>Milyen hatással van a teljesítmény-előzményekre és a percentilis kihasználtságára a javasolt méret?

Ez a tulajdonságok csak a teljesítményalapú méretezés esetén számítanak.

- Azure Migrate gyűjti a helyszíni gépek teljesítménybeli előzményeit, és a használatával a virtuális gép méretét és a lemez típusát javasolja az Azure-ban.
- A készülék folyamatosan a helyszíni környezetet gyűjti a valós idejű kihasználtsági adatok gyűjtésére 20 másodpercenként. A berendezés összesíti a 20 másodpercenként vett mintákat, és egyetlen adatpontot hoz létre minden 15 percről. A berendezés úgy hoz létre egyetlen adatpontot, hogy kiválasztja minden 20 másodperces minta csúcsértékét, amelyet elküld az Azure-nak.
- Amikor felmérést hoz létre az Azure-ban (a teljesítmény időtartama és a teljesítményi előzmények percentilis értéke alapján), Azure Migrate kiszámítja a tényleges kihasználtság értékét, és azt használja a méretezéshez.
- Ha például úgy állítja be a teljesítmény időtartamát, hogy az egy nap legyen, és a percentilis értéke 95 percentilis, Azure Migrate az elmúlt nap gyűjtője által elküldett 15 perces mintavételi pontokat használja, a növekvő sorrendbe rendezi azokat, és kiválasztja a 95. percentilis értékét hatékony kihasználtság.
- A 95. percentilis értéke biztosítja, hogy figyelmen kívül hagyja a kiugró adatokat, ami akkor fordulhat elő, ha a esetek 99% percentilis-t használja. Ha az időtartam használati csúcsát szeretné kiválasztani, és nem szeretne lemaradni egyetlen kiugró adatról sem, a 99. percentilist válassza.

### <a name="what-is-dependency-visualization"></a>Mi a függőségi vizualizáció?

A függőségek vizualizációja lehetővé teszi, hogy a nagyobb megbízhatósággal rendelkező virtuális gépek csoportjait mérje fel áttelepítésre. Az értékelés futtatása előtt ellenőrzi a gépek függőségeit. A függőségi vizualizáció segítségével biztosítható, hogy semmi ne maradjon hátra, és elkerülje a váratlan kimaradások elkerülését az Azure-ba való Migrálás során. A Azure Migrate a függőségek vizualizációjának engedélyezéséhez a Service Map megoldást Azure Monitor naplókban használja.

> [!NOTE]
> A függőségi vizualizáció funkció Azure Governmentban nem érhető el.

### <a name="do-i-need-to-pay-to-use-the-dependency-visualization-feature"></a>Fizetnem kell a függőségi vizualizáció funkció használatához?

Nem. [Itt talál további információkat](https://azure.microsoft.com/pricing/details/azure-migrate/) az Azure Migrate díjszabásáról.

### <a name="do-i-need-to-install-anything-for-dependency-visualization"></a>Telepíteni kell bármit a függőségi vizualizációhoz?

A függőségi vizualizáció használatához le kell töltenie és telepítenie kell az ügynököket minden olyan helyszíni gépen, amelyet ki szeretne értékelni.

- A [Microsoft monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows) szolgáltatást minden gépre telepíteni kell.
- A [függőségi ügynököt](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure) minden gépen telepíteni kell.
- Továbbá, ha olyan gépekkel rendelkezik, amelyeknek nincs internetkapcsolata, le kell töltenie és telepítenie kell Log Analytics-átjárót.

Ezek az ügynökök csak akkor szükségesek, ha függőségi vizualizációt használ.

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>Használhatok egy meglévő munkaterületet a függőségi vizualizációhoz?

Igen, csatlakoztathat egy meglévő munkaterületet az áttelepítési projekthez, és kihasználhatja azt a függőségi vizualizációhoz. [További információk](concepts-dependency-visualization.md#how-does-it-work).

### <a name="can-i-export-the-dependency-visualization-report"></a>Exportálhatók a függőségi vizualizációs jelentés?

Nem, a függőségi vizualizációt nem lehet exportálni. Mivel azonban a Azure Migrate Service Mapt használ a függőségi vizualizációhoz, a [Service Map REST API](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) -k használatával kérheti le a függőségeket JSON formátumban.

### <a name="how-can-i-automate-the-installation-of-microsoft-monitoring-agent-mma-and-dependency-agent"></a>Hogyan automatizálható a Microsoft monitoring Agent (MMA) és a függőségi ügynök telepítése?

[Ezt a szkriptet használja](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples) az ügynökök telepítéséhez. [Kövesse ezeket az utasításokat](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) az MMA parancssor vagy Automation használatával történő telepítéséhez. Az MMA esetében használja [ezt a szkriptet](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).

A szkripteken kívül olyan központi telepítési eszközöket is használhat, mint például a System Center Configuration Manager, [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) stb. az ügynökök üzembe helyezéséhez.

### <a name="what-operating-systems-are-supported-by-mma"></a>Milyen operációs rendszereket támogat az MMA?

- [Tekintse át](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems) az MMA által támogatott Windows operációs rendszerek listáját.
- [Tekintse át](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) az MMA által támogatott Linux operációs rendszerek listáját.

### <a name="what-are-the-operating-systems-supported-by-the-dependency-agent"></a>Mik a függőségi ügynök által támogatott operációs rendszerek?

[Tekintse át](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems) a függőségi ügynök által támogatott Windows operációs rendszereket.
[Tekintse át](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems) a függőségi ügynök által támogatott Linux operációs rendszerek listáját.

### <a name="can-i-visualize-dependencies-in-azure-migrate-for-more-than-an-hour"></a>Megjeleníthetem a függőségeket Azure Migrateban több mint egy óráig?
Nem, akár egy óráig is megjelenítheti a függőségeket. Visszatérhet az előzmények egy adott dátumához, akár az előző hónapig, de a vizualizációk maximális időtartama egy óra. Használhatja például az időtartamot a függőségi Térképben a tegnapi függőségek megjelenítéséhez, de csak egy órás időszakra lehet megtekinteni. A [függőségi adat](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) hosszabb időtartamon keresztül történő lekérdezéséhez azonban Azure monitor naplókat is használhat.

### <a name="is-dependency-visualization-supported-for-groups-with-more-than-10-vms"></a>Támogatott a több mint 10 virtuális géppel rendelkező csoportok esetében a függőségi vizualizáció?
A legfeljebb 10 virtuális géppel rendelkező [csoportok függőségeit jelenítheti](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) meg. Ha több mint 10 virtuális géppel rendelkező csoporttal rendelkezik, javasoljuk, hogy ossza szét a csoportot kisebb csoportokba, majd jelenítse meg a függőségeket.

## <a name="azure-migrate-server-migration"></a>Azure Migrate: Server Migration

### <a name="how-is-azure-migrate-server-migration-different-from-azure-site-recovery"></a>Azure Migrate: A kiszolgáló áttelepítése eltér a Azure Site Recoverytól?

Azure Migrate: A kiszolgáló áttelepítése kihasználja Site Recovery replikációs motorját a VMware virtuális gépek ügynök-alapú áttelepítéséhez, a Hyper-V virtuális gépek áttelepítéséhez és a fizikai kiszolgálók áttelepítéséhez az Azure-ba. A VMware virtuális gépek áttelepítésére szolgáló ügynök nélküli beállítás a kiszolgáló migrálása natív módon épül fel.

## <a name="next-steps"></a>További lépések
A [Azure Migrate áttekintése](migrate-services-overview.md)

---
title: Azure Migrate – gyakori kérdések (GYIK) | Microsoft Docs
description: A Azure Migrateekkel kapcsolatos gyakori kérdések címei
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: snehaa
ms.openlocfilehash: 46c6ac52e1afb6c1619b814580a1059fd3dfedda
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70279503"
---
# <a name="azure-migrate-frequently-asked-questions-faq"></a>Azure Migrate: Gyakori kérdések (GYIK)

Ez a cikk a Azure Migrateával kapcsolatos gyakori kérdésekre ad választ. Ha további lekérdezésekre van szüksége a cikk elolvasása után, tegye fel őket a [Azure Migrate fórumba](https://aka.ms/AzureMigrateForum).

## <a name="general"></a>Általános

### <a name="which-azure-geographies-does-azure-migrate-support"></a>Mely Azure-régiók támogatják a Azure Migrate?

Tekintse [meg a VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) és a [Hyper-V listájának](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects)listáját.

### <a name="whats-the-difference-between-azure-migrate-and-azure-site-recovery"></a>Mi a különbség a Azure Migrate és az Azure Site Recovery között?

Azure Migrate központosított hubot biztosít a Migrálás elindításához, a gépek és munkaterhelések felderítésének és értékelésének végrehajtásához, valamint a gépek és számítási feladatok Azure-ba való áttelepítésének végrehajtásához és nyomon követéséhez. [Azure site Recovery](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure) vész-helyreállítási megoldás. Azure Migrate kiszolgáló áttelepítése a háttér-Azure Site Recovery használatával lehetővé teszi az áttelepítési forgatókönyvek használatát a helyszíni gépek áttelepítéséhez.

## <a name="azure-migrate-appliance"></a>Azure Migrate-berendezés

### <a name="how-does-the-azure-migrate-appliance-connect-to-azure"></a>Hogyan kapcsolódik az Azure Migrate készülék az Azure-hoz?

A kapcsolat lehet az interneten keresztül, vagy használhatja az Azure ExpressRoute-t nyilvános/Microsoft-partnerekkel is.

### <a name="what-are-the-network-connectivity-requirements-for-azure-migrate-server-assessment-and-migration"></a>Milyen hálózati kapcsolati követelmények vonatkoznak a Azure Migrate Server Assessment and Migration szolgáltatásra?

Tekintse meg a [VMware](migrate-support-matrix-vmware.md) és a [Hyper-V](migrate-support-matrix-hyper-v.md) támogatási mátrixait a Azure Migrate az Azure-ban való kommunikációhoz szükséges URL-címekkel és portokkal kapcsolatos információkért.

### <a name="can-i-harden-the-appliance-vm-that-i-set-up-with-the-template"></a>Megerősítem azt a készüléket használó virtuális gépet, amelyet a sablonnal állítottam be?

Hozzáadhat összetevőket (például víruskeresőt) a sablonhoz, feltéve, hogy a Azure Migrate berendezéshez szükséges kommunikációs és tűzfalszabályok megmaradnak.

### <a name="what-data-is-collected-by-the-azure-migrate-appliance"></a>Milyen adatokat gyűjtenek a Azure Migrate berendezés?

A [Azure Migrate dokumentációjában](https://docs.microsoft.com/azure/migrate/migrate-appliance#collected-performance-data-vmware)megtekintheti az Azure Migrate készülék által összegyűjtött adatok részleteit.

### <a name="is-there-any-performance-impact-on-the-analyzed-vmware-or-hyper-v-environment"></a>Hatással van-e az elemzett VMware vagy Hyper-V környezet teljesítményére?

A Azure Migrate berendezés a helyszíni gépeket folyamatosan a virtuális gép teljesítményadatait méri. Ez a profilkészítés szinte semmilyen hatással nincs a Hyper-V/ESXi-gazdagépekre vagy vCenter Serverra.

### <a name="where-is-the-collected-data-stored-and-for-how-long"></a>Hol tárolódnak az összegyűjtött adatok, és mennyi ideig?

Az Azure Migrate készülék által gyűjtött adatokat az Azure-beli helyen tárolja, amelyet az áttelepítési projekt létrehozásakor választott ki. Az adattárolást a Microsoft-előfizetések biztonságosan tárolják, és a Azure Migrate-projekt törlésekor törlődnek.

A függőségi vizualizációk esetében, ha ügynököket telepít a virtuális gépekre, a függőségi ügynökök által gyűjtött adatokat az Egyesült Államokban tárolja az Azure-előfizetésben létrehozott Log Analytics munkaterületen. Ezek az adatai törlődnek, amikor törli a Log Analytics munkaterületet az előfizetésében. További információ: [függőségi vizualizáció](concepts-dependency-visualization.md).

### <a name="what-volume-of-data-is-uploaded-by-the-azure-migrate-appliance-during-continuous-profiling"></a>Milyen adatmennyiséget töltenek fel a Azure Migrate berendezés a folyamatos profilkészítés során?

A Azure Migrate elküldett adatok mennyisége több paramétertől függ. A kötet értelmezéséhez: egy Azure Migrate-projekt 10 géppel (mindkettő egy lemezzel és egy hálózati adapterrel) körülbelül 50 MB-ot küld naponta. Ez az érték egy közelítés. A hálózati adapterek és lemezek adatpontjainak száma alapján változik. (A továbbított adatmennyiség növekedése nem lineáris, ha a gépek, hálózati adapterek vagy lemezek száma növekszik.)

### <a name="is-the-data-encrypted-at-rest-and-in-transit"></a>Titkosítva vannak-e az adatok a nyugalmi állapotban és az átvitel során?

Igen, mindkettő. A metaadatokat a rendszer biztonságosan továbbítja a Azure Migrate szolgáltatásnak az interneten keresztül, HTTPS-kapcsolaton keresztül. A metaadatok egy [Azure Cosmos db](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest) adatbázisban és az [Azure Blob Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) -ban tárolódnak egy Microsoft-előfizetésben. A metaadatok titkosítva vannak a nyugalmi állapotban.

A függőségi ügynökök által gyűjtött adatok az átvitel során is titkosítva vannak (biztonságos HTTPS). Ez az előfizetés egy Log Analytics munkaterületén van tárolva. Emellett a szolgáltatás inaktív állapotban is titkosítva van.

### <a name="how-does-the-azure-migrate-appliance-communicate-with-vcenter-server-and-the-azure-migrate-service"></a>Hogyan kommunikál a Azure Migrate készülék a vCenter Server és a Azure Migrate szolgáltatással?

A berendezés a berendezés beállításakor megadott hitelesítő adatok használatával csatlakozik a vCenter Serverhoz (443-es port). A VMware PowerCLI használatával kérdezi le vCenter Server, hogy metaadatokat gyűjtsön a vCenter Server által felügyelt virtuális gépekről. Konfigurációs adatokat gyűjt a virtuális gépekről (magok, memória, lemezek, hálózati adapterek stb.), valamint az összes virtuális gép teljesítménybeli előzményeiről az elmúlt hónapban. Ezután az összegyűjtött metaadatokat a rendszer Azure Migrate kiszolgáló-felmérésre (az interneten keresztül HTTPS-kapcsolaton keresztül) elküldi az értékeléshez.

### <a name="can-i-connect-the-same-appliance-to-multiple-vcenter-server-instances"></a>Több vCenter Server példányhoz is csatlakozhatok ugyanahhoz a berendezéshez?

Nem. A készülék és a vCenter Server között egy-az-egyhez típusú hozzárendelés található. Ha több vCenter Server példányon kell felderíteni a virtuális gépeket, több berendezést kell telepítenie.


### <a name="i-changed-my-machine-size-can-i-run-the-assessment-again"></a>Módosítottam a gép méretét. Futtathatom újra az értékelést?

A Azure Migrate berendezés folyamatosan gyűjt információkat a helyszíni környezetről. Az értékelés azonban a helyszíni virtuális gépekre vonatkozó időponthoz kapcsolódó pillanatkép. Ha módosítja egy olyan virtuális gép beállításait, amelyet fel szeretne mérni, használja az újraszámítási lehetőséget az értékelés frissítéséhez a legújabb módosításokkal.

### <a name="how-can-i-perform-discovery-in-a-multitenant-environment-in-azure-migrate-server-assessment"></a>Hogyan végezhető el a felderítés több-bérlős környezetben Azure Migrate Server Assessment szolgáltatásban?

A VMware esetében, ha olyan környezettel rendelkezik, amely a bérlők között van elosztva, és nem szeretné felderíteni egy másik bérlő előfizetésében lévő virtuális gépeket, hozzon létre vCenter Server hitelesítő adatokat, amelyek csak a felderíteni kívánt virtuális gépekhez férhetnek hozzá. Ezután használja ezeket a hitelesítő adatokat, amikor elindítja a felderítést a Azure Migrate berendezésben.

A Hyper-V esetében a felderítés a Hyper-V gazdagép hitelesítő adatait használja. Ha a virtuális gépek ugyanazt a Hyper-V-gazdagépet használják, jelenleg nincs lehetőség a felderítés elválasztására.  

### <a name="how-many-vms-can-i-discover-with-a-single-migration-appliance"></a>Hány virtuális gépet lehet felderíteni egyetlen áttelepítési berendezéssel?

Akár 10 000 VMware virtuális gépet és akár 5 000 Hyper-V virtuális gépet is felfedezhet egyetlen áttelepítési berendezéssel. Ha több géppel rendelkezik a helyszíni környezetben, Ismerje meg, hogyan méretezheti a [Hyper-V](scale-hyper-v-assessment.md) és a [VMware](scale-vmware-assessment.md) értékelését.

### <a name="can-i-delete-the-azure-migrate-appliance-from-the-project"></a>Törölhetem a Azure Migrate készüléket a projektből?
A készülék jelenleg nem törlődik a projektből. A készülék törlésének egyetlen módja, ha törli a Azure Migrate projektet tartalmazó erőforráscsoportot, amely a berendezéshez van társítva, de más regisztrált berendezéseket is töröl, a felderített leltárt, az értékeléseket és az összes többi Azure-összetevőt. az erőforráscsoport projekthez van társítva.

## <a name="azure-migrate-server-assessment"></a>Azure Migrate kiszolgáló értékelése

### <a name="does-azure-migrate-server-assessment-support-assessment-of-physical-servers"></a>A Azure Migrate Server Assessment támogatja a fizikai kiszolgálók értékelését?

Nem, Azure Migrate jelenleg nem támogatja a fizikai kiszolgálók értékelését.

### <a name="does-azure-migrate-need-vcenter-server-to-perform-discovery-in-a-vmware-environment"></a>Szükség van Azure Migrate vCenter Server a felderítést a VMware-környezetben?

Igen, Azure Migrate a felderítést a VMware-környezetben vCenter Server kell végrehajtania. Nem támogatja az vCenter Server által nem felügyelt ESXi-gazdagépek felderítését.

### <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Mi a különbség a Azure Migrate Server Assessment és a MAP Toolkit között?

A Azure Migrate Server Assessment áttelepítési értékelést biztosít az áttelepítés készültségének biztosításához, valamint az Azure-ba való áttelepítéshez szükséges munkaterhelések kiértékeléséhez. A [Microsoft felmérési és tervezési (MAP) eszközkészlete](https://www.microsoft.com/download/details.aspx?id=7826) más feladatokkal, például a Windows-ügyfél és a kiszolgálói operációs rendszerek újabb verzióinak, illetve a szoftverek használatának nyomon követésének megtervezésében nyújt segítséget. Ilyen esetekben folytassa a MAP Toolkit használatát.

### <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-azure-site-recovery-deployment-planner"></a>Mi a különbség a Azure Migrate Server Assessment és a Azure Site Recovery Deployment Planner között?

Azure Migrate Server Assessment egy áttelepítési tervezési eszköz. A Azure Site Recovery Deployment Planner vész-helyreállítási tervezési eszköz.

**Áttelepítés VMware/Hyper-V-ről az Azure-** ba: Ha a helyszíni kiszolgálókat az Azure-ba szeretné áttelepíteni, használja a Azure Migrate kiszolgáló-értékelést az áttelepítés megtervezéséhez. Felméri a helyszíni számítási feladatokat, és útmutatást, megállapításokat és eszközöket biztosít a kiszolgálók Azure-ba való migrálása érdekében. Miután elkészült az áttelepítési tervvel, használhat olyan eszközöket, mint a Azure Migrate-kiszolgáló áttelepítése a gépek Azure-ba való áttelepítéséhez.

Vész- **helyreállítás a VMware/Hyper-V-ről az Azure-** ba: Az Azure-ban Site Recoveryon keresztüli vész-helyreállításhoz használja a Site Recovery Deployment Planner a tervezéshez. Site Recovery Deployment Planner a helyszíni környezet alapos, Site Recovery-specifikus értékelését végzi el. Ajánlásokat tesz Site Recovery számára a sikeres vészhelyzeti műveletekhez, például a virtuális gépek replikálásához és feladatátvételéhez.

### <a name="does-azure-migrate-support-cost-estimation-for-the-enterprise-agreement-ea-program"></a>Azure Migrate támogatja a Nagyvállalati Szerződés (EA) programra vonatkozó költségbecslés összegét?

A Azure Migrate jelenleg nem támogatja a [nagyvállalati szerződés programhoz](https://azure.microsoft.com/offers/enterprise-agreement-support/)tartozó költségbecslés használatát. A megkerülő megoldás az **ajánlat** utólagos **elszámolású** módja, és az értékelési tulajdonságok **kedvezmény** mezőjében manuálisan megadhatja az engedmény százalékos arányát (az előfizetésre érvényes):

  ![Értékelés tulajdonságai](./media/resources-faq/discount.png)

### <a name="whats-the-difference-between-as-on-premises-sizing-and-performance-based-sizing"></a>Mi a különbség a helyszíni méretezés és a teljesítmény-alapú méretezés között?

A helyszíni méretezéssel a Azure Migrate nem veszi figyelembe a virtuális gépek teljesítményadatait. A virtuális gépeket a helyszíni konfiguráció alapján méretezi. A teljesítmény-alapú méretezéssel a méretezés a kihasználtsági adatain alapul.

Vegyünk például egy olyan helyszíni virtuális gépet, amely 4 maggal és 8 GB memóriával rendelkezik, 50%-os CPU-kihasználtság és 50% memória kihasználtsága esetén. Ehhez a virtuális géphez a helyszíni méretezés azt ajánlja, hogy egy Azure-beli virtuális gép SKU-jának négy maggal és 8 GB memóriával rendelkezik. A teljesítmény-alapú méretezés olyan virtuálisgép-SKU-t javasol, amely két maggal és 4 GB memóriával rendelkezik, mert a kihasználtság százalékos arányát veszi figyelembe.

Hasonlóképpen, a lemez méretezése két értékelési tulajdonságtól függ: a méretezési feltételektől és a tárolási típustól. Ha a méretezési feltétel teljesítmény-alapú, és a tárolási típus automatikus, Azure Migrate veszi figyelembe a lemez IOPS és átviteli értékeit, amikor azonosítja a céllemez típusát (standard vagy prémium).

Ha a méretezési feltétel teljesítmény-alapú, és a tárolási típus prémium, Azure Migrate a prémium szintű lemezt javasolja. A prémium szintű lemez SKU a helyszíni lemez mérete alapján van kiválasztva. Ugyanez a logikát használja a lemezek méretezésére, ha a méretezési feltétel a helyszíni méretezés, és a tárolási típus a standard vagy a prémium.

### <a name="what-impact-does-performance-history-and-utilization-percentile-have-on-size-recommendations"></a>Milyen hatással van a teljesítmény-előzmények és a kihasználtsági arány a méretre vonatkozó javaslatokkal?

Ez a tulajdonságok csak a teljesítményalapú méretezés esetén számítanak.

Azure Migrate gyűjti a helyszíni gépek teljesítményének előzményeit, és azt használja a virtuális gép méretének és a lemez típusának az Azure-ban való használatát.

A készülék folyamatosan a helyszíni környezetet gyűjti a valós idejű kihasználtsági adatok gyűjtésére 20 másodpercenként. A készülék összesíti a 20 másodperces mintákat, és minden 15 percenként létrehoz egyetlen adatpontot. Az adatpont létrehozásához a berendezés a 20 másodperces mintákból kiválasztja a csúcsérték értékét. A készülék elküldi ezt az adatpontot az Azure-nak.

Ha az Azure-ban hoz létre értékelést (a teljesítmény időtartama és a teljesítményi előzmények percentilis értéke alapján), Azure Migrate kiszámítja a tényleges kihasználtság értékét, és azt használja a méretezéshez.

Tegyük fel például, hogy a teljesítmény időtartamát egy napra és a percentilis értékét 95 percentilis értékre állítja. Azure Migrate rendezi a gyűjtő által az elmúlt nap során eljuttatott 15 perces mintát növekvő sorrendbe, és a 95. percentilis értékét a tényleges kihasználtság értékre választja.

A 95. percentilis értéke biztosítja a kiugró értékek figyelmen kívül hagyásával. A kiugró értékek a esetek 99% percentilis használata esetén szerepelhetnek. Ha ki szeretné választani az időszak maximális kihasználtságát, és nem szeretne kiugró értékeket kihagyni, válassza ki a esetek 99% percentilis értéket.

### <a name="what-is-dependency-visualization"></a>Mi a függőségi vizualizáció?

A függőségek vizualizációja lehetővé teszi, hogy a nagyobb megbízhatósággal rendelkező virtuális gépek csoportjait mérje fel áttelepítésre. Az értékelés futtatása előtt ellenőrzi a gépek függőségeit. A függőségi vizualizáció segít biztosítani, hogy a váratlan kimaradások elkerülése érdekében ne maradjon hátra az Azure-ba való Migrálás során. Azure Migrate a függőségi vizualizáció engedélyezéséhez a Azure Monitor-naplók Service Map megoldását használja.

> [!NOTE]
> A függőségi vizualizáció nem érhető el Azure Governmentban.

### <a name="do-i-need-to-pay-to-use-dependency-visualization"></a>Fizetnem kell a függőségi vizualizáció használatához?

Nem. További információ: [Azure Migrate díjszabása](https://azure.microsoft.com/pricing/details/azure-migrate/).

### <a name="do-i-need-to-install-anything-for-dependency-visualization"></a>Telepíteni kell bármit a függőségi vizualizációhoz?

A függőségi vizualizáció használatához le kell töltenie és telepítenie kell az ügynököket minden olyan helyszíni gépen, amelyet ki szeretne értékelni.

A következő ügynököket kell telepítenie mindegyik gépre:
- [Microsoft monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).
- [Függőségi ügynök](../azure-monitor/platform/agents-overview.md#dependency-agent).
- Ha olyan gépekkel rendelkezik, amelyeknek nincs internetkapcsolata, le kell töltenie és telepítenie kell Log Analytics-átjárót.

Ezek az ügynökök csak akkor szükségesek, ha függőségi vizualizációt használ.

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>Használhatok egy meglévő munkaterületet a függőségi vizualizációhoz?

Igen, csatlakoztathat egy meglévő munkaterületet az áttelepítési projekthez, és használhatja azt a függőségi vizualizációhoz. További információ: "hogyan működik ez a funkció" a [függőségi vizualizációban](concepts-dependency-visualization.md#how-does-it-work) .

### <a name="can-i-export-the-dependency-visualization-report"></a>Exportálhatók a függőségi vizualizációs jelentés?

Nem, a függőségi vizualizációt nem lehet exportálni. Mivel azonban a Azure Migrate Service Map használ a függőségi vizualizációhoz, a [Service Map REST API](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) a függőségek JSON formátumban való lekéréséhez használhatja.

### <a name="how-can-i-automate-the-installation-of-microsoft-monitoring-agent-mma-and-the-dependency-agent"></a>Hogyan automatizálható a Microsoft monitoring Agent (MMA) és a függőségi ügynök telepítése?

Ezzel [a parancsfájllal telepítheti a függőségi ügynököt](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples). Kövesse ezeket az [utasításokat az MMA telepítéséhez](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) a parancssor vagy az Automation használatával. Az MMA esetében használja [ezt a parancsfájlt](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).

A parancsfájlok mellett olyan központi telepítési eszközöket is használhat, mint a System Center Configuration Manager és a [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) az ügynökök üzembe helyezéséhez.

### <a name="what-operating-systems-are-supported-by-mma"></a>Milyen operációs rendszereket támogat az MMA?

- Tekintse meg az [MMA által támogatott Windows operációs rendszerek](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)listáját.
- Megtekintheti az [MMA által támogatott Linux operációs rendszerek](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems)listáját.

### <a name="what-operating-systems-are-supported-by-the-dependency-agent"></a>Milyen operációs rendszereket támogat a függőségi ügynök?

A [Azure monitor for VMS által támogatott Windows-és Linux-operációs rendszerek](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems)listájának megtekintése.

### <a name="can-i-visualize-dependencies-in-azure-migrate-for-more-than-an-hour"></a>Megjeleníthetem a függőségeket Azure Migrateban több mint egy óráig?
Nem. A függőségeket akár egy óráig is megjelenítheti. Visszatérhet egy adott dátumhoz az előzményekben, egy hónapra visszamenőleg, de a vizualizációk maximális időtartama egy óra. Használhatja például az időtartamot a függőségi térképen a tegnapi függőségek megtekintéséhez, de csak egyórás időszakra lehet megtekinteni. A [függőségi adat](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) hosszabb időtartamon keresztül történő lekérdezéséhez azonban Azure monitor naplókat is használhat.

### <a name="can-i-use-dependency-visualization-for-groups-that-contain-more-than-10-vms"></a>Használhatok függőségi vizualizációt a több mint 10 virtuális gépet tartalmazó csoportok esetén?
Megjelenítheti a legfeljebb 10 virtuális gépet tartalmazó [csoportok függőségeit](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) . Ha több mint 10 virtuális géppel rendelkező csoporttal rendelkezik, javasoljuk, hogy ossza szét a csoportot kisebb csoportokba, majd jelenítse meg a függőségeket.

## <a name="azure-migrate-server-migration"></a>Azure Migrate kiszolgáló áttelepítése

### <a name="whats-the-difference-between-azure-migrate-server-migration-and-azure-site-recovery"></a>Mi a különbség a Azure Migrate kiszolgáló áttelepítése és Azure Site Recovery között?

Azure Migrate a kiszolgáló áttelepítése a VMware virtuális gépek ügynök-alapú áttelepítéséhez, a Hyper-V virtuális gépek áttelepítéséhez és a fizikai kiszolgálók Azure-ba való áttelepítéséhez a Site Recovery replikációs motort használja. A VMware virtuális gépek áttelepítésére szolgáló ügynök nélküli beállítás natív módon van beépítve a kiszolgáló áttelepítésére.

## <a name="next-steps"></a>További lépések
Olvassa el a [Azure Migrate áttekintést](migrate-services-overview.md).

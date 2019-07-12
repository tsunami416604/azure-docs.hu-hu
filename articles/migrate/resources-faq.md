---
title: Az Azure Migrate – gyakori kérdések (GYIK) |} A Microsoft Docs
description: A címek az Azure Migrate kapcsolatos gyakori kérdések
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: snehaa
ms.openlocfilehash: 08a0312f12b3daab8b7f5e88da118b5bcbeb2f4c
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807328"
---
# <a name="azure-migrate---frequently-asked-questions-faq"></a>Az Azure Migrate – gyakori kérdések (GYIK)

Ez a cikk az Azure Migrate – gyakori kérdések tartalmazza. Ha ez a cikk elolvasása után további lekérdezéseket, tegye azt közzé a a [Azure Migrate fórum](https://aka.ms/AzureMigrateForum).

## <a name="general"></a>Általános

### <a name="which-azure-geographies-are-supported-by-azure-migrate"></a>Melyik Azure-régiócsoportok támogat az Azure Migrate?
Az Azure Migrate jelenleg támogatja a több régióban, amelyben az Azure Migrate-projektet is létrehozható. Annak ellenére, hogy ezek földrajzi területeken csak projekteket hozhat létre, másik célhelyet a gépek értékelését ettől függetlenül is. A projekt földrajzi csak használatos felderített metaadatok tárolására.

**Földrajzi hely** | **metaadat-tároló helyét** Azure Government |} US Gov Virginia Ázsia |} Délkelet-Ázsia, Kelet-Ázsia, Európa vagy |} Dél-Európában és Nyugat-Európa, Egyesült Királyság |} Egyesült Királyság déli régiója, Egyesült Királyság nyugati Egyesült Államok vagy |} USA középső RÉGIÓJA és USA 2. nyugati

### <a name="how-is-azure-migrate-different-from-azure-site-recovery"></a>Miben különbözik az Azure Migrate az Azure Site Recovery?

Az Azure Migrate olyan eszközöket biztosít segít felderíteni, felmérési és gépeket és számítási feladatok migrálása az Azure-bA. [Az Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure) van egy vész-helyreállítási megoldást. Mindkét szolgáltatás ossza meg néhány összetevőt.

## <a name="azure-migrate-appliance-vmwarephysical-servers"></a>Az Azure Migrate készülék (VMware-ről/fizikai kiszolgálók)

### <a name="how-does-the-azure-migrate-appliance-connect-to-azure"></a>Hogyan csatlakozhat az Azure Migrate készülék az Azure?

A kapcsolat lehet az interneten keresztül, vagy használhatja az ExpressRoute nyilvános társviszony-létesítés.

### <a name="what-network-connectivity-requirements-are-needed-for-azure-migrate-server-assessment-and-migration"></a>Az Azure Migrate kiszolgáló elemzésekhez és Migráláshoz szükséges milyen hálózati kapcsolatokra vonatkozó követelményeket

Az URL-címeket és portokat használ az Azure Migrate való kommunikációhoz az Azure-ral, tekintse át a [VMWare](migrate-support-matrix-vmware.md) és [Hyper-V](migrate-support-matrix-hyper-v.md) mátrixok támogatja.

### <a name="can-i-harden-the-appliance-vmware-vm-i-set-up-with-the-ova-template"></a>A berendezés az OVA-sablon beállítása VMware virtuális gép is felvértezni?

További összetevőket (például víruskereső) is hozzáadhatók az OVA sablonba, mindaddig, amíg a kommunikációs és a tűzfal szabályok re balra, az Azure Migrate berendezés szükséges van.   

### <a name="to-harden-the-azure-migrate-appliance-what-are-the-recommended-antivirus-av-exclusions"></a>Az Azure Migrate-készülék, Mik azok a víruskereső (Víruskereső) ajánlott kivételeket mobileszközcsoportra?

A következő mappák kizárása vizsgálatának a készüléken kell megadnia:

- Az Azure Migrate szolgáltatás bináris fájlokat tartalmazó mappa. Zárja ki az összes alárendelt mappát.
- %ProgramFiles%\ProfilerService  
- Az Azure Migrate webes alkalmazást. Zárja ki az összes alárendelt mappát.
- %SystemDrive%\inetpub\wwwroot
- A helyi gyorsítótár az adatbázis és naplófájlok. Az Azure Migrate szolgáltatás kell olvasási/írási hozzáféréssel ehhez a mappához.
  - %SystemDrive%\Profiler

### <a name="what-data-is-collected-by-azure-migrate"></a>Azure Migrate által gyűjtött adatokat?

Az Azure Migrate készülék metaadatait gyűjti a helyszíni virtuális gépek, többek között:

**A virtuális gép konfigurációs adatok**
- Virtuális gép megjelenített neve (a vCenter)
- Virtuális gép szoftverleltár elérési útja (gazdagép/fürt/mappa vCenter)
- IP-cím
- MAC-cím
- Operációs rendszer
- Magok, lemezek, hálózati adapterek száma
- Memória mérete, a lemezméretek

**A virtuális gép teljesítményadatok**
- CPU-használat
- Memóriahasználat
- A virtuális géphez csatlakoztatott összes lemez:
  - Lemez sebessége olvasott átviteli sebesség
  - Lemezre írási műveletek átviteli sebesség
  - Lemezolvasási művelet / mp-ben
  - Lemezre írási műveletek / mp-ben
- Az összes virtuális Géphez csatlakoztatott hálózati adapter:
  - A hálózati
  - Kimenő hálózati forgalom

Emellett TN függőségi leképezés telepít a függőségi leképezés ügynökök gyűjt adatokat tartalmazó számítógép teljes Tartománynevét, operációs rendszer, IP-cím, a MAC-címet, a virtuális gép, és a bejövő/kimenő TCP-kapcsolatok a virtuális gép futó folyamatok. Ez a felderítés nem kötelező, csak akkor használható, ha engedélyezi a függőségi leképezés a felderítéshez.

### <a name="is-there-any-performance-impact-on-the-analyzed-esxi-host-environment"></a>Van bármilyen teljesítményre gyakorolt hatás a elemzett ESXi-gazdagép környezetet?

A teljesítményadatok folyamatos profilkészítési, a készülék az Azure Migrate profilokat a helyszíni gépek és mérhető, virtuális gép teljesítményadatait. Ez a szinte nullára teljesítmény hatással van, az ESXi-gazdagépek, valamint a vCenter-kiszolgálóhoz.

### <a name="where-is-the-collected-data-stored-and-for-how-long"></a>Hol található az összegyűjtött adatok tárolt és mennyi ideig?

Az Azure Migrate-berendezés által gyűjtött adatok az Azure-beli helyen, amely a migrálási projekt létrehozásakor megadott tárolódik. Az adatok tárolása biztonságos egy adott Microsoft-előfizetés, és az Azure Migrate-projekt törlésekor törlődik.

A függőségek képi megjelenítéséről a virtuális gépeket, telepíti a védelmi ügynököt a függőségi ügynökök által gyűjtött adatok tárolódnak a az Egyesült Államok, az Azure-előfizetésben létrehozott Log Analytics-munkaterületet. Ezeket az adatokat a Log Analytics-munkaterületet az előfizetésben való törlésekor törlődik. [További információk](concepts-dependency-visualization.md).

### <a name="what-is-the-volume-of-data-uploaded-by-azure-migrate-during-continuous-profiling"></a>Mi a folyamatos profilkészítés során az Azure Migrate által feltöltött adatok mennyiségét?

Az Azure Migrate küldött adatmennyiség függvényében több paramétert. Adjon meg egy tájékoztató számot, az Azure Migrate-projektet (amelyek mindegyike egy lemezt és egy hálózati adapter), 10 gépekkel körülbelül 50 MB / nap küld. Ez a becsült érték, módosítások alapuló adatpontok számát, a hálózati adapterek és a lemezek (elküldött adatokat a nem lineáris Ha a gépeken, hálózati adapterekhez vagy lemezek száma).

### <a name="is-the-data-encrypted-at-rest-and-in-transit"></a>A titkosított adatok inaktív és átvitel?

Igen is. A metaadatok biztonságosan, https-kapcsolaton keresztül az interneten keresztül elküldi az Azure Migrate szolgáltatásnak. A metaadatokat tárolja egy [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest), majd a [az Azure blob storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) egy adott Microsoft-előfizetés és titkosított inaktív.

A függőségi ügynökök által gyűjtött adatok is a titkosított átvitel (biztonságos HTTPS), és a felhasználói előfizetés a Log Analytics-munkaterület van tárolva. Emellett akkor is titkosított inaktív.

### <a name="how-does-the-azure-migrate-appliance-communicate-with-the-vcenter-server-and-the-azure-migrate-service"></a>Az Azure Migrate készülék miként nem kommunikál a vCenter-kiszolgáló és az Azure Migrate szolgáltatás?

A berendezés csatlakozik a vCenter-kiszolgáló (a 443-as port) a készülék beállításakor megadott hitelesítő adatokkal. Lekérdezi a vCenter-kiszolgáló használatával VMware powercli-t, a vCenter-kiszolgáló által kezelt virtuális gépek metaadatait gyűjtéséhez. Azt mindkét konfigurációs adatokat gyűjti össze a virtuális gépek (magok, memória, lemezek, hálózati adapter stb.), és minden virtuális gép a legutóbbi hónap teljesítményelőzményeinek. Az összegyűjtött metaadatok majd küld az Azure Migrate Server Assessment (az interneten keresztül HTTPS) az értékeléshez. 

### <a name="can-i-connect-the-same-appliance-to-multiple-vcenter-servers"></a>Csatlakozhatok az azonos készülék több vCenter-kiszolgáló?

Igen, egy egyetlen Azure Migrate készülék használata több vCenter-kiszolgálók felderítéséhez, de nem egy időben. Egymás után felderítések futtatásához szükséges.

### <a name="is-the-ova-template-used-by-site-recovery-integrated-with-the-ova-used-by-azure-migrate"></a>A Site Recovery által használt OVA sablon integrálva van az Azure Migrate által használt OVA?

Jelenleg nincs nincs integráció. A. A Site Recovery OVA-sablon beállítása a Site Recovery konfigurációs kiszolgálónak a VMware virtuális gépek/fizikai kiszolgáló replikációjához szolgál. A. Az Azure Migrate által használt OVA elemzésekhez és migráláshoz az alkalmazásában egy vCenter-kiszolgáló által felügyelt VMware virtuális gépek felderítésére szolgál.

### <a name="i-changed-my-machine-size-can-i-rerun-an-assessment"></a>Módosítottam a saját gép méretét. Futtathatja egy értékelés?
Az Azure Migrate készülék folyamatosan gyűjt adatokat a helyszíni környezetben. Az értékelés azonban csak a helyszíni virtuális gépek időponthoz pillanatképet. Ha módosítja az értékelni kívánt virtuális gép beállításait, a "Újraszámítása" lehetőség használatával az értékelés frissítése a legújabb módosításokkal.

### <a name="how-can-i-discover-a-multi-tenant-environment-in-azure-migrate"></a>Hogyan képes felderíteni egy több-bérlős környezetben az Azure Migrate?

VMware-ről Ha a bérlők között megosztott-környezettel rendelkezik, és nem kívánja a bérlő egy másik előfizetésben egy bérlő virtuális gépek felderítéséhez, hozzon létre vCenter hitelesítő adatait csak a felderíteni kívánt virtuális gépeken való hozzáférés. Ezután használja a hitelesítő adatokat, ha az Azure Migrate készülék a felderítés megkezdése.

A Hyper-V, a felderítés használja a Hyper-V gazdagép hitelesítő adatokat, ha a virtuális gépek ugyanazon a Hyper-V gazdagépen, jelenleg nem semmilyen módon nem lehet a felderítés külön.  

### <a name="how-many-vms-can-be-discovered-using-a-single-migration-appliance"></a>Hány virtuális gépet is deríthetők a migrálást berendezés használata?

Felfedezheti, hogy legfeljebb 10 000 VMware virtuális gépek és legfeljebb 5000 Hyper-V virtuális gépek migrálást berendezés használata.  Ha több gépet a helyszíni környezetben, megtudhatja, hogyan skálázhatja [Hyper-V](scale-hyper-v-assessment.md) és [VMware](scale-vmware-assessment.md) értékelés.


## <a name="azure-migrate-server-assessment"></a>Az Azure Migrate: Server Assessment

### <a name="does-azure-migrate-server-assessment-support-assessment-of-physical-servers"></a>Az Azure Migrate nem: Server Assessment támogatja a fizikai kiszolgálók?

Nem, az Azure Migrate jelenleg nem támogatja fizikai kiszolgálók értékelését. 

### <a name="does-azure-migrate-need-vcenter-server-to-discover-a-vmware-environment"></a>Nem kell az Azure Migrate a vCenter-kiszolgáló felderítése a VMware-környezet?

Igen, az Azure Migrate kell vCenter-kiszolgáló egy VMware-környezet felderítéséhez. Nem támogatja a nem a vCenter-kiszolgáló által kezelt ESXi-gazdagépek felderítését.

### <a name="whats-the-difference-between-using-azure-migrate-server-assessment-and-the-map-toolkit"></a>Mi a különbség az Azure Migrate szolgáltatással: Server Assessment és a Map eszközkészlet?

Az Azure Migrate: Server migrálási felmérést való migrálás kompatibilitásáról és a számítási feladatok Azure-ba való migrálásának értékelése szolgál. [A Microsoft Assessment és tervezés (Leképezés) eszközkészlet](https://www.microsoft.com/download/details.aspx?id=7826) rendelkezik egyéb funkciókat, például a migrálási tervezés újabb verzióiban a Windows ügyfél és kiszolgáló operációs rendszerek és szoftverek használat nyomon követése. Ezek a forgatókönyvek esetén továbbra is használhatja a MAP eszközkészlet.

### <a name="how-is-azure-migrate-server-assessment-different-from-azure-site-recovery-deployment-planner"></a>Hogyan történik az Azure Migrate: Server Assessment különbözik az Azure Site Recovery Deployment Planner?

Az Azure Migrate: Server Assessment eszközt áttelepítés. Az Azure Site Recovery Deployment Planner eszközt vész-helyreállítási.

- **Az Azure-ba történő VMware vagy Hyper-V áttelepítés**: Ha szeretne áttelepíteni a helyszíni kiszolgálók Azure-ba, az Azure Migrate használata: Server Assessment eszközt, az áttelepítés tervezéséhez. Az eszköz a helyszíni számítási feladatokat értékeli, és útmutatást, insights, és segítséget nyújtanak az Azure-ba való migrálás mechanizmusokat biztosít. Miután elkészült a migrálási terv, eszközök, például az Azure Migrate is használhatja: A gépek áttelepítése az Azure-kiszolgáló áttelepítését.
- **Az Azure-ba történő VMware vagy Hyper-V vész-helyreállítási**: Az Azure Site Recovery használata vész-helyreállítási használja a Site Recovery Deployment Planner a vészhelyreállítási tervek. A Site Recovery Deployment Planner hajtja végre a helyszíni környezet mélyebb, Site Recovery-specifikus értékelése. A sikeres vészhelyreállítási műveleteket, mint a replikáció és a virtuális gépek feladatátvétele a Site Recovery által igényelt javaslatokat nyújt. 

### <a name="does-azure-migrate-support-enterprise-agreement-ea-based-cost-estimation"></a>Az Azure Migrate támogatja Nagyvállalati szerződés-alapú költségbecslési?

Az Azure Migrate jelenleg nem támogatja a költségbecslést [ajánlat nagyvállalati ügyfeleknek](https://azure.microsoft.com/offers/enterprise-agreement-support/). A megoldás, hogy adja meg a használatalapú fizetéssel az ajánlatra, és adja meg manuálisan az értékelésben "Kedvezményt" mezőjében (az előfizetésre vonatkozó) százalékos kedvezmény.

  ![Kedvezmény](./media/resources-faq/discount.png)

### <a name="whats-the-difference-between-as-on-premises-sizing-and-performance-based-sizing"></a>Mi a különbség a között, helyszíni méretezése és a teljesítményalapú méretezéshez?

- A helyszíni méretezéshez az Azure Migrate nem érdemes virtuális gép teljesítményadatait. A helyszíni konfiguráció alapján a virtuális gépek méreteit azt. -A teljesítményalapú méretezés méretezési kihasználtsági adatok alapul.
- Például ha egy helyszíni virtuális gépen, 4 mag, és 8 GB memóriát, valamint 50 %-os CPU-kihasználtság és 50 %-a memóriahasználat, mint a helyszíni méretezési javasolja-4 mag, 8GB memóriával rendelkező Azure-beli VM Termékváltozata. Teljesítményalapú méretezést, azonban azt javasolja, 2 magos virtuális Gépet a Termékváltozat és a 4 GB, mert a kihasználtsági százaléka tekinthető.
- Lemez méretezési hasonlóan két értékelésben - feltétel és a tároló méretezése függ.
= Ha a méretezési feltétel teljesítményalapú és tárolási típus automatikus, a lemez IOPS és átviteli sebesség értékét minősülnek, amikor meg kell állapítani a céllemez típusa (Standard vagy prémium).
- Ha a méretezési feltétel teljesítményalapú pedig az a tárolótípus prémium szintű, ajánlott prémium szintű lemezt. A kiválasztott Termékváltozat prémium szintű lemezt a helyi lemez mérete alapján. Ugyanazt a logikát lemezes méretezési, ha a méretezési feltétel teljesítményalapú, helyszíni méretezésnél, és a tároló típusa standard vagy prémium szintű szolgál.

### <a name="what-impact-does-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>Milyen hatással van a teljesítmény előzmények és PERCENTILIS kihasználtságát a javaslatok a méretekkel kapcsolatban?

Ez a tulajdonságok csak a teljesítményalapú méretezés esetén számítanak.

- Az Azure Migrate a helyszíni gépek korábbi teljesítménye gyűjt, és annak használatával az Azure-beli virtuális gép méretét és a lemez típusa javasoljuk.
- A berendezés folyamatosan profilokat a helyszíni környezetben, valós idejű használati adatok gyűjtéséhez 20 másodpercenként. A berendezés összesíti a 20 másodpercenként vett mintákat, és egyetlen adatpontot hoz létre minden 15 percről. A berendezés úgy hoz létre egyetlen adatpontot, hogy kiválasztja minden 20 másodperces minta csúcsértékét, amelyet elküld az Azure-nak.
- Értékelés (a teljesítmény időtartamát és a teljesítmény előzmények PERCENTILIS értéke alapján) Azure-ban való létrehozásakor az Azure Migrate a hatékony kihasználtsági értéket számít, és méretezéshez használja.
- Például ha egy nap, hogy a teljesítmény időtartamát, és a 95. százalékos érték, az Azure Migrate PERCENTILIS értéket használ a 15 perces minta pontok utolsó napjára-gyűjtő által küldött, növekvő sorrendbe rendezi, és mint 95 % választja ki a hatékony kihasználtságát.
- A 95. percentilis biztosítja, amelyek előfordulhatnak, ha 99 % kiugró adatokat figyelmen kívül. Ha az időtartam használati csúcsát szeretné kiválasztani, és nem szeretne lemaradni egyetlen kiugró adatról sem, a 99. percentilist válassza.

### <a name="what-is-dependency-visualization"></a>Mit jelent a függőségek képi megjelenítésének?

Függőségek képi megjelenítésének segítségével felmérheti a migrálásra virtuális gépek csoportját nagyobb biztonsággal. Azt cross-ellenőrzések gépfüggőségek értékelés futtatása előtt. Függőségek képi megjelenítésének segítségével, győződjön meg arról, hogy semmi sem marad, és elkerülheti a váratlan leállások, az Azure-ba való áttelepítésekor. Az Azure Migrate a Service Map megoldás az Azure Monitor naplókat, a függőségek képi megjelenítésének engedélyezése használja.

> [!NOTE]
> A függőségek képi megjelenítésének funkcióival nem érhető el az Azure Government szolgáltatásban.

### <a name="do-i-need-to-pay-to-use-the-dependency-visualization-feature"></a>Kell fizetnie a függőségmegjelenítési funkciót használja?

Nem. [Itt talál további információkat](https://azure.microsoft.com/pricing/details/azure-migrate/) az Azure Migrate díjszabásáról.

### <a name="do-i-need-to-install-anything-for-dependency-visualization"></a>Kell telepít semmit a függőségek képi megjelenítésének?

Függőségmegjelenítést használ, meg kell töltse le és telepítse az ügynököt minden olyan szeretne értékelni a helyszíni gépen.

- [Microsoft Monitoring agent(MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows) minden gépen kell telepíteni.
- A [függőségi ügynök](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure) minden gépen kell telepíteni.
- Ezenkívül ha internetkapcsolat nélküli gépek, meg kell töltse le és telepítse őket a Log Analytics-átjáró.

Ezeket az ügynököket nem szükséges, kivéve, ha függőségmegjelenítést használ.

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>Használható a függőségek megjelenítését egy meglévő munkaterületet?

Igen, meglévő munkaterület csatolása a migrálási projektben, és a függőségek képi megjelenítésének kihasználja azt. [További információk](concepts-dependency-visualization.md#how-does-it-work).

### <a name="can-i-export-the-dependency-visualization-report"></a>Exportálhatja a függőségek képi megjelenítés jelentést?

Nem, nem lehet exportálni a függőségek képi megjelenítéséről. Azonban mivel a Service Map az Azure Migrate használ a függőségek képi megjelenítésével, használhatja a [Service Map REST API-k](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) úgy szerezheti be a függőségeket a json-formátumú.

### <a name="how-can-i-automate-the-installation-of-microsoft-monitoring-agent-mma-and-dependency-agent"></a>Hogyan automatizálhatók a Microsoft Monitoring Agent (MMA) és a függőségi ügynök telepítését?

[Ezzel a parancsfájllal](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples) az ügynökök telepítéséhez. [Az alábbi lépéseket követve](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) segítségével a parancssorból vagy az automation az MMA telepítése. Az MMA, kihasználva [Ez a szkript](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).

Parancsfájlok, mellett használhatja a központi telepítési eszközök például a System Center Configuration Managerben, [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) az ügynökök telepítéséhez stb.

### <a name="what-operating-systems-are-supported-by-mma"></a>Az MMA mely operációs rendszerek támogatottak?

- [Felülvizsgálat](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems) MMA által támogatott Windows operációs rendszerek listája.
- [Áttekintése] https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) MMA által támogatott Linux operációs rendszerek listája.

### <a name="what-are-the-operating-systems-supported-by-the-dependency-agent"></a>Mik azok a függőségi ügynök által támogatott operációs rendszerek?

[Felülvizsgálat](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems) a függőségi ügynök által támogatott Windows operációs rendszerek.
[Felülvizsgálat](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems) a függőségi ügynök által támogatott Linux operációs rendszerek listája.

### <a name="can-i-visualize-dependencies-in-azure-migrate-for-more-than-an-hour"></a>Megjeleníthetők az Azure Migrate függőségeinek több, mint egy órája?
Nem, jelenítheti meg akár egy órára függőségeit. Visszaléphet az előzmények, akár az elmúlt hónapban, az adott dátumhoz, de a maximális időtartamot, a Vizualizáció egy óra. Például akkor használhatja tegnap függőségek megtekintése a függőségi térkép az időtartamot, de csak megtekintheti azt számára egy egy órás időszak. Azonban használhatja az Azure Monitor naplók [függőségi adatlekérdezés](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) hosszabb ideig keresztül.

### <a name="is-dependency-visualization-supported-for-groups-with-more-than-ten-vms"></a>Függőségek képi megjelenítésének csoportokhoz a több mint tíz virtuális gépek támogatott?
Is [csoportokra vonatkozó függőségek vizualizálása](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) legfeljebb tíz virtuális gépekkel. Ha egy csoport több mint tíz virtuális gépekkel rendelkezik, azt javasoljuk, hogy a csoport a kisebb csoportok felosztása, és ezután megjelenítheti a függőségeket.

## <a name="azure-migrate-server-migration"></a>Az Azure Migrate: Kiszolgáló áttelepítése

### <a name="how-is-azure-migrate-server-migration-different-from-azure-site-recovery"></a>Hogyan történik az Azure Migrate: A Kiszolgálóáttelepítés különbözik az Azure Site Recovery?

Az Azure Migrate: Kiszolgáló-áttelepítési áttelepítéséhez az ügynök-alapú kiszolgálók Azure-bA a Site Recovery replikációs motorjának használja.
## <a name="next-steps"></a>További lépések
Olvassa el a [Azure Migrate áttekintése](migrate-services-overview.md)

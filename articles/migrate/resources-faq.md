---
title: Az Azure Migrate – gyakori kérdések (GYIK) |} A Microsoft Docs
description: A címek az Azure Migrate kapcsolatos gyakori kérdések
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: snehaa
ms.openlocfilehash: 619369972991bab0b23e7e9ae4e0a146f875e59a
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2019
ms.locfileid: "55079256"
---
# <a name="azure-migrate---frequently-asked-questions-faq"></a>Az Azure Migrate – gyakori kérdések (GYIK)

Ez a cikk az Azure Migrate – gyakori kérdések tartalmazza. Ha ez a cikk elolvasása után további lekérdezéseket, tegye azt közzé a a [Azure Migrate fórum](https://aka.ms/AzureMigrateForum).

## <a name="general"></a>Általános kérdések

### <a name="does-azure-migrate-support-assessment-of-only-vmware-workloads"></a>Az Azure Migrate támogatja csak a VMware számítási feladatainak értékelését?

Igen, az Azure Migrate jelenleg csak támogatja a VMware számítási feladatainak értékelését. Hyper-V és fizikai kiszolgálók támogatása a jövőben engedélyezve lesz.

### <a name="does-azure-migrate-need-vcenter-server-to-discover-a-vmware-environment"></a>Nem kell az Azure Migrate a vCenter-kiszolgáló felderítése a VMware-környezet?

Igen, az Azure Migrate vCenter-kiszolgáló egy VMware-környezet felderítéséhez szükséges. Nem támogatja a vCenter-kiszolgáló által nem kezelt ESXi-gazdagépek felderítését.

### <a name="how-is-azure-migrate-different-from-azure-site-recovery"></a>Miben különbözik az Azure Migrate az Azure Site Recovery?

Az Azure Migrate egy felméréseket készítő szolgáltatás, amely segít felderíteni a helyszíni számítási feladatok, és tervezze meg a migrációt az Azure-bA. [Az Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure), folyamatban van egy vész-helyreállítási megoldást, valamint segít a helyszíni számítási feladatok migrálása az Azure IaaS virtuális gépekre.

### <a name="whats-the-difference-between-using-azure-migrate-for-assessments-and-the-map-toolkit"></a>Mi a különbség az Azure Migrate az értékelés és a Map eszközkészlet között?

[Az Azure Migrate](migrate-overview.md) kifejezetten való migrálás kompatibilitásáról és a helyszíni számítási feladatok Azure-ba való értékelése a segítségére a migrálási felmérést biztosít. [A Microsoft Assessment és tervezés (Leképezés) eszközkészlet](https://www.microsoft.com/en-us/download/details.aspx?id=7826) rendelkezik egyéb funkciókat, például a migrálási tervezés újabb verzióiban a Windows ügyfél és kiszolgáló operációs rendszerek és szoftverek használat nyomon követése. Ezek a forgatókönyvek esetén továbbra is használhatja a MAP eszközkészlet.


### <a name="how-is-azure-migrate-different-from-azure-site-recovery-deployment-planner"></a>Miben különbözik az Azure Migrate az Azure Site Recovery Deployment Planner?

Az Azure Migrate egy migrálási eszközt, és az Azure Site Recovery Deployment Planner eszközt vészhelyreállítás (DR).

**Áttelepítés a VMware-ből az Azure-bA**: Ha szeretne a helyszíni számítási feladatok migrálása az Azure-ba, az áttelepítés tervezéséhez használja az Azure Migrate. Az Azure Migrate a helyszíni számítási feladatokat értékeli, és útmutatást, insights, és segítséget nyújtanak az Azure-ba való migrálás mechanizmusokat biztosít. Miután elkészült a migrálási terv, szolgáltatások, például az Azure Site Recovery és az Azure Database Migration Service segítségével a gépek áttelepítése az Azure-bA.

**Áttelepítés a Hyper-V-ről az Azure-bA**: Az Azure Migrate jelenleg csak támogatja a VMware virtuális gépek értékelése az Azure-ba való migrálásra. Hyper-V támogatása az Azure Migrate tervbe van véve. A belső, a Site Recovery Deployment Planner is használhatja. Hyper-V támogatása az Azure Migrate engedélyezése után is használhatja az Azure Migrate Hyper-V-alapú számítási feladatok migrálásának megtervezéséhez.

**Az Azure-ba történő VMware vagy Hyper-V vész-helyreállítási**: Ha azt tervezi, ehhez a vészhelyreállítás (DR) az Azure-ban az Azure Site Recovery (a Site Recovery), használja a Site Recovery Deployment Planner a vészhelyreállítás megtervezése. A Site Recovery Deployment Planner hajtja végre a helyszíni környezet mély, az ASR-specifikus értékelését. Javaslatok a sikeres Vészhelyreállítási műveletek, például a replikáció, feladatátvétel, a virtuális gépek Site Recovery által igényelt biztosít.  

### <a name="which-azure-geographies-are-supported-by-azure-migrate"></a>Melyik Azure-régiócsoportok támogat az Azure Migrate?

Az Azure Migrate Európa, Egyesült Államok és az Azure Government jelenleg a projekt földrajzi területeken támogatja. Annak ellenére, hogy ezek földrajzi területeken csak migration-projektek hozhat létre, továbbra is felmérheti a gépek [több célhelyek](https://docs.microsoft.com/azure/migrate/how-to-modify-assessment#edit-assessment-properties). A projekt földrajzi csak használatos felderített metaadatok tárolására.

**Régiócsoport** | **Metaadatok tárolási helye**
--- | ---
Azure Government | USA-beli államigazgatás – Virginia
Ázsia | Délkelet-Ázsia
Európa | Észak-Európában és Nyugat-Európa
Több egység állapotok | USA nyugati középső RÉGIÓJA, USA keleti RÉGIÓJA

### <a name="how-does-the-on-premises-site-connect-to-azure-migrate"></a>Hogyan nem a helyszíni hely csatlakozni az Azure Migrate?

A kapcsolat az interneten keresztül vagy az ExpressRoute használata a nyilvános társviszony-létesítés.

### <a name="can-i-harden-the-vm-set-up-with-the-ova-template"></a>Felvértezni a virtuális gép az OVA-sablon beállítása is?

Mindaddig, amíg a kommunikációs és a tűzfal az Azure Migrate berendezés működéséhez szükséges szabályok van hátra, mert további összetevők (például víruskereső) hozzáadható az OVA sablonba be.   

### <a name="to-harden-the-azure-migrate-appliance-what-are-the-recommended-antivirus-av-exclusions"></a>Az Azure Migrate-készülék, Mik azok a víruskereső (Víruskereső) ajánlott kivételeket mobileszközcsoportra?

Az alábbi mappák a készülék a víruskeresővel való ellenőrzésekből kizárni kívánt szüksége:

- Az Azure Migrate szolgáltatás a bináris fájlokat tartalmazó mappa. Zárja ki az összes alárendelt mappát.
  %ProgramFiles%\ProfilerService  
- Az Azure Migrate webes alkalmazást. Zárja ki az összes alárendelt mappát.
  %SystemDrive%\inetpub\wwwroot
- Helyi gyorsítótár az adatbázis és naplófájlok. Az Azure migrate szolgáltatás ezt a mappát RW lemezt hozzá kell férnie.
  %SystemDrive%\Profiler

## <a name="discovery"></a>Detektálás

### <a name="what-data-is-collected-by-azure-migrate"></a>Azure Migrate által gyűjtött adatokat?

Az Azure Migrate kétféle felderítést támogat, a berendezés- és az ügynökalapú felderítést.
A berendezés-alapú felderítés a helyszíni virtuális gépek metaadatait gyűjti, a berendezés által gyűjtött metaadatok teljes listáját lásd alább:

**A virtuális gép konfigurációs adatok**
- Virtuális gép megjelenített neve (a vCenter)
- Virtuális gép szoftverleltár elérési útja (gazdagép/fürt/mappa vCenter)
- IP-cím
- MAC-cím
- Operációs rendszer
- Magok, lemezek, hálózati adapterek száma
- Memória mérete, a lemezméretek

**A virtuális gép teljesítményadatok**
- Processzorhasználat
- Memóriahasználat
- A virtuális géphez csatlakoztatott összes lemez:
  - Lemez sebessége olvasott átviteli sebesség
  - Lemezre írási műveletek átviteli sebesség
  - Lemezolvasási művelet / mp-ben
  - Lemezre írási műveletek / mp-ben
- Az összes virtuális Géphez csatlakoztatott hálózati adapter:
  - A hálózati
  - Kimenő hálózati forgalom

Az ügynökalapú felderítés a berendezésalapú felderítést kiegészítő megoldásként érhető el, és segítséget nyújt az ügyfeleknek a helyszíni virtuális gépek [függőségeinek megjelenítésében](how-to-create-group-machine-dependencies.md). A függőségi ügynökök olyan adatokat gyűjtenek be a virtuális gépről, mint a teljes tartománynév, az operációs rendszer, az IP-cím, a MAC-cím, a virtuális gépen futó folyamatok és a bejövő/kimenő TCP-kapcsolatok. Az ügynökalapú felderítés nem kötelező, és ha szeretné, ha nem szeretné a virtuális gépek a függőségek képi megjelenítése érdekében telepítse az ügynököket.

### <a name="would-there-be-any-performance-impact-on-the-analyzed-esxi-host-environment"></a>A elemzett ESXi-gazdagép környezet teljesítményét hatással az ott lenne?

Abban az esetben, a [egy ideje a felderítési módszer](https://docs.microsoft.com/azure/migrate/concepts-collector#discovery-methods), annak érdekében, hogy a teljesítményadatok összegyűjtése a statisztikai szint a vCenter-kiszolgálón 3 értéke lenne. Ezen a szinten értékre állítaná összegyűjtik a nagy mennyiségű adatokat, amelyek a vCenter Server-adatbázis a hibaelhárítás. A vcenter Server oldalán teljesítményproblémák lépnek fel, így eredményezhet. Lenne elhanyagolható hatással az ESXi-gazdagépen.

Bevezettük a folyamatos profilkészítés teljesítményadatok (amely előzetes verzióban érhető el). A folyamatos profilkészítés már nem látható egy kell módosítani, a vCenter Server statisztikai szintje a teljesítményalapú értékelés futtatása. A gyűjtőberendezés most fog profilt készíteni a helyszíni gépek és mérhető, a virtuális gépek teljesítményadatait. Ez az ESXi-gazdagépek, valamint a vCenter-kiszolgáló rendelkezik szinte nullára gyakorolt hatást.

### <a name="where-is-the-collected-data-stored-and-for-how-long"></a>Hol található az összegyűjtött adatok tárolt és mennyi ideig?

A gyűjtőberendezés által összegyűjtött adatokat az Azure-beli helyen, a migrálási projekt létrehozása során megadott tárolt. Az adatok tárolása biztonságos egy adott Microsoft-előfizetés, és törlődik, amikor a felhasználó törli az Azure Migrate-projektben.

A függőségek képi megjelenítéséről a virtuális gépeket, telepíti a védelmi ügynököt a függőségi ügynökök által gyűjtött adatok tárolódnak a az USA-beli felhasználói előfizetésben létrehozott Log Analytics-munkaterületet. Ezeket az adatokat a Log Analytics-munkaterületet az előfizetésben való törlésekor törlődik. [További információk](https://docs.microsoft.com/azure/migrate/concepts-dependency-visualization).

### <a name="is-the-data-encrypted-at-rest-and-while-in-transit"></a>Titkosítja az adatokat inaktív állapotban, és az átvitel során?

Igen, az összegyűjtött adatok titkosított inaktív és az átvitel során. A berendezés által gyűjtött metaadatok biztonságosan küld az Azure Migrate szolgáltatás interneten keresztül https-kapcsolaton keresztül. Az összegyűjtött metaadatok tárolódnak [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest) és a [az Azure blob storage-bA](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) egy adott Microsoft-előfizetés és titkosítása.

A függőségi ügynökök által gyűjtött adatok is a titkosított átvitel (biztonságos csatornán), és a felhasználói előfizetés a Log Analytics-munkaterület van tárolva. Inaktív állapotban is titkosítva vannak.

### <a name="how-does-the-collector-communicate-with-the-vcenter-server-and-the-azure-migrate-service"></a>A gyűjtő miként nem kommunikál a vCenter-kiszolgáló és az Azure Migrate szolgáltatás?

A gyűjtőberendezés csatlakozik a vCenter-kiszolgáló (a 443-as port) a készülék a felhasználó által megadott hitelesítő adatokkal. A vCenter Server használatával a VMware PowerCLI gyűjtéséhez a vCenter-kiszolgáló által kezelt virtuális gépek metaadatait kérdezi le. Azt is konfigurációs adatokat gyűjti össze (magok, memória, lemezek, hálózati adapter stb.) virtuális gépek és az egyes virtuális gépek korábbi teljesítménye az utolsó egy hónap a vCenter Server alkalmazásból. Az összegyűjtött metaadatok majd érkezik az Azure Migrate szolgáltatás (interneten keresztül https) az értékeléshez. [További információ](concepts-collector.md)

### <a name="can-i-connect-the-same-collector-appliance-to-multiple-vcenter-servers"></a>Csatlakozhatok az azonos gyűjtőberendezés több vCenter-kiszolgáló?

Igen, egy gyűjtőberendezés segítségével több vCenter-kiszolgálók felderítéséhez, de nem egy időben. Egymás után a felderítések futtatásához szükséges.

### <a name="is-the-ova-template-used-by-site-recovery-integrated-with-the-ova-used-by-azure-migrate"></a>A Site Recovery által használt OVA sablon integrálva van az Azure Migrate által használt OVA?

Jelenleg nincs nincs integráció. A. A Site Recovery OVA-sablon beállítása a Site Recovery konfigurációs kiszolgálónak a VMware virtuális gépek/fizikai kiszolgáló replikációjához szolgál. A. Az Azure Migrate által használt OVA migrálási felmérést alkalmazásában egy vCenter-kiszolgáló által felügyelt VMware virtuális gépek felderítésére szolgál.

### <a name="i-changed-my-machine-size-can-i-rerun-the-assessment"></a>Módosítottam a saját gép méretét. Futtathatja az értékelést?

Ha módosítja az értékelni kívánt virtuális gép beállításait, az eseményindító Fedezze fel a gyűjtőberendezés segítségével újból. A berendezés, használja a **gyűjtés újbóli elindítása** ehhez lehetőséget. Miután elkészült a gyűjtemény, válassza az **Újraszámítás** lehetőséget az értékeléshez a portálon, hogy megkapja a frissített értékelési eredményeket.

### <a name="how-can-i-discover-a-multi-tenant-environment-in-azure-migrate"></a>Hogyan képes felderíteni egy több-bérlős környezetben az Azure Migrate?

Ha a bérlők között megosztott környezet, és nem szeretné, hogy egy bérlő a bérlő egy másik előfizetésben található virtuális gépek felderítése, a hatókör mezője a gyűjtőberendezés segítségével a felderítés hatókörét. Ha a bérlők gazdagépek, csak a virtuális gépek az adott bérlőbe történő tartozó csak olvasási hozzáféréssel rendelkező hitelesítő adatok létrehozása, majd ezeket a hitelesítő adatokat használni a gyűjtőberendezés, és a gazdagépként ehhez a felderítési hatókör megadása. Azt is megteheti a virtuális gépek tenant1 mappa1, valamint tenant2 mappákat is létrehozhat a vCenter Server (tegyük a tenant1 mappa1 és a tenant2 mappa2), a megosztott gazdagép alatt helyezhetik át mappa2 és majd ennek megfelelően az a felderítéseket, a gyűjtő a hatókör Adja meg a megfelelő mappát.

### <a name="how-many-virtual-machines-can-be-discovered-in-a-single-migration-project"></a>Hány virtuális gépet egyetlen migrálási projekt könnyen megtalálhatók legyenek?

Felfedezheti, hogy egyetlen migrálási projekt 1500 virtuális gépet. Ha több gépet a helyszíni környezetben, [további](how-to-scale-assessment.md) kapcsolatos is találhat meg az Azure Migrate nagy méretű környezet.


## <a name="assessment"></a>Értékelés

### <a name="does-azure-migrate-support-enterprise-agreement-ea-based-cost-estimation"></a>Nem Azure Migrate támogatás nagyvállalati szerződés (EA) alapú költségbecsléshez?

Az Azure Migrate jelenleg nem támogatja a költségbecslést [ajánlat nagyvállalati ügyfeleknek](https://azure.microsoft.com/offers/enterprise-agreement-support/). A megoldás, hogy adja meg a használatalapú fizetéssel az ajánlat és manuális megadása (az előfizetésre vonatkozó) százalékos kedvezmény az értékelésben kedvezményes"mezőjében.

  ![Kedvezmény](./media/resources-faq/discount.png)

### <a name="what-is-the-difference-between-as-on-premises-sizing-and-performance-based-sizing"></a>Mi a különbség a között, helyszíni méretezése és a teljesítményalapú méretezéshez?

A méretezési feltétel teljesítményalapú szerint – a helyszínen a megadása esetén méretezése, Azure Migrate nem veszi figyelembe a teljesítményadatokat a virtuális gépek és a helyszíni konfiguráció alapján a virtuális gépek mérete. Ha a méretezési feltétel teljesítményalapú, a méretezés a kihasználtsági adatok alapján történik. Ha például van egy helyszíni virtuális gép 4 maggal és 8 GB memóriát, valamint 50 CPU-kihasználtság százalékos és 50 %-a memóriahasználat. Ha a méretezési feltétel teljesítményalapú a helyszíni méretezése egy Azure-beli VM Termékváltozata 4 maggal és 8GB memória használata ajánlott, azonban, ha a méretezési feltétel teljesítményalapú alapján, mint 2 magos virtuális gép Termékváltozata, és 4 GB lenne ajánlott, mivel a kihasználtsági százaléka van közben javasolja a méretét. Ehhez hasonlóan a lemezek, a lemezt méretezési attól függ, két értékelés tulajdonságai – a méretezési feltétel és a tárolás típusát. Ha a méretezési feltétel teljesítményalapú, a tárolótípus pedig automatikus, a rendszer a lemez IOPS- és átvitelisebesség-értékeit veszi figyelembe a cél lemeztípus (Standard vagy Prémium) azonosításához. Ha a méretezési feltétel teljesítményalapú, a tárolótípus pedig prémium, a rendszer prémium lemezt ajánl. A prémium lemez termékváltozat kiválasztása az Azure-ban a helyszíni lemez mérete alapján történik. A rendszer ugyanezt a logikát használja a lemezméretezéshez, ha a méretezési feltétel helyszíni méretezés, a tárolótípus pedig standard vagy prémium.

### <a name="what-impact-does-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>Milyen hatással van a teljesítmény előzmények és PERCENTILIS kihasználtságát a javaslatok a méretekkel kapcsolatban?

Ez a tulajdonságok csak a teljesítményalapú méretezés esetén számítanak. Az Azure Migrate gyűjti a helyszíni gépek teljesítményelőzményeit, és ezeket alapján tesz javaslatot az Azure-beli virtuális gép méretére és lemeztípusára. A gyűjtőberendezés folyamatosan készít profilokat a helyszíni környezetről a kihasználtsággal kapcsolatos valós idejű adatok 20 másodpercenként történő gyűjtéséhez. A berendezés összesíti a 20 másodpercenként vett mintákat, és egyetlen adatpontot hoz létre minden 15 percről. A berendezés úgy hoz létre egyetlen adatpontot, hogy kiválasztja minden 20 másodperces minta csúcsértékét, amelyet elküld az Azure-nak. Amikor értékelést hoz létre az Azure-ban a teljesítmény-időtartam és a teljesítményelőzmények percentilisértéke alapján, az Azure Migrate a tényleges kihasználtsági értéket számítja ki, és azt használja a méretezéshez. Például ha a teljesítmény időtartamát, 1 nap és a 95. percentilis százalékértékről állított be, az Azure Migrate használja a pontok küldött gyűjtő által az elmúlt egy nap, növekvő sorrendben rendezi őket, és kiválasztja a hatékony ut mint 95 % 15 perc-minta ilization. A 95. percentilis biztosítja, hogy ha kiválasztjuk az esetek 99 % előfordulhat, hogy olyan kiugró adatokat figyelmen kívül. Ha az időtartam használati csúcsát szeretné kiválasztani, és nem szeretne lemaradni egyetlen kiugró adatról sem, a 99. percentilist válassza.

## <a name="dependency-visualization"></a>Függőségek vizualizációja

> [!NOTE]
> A függőségek képi megjelenítésének funkcióival nem érhető el az Azure Government szolgáltatásban.

### <a name="what-is-dependency-visualization"></a>Mit jelent a függőségek képi megjelenítésének?

Függőségek képi megjelenítésének lehetővé teszi, hogy Önnek felmérni a virtuális gépek migrálásra csoportok nagyobb biztonsággal által gépfüggőségek kereszt-ellenőrzés az értékelés futtatása előtt. Függőségek képi megjelenítésének segítségével győződjön meg arról, hogy semmi sem van magányos, elkerülve a váratlan kimaradás esetén, az Azure-ba való áttelepítésekor. Az Azure Migrate a Service Map megoldás a Log Analytics engedélyezése a függőségek képi megjelenítésének használja.

### <a name="do-i-need-to-pay-to-use-the-dependency-visualization-feature"></a>Kell fizetnie a függőségmegjelenítési funkciót használja?

Nem. További tudnivalókat az Azure Migrate díjszabásáról [itt](https://azure.microsoft.com/pricing/details/azure-migrate/) talál.

### <a name="do-i-need-to-install-anything-for-dependency-visualization"></a>Kell telepít semmit a függőségek képi megjelenítésének?

Függőségmegjelenítést használ, meg kell töltse le és telepítse az ügynököt minden olyan szeretne értékelni a helyszíni gépen.

- [Microsoft Monitoring agent(MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows) minden gépen kell telepíteni.
- A [függőségi ügynök](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure) minden gépen kell telepíteni.
- Ezenkívül ha internetkapcsolat nélküli gépek, meg kell töltse le és telepítse őket a Log Analytics-átjáró.

Nem kell ezeket az ügynököket, kivéve, ha függőségmegjelenítést használ értékelni kívánt gépeken.

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>Használható a függőségek megjelenítését egy meglévő munkaterületet?

Igen, az Azure Migrate mostantól lehetővé teszi meglévő munkaterület csatolása a migrálási projekt és függőségek képi kihasználja azt. [További információk](https://docs.microsoft.com/azure/migrate/concepts-dependency-visualization#how-does-it-work).

### <a name="can-i-export-the-dependency-visualization-report"></a>Exportálhatja a függőségek képi megjelenítés jelentést?

Nem, a függőség vizualizációja nem exportálható. Azonban mivel a Service Map az Azure Migrate használ a függőségek képi megjelenítésével, használhatja a [Service Map REST API-k](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) úgy szerezheti be a függőségeket a json-formátumú.

### <a name="how-can-i-automate-the-installation-of-microsoft-monitoring-agent-mma-and-dependency-agent"></a>Hogyan automatizálhatók a Microsoft Monitoring Agent (MMA) és a függőségi ügynök telepítését?

[Itt](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples) olyan parancsfájl, amely is használhat a függőségi ügynök telepítéséről. [Itt](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) vonatkozó utasításokkal hogyan telepítse az MMA parancssor vagy automatizált módszert használ. Az MMA, hogy kihasználhatja elérhető parancsfájl [Itt](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab) a TechNet webhelyén.

Parancsfájlok, mellett is használhatja a System Center Configuration Manager (SCCM) például a központi telepítési eszközök [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) az ügynökök telepítéséhez stb.

### <a name="what-are-the-operating-systems-supported-by-mma"></a>Mik azok az MMA által támogatott operációs rendszerek?

Az MMA által támogatott Windows operációs rendszerek listája [Itt](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
Az MMA által támogatott Linux operációs rendszerek listája [Itt](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

### <a name="what-are-the-operating-systems-supported-by-dependency-agent"></a>Mik azok a függőségi ügynök által támogatott operációs rendszerek?

Függőségi ügynök által támogatott Windows operációs rendszerek listája [Itt](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems).
Függőségi ügynök által támogatott Linux operációs rendszerek listája [Itt](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems).

### <a name="can-i-visualize-dependencies-in-azure-migrate-for-more-than-one-hour-duration"></a>Megjeleníthetők az Azure Migrate függőségeinek több mint egy óra időtartama?
Nem, az Azure Migrate segítségével vizualizálhatja függőségek legfeljebb egy órát részére. Az Azure Migrate lehetővé teszi, hogy térjen vissza az az előzmények között az utolsó egy hónap legfeljebb egy adott dátumot, de a maximális időtartamot, amelynek jelenítheti meg a függőségek legfeljebb 1 óra. Például hogy használhatja tegnap függőségek megtekintése a függőségi térkép az idő időtartamát funkciót, de csak megtekintheti azt számára egy egy órás időszak. Azonban, használhatja a Log Analyticsben, hogy [a függőségi adatok lekérdezése](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#query-dependency-data-from-log-analytics) hosszabb ideig keresztül.

### <a name="is-dependency-visualization-supported-for-groups-with-more-than-10-vms"></a>Függőségek képi megjelenítésének támogatott csoportok több mint 10-alapú virtuális gépekhez?
Is [csoportokra vonatkozó függőségek vizualizálása](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) , amelyek legfeljebb 10 virtuális gépeket. Ha egy csoport több mint 10 virtuális géppel rendelkezik, azt javasoljuk, hogy a csoport a kisebb csoportok osztani, és a Függőségek megjelenítése.


## <a name="next-steps"></a>További lépések

- Olvassa el a [Azure Migrate áttekintése](migrate-overview.md)
- Megtudhatja, hogyan zajlik [felderítéséhez és értékeléséhez](tutorial-assessment-vmware.md) egy VMware-környezet

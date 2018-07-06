---
title: Az Azure Migrate – gyakori kérdések (GYIK) |} A Microsoft Docs
description: A címek az Azure Migrate kapcsolatos gyakori kérdések
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: snehaa
ms.openlocfilehash: c85e512dede7c14e7b678297ed524fa7a1d7e79d
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2018
ms.locfileid: "37859523"
---
# <a name="azure-migrate---frequently-asked-questions-faq"></a>Az Azure Migrate – gyakori kérdések (GYIK)

Ez a cikk az Azure Migrate – gyakori kérdések tartalmazza. Ha ez a cikk elolvasása után további lekérdezéseket, tegye azt közzé a a [Azure Migrate fórum](http://aka.ms/AzureMigrateForum).

## <a name="general"></a>Általános kérdések

### <a name="how-is-azure-migrate-different-from-azure-site-recovery"></a>Miben különbözik az Azure Migrate az Azure Site Recovery?

Az Azure Migrate egy felméréseket készítő szolgáltatás, amely segít felderíteni a helyszíni számítási feladatok, és tervezze meg a migrációt az Azure-bA. [Az Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure), folyamatban van egy vész-helyreállítási megoldást, valamint segít a helyszíni számítási feladatok migrálása az Azure IaaS virtuális gépekre.

### <a name="whats-the-difference-between-using-azure-migrate-for-assessments-and-the-map-toolkit"></a>Mi a különbség az Azure Migrate az értékelés és a Map eszközkészlet között?

[Az Azure Migrate](migrate-overview.md) kifejezetten való migrálás kompatibilitásáról és a helyszíni számítási feladatok Azure-ba való értékelése a segítségére a migrálási felmérést biztosít. [A Microsoft Assessment és tervezés (Leképezés) eszközkészlet](https://www.microsoft.com/en-us/download/details.aspx?id=7826) egyéb funkcióval rendelkezik. Például a migrálás megtervezése újabb verzióiban a Windows ügyfél és kiszolgáló operációs rendszerek, szoftverek használatának követése stb. Ezek a forgatókönyvek esetén továbbra is használhatja a MAP eszközkészlet.


### <a name="how-is-azure-migrate-different-from-azure-site-recovery-deployment-planner"></a>Miben különbözik az Azure Migrate az Azure Site Recovery Deployment Planner?

Az Azure Migrate egy migrálási eszközt, és az Azure Site Recovery Deployment Planner eszközt vészhelyreállítás (DR).

**Áttelepítés a VMware-ből az Azure-bA**: Ha azt tervezi, a helyszíni számítási feladatok migrálása az Azure-ba, az Azure Migrate az áttelepítés tervezéséhez. Az Azure Migrate a helyszíni számítási feladatokat értékeli, és útmutatást, insights, és segítséget nyújtanak az Azure-ba való migrálás mechanizmusokat biztosít. Miután elkészült a migrálási terv, szolgáltatások, például az Azure Site Recovery és az Azure Database Migration Service segítségével a gépek áttelepítése az Azure-bA.

**Az Azure-ba történő Hyper-V áttelepítés**: az Azure Migrate jelenleg csak az támogatja a VMware virtuális gépek értékelése az Azure-ba való migrálásra. Hyper-V támogatása az Azure Migrate tervbe van véve. A belső, a Site Recovery Deployment Planner is használhatja. Hyper-V támogatása az Azure Migrate engedélyezése után is használhatja az Azure Migrate Hyper-V-alapú számítási feladatok migrálásának megtervezéséhez.

**Az Azure-ba történő VMware vagy Hyper-V vész-helyreállítási**: Ha azt tervezi, ehhez a vészhelyreállítás (DR) az Azure-ban az Azure Site Recovery (a Site Recovery), használja a Site Recovery Deployment Planner a vészhelyreállítás megtervezése. A Site Recovery Deployment Planner hajtja végre a helyszíni környezet mély, az ASR-specifikus értékelését. Javaslatok a sikeres Vészhelyreállítási műveletek, például a replikáció, feladatátvétel, a virtuális gépek Site Recovery által igényelt biztosít.  

### <a name="does-azure-migrate-need-vcenter-server-to-discover-a-vmware-environment"></a>Nem kell az Azure Migrate a vCenter-kiszolgáló felderítése a VMware-környezet?

Igen, az Azure Migrate vCenter-kiszolgáló egy VMware-környezet felderítéséhez szükséges. Nem támogatja a vCenter-kiszolgáló által nem kezelt ESXi-gazdagépek felderítését.

### <a name="which-azure-regions-are-supported-by-azure-migrate"></a>Mely Azure-régiók az Azure Migrate által támogatott?

### <a name="how-does-the-on-premises-site-connect-to-azure-migrate"></a>Hogyan nem a helyszíni hely csatlakozni az Azure Migrate?

A kapcsolat az interneten keresztül vagy az ExpressRoute használata a nyilvános társviszony-létesítés.

### <a name="can-i-harden-the-vm-set-up-with-the-ova-template"></a>Is szeretnék felvértezni a virtuális gép beállítása a. OVA sablont?

További összetevőket (például víruskereső) is lesz hozzáadva a. OVA mindaddig, amíg a kommunikációs és a tűzfal szabályok az Azure Migrate berendezés működéséhez szükség van hátra áll.   


## <a name="discovery-and-assessment"></a>Felderítés és értékelés

### <a name="what-data-is-collected-by-azure-migrate"></a>Azure Migrate által gyűjtött adatokat?

Az Azure Migrate felderítési, a készülék alapú felderítés és az ügynökalapú felderítés kétféle támogatja.
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

Az ügynökalapú felderítés beállítás elérhető, a készülék-alapú felderítés felett, és segítséget nyújt ügyfeleinknek [függőségek vizualizálása](how-to-create-group-machine-dependencies.md) a helyszíni virtuális gépek. A függőségi ügynökök gyűjthet adatokat, például teljes tartománynév, az operációs rendszer, IP címe, a MAC-cím, a virtuális gépről a virtuális gép és a bejövő/kimenő TCP-kapcsolatok belül futó folyamatok. Az ügynökalapú felderítés nem kötelező, és ha szeretné, ha nem szeretné a virtuális gépek a függőségek képi megjelenítése érdekében telepítse az ügynököket.

### <a name="where-is-the-collected-data-stored-and-for-how-long"></a>Hol található az összegyűjtött adatok tárolt és mennyi ideig?

A gyűjtőberendezés által összegyűjtött adatokat az Azure-beli helyen, a migrálási projekt létrehozása során megadott tárolt. Az adatok tárolása biztonságos egy adott Microsoft-előfizetés, és törlődik, amikor a felhasználó törli az Azure Migrate-projektben.

A függőségek képi megjelenítéséről a virtuális gépeket, telepíti a védelmi ügynököt a függőségi ügynökök által gyűjtött adatok tárolódnak a az USA-beli felhasználói előfizetésben létrehozott OMS-munkaterületet. Ezeket az adatokat az OMS-munkaterületet az előfizetésben való törlésekor törlődik. [További információk](https://docs.microsoft.com/azure/migrate/concepts-dependency-visualization).

### <a name="how-does-the-collector-communicate-with-the-vcenter-server-and-the-azure-migrate-service"></a>A gyűjtő miként nem kommunikál a vCenter-kiszolgáló és az Azure Migrate szolgáltatás?

A gyűjtőberendezés csatlakozik a vCenter-kiszolgáló (a 443-as port) a készülék a felhasználó által megadott hitelesítő adatokkal. A vCenter Server használatával a VMware PowerCLI gyűjtéséhez a vCenter-kiszolgáló által kezelt virtuális gépek metaadatait kérdezi le. Azt is konfigurációs adatokat gyűjti össze (magok, memória, lemezek, hálózati adapter stb.) virtuális gépek és az egyes virtuális gépek korábbi teljesítménye az utolsó egy hónap a vCenter Server alkalmazásból. Az összegyűjtött metaadatok majd érkezik az Azure Migrate szolgáltatás (interneten keresztül https) az értékeléshez. [További információ](concepts-collector.md)

### <a name="can-i-connect-to-multiple-vcenter-servers"></a>Csatlakozhatok több vCenter-kiszolgáló?

Egy összekötő készülék szükséges megfelelő az egyes kiszolgálókon.

### <a name="is-the-ova-template-used-by-site-recovery-integrated-with-the-ova-used-by-azure-migrate"></a>Van a. A Site Recovery által használt OVA sablon integrálva van a. Azure Migrate által használt OVA?

Jelenleg nincs nincs integráció. A. A Site Recovery OVA-sablon beállítása a Site Recovery konfigurációs kiszolgálónak a VMware virtuális gépek/fizikai kiszolgáló replikációjához szolgál. A. Az Azure Migrate által használt OVA migrálási felmérést alkalmazásában egy vCenter-kiszolgáló által felügyelt VMware virtuális gépek felderítésére szolgál.

### <a name="i-changed-my-machine-size-can-i-rerun-the-assessment"></a>Módosítottam a saját gép méretét. Futtathatja az értékelést?

Ha módosítja az értékelni kívánt virtuális gép beállításait, az eseményindító Fedezze fel a gyűjtőberendezés segítségével újból. A berendezés, használja a **gyűjtés újbóli elindítása** ehhez lehetőséget. Miután elkészült a gyűjteményben, válassza ki a **számítsa ki újra** frissített értékelési eredmények eléréséhez a portálon, az értékelés lehetőséget.

### <a name="is-the-data-encrypted-at-rest-and-while-in-transit"></a>Titkosítja az adatokat inaktív állapotban, és az átvitel során?

Igen, az összegyűjtött adatok titkosított inaktív és az átvitel során. A berendezés által gyűjtött metaadatok biztonságosan küld az Azure Migrate szolgáltatás interneten keresztül https-kapcsolaton keresztül. Az összegyűjtött metaadatok tárolódnak [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest) és a [az Azure blob storage-bA](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) egy adott Microsoft-előfizetés és titkosítása.

A függőségi ügynökök által gyűjtött adatok is a titkosított átvitel (biztonságos csatornán), és a felhasználói előfizetés a Log Analytics-munkaterület van tárolva. Inaktív állapotban is titkosítva vannak.

### <a name="how-can-i-discover-a-multi-tenant-environment-in-azure-migrate"></a>Hogyan képes felderíteni egy több-bérlős környezetben az Azure Migrate?

Ha a bérlők között megosztott környezet, és nem szeretné, hogy egy bérlő a bérlő egy másik előfizetésben található virtuális gépek felderítése, a hatókör mezője a gyűjtőberendezés segítségével a felderítés hatókörét. Ha a bérlők gazdagépek, csak a virtuális gépek az adott bérlőbe történő tartozó csak olvasási hozzáféréssel rendelkező hitelesítő adatok létrehozása, majd ezeket a hitelesítő adatokat használni a gyűjtőberendezés, és a gazdagépként ehhez a felderítési hatókör megadása. Azt is megteheti a virtuális gépek tenant1 mappa1, valamint tenant2 mappákat is létrehozhat a vCenter Server (tegyük a tenant1 mappa1 és a tenant2 mappa2), a megosztott gazdagép alatt helyezhetik át mappa2 és majd ennek megfelelően az a felderítéseket, a gyűjtő a hatókör Adja meg a megfelelő mappát.

### <a name="how-many-virtual-machines-can-be-discovered-in-a-single-migration-project"></a>Hány virtuális gépet egyetlen migrálási projekt könnyen megtalálhatók legyenek?

Felfedezheti, hogy egyetlen migrálási projekt 1500 virtuális gépet. Ha több gépet a helyszíni környezetben, [további](how-to-scale-assessment.md) kapcsolatos is találhat meg az Azure Migrate nagy méretű környezet.

## <a name="dependency-visualization"></a>Függőségek vizualizációja

### <a name="do-i-need-to-pay-to-use-the-dependency-visualization-feature"></a>Kell fizetnie a függőségmegjelenítési funkciót használja?

Az Azure Migrate díjmentesen érhető el. További tudnivalókat az Azure Migrate díjszabásáról [itt](https://azure.microsoft.com/pricing/details/azure-migrate/) talál.

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>Használható a függőségek megjelenítését egy meglévő munkaterületet?

Az Azure Migrate nem támogatja a függőségek képi megjelenítésének használatával egy meglévő munkaterületet, azonban a Microsoft Monitoring Agent (MMA) többhelyű elérhetőséget támogat, és lehetővé teszi, hogy adatokat küldjön több munkaterülettel. Így ha már rendelkezik az ügynökök üzembe helyezését és konfigurálását egy munkaterülethez, is kihasználhatja az MMA-ügynök a többkiszolgálós, és konfigurálja azt az Azure Migrate munkaterületre (felül a meglévő munkaterület) és együttműködésre. [Itt](https://blogs.technet.microsoft.com/msoms/2016/05/26/oms-log-analytics-agent-multi-homing-support/) meg, hogyan engedélyezheti az MMA-ügynök többkiszolgálós blog van.

## <a name="next-steps"></a>További lépések

- Olvassa el a [Azure Migrate áttekintése](migrate-overview.md)
- Megtudhatja, hogyan zajlik [felderítéséhez és értékeléséhez](tutorial-assessment-vmware.md) egy VMware-környezet

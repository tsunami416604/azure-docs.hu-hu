---
title: Telepítse át az Azure - gyakran ismételt kérdések (GYIK) |} Microsoft Docs
description: Gyakori kérdések az Azure áttelepítése címek
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/06/2018
ms.author: snehaa
ms.openlocfilehash: a18cab73a019039bf5e5829ad1faa4b8f1a70391
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36209945"
---
# <a name="azure-migrate---frequently-asked-questions-faq"></a>Telepítse át az Azure - gyakran ismételt kérdések (GYIK)

A cikk tartalmaz Azure áttelepítése kapcsolatos gyakori kérdésekre. Ha ez a cikk elolvasása után további lekérdezések rendelkezik, az fel őket a [Azure áttelepítése fórum](http://aka.ms/AzureMigrateForum).

## <a name="general"></a>Általános kérdések

### <a name="how-is-azure-migrate-different-from-azure-site-recovery"></a>Miben különbözik Azure át Azure Site Recovery?

Az Azure áttelepítése egy értékelési szolgáltatás, amely segít a helyszíni munkaterhelések tervezze az áttelepítést, az Azure-ba, valamint. [Az Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure), éppen egy vész-helyreállítási megoldást, valamint segít a helyszíni munkaterheléseket telepít át IaaS virtuális gépeket az Azure-ban. 

### <a name="how-is-azure-migrate-different-from-azure-site-recovery-deployment-planner"></a>Miben különbözik Azure át Azure Site Recovery telepítési Planner?

Azure áttelepítése egy áttelepítési eszköz tervezése és Azure Site Recovery telepítési Planner eszköz tervezési vészhelyreállítás (DR).

**Áttérés Azure VMware**: Ha a helyszíni alkalmazások áttelepítése az Azure-ba, használja Azure áttelepítése az áttelepítés tervezéséhez. Azure áttelepítése a helyszíni munkaterhelések értékelésére, és útmutatást, insights és mechanizmusok segítséget áttelepítése az Azure biztosít. Ha ezzel elkészült, az áttelepítési terv, szolgáltatások, például az Azure Site Recovery és az Azure-adatbázis áttelepítése szolgáltatás segítségével a gépek áttelepítése az Azure-bA.

**Az Azure-bA Hyper-V áttelepítés**: Azure áttelepítése jelenleg csak a támogatja a VMware virtuális gépek értékelése áttelepítése az Azure-bA. Hyper-V funkció ütemterv a Azure áttelepíteni. A belső, az automatikus központi telepítési Planner is használhatja. Után Hyper-V támogatása engedélyezve van Azure áttelepítése, használhatja a Azure át a Hyper-V munkaterhelések áttelepítése tervezéséhez.

**Az Azure-ba történő VMware-vagy Hyper-V vész-helyreállítási**: Ha azt tervezi, ehhez a vészhelyreállítás (DR) Azure-ban Azure webhely-helyreállítás (ASR), automatikus központi telepítési Planner használata vész-Helyreállítási megtervezése. Automatikus központi telepítési Planner végzi a helyszíni környezetben a részletes, az automatikus rendszer-Helyreállítás-specifikus értékelése. Javaslatok automatikus rendszer-Helyreállítás sikeres vész-Helyreállítási műveletek, például a replikálást, a virtuális gépek feladatátvételi szükséges biztosít.  

### <a name="does-azure-migrate-need-vcenter-server-to-discover-a-vmware-environment"></a>Nem kell Azure telepítse át a vCenter-kiszolgáló számára az egy VMware-környezetben?

Igen, Azure áttelepítéséhez szükséges vCenter-kiszolgáló számára az egy VMware-környezetben. Nem támogatja az ESXi-gazdagépek, amelyeket nem felügyel egy vCenter-kiszolgáló felderítése.

## <a name="discovery"></a>Detektálás

### <a name="what-data-is-collected-by-azure-migrate"></a>Azure áttelepítése által összegyűjtött adatok?

Az Azure áttelepítése kétféle felderítési, a készülék-alapú feltárásához és az ügynök-alapú feltárásához támogatja.
A készülék-alapú feltárásához összegyűjti a helyszíni virtuális gépek metaadatait, a készülék által gyűjtött metaadatok teljes listáját lásd alább:

**A virtuális gép konfigurációs adatait**
- Virtuális gép megjelenített neve (a vCenter)
- Virtuális gép szoftverleltár elérési útjának (állomás/fürt/mappát a vCenter)
- IP-cím
- MAC-cím
- Operációs rendszer
- Magok, a lemezek, a hálózati adapterek száma
- Memória mérete, a lemez mérete

**A virtuális gép teljesítményadatainak**
- Processzorhasználat
- Memóriahasználat
- A virtuális géphez csatolt lemezek:
  - Lemez sebessége olvasott átviteli sebesség
  - Lemezírás teljesítménye
  - Lemezolvasási műveletek másodpercenkénti száma
  - Az írási műveletek másodpercenkénti száma
- Minden hálózati adapter kapcsolódik a virtuális Gépet:
  - A hálózati
  - Kimenő hálózati

Az ügynök-alapú feltárásához beállítás elérhető, a készülék-alapú feltárásához felett, és segítségével az ügyfelek [Függőségek megjelenítése](how-to-create-group-machine-dependencies.md) a helyszíni virtuális gépek. A függőség ügynökök gyűjtése adatait, például teljes tartománynév, az operációs rendszer, IP címe, MAC-cím, a virtuális gépről a virtuális gép és a bejövő/kimenő TCP-kapcsolatok belül futó folyamatok. Az ügynök-alapú feltárásához nem kötelező, és telepíti az ügynököt, ha nem szeretné, hogy a virtuális gépek függőségeit megjelenítéséhez választhatja.

### <a name="where-is-the-collected-data-stored-and-for-how-long"></a>Hol található az összegyűjtött adatok tárolt és mennyi ideig?

A gyűjtő készülék által összegyűjtött adatokat az Azure-beli hely, az áttelepítés projekt létrehozása közben megadott tárolódik. Az adatok egy Microsoft-előfizetési biztonságos helyen tárolja, és törlődik, amikor a felhasználó nem törli a Azure áttelepítése projekt.

Függőségi képi megjelenítés Ha az ügynökök telepítése virtuális gépeken, a függőség-ügynökök által gyűjtött adatok van tárolva az Egyesült Államokban, a felhasználó az előfizetéshez létrehozott egy OMS-munkaterület. Ezek az adatok törlődnek, miután a felhasználó nem törli az OMS-munkaterület létrehozásának vagy az előfizetésben. [További információk](https://docs.microsoft.com/azure/migrate/concepts-dependency-visualization).

### <a name="how-does-the-collector-communicate-with-the-vcenter-server-and-the-azure-migrate-service"></a>Hogyan nem a gyűjtő kommunikáljanak a vCenter-kiszolgáló és az Azure áttelepítése szolgáltatás?

A gyűjtő készülék csatlakozik a vCenter-kiszolgáló (a 443-as port) a készülék a felhasználó által megadott hitelesítő adatok használatával. Lekéri a vCenter Server használatával VMware PowerCLI a vCenter-kiszolgáló által kezelt virtuális gépek metaadatainak gyűjtéséhez. Összegyűjti az mindkét virtuális gépek (mag, a memória, a lemezek, a hálózati adapter stb.) konfigurációs adatait valamint előzményeinek minden virtuális gép a legutóbbi hónapig a vCenter Server alkalmazásból. Az összegyűjtött metaadatokból küldi el a rendszer az Azure áttelepítése szolgáltatáshoz (interneten keresztül https) értékelésére. [További információ](concepts-collector.md)

### <a name="is-the-data-encrypted-at-rest-and-while-in-transit"></a>Titkosítja az adatokat nyugalmi és az átvitel során?

Igen, az összegyűjtött adatok titkosítása aktívan és az átvitel során. A metaadatokat, a készülék által gyűjtött van biztonságos módon küldje a Azure áttelepítése szolgáltatás interneten keresztül https használatával. Az összegyűjtött metaadatokból tárolódik [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest) és a [Azure blob storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) egy Microsoft előfizetésben és inaktív.

A függőségi ügynök által gyűjtött adatokat is a titkosított átvitel (biztonságos https-csatorna), és a felhasználó az előfizetéshez a Naplóelemzési munkaterület tárolja. Azt is titkosítása.

### <a name="how-can-i-discover-a-multi-tenant-environment-in-azure-migrate"></a>Hogyan képes felderíteni egy több-bérlős környezet az Azure át?

Ha a bérlők által megosztott környezet, és nem szeretné, hogy a másik bérlői előfizetéshez egy bérlő virtuális gépeinek felderítése, a gyűjtő készülék hatókör mezője használhatja a felderítés hatókörének. A bérlők osztja gazdagépeken, ha csak a virtuális gépek az adott bérlőhöz tartozó csak olvasási jogosultságokkal rendelkező hitelesítő adatok létrehozása, majd ezeket a hitelesítő adatokat használja a gyűjtő készüléknek, és adja meg a hatókörét a gazdagép, a felderítés elvégzéséhez. Azt is megteheti a virtuális gépeket a Mappa1 tenant1 és tenant2 is létrehozhat mappákat a vCenter Server (Tételezzük fel a tenant1 mappa1 és a tenant2 mappa2), a megosztott gazdagép áthelyezi mappa2 és majd hatókörének megfelelően a gyűjtő a felderítések Megadja a megfelelő mappát.

### <a name="how-many-virtual-machines-can-be-discovered-in-a-single-migration-project"></a>Hány virtuális gépet egyetlen áttelepítési projektben észlelhetők?

Is felderítheti, 1500 virtuális gépek egyetlen áttelepítési projektben. Ha a helyszíni környezetben, több gép [további](how-to-scale-assessment.md) kapcsolatos hogyan felderíthetők az Azure át nagy környezetében.

## <a name="dependency-visualization"></a>Függőségek vizualizációja

### <a name="do-i-need-to-pay-to-use-the-dependency-visualization-feature"></a>Kell fizetnie a függőség a képi megjelenítés szolgáltatással?

Az Azure Migrate díjmentesen érhető el. További tudnivalókat az Azure Migrate díjszabásáról [itt](https://azure.microsoft.com/pricing/details/azure-migrate/) talál.

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>Használhatom meglévő munkaterülethez függőségi a képi megjelenítéshez tartozó?

Az Azure áttelepítése nem támogatja egy meglévő munkaterület használata függőségi a képi megjelenítéshez tartozó, azonban a Microsoft Monitoring Agent (MMA) támogatja a többszörös homing, és lehetővé teszi több munkaterületek adatküldéshez. Ezért ha már rendelkezik az ügynökök üzembe helyezését és konfigurálását a munkaterületet, után kihasználja a MMA ügynök több homing és konfigurálja az Azure áttelepítése munkaterületet (mellett a meglévő munkaterület) is használható legyen. [Itt](https://blogs.technet.microsoft.com/msoms/2016/05/26/oms-log-analytics-agent-multi-homing-support/) több homing MMA-ügynök engedélyezése a blog van.

## <a name="next-steps"></a>További lépések

- Olvassa el a [Azure áttelepítése – áttekintés](migrate-overview.md)
- Megtudhatja, hogyan zajlik [felderítése és értékeléséhez](tutorial-assessment-vmware.md) VMware környezetben

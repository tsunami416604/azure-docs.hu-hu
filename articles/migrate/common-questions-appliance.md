---
title: Az Azure Migrate készülékkel kapcsolatos gyakori kérdések
description: Válaszok az Azure Migrate készülékkel kapcsolatos gyakori kérdésekre
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 3bb066b08447a951665e629da5ebcb75714b9f1e
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425355"
---
# <a name="azure-migrate-appliance-common-questions"></a>Azure Migrate berendezés: gyakori kérdések

Ez a cikk az Azure Migrate berendezéssel kapcsolatos gyakori kérdésekre ad választ. Ha további kérdései vannak, tekintse át a következő cikkeket:

- [Általános kérdések](resources-faq.md) a Azure Migrateról.
- A felderítéssel, az értékeléssel és a függőségi vizualizációval kapcsolatos [kérdések](common-questions-discovery-assessment.md) .
- A kiszolgálók áttelepítésével kapcsolatos [kérdések](common-questions-server-migration.md) .
- A [Azure Migrate fórumon](https://aka.ms/AzureMigrateForum)választ kaphat kérdéseire. 


## <a name="what-is-the-azure-migrate-appliance"></a>Mi a Azure Migrate készülék?

A Azure Migrate készülék egy egyszerűsített berendezés, amelyet a Azure Migrate: Server Assessment Tool a helyszíni kiszolgálók felderítésére és értékelésére használ. A készüléket a Azure Migrate: Server áttelepítési eszköz is használja a helyszíni VMware virtuális gépek ügynök nélküli áttelepítéséhez. 

- A berendezést virtuális gépre vagy fizikai gépre helyezik üzembe.
- A készülék felfedi a helyszíni gépeket, és folyamatosan küldi a gépi metaadatokat és teljesítményadatokat a Azure Migrate.
- A készülék felderítése ügynök nélkül történik. Nincs telepítve a felderített gépekre.

[További](migrate-appliance.md) információ a készülékről.

## <a name="how-does-the-appliance-connect-to-azure"></a>Hogyan kapcsolódik a készülék az Azure-hoz?

A készülék csatlakozhat az interneten keresztül, vagy az Azure ExpressRoute nyilvános/Microsoft-partneri kapcsolaton keresztül.

## <a name="does-appliance-analysis-impact-performance"></a>Befolyásolja a készülék elemzése a teljesítményt?

A Azure Migrate készülék folyamatosan a teljesítményadatokat méri a helyszíni gépeken. Ez a profilkészítés csaknem nem befolyásolta a profilokban szereplő gépeket.

### <a name="can-i-harden-the-appliance-vm"></a>Megerősíthető a készülék virtuális gépe?

Amikor a letöltött sablonnal hozza létre a berendezés virtuális gépet, hozzáadhat összetevőket (például víruskeresőt) a sablonhoz, ha nem hagyja meg a Azure Migrate berendezéshez szükséges kommunikációs és tűzfalszabályok használatát.


## <a name="what-network-connectivity-is-needed"></a>Milyen hálózati kapcsolatra van szükség?

Tekintse át a hálózati kapcsolattal kapcsolatos információkat:
- VMware Assessment a Azure Migrate készülék használatával: [URL-cím](migrate-appliance.md#url-access) és [port](migrate-support-matrix-vmware.md#port-access) hozzáférési követelmények.
- VMware ügynök nélküli áttelepítés a Azure Migrate berendezéssel: [URL-cím](migrate-appliance.md#url-access) és [port](migrate-support-matrix-vmware-migration.md#agentless-ports) hozzáférési követelmények.
- Hyper-V értékelés a Azure Migrate berendezés használatával: [URL-cím](migrate-appliance.md#url-access) és [port](migrate-support-matrix-hyper-v.md#port-access) hozzáférési követelmények.


## <a name="what-data-does-the-appliance-collect"></a>Milyen adatokat gyűjt a készülék?

Tekintse át az összegyűjtött adatokat:

- VMware virtuális gép [teljesítményadatait](migrate-appliance.md#collected-performance-data-vmware) és [metaadatait](migrate-appliance.md#collected-metadata-vmware).
- A Hyper-V virtuális gép [teljesítményadatait](migrate-appliance.md#collected-performance-data-hyper-v) és [metaadatait](migrate-appliance.md#collected-metadata-hyper-v).


## <a name="how-is-data-stored"></a>Hogyan történik az adatok tárolása?

Az Azure Migrate készülék által gyűjtött adatokat az Azure-beli helyen tárolja a rendszer, amelyben létrehozta a Azure Migrate projektet. 

- Az adattárolást a Microsoft-előfizetések biztonságosan tárolják, és törlődnek a Azure Migrate projekt törlésekor.
- Ha [függőségi vizualizációt](concepts-dependency-visualization.md)használ, az összegyűjtött adatokat az Azure-előfizetésben létrehozott Log Analytics munkaterület Egyesült Államok tárolja. Ezek az adatai törlődnek, amikor törli a Log Analytics munkaterületet az előfizetésében.

## <a name="how-much-data-is-uploaded-in-continuous-profiling"></a>Mekkora mennyiségű adatfeltöltés történik a folyamatos profilkészítés során?

A Azure Migrate elküldett adatok mennyisége számos paramétertől függ. Ahhoz, hogy egy Azure Migrate-projekt 10 géppel (mindkettő egy lemezzel és egy hálózati adapterrel) körülbelül 50 MB-ot küldjön naponta. Ez az érték hozzávetőleges, és megváltozik a hálózati adapterek és lemezek adatpontjainak száma alapján. A továbbított adatmennyiség növekedése nem lineáris, ha a gépek, hálózati adapterek vagy lemezek számának növekedése növekszik.

## <a name="is-data-encrypted-at-restin-transit"></a>Titkosítva van-e az adatok a REST/átvitel során?

Igen, mindkettőhöz.

- A metaadatokat a rendszer biztonságosan továbbítja a Azure Migrate szolgáltatásnak az interneten keresztül, HTTPS-kapcsolaton keresztül.
- A metaadatok egy [Azure Cosmos](../cosmos-db/database-encryption-at-rest.md) -adatbázisban, az [Azure Blob Storage](../storage/common/storage-service-encryption.md)-ban, egy Microsoft-előfizetésben tárolódnak. A metaadatok titkosítva vannak a REST-ben.
- A függőségek elemzéséhez használt adatforgalom is titkosítva van (biztonságos HTTPS). Ez az előfizetés egy Log Analytics munkaterületén van tárolva. Emellett a REST-titkosítás is titkosítva van.

## <a name="how-does-the-appliance-connect-to-vcenter-server"></a>Hogyan kapcsolódik a készülék a vCenter Serverhoz?

1. A berendezés a berendezés beállításakor megadott hitelesítő adatok használatával csatlakozik a vCenter Serverhoz (443-es port).
2. A készülék VMware PowerCLI-t használ a vCenter Server lekérdezéséhez, hogy metaadatokat gyűjtsön a vCenter Server által felügyelt virtuális gépekről.
3. A berendezés konfigurációs adatokat gyűjt a virtuális gépekről (magok, memória, lemezek, hálózati adapterek) és az egyes virtuális gépek teljesítménybeli előzményeiről az elmúlt hónapban.
4. Az összegyűjtött metaadatokat a rendszer a (z) kiszolgáló felmérésére (az interneten keresztül HTTPS protokollon keresztül) továbbítja Azure Migrate.

## <a name="can-i-connect-the-appliance-to-multiple-vcenter-servers"></a>Összekapcsolhatom a készüléket több vCenter-kiszolgálóval?

Nem. A készülék és a vCenter Server között egy-az-egyhez típusú hozzárendelés található. Több vCenter Server példányon futó virtuális gépek felderítéséhez több berendezést kell telepítenie.

## <a name="how-many-vms-or-servers-can-i-discover-with-an-appliance"></a>Hány virtuális gépet vagy kiszolgálót tud felderíteni egy berendezéssel?

Akár 10 000 VMware virtuális gép, akár 5 000 Hyper-V virtuális gép, valamint akár 250 fizikai kiszolgáló is felderíthető egyetlen berendezéssel. Ha több géppel rendelkezik a helyszíni környezetben, olvassa el a [Hyper-V](scale-hyper-v-assessment.md), a [VMware](scale-vmware-assessment.md) és a [fizikai](scale-physical-assessment.md) értékelés méretezésével foglalkozó részt.

## <a name="can-i-delete-an-appliance"></a>Törölhetek-e készüléket?

A készüléknek a projektből való törlése jelenleg nem támogatott.

- A készülék törlésének egyetlen módja a berendezéshez társított Azure Migrate projektet tartalmazó erőforráscsoport törlése.
- Az erőforráscsoport törlése azonban törli a többi regisztrált készüléket, a felderített leltárt, az értékeléseket és az erőforráscsoport projekthez társított összes többi Azure-összetevőt is.


## <a name="can-i-use-the-appliance-with-a-different-subscriptionproject"></a>Használhatom a készüléket egy másik előfizetéssel vagy projekttel?

Miután a készüléket a felderítés elindítására használta, nem konfigurálhatja újra egy másik Azure-előfizetéssel, vagy nem használhatja azt egy másik Azure Migrate projektben. Nem derítheti fel a virtuális gépeket egy másik vCenter Serveron is. Hozzon létre egy új készüléket ezekhez a feladatokhoz.

## <a name="can-i-set-up-the-appliance-on-an-azure-vm"></a>Be lehet állítani a készüléket egy Azure-beli virtuális gépen?
Nem. Ez jelenleg nem támogatott. 

## <a name="can-i-discover-on-an-esxi-host"></a>Felfedezhető egy ESXi-gazdagépen is?
Nem. A VMware virtuális gépek felderítéséhez vCenter Server szükséges.

## <a name="how-do-i-update-the-appliance"></a>Hogyan frissíteni a készüléket?

Alapértelmezés szerint a készülék és a telepített ügynökök automatikusan frissülnek. A készülék 24 óránként ellenőrzi a frissítéseket. Ha a frissítési folyamat meghiúsul, próbálkozzon újra. Az automatikus frissítések csak a készülék és a berendezés ügynökeit frissítik. Az operációs rendszer nem frissül. Az operációs rendszer naprakészen tartása a Microsoft Updates használatával.

## <a name="can-i-check-agent-health"></a>Ellenőrizhető az ügynök állapota?

A portálon lépjen az **ügynök állapota** lapra a kiszolgáló értékelése vagy a kiszolgáló áttelepítése eszközben. Itt megtekintheti a kapcsolat állapotát a készüléken és az Azure-ban található felderítési és értékelési ügynökök között.

## <a name="next-steps"></a>Következő lépések
Olvassa el a [Azure Migrate áttekintést](migrate-services-overview.md).

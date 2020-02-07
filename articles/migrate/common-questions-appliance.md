---
title: Az Azure Migrate készülékkel kapcsolatos gyakori kérdések
description: Válaszok az Azure Migrate készülékkel kapcsolatos gyakori kérdésekre
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: de9583b7983dda904e06ea258ecb0d202644586b
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064699"
---
# <a name="azure-migrate-appliance-common-questions"></a>Azure Migrate berendezés: gyakori kérdések

Ez a cikk az Azure Migrate berendezéssel kapcsolatos gyakori kérdésekre ad választ. Ha további lekérdezésekre van szüksége a cikk elolvasása után, tegye fel őket a [Azure Migrate fórumba](https://aka.ms/AzureMigrateForum). Ha további kérdései vannak, tekintse át a következő cikkeket:

- [Általános kérdések](resources-faq.md) a Azure Migrateról.
- A felderítéssel, az értékeléssel és a függőségi vizualizációval kapcsolatos [kérdések](common-questions-discovery-assessment.md) .
- A kiszolgálók áttelepítésével kapcsolatos [kérdések](common-questions-server-migration.md) .


## <a name="what-is-the-azure-migrate-appliance"></a>Mi a Azure Migrate készülék?

A Azure Migrate készülék egy könnyű berendezés, amelyet a Azure Migrate: a kiszolgáló-értékelési eszköz a helyszíni kiszolgálók felderítésére és értékelésére használ, és a Azure Migrate: a kiszolgáló áttelepítési eszköze a helyszíni VMware virtuális gépek ügynök nélküli áttelepítéséhez. 

A berendezést virtuális gépre vagy fizikai gépre helyezik üzembe. A készülék felfedi a helyszíni gépeket, és folyamatosan küldi a gépi metaadatokat és teljesítményadatokat a Azure Migrate. A készülék felderítése ügynök nélkül történik. Nincs telepítve a felderített gépekre. [További](migrate-appliance.md) információ a készülékről.

## <a name="how-does-the-appliance-connect-to-azure"></a>Hogyan kapcsolódik a készülék az Azure-hoz?

A kapcsolat lehet az interneten keresztül, vagy használhatja az Azure ExpressRoute nyilvános/Microsoft-partnerekkel.

## <a name="does-appliance-analysis-impact-performance"></a>Befolyásolja a készülék elemzése a teljesítményt?

A Azure Migrate berendezés a helyszíni gépeket folyamatosan a virtuális gép teljesítményadatait méri. Ez a profilkészítés szinte semmilyen hatással nincs a Hyper-V/ESXi-gazdagépekre vagy VMware vCenter Serverra.

### <a name="can-i-harden-the-appliance-vm"></a>Megerősíthető a készülék virtuális gépe?

Amikor a letöltött sablonnal hozza létre a berendezés virtuális gépet, hozzáadhat összetevőket (például víruskeresőt) a sablonhoz, ha nem hagyja meg a Azure Migrate berendezéshez szükséges kommunikációs és tűzfalszabályok használatát.


## <a name="what-network-connectivity-is-needed"></a>Milyen hálózati kapcsolatra van szükség?

Tekintse át a következőket:
- VMware Assessment a Azure Migrate készülék használatával: [URL-cím](migrate-appliance.md#url-access) és [port](migrate-support-matrix-vmware.md#port-access) hozzáférési követelmények.
- VMware ügynök nélküli áttelepítés a Azure Migrate berendezéssel: [URL-cím](migrate-appliance.md#url-access) és [port](migrate-support-matrix-vmware-migration.md#agentless-ports) hozzáférési követelmények.
- Hyper-V értékelés a Azure Migrate berendezés használatával: [URL-cím](migrate-appliance.md#url-access) és [port](migrate-support-matrix-hyper-v.md#port-access) hozzáférési követelmények.


## <a name="what-data-does-the-appliance-collect"></a>Milyen adatokat gyűjt a készülék?

Tekintse át az összegyűjtött adatokat:

- VMware virtuális gép [teljesítményadatait](migrate-appliance.md#collected-performance-data-vmware) és [metaadatait](migrate-appliance.md#collected-metadata-vmware).
- A Hyper-V virtuális gép [teljesítményadatait](migrate-appliance.md#collected-performance-data-hyper-v) és [metaadatait](migrate-appliance.md#collected-metadata-hyper-v).


## <a name="how-is-data-stored"></a>Hogyan történik az adatok tárolása?

Az Azure Migrate készülék által gyűjtött adatokat az Azure-helyen tárolja a rendszer, amelyet az áttelepítési projekt létrehozásakor választott ki. 

- Az adattárolást a Microsoft-előfizetések biztonságosan tárolják, és törlődnek a Azure Migrate projekt törlésekor.
- Ha [függőségi vizualizációt](concepts-dependency-visualization.md)használ, az összegyűjtött adatokat az Azure-előfizetésben létrehozott Log Analytics munkaterület Egyesült Államok tárolja. Ezek az adatai törlődnek, amikor törli a Log Analytics munkaterületet az előfizetésében.

## <a name="how-much-data-is-uploaded-in-continuous-profiling"></a>Mekkora mennyiségű adatfeltöltés történik a folyamatos profilkészítés során?

A Azure Migrate elküldett adatok mennyisége számos paramétertől függ. Annak érdekében, hogy a kötet értelmet nyújtson, a 10 gépet tartalmazó Azure Migrate-projektek (amelyek mindegyike egy lemezzel és egy hálózati adapterrel) naponta 50 MB-ot küld. Ez az érték közelítő, és a hálózati adapterek és a lemezek adatpontjainak száma alapján változik. A továbbított adatmennyiség növekedése nem lineáris, ha a gépek, hálózati adapterek vagy lemezek számának növekedése növekszik.

## <a name="is-data-encrypted-at-restin-transit"></a>Titkosítva van-e az adatok a REST/átvitel során?

Igen, mindkettőhöz.

- A metaadatokat a rendszer biztonságosan továbbítja a Azure Migrate szolgáltatásnak az interneten keresztül, HTTPS-kapcsolaton keresztül.
- A metaadatok egy [Azure Cosmos](../cosmos-db/database-encryption-at-rest.md) -adatbázisban, az [Azure Blob Storage](../storage/common/storage-service-encryption.md)-ban, egy Microsoft-előfizetésben tárolódnak. A metaadatok titkosítva vannak a REST-ben.
- A függőségek elemzéséhez használt adatforgalom is titkosítva van (biztonságos HTTPS). Ez az előfizetés egy Log Analytics munkaterületén van tárolva. Emellett a REST-titkosítás is titkosítva van.

## <a name="how-does-the-appliance-connect-to-vcenter-server"></a>Hogyan kapcsolódik a készülék a vCenter Serverhoz?

1. A berendezés a berendezés beállításakor megadott hitelesítő adatok használatával csatlakozik a vCenter Serverhoz (443-es port).
2. A készülék VMware PowerCLI-t használ a vCenter Server lekérdezéséhez, hogy metaadatokat gyűjtsön a vCenter Server által felügyelt virtuális gépekről.
3. A berendezés konfigurációs adatokat gyűjt a virtuális gépekről (magok, memória, lemezek, hálózati adapterek) és az egyes virtuális gépek teljesítménybeli előzményeiről az elmúlt hónapban.
4. Az összegyűjtött metaadatok a Azure Migrate: a kiszolgáló értékelése (az interneten keresztül HTTPS használatával) az értékeléshez.

## <a name="can-i-connect-the-appliance-to-multiple-vcenter-servers"></a>Összekapcsolhatom a készüléket több vCenter-kiszolgálóval?

Nem. A készülék és a vCenter Server között egy-az-egyhez típusú hozzárendelés található. Több vCenter Server példányon futó virtuális gépek felderítéséhez több berendezést kell telepítenie.

### <a name="how-many-vms-or-servers-can-i-discover-with-an-appliance"></a>Hány virtuális gépet vagy kiszolgálót tud felderíteni egy berendezéssel?

Akár 10 000 VMware virtuális gépet, akár 5 000 Hyper-V virtuális gépeket és akár 250 kiszolgálót is felfedezhet egyetlen berendezéssel. Ha több géppel rendelkezik a helyszíni környezetben, olvassa el a [Hyper-V](scale-hyper-v-assessment.md), a [VMware](scale-vmware-assessment.md) és a [fizikai](scale-physical-assessment.md) értékelés méretezésével foglalkozó részt.

## <a name="can-i-delete-an-appliance"></a>Törölhetek-e készüléket?

A berendezés jelenleg a projektből való törlése nem támogatott.

- A készülék törlésének egyetlen módja a berendezéshez társított Azure Migrate projektet tartalmazó erőforráscsoport törlése.
- Az erőforráscsoport törlése azonban törli a többi regisztrált készüléket, a felderített leltárt, az értékeléseket és az erőforráscsoport projekthez társított összes többi Azure-összetevőt is.


## <a name="can-i-use-the-appliance-with-a-different-subscriptionproject"></a>Használhatom a készüléket egy másik előfizetéssel vagy projekttel?

Miután a készüléket a felderítés elindítására használta, nem lehet újrakonfigurálni egy másik Azure-előfizetéssel, vagy egy másik Azure Migrate projektben. Nem derítheti fel a virtuális gépeket egy másik vCenter Serveron is. Hozzon létre egy friss berendezést ezekhez a feladatokhoz.

## <a name="can-i-set-up-the-appliance-on-an-azure-vm"></a>Be lehet állítani a készüléket egy Azure-beli virtuális gépen?
Jelenleg nem támogatott. 

## <a name="can-i-discover-on-an-esxi-host"></a>Felfedezhető egy ESXi-gazdagépen is?
A VMware virtuális gépek felderítéséhez nincs szükség vCenter Serverra.

## <a name="how-do-i-update-the-appliance"></a>Hogyan frissíteni a készüléket?

Alapértelmezés szerint a készülék és a telepített ügynökök automatikusan frissülnek. A készülék 24 óránként ellenőrzi a frissítéseket. Ha a frissítési folyamat során hibák merülnek fel, újrapróbálkozási folyamatra van lehetőség. Az automatikus frissítések csak a készülék és a berendezés ügynökeit frissítik. Az operációs rendszer nem frissül. Az operációs rendszer naprakészen tartása a Microsoft Updates használatával.

## <a name="can-i-check-agent-health"></a>Ellenőrizhető az ügynök állapota?

A portálon lépjen az **ügynök állapota** lapra a kiszolgáló értékelése vagy a kiszolgáló áttelepítése eszközben. Itt megtekintheti a kapcsolat állapotát a készüléken és az Azure-ban található felderítési és értékelési ügynökök között.

## <a name="next-steps"></a>Következő lépések
Olvassa el a [Azure Migrate áttekintést](migrate-services-overview.md).

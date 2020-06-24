---
title: Azure Migrate készülék – gyakori kérdések
description: Választ kaphat a Azure Migrate berendezéssel kapcsolatos gyakori kérdésekre.
ms.topic: conceptual
ms.date: 06/03/2020
ms.openlocfilehash: afdede99fa4485ba7e5494e844d795c2d5d3deee
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2020
ms.locfileid: "85078975"
---
# <a name="azure-migrate-appliance-common-questions"></a>Azure Migrate berendezés: gyakori kérdések

Ez a cikk az Azure Migrate berendezéssel kapcsolatos gyakori kérdésekre ad választ. Ha további kérdései vannak, tekintse meg a következő erőforrásokat:

- [Általános kérdések](resources-faq.md) a Azure Migrate
- A [felderítéssel, az értékeléssel és a függőségi vizualizációval](common-questions-discovery-assessment.md) kapcsolatos kérdések
- A [kiszolgálók áttelepítésével](common-questions-server-migration.md) kapcsolatos kérdések
- A [Azure Migrate fórumban](https://aka.ms/AzureMigrateForum) választ kaphat kérdéseire

## <a name="what-is-the-azure-migrate-appliance"></a>Mi a Azure Migrate készülék?

A Azure Migrate berendezés egy könnyű berendezés, amelyet a Azure Migrate: a kiszolgáló-értékelési eszköz a helyszíni kiszolgálók felderítésére és értékelésére használ. A Azure Migrate: a kiszolgáló áttelepítési eszköze a berendezést is használja a helyszíni VMware virtuális gépek ügynök nélküli áttelepítéséhez.

További információ az Azure Migrate készülékről:

- A berendezést virtuális gépre vagy fizikai gépre helyezik üzembe.
- A készülék felfedi a helyszíni gépeket, és folyamatosan küldi a gépi metaadatokat és teljesítményadatokat a Azure Migrate.
- A készülék felderítése ügynök nélkül történik. Nincs telepítve a felderített gépekre.

[További](migrate-appliance.md) információ a készülékről.

## <a name="how-can-i-deploy-the-appliance"></a>Hogyan helyezhetem üzembe a készüléket?

A berendezés a következőképpen telepíthető:

- Sablon használata VMware virtuális gépekhez és Hyper-V virtuális gépekhez (a VMware-hez vagy a virtuális merevlemezhez tartozó PETESEJT-sablon a Hyper-V-hez).
- Ha nem szeretne sablont használni, vagy Ön Azure Government, akkor PowerShell-parancsfájl használatával telepítheti a készüléket a VMware vagy a Hyper-V rendszerre.
- Fizikai kiszolgálók esetében a készüléket mindig egy parancsfájl használatával helyezheti üzembe.


## <a name="how-does-the-appliance-connect-to-azure"></a>Hogyan kapcsolódik a készülék az Azure-hoz?

A készülék az interneten keresztül vagy az Azure ExpressRoute nyilvános/Microsoft-partneri kapcsolattal való használatával is csatlakozhat.

## <a name="does-appliance-analysis-affect-performance"></a>Befolyásolja a berendezések elemzése a teljesítményt?

A Azure Migrate készülék folyamatosan a teljesítményadatokat méri a helyszíni gépeken. Ez a profilkészítés szinte nem befolyásolja a profilkészítésen átadott gépek teljesítményét.

## <a name="can-i-harden-the-appliance-vm"></a>Megerősíthető a készülék virtuális gépe?

Ha a letöltött sablonnal hozza létre a berendezés virtuális gépet, hozzáadhat összetevőket (például víruskeresőt) a sablonhoz, ha a Azure Migrate berendezéshez szükséges kommunikációs és tűzfalszabályok nem állnak helyre.

## <a name="what-network-connectivity-is-required"></a>Milyen hálózati kapcsolat szükséges?


A készüléknek hozzá kell férnie az Azure URL-címeihez. [Tekintse át](migrate-appliance.md#url-access) az URL-listát.

## <a name="what-data-does-the-appliance-collect"></a>Milyen adatokat gyűjt a készülék?

A következő cikkekben talál információt arról, hogy az Azure Migrate készülék milyen adatokat gyűjt a virtuális gépeken:

- **VMWare virtuális gép**: [tekintse át](migrate-appliance.md#collected-data---vmware) az összegyűjtött adatokat. [
- **Hyper-V virtuális gép**: [tekintse át](migrate-appliance.md#collected-data---hyper-v) az összegyűjtött adatokat.

## <a name="how-is-data-stored"></a>Hogyan történik az adatok tárolása?

Az Azure Migrate készülék által gyűjtött adatokat az Azure-beli helyen tárolja a rendszer, ahol létrehozta a Azure Migrate projektet.

További információ az adatok tárolásáról:

- Az összegyűjtött adatokat a rendszer biztonságosan tárolja a Microsoft-előfizetések CosmosDB. Az Azure Migrate-projekt törlésekor a rendszer törli az adatvesztést. A tárterületet Azure Migrate kezeli. Nem választhat külön Storage-fiókot az összegyűjtött adatokhoz.
- Ha [függőségi vizualizációt](concepts-dependency-visualization.md)használ, a gyűjtött adatokat az Azure-előfizetésében létrehozott Azure log Analytics-munkaterület Egyesült Államok tárolja. Az adatai törlődnek, amikor törli a Log Analytics munkaterületet az előfizetésében.

## <a name="how-much-data-is-uploaded-during-continuous-profiling"></a>Mekkora mennyiségű adatfeltöltés történik a folyamatos profilkészítés során?

A Azure Migrate elküldett adatok mennyisége több paramétertől függ. Például egy olyan Azure Migrate-projekt, amely 10 géppel rendelkezik (mindkettő egy lemezzel és egy hálózati adapterrel) körülbelül 50 MB adatmennyiséget küld naponta. Ez az érték közelítő; a tényleges érték a lemezek és hálózati adapterek adatpontjainak számától függ. Ha a gépek, lemezek vagy hálózati adapterek száma növekszik, az elküldett adatmennyiség növekedése nem lineáris.

## <a name="is-data-encrypted-at-rest-and-in-transit"></a>Titkosítva van-e az adatok a nyugalmi és a átvitel során?

Igen, mindkettő esetén:

- A metaadatok biztonságos küldése a Azure Migrate szolgáltatásnak az interneten keresztül HTTPS használatával történik.
- A metaadatok egy [Azure Cosmos](../cosmos-db/database-encryption-at-rest.md) -adatbázisban és egy Microsoft-előfizetésben található [Azure Blob Storage](../storage/common/storage-service-encryption.md) -ban tárolódnak. A metaadatok titkosítva vannak a tárolás során.
- A függőségek elemzésének adatai is titkosítva vannak az átvitelben (biztonságos HTTPS-kapcsolaton keresztül). Ez az előfizetés egy Log Analytics munkaterületén van tárolva. Az adatok inaktív állapotban vannak titkosítva a függőségek elemzéséhez.

## <a name="how-does-the-appliance-connect-to-vcenter-server"></a>Hogyan kapcsolódik a készülék a vCenter Serverhoz?

Ezek a lépések azt írják le, hogyan kapcsolódik a készülék a VMware vCenter Serverhoz:

1. A berendezés a berendezés beállításakor megadott hitelesítő adatok használatával csatlakozik a vCenter Serverhoz (443-es port).
2. A készülék VMware PowerCLI használatával kérdezi le vCenter Server a vCenter Server által felügyelt virtuális gépekkel kapcsolatos metaadatok gyűjtését.
3. A berendezés konfigurációs adatokat gyűjt a virtuális gépekről (magok, memória, lemezek, hálózati adapterek) és az egyes virtuális gépek teljesítménybeli előzményeiről az elmúlt hónapban.
4. Az összegyűjtött metaadatokat a rendszer a Azure Migrate: kiszolgáló-értékelési eszközre (az interneten keresztül HTTPS protokollon keresztül) továbbítja az értékeléshez.

## <a name="can-the-azure-migrate-appliance-connect-to-multiple-vcenter-servers"></a>Csatlakozhat a Azure Migrate készülék több vCenter-kiszolgálóhoz?

Nem. Egy [Azure Migrate berendezés](migrate-appliance.md) és vCenter Server között egy-az-egyhez típusú hozzárendelés található. Több vCenter Server példányon futó virtuális gépek felderítéséhez több berendezést kell telepítenie. 

## <a name="can-an-azure-migrate-project-have-multiple-appliances"></a>Rendelkezhet Azure Migrate-projekttel több készülékkel?
Egy projekthez több készülék is csatlakoztatható. Egy berendezés azonban csak egy projekthez társítható. 

## <a name="can-the-azure-migrate-appliancereplication-appliance-connect-to-the-same-vcenter"></a>Csatlakozhat a Azure Migrate készülék/replikációs berendezés ugyanahhoz a vCenter?
Igen. Felveheti mind a Azure Migrate készüléket (az értékeléshez és az ügynök nélküli VMware áttelepítéshez), mind a replikációs berendezést (amely a VMware virtuális gépek ügynök-alapú áttelepítésére szolgál) ugyanarra a vCenter-kiszolgálóra.


## <a name="how-many-vms-or-servers-can-i-discover-with-an-appliance"></a>Hány virtuális gépet vagy kiszolgálót tud felderíteni egy berendezéssel?

Akár 10 000 VMware virtuális gép, akár 5 000 Hyper-V virtuális gép, valamint akár 1000 fizikai kiszolgáló is felderíthető egyetlen berendezéssel. Ha több géppel rendelkezik a helyszíni környezetben, olvassa el a [Hyper-V felmérésének](scale-hyper-v-assessment.md)méretezését, [a VMware-értékelés méretezését](scale-vmware-assessment.md)és [a fizikai kiszolgáló értékelésének](scale-physical-assessment.md)méretezését ismertető részt.

## <a name="can-i-delete-an-appliance"></a>Törölhetek-e készüléket?

A készüléknek a projektből való törlése jelenleg nem támogatott.

A készülék törlésének egyetlen módja, ha törli a berendezéshez társított Azure Migrate projektet tartalmazó erőforráscsoportot.

Az erőforráscsoport törlése azonban törli az egyéb regisztrált berendezéseket, a felderített leltárt, az értékeléseket és a projekthez társított erőforráscsoport összes többi Azure-összetevőjét is.

## <a name="can-i-use-the-appliance-with-a-different-subscription-or-project"></a>Használhatom a készüléket egy másik előfizetéssel vagy projekttel?

Miután a berendezés használatával kezdeményezte a felderítést, nem konfigurálhatja újra a készüléket egy másik Azure-előfizetéssel való használatra, és nem használhatja azt egy másik Azure Migrate projektben. Nem derítheti fel a virtuális gépeket a vCenter Server egy másik példányán is. Hozzon létre egy új készüléket ezekhez a feladatokhoz.

## <a name="can-i-set-up-the-appliance-on-an-azure-vm"></a>Be lehet állítani a készüléket egy Azure-beli virtuális gépen?

Nem. Ez a lehetőség jelenleg nem támogatott. 

## <a name="can-i-discover-on-an-esxi-host"></a>Felfedezhető egy ESXi-gazdagépen is?

Nem. A VMware virtuális gépek felderítéséhez vCenter Server szükséges.

## <a name="how-do-i-update-the-appliance"></a>Hogyan frissíteni a készüléket?

Alapértelmezés szerint a készülék és a telepített ügynökök automatikusan frissülnek. A készülék 24 óránként ellenőrzi a frissítéseket. A sikertelen frissítések újrapróbálkoznak. 

Ezek az automatikus frissítések csak a készülék és a berendezés ügynökeit frissítik. Azure Migrate automatikus frissítések nem frissítik az operációs rendszert. Az operációs rendszer naprakészen tartása Windows-frissítések használatával.

## <a name="can-i-check-agent-health"></a>Ellenőrizhető az ügynök állapota?

Igen. A portálon nyissa meg az Azure Migrate: Server Assessment vagy Azure Migrate: Server áttelepítési eszköz **ügynök állapota** lapját. Itt megtekintheti a kapcsolat állapotát az Azure és a készüléken található felderítési és értékelési ügynökök között.

## <a name="next-steps"></a>További lépések

Olvassa el a [Azure Migrate áttekintést](migrate-services-overview.md).

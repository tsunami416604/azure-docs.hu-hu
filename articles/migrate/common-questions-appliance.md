---
title: Az Azure Migrate készülék – gyakori kérdések
description: Válaszok az Azure Migrate készülékkel kapcsolatos gyakori kérdésekre.
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: a08423637f255b973f52616f53172ef6e48d0804
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437612"
---
# <a name="azure-migrate-appliance-common-questions"></a>Azure Migrate készülék: Gyakori kérdések

Ez a cikk az Azure Migrate-berendezéssel kapcsolatos gyakori kérdésekre ad választ. Ha egyéb kérdése van, ellenőrizze az alábbi forrásokat:

- [Általános kérdések az](resources-faq.md) Azure Áttelepítésével kapcsolatban
- Kérdések [a felderítéssel, értékeléssel és függőségi megjelenítéssel kapcsolatban](common-questions-discovery-assessment.md)
- Kérdések a [kiszolgáló áttelepítésével](common-questions-server-migration.md) kapcsolatban
- Kérdések megválaszolása az [Azure Áttelepítésfórumában](https://aka.ms/AzureMigrateForum)

## <a name="what-is-the-azure-migrate-appliance"></a>Mi az Azure Migrate készülék?

Az Azure Migrate készülék egy könnyű berendezés, amely az Azure Migrate: Server Assessment eszköz segítségével felderítheti és felmérte a helyszíni kiszolgálók. Az Azure Áttelepítés: Kiszolgálóáttelepítési eszköz is használja a készüléket ügynök nélküli áttelepítése a helyszíni VMware virtuális gépek.

További információ az Azure Migrate készülékről:

- A készülék üzembe helyezése a helyszínen, mint egy virtuális gép vagy fizikai gép.
- A készülék felderíti a helyszíni gépeket, és folyamatosan elküldi a gép metaadatait és teljesítményadatait az Azure Migrate szolgáltatásnak.
- A készülék felderítése ügynök nélküli. Semmi sincs telepítve a felfedezett gépeken.

[További információ](migrate-appliance.md) a készülékről.

## <a name="how-does-the-appliance-connect-to-azure"></a>Hogyan kapcsolódik a készülék az Azure-hoz?

A készülék az interneten keresztül vagy az Azure ExpressRoute nyilvános/Microsoft társviszony-létesítéssel való használatával is csatlakozhat.

## <a name="does-appliance-analysis-affect-performance"></a>Befolyásolja a készülékelemzés a teljesítményt?

Az Azure Migrate berendezés profilok helyszíni gépek folyamatosan a teljesítményadatok mérésére. Ez a profilkészítés szinte nincs teljesítményhatással a profilozott gépek.

## <a name="can-i-harden-the-appliance-vm"></a>Megkeményíthetem a készülék virtuális gépét?

Amikor a letöltött sablon használatával hozza létre a virtuális gép a készüléket, hozzáadhat összetevőket (például víruskereső) a sablonhoz, ha hagyja a helyén a kommunikációs és tűzfalszabályokat, amelyek szükségesek az Azure Migrate készülék.

## <a name="what-network-connectivity-is-required"></a>Milyen hálózati kapcsolatra van szükség?

Az Azure Migrate-berendezés hálózati kapcsolati követelményeiről az alábbi cikkekben talál tájékoztatást:

- **VMware értékelése**: [URL-hozzáférés](migrate-appliance.md#url-access) és [porthozzáférés](migrate-support-matrix-vmware.md#port-access)
- **VMware ügynök nélküli áttelepítés:** [URL-hozzáférés](migrate-appliance.md#url-access) és [porthozzáférés](migrate-support-matrix-vmware-migration.md#agentless-ports)
- **Hyper-V felmérés:** [URL-hozzáférés](migrate-appliance.md#url-access) és [porthozzáférés](migrate-support-matrix-hyper-v.md#port-access)

## <a name="what-data-does-the-appliance-collect"></a>Milyen adatokat gyűjt a készülék?

Az alábbi cikkekben az Azure Migrate-berendezés virtuális gépeken gyűjtött adatairól az alábbi cikkekben talál tájékoztatást:

- **VMware VM**: Az összegyűjtött adatok [áttekintése.](migrate-appliance.md#collected-data---vmware) [
- **Hyper-V VM**: Az összegyűjtött adatok [áttekintése.](migrate-appliance.md#collected-data---hyper-v)

## <a name="how-is-data-stored"></a>Hogyan történik az adatok tárolása?

Az Azure Migrate appliance által gyűjtött adatok at az Azure-ban tárolják, ahol létrehozta az Azure Migrate projektet.

Az adatok tárolásáról az alábbiakat olvashatja:

- Az összegyűjtött adatok biztonságosan tárolják a CosmosDB egy Microsoft-előfizetésben. Az adatok törlődnek, ha törli az Azure Migrate projektet. A tárolást az Azure Migrate kezeli. Az összegyűjtött adatokhoz nem választhat kifejezetten tárfiókot.
- Ha [függőségi vizualizációt](concepts-dependency-visualization.md)használ, az összegyűjtött adatokat az Egyesült Államokban tárolja az Azure Log Analytics-munkaterületen, amelyet az Azure-előfizetésében hoz létre. Az adatok törlődnek, ha törli a Log Analytics munkaterületet az előfizetésben.

## <a name="how-much-data-is-uploaded-during-continuous-profiling"></a>Mennyi adatot tölt fel a rendszer a folyamatos profilalkotás során?

Az Azure Migrate szolgáltatásnak küldött adatok mennyisége több paramétertől függ. Például egy Azure Migrate projekt, amely 10 gép (mindegyik egy lemezzel és egy hálózati adapterrel) naponta körülbelül 50 MB adatot küld. Ez az érték hozzávetőleges; a tényleges érték a lemezek és a hálózati adapterek adatpontjainak számától függ. Ha a gépek, lemezek vagy hálózati adapterek száma nő, az elküldött adatok növekedése nem lineáris.

## <a name="is-data-encrypted-at-rest-and-in-transit"></a>Az adatok titkosítva vannak az inaktív és az átvitel során?

Igen, mindkét esetben:

- A metaadatok biztonságosan elküldésre kerülnek az Azure Migrate szolgáltatásba az interneten keresztül HTTPS-kapcsolaton keresztül.
- A metaadatokat egy [Azure Cosmos-adatbázisban](../cosmos-db/database-encryption-at-rest.md) és egy [Microsoft-előfizetésben tárolt Azure Blob-tárban](../storage/common/storage-service-encryption.md) tárolják. A metaadatok at in-edik tároló.
- A függőségelemzéshez szükséges adatok is titkosítva vannak az átvitel során (biztonságos HTTPS protokollon keresztül). Az előfizetésben egy Log Analytics-munkaterület tárolja. Az adatok in-t in-t titkosítva vannak a függőség elemzéséhez.

## <a name="how-does-the-appliance-connect-to-vcenter-server"></a>Hogyan kapcsolódik a készülék a vCenter Server kiszolgálóhoz?

Az alábbi lépések azt ismertetik, hogy a készülék hogyan csatlakozik a VMware vCenter Server kiszolgálóhoz:

1. A készülék a vCenter Server kiszolgálóhoz (443-as port) csatlakozik a készülék beállításakor megadott hitelesítő adatok alapján.
2. A készülék a VMware PowerCLI segítségével kérdezi le a vCenter Server rendszert a vCenter Server által felügyelt virtuális gépek metaadatainak gyűjtésére.
3. A készülék konfigurációs adatokat gyűjt a virtuális gépekről (magok, memória, lemezek, hálózati adapterek) és az egyes virtuális gépek teljesítményelőzményeiről az elmúlt hónapban.
4. Az összegyűjtött metaadatok at az Azure Migrate: Server Assessment eszköz (az interneten keresztül HTTPS-kapcsolaton keresztül) értékelésre.

## <a name="can-the-azure-migrate-appliance-connect-to-multiple-vcenter-servers"></a>Az Azure Migrate berendezés több vCenter-kiszolgálóhoz is csatlakozhat?

Nem. Az [Azure Migrate-berendezés](migrate-appliance.md) és a vCenter-kiszolgáló között egy-az-egyhez hozzárendelés van. Ha több vCenter Server-példányon is fel szeretné deríteni a virtuális gépeket, több berendezést kell telepítenie. 

## <a name="can-an-azure-migrate-project-have-multiple-appliances"></a>Egy Azure Migrate projekt több készüléket is rendelkezhet?
Egy projekthez több készülék is csatlakoztatható. Egy készülék azonban csak egy projekthez társítható. 

## <a name="can-the-azure-migrate-appliancereplication-appliance-connect-to-the-same-vcenter"></a>Az Azure Migrate appliance/Replication appliance csatlakozhat ugyanahhoz a vCenterhez?
Igen. Hozzáadhatja az Azure Migrate-készüléket (értékeléshez és ügynök nélküli VMware-áttelepítéshez), és a replikációs berendezést (a VMware virtuális gépek ügynökalapú áttelepítéséhez) ugyanarra a vCenter-kiszolgálóra.


## <a name="how-many-vms-or-servers-can-i-discover-with-an-appliance"></a>Hány virtuális gépet vagy kiszolgálót fedezhetek fel egy készülékkel?

Legfeljebb 10 000 VMware virtuális gépet, akár 5000 Hyper-V virtuális gépet és akár 250 fizikai kiszolgálót fedezhet fel egyetlen készülékkel. Ha több gép van a helyszíni környezetben, olvassa el [a Hyper-V-felmérés méretezéséről,](scale-hyper-v-assessment.md) [a VMware-felmérés méretezéséről](scale-vmware-assessment.md)és [a fizikai kiszolgálói értékelés méretezéséről.](scale-physical-assessment.md)

## <a name="can-i-delete-an-appliance"></a>Törölhetek egy készüléket?

Jelenleg egy készülék törlése a projektből nem támogatott.

A készülék törlésének egyetlen módja az Azure Migrate projektet tartalmazó erőforráscsoport törlése.

Azonban az erőforráscsoport törlése is törli a többi regisztrált készülékek, a felderített leltár, értékelések és az összes többi Azure-összetevők az erőforráscsoportban, amelyek a projekthez társított.

## <a name="can-i-use-the-appliance-with-a-different-subscription-or-project"></a>Használhatom a készüléket másik előfizetéssel vagy projekttel?

Miután a készülék használatával felderítést kezdeményez, nem konfigurálhatja újra a készüléket egy másik Azure-előfizetéssel való használatra, és nem használhatja azt egy másik Azure Migrate projektben. A virtuális gépeket a vCenter-kiszolgáló egy másik példányán sem fedezheti fel. Új készülék beállítása ezekhez a feladatokhoz.

## <a name="can-i-set-up-the-appliance-on-an-azure-vm"></a>Beállíthatja a készüléket egy Azure-beli virtuális gép?

Nem. Jelenleg ez a beállítás nem támogatott. 

## <a name="can-i-discover-on-an-esxi-host"></a>Felfedezhetem egy ESXi host?

Nem. A VMware virtuális gépek felderítéséhez vCenter Server szolgáltatásnak kell rendelkeznie.

## <a name="how-do-i-update-the-appliance"></a>Hogyan tudom frissíteni a készüléket?

Alapértelmezés szerint a készülék és a telepített ügynökök automatikusan frissülnek. A készülék 24 óránként ellenőrzi a frissítéseket. A sikertelen frissítéseket a rendszer újra megkíséreli. 

Ezek az automatikus frissítések csak a készüléket és a készülékügynököket frissítik. Az Azure Migrate automatikus frissítésnem frissíti az operációs rendszert. Az operációs rendszer naprakészen tartása érdekében a Windows-frissítések segítségével tartsa naprakészen.

## <a name="can-i-check-agent-health"></a>Ellenőrizhetem az ügynök állapotát?

Igen. A portálon nyissa meg az Azure Áttelepítés: Kiszolgálófelmérés vagy az Azure Áttelepítés: Kiszolgálóáttelepítés eszköz **ügynök állapotlapját.** Itt ellenőrizheti az Azure és a felderítési és assessment ügynökök a készüléken közötti kapcsolat állapotát.

## <a name="next-steps"></a>További lépések

Olvassa el az [Azure Migrate áttekintését.](migrate-services-overview.md)

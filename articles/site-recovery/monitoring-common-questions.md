---
title: Gyakori kérdések az Azure Site Recovery figyelésével kapcsolatban
description: Válaszok az Azure Site Recovery figyelésével kapcsolatos gyakori kérdésekre a beépített figyelés és az Azure Monitor (Log Analytics) használatával
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.date: 07/31/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: c1d30a9cdd2cd6ca288edd609a2e2e7bee9174d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68718261"
---
# <a name="common-questions-about-site-recovery-monitoring"></a>Gyakori kérdések a webhely-helyreállítási figyeléssel kapcsolatban

Ez a cikk választ ad az Azure [Site Recovery](site-recovery-overview.md)figyelésével, a beépített site recovery figyeléssel és az Azure Monitor (Log Analytics) használatával kapcsolatos gyakori kérdésekre.

## <a name="general"></a>Általános kérdések

### <a name="how-is-the-rpo-value-logged-different-from-the-latest-available-recovery-point"></a>Miben különbözik a naplózott RPO-érték a legújabb elérhető helyreállítási ponttól?

A Site Recovery többlépéses, aszinkron folyamatot használ a gépek Azure-ba replikálásához.

- A replikáció utolsó előtti lépésében a rendszer a számítógép legutóbbi módosításait és a metaadatokat egy napló-/gyorsítótár-tárfiókba másolja.
- Ezek a módosítások, valamint a címke, amely azonosítja a helyreállítható pontot, a célrégióban lévő tárfiókba/felügyelt lemezre kerül.
- A Site Recovery most már létrehozhat egy helyreállítható pontot a gép számára.
- Ezen a ponton az RPO teljesült a tárfiókba eddig feltöltött módosításokhoz. Más szóval, a gép RPO ezen a ponton egyenlő az idő, amely eltelt az időbélyeg megfelelő helyreállítható pont.
- Most site recovery kiválasztja a feltöltött adatokat a tárfiókból, és alkalmazza azokat a replikáni létrehozott lemezek a géphez.
- A Site Recovery ezután létrehoz egy helyreállítási pontot, és ezt a pontot feladatátvételkor elérhetővé teszi a helyreállításhoz.
- Így a legújabb rendelkezésre álló helyreállítási pont jelzi a már feldolgozott és a replikalemezekre alkalmazott legújabb helyreállítási pontnak megfelelő időbélyeget.


A replikáló forrásgépen vagy a helyszíni infrastruktúra-kiszolgálókon a helytelen rendszeridő megdönti a számított RPO-értéket. A pontos RPO-jelentés érdekében győződjön meg arról, hogy a rendszeróra minden kiszolgálón és gépen pontos.



## <a name="inbuilt-site-recovery-logging"></a>Beépített hely helyreállítási naplózása


### <a name="why-is-the-vm-count-in-the-vault-infrastructure-view-different-from-the-total-count-shown-in-replicated-items"></a>Miért különbözik a virtuális gépek száma a tároló infrastruktúra nézetében a replikált elemekben látható teljes számtól?

A tároló infrastruktúra nézet replikációs forgatókönyvek hatóköre. A nézet számában csak az aktuálisan kiválasztott replikációs forgatókönyvben lévő gépek szerepelnek. Emellett csak azokat a virtuális gépeket számoljuk, amelyek az Azure-ba replikálásra vannak konfigurálva. A feladatátvételi feladatátvételi feladatátvételi feladatátvételi feladatokkal, vagy a helyszíni helyre replikáló gépek nem számítanak a nézetben.

### <a name="why-is-the-count-of-replicated-items-in-essentials-different-from-the-total-count-of-replicated-items-on-the-dashboard"></a>Miért különbözik az Essentials replikált elemek száma az irányítópulton lévő replikált elemek teljes számától?

Csak azokat a gépeket tartalmazza, amelyek kezdeti replikációja befejeződött, és amelyek száma az Essentials-ban látható. A replikált elemek összesen tartalmazza az összes gép a tárolóban, beleértve azokat is, amelyek kezdeti replikáció jelenleg folyamatban van.

## <a name="azure-monitor-logging"></a>Azure Monitor-naplózás


### <a name="how-often-does-site-recovery-send-diagnostic-logs-to-azure-monitor-log"></a>Milyen gyakran küld a Site Recovery diagnosztikai naplókat az Azure Monitor Log? 

- Az AzureSiteRecoveryReplicationStats és az AzureSiteRecoveryRecoveryPoints 15 percenként kerül elküldésre.  
- Az AzureSiteRecoveryReplicationDataUploadRate és az AzureSiteRecoveryProtectedDiskDataChurn ötpercenként kerül elküldésre. 
- Az AzureSiteRecoveryJobs a feladat eseményindítójára és befejezésére kerül.
- Az AzureSiteRecoveryEvents esemény létrehozásakor kerül elküldésre. 
- Az AzureSiteRecoveryReplicatedItems minden környezeti változás esetén elküldésre kerül. Az adatfrissítési idő általában 15 perccel a módosítás után történik. 

### <a name="how-long-is-data-kept-in-azure-monitor-logs"></a>Mennyi ideig tárolják az adatokat az Azure Monitor-naplókban? 

Alapértelmezés szerint a megőrzés 31 napig tart. Növelheti az időszakot a **Használati és becsült költség** szakaszban a Log Analytics munkaterületen. Kattintson **az Adatmegőrzés gombra,** és válassza ki a tartományt.

### <a name="whats-the-size-of-the-diagnostic-logs"></a>Mekkora a diagnosztikai naplók mérete? 

A napló mérete általában 15–20 KB. 


## <a name="next-steps"></a>További lépések

Megtudhatja, hogy miként figyelheti a [Site Recovery beépített figyelésével](site-recovery-monitor-and-troubleshoot.md)vagy az [Azure Monitor lal.](monitor-log-analytics.md)



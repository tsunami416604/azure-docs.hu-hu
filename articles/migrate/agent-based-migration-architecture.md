---
title: Ügynökalapú áttelepítés az Azure áttelepítési kiszolgálóáttelepítésében
description: Áttekintést nyújt az ügynök-alapú VMware vm-áttelepítés az Azure Áttelepítése.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: raynew
ms.openlocfilehash: d345d707cbf58f48466c3bd830d93250d13397c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77425857"
---
# <a name="agent-based-migration-architecture"></a>Ügynökalapú migrálási architektúra

Ez a cikk áttekintést nyújt a VMware virtuális gépek ügynökalapú replikációjához az [Azure Migrate: Server Migration](migrate-services-overview.md#azure-migrate-server-assessment-tool) eszközzel használt architektúráról és folyamatokról.

Az Azure Áttelepítés: Kiszolgálóáttelepítés használatával néhány lehetőséggel replikálhatja a VMware virtuális gépeket:

- Telepítse a virtuális gépeket ügynökalapú replikációval, a jelen cikkben leírtak szerint.
- Telepítse a VMware virtuális gépeket ügynök nélküli replikációval. Ez áttelepíti a virtuális gépeket anélkül, hogy bármit is telepítenie kellene rájuk.

További információ a VMware virtuális gépek áttelepítési módszereinek [kiválasztásáról és összehasonlításáról.](server-migrate-overview.md) 


## <a name="agent-based-migration"></a>Ügynökalapú áttelepítés

Az ügynökalapú áttelepítés a helyszíni VMware virtuális gépek és fizikai kiszolgálók Azure-ba való áttelepítésére szolgál. Más helyszíni virtualizált kiszolgálók, valamint magán- és nyilvános felhőalapú virtuális gépek, beleértve az AWS-példányokat és a GCP virtuális gépeket is áttelepíthető. Ügynök-alapú áttelepítés az Azure Migrate használ néhány háttérfunkció az [Azure Site Recovery](../site-recovery/site-recovery-overview.md) szolgáltatás.


## <a name="architectural-components"></a>Az architektúra összetevői

Az ábra az ügynökalapú áttelepítés összetevőit mutatja be.

![Architektúra](./media/agent-based-replication-architecture/architecture.png)

A tábla összefoglalja az ügynökalapú áttelepítéshez használt összetevőket.

**Összetevő** | **Részletek** | **Telepítés**
--- | --- | ---
**Replikációs berendezés** | A replikációs berendezés (konfigurációs kiszolgáló/folyamatkiszolgáló) egy helyszíni gép, amely hídként működik a helyszíni környezet és a Kiszolgáló áttelepítése között. A készülék felderíti a helyszíni gépleltárt, hogy a kiszolgáló áttelepítése koordinálhassa a replikációt és az áttelepítést. A készülék két összetevőből áll:<br/><br/> **Konfigurációs kiszolgáló**: Csatlakozik a kiszolgáló áttelepítéséhez, és koordinálja a replikációt.<br/> **Folyamatkiszolgáló**: Kezeli az adatreplikációt. A folyamatkiszolgáló fogadja a gép adatait, tömöríti és titkosítja azokat, és elküldi az Azure-ba. Az Azure-ban a Server Migration a felügyelt lemezekre írja az adatokat. | Alapértelmezés szerint a folyamatkiszolgáló a replikációs berendezés konfigurációs kiszolgálójával együtt van telepítve.
**Mobilitási szolgáltatás** | A Mobilitás szolgáltatás egy olyan ügynök, amely minden replikálni és áttelepíteni kívánt számítógépre telepítve van. Replikációs adatokat küld a gépről a folyamatkiszolgálónak. | A Mobility szolgáltatás különböző verzióihoz szükséges telepítési fájlok a replikációs készüléken találhatók. Töltse le és telepítse a szükséges ügynököt, összhangban az operációs rendszer és a készülék replikálni kívánt verziója.

## <a name="mobility-service-installation"></a>A mobilitási szolgáltatás telepítése

A mobilitási szolgáltatást a következő módszerekkel telepítheti:

- **Leküldéses telepítés**: A Mobilrendszer-kiszolgáló a folyamatkiszolgálón telepíti a mobilszolgáltatást, ha engedélyezi a számítógép védelmét. 
- **Manuális telepítés**: A Mobilitás szolgáltatást manuálisan telepítheti minden számítógépre a felhasználói felületen vagy a parancssorban.

A Mobilitás szolgáltatás kommunikál a replikációs készülékkel és a replikált gépekkel. Ha a replikációs készüléken, a folyamatkiszolgálókon vagy a replikált gépeken víruskereső szoftver fut, a következő mappákat ki kell zárni a vizsgálatból:


- C:\Program Files\Microsoft Azure helyreállítási szolgáltatások ügynöke
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure webhely helyreállítása
- C:\Program Files (x86)\Microsoft Azure Site Recovery
- C:\ProgramData\ASR\agent (olyan Windows-gépeken, amelyeken telepítve van a Mobility szolgáltatás)

## <a name="replication-process"></a>Replikációs folyamat

1. Ha engedélyezi a replikációt egy gép, az Azure-ba történő kezdeti replikáció kezdődik.
2. A kezdeti replikáció során a Mobilitás szolgáltatás beolvassa az adatokat a számítógép lemezeiről, és elküldi azokat a folyamatkiszolgálónak.
3. Ezeket az adatokat az Azure-előfizetésben a lemez egy példányának elültetéséhez használjuk. 
4. A kezdeti replikáció befejezése után megkezdődik az Azure-ba történő különbözeti módosítások replikációja. A replikáció blokkszintű, és közel folyamatos.
4. A Mobilitás szolgáltatás az operációs rendszer tárolóalrendszerével integrálva elfogja a lemezmemóriába történő írásokat. Ezzel a módszerrel elkerülhetők a lemez I/O-műveletei a replikálógépen a növekményes replikációhoz. 
5. A rendszer a HTTPS 9443 bejövő porton küldi el a folyamatkiszolgálónak a számítógép nyomon követett módosításait. Ez a port módosítható. A folyamatkiszolgáló tömöríti és titkosítja, és elküldi az Azure-ba. 

## <a name="ports"></a>Portok

**Eszköz** | **Kapcsolat**
--- | --- 
**Replikáló gépek** | A virtuális gépeken futó mobilitási szolgáltatás kommunikál a HTTPS 443-as porton bejövő helyszíni replikációs eszközzel a replikáció kezeléséhez.<br/><br/> A gépek a HTTPS 9443 porton lévő folyamatkiszolgálóra küldik a replikációs adatokat. Ez a port módosítható.
**Replikációs berendezés** | A replikációs berendezés vezényli a replikációt az Azure-ral HTTPS 443 kimenő porton keresztül.
**Folyamatkiszolgáló** | A folyamatkiszolgáló fogadja a replikációs adatokat, optimalizálja és titkosítja azokat, és elküldi az Okat az Azure storage-ba a 443-as kimenő porton keresztül.


## <a name="performance-and-scaling"></a>Teljesítmény és skálázás

Alapértelmezés szerint egyetlen replikációs berendezést telepít, amely a konfigurációs kiszolgálót és a folyamatkiszolgálót is futtatja. Ha csak néhány gépet replikál, ez a központi telepítés elegendő. Ha azonban több száz gépet replikál és telepít át, előfordulhat, hogy egyetlen folyamatkiszolgáló nem tudja kezelni az összes replikációs forgalmat. Ebben az esetben további horizontális felskálázási folyamatkiszolgálókat is telepíthet.

### <a name="plan-vmware-deployment"></a>A VMware telepítésének megtervezése

VMware virtuális gépek replikálása esetén a [Site Recovery Deployment Planner for VMware](../site-recovery/site-recovery-deployment-planner.md)segítségével meghatározhatja a teljesítménykövetelményeket, beleértve a napi adatváltozási sebességet és a szükséges folyamatkiszolgálókat.

### <a name="replication-appliance-capacity"></a>A replikációs berendezés kapacitása

A táblázatban szereplő értékek segítségével állapítsa meg, hogy szükség van-e további folyamatkiszolgálóra a központi telepítéshez.

- Ha a napi változási sebesség (lemorzsolódási arány) több mint 2 TB, telepítsen egy további folyamatkiszolgálót.
- Ha több mint 200 gépet replikál, telepítsen egy további replikációs berendezést.

**Cpu** | **Memory (Memória)** | **Szabad téradat-gyorsítótárazás** | **Lemorzsolódási arány** | **Replikációs korlátok**
--- | --- | --- | --- | ---
8 vCPU (2 foglalat * \@ 4 mag 2,5 GHz) | 16 GB | 300 GB | 500 GB vagy kevesebb | < 100 gép 
12 vCPU (2 foglalat * \@ 6 mag 2,5 GHz) | 18 GB | 600 GB | 501 GB–1 TB | 100-150 gép.
16 vCPU (2 foglalat * \@ 8 mag 2,5 GHz) | 32 G1 |  1 TB | 1 TB és 2 TB között | 151-200 gép.

### <a name="sizing-scale-out-process-servers"></a>Horizontális felskálázási folyamat kiszolgálóinak méretezése

Ha horizontális felskálázási folyamatkiszolgálót kell telepítenie, ezzel a táblával kitalálja a kiszolgáló méretezését.

**Folyamatkiszolgáló** | **Szabad terület az adatgyorsítótárazáshoz** | **Lemorzsolódási arány** | **Replikációs korlátok**
--- | --- | --- | --- 
4 vCPU (2 foglalat * \@ 2 mag 2,5 GHz), 8 GB memória | 300 GB | 250 GB vagy kevesebb | Akár 85 gép 
8 vCPU (2 foglalat * \@ 4 mag 2,5 GHz), 12 GB memória | 600 GB | 251 GB–1 TB    | 86-150 gép.
12 vCPU (2 foglalat * \@ 6 mag 2,5 GHz), 24 GB-os memória | 1 TB | 1-2 TB | 151-225 gép.

## <a name="throttle-upload-bandwidth"></a>Fojtószelep feltöltési sávszélessége.

VMware-forgalom, amely replikálja az Azure-ba megy keresztül egy adott folyamatkiszolgálón keresztül. Korlátozhatja a feltöltési átviteli folyamatot a folyamatkiszolgálóként futó gépek sávszélességének szabályozásával. A beállításkulcs segítségével befolyásolhatja a sávszélességet:

- A HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM beállításjegyzék-érték a lemez adatátviteléhez (kezdeti vagy különbözeti replikációhoz) használt szálak számát adja meg. A nagyobb érték növeli a replikációhoz használt hálózati sávszélességet. Az alapértelmezett érték négy. A maximális érték 32. Az optimális érték kiválasztásához kövesse figyelemmel a forgalmat.
- Ezenkívül a folyamatkiszolgálón a sávszélességet az alábbiak szerint szabályozhatja:

    1. A folyamatkiszolgáló-gépen nyissa meg az Azure Backup MMC beépülő modult. Van egy parancsikon az asztalon vagy a C:\Program Files\Microsoft Azure Recovery Services Agent\bin mappában. 
    2. A beépülő modulban válassza a **Tulajdonságok módosítása**lehetőséget.
    3. A **Szabályozás területen**válassza az Internet **sávszélesség-használat szabályozásának engedélyezése a biztonsági mentési műveletekhez**lehetőséget. Állítsa be a munkaidő és a nem munkaidő korlátait. Az érvényes tartományok 512 Kb/s és 1023 Mb/s között mozognak.


## <a name="next-steps"></a>További lépések

Próbálja ki [az ügynökalapú áttelepítést](tutorial-migrate-vmware-agent.md) a [VMware](tutorial-migrate-vmware-agent.md) vagy a [fizikai kiszolgálók](tutorial-migrate-physical-virtual-machines.md)számára.

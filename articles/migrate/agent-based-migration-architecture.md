---
title: Ügynök-alapú áttelepítés Azure Migrate kiszolgáló áttelepítésekor
description: Áttekintést nyújt az ügynök-alapú VMware VM-Migrálás Azure Migrate kiszolgáló áttelepítésével.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: a8477b4c10ccbc76f36eed4d64ac12e8bb648a28
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186102"
---
# <a name="agent-based-migration-architecture"></a>Ügynökalapú migrálási architektúra

Ez a cikk áttekintést nyújt az ügynök alapú replikációhoz használt architektúráról és folyamatokról az Azure Migrate Server áttelepítési eszközzel.

[Azure Migrate](migrate-services-overview.md) egy központi központot biztosít a helyszíni alkalmazások és munkaterhelések, valamint az AWS/GCP virtuálisgép-példányok felderítésének, értékelésének és áttelepítésének nyomon követéséhez az Azure-ban. Az elosztó Azure Migrate eszközöket biztosít az értékeléshez és az áttelepítéshez, valamint a harmadik féltől származó független szoftvergyártók (ISV) ajánlatokhoz.

## <a name="agent-based-replication"></a>Ügynök-alapú replikáció

A Azure Migrate kiszolgáló-replikációs eszköz ügynök-alapú replikálása a helyszíni VMware virtuális gépek és fizikai kiszolgálók Azure-ba történő áttelepítésére szolgál. Más helyszíni virtualizált kiszolgálók, valamint magán-és nyilvános Felhőbeli virtuális gépek, például AWS-példányok és GCP virtuális gépek áttelepítésére is használható.

A VMware-áttelepítéshez a Azure Migrate Server áttelepítési eszköz több lehetőséget kínál:

- Az ügynök-alapú replikáció használatával történő áttelepítés a jelen cikkben leírtak szerint.
- Ügynök nélküli replikáció, a virtuális gépek áttelepítése anélkül, hogy bármit telepíteni kellene.

További információ a [VMware-hez készült áttelepítési módszer kiválasztásáról](server-migrate-overview.md).

## <a name="server-migration-and-azure-site-recovery"></a>Kiszolgáló áttelepítése és Azure Site Recovery

A Azure Migrate kiszolgáló áttelepítése egy eszköz a helyszíni és a nyilvános Felhőbeli számítási feladatok Azure-ba való áttelepítéséhez. Áttelepítésre van optimalizálva. Site Recovery vész-helyreállítási eszköz. Az Azure-kiszolgáló áttelepítése és Site Recovery megoszthatja az adatreplikáláshoz használt közös technológiai összetevőket, de különböző célokra szolgál.

## <a name="architectural-components"></a>Az architektúra összetevői

![Architektúra](./media/agent-based-replication-architecture/architecture.png)

A tábla összegzi az ügynök alapú áttelepítéshez használt összetevőket.

**Összetevő** | **Részletek** | **Telepítés**
--- | --- | ---
**Replikációs berendezés** | A replikációs berendezés (konfigurációs kiszolgáló) egy helyszíni gép, amely hidat képez a helyszíni környezet és a Azure Migrate Server áttelepítési eszköz között. A készülék felfedi a helyszíni virtuális gépek leltárát, így az Azure-kiszolgáló áttelepítése képes a replikáció és az áttelepítés koordinálására. A készülék két összetevőből áll:<br/><br/> **Konfigurációs kiszolgáló**: csatlakozás Azure Migrate kiszolgáló áttelepítéséhez és a replikáció koordinálásához.<br/> **Process Server**: kezeli az adatreplikációt. A virtuális gép adatokat fogad, tömöríti és titkosítja, és elküldi az Azure-előfizetésnek. A kiszolgáló áttelepítése a felügyelt lemezekre írja az adatot. | Alapértelmezés szerint a Process Server a replikációs berendezés konfigurációs kiszolgálójával együtt települ.
**Mobilitási szolgáltatás** | A mobilitási szolgáltatás egy olyan ügynök, amely minden replikálni és áttelepíteni kívánt gépre telepítve van. Replikációs adatokat küld a gépről a Process Server rendszernek. Számos különböző mobilitási szolgáltatási ügynök érhető el. | A mobilitási szolgáltatás telepítési fájljai a replikációs berendezésen találhatók. Töltse le és telepítse a szükséges ügynököt a replikálni kívánt gép operációs rendszerének és verziójának megfelelően.

### <a name="mobility-service-installation"></a>A mobilitási szolgáltatás telepítése

A mobilitási szolgáltatást a következő módszerekkel telepítheti:

- **Leküldéses telepítés**: a folyamat-kiszolgáló telepíti a mobilitási szolgáltatást, ha engedélyezi a gép védelmét. 
- **Manuális telepítés**: a mobilitási szolgáltatást manuálisan is telepítheti az egyes gépeken a felhasználói felületen vagy a parancssorban.

A mobilitási szolgáltatás kommunikál a replikációs berendezéssel és a replikált számítógépekkel. Ha a replikációs berendezésen fut a víruskereső szoftver, a kiszolgálókat vagy a replikált gépeket, a következő mappákat ki kell zárni a vizsgálatból:


- C:\Program Files\Microsoft Azure Recovery Services ügynök
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- C:\Program Files (x86) \Microsoft Azure Site Recovery
- C:\ProgramData\ASR\agent (Windows rendszerű gépeken, amelyen telepítve van a mobilitási szolgáltatás)

## <a name="replication-process"></a>Replikációs folyamat

1. Amikor engedélyezi a replikációt egy virtuális géphez, a kezdeti replikáció megkezdődik az Azure-ban.
2. A kezdeti replikálás során a mobilitási szolgáltatás beolvassa az adatokat a gépi lemezekről, és elküldi azt a folyamat-kiszolgálónak.
3. Ezek az adatmennyiségek a lemez egy példányának az Azure-előfizetésben való összevetésére szolgálnak. 
4. A kezdeti replikálás befejeződése után a változásokat az Azure-ba replikálva megkezdődik. A replikáció blokk szintű, és közel folyamatos.
4. A mobilitási szolgáltatás a virtuális gép memóriájába írja az írásokat az operációs rendszer tárolási alrendszerének integrálásával. Ezzel a módszerrel elkerülhető a replikálást végző gép lemezes I/O-műveleteinek növekményes replikációja. 
5. A gép nyomon követett módosításait a rendszer a HTTPS 9443 bejövő porton továbbítja a folyamat-kiszolgálónak. Ez a port módosítható. A Process Server tömöríti és titkosítja azt, és elküldi az Azure-nak. 

## <a name="ports"></a>Portok

**Device** | **kapcsolat**
--- | --- 
Virtuális gépek | A virtuális gépeken futó mobilitási szolgáltatás a HTTPS 443 bejövő porton keresztül kommunikál a helyszíni replikációs berendezéssel a replikálás kezeléséhez.<br/><br/> A virtuális gépek replikációs adatküldést küldenek a Process kiszolgálónak (alapértelmezés szerint a replikációs berendezésen futnak) a HTTPS 9443 bejövő porton. Ez a port módosítható.
Replikációs berendezés | A replikációs berendezés az Azure-ba irányuló replikációt a HTTPS 443 kimenő porton keresztül hangolja össze.
Kiszolgáló feldolgozása | A Process Server replikációs adatokat fogad, optimalizálja és titkosítja, majd az Azure Storage-ba küldi az 443-as porton keresztül.


## <a name="performance-and-scaling"></a>Teljesítmény és skálázás

Alapértelmezés szerint egyetlen replikációs berendezést telepít, amely a konfigurációs kiszolgálót és a Process Servert is futtatja. Ha csak néhány gépet replikál, akkor ez a központi telepítés elegendő. Ha azonban több száz gép replikálását és áttelepítését végzi, előfordulhat, hogy egyetlen folyamat-kiszolgáló nem tudja kezelni az összes replikációs forgalmat. Ebben az esetben telepíthet további, kibővíthető folyamat-kiszolgálókat is.

### <a name="site-recovery-deployment-planner-for-vmware"></a>VMware-Deployment Planner Site Recovery

Ha VMware virtuális gépeket replikál, a VMware-hez készült [Site Recovery Deployment Planner](../site-recovery/site-recovery-deployment-planner.md) segítségével meghatározhatja a teljesítményre vonatkozó követelményeket, beleértve a napi adatváltozási arányt és a szükséges folyamat-kiszolgálókat is.

### <a name="replication-appliance-capacity"></a>Replikációs berendezés kapacitása

Az ebben a táblázatban szereplő értékek alapján megállapítható, hogy szükség van-e egy további Process Serverre a telepítésben.

- Ha a napi változási arány (a forgalom aránya) meghaladja a 2 TB-ot, helyezzen üzembe egy további kiszolgálót.
- Ha több mint 200 gépet replikál, helyezzen üzembe egy további replikációs készüléket.

**CPU** | **Memória** | **Szabad terület az adatgyorsítótárazáshoz** | **Adatforgalom aránya** | **Replikációs korlátok**
--- | --- | --- | --- | ---
8 vCPU (2 szoftvercsatorna * 4 mag \@ 2,5 GHz) | 16 GB | 300 GB | 500 GB vagy kevesebb | < 100 gép 
12 vCPU (2 szoftvercsatorna * 6 mag \@ 2,5 GHz) | 18 GB | 600 GB | 501 GB – 1 TB | 100-150 gép.
16 vCPU (2 szoftvercsatorna * 8 mag \@ 2,5 GHz) | 32 G1 |  1 TB | 1 TB – 2 TB | 151-200 gép.

### <a name="scale-out-process-server-sizing"></a>Felskálázási folyamat kiszolgálójának méretezése

Ha egy kibővíthető folyamat-kiszolgálót kell üzembe helyeznie, a táblázat segítségével kiderítheti a kiszolgálók méretezését.

**Folyamatkiszolgáló** | **Szabad terület az adatgyorsítótárazáshoz** | **Adatforgalom aránya** | **Replikációs korlátok**
--- | --- | --- | --- 
4 vCPU (2 szoftvercsatorna * 2 mag \@ 2,5 GHz), 8 GB memória | 300 GB | 250 GB vagy kevesebb | Akár 85 gép 
8 vCPU (2 szoftvercsatorna * 4 mag \@ 2,5 GHz), 12 GB memória | 600 GB | 251 GB – 1 TB    | 86-150 gép.
12 vCPU (2 szoftvercsatorna * 6 mag \@ 2,5 GHz), 24 GB memória | 1 TB | 1-2 TB | 151-225 gép.

## <a name="control-upload-throughput"></a>A feltöltési sebesség szabályozása


 Az Azure-ba replikált VMware-forgalom egy adott folyamat-kiszolgálón halad át. A feltöltési sebességet korlátozhatja a folyamat-kiszolgálóként futó gépek sávszélességének szabályozásával. A következő beállításkulcs használatával befolyásolhatja a sávszélességet:

- A HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\Windows az Azure Backup\Replication\UploadThreadsPerVM beállításazonosító megadja a lemez adatátviteli (kezdeti vagy különbözeti replikációja) által használt szálak számát. A magasabb érték növeli a replikáláshoz használt hálózati sávszélességet. Az alapértelmezett érték négy. A maximális érték 32. Az optimális érték kiválasztásához kövesse figyelemmel a forgalmat.
- Emellett a következő módon szabályozhatja a sávszélességet a Process Server-gépen:

    1. A Process Server gépen nyissa meg az Azure Backup MMC beépülő modult. Parancsikon található az asztalon vagy a következő mappában: C:\Program Files\Microsoft Azure Recovery Services Agent\bin. 
    2. A beépülő modulban válassza a **Tulajdonságok módosítása**lehetőséget.
    3. A **szabályozás**alatt jelölje be a **biztonsági mentési műveletek internetes sávszélesség-szabályozásának engedélyezése**jelölőnégyzetet. A munkamennyiség és a munkaidőn kívüli munkaidő korlátozásának beállítása. Az érvényes tartományok 512 kbps és 1 023 Mbps között vannak.


## <a name="next-steps"></a>Következő lépések

Próbálja ki az ügynök-alapú [VMWare virtuális gépek áttelepítését](tutorial-migrate-vmware-agent.md) Azure Migrate kiszolgáló áttelepítésével.

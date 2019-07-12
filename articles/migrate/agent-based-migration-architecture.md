---
title: Az ügynök-alapú áttelepítési architektúra az Azure Migrate-kiszolgáló áttelepítése
description: Az ügynök-alapú a VMware virtuális gépek áttelepítése az Azure Migrate-kiszolgáló áttelepítése áttekintést nyújt.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: raynew
ms.openlocfilehash: 21c779587842c976ba93d7fa592a91ee714bc55c
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811153"
---
# <a name="agent-based-migration-architecture"></a>Az ügynök-alapú áttelepítési architektúra

Ez a cikk architektúrájának és folyamatainak ügynök-alapú replikálás az Azure Migrate-kiszolgáló áttelepítése eszközzel használt áttekintést nyújt.

[Az Azure Migrate](migrate-services-overview.md) kínál egy központi agyhoz felderítési, felmérési és a helyszíni alkalmazások és számítási feladatok és az AWS/GCP Virtuálisgép-példányok, az Azure-ba való migrálásának nyomon követéséhez. A hub felmérés és migrálás, valamint a külső független szoftverszállítók (ISV) szállító ajánlatok az Azure Migrate eszközöket biztosít.

## <a name="agent-based-replication"></a>Replikációs ügynök-alapú

Replikációs ügynök-alapú eszköz való áttelepítéshez használható az Azure Migrate Tárreplikáció a helyszíni VMware virtuális gépek és fizikai kiszolgálók Azure-bA. Is használható más helyszíni virtualizált kiszolgálók, valamint privát és nyilvános felhőbeli VM-eken, beleértve az AWS-példányokat, és GCP virtuális gépek áttelepítéséhez.

A VMware-migrálás az Azure Migrate-kiszolgáló áttelepítése eszközt kínál a több lehetőség közül választhat:

- Az áttelepítés ügynök-alapú replikálás használata ebben a cikkben leírtak szerint.
- Ügynök nélküli kivételfigyelés replikációs migrálhat virtuális gépeket anélkül, hogy telepít semmit a őket.

Tudjon meg többet [egy áttelepítési módszer kiválasztása: VMware-ről](server-migrate-overview.md).

## <a name="server-migration-and-azure-site-recovery"></a>Az Azure Site Recovery és a kiszolgáló áttelepítése

Az Azure Migrate kiszolgáló áttelepítése egy olyan eszköz áttelepítése a helyszíni és a nyilvános felhőalapú számítási feladatokhoz az Azure-bA. Áttelepítés van optimalizálva. Site Recovery szolgáltatás a vész-helyreállítási eszköze. Az Azure kiszolgáló áttelepítése és a Site Recovery néhány gyakori technológia összetevők adatreplikációt megosztani, de különböző célokat szolgálnak.

## <a name="architectural-components"></a>Az architektúra összetevői

![Architektúra](./media/agent-based-replication-architecture/architecture.png)

A táblázat összefoglalja az ügynök-alapú áttelepítéshez használt összetevőket.

**Összetevő** | **Részletek** | **Telepítés**
--- | --- | ---
**Replikációs készülék** | A replikációs berendezés (konfigurációs kiszolgáló) egy helyszíni gépre, amely a hídként működnek a helyszíni környezet és az Azure Migrate Server áttelepítési eszköz között. A berendezés felderíti a helyszíni virtuális gép készlet, úgy, hogy a replikáció és a migrálás vezényelhető az Azure-kiszolgáló áttelepítése. A berendezés két részből áll:<br/><br/> **Konfigurációs kiszolgáló**: Az Azure Migrate-kiszolgáló áttelepítése csatlakozik, és koordinálja a replikációt.<br/> **Folyamatkiszolgáló**: Kezeli az adatreplikációt. Virtuális gép adatokat fogad, tömöríti és titkosítja azokat és küld az Azure-előfizetést. Itt áttelepítése az adatokat a managed Disks szolgáltatásba írja. | Alapértelmezés szerint a folyamatkiszolgáló telepítve van a konfigurációs kiszolgáló, a replikáció berendezésen együtt.
**Mobilitási szolgáltatás** | A mobilitási szolgáltatás az ügynök minden replikálni, és a migrálni kívánt gépen telepítve. A folyamatkiszolgáló küld adatokat a gépről. Nincsenek elérhető egy másik mobilitási szolgáltatási ügynökök számát. | A mobilitási szolgáltatás telepítési fájljainak a replikációs berendezés található. Töltse le és telepítse az ügynököt van szüksége, az operációs rendszer és a replikálni kívánt gépen verziójának megfelelően.

### <a name="mobility-service-installation"></a>A mobilitási szolgáltatás telepítése

A mobilitási szolgáltatás a következő módszerekkel helyezhető üzembe:

- **Leküldéses telepítés**: A mobilitási szolgáltatást a folyamatkiszolgáló telepítve van, ha a gép védelmét engedélyezi. 
- **Telepítse manuálisan**: Az összes felhasználói felületen vagy az parancssor segítségével manuálisan telepítheti a mobilitási szolgáltatást.

A mobilitási szolgáltatás a replikációs berendezés kommunikál, és a replikált gépek. Ha víruskereső szoftver is fut a replikációs berendezés, a folyamat kiszolgálóra, vagy a replikált gépek, a következő mappák ki kell zárni vizsgálata:


- C:\Program Files\Microsoft Azure Recovery Services-ügynök
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- C:\Program Files (x86)\Microsoft Azure Site Recovery
- C:\ProgramData\ASR\agent (a Windows-gépeken telepített mobilitási szolgáltatással)

## <a name="replication-process"></a>Replikációs folyamat

1. Amikor engedélyezi egy virtuális gép replikációját, megkezdődik a kezdeti replikálás az Azure-bA.
2. Kezdeti replikálás során a mobilitási szolgáltatás adatokat olvas be a gépek lemezeit, és elküldi azokat a folyamatkiszolgáló.
3. Ezek az adatok segítségével ültet be egy példányát a lemezt az Azure-előfizetésében. 
4. Kezdeti replikálás befejezése után kezdődik replikációja az Azure-bA. Replikáció, blokkszintű, valamint közel folyamatos.
4. A mobilitási szolgáltatás elfogja azokat a Virtuálisgép-lemez memória, a tárolóalrendszer, az operációs rendszer integrálásával ír. Ezzel a módszerrel elkerülhetők a lemezes i/o-műveletek növekményes replikáció a replikáló gépen. 
5. A gépek nyomon követett módosításait érkeznek a folyamatkiszolgáló HTTPS 9443-as porton bejövő. Ez a port módosítható. A folyamatkiszolgáló tömöríti és titkosítja azokat, és elküldi azt az Azure-bA. 

## <a name="ports"></a>Portok

**Device** | **kapcsolat**
--- | --- 
Virtuális gépek | A virtuális gépeken futó mobilitási szolgáltatás kommunikál a helyszíni replikációs berendezés HTTPS a 443-as porton bejövő, a replikáció kezelését.<br/><br/> Virtuális gépek küldhetnek replikációs adatokat a folyamatkiszolgálónak (alapértelmezés szerint a replikációs berendezés futtatását) HTTPS 9443-as porton bejövő. Ez a port módosítható.
Replikációs készülék | A replikációs berendezés koordinálja a replikálás az Azure-ral HTTPS 443-as kimenő porton keresztül.
Folyamatkiszolgáló | A folyamatkiszolgáló fogadja a replikált adatokat, optimalizálja a és titkosítja azokat, és elküldi azt az Azure storage 443-as porton keresztüli kimenő.


## <a name="performance-and-scaling"></a>Teljesítmény és skálázás

Alapértelmezés szerint egy egyetlen replikációs berendezés, amely futtatja a konfigurációs kiszolgáló és a folyamatkiszolgáló üzembe helyezése. Ha csak néhány gépeket replikál, a központi telepítés is használhatók. Azonban ha Ön replikálása és több száz gépek migrálása, a folyamat egyetlen kiszolgáló nem tudja kezelni a replikációs forgalmat. Ebben az esetben további, a horizontális felskálázási folyamatkiszolgáló telepítheti.

### <a name="site-recovery-deployment-planner-for-vmware"></a>Site Recovery Deployment Planner VMware-ről

Ha VMware virtuális gépeket replikál, használhatja a [Site Recovery Deployment Planner](../site-recovery/site-recovery-deployment-planner.md) többek között a napi adatok segítségével meghatározhatja, hogy a teljesítmény-követelmények VMware sebességet és a folyamat a szükséges kiszolgálók módosítása.

### <a name="replication-appliance-capacity"></a>Replikációs készülék kapacitás

Az értékek a táblázat segítségével döntse el, hogy a központi telepítésben szüksége további folyamatkiszolgálót.

- Ha több mint 2 TB-os, a napi adatváltozási sebesség (forgalom sebessége) üzembe helyezése egy további folyamatkiszolgálón.
- Ha több mint 200 olyan gépet replikál, üzembe helyezése további replikációs telepíthetőek.

**CPU** | **Memória** | **Szabad terület az adatok gyorsítótárazása** | **Adatváltozásának** | **Replikációs korlátozások**
--- | --- | --- | --- | ---
8 Vcpu (2 sockets * 4 mag \@ 2,5 GHz-es) | 16 GB | 300 GB | 500 GB vagy kevesebb | < 100 gépek 
12 vcpu-k (2 sockets * 6 magok \@ 2,5 GHz-es) | 18 GB | 600 GB | 501 GB – 1 TB | 100 – 150 gépek.
16 vcpu-k (2 sockets * 8 magos \@ 2,5 GHz-es) | 32 G1 |  1 TB | 1 TB-os 2 TB-ig | 151 – 200 – gépek.

### <a name="scale-out-process-server-sizing"></a>Horizontális felskálázási folyamat méretezése

Ha szeretne horizontális felskálázási folyamatkiszolgáló üzembe helyezése, ez a táblázat segíthet azonosítani a méretezése.

**Folyamatkiszolgáló** | **Szabad terület az adatok gyorsítótárazása** | **Adatváltozásának** | **Replikációs korlátozások**
--- | --- | --- | --- 
4 vcpu-k (2 sockets * 2 mag \@ 2,5 GHz-es), 8 GB memória | 300 GB | 250 GB vagy kevesebb | 85 gépek 
8 Vcpu (2 sockets * 4 mag \@ 2,5 GHz-es), 12 GB memória | 600 GB | 1 TB-os 251 GB    | 86 – 150 gépek.
12 vcpu-k (2 sockets * 6 magok \@ 2,5 GHz-es), 24 GB memória | 1 TB | 1-2 TB | gépek 151-225.

## <a name="control-upload-throughput"></a>Vezérlő feltöltési sebessége

Adatok feltöltése az Azure-ban minden egyes Hyper-V gazdagépen használt sávszélesség is korlátozza. légy óvatos. Ha túl alacsony értékek negatívan befolyásolja replikáció és a késleltetés migrálása.


1. Jelentkezzen be a Hyper-V gazdagép vagy -fürt csomópontja.
2. Futtatás **C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.msc**, a Windows Azure Backup szolgáltatás MMC beépülő modul megnyitásához.
3. A beépülő modulban, válassza ki a **tulajdonságainak módosítása**.
4. A **sávszélesség-szabályozási**válassza **szabályozása a biztonsági mentési műveletek internetes sávszélességének**. Állítsa be a munkaórákra és a munkaórákon kívüli. 512 KB/s a 1,023 Mbps érvényes tartományok származnak.
I

### <a name="influence-upload-efficiency"></a>Befolyásoló feltöltési hatékonyságát

Ha rendelkezik a replikáció tartalékolt sávszélesség, és szeretné növelni a feltöltések, növelheti a következőképpen a feltöltési feladathoz lefoglalt szálak száma:

1. Nyissa meg a beállításjegyzékben a Regedit.
2. Keresse meg a kulcs HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM
3. Növelje az adatfeltöltés replikáló virtuális gépek használt szálak számát. Az alapértelmezett értéke 4, maximális értéke pedig 32. 

## <a name="next-steps"></a>További lépések

Próbálja ki az ügynök-alapú [VMware virtuális gépek migrálása](tutorial-migrate-vmware-agent.md) Azure Migrate-áttelepítés használatával.

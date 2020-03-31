---
title: A teljesítmény optimalizálása az Azure Lsv2 sorozatú virtuális gépeken – Storage
description: Ismerje meg, hogyan optimalizálhatja a teljesítményt a megoldáshoz az Lsv2 sorozatú virtuális gépeken.
services: virtual-machines-linux
author: laurenhughes
manager: gwallace
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/05/2019
ms.author: joelpell
ms.openlocfilehash: 8d99f63ae084b4f1dae3c0125420eaecf5655e2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034758"
---
# <a name="optimize-performance-on-the-lsv2-series-virtual-machines"></a>Optimalizálja a teljesítményt az Lsv2 sorozatú virtuális gépeken

Az Lsv2 sorozatú virtuális gépek számos olyan számítási feladatot támogatnak, amelyek magas I/O-t és átviteli kapacitást igényelnek a helyi tárolókban az alkalmazások és iparágak széles körében.  Az Lsv2 sorozat ideális big data, SQL, NoSQL adatbázisok, adattárház és nagy tranzakciós adatbázisok, beleértve a Cassandra, MongoDB, Cloudera és Redis.

Az Lsv2 sorozatú virtuális gépek (VM-ek) kialakítása maximalizálja az AMD EPYC™ 7551 processzort, hogy a legjobb teljesítményt nyújtsa a processzor, a memória, az NVMe-eszközök és a virtuális gépek között. A Linuxon dolgozó partnerekkel együttműködve számos build érhető el az Azure Marketplace-en, amelyek az Lsv2 sorozat teljesítményére vannak optimalizálva, és jelenleg a következőket tartalmazzák:

- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 8,0
- Debian 9
- Debian 10

Ez a cikk tippeket és javaslatokat tartalmaz annak biztosítására, hogy a számítási feladatok és az alkalmazások a virtuális gépekbe tervezett maximális teljesítményt érjék el. Az ezen az oldalon található információk folyamatosan frissülnek, amint több Lsv2-re optimalizált lemezkép kerül az Azure Piactérre.

## <a name="amd-eypc-chipset-architecture"></a>AMD EYPC™ lapkakészlet architektúra

Az Lsv2 sorozatú virtuális gépek AMD EYPC™ zen mikroarchitektúrán alapuló kiszolgálóprocesszorokat használnak. Az AMD az Infinity Fabric (IF) for EYPC™ as ascalable connect for its NUMA model that could be used for on-die, on-package, and multi-package communications. Az Intel modern monolitikus processzorain használt QPI-vel (Quick-Path Interconnect) és UPI-vel (Ultra-Path Interconnect) összehasonlítva az AMD sok NUMA kis-die architektúrája mind a teljesítményelőnyöket, mind a kihívásokat növelheti. A memória sávszélességének és a késési korlátoknak a tényleges hatása a futó számítási feladatok típusától függően változhat.

## <a name="tips-to-maximize-performance"></a>Tippek a teljesítmény maximalizálásához

* Ha egy egyéni Linux GuestOS-t tölt fel a számítási feladatokhoz, vegye figyelembe, hogy az Accelerated Networking alapértelmezés szerint ki lesz **kapcsolva.** Ha engedélyezni kívánja az Accelerated Networking,engedélyezze a virtuális gép létrehozásakor a legjobb teljesítmény érdekében.

* Az Lsv2 sorozatú virtuális gépeket működtető hardver nyolc I/O várólista-párral (QP) használja az NVMe-eszközöket. Minden NVMe eszköz I/O-várólista valójában egy pár: egy beküldési és egy befejezési várólista. Az NVMe illesztőprogram úgy van beállítva, hogy optimalizálja a nyolc I/O qP kihasználtságát az I/O-k ciklikus multiplexelési ütemezésben történő terjesztésével. A maximális teljesítmény érdekében eszközenként nyolc feladatot kell futtatni, hogy megfeleljen.

* Ne keverje az NVMe felügyeleti parancsokat (például NVMe SMART info lekérdezést stb.) az NVMe I/O parancsokkal az aktív munkaterhelések során. Az Lsv2 NVMe eszközöket a Hyper-V NVMe Direct technológia támogatja, amely "lassú módba" vált, ha bármely NVMe admin parancs függőben van. Lsv2 használók tudna lát egy drámai előadás csepp -ban NVMe I/O előadás ha amit történik.

* Lsv2 felhasználók nem támaszkodhat eszköz NUMA adatok (mind 0) jelentett a virtuális gép adatmeghajtók dönta numa affinitása az alkalmazások. A jobb teljesítmény ajánlott módja a számítási feladatok terjesztése a processzorok között, ha lehetséges.

* Az Lsv2 VM NVMe eszköz I/O-várólista-páronkénti maximális támogatott várólista-mélysége 1024 (szemben az Amazon i3 QD 32 korláttal). Az Lsv2-felhasználóknak a (szintetikus) teljesítményértékelési számítási feladataikat az 1024-es vagy alacsonyabb várólistamélységre kell korlátozniuk, hogy elkerüljék a várólista teljes feltételeinek kiváltását, ami csökkentheti a teljesítményt.

## <a name="utilizing-local-nvme-storage"></a>Helyi NVMe tároló felhasználásával

Az 1,92 TB-os NVMe lemezen lévő helyi tárolás az összes Lsv2 virtuális gépen rövid élettartamú. A virtuális gép sikeres szabványos újraindítása során a helyi NVMe-lemezen lévő adatok megmaradnak. Az adatok nem maradnak meg az NVMe-n, ha a virtuális gép újratelepítése, de-lefoglalt, vagy törölt. Az adatok nem maradnak meg, ha egy másik probléma okozza a virtuális gép, vagy a hardver fut, nem kifogástalan lesz. Ha ez történik, a régi állomáson lévő adatok biztonságosan törlődnek.

Lesznek olyan esetek is, amikor a virtuális gépet át kell helyezni egy másik gazdagépre, például egy tervezett karbantartási művelet során. Tervezett karbantartási műveletek és néhány hardverhiba várható [az ütemezett eseményekkel.](scheduled-events.md) Ütemezett események kell használni, hogy naprakész maradjon az előre jelzett karbantartási és helyreállítási műveletek.

Abban az esetben, ha egy tervezett karbantartási esemény megköveteli, hogy a virtuális gép újra létre kell hozni egy új állomás üres helyi lemezekkel, az adatokat újra kell szinkronizálni (ismét a régi állomás on-t, hogy biztonságosan törlik). Ennek az az oka, hogy az Lsv2 sorozatú virtuális gépek jelenleg nem támogatják az élő áttelepítést a helyi NVMe-lemezen.

A tervezett karbantartásnak két módja van.

### <a name="standard-vm-customer-controlled-maintenance"></a>Standard virtuális gép ügyfél által ellenőrzött karbantartás

- A virtuális gép egy 30 napos időszakban egy frissített állomásra kerül.
- Lsv2 helyi tárolási adatok elveszhetnek, ezért az esemény előtti biztonsági mentési adatok ajánlott.

### <a name="automatic-maintenance"></a>Automatikus karbantartás

- Akkor következik be, ha az ügyfél nem hajt végre ügyfél által ellenőrzött karbantartást, vagy vészhelyzetesetén, például egy biztonsági nulladik napi esemény esetén.
- Az ügyféladatok megőrzése, de van egy kis kockázata a virtuális gép lefagyása vagy újraindítása.
- Lsv2 helyi tárolási adatok elveszhetnek, ezért az esemény előtti biztonsági mentési adatok ajánlott.

A közelgő szolgáltatási események esetén használja az ellenőrzött karbantartási folyamatot a frissítéshez legmegfelelőbb időpont kiválasztásához. Az esemény előtt biztonsági másolatot kaphat adatairól a prémium szintű tárhelyen. A karbantartási esemény befejezése után visszaküldheti az adatokat a frissített Lsv2 virtuális gépek helyi NVMe tárolóba.

A helyi NVMe-lemezeken adatokat karbantartó forgatókönyvek a következők:

- A virtuális gép fut, és kifogástalan állapotú.
- A virtuális gép újraindul a helyén (ön vagy az Azure).
- A virtuális gép szünetel (leállítva a foglalás törlése nélkül).
- A tervezett karbantartási karbantartási műveletek többsége.

Az adatokbiztonságos törlését szolgáló forgatókönyvek a következők:

- A virtuális gép újratelepítése, leállítása (de-lefoglalt), vagy törli (ön által).
- A virtuális gép nem megfelelő állapotúvá válik, és hardverhiba miatt egy másik csomópontnak kell kiszolgálnia a gyógyulást.
- A tervezett karbantartási karbantartási műveletek egy kis száma, amely megköveteli, hogy a virtuális gép újra kell osztani egy másik állomás ra d.

Ha többet szeretne tudni az adatok helyi tárolóban való biztonsági mentésének lehetőségeiről, olvassa [el az Azure IaaS-lemezek biztonsági mentése és vészhelyreállítása.](backup-and-disaster-recovery-for-azure-iaas-disks.md)

## <a name="frequently-asked-questions"></a>Gyakori kérdések

* **Hogyan kezdhetem el telepíteni az Lsv2 sorozatú virtuális gépeket?**  
   Más virtuális gépekhez hasonlóan használja a [portál](quick-create-portal.md), [Azure CLI](quick-create-cli.md)vagy [PowerShell](quick-create-powershell.md) virtuális gép létrehozásához.

* **Egyetlen NVMe-lemez hiba okozza az összes virtuális gép a gazdagépen, hogy nem?**  
   Ha a hardvercsomóponton lemezhiba észlelhető, a hardver meghibásodott állapotban van. Ha ez bekövetkezik, a csomóponton lévő összes virtuális gép automatikusan leleszfoglalva, és egy kifogástalan állapotú csomópontra kerül. Az Lsv2 sorozatú virtuális gépek esetében ez azt jelenti, hogy az ügyfél hibás csomóponton lévő adatai is biztonságosan törlődnek, és az ügyfélnek újra létre kell hoznia az új csomóponton. Mint megjegyezte, mielőtt az élő áttelepítés elérhetővé válik az Lsv2, a hibás csomópont adatait proaktív módon áthelyezik a virtuális gépek, ahogy azok egy másik csomópontra.

* **Szükség van-e módosításokra a teljesítmény hez rq_affinity?**  
   A rq_affinity beállítás egy kisebb beállítás az abszolút maximális bemeneti/kimeneti műveletek másodpercenkénti (IOPS) használatakor. Miután minden más jól működik, akkor próbálja meg rq_affinity 0-ra állítani, hogy lássa, számít-e.

* **Módosítanom kell a blk_mq beállításait?**  
   Az RHEL/CentOS 7.x automatikusan blk-mq-t használ az NVMe készülékekhez. Nincs szükség konfigurációs módosításokra vagy beállításokra. A scsi_mod.use_blk_mq beállítás csak SCSI-re van, és az Lsv2 előzetes verzió során használták, mert az NVMe-eszközök scsi-eszközként voltak láthatók a vendég virtuális gépeken. Jelenleg az NVMe eszközök NVMe eszközként láthatók, így az SCSI blk-mq beállítás lényegtelen.

* **Ki kell cserélnem a "fio"-t?**  
   A maximális IOPS-hoz egy teljesítménymérő eszköz, mint a "fio" az L64v2 és L80v2 virtuális gép méretben, állítsa a "rq_affinity" 0-ra minden NVMe eszközön.  Ez a parancssor például az L80v2 virtuális gép összes 10 NVMe-eszközén nullára állítja a "rq_affinity" értéket:

   ```console
   for i in `seq 0 9`; do echo 0 >/sys/block/nvme${i}n1/queue/rq_affinity; done
   ```

   Azt is vegye figyelembe, hogy a legjobb teljesítményt akkor kapja meg, ha az I / O közvetlenül az egyes nyers NVMe eszközökre történik, particionálás, nincs fájlrendszer, nincs RAID 0 konfiguráció, stb. A tesztelési munkamenet megkezdése előtt győződjön meg arról, `blkdiscard` hogy a konfiguráció ismert friss/tiszta állapotban van az egyes NVMe-eszközökön való futtatással.
   
## <a name="next-steps"></a>További lépések

* Tekintse meg az [Azure-beli tárolási teljesítményre optimalizált](sizes-storage.md) összes virtuális gép specifikációit

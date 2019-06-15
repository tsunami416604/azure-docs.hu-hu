---
title: Az Azure Lsv2 sorozatú virtual machines - tárolási teljesítmény optimalizálása |} A Microsoft Docs
description: Ismerje meg, hogy a megoldás a Lsv2 sorozatú virtuális gépek teljesítményének optimalizálása.
services: virtual-machines-linux
author: laurenhughes
manager: jeconnoc
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/17/2019
ms.author: joelpell
ms.openlocfilehash: 7be86c8934b8766217f9fca432327d254204f0c4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60738944"
---
# <a name="optimize-performance-on-the-lsv2-series-virtual-machines"></a>A Lsv2 sorozatú virtuális gépek teljesítményének optimalizálása

Lsv2 sorozatú virtuális gépek támogatják a különböző számos különféle alkalmazások és az iparág nagy i/o és átviteli sebesség a helyi tárban igénylő számítási feladatok.  A Lsv2 sorozat ideális Big Data, SQL, nosql-alapú adatbázisok, az adattárházak és a nagy tranzakciós adatbázisok, például Cassandra, MongoDB, Cloudera, és a Redis.

A kialakítás a Lsv2 sorozatú virtuális gépek (VM) a lehető legnagyobbra növeli a AMD EPYC™ 7551 processzor, a processzor, memória, NVMe-eszközök és a virtuális gépek között a lehető legjobb teljesítményt. Maximalizálása a hardverek teljesítményére, valamint Lsv2-sorozat virtuális gépei úgy tervezték, hogy a jobb teljesítmény érdekében az a hardver- és a Linux operációs rendszerek igényeinek megfelelően dolgozhat.

Hardver- és finomhangolási eredményezett optimalizált verziója [Canonical Ubuntu 18.04 és 16.04](https://azuremarketplace.microsoft.com/marketplace/apps/Canonical.UbuntuServer?tab=Overview), az Azure piactéren, amely támogatja a maximális teljesítményt az NVMe-eszközök a korai December 2018-ban kiadott Lsv2-sorozat virtuális gépei.

Ez a cikk ismerteti a tippeket, és annak biztosítására, a számítási feladatok és alkalmazások javaslatok érheti el a maximális teljesítményt, a virtuális gépek tervezett. Ezen a lapon található információkat folyamatosan frissítjük a optimalizált Lsv2 további rendszerképeket az Azure Marketplace-en való hozzáadása során.

## <a name="amd-eypc-chipset-architecture"></a>AMD EYPC™ lapkakészlet-architektúra

Lsv2 sorozatú virtuális gépek használata a Zen microarchitecture alapján AMD EYPC™ server processzor. AMD fejlesztette ki végtelen Fabric (ha) EYPC™ méretezhető, ezek a NUMA-modell, amely a die, a-package és a több csomag kommunikációhoz használható. Képest a QPI (gyors elérési-útja követelő) és UPI (Ultra-Path követelő) processzoron Intel modern monolitikus die használt a AMD a több NUMA-kisméretű-die architektúra felhívhatja mindkét teljesítménybeli előnyei és kihívásai. A sávszélesség és késés memóriakorlátozások tényleges hatását a futó számítási feladatok típusától függően eltérőek lehetnek.

## <a name="tips-to-maximize-performance"></a>Tippek a legjobb teljesítmény érdekében

* Ha egy egyéni Linux GuestOS tölt fel a számítási feladatok számára, vegye figyelembe, hogy a gyorsított hálózatkezelés lesz **OFF** alapértelmezés szerint. Ha szeretne engedélyezni a gyorsított hálózatkezelés, engedélyezze a virtuális gép létrehozása idején a legjobb teljesítmény érdekében.

* A hardver-megoldás a Lsv2 sorozatú virtuális gépek nyolc i/o-várólistára párok (QP) s NVMe-eszközöket használja. Minden NVMe eszköz i/o-várólistára ténylegesen egy pár: küldésének üzenetsor és a egy befejezési várólistára. Az NVMe-illesztőprogram osztja meg tudom ezeket nyolc i/o-QPs felhasználása optimalizálása be van állítva, illetve O meg az egy Ciklikus időszeleteléses ütemezése. Maximális teljesítmény eléréséhez megfelelő eszközönként nyolc feladatokat is futtathat.

* Elkerülése érdekében NVMe felügyeleti parancsok (például INTELLIGENS NVMe info lekérdezés, stb.) az NVMe i/o-parancsok aktív számítási feladatok során. Lsv2 NVMe-eszközök élvezik Hyper-V NVMe közvetlen technológiát, amely a "lassú módban" vált, amikor NVMe felügyeleti parancsok függőben. Lsv2 sikerült látnak dobja el a NVMe i/o-teljesítményt, ha történik egy drámai teljesítményét.

* Lsv2 felhasználók nem támaszkodhat adatmeghajtók dönthet arról, hogy alkalmazásaikat a NUMA-affinitás révén a virtuális gép által jelentett NUMA eszköz adatai (minden 0). Az ajánlott módszer a jobb teljesítmény érdekében, hogy számítási feladatok elosztva a processzorok.

* A maximális támogatott várólistamélységének egy i/o-várólista pár Lsv2 VM NVMe eszköz 1024 (vs. Amazon i3 QD 32 korlát). Lsv2 felhasználók érdemes korlátozni a várólistamélységének 1024 vagy üzenetsor teljes feltételek, amelyek is teljesítménycsökkenést kiváltó elkerülése érdekében kisebb teljesítménymérési (szintetikus) számítási feladatokat.

## <a name="utilizing-local-nvme-storage"></a>Helyi NVMe-tároló használatával

Helyi tároló összes Lsv2 virtuális 1.92 TB NVMe lemezen rövid élettartamú. A virtuális gép sikeres standard újraindítása, során az adatokat a helyi lemezen NVMe addig megmarad. Az adatok nem megmaradnak az NVMe, ha a virtuális gép újratelepítése, felszabadítva vagy törölni. Adatok nem addig megmarad, ha egy másik probléma miatt a virtuális gép vagy a hardver-on fut, a nem megfelelő állapotú. Ha ez történik, a régi gazdagépen lévő adatok biztonságosan törölve lesz.

Is lesz esetben amikor a virtuális gép áthelyezése a másik gazdaszámítógépet, például egy tervezett karbantartási művelet során. A tervezett karbantartási műveleteket, és bizonyos hardveres hibák esetén lehet következtetni a [ütemezett események](scheduled-events.md). Az ütemezett események használandó naprakész bármely olyan előre jelzett karbantartási és helyreállítási műveletek.

Abban az esetben, egy tervezett karbantartási esemény a virtuális gép újra létre kell hozni egy üres helyi lemezek új gazdagépre van szükség az adatok (újra, a biztonságos törlése folyamatban van a régi gazdagépen lévő adatok) újra kell szinkronizálni kell. Ennek oka az, Lsv2-sorozat virtuális gépei jelenleg nem támogatják az élő áttelepítés a helyi lemezen NVMe.

Nincsenek tervezett karbantartás két módban.

### <a name="standard-vm-customer-controlled-maintenance"></a>Standard VM ügyfél által szabályozott karbantartás

- A virtuális gép egy frissített gazdagépre került egy 30 napos időszakban.
- Lsv2 helyi tár adatok elveszhetnek, így a biztonsági adatokat az esemény előtt ajánlott.

### <a name="automatic-maintenance"></a>Automatikus karbantartás

- Akkor következik be, ha az ügyfél, ügyfél által szabályozott karbantartás nem hajtható végre, vagy esetén például a nulladik napi biztonsági esemény eljárások vészhelyzet esetére.
- Vásárlói adatok megőrzése érdekében javasolt, de a virtuális gép lefagyását tapasztaló vagy újraindítás kis kockázata.
- Lsv2 helyi tár adatok elveszhetnek, így a biztonsági adatokat az esemény előtt ajánlott.

Bármely lezajló szolgáltatásfrissítések, tervezett események az ellenőrzött-karbantartási folyamata segítségével válassza ki a legmegfelelőbb Önnek, a frissítés ideje. Az esemény előtt lehet, hogy biztonsági másolatot az adatairól, prémium szintű storage-ban. A karbantartási esemény befejezése után az adatok visszatérhet a frissített Lsv2 virtuális gépek helyi NVMe tároló.

Helyi NVMe-lemezeken lévő adatokat forgatókönyvek a következők:

- A virtuális gép fut, és kifogástalan állapotban.
- A virtuális gép (amelyet Ön vagy Azure) helyen lehet újraindítani.
- A virtuális gép működése szünetel (leállított felszabadítási nélkül).
- A legtöbb karbantartási műveleteket a tervezett karbantartás.

Adatok védelme érdekében az ügyfél törölje biztonságosan forgatókönyvek a következők:

- A virtuális gép az újratelepítés, leállítva (felszabadítva), vagy törölte (,).
- A virtuális gép akkor kerül sérült, és a szolgáltatásnak van egy másik csomópontra hardverprobléma miatt javítása.
- A tervezett karbantartás karbantartási műveleteket kis számú, amely csak a virtuális gép egy másik gazdagépre kell osztani a karbantartáshoz.

Lehetőség a helyi storage szolgáltatásban tárolt adatok biztonsági mentésével kapcsolatos további információkért lásd: [biztonsági mentési és vész-helyreállítási Azure IaaS-lemezek](backup-and-disaster-recovery-for-azure-iaas-disks.md).

## <a name="frequently-asked-questions"></a>Gyakori kérdések

* **Hogyan kezdhetem meg, hogy Lsv2-sorozatú virtuális gépek üzembe helyezéséhez?**  
   Sokkal mint bármely más virtuális Gépet, használja a [portál](quick-create-portal.md), [Azure CLI-vel](quick-create-cli.md), vagy [PowerShell](quick-create-powershell.md) hozhat létre virtuális Gépet.

* **Hatására NVMe egyetlen lemezhiba esetén minden virtuális gép leáll a gazdagépen?**  
   Ha egy lemez meghibásodása a hardver csomóponton észlel, a hardver egy hibás állapotban van. Ha ez történik, a csomópont minden virtuális gép automatikusan fel lesznek szabadítva és kifogástalan állapotú csomópontba helyezésére. Lsv2 sorozatú virtuális gépek esetén ez azt jelenti, hogy, hogy az ügyfél adatainak a hibás csomóponton pedig biztonságosan törölve lesz, és újra létre kell hozni az új csomópont az ügyfélnek kell. Feljegyzett, mielőtt az élő áttelepítés válik elérhetővé a Lsv2, az adatok a hibás csomóponton proaktív módon átkerül a virtuális gépek szerint átkerülnek egy másik csomópontra.

* **Szükséges, a teljesítmény rq_affinity előtesztelést?**  
   Az rq_affinity érték kisebb adatelosztást az abszolút maximális bemeneti/kimeneti műveletek száma másodpercenként (IOPS) használatakor. Miután minden mást is működik, majd próbáljon meg rq_affinity megtekintheti, ha azt sokat számít 0-ra.

* **Van szükségem blk_mq beállításainak módosítása?**  
   RHEL/CentOS 7.x az NVMe-eszközök automatikusan blk-mq használja. Egy konfigurációmódosítás vagy a beállítások sem szükséges-e. A scsi_mod.use_blk_mq beállítás SCSI csak és Lsv2 előzetes verzió használata során lett megadva, mert az NVMe-eszközök nem jelenik meg a Vendég virtuális gépek, mint a SCSI-eszközök. Jelenleg az NVMe-eszközök láthatók NVMe-eszközöket, így a SCSI-blk-mq beállítás nincs jelentősége.

* **Kell módosítani a "fio"?**  
   Maximális iops-érték lekéréséhez és a egy mérési eszköz, például az L64v2 és L80v2 Virtuálisgép-méretek "fio" teljesítmény "rq_affinity" 0 értékre állítva minden NVMe-eszközön.  Ez a parancssor például állítsa a minden 10 NVMe-eszközök esetében nullának "rq_affinity" L80v2-beli virtuális gépen:

   ```console
   for i in `seq 0 9`; do echo 0 >/sys/block/nvme${i}n1/queue/rq_affinity; done
   ```

   Azt is vegye figyelembe, hogy a lehető legjobb teljesítményt kapjuk meg, amikor i/o történik közvetlenül a nyers NVMe-eszközökhöz a particionálás, nincs fájlrendszerek nem RAID 0 config, stb. Mielőtt hozzákezdene egy tesztelési munkamenetet, győződjön meg, hogy a konfigurációs friss/tiszta ismert állapotba futtatásával `blkdiscard` egyes az NVMe-eszközök.
   
## <a name="next-steps"></a>További lépések

* Tekintse meg az összes specifikációk [tároló-teljesítményre optimalizált virtuális gépek](sizes-storage.md) az Azure-ban

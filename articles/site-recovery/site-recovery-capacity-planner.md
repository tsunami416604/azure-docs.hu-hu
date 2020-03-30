---
title: Tervezze meg a Hyper-V vész-helyreállítási kapacitását az Azure Site Recovery segítségével
description: Ez a cikk a kapacitás becsléséhez, amikor az Azure Site Recovery szolgáltatás sal történő vészhelyreállítást állít fel.
author: rayne-wiselman
manager: carmonm
services: site-recovery
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: 843d5da26d6791cea880e5dfb654fe27b74f5d9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73936049"
---
# <a name="plan-capacity-for-hyper-v-vm-disaster-recovery"></a>A Hyper-V VM vész-helyreállítási kapacitásának megtervezése 

Az[Azure Site Recovery Deployment Planner] (site-recovery-hyper-v-deployment-planner.md) a Hyper-V és az Azure üzembe helyezéséhez a következőket biztosítja:

* Virtuális gép jogosultsági felmérése a lemezek száma, a lemezméret, az IOPS, a lemorzsolódás és néhány virtuális gép-jellemzők alapján
* A hálózatisávszélesség-igény és RPO-elemzés
* Azure infrastruktúra-követelmények
* Helyszíni infrastruktúra-követelmények
* Útmutató a kezdeti replikáció kötegeléséhez
* Becsült teljes vész-helyreállítási költség az Azure-ba


Az Azure Site Recovery capacity Planner segítségével meghatározhatja a kapacitásigényeket, amikor hyper-v virtuális gépeket replikál az Azure Site Recovery segítségével.

A Webhely-helyreállítási kapacitástervező vel elemezheti a forráskörnyezetet és a munkaterheléseket. Segít megbecsülni a sávszélesség-igényeket, a forráshelyhez szükséges kiszolgálói erőforrásokat, valamint a célhelyen szükséges erőforrásokat (például virtuális gépeket és tárhelyet).

Az eszközt kétféle kulccant üzemmódban futtathatja:

* **Gyors tervezés**: Hálózati és kiszolgálói előrejelzéseket biztosít a virtuális gépek, lemezek, tárolók és a változási sebesség átlagos száma alapján.
* **Részletes tervezés:** A virtuális gép szintjén az egyes munkaterhelések részleteit tartalmazza. Elemezze a virtuális gép kompatibilitását, és kapjon hálózati és kiszolgálói előrejelzéseket.

## <a name="before-you-start"></a>Előkészületek

* Adatokat gyűjthet a környezetről, beleértve a virtuális gépeket, a virtuális gépenkénti lemezeket, a lemezenkénti tárhelyet.
* Azonosítsa a replikált adatok napi változási (lemorzsolódási) sebességét. Töltse le a [Hyper-V kapacitástervező eszközt](https://www.microsoft.com/download/details.aspx?id=39057) a változási sebesség leigazolásához. [További információ](site-recovery-capacity-planning-for-hyper-v-replication.md) az eszközről. Azt javasoljuk, hogy futtassa ezt az eszközt egy héten keresztül az átlagok rögzítéséhez.


## <a name="run-the-quick-planner"></a>A gyorstervező futtatása
1. Töltse le és nyissa meg [a Site Recovery Capacity Planner -t.](https://aka.ms/asr-capacity-planner-excel) Makrókat kell futtatnia. Amikor a rendszer kéri, a szerkesztés és a tartalom engedélyezéséhez válasszon.

2. A **Tervezőtípus kiválasztása** listában válassza a **Gyorstervező lehetőséget.**

   ![Bevezetés](./media/site-recovery-capacity-planner/getting-started.png)

3. A **Kapacitástervező** munkalapon adja meg a szükséges adatokat. Töltse ki az összes pirosan bekarikázott mezőt a következő képernyőképen:

   a. A **Válassza ki a forgatókönyvet,** válassza a **Hyper-V az Azure** vagy **vmware/fizikai az Azure-ba.**

   b. A **Napi adatváltozási arány átlaga (%)** csoportban adja meg a [Hyper-V kapacitástervező eszközzel](site-recovery-capacity-planning-for-hyper-v-replication.md) vagy a [Hely-helyreállítási telepítéstervezővel](./site-recovery-deployment-planner.md)gyűjtött adatokat.

   c. A **tömörítési** beállítás nem használatos, ha hyper-V virtuális gépeket replikál az Azure-ba. A tömörítéshez használjon külső gyártótól származó készüléket, például a folyómedert.

   d. A **Megőrzés napokban**adja meg napokban, hogy mennyi ideig őrizze meg a replikákat.

   e. Az **On Number of Hours , amelyben a virtuális gépek kötegének kezdeti replikációja befejeződik,** és **a virtuális gépek száma kezdeti replikációs kötegenként**( Adja meg a kezdeti replikációs követelmények kiszámításához használt beállításokat). A Site Recovery telepítésekor a rendszer feltölti a teljes kezdeti adatkészletet.

   ![Bemenetek](./media/site-recovery-capacity-planner/inputs.png)

4. Miután megadta a forráskörnyezet értékeit, a megjelenített kimenet a következőket tartalmazza:

   * **A különbözeti replikációhoz szükséges sávszélesség (Megabit/mp-ben)**: A különbözeti replikáció hálózati sávszélessége a napi adatváltozás irásának átlaga alapján kerül kiszámításra.
   * **A kezdeti replikációhoz szükséges sávszélesség (Megabit/mp-ben)**: A kezdeti replikáció hálózati sávszélessége a megadott kezdeti replikációs értékek alapján kerül kiszámításra.
   * **Szükséges tárhely (gb-kban)**: A teljes Azure-tárhely szükséges.
   * **Teljes IOPS standard storage:** A szám számítása a teljes standard storage-fiókok 8K IOPS-egységmérete alapján kerül kiszámításra. A Gyorstervező esetében a szám kiszámítása az összes forrás virtuálisgép-lemez és a napi adatváltozási arány alapján történik. A részletes tervező, a szám kiszámítása alapján a szabványos Azure virtuális gépekhez leképezett virtuális gépek teljes száma és az adatok változási aránya a virtuális gépek.
   * **A szükséges standard szintű tárfiókok**száma: A virtuális gépek védelméhez szükséges szabványos tárfiókok teljes száma. Egy normál tárfiók legfeljebb 20 000 IOPS-t tarthat a szabványos tárolóban lévő összes virtuális gépen. Lemezenként legfeljebb 500 IOPS támogatott.
   * **Blob lemezek száma szükséges:** Az Azure storage-ban létrehozott lemezek száma.
   * **A szükséges prémium szintű fiókok**száma: A virtuális gépek védelméhez szükséges prémium szintű tárfiókok teljes száma. A magas IOPS-értékkel (20 000-nél nagyobb) rendelkező forrásvirtuális gépnek prémium szintű tárfiókra van szüksége. Egy prémium szintű tárfiók legfeljebb 80 000 IOPS-t képes tárolni.
   * **Teljes IOPS prémium szintű storage:** A szám számítása a 256K IOPS-egység mérete alapján a teljes prémium szintű tárfiókok. A Gyorstervező esetében a szám kiszámítása az összes forrás virtuálisgép-lemez és a napi adatváltozási arány alapján történik. A részletes tervező, a szám kiszámítása alapján a prémium szintű Azure virtuális gépek (DS és GS sorozat) és az adatváltozási arány a virtuális gépek en leképezve a virtuális gépek teljes száma alapján.
   * **A szükséges konfigurációs kiszolgálók száma**: Megmutatja, hogy hány konfigurációs kiszolgálóra van szükség a központi telepítéshez.
   * **További folyamatkiszolgálók száma szükséges**: Megmutatja, hogy szükség van-e további folyamatkiszolgálókra a konfigurációs kiszolgálón alapértelmezés szerint futó folyamatkiszolgálón kívül.
   * **100%-os további tárhely a Forrás:** Megmutatja, hogy szükség van-e további tárhelyre a forráshelyen.

      ![Kimenet](./media/site-recovery-capacity-planner/output.png)

## <a name="run-the-detailed-planner"></a>A részletes tervező futtatása

1. Töltse le és nyissa meg [a Site Recovery Capacity Planner -t.](https://aka.ms/asr-capacity-planner-excel) Makrókat kell futtatnia. Amikor a rendszer kéri, a szerkesztés és a tartalom engedélyezéséhez válasszon.

2. A **Tervezőtípus kiválasztása**párbeszédpanelen válassza a **Listamező Részletes tervező mezőjét.**

   ![Útmutató az első lépésekhez](./media/site-recovery-capacity-planner/getting-started-2.png)

3. A **Munkaterhelés minősítése** munkalapon adja meg a szükséges adatokat. Ki kell töltenie az összes megjelölt mezőt.

   a. A **processzormagok**ban adja meg a forráskiszolgálón lévő magok teljes számát.

   b. A **Memórialefoglalás (MB-kben)** adja meg a forráskiszolgáló RAM-méretét.

   c. A **Hálózati adapterek száma csoportban**adja meg a hálózati adapterek számát a forráskiszolgálón.

   d. A **Teljes tárterület (GB)**, adja meg a virtuális gép tároló teljes méretét. Ha például a forráskiszolgáló három, egyenként 500 GB-os lemezzel rendelkezik, a teljes tárterület mérete 1500 GB.

   e. A **Csatlakoztatott lemezek száma csoportban**adja meg a forráskiszolgáló lemezeinek teljes számát.

   f. A **Lemezkapacitás-kihasználtság (%)** csoportban adja meg az átlagos kihasználtságot.

   g. A **Napi adatváltozási arányban (%)** adja meg a forráskiszolgáló napi adatváltozási sebességét.

   h. Az **Azure virtuális gép méretének leképezése,** adja meg az Azure virtuális gép méretét, amely le szeretné képezni. Ha ezt nem szeretné manuálisan elvégezni, válassza **a Számítási iaaS virtuális gépek lehetőséget.** Ha kézi beállítást ad meg, majd a **Számítási iaaS virtuális gépek lehetőséget választja,** előfordulhat, hogy a manuális beállítás felülíródik. A számítási folyamat automatikusan azonosítja a legjobb egyezést az Azure virtuális gép mérete.

   ![Számítási feladatok képzettsége munkalap](./media/site-recovery-capacity-planner/workload-qualification.png)

4. Ha **a Számítási iaaS virtuális gépek lehetőséget választja,** a következőket teszi:

   * Ellenőrzi a kötelező bemeneteket.
   * Kiszámítja az IOPS-t, és a legjobb Azure-virtuálisgép-méretet javasolja minden olyan virtuális géphez, amely jogosult az Azure-ba való replikációra. Ha egy megfelelő méretű Azure virtuális gép nem észlelhető, egy hiba jelenik meg. Ha például a csatlakoztatott lemezek száma 65, egy hiba jelenik meg, mert az Azure virtuális gép legnagyobb mérete 64.
   * Egy Azure-beli virtuális géphez használható tárfiókot javasol.
   * Kiszámítja a számítási feladatokhoz szükséges standard szintű tárfiókok és prémium szintű tárfiókok teljes számát. Görgessen le az Azure-tárhely típusának és a forráskiszolgálóhoz használható tárfiók megtekintéséhez.
   * Befejezi és rendezi a tábla többi részét a virtuális géphez rendelt szükséges tárolási típus (standard vagy prémium díj) és a csatlakoztatott lemezek száma alapján. Az Azure követelményeinek megfelelő összes virtuális gép esetében az oszlop **a virtuális gép minősített?** **az Igen**oszlop ot mutatja. Ha egy virtuális gép nem lehet biztonsági másolatot az Azure-ba, egy hiba jelenik meg.

Az AA–AE oszlopok kimeneti és információkat nyújtanak az egyes virtuális gépekhez.

![AA–AE kimeneti oszlopok](./media/site-recovery-capacity-planner/workload-qualification-2.png)

### <a name="example"></a>Példa
Például hat virtuális gép a táblázatban látható értékekkel, az eszköz kiszámítja és hozzárendeli a legjobb Azure virtuális gép egyezést és az Azure storage-követelmények.

![Számítási feladatok képzettsége](./media/site-recovery-capacity-planner/workload-qualification-3.png)

* A példakimenetben vegye figyelembe a következőket:

  * Az első oszlop a virtuális gépek, lemezek és forgalomjegyzék érvényesítési oszlopa.
  * Két standard szintű tárfiókok és egy prémium szintű tárfiók öt virtuális gépek.
  * VM3 doesn't qualify for protection because one or more disks are more than 1 TB.
  * A VM1 és a VM2 használhatja az első standard szintű tárfiókot
  * A VM4 használhatja a második standard szintű tárfiókot.
  * VM5 és VM6 kell egy prémium szintű tárfiókot, és mindkettő használhatja egyetlen fiókot.

    > [!NOTE]
    > IOPS standard és prémium szintű storage a virtuális gép szintjén számítják ki, és nem lemez szinten. Egy szabványos virtuális gép lemezenként legfeljebb 500 IOPS-t képes kezelni. Ha egy lemez IOPS nagyobb, mint 500, prémium szintű tárhelyre van szüksége. Ha egy lemez IOPS-a több mint 500, de az IOPS az összes virtuálisgép-lemezek a támogatási szabványos Azure virtuális gép korlátok, a tervező egy szabványos virtuális gép, és nem a DS vagy gs sorozat. (Az Azure vm-korlátok a virtuális gép mérete, a lemezek száma, az adapterek száma, a PROCESSZOR és a memória.) Manuálisan kell frissítenie a leképezési Azure méretcella a megfelelő DS vagy GS sorozat virtuális gép.


Miután az összes adatot megadta, válassza **az Adatok küldése a tervező eszköznek** lehetőséget a Kapacitástervező megnyitásához. A munkaterhelések ki vannak emelve, hogy megmutassák, jogosultak-e a védelemre.

### <a name="submit-data-in-capacity-planner"></a>Adatok küldése a Kapacitástervezőben
1. A **Kapacitástervező** munkalap megnyitásakor a rendszer a megadott beállítások alapján tölti ki. A "Munkaterhelés" szó megjelenik az **Infra bemenetek** forráscellájában, amely azt mutatja, hogy a bemenet a **Számítási feladatok minősítése** munkalap.

2. Ha módosításokat szeretne végrehajtani, módosítania kell a **Munkaterhelés minősítése** munkalapot. Ezután ismét válassza **az Adatok küldése a tervezőeszköznek** lehetőséget.

   ![Kapacitástervező](./media/site-recovery-capacity-planner/capacity-planner.png)

## <a name="next-steps"></a>További lépések
További információ a kapacitástervezési eszköz [futtatásáról.](site-recovery-capacity-planning-for-hyper-v-replication.md)

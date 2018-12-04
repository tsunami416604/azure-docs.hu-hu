---
title: Kapacitás megtervezése a Hyper-V vészhelyreállítás az Azure Site Recoveryvel |} A Microsoft Docs
description: Ez a cikk segítségével megbecsülheti a kapacitás beállítása az Azure Site Recovery szolgáltatással vészhelyreállítás során.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: raynew
ms.openlocfilehash: d8ba4fa1b5f5efd671c13ad2201b0cd34642d346
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52844940"
---
# <a name="plan-capacity-for-hyper-v-vm-disaster-recovery"></a>Kapacitás megtervezése a Hyper-V-beli virtuális gépek vészhelyreállításához 

A továbbfejlesztett verziójának [Azure Site Recovery Deployment Planner a Hyper-V – Azure-beli](site-recovery-hyper-v-deployment-planner.md) már elérhető. Lecseréli a régi eszközt. Az új eszköz használata az üzembe helyezés megtervezése.
Az eszköz biztosítja a következő irányelveket:

* Virtuális gép jogosultságfelmérése a lemezek, lemezméretek, IOPS, adatváltozás és néhány Virtuálisgép-jellemző száma alapján
* Hálózatisávszélesség-igény és RPO-elemzés
* Az Azure infrastruktúra-követelmények
* A helyszíni infrastruktúrával kapcsolatos követelmények
* A kezdeti replikáció kötegeléséhez útmutató
* Becsült teljes vészhelyreállítási költség Azure-bA


Az Azure Site Recovery Capacity Planner segítségével határozható meg, hogy a kapacitásigények Hyper-V virtuális gépek az Azure Site Recovery replikálja.

Site Recovery Capacity Planner segítségével a forráskörnyezetét és a számítási feladatok elemzése. Ez segít megbecsülni a sávszélesség-igény, a kiszolgáló erőforrásait, a forráshely van szüksége, és az erőforrások (például virtuális gépek és tárolási) van szüksége a célhelyen.

Az eszköz két üzemmódban futtatható:

* **Gyors tervezési**: virtuális gépek, a lemezeket, a storage és az adatváltozási sebesség átlagos számán alapuló, hálózat- és leképezések biztosít.
* **Részletes tervezési**: a virtuális gép szintjén minden számítási feladat részletesen ismerteti. Virtuális gép kompatibilitási elemzése, és a hálózat- és leképezések beolvasása.

## <a name="before-you-start"></a>Előkészületek

* A környezetben, beleértve a virtuális gépek, virtuális Gépet, a lemezenkénti tárterületeket lemezek információt gyűjteni.
* Azonosítsa a napi (forgalom) változási replikált adatok tárolására. Töltse le a [Hyper-V kapacitástervezési eszköz](https://www.microsoft.com/download/details.aspx?id=39057) változási beolvasásához. [További információ](site-recovery-capacity-planning-for-hyper-v-replication.md) az eszközről. Azt javasoljuk, hogy egy adott héten átlagokat rögzítéséhez futtatja az eszközt.


## <a name="run-the-quick-planner"></a>A gyors Planner futtatása
1. Töltse le és nyissa meg a [Site Recovery Capacity Planner](https://aka.ms/asr-capacity-planner-excel). Makrók futtatni szeretné. Amikor a rendszer kéri, adja meg a beállításokat a Szerkesztés engedélyezése és tartalmát.

2. Az a **planner típusának kiválasztása** válassza ki a lista **gyors Planner**.

   ![Bevezetés](./media/site-recovery-capacity-planner/getting-started.png)

3. Az a **Capacity Planner** munkalapra, adja meg a szükséges információkat. Töltse ki az alábbi képernyőképen pirossal bekarikázva minden mező:

   a. A **válassza ki a forgatókönyv**, válassza a **Hyper-V – Azure** vagy **VMware/fizikai az Azure-bA**.

   b. A **átlagos napi adatváltozási sebessége (%)**, adja meg a segítségével gyűjtött információkat a [Hyper-V kapacitástervezési eszköz](site-recovery-capacity-planning-for-hyper-v-replication.md) vagy [Site Recovery Deployment Planner](./site-recovery-deployment-planner.md).

   c. A **tömörítési** beállítást nem használja, ha az Azure-bA replikált Hyper-V virtuális gépek. Tömörítés használja a külső-készülék, például a riverbed szolgáltatással.

   d. A **megőrzési napjainak**, adja meg napokban, mennyi ideig megőrzi a replikákat.

   e. A **hajtsa végre a köteghez tartozó virtuális gépek mely kezdeti replikációs órák száma** és **virtuális gépek kezdeti replikációja kötegenkénti száma**, adja meg a beállításokat, amelyek számítási Kezdeti replikációs követelményeit. A Site Recovery üzembe lett helyezve, amikor a teljes kezdeti adatkészlet van feltöltve.

   ![Bemenetek](./media/site-recovery-capacity-planner/inputs.png)

4. Miután megadta az értékeket a forráskörnyezethez, a megjelenő kimenet tartalmazza:

   * **(A megabit/másodperc) változásreplikációjához szükséges sávszélesség**: átlagos napi adatváltozási sebesség kiszámítása a hálózati sávszélesség a változásreplikáláshoz.
   * **A kezdeti replikációhoz (a megabit/másodperc) szükséges sávszélességet**: a kezdeti replikáció értékek kiszámítása a hálózati sávszélesség a kezdeti replikációhoz.
   * **Tárolás szükséges (GB)**: az összes Azure storage szükséges.
   * **Standard szintű tárolóban működő IOPS teljes**: A szám a 8 KB IOPS egység méretét a teljes standard storage-fiókok alapján kiszámítja. A gyors Planner a szám alapján számítja ki a forrás virtuális gép az összes lemez és a napi adatváltozási gyakoriság. A részletes Planner száma alapján lesz kiszámítva vannak rendelve a standard szintű Azure-beli virtuális gépek teljes száma, és az adatváltozási sebessége a virtuális gépeken.
   * **A standard szintű tárfiókok szükséges**: a virtuális gép védelméhez szükséges a standard szintű tárfiókok teljes száma. A standard szintű tárfiók tartalmazhat legfeljebb 20 000 iops-t minden virtuális gépen standard storage-ban. Legfeljebb 500 IOPS lemezenként támogatott.
   * **Blob szükséges lemezek száma**: az Azure storage létrehozott lemezek számát.
   * **Szükséges prémium szintű tárfiókok száma**: a virtuális gép védelméhez szükséges prémium szintű tárfiókok teljes száma. A virtuális gép magas iops (több mint 20 000) van szüksége a premium storage-fiók. Premium storage-fiók 80 000 IOPS képes tárolni.
   * **A Premium Storage IOPS teljes**: A szám kiszámítása a teljes premium storage-fiókok 256 K IOPS-egységet méret alapján. A gyors Planner a szám alapján számítja ki a forrás virtuális gép az összes lemez és a napi adatváltozási gyakoriság. A részletes Planner a szám a prémium szintű Azure virtuális gépek (DS vagy GS sorozat) rendelt virtuális gépek teljes száma alapján számoljuk, és az adatváltozási sebessége a virtuális gépeken.
   * **Szükséges konfigurációs kiszolgálók száma**: látható, a telepítéshez szükséges konfigurációs kiszolgálók számát.
   * **További Folyamatkiszolgálók szükséges számú**: bemutatja, hogy a folyamatkiszolgáló alapértelmezés szerint a konfigurációs kiszolgálón futó mellett szükség-e további folyamatkiszolgálók.
   * **100 %-os tárhely, a forrás**: bemutatja, hogy szükséges-e további tárterületet a forráshelyen.

      ![Kimenet](./media/site-recovery-capacity-planner/output.png)

## <a name="run-the-detailed-planner"></a>A részletes Planner futtatása

1. Töltse le és nyissa meg a [Site Recovery Capacity Planner](https://aka.ms/asr-capacity-planner-excel). Makrók futtatni szeretné. Amikor a rendszer kéri, adja meg a beállításokat a Szerkesztés engedélyezése és tartalmát.

2. A **egy planner-típus kiválasztása**válassza **részletes Planner** a legördülő listából.

   ![Útmutató az első lépésekhez](./media/site-recovery-capacity-planner/getting-started-2.png)

3. Az a **munkaterhelés minősítési** munkalapra, adja meg a szükséges információkat. Ki kell töltenie az összes megjelölt mező.

   a. A **Processzormagok**, adja meg a magok teljes száma a forráskiszolgálón.

   b. A **lefoglalt memória (a MB-ban)**, adja meg a forráskiszolgáló RAM méretét.

   c. A **hálózati adapterek száma**, adja meg a hálózati adapterek számát a forráskiszolgálón.

   d. A **összes tárterület (GB)**, adja meg a VM-tárterület teljes méretét. Például ha a forráskiszolgáló minden 500 GB-os három lemezt, tárterületének teljes mérete 1500 GB lesz.

   e. A **csatlakoztatott lemezek száma**, adja meg a forráskiszolgáló lemezek teljes száma.

   f. A **tárolókapacitás kihasználtságát (%) lemez**, adja meg az átlagos kihasználtság.

   g. A **napi adatváltozási gyakoriság (%)**, adja meg a napi adatváltozási gyakoriság a forráskiszolgáló.

   h. A **leképezése az Azure Virtuálisgép-méret**, adja meg a leképezni kívánt Azure Virtuálisgép-méretet. Ha nem szeretné, ez megoldható manuálisan, válassza ki a **számítási IaaS virtuális gépek**. Ha a manuális beállítást adja, és válassza ki **számítási IaaS virtuális gépek**, felülírhatják a manuális beállítást. A számítási folyamat automatikusan meghatározza a legmegfelelőbb az Azure virtuális gép méretét.

   ![Számítási feladatok minősítési munkalap](./media/site-recovery-capacity-planner/workload-qualification.png)

4. Ha **számítási IaaS virtuális gépek**, azt a következő:

   * A kötelező bemenet érvényesíti.
   * Kiszámítja az iops-t, és az Azure virtuális gép mérete számára legmegfelelőbb minden virtuális gép, amely egy Azure-ba történő javasol. Ha egy megfelelő méretet az Azure virtuális gép nem észlelhető hiba jelenik meg. Például ha csatlakoztatott lemezek száma 65, hibát jeleníti meg, mert egy Azure virtuális gép legnagyobb mérete a 64.
   * Storage-fiók, amely egy Azure virtuális gép használható javasol.
   * Kiszámítja a standard szintű storage-fiókok és a premium storage-fiókok szükséges a számítási feladatok teljes száma. Az Azure storage típusát és a storage-fiók, amely a forráskiszolgáló használható megtekintéséhez görgessen le.
   * Befejeződött, és az a táblázat többi része a szükséges tárolótípus (standard vagy prémium szintű) hozzárendelt virtuális gép és a csatlakoztatott lemezek száma alapján rendezi. Az összes virtuális gép, amelyek megfelelnek a követelményeknek, az Azure-hoz, az oszlop **van virtuális gép minősített?** látható **Igen**. Ha egy virtuális gép nem készíthető biztonsági másolat az Azure-ba, hiba jelenik meg.

Oszlopok AA AE bocsát ki, és adjon meg információt minden virtuális Géphez.

![Kimeneti oszlopok AA AE](./media/site-recovery-capacity-planner/workload-qualification-2.png)

### <a name="example"></a>Példa
Tegyük fel, a táblázatban látható értékekkel hat virtuális gépek számára az eszköz számítja ki, és hozzárendeli a legmegfelelőbb Azure virtuális Gépen, és az Azure storage követelményeit.

![Számítási feladatok minősítési hozzárendelések](./media/site-recovery-capacity-planner/workload-qualification-3.png)

* A példa a kimenetre vegye figyelembe a következőket:

  * Az első oszlop a virtuális gépek, a lemezek és a forgalom érvényesítési oszlop.
  * Öt virtuális gépek két standard szintű storage-fiókok és a egy premium storage-fiók szükséges.
  * Vm3 virtuális gép nem jogosultak a védelmet, mert egy vagy több lemez több mint 1 TB.
  * A VM1, VM2 és használhatja az első standard szintű storage-fiók
  * VM4 második standard szintű storage-fiókot is használhatja.
  * VM5 és VM6 kell egy prémium szintű storage-fiókot, és mindkét egyetlen fiókot is használhat.

    > [!NOTE]
    > A standard és prémium szintű storage IOPS számítják ki a virtuális gép szintjén, és a lemez szintjén nem. Egy standard virtuális gép legfeljebb 500 IOPS lemezenként képes kezelni. Ha a lemez iops-érték 500-nál nagyobb, prémium szintű storage kell. Ha a lemez iops-érték több mint 500, de iops-t a teljes virtuális gép lemezeivel kapcsolatban támogatási szabványos Azure virtuális gép határértékeken belül van, a planner választja ki egy standard virtuális Gépet, és nem a DS vagy GS-sorozat. (Az Azure virtuális gépekre korlátai Virtuálisgép-méretet, lemezek, az adapterek, a Processzor és memória száma is.) A megfelelő DS vagy GS sorozatú virtuális gépek a leképezés Azure mérete cella manuálisan frissíteni kell.


Után minden információt is meg kell adni, jelölje be az **a planner eszköz adatokat küldenek** Capacity Planner megnyitásához. Számítási feladatok e azok jogosult protection megjelenítéséhez ki vannak emelve.

### <a name="submit-data-in-capacity-planner"></a>A Capacity Planner adatok küldése
1. Amikor megnyitja a **Capacity Planner** munkalapra, azt a megadott beállítások alapján van feltöltve. A szó "Számítási feladat" jelenik meg a **Infra bemeneti forrás** cella mutatja be, hogy a bemeneti a **munkaterhelés minősítési** munkalapon.

2. Ha azt szeretné, módosíthatja, módosítania a **munkaterhelés minősítési** munkalap. Válassza ki **a planner eszköz adatokat küldenek** újra.

   ![Capacity Planner](./media/site-recovery-capacity-planner/capacity-planner.png)

## <a name="next-steps"></a>További lépések
[Ismerje meg, hogyan futtathat](site-recovery-capacity-planning-for-hyper-v-replication.md) a kapacitástervezés eszközt.

---
title: "Becsült replikációs kapacitás az Azure-ban |} Microsoft Docs"
description: "Ez a cikk kapacitása becsléséhez Azure Site Recovery segítségével a replikált használata"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 0a1cd8eb-a8f7-4228-ab84-9449e0b2887b
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/28/2017
ms.author: nisoneji
ms.openlocfilehash: bfeefde53aa2b3645934f068d580c0714714dd69
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="plan-capacity-for-protecting-hyper-v-vms-with-site-recovery"></a>A Site Recovery szolgáltatással a Hyper-V virtuális gépek védelméhez kapacitásának megtervezése

A továbbfejlesztett verziójának [Azure hely helyreállítási telepítési Planner for Hyper-V számára az Azure-telepítés](site-recovery-hyper-v-deployment-planner.md) érhető el. Lecseréli a régi eszköz. Az új eszköz használata a központi telepítésének megtervezése. Az eszközt biztosít a következő irányelveket:

* Virtuális gép jogosultsági assessment, a lemezek számát, lemez méretét, IOPS, forgalmának kezeléséhez, és néhány virtuális gép jellemzők alapján
* Hálózati sávszélesség és a helyreállítási Időkorlát assessment kell
* Az Azure-infrastruktúrával kapcsolatos követelmények
* A helyszíni infrastruktúrával kapcsolatos követelmények
* Kezdeti replikálás kötegelés útmutató
* Becsült teljes vész helyreállítási költségének az Azure-bA


Az Azure Site Recovery Capacity Planner segítségével határozható meg, hogy a kapacitásigények Hyper-V virtuális gépek az Azure Site Recovery szolgáltatással replikálja.

Site Recovery Capacity Planner segítségével elemezheti a forráskörnyezetét és a munkaterhelések. Segít sávszélesség igényei meghatározásához, a kiszolgáló erőforrásait, a forráshely van szüksége, és az erőforrások (például virtuális gépek és tárolás) van szüksége a célhelyen.

Az eszköz két módban is futtathatja:

* **Gyors tervezési**: biztosítja a hálózat- és leképezések virtuális gépeket, a lemezek, a tároló és a változási sebessége átlagos száma alapján.
* **Részletes tervezési**: részletesen bemutatja a virtuális gép szinten minden munkaterhelés. Virtuális gép kompatibilitási elemzéséhez, és a hálózat- és leképezések beolvasása.

## <a name="before-you-start"></a>Előkészületek

* A környezetben, beleértve a virtuális gépek, virtuális gép, lemezenkénti lemezeket információt gyűjteni.
* Azonosítsa a replikált adatok napi adatváltozás (forgalom) sebessége. Töltse le a [Hyper-V kapacitástervezési eszköz](https://www.microsoft.com/download/details.aspx?id=39057) lekérni a változási sebessége. [További](site-recovery-capacity-planning-for-hyper-v-replication.md) erről az eszközről. Azt javasoljuk, hogy az eszköz rögzíteni átlagok hetente keresztül.
   

## <a name="run-the-quick-planner"></a>A gyors Planner
1. Töltse le és nyissa meg a [Site Recovery Capacity Planner](http://aka.ms/asr-capacity-planner-excel). Makrók futtatásához szükséges. Amikor a rendszer kéri, adja meg a Szerkesztés engedélyezése és a tartalom beállításokat.

2. Az a **planner-típus kiválasztása** listában válasszon **gyors Planner**.

   ![Bevezetés](./media/site-recovery-capacity-planner/getting-started.png)

3. Az a **Capacity Planner** munkalap, adja meg a szükséges adatokat. Az alábbi képernyőfelvételen a piros körben minden mező kitöltése:

   a. A **válassza ki a forgatókönyv**, válassza a **Hyper-V Azure** vagy **VMware vagy fizikai Azure**.

   b. A **átlagos napi módosulása aránya (%)**, adja meg az adatokat, akkor gyűjtse össze a [Hyper-V kapacitástervezési eszköz](site-recovery-capacity-planning-for-hyper-v-replication.md) vagy [Site Recovery telepítési Planner](./site-recovery-deployment-planner.md). 

   c. A **tömörítés** nem használja a beállítást, ha a Hyper-V virtuális gépek replikálása az Azure-bA. Tömörítési használjon egy külső készülék például Riverbed.

   d. A **napban megőrzési**, mennyi ideig szeretné megőrizni a replikák nap.

   e. A **kell végeznie a kezdeti replikálást melyik virtuális gépek a köteg órák száma** és **számú virtuális gép kezdeti replikációs kötegenként**, adja meg a kiszámításához használt beállítások kezdeti replikálás követelményeinek. Ha a Site Recovery lett telepítve, a teljes kezdeti adatkészlet feltöltődött.

   ![Bemenetek](./media/site-recovery-capacity-planner/inputs.png)

4. Akkor adja meg az értékeket a forráskörnyezetben, miután a megjelenített kimenete:

   * **Sávszélességre van szüksége (a megabit/másodperc) változásreplikálás**: változásreplikálás hálózati sávszélességet a átlagos napi adatváltozási sebesség számolja ki.
   * **A kezdeti replikáláshoz (a megabit/másodperc) a szükséges sávszélesség**: hálózati sávszélesség a kezdeti replikáláshoz megadott kezdeti replikálás értékek számolja ki.
   * **Minimális (GB) tárolási**: A teljes Azure tárterület szükséges.
   * **A Standard tárolási IOPS teljes**: A szám a 8 KB-os IOPS-méretét a teljes standard tárfiókokban alapján kiszámítja. A gyors Planner, az a szám alapján van kiszámítva a forrás virtuális gép lemezeivel és napi adatváltozási sebessége. A részletes Planner a szám alapján van kiszámítva leképezett szabványos Azure virtuális gépeken futó virtuális gépek száma és az adatok és a virtuális gépek módosítása.
   * **Standard szintű storage-fiókok szükséges száma**: a virtuális gépek védelméhez szükséges szabványos tárfiókok teljes száma. Standard szintű tárfiók tárolására képes legfeljebb 20 000 IOPS között a virtuális gépek a standard szintű tárolót. Legfeljebb 500 iops-érték lemezenként támogatott.
   * **A Blob szükséges lemezek számát**: az Azure storage a létrehozott lemezek számát.
   * **Prémium szintű fiókok szükséges**: prémium szintű storage-fiókok a virtuális gépek védelméhez szükséges teljes száma. A forrás virtuális gép magas iops-érték (több mint 20 000) és a prémium szintű tárfiók kell. A prémium szintű tárfiók akár 80000 IOPS tárolására képes.
   * **A prémium szintű Storage IOPS teljes**: A szám kiszámítja a 256 KB-os IOPS egység méretét a teljes prémium szintű storage-fiókok. A gyors Planner, az a szám alapján van kiszámítva a forrás virtuális gép lemezeivel és napi adatváltozási sebessége. A részletes Planner a szám alapján van kiszámítva a virtuális gépek, a prémium szintű Azure virtuális gépeken (DS és GS-sorozat) rendelt teljes számát és az adatok és a virtuális gépek módosítása.
   * **Szükséges konfigurációs kiszolgálók száma**: látható, a telepítéshez szükséges konfigurációs kiszolgálók számát.
   * **Szükséges további folyamat kiszolgálók száma**: jeleníti meg, hogy a folyamatkiszolgáló, alapértelmezés szerint a konfigurációs kiszolgálón futó mellett szükség-e további folyamat kiszolgálók.
   * **További tárhely 100 %-át a forrás**: jeleníti meg, hogy a további tárhely szükséges-e a forráshelyen.

      ![Kimenet](./media/site-recovery-capacity-planner/output.png)

## <a name="run-the-detailed-planner"></a>A részletes Planner

1. Töltse le és nyissa meg a [Site Recovery Capacity Planner](http://aka.ms/asr-capacity-planner-excel). Makrók futtatásához szükséges. Amikor a rendszer kéri, adja meg a Szerkesztés engedélyezése és a tartalom beállításokat.

2. A **planner-típus kiválasztása**, jelölje be **részletes Planner** a legördülő listából.

   ![Útmutató az első lépésekhez](./media/site-recovery-capacity-planner/getting-started-2.png)

3. Az a **munkaterhelés minősítési** munkalap, adja meg a szükséges adatokat. Meg kell adnia a megjelölt mezőket.

   a. A **Processzormagok**, adja meg az magok teljes száma a forráskiszolgálón.

   b. A **memóriafoglalása (MB-ban)**, adja meg a RAM memória méretét a forráskiszolgáló.

   c. A **hálózati adapterek száma**, a hálózati adapterek számát adja meg a forráskiszolgálón.

   d. A **teljes tárolás (GB)**, adja meg a Virtuálisgép-tároló teljes méretét. Például ha a forráskiszolgálóra 500 GB három lemezt, a tárterület teljes méretét pedig 1500 GB.

   e. A **csatlakoztatott lemezek számát**, adja meg a forráskiszolgáló lemezeinek teljes száma.

   f. A **lemez a tárolókapacitás kihasználtságát (%)**, adja meg az átlagos kihasználtság.

   g. A **napi adatváltozási sebessége (%)**, adja meg, napi adatváltozási sebessége eltér a forráskiszolgáló nevétől.

   h. A **leképezési Azure Virtuálisgép-méretet**, adja meg a leképezni kívánt Azure Virtuálisgép-méretet. Ha nem szeretné elvégezni, válassza ki a **számítási IaaS virtuális gépeket**. Ha a bemeneti kézi beállítás, és válassza ki **számítási IaaS virtuális gépeket**, a manuális beállítás felülíródhatnak. A számítási folyamat automatikusan azonosítja a legmegfelelőbb az Azure virtuális gép méretét.

   ![Munkaterhelés minősítési munkalap](./media/site-recovery-capacity-planner/workload-qualification.png)

4. Ha **számítási IaaS virtuális gépeket**, itt a hatása van:

   * A kötelező bemeneti ellenőrzi.
   * Kiszámítja az IOPS, és az Azure virtuális gép mérete legmegfelelőbb az egyes virtuális gépek, amelyek jogosultak a replikálás az Azure-bA javasol. Ha egy megfelelő méretű Azure virtuális gép nem észlelhető hiba jeleníti meg. Például ha a csatlakoztatott lemezek száma 65, hibát jeleníti meg, mert a legmagasabb egy Azure virtuális gép mérete 64.
   * A storage-fiók, amely egy Azure virtuális gép nem használható javasol.
   * Kiszámítja a standard szintű storage-fiókok és a prémium szintű storage-fiókok a munkaterhelés számára szükséges teljes száma. Görgessen le az Azure storage típusának és a storage-fiók, amely a forráskiszolgáló is használható.
   * Befejeződött, és a többi hozzárendelése a virtuális gépek és a csatlakoztatott lemezek számát szükséges tárolási típusát (standard vagy prémium) alapján rendezi. Az összes virtuális gépet, amely az Azure, az oszlop követelményeinek **van VM minősíteni?** látható **Igen**. Ha a virtuális gépek biztonsági mentése nem végezhető az Azure-ba, hiba jelenik meg.

Oszlopok AA AE kimenete, és adja meg az egyes virtuális gépek információkat.

![Kimeneti oszlopok AA AE](./media/site-recovery-capacity-planner/workload-qualification-2.png)

### <a name="example"></a>Példa
Tegyük fel, a táblázatban látható értékekkel hat virtuális gépek esetén az eszköz számítja ki, és hozzárendeli a legmegfelelőbb Azure virtuális gép és az Azure tárolási követelmények érvényesek.

![Munkaterhelés minősítési hozzárendelések](./media/site-recovery-capacity-planner/workload-qualification-3.png)

* A példa az alábbiakat vegye figyelembe a következőket:

  * Az első oszlop a virtuális gépek, a lemezek és a forgalom érvényesítési oszlop.
  * Öt virtuális gépek két szabványos storage-fiókok és egy prémium szintű tárfiók van szükség.
  * VM3 nem jogosultak a védelmet, mert egy vagy több lemez 1 TB-nál több.
  * VM1 és vm2 virtuális gépnek használhatja az első standard szintű tárfiók
  * VM4 használhatja a második standard szintű tárfiók.
  * VM5 és VM6 van szüksége a prémium szintű tárfiók, és egyaránt használhatja ugyanazt a fiókot.

    > [!NOTE]
    > A standard és prémium szintű storage IOPS kiszámítása a virtuális gép szintjén, és nem szabad szinten. Szabványos a virtuális gép legfeljebb 500 iops-érték lemezenként képes kezelni. Ha egy lemez IOPS még nagyobb, mint 500, prémium szintű storage kell. Ha IOPS egy lemez legfeljebb 500, de a teljes méretű lemezek IOPS szabványos Azure virtuális gép támogatási korlátok, a planner választja ki egy szabványos virtuális Gépet, és nem a DS vagy a GS adatsorozat. (Az Azure virtuális gép korlátok Virtuálisgép-méretet, lemezek, adapterek, CPU és memória számát is.) Frissítenie kell manuálisan a leképezési Azure mérete cella adatsorozattal a megfelelő DS vagy a GS virtuális gép.


Után az összes információt is meg kell adni, válasszon **a planner eszköz adatok küldése** Capacity Planner megnyitásához. Munkaterhelések megjelenítése, hogy fontosságúak védelemre jogosult vannak kiemelve.

### <a name="submit-data-in-capacity-planner"></a>A Capacity Planner adatok küldése
1. Amikor megnyitja a **Capacity Planner** munkalap, a telepítéskor megadott beállítások alapján. A word "Munkaterhelési" jelenik meg a **Infra bemeneti forrás** mutatja be, hogy a bemeneti cella a **munkaterhelés minősítési** munkalap.

2. Ha szeretné módosítani, módosítania kell a **munkaterhelés minősítési** munkalapon. Válassza ki **a planner eszköz adatok küldése** újra. 

   ![Capacity Planner](./media/site-recovery-capacity-planner/capacity-planner.png)

## <a name="next-steps"></a>További lépések
[Ismerje meg, hogyan futtathat](site-recovery-capacity-planning-for-hyper-v-replication.md) a kapacitástervezés eszköz.

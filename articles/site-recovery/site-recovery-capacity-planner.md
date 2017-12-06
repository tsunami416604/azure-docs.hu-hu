---
title: "Becsült replikációs kapacitás az Azure-ban |} Microsoft Docs"
description: "Ez a cikk kapacitása becsléséhez replikálása az Azure Site Recovery esetén használja"
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
ms.openlocfilehash: f504888aac9e8d97e974fb5bec0a12a8ede39c76
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2017
---
Új javított változata [Azure Site Recovery telepítési planner Hyper-v Azure](site-recovery-hyper-v-deployment-planner.md) érhető el, és a régi eszköz helyett. Az új eszköz használata a központi telepítésének megtervezése. Az eszköz biztosít a következő eszközöket:
* Virtuális gép jogosultsági értékelése, lemezek, lemez méretét, IOPS, forgalom és néhány virtuális gép jellemzők száma alapján.
* Hálózati sávszélesség és a helyreállítási Időkorlát assessment kell.
* Az Azure-infrastruktúrával kapcsolatos követelményei.
* A helyszíni infrastruktúrával kapcsolatos követelményei.
* Kezdeti replikálás kötegelés útmutatást.
* Az Azure-bA összköltsége vész-Helyreállítási becsült.

# <a name="plan-capacity-for-protecting-hyper-v-vms-with-site-recovery"></a>A Site Recovery szolgáltatással a Hyper-V virtuális gépek védelméhez kapacitásának megtervezése

Az Azure Site Recovery Capacity Planner eszköz segítségével mérje fel, a kapacitásigények, az Azure Site Recovery szolgáltatással a Hyper-V virtuális gépek replikálása esetén.

A Site Recovery Capacity Planner segítségével elemezheti a forráskörnyezetét és a munkaterhelések, a sávszélesség igényeket, és kiszolgáló-erőforrás lesz szüksége a forráshely és a (virtuális gép és tárterület stb.), szükséges erőforrások a célhelyen .

Néhány módok futtathatja az eszközt:

* **Gyors tervezési**: futtatás ebben a módban a hálózat- és leképezések megszerezni az eszköz a virtuális gépek, a lemezek, a tárolás és a változási sebessége átlagos száma alapján.
* **Részletes tervezési**: Ebben a módban futtassa az eszközt, és adja meg a virtuális gép szinten minden munkaterhelés részleteit. Virtuális gép kompatibilitási elemzéséhez, és a hálózat- és leképezések beolvasása.

## <a name="before-you-start"></a>Előkészületek


1. A környezetben, beleértve a virtuális gépek, virtuális gép, lemezenkénti lemezeket információt gyűjteni.
2. Azonosítsa a replikált adatok napi adatváltozás (forgalom) sebessége. Ehhez az szükséges, töltse le a [Hyper-V kapacitástervezési eszköz](https://www.microsoft.com/download/details.aspx?id=39057) lekérni a változási sebessége. [További](site-recovery-capacity-planning-for-hyper-v-replication.md) erről az eszközről. Azt javasoljuk, hogy rögzíteni átlagok hetente keresztül futtatja az eszközt.
   

## <a name="run-the-quick-planner"></a>A gyors Planner
1. Töltse le és nyissa meg a [Azure Site Recovery Capacity Planner](http://aka.ms/asr-capacity-planner-excel) eszköz. Makrók, ezért select szerkesztésének engedélyezése és tartalmakhoz, ha a rendszer kéri futtatásához szükséges.
2. A **planner-típus kiválasztása** válasszon **gyors Planner** a legördülő listából.

   ![Bevezetés](./media/site-recovery-capacity-planner/getting-started.png)
3. Az a **Capacity Planner** munkalap, adja meg a szükséges adatokat. Meg kell adnia az alábbi képernyőképen látható piros körben összes mezőt.

   * A **válassza ki a forgatókönyv**, válassza a **Hyper-V Azure** vagy **VMware vagy fizikai Azure**.
   * A **átlagos napi módosulása aránya (%)**, helyezze az adatokat gyűjtse össze a használatával a [Hyper-V kapacitástervezési eszköz](site-recovery-capacity-planning-for-hyper-v-replication.md) vagy a [Azure Site Recovery telepítési Planner](./site-recovery-deployment-planner.md).  
   * A **tömörítés** nem használja a beállítást, ha a Hyper-V virtuális gépek replikálása Azure-bA. Tömörítési használjon egy külső készülék például Riverbed.
   * A **megőrzési bemenetek**, adja meg, hogy mennyi ideig replikák meg kell őrizni, órákban.
   * A **kell végeznie a kezdeti replikálást melyik virtuális gépek a köteg órák száma** és **számú virtuális gép kezdeti replikációs kötegenként**, megadott kezdeti replikációs követelményektől kiszámításához használt beállításokat.  Ha a Site Recovery lett telepítve, a teljes kezdeti adatkészlet fel kell tölteni.

   ![Bemenetek](./media/site-recovery-capacity-planner/inputs.png)
4. A forrás környezetében értékeit helyezett után megjelenő kimeneti tartalmazza:

   * **Változásreplikálás szükséges sávszélesség** (MB/s). Változásreplikálás hálózati sávszélességet a átlagos napi adatváltozási sebesség számolja ki.
   * **A kezdeti replikáláshoz szükséges sávszélesség** (MB/s). Hálózati sávszélesség a kezdeti replikálás a kezdeti replikáció értékek helyezett számolja ki.
   * **Minimális (GB) tárolási** pedig a teljes Azure tárhely szükséges.
   * **Teljes IOPS szabványos tárfiókokban** van 8 K IOPS egység méretét a teljes standard tárfiókok alapján számítja ki.  A gyors Planner, az a szám alapján van kiszámítva a forrás virtuális gépek összes lemezt, és napi adatváltozási sebessége. A részletes Planner számának van alapján számítja ki a virtuális gépek a szabványos Azure virtuális gépekhez rendelt teljes száma, és adatok módosítása és a virtuális gépek.
   * **Standard szintű storage-fiókok száma** szabványos storage-fiókok a virtuális gépek védelméhez szükséges teljes számát jeleníti meg. Egy standard szintű tárfiókot, amelyet 20000 IOPS tárolható egy standard szintű tárolót a virtuális gépek között, és legfeljebb 500 iops-érték lemezenként támogatott.
   * **A blob szükséges lemezek számát** az Azure storage a létrehozott lemezek számát.
   * **Prémium szintű storage-fiókok szükséges száma** prémium szintű storage-fiókok a virtuális gépek védelméhez szükséges teljes számát jeleníti meg. A forrás virtuális gép magas iops-érték (több mint 20000) és a prémium szintű tárfiók van szüksége. A prémium szintű tárfiók akár 80000 IOPS tárolására képes.
   * **A prémium szintű storage IOPS teljes** van 256 KB-os IOPS egység méretét a teljes prémium szintű storage-fiókok a alapján számítja ki.  A gyors Planner, az a szám alapján van kiszámítva a forrás virtuális gépek összes lemezt, és napi adatváltozási sebessége. A részletes Planner a szám alapján van kiszámítva a virtuális gépek, a prémium szintű Azure virtuális gép (DS és GS-sorozat) rendelt teljes számát és az adatok és a virtuális gépek módosítása.
   * **Szükséges konfigurációs kiszolgálók száma** látható, a telepítéshez szükséges konfigurációs kiszolgálók számát.
   * **További folyamat szükséges kiszolgálók száma** jeleníti meg, hogy a folyamatkiszolgáló, alapértelmezés szerint a konfigurációs kiszolgálón futó mellett szükség-e további folyamat kiszolgálók.
   * **További tárhely 100 %-át a forrás** jeleníti meg, hogy a további tárhely szükséges-e a forráshelyen.

   ![Kimenet](./media/site-recovery-capacity-planner/output.png)

## <a name="run-the-detailed-planner"></a>A részletes Planner

1. Töltse le és nyissa meg a [Azure Site Recovery Capacity Planner](http://aka.ms/asr-capacity-planner-excel) eszköz. Makrók, ezért select szerkesztésének engedélyezése és tartalmakhoz, ha a rendszer kéri futtatásához szükséges.
2. A **planner-típus kiválasztása**, jelölje be **részletes Planner** a legördülő listából.

   ![Első lépések](./media/site-recovery-capacity-planner/getting-started-2.png)
3. Az a **munkaterhelés minősítési** munkalap, adja meg a szükséges adatokat. Meg kell adnia a megjelölt mezőket.

   * A **Processzormagok**, adja meg az magok teljes száma a forráskiszolgálón.
   * A **MB-ban a memóriafoglalás**, adja meg a RAM memória méretét a forráskiszolgáló.
   * A **hálózati adapterek száma**, a hálózati adapterek számát adja meg a forráskiszolgálón.
   * A **tárhely (GB) a teljes**, adja meg a Virtuálisgép-tároló teljes méretét. Például ha a forráskiszolgálóra és 500 GB 3 lemezt, majd teljes tárterület mérete 1500 GB.
   * A **csatlakoztatott lemezek számát**, adja meg a forráskiszolgáló lemezeinek teljes száma.
   * A **lemez a tárolókapacitás kihasználtságát**, adja meg az átlagos kihasználtság.
   * A **napi adatváltozási sebessége (%)**, adja meg, napi adatváltozási sebessége eltér a forráskiszolgáló nevétől.
   * A **Azure leképezési méret**, adja meg a leképezni kívánt Azure Virtuálisgép-méretet. Ha nem szeretné, ehhez manuálisan, kattintson a **számítási IaaS virtuális gépeket**. Adjon meg egy manuális beállítást, és kattintson a számítási IaaS virtuális gépeket, ha a manuális beállítás lehet, hogy frissíthetők, mert a számítási folyamat automatikusan azonosítja a legmegfelelőbb az Azure virtuális gép méretét.

   ![Munkaterhelés minősítés](./media/site-recovery-capacity-planner/workload-qualification.png)
4. Ha **számítási IaaS virtuális gépeket** Itt a hatása van:

   * A kötelező bemeneti ellenőrzi.
   * Kiszámítja az IOPS, és javasol, a legjobb Azure virtuális gép aize egyezés minden egyes virtuális gépekhez, amely jogosult a replikálás az Azure-bA. Ha a megfelelő méretű Azure virtuális gép nem észlelhető hiba jelenik meg. Például 65 csatolt a lemezek számát, ha hiba jelenik meg, mert a legmagasabb Azure virtuális gép mérete 64.
   * A storage-fiók, amely egy Azure virtuális gép nem használható javasol.
   * Kiszámítja a standard szintű storage-fiókok és a prémium szintű storage-fiókok a munkaterhelés számára szükséges teljes száma. Görgessen le a nézet az Azure tárolási típus, és a tárfiók olyan forráskiszolgálóhoz használható.
   * Befejeződött, és a többi szükséges tárolás (standard vagy prémium) társított típusból egy virtuális Gépet, és a csatlakoztatott lemezek számát alapján rendezi. Az összes virtuális gépet, amely az Azure, az oszlop követelményeinek **van VM minősíteni?** látható **Igen**. Ha a virtuális gépek biztonsági mentése nem végezhető az Azure-ba, egy hibaüzenet jelenik meg.

Oszlopok AA AE kimenete, és adja meg az egyes virtuális gépek információkat.

![Munkaterhelés minősítés](./media/site-recovery-capacity-planner/workload-qualification-2.png)

### <a name="example"></a>Példa
Tegyük fel, a táblázatban látható értékekkel hat virtuális gépek esetén az eszköz számítja ki, és hozzárendeli a legmegfelelőbb Azure virtuális Gépen, és az Azure tárolási követelmények érvényesek.

![Munkaterhelés minősítés](./media/site-recovery-capacity-planner/workload-qualification-3.png)

* A példa az alábbiakat vegye figyelembe a következőket:

  * Az első oszlop a virtuális gépek, a lemezek és a forgalom érvényesítési oszlop.
  * Öt virtuális gépek két szabványos storage-fiókok és egy prémium szintű tárfiók van szükség.
  * VM3 védelmét, mert egy vagy több lemez 1 Terabájtnál nem jogosultak.
  * VM1 és vm2 virtuális gépnek használhatja az első standard szintű tárfiók
  * VM4 használhatja a második standard szintű tárfiók.
  * VM5 és VM6 van szüksége a prémium szintű tárfiók, és használhatja is ugyanazt a fiókot.

    > [!NOTE]
    > A standard és prémium szintű storage IOPS kiszámítása a virtuális gép szintjén, és nem szabad szinten. A normál virtuális gépek legfeljebb 500 iops-érték lemezenként képes kezelni. Ha egy lemez IOPS még nagyobb, mint 500, prémium szintű storage kell. Azonban ha IOPS egy lemez legfeljebb 500, de a teljes méretű lemezek IOPS támogatási szabványos Azure virtuális gép keretein belül (Virtuálisgép-méretet, lemezek, adapterek, Processzor, memória száma száma), majd a planner választja ki egy szabványos VM és nem a DS vagy GS adatsorozat. Frissítenie kell manuálisan a leképezési Azure mérete cella adatsorozattal megfelelő DS vagy a GS virtuális gép.


A részletek érvényben vannak, kattintson **a planner eszköz adatok küldése** megnyitásához a **Capacity Planner** munkaterhelések vannak kiemelve megjelenítése, hogy fontosságúak védelemre jogosult-e.

### <a name="submit-data-in-the-capacity-planner"></a>A Capacity Planner az adatok küldése
1. Amikor megnyitja a **Capacity Planner** munkalap a telepítéskor megadott beállítások alapján. A word "Munkaterhelési" jelenik meg a **Infra bemeneti forrás** cella mutatja be, hogy a bemeneti a **munkaterhelés minősítési** munkalap.
2. Ha szeretné módosítani, módosítania kell a **munkaterhelés minősítési** munkalapot, és kattintson **a planner eszköz adatok küldése** újra.  

   ![Capacity Planner](./media/site-recovery-capacity-planner/capacity-planner.png)

## <a name="next-steps"></a>Következő lépések

[Ismerje meg, hogyan futtathat](site-recovery-capacity-planning-for-hyper-v-replication.md) a Capacity Planner.

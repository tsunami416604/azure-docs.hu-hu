---
title: B-sorozat burstable - Azure virtuális gépek
description: Ismerteti a B-sorozat burstable Azure virtuális gép méretei.
services: virtual-machines
author: ayshakeen
manager: gwallace
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: ayshak
ms.openlocfilehash: 5121ef8eb3123d830233e91968c69b331d255bd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78161080"
---
# <a name="b-series-burstable-virtual-machine-sizes"></a>B sorozatú felrobbanható virtuális gépméretek

A B sorozatú virtuális gépek ideálisak olyan számítási feladatokhoz, amelyek nem igényelnek folyamatosan a PROCESSZOR teljes teljesítményét, például webkiszolgálókat, koncepciók igazolását, kis adatbázisokat és fejlesztői buildkörnyezeteket. Ezek a számítási feladatok általában burstable teljesítménykövetelményekkel rendelkeznek. A B-sorozat lehetővé teszi, hogy vásároljon egy virtuális gép méretét az alapteljesítmény és a virtuálisgép-példány krediteket hoz létre, ha az alapértéknél kevesebbet használ. Ha a virtuális gép kreditet halmozott fel, a virtuális gép a vCPU 100%-ának használatával az alapvonal felett robbanhat, ha az alkalmazás nagyobb CPU-teljesítményt igényel.

A B-sorozat a következő virtuális gépméretekben kapható:

Prémium szintű tárhely: Támogatott

Prémium szintű tárolási gyorsítótárazás: Nem támogatott

Élő áttelepítés: Támogatott

Memóriamegőrzési frissítések: Támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | A virtuális gép alap CPU-perf-je | Virtuális gép maximális CPU-perf-je | Kezdeti kreditek | Bebankolt kredit/óra | Maximális bankolt kreditek | Adatlemezek max. száma | Gyorsítótárazott és ideiglenes tárolási átviteli sebesség maximális kapacitása: IOPS/MBps | Maximális nem gyorsítótárazott lemezátviteli sebesség: IOPS/MBps | Hálózati adapterek maximális száma |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| Standard_B1ls<sup>1.</sup> | 1  | 0,5 | 4   | 5%   | 100%  | 30  | 3   | 72   | 2  | 200/10    | 160/10    | 2  |
| Standard_B1s              | 1  | 1   | 4   | 10%  | 100%  | 30  | 6   | 144  | 2  | 400/10    | 320/10    | 2  |
| Standard_B1ms             | 1  | 2   | 4   | 20%  | 100%  | 30  | 12  | 288  | 2  | 800/10    | 640/10    | 2  |
| Standard_B2s              | 2  | 4   | 8   | 40%  | 200%  | 60  | 24  | 576  | 4  | 1600/15   | 1280/15   | 3  |
| Standard_B2ms             | 2  | 8   | 16  | 60%  | 200%  | 60  | 36  | 864  | 4  | 2400/22.5 | 1920/22.5 | 3  |
| Standard_B4ms             | 4  | 16  | 32  | 90%  | 400%  | 120 | 54  | 1296 | 8  | 3600/35   | 2880/35   | 4  |
| Standard_B8ms             | 8  | 32  | 64  | 135% | 800%  | 240 | 81  | 1944 | 16 | 4320/50   | 4320/50   | 4  |
| Standard_B12ms            | 12 | 48  | 96  | 202% | 1200% | 360 | 121 | 2909 | 16 | 6480/75   | 4320/50   | 6  |
| Standard_B16ms            | 16 | 64  | 128 | 270% | 1600% | 480 | 162 | 3888 | 32 | 8640/100  | 4320/50   | 8  |
| Standard_B20ms            | 20 | 80  | 160 | 337% | 2000% | 600 | 203 | 4860 | 32 | 10800/125 | 4320/50   | 8  |

<sup>1</sup> B1ls csak Linux on támogatott

## <a name="workload-example"></a>Példa számítási feladatokra

Fontolja meg egy irodai check-in/out jelentkezési lapot. Az alkalmazásnak szüksége van a CPU-kitörésekre munkaidőben, de nem sok számítási teljesítményre van szüksége munkaidőn kívül. Ebben a példában a számítási feladatok hoz egy 16vCPU virtuális gép 64GiB RAM hatékony működéséhez.

A táblázat az óránkénti forgalmi adatokat mutatja, és a diagram a forgalom vizuális ábrázolása.

B16 jellemzők:

Maximális CPU perf: 16vCPU * 100% = 1600%

Kiindulási érték: 270%

![Óránkénti forgalmi adatok diagramja](./media/b-series-burstable/office-workload.png)

| Forgatókönyv | Time | CPU-használat (%) | Felhalmozott kreditek<sup>1</sup> | Kreditek állnak rendelkezésre |
| --- | --- | --- | --- | --- |
| B16ms telepítése | Környezet | Környezet  | 480 (Kezdeti kreditek) | 480 |
| Nincs forgalom | 0:00 | 0 | 162 | 642 |
| Nincs forgalom | 1:00 | 0 | 162 | 804 |
| Nincs forgalom | 2:00 | 0 | 162 | 966 |
| Nincs forgalom | 3:00 | 0 | 162 | 1128 |
| Nincs forgalom | 4:00 | 0 | 162 | 1290 |
| Nincs forgalom | 5:00 | 0 | 162 | 1452 |
| Alacsony forgalom | 6:00 | 270 | 0 | 1452 |
| Alkalmazottak jönnek az irodába (app igények 80% vCPU) | 7:00 | 1280 | -606 | 846 |
| Az alkalmazottak továbbra is az irodába jönnek (az alkalmazásnak 80% vCPU-ra van szüksége) | 8:00 | 1280 | -606 | 240 |
| Alacsony forgalom | 9:00 | 270 | 0 | 240 |
| Alacsony forgalom | 10:00 | 100 | 102 | 342 |
| Alacsony forgalom | 11:00 | 50 | 132 | 474 |
| Alacsony forgalom | 12:00 | 100 | 102 | 576 |
| Alacsony forgalom | 13:00 | 100 | 102 | 678 |
| Alacsony forgalom | 14:00 | 50 | 132 | 810 |
| Alacsony forgalom | 15:00 | 100 | 102 | 912 |
| Alacsony forgalom | 16:00 | 100 | 102 | 1014 |
| Alkalmazottak kijelentkezése (az alkalmazásnak 100%-os vCPU-ra van szüksége) | 17:00 | 1600 | -798 | 216 |
| Alacsony forgalom | 18:00 | 270 | 0 | 216 |
| Alacsony forgalom | 19:00 | 270 | 0 | 216 |
| Alacsony forgalom | 20:00 | 50 | 132 | 348 |
| Alacsony forgalom | 21:00 | 50 | 132 | 480 |
| Nincs forgalom | 22:00 | 0 | 162 | 642 |
| Nincs forgalom | 23:00 | 0 | 162 | 804 |

<sup>1</sup> Az egy órában felhalmozott kreditek `((Base CPU perf of VM - CPU Usage) / 100) * 60 minutes`egyenértékűek a következőkkal: .  

A 16 vCPU-val és 64 GiB memóriával rendelkező D16s_v3 esetében az óradíj óránként $0.936 (havi $673.92) és 16 vCPU-val és 64 GiB memóriával rendelkező B16ms esetén az óradíj $0.794 óránként (havi $547.86). <b>Ez 15%-os megtakarítást eredményez!</b>

## <a name="q--a"></a>Kérdések és válaszok

### <a name="q-how-do-you-get-135-baseline-performance-from-a-vm"></a>K: Hogyan kap 135%-os alapteljesítményt egy virtuális gép?

**V**: A 135%, a virtuális gép méretét kitetethető 8 vCPU-k között van megosztva. Például ha az alkalmazás a kötegelt feldolgozáson dolgozó 8 magból 4-et használ, és a 4 vCPU mindegyike 30%-os kihasználtsa, a virtuális gép cpu-jának teljes mennyisége 120%.  Ami azt jelenti, hogy a virtuális gép lenne a hitelidő az alapteljesítmény 15%-os delta alapján.  De ez azt is jelenti, hogy ha a rendelkezésre álló hitelek, hogy ugyanaz a virtuális gép használhatja 100%-a 8 vCPU így, hogy a virtuális gép maximális CPU-teljesítmény 800%.


### <a name="q-how-can-i-monitor-my-credit-balance-and-consumption"></a>K: Hogyan követhetem nyomon a hitelegyenlegemet és a felhasználásomat?

**V:** Az elkövetkező hetekben 2 új mérőszámot vezetünk be, a **Kredit** metrika lehetővé teszi, hogy megtekintse, hány kreditet kapott a virtuális gép, és a **ConsumedCredit** mérőszám megmutatja, hogy a virtuális gép hány CPU-kreditet használt fel a bankból.    Ezeket a metrikákat a portál metrika ablaktáblájában vagy programozott módon tekintheti meg az Azure Monitor API-kon keresztül.

Az Azure metrikaadatainak eléréséről a [Microsoft Azure metrikáinak áttekintése című témakörben](../azure-monitor/platform/data-platform.md)olvashat bővebben.

### <a name="q-how-are-credits-accumulated"></a>K: Hogyan halmozódnak fel a kreditek?

**V**: A virtuális gép felhalmozási és fogyasztási díjai úgy vannak beállítva, hogy a virtuális gép, amely pontosan az alap teljesítményszintjén fut, nem rendelkezik a felszakítási kreditek nettó felhalmozódásával vagy felhasználásával.  A virtuális gép a kreditek nettó növekedése lesz, amikor az alap teljesítményszintje alatt fut, és nettó csökkenést fog okozni a kreditekben, amikor a virtuális gép a PROCESSZORt az alapteljesítményszintjénél nagyobb mértékben használja.

**Példa:** Üzembe helyezem a virtuális gépet a B1ms mérete az én kis idő-és jelenlét-adatbázis-alkalmazás. Ez a méret lehetővé teszi az alkalmazás omlós használata akár 20%-a vCPU, mint az én kiindulási, ami 0,2 kredit percenként tudom használni, vagy a bank.

Saját alkalmazás foglalt elején és végén az én alkalmazottak munkanap között 7:00-9:00 és 4:00-6:00. A nap többi 20 órájában az alkalmazásom általában tétlen, csak a vCPU 10%-át használja. A nem csúcsidőben percenként 0,2 kreditet keresek, de csak 0.l kreditet fogyasztok percenként, így a virtuális gépem 0,1 x 60 = 6 kreditet fog fogyasztani óránként.  A 20 óra, hogy én vagyok off-peak, fogok bank 120 kredit.  

Csúcsidőben az alkalmazásom átlagosan 60% vCPU-kihasználtságot ér el, még mindig 0,2 kreditet keresek percenként, de percenként 0,6 kreditet fogyasztok, percenként 0,4 kredit nettó költségért, vagy 0,4 x 60 = 24 kreditóránként. Nekem van 4 óra naponta csúcs használat, így a költségek 4 x 24 = 96 kredit az én csúcs használat.

Ha veszem a 120 kreditet szereztem off-peak, és vonjuk ki a 96 kreditet használtam az én csúcsidőben, én bank további 24 kredit naponta, hogy tudom használni más tört tevékenység.

### <a name="q-how-can-i-calculate-credits-accumulated-and-used"></a>K: Hogyan számíthatom ki a felhalmozott és felhasznált krediteket?

**A**: A következő képletet használhatja:

(A VM alap CPU-perfje - CPU-használat) / 100 = Kreditbank vagy használat percenként

pl. a fenti esetben az alapérték 20%, és ha a cpu 10%-át használja (20%10%)/100 = 0,1 kredit percenként.

### <a name="q-does-the-b-series-support-premium-storage-data-disks"></a>K: Támogatja a B sorozat a prémium szintű tárolási adatlemezeket?

**A**: Igen, minden B sorozatú méret támogatja a prémium szintű tárolási adatlemezeket.

### <a name="q-why-is-my-remaining-credit-set-to-0-after-a-redeploy-or-a-stopstart"></a>K: Miért van a fennmaradó kreditem 0-ra beállítva az újratelepítés vagy a stop/start után?

**V** : Ha egy virtuális gép "REDPLOYED", és a virtuális gép egy másik csomópontra költözik, a felhalmozott hitel elvész. Ha a virtuális gép levan állítva/elindítva, de ugyanazon a csomóponton marad, a virtuális gép megtartja a halmozott jóváírást. Amikor a virtuális gép elindul friss egy csomóponton, kap egy kezdeti jóváírást, Standard_B8ms 240 mins.

### <a name="q-what-happens-if-i-deploy-an-unsupported-os-image-on-b1ls"></a>K: Mi történik, ha nem támogatott operációsrendszer-rendszerképet telepítek a B1ls-en?

**V:** A B1ls csak linuxos lemezképeket támogat, és ha egy másik operációsrendszer-lemezképet telepít, előfordulhat, hogy nem a legjobb felhasználói élményt kapja.

## <a name="other-sizes"></a>Egyéb méretek

- [Általános célú](sizes-general.md)
- [Számításra optimalizált](sizes-compute.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tárolásra optimalizált](sizes-storage.md)
- [GPU-optimalizált](sizes-gpu.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)

## <a name="next-steps"></a>További lépések

További információ arról, hogy [az Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek a számítási teljesítmény összehasonlításában az Azure-sKU-k között.
---
title: Az Azure Site Recovery hibaelhárítás a folyamatban lévő Azure – Azure replikációs problémák |} A Microsoft Docs
description: Vész-helyreállítási Azure-beli virtuális gépek replikálása hibák és problémák elhárítása
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.author: asgang
ms.openlocfilehash: 9ff756270c368d39b7ef78d7c1046f7c91169668
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2019
ms.locfileid: "56299599"
---
# <a name="troubleshoot-ongoing-problems-in-azure-to-azure-vm-replication"></a>Az Azure-ról Azure-beli virtuális gép replikálása folyamatban lévő kapcsolatos problémák elhárítása

Ez a cikk ismerteti a gyakori problémák az Azure Site Recoveryben takarít meg replikálása és helyreállítása Azure-beli virtuális gépek egyik régióból egy másik régióba. Azt is bemutatja, hogyan háríthatók el őket. Támogatott konfigurációk kapcsolatos további információkért lásd: a [támogatási mátrixa Azure virtuális gépek replikálása](site-recovery-support-matrix-azure-to-azure.md).

Az Azure Site Recovery folyamatosan replikálja az adatokat a forrás-régióból a vészhelyreállítási régióban, és 5 percenként összeomlás-konzisztens helyreállítási pont létrehozása. A Site Recovery 60 percig nem hozható létre helyreállítási pontokat, ha riasztást jelenít meg a következő információkkal:

Hibaüzenet: "Nincs összeomlási elérhető alkalmazáskonzisztens helyreállítási pont a virtuális gép az elmúlt 60 percben."</br>
Hiba azonosítója: 153007 </br>

A következő szakaszok ismertetik a okait és megoldásait.

## <a name="high-data-change-rate-on-the-source-virtal-machine"></a>A forrás virtuális gépen nagy adatváltozási sebesség
Az Azure Site Recovery egy esemény küldése, ha a forrás virtuális gép adatváltozási sebessége meghaladja a támogatott határértékeket. Ellenőrizze, hogy a probléma nagy forgalom miatt van-e, lépjen a **replikált elemek** > **VM** > **események – elmúlt 72 órában**.
Az esemény "A módosult adatok aránya meghaladja a támogatott határértékeket" megjelennie:

![data_change_rate_high](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png)

Ha az eseményt, a pontos lemez adatainak kell megjelennie:

![data_change_rate_event](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png)


### <a name="azure-site-recovery-limits"></a>Az Azure Site Recovery korlátai
Az alábbi táblázat az Azure Site Recovery korlátait tartalmazza. Ezek a korlátok tesztjeinken alapulnak, de nem fedhetik le az összes lehetséges alkalmazások i/o-kombinációját. A tényleges eredmények a saját alkalmazásának I/O-műveletei alapján változhatnak. 

Nincsenek, érdemes figyelembe venni két korlátok, a lemezenkénti adatváltozásról és adatváltozás virtuális gépenként. Ha például tekintsük át az alábbi táblázat a prémium szintű P20 lemez. A Site Recovery képes kezelni 5 MB/s lemez adatforgalom, amely legfeljebb öt ilyen lemezek virtuális gépenkénti, a virtuális gép összes adatváltozással 25 MB/s miatt.

**Replikáció tárolási célja** | **Forráslemez átlagos i/o-mérethez** |**Forráslemez átlagos adatváltozása** | **Forráslemez adatok napi teljes adatváltozása**
---|---|---|---
Standard szintű Storage | 8 KB | 2 MB/s | Lemezenként 168 GB
Prémium szintű P10 vagy P15 lemez | 8 KB  | 2 MB/s | Lemezenként 168 GB
Prémium szintű P10 vagy P15 lemez | 16 KB | 4 MB/s |  Lemezenként 336 GB
Prémium szintű P10 vagy P15 lemez | 32 KB vagy több | 8 MB/s | Lemezenként 672 GB
Prémium szintű P20, P30, P40 vagy P50 lemez | 8 KB    | 5 MB/s | Lemezenként 421 GB
Prémium szintű P20, P30, P40 vagy P50 lemez | 16 KB vagy több |10 MB/s | Lemezenként 842 GB

### <a name="solution"></a>Megoldás
Az Azure Site Recovery korlátozások vonatkoznak az adatmódosítási arány, a lemez típusa alapján. Tudja meg, ha a probléma-e ismétlődő vagy pillanatnyi, keresse meg az érintett virtuális gép akkor változik, ha az adatokat. Nyissa meg a forrás virtuális gép, a metrikák alatt található **figyelés**, és adja hozzá a metrikák, ezen a képernyőfelvételen látható módon:

![Három lépéses folyamat lehet megkeresni az adatmódosítási arány](./media/site-recovery-azure-to-azure-troubleshoot/churn.png)

1. Válassza ki **metrika hozzáadása**, és adja hozzá **operációs rendszer lemez írási bájt/mp** és **adatok lemezre írása bájt/mp**.
2. Figyelheti a megnövekedett, a képernyőképen látható módon.
3. A nézet az összes írási művelet, operációsrendszer-lemez és az összes adatlemezt együttesen történik. Ezek a metrikák előfordulhat, hogy nem körülményeiről a lemez szinten, de azt jelzik, hogy a teljes minta adatváltozások egyes típusai.

Ha ugrásszerű van egy burst alkalmanként adatokat és az adatok módosítsa a következőre arány értéke nagyobb, mint 10 MB/s (prémium szintű) és 2 MB/néhány (a standard szintű) s idő és előre, replikációs olvasásra lesznek. De ha a forgalom túlmutató a támogatott korlátozza a legtöbbször, lehetőség szerint fontolja meg az alábbi lehetőségek egyikét:

* **Egy magas szintű adatváltozások arány okozó lemezének kizárása**: A lemez használatával kizárhatják [PowerShell](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#replicate-azure-virtual-machine).
* **A vész-helyreállítási tárolólemez szintjének módosítása**: Ez a beállítás akkor lehetséges, csak ha a lemez adatváltozásának kevesebb mint 10 MB/s. Tegyük fel, egy P10 lemezt egy virtuális Gépet egy nagyobb, mint a 8 MB/s, de kevesebb mint 10 MB/s adatváltozása tapasztalja. Az ügyfél védelem során P30 lemez használható a céloldali tárolóra, ha a probléma megoldhatók.

## <a name="Network-connectivity-problem"></a>Hálózati kapcsolódási problémák

### <a name="network-latency-to-a-cache-storage-account"></a>Hálózati késés a gyorsítótárfiókba
A Site Recovery a replikált adatokat küld a gyorsítótár tárfiókjához. Előfordulhat, hogy látja, hálózati késés, ha az adatok a virtuális gépről a gyorsítótárfiókba feltöltését lassabb, mint 4 MB-os 3 másodpercen belül. 

Késéssel kapcsolatos probléma ellenőrzéséhez használja [azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) feltölteni az adatokat a virtuális gépről a gyorsítótárfiókba. Ha nagy a késés, ellenőrizze, ha egy hálózati virtuális készüléket (nva-t) használ a virtuális gépek kimenő hálózati forgalom szabályozására. A berendezés előfordulhat, hogy leszabályozza, ha a replikációs forgalmat az nva-n keresztül halad. 

Azt javasoljuk, hogy hoz létre egy hálózati végpontot a virtuális hálózat "Tároló" úgy, hogy a replikálás forgalma nem nyissa meg az nva-t. További információkért lásd: [hálózati virtuális berendezés konfigurációjának](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#network-virtual-appliance-configuration).

### <a name="network-connectivity"></a>Hálózati kapcsolat
A Site Recovery replikációja, a kimenő kapcsolat az adott URL-címek vagy IP-címtartományok szükség a virtuális gépről. Ha a virtuális gép tűzfal mögött található, vagy használja a hálózati biztonsági csoport (NSG) szabályai kimenő kapcsolat szabályozásához, előfordulhat, hogy között ezek a problémák egyike. Gondoskodjon arról, hogy az URL-címeket kapcsolódnak, lásd: [kimenő kapcsolatok esetében a Site Recovery URL-címeinek](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges). 
---
title: Lsv2 sorozat - Azure virtuális gépek
description: Az Lsv2 sorozatú virtuális gépek specifikációi.
services: virtual-machines
author: sasha-melamed
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 103e19d6e299956b5ee1ad45b577e25f9f2de1c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164032"
---
# <a name="lsv2-series"></a>Lsv2 sorozat

Az Lsv2 sorozat nagy átviteli sebességgel, alacsony késleltetéssel, közvetlenül feltérképezett helyi NVMe tárolóval rendelkezik, amely az [AMD EPYC<sup>TM</sup> 7551 processzoron](https://www.amd.com/en/products/epyc-7000-series) fut, 2,55 GHz-es magemeléssel és 3,0 GHz-es maximális növekedéssel. Az Lsv2 sorozatú virtuális gépek 8 és 80 vCPU között kaphatók egyidejű többszálas konfigurációban.  Van 8 GiB memória vCPU-nként, és egy 1.92TB NVMe SSD M.2 eszköz 8 vCPU-nként, akár 19.2TB (10x1.92TB) elérhető az L80s v2.

> [!NOTE]
> Az Lsv2 sorozatú virtuális gépek vannak optimalizálva, hogy a helyi lemeza közvetlenül a virtuális géphez csatlakoztatott csomóponton, nem pedig tartós adatlemezek használatával. Ez lehetővé teszi a nagyobb IP/átviteli / átviteli a számítási feladatokhoz. Az Lsv2 és ls-sorozat nem támogatja a helyi gyorsítótár létrehozását a tartós adatlemezek által elérhető IPP-k növelése érdekében.
>
> A helyi lemez nagy átviteli és IP-jei ideálissá teszik az Lsv2 sorozatú virtuális gépeket a NoSQL-áruházak, például az Apache Cassandra és a MongoDB számára, amelyek több virtuális gépen replikálják az adatokat, hogy egyetlen virtuális gép meghibásodása esetén perzisztenciát érjenek el.
>
> További információ: Teljesítmény optimalizálása az Lsv2 sorozatú virtuális gépeken [Windows](../virtual-machines/windows/storage-performance.md) vagy [Linux](../virtual-machines/linux/storage-performance.md)rendszeren.  

ACU: 150-175

Prémium szintű tárhely: Támogatott

Prémium szintű tárolási gyorsítótárazás: Nem támogatott

Élő áttelepítés: Nem támogatott

Memóriamegőrzési frissítések: Nem támogatott

| Méret | vCPU | Memória (GiB) | <sup>1.</sup> ideiglenes lemez (GiB) | NVMe lemezek<sup>2</sup> | NVMe-lemez átviteli sebessége<sup>3</sup> (IOPS olvasása/Mb/S) | Maximális nem gyorsítótárazott adatlemez átviteli sebesség (IoPs/MpBps)<sup>4</sup> | Maximális adatlemezek | Max hálózati adapterek / várható hálózati sávszélesség (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_L8s_v2   |  8 |  64 |  80 |  1x1,92 TB  | 400000/2000  | 8000/160   | 16 | 2 / 3200   |
| Standard_L16s_v2  | 16 | 128 | 160 |  2x1,92 TB  | 800000/4000  | 16000/320  | 32 | 4 / 6400   |
| Standard_L32s_v2  | 32 | 256 | 320 |  4x1,92 TB  | 1,5 M/8000    | 32000/640  | 32 | 8 / 12800  |
| Standard_L48s_v2  | 48 | 384 | 480 |  6x1,92 TB  | 2,2 M/14000   | 48000/960  | 32 | 8 / 16000+ |
| Standard_L64s_v2  | 64 | 512 | 640 |  8x1,92 TB  | 2,9 M/16000   | 64000/1280 | 32 | 8 / 16000+ |
| <sup>5. Standard_L80s_v2</sup> | 80 | 640 | 800 | 10x1.92TB | 3,8 M/20000 | 80000/1400 | 32 | 8 / 16000+ |

<sup>1</sup> Az Lsv2 sorozatú virtuális gépek szabványos SCSI-alapú ideiglenes erőforráslemezzel rendelkeznek az operációs rendszer lapozásához/lapozásához (D: Windows, /dev/sdb Linux on). Ez a lemez 80 GiB tárhelyet, 4000 IOPS-t és 80 MBps átviteli sebességet biztosít minden 8 vCPU-hoz (például Standard_L80s_v2 800 GiB-t biztosít 40 000 IOPS és 800 MBPS értékben). Ez biztosítja, hogy az NVMe meghajtók teljes mértékben az alkalmazások használatára legyenek elkötelezve. Ez a lemez efemer, és minden adat elvész a stop/deallocate.

<sup>2</sup> A helyi NVMe-lemezek rövid élettartamúak, az adatok elvesznek ezeken a lemezeken, ha leállítja/felszabadítja a virtuális gépét.

<sup>3</sup> A Hyper-V NVMe Direct technológia nem szabályozott hozzáférést biztosít a helyi NVMe meghajtókhoz, amelyek biztonságosan vannak leképezve a vendég virtuális gép térbe.  A maximális teljesítmény eléréséhez a legújabb WS2019-es build, vagy az Azure Piactérről származó Ubuntu 18.04 vagy 16.04 használatával kell elérni.  Az írási teljesítmény az I/O-mérettől, a meghajtó terhelésétől és a kapacitáskihasználtságtól függ.

<sup>4</sup> Lsv2 sorozatú virtuális gépek nem biztosítanak állomás-gyorsítótárat az adatlemezhez, mivel nem előnyös az Lsv2 számítási feladatok.  Az Lsv2 virtuális gépek azonban képesek az Azure ideiglenes virtuálisgép-operációs rendszer lemezbeállításának (legfeljebb 30 GiB)-re.

<sup>5,</sup> 64-nél több vCPU-val rendelkező virtuális géphez a támogatott vendég operációs rendszerek egyikére van szükség:

- Windows Server 2016 vagy újabb
- Ubuntu 16.04 LTS vagy újabb, Azure hangolt kernellel (4.15 kernel vagy újabb)
- SLES 12 SP2 vagy újabb
- RHEL vagy CentOS 6.7-től 6.10-ig, a Microsoft által biztosított 4.3.1-es (vagy újabb) LIS-csomaggal telepítve
- RHEL vagy CentOS 7.3-as verzió, a Microsoft által biztosított 4.2.1-es (vagy újabb) LIS-csomaggal telepítve
- RHEL vagy CentOS 7.6-os vagy újabb verzió
- Oracle Linux UEK4 vagy újabb verzióval
- Debian 9 a backport kernellel, Debian 10 vagy újabb
- CoreOS 4.14-es vagy újabb kernellel

## <a name="size-table-definitions"></a>Mérettábla definíciói

- A tárolókapacitás mértékegysége GiB (gibibájt = 1024^3 bájt). A gigabájtban (1000^3 bájt) és a gibibájtban (1024^3 bájt) mért meghajtók összehasonlításakor tartsa észben, hogy a GiB-ban kifejezett kapacitások kisebbnek tűnhetnek. Például: 1023 GiB = 1098,4 GB
- A lemezteljesítmény másodpercenkénti bemeneti/kimeneti műveletek (IOPS) mennyiségeként van kifejezve, valamint MBps-ben, ahol 1 MBps = 10^6 bájt/másodperc.
- Ha azt szeretné, hogy a legjobb teljesítményt a virtuális gépek, az adatlemezek számát kell korlátozni a vCPU-nként 2 lemezek.
- **A várt hálózati sávszélesség** a [virtuális géptípusonként az](../virtual-network/virtual-machine-network-throughput.md) összes hálózati adapteren leosztott maximális összesített sávszélesség az összes cél számára. A felső határértékek nem garantáltak, csak útmutatóul szolgálnak a kívánt alkalmazásra megfelelő VM-típus kiválasztásához. A tényleges hálózati teljesítmény számos tényezőtől függ, többek között a hálózat túlterhelésétől, az alkalmazás terhelésétől, valamint az alkalmazás hálózati beállításaitól. A hálózati átviteli sebesség optimalizálásával kapcsolatos információkért lásd: [A hálózati átviteli sebesség optimalizálása Windows és Linux rendszeren](../virtual-network/virtual-network-optimize-network-bandwidth.md). Linux vagy Windows rendszeren a várt hálózati teljesítmény eléréséhez egy adott verzió kiválasztására vagy a virtuális gép optimalizálására lehet szükség. További információkért lásd: [Virtuális gépek átviteli sebességének megbízható tesztelése](../virtual-network/virtual-network-bandwidth-testing.md).

## <a name="next-steps"></a>További lépések

További információ arról, hogy [az Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek a számítási teljesítmény összehasonlításában az Azure-sKU-k között.

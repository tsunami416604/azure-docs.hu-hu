---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/24/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 212506667a56befb4e3926dec7a9e3eb9772ebed
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2019
ms.locfileid: "55736086"
---
# <a name="ultra-ssd-preview-managed-disks-for-azure-virtual-machine-workloads"></a>Az Azure virtuális gépek számítási feladataihoz ultranagy SSD (előzetes verzió) felügyelt lemezek

Azure Ultranagy SSD (előzetes verzió) az Azure IaaS virtuális gépek nagy átviteli sebességet, magas iops-érték és egységes közel valós idejű lemezes tárolást kínál. Az új ajánlat felső részén a meglévő lemezek ajánlataink, ugyanazon rendelkezésre állási szinten sor teljesítményt biztosít. Ultranagy SSD további előnyei a következők dinamikusan megváltoztathatja a lemezes célok mellett a virtuális gépek újraindítása nélkül a számítási feladatok teljesítményére. Ultranagy SSD adatok-igényes számítási feladatokhoz, például az SAP HANA, a felső szintű adatbázisok és a tranzakciós adatokat használó munkaterhelések esetén használhatók.

## <a name="ultra-ssd-features"></a>Ultranagy SSD-funkciók

**A Managed Disks**: Felügyelt lemezként ultranagy SSD-k csak érhetők el. Ultranagy SSD-k nem állítható rendszerbe a nem felügyelt lemezek és Lapblobok. Egy felügyelt lemez létrehozásakor adja meg a lemez termékváltozat UltraSSD_LRS típusa, és azt jelzik, lemezt, az IOPS és átviteli sebességért és az Azure létrehozza és felügyeli a lemezt Ön helyett.  

**Virtuális gépek**: Ultranagy SSD-k úgy tervezték, hogy engedélyezve van az Azure VM-termékváltozatok; minden prémium szintű SSD használata Azonban mivel jelenleg előzetes verzióban érhető el, a virtuális gépek mérete gen/DS v3-as.

**A dinamikus Teljesítménykonfiguráció**: Ultranagy SSD-k lehetővé teszik a teljesítményt (IOPS és átviteli sebesség) a munkaterhelés igényei és a lemez dinamikus módosítását az a virtuális gépek újraindítása nélkül.

## <a name="scalability-and-performance-targets"></a>Méretezhetőségi és teljesítménycélok

Egy Ultra SSD üzembe helyezésekor, hogy egymástól függetlenül konfigurálhatja a kapacitás és a lemez teljesítménye. Ultranagy SSD 4 GIB-ra legfeljebb 64 Tib-ra a különféle rögzített méretű származnak, és rugalmas teljesítmény konfigurációs modell, amely lehetővé teszi, hogy külön konfigurálni az IOPS és átviteli sebesség funkció. Ultranagy SSD-k, adatlemez csak jól használható. Operációsrendszer-lemezek, prémium szintű SSD-k használatát javasoljuk.

Az Ultranagy SSD néhány főbb funkciók a következők:

- Lemez kapacitása: Ultranagy SSD kínál, a lemezméretek különböző számos 4 GIB-ra legfeljebb 64 Tib-ra.
- A lemez iops-érték: Ultranagy SSD-k támogatja a 300 IOPS/GIB-ra, egy 160 KB IOPS lemezenként maximum IOPS-korlátok. Elérése érdekében az Ön által üzembe helyezett iops-érték, győződjön meg arról, hogy a kiválasztott lemez iops-érték kisebb, mint a virtuális gépek iops-érték. A minimális IOPS lemez 100 iops-t.
- Lemez átviteli sebesség: Ultranagy SSD-k, az átviteli sebesség egyetlen határérték 256 KiB/s minden egyes üzembe helyezett IOPS, legfeljebb 2000 MB/s lemezenként (ahol MBps = 10 ^ 6 bájt / másodperc). A minimális átviteli sebesség 1 MiB.

Az alábbi táblázatban a különféle lemezméretek által támogatott konfigurációkat foglaltuk össze:  

### <a name="ultra-ssd-managed-disk-offerings"></a>Ultra SSD Managed Disks-ajánlatok

|Lemezméret (GiB)  |IOPS Caps  |Átviteli sebesség korlát (MB/s)  |
|---------|---------|---------|
|4     |1,200         |300         |
|8     |2,400         |600         |
|16     |4,800         |1,200         |
|32     |9,600         |2,000         |
|64     |19,200         |2,000         |
|128     |38,400         |2,000         |
|256     |76,800         |2,000         |
|512     |80,000         |2,000         |
|1024-65,536 (ebben a tartományban, 1 Tib-ra egységnyi növekményekben növelése mérete)     |160,000         |2,000         |

## <a name="pricing-and-billing"></a>Árak és számlázás

Ultranagy SSD-k használatával, az alábbi számlázási szempontok lépnek érvénybe:

- Managed Disk Size
- Felügyelt lemez kiosztott iops-érték
- Felügyelt lemez kiosztott átviteli sebesség
- Ultranagy SSD Virtuálisgép-foglalás díj

### <a name="managed-disk-size"></a>Managed Disk Size

Felügyelt lemezeket a Virtuálisgép-méretek egy új Azure virtuális gép kiépítése során választott számítjuk fel. Az Azure a kiépített méretet a legközelebbi lemezméret-ajánlathoz (kerekítve) rendeli hozzá. A lemezméretek érhető el a részletekért lásd: a tábla, méretezhetőségi és Teljesítménycéljai a fenti szakaszban. Mindegyik lemez képez le egy támogatott kiosztott lemez méretét és ennek megfelelően számlázva lesz óránként. Ha üzembe helyezett egy 200 GiB Ultranagy SSD-lemez, és 20 óra múlva törli azt, például azt a lemez mérete vonatkozó ajánlatot 256 GIB-ra lesz leképezve, és kell fizetnie a 256 GIB-ra vonatkozó 20 óra. Ezt a számlázási számítási óra felhasználás függetlenül ténylegesen a lemezre írt adatok mennyisége alapján.

### <a name="managed-disk-provisioned-iops"></a>Felügyelt lemez kiosztott iops-érték

Iops-t is, hogy az alkalmazás által küldött kérések száma másodpercenként a lemezekre. Egy bemeneti/kimeneti műveleti Szekvenciális olvasási vagy írási vagy véletlenszerű olvasási vagy írási. A lemez mérete vagy a virtuális Géphez csatolt lemezek száma alapján, IOPS átlagos száma számlázása óránként. A lemez iops-érték érhető el, tájékozódhat a tábla, méretezhetőségi és Teljesítménycéljai a fenti szakaszban.

### <a name="managed-disk-provisioned-throughput"></a>Felügyelt lemez kiosztott átviteli sebesség

Átviteli sebesség, amely az alkalmazás által küldött, a lemezek egy megadott időszak bájt/s mérve adatok mennyisége. Ha az alkalmazás által végzett nagy bemeneti/kimeneti műveletek, nagy átviteli sebesség szükséges.  

Van egy kapcsolat közötti átviteli sebesség és az IOPS, ahogyan az a következő képletre:  I/o-mérete x IOPS = átviteli sebesség

Ezért fontos meghatározni az optimális átviteli sebesség és az IOPS értékek, az alkalmazás számára szükséges. Próbálja ki az egyik optimalizálása, a másik lekérdezi eszköztípusokra is. Javasoljuk, hogy kezdve egy átviteli megfelelő 16 KiB i/o-mérethez és módosításával, ha a kapacitás növelése érdekében van szükség.

Az Ultranagy SSD támogatott lemez átviteli sebességet a részletekért lásd: a tábla, méretezhetőségi és Teljesítménycéljai a fenti szakaszban. Például a lemez méretét és az IOPS az a kiosztott átviteli sebesség kiosztott MB / s számítónk történik.

### <a name="ultra-ssd-vm-reservation-fee"></a>Ultranagy SSD Virtuálisgép-foglalás díj

Egy olyan funkció, a virtuális gépen, amely jelzi, hogy a virtuális gép Ultranagy SSD kompatibilis vezetünk be. Egy Ultra SSD kompatibilis virtuális gép foglalja le a számítási Virtuálisgép-példány és a teljesítmény optimalizálása és a késés csökkentése érdekében a blokk tárolóskálázási egységben közötti dedikált sávszélesség-kapacitást. Ez a funkció hozzáadása a Foglalás számítunk fel, amelyek csak áll elő, ha Ultranagy SSD funkció a virtuális gépen engedélyezve van, anélkül, hogy egy Ultra SSD-lemez csatolása a virtuális gép eredményeket. Ha egy Ultra SSD-lemez a Ultranagy SSD-kompatibilis virtuális gép csatlakozik, ez a díj nem fogjuk alkalmazni. Ez a díj a virtuális gép kiépítése vCPU történik.

Tekintse meg a [Azure Disks díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/managed-disks/) az új Ultranagy SSD-lemez díja a részletek korlátozott előzetes verzióban érhetők el.

### <a name="ultra-ssd-preview-scope-and-limitations"></a>Ultranagy SSD előzetes hatókör és korlátozások

Előzetes verzióban, Ultranagy SSD-lemez:

- Kezdetben az támogatni fogja az USA keleti RÉGIÓJA 2, egy rendelkezésre állási zóna  
- Csak akkor használható a rendelkezésre állási zónák (rendelkezésre állási csoportok és egyetlen virtuális gépek üzembe helyezése kívül zónák lesz lehetősége egy Ultra SSD-lemez csatolása)
- Csak a gen/DS v3-as virtuális gépek támogatottak
- Az adatlemezeket és a csak 4k támogatási fizikai szektormérettel csak elérhetők  
- Csak üres lemezként hozható létre  
- Jelenleg kizárólag telepíthető Resource Manager-sablonok, a CLI és a Python SDK használatával.
- Rendszer (még nem) támogatás lemez pillanatképeinek, Virtuálisgép-rendszerképek, a rendelkezésre állási csoportok, Virtual Machine Scale Sets és az Azure Disk Encryption.
- Nem (még) integráció az Azure Backup és az Azure Site Recovery támogatja.
- A [legtöbb előzetes verziók](https://azure.microsoft.com/support/legal/preview-supplemental-terms/), ez a funkció nem használható éles számítási feladatokra amíg általánosan elérhető (GA).

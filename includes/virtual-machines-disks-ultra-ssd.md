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
ms.openlocfilehash: 4b5d2de2e9ccd44517e083a435e127bd5678f002
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2019
ms.locfileid: "56331243"
---
# <a name="ultra-disks-preview-managed-disks-for-azure-virtual-machine-workloads"></a>Ultranagy lemezek (előzetes verzió) a felügyelt lemezek az Azure virtuális gépek számítási feladataihoz

Azure-lemezek ultranagy (előzetes verzió) biztosít a nagy teljesítményű, magas iops-érték és egységes közel valós idejű lemezes tárolás az Azure IaaS virtuális gépek. Az új ajánlat felső részén a meglévő lemezek ajánlataink, ugyanazon rendelkezésre állási szinten sor teljesítményt biztosít. Ultranagy lemezek további előnyök közé tartozik a dinamikusan megváltoztathatja a lemezes célok mellett a virtuális gépek újraindítása nélkül a számítási feladatok teljesítményére. Ultranagy lemezek adatok-igényes számítási feladatokhoz, például az SAP HANA, a felső szintű adatbázisok és a tranzakciós adatokat használó munkaterhelések vannak kialakítva.

## <a name="ultra-disk-features"></a>Ultranagy lemez-szolgáltatások

**A Managed disks**: Ultranagy lemezek, a felügyelt lemezek csak érhetők el. Ultranagy lemezek nem állítható rendszerbe a nem felügyelt lemezek és lapblobok. Felügyelt lemez létrehozásakor adja meg a lemez termékváltozat UltraSSD_LRS típusa, és azt jelzik, lemezt, az IOPS és átviteli sebességért és az Azure létrehozza és felügyeli a lemezt Ön helyett.  

**Virtuális gépek**: Ultranagy lemezeket úgy tervezték, hogy engedélyezve van az Azure VM-termékváltozatok; minden prémium szintű SSD használata Azonban mivel jelenleg előzetes verzióban érhető el, a virtuális gépek mérete gen/DS v3-as.

**A dinamikus Teljesítménykonfiguráció**: Ultranagy lemezek lehetővé teszik a teljesítményt (IOPS és átviteli sebesség) a munkaterhelés igényei és a lemez dinamikus módosítását az a virtuális gépek újraindítása nélkül.

## <a name="scalability-and-performance-targets"></a>Méretezhetőségi és teljesítménycélok

Egy ultra lemezek üzembe helyezésekor, hogy egymástól függetlenül konfigurálhatja a kapacitás és a lemez teljesítménye. Ultranagy lemezek 4 GIB-ra legfeljebb 64 Tib-ra a különféle rögzített méretű származnak, és rugalmas teljesítmény konfigurációs modell, amely lehetővé teszi, hogy külön konfigurálni az IOPS és átviteli sebesség funkció. Ultranagy lemezek csak, az adatlemezeket is javítható. Operációsrendszer-lemezek, prémium szintű SSD-k használatát javasoljuk.

Ultranagy lemezek néhány főbb funkciók a következők:

- Lemez kapacitása: Ultranagy lemezek számos, különböző lemezméretek a 4 GB, legfeljebb 64 Tib-ra.
- A lemez iops-érték: Ultranagy lemezeket támogatja a 300 IOPS/GIB-ra, egy 160 KB IOPS lemezenként maximum IOPS-korlátok. Elérése érdekében az Ön által üzembe helyezett iops-érték, győződjön meg arról, hogy a kiválasztott lemez iops-érték kisebb, mint a virtuális gépek iops-érték. A minimális IOPS lemez 100 iops-t.
- Lemez átviteli sebesség: Ultranagy lemezek átviteli egyetlen határérték 256 KiB/s minden egyes üzembe helyezett IOPS, legfeljebb 2000 MB/s lemezenként (ahol MBps = 10 ^ 6 bájt / másodperc). A minimális átviteli sebesség 1 MiB.

Az alábbi táblázatban a különféle lemezméretek által támogatott konfigurációkat foglaltuk össze:  

### <a name="ultra-disks-managed-disk-offerings"></a>Ultranagy disks – felügyelt lemez ajánlatok

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

Ultranagy lemezeket használ, az alábbi számlázási szempontok lépnek érvénybe:

- Managed Disk Size
- Felügyelt lemez kiosztott iops-érték
- Felügyelt lemez kiosztott átviteli sebesség
- Ultranagy lemez virtuális gép foglalás díj

### <a name="managed-disk-size"></a>Felügyelt lemez mérete

Felügyelt lemezeket a Virtuálisgép-méretek egy új Azure virtuális gép kiépítése során választott számítjuk fel. Az Azure a kiépített méretet a legközelebbi lemezméret-ajánlathoz (kerekítve) rendeli hozzá. A lemezméretek érhető el a részletekért lásd: a tábla, méretezhetőségi és Teljesítménycéljai a fenti szakaszban. Mindegyik lemez képez le egy támogatott kiosztott lemez méretét és ennek megfelelően számlázva lesz óránként. Ha üzembe helyezett egy 200 GiB ultranagy lemezt, és 20 óra múlva törli azt, például azt a lemez mérete vonatkozó ajánlatot 256 GIB-ra lesz leképezve, és kell fizetnie a 256 GIB-ra vonatkozó 20 óra. Ezt a számlázási számítási óra felhasználás függetlenül ténylegesen a lemezre írt adatok mennyisége alapján.

### <a name="managed-disk-provisioned-iops"></a>Felügyelt lemez kiosztott iops-érték

Iops-t is, hogy az alkalmazás által küldött kérések száma másodpercenként a lemezekre. Egy bemeneti/kimeneti műveleti Szekvenciális olvasási vagy írási vagy véletlenszerű olvasási vagy írási. A lemez mérete vagy a virtuális Géphez csatolt lemezek száma alapján, IOPS átlagos száma számlázása óránként. A lemez iops-érték érhető el, tájékozódhat a tábla, méretezhetőségi és Teljesítménycéljai a fenti szakaszban.

### <a name="managed-disk-provisioned-throughput"></a>Felügyelt lemez kiosztott átviteli sebesség

Átviteli sebesség, amely az alkalmazás által küldött, a lemezek egy megadott időszak bájt/s mérve adatok mennyisége. Ha az alkalmazás által végzett nagy bemeneti/kimeneti műveletek, nagy átviteli sebesség szükséges.  

Van egy kapcsolat közötti átviteli sebesség és az IOPS, ahogyan az a következő képletre:  I/o-mérete x IOPS = átviteli sebesség

Ezért fontos meghatározni az optimális átviteli sebesség és az IOPS értékek, az alkalmazás számára szükséges. Próbálja ki az egyik optimalizálása, a másik lekérdezi eszköztípusokra is. Javasoljuk, hogy kezdve egy átviteli megfelelő 16 KiB i/o-mérethez és módosításával, ha a kapacitás növelése érdekében van szükség.

A támogatott adatátviteli sebességet ultranagy lemezen a részletekért lásd: a tábla, méretezhetőségi és Teljesítménycéljai a fenti szakaszban. Például a lemez méretét és az IOPS az a kiosztott átviteli sebesség kiosztott MB / s számítónk történik.

### <a name="ultra-disk-vm-reservation-fee"></a>Ultranagy lemez virtuális gép foglalás díj

Egy olyan funkció, amely jelzi, hogy a virtuális gép kompatibilis ultranagy lemez a virtuális gépen vezetünk be. Kompatibilis virtuális gép foglalja le egy ultra lemez dedikált sávszélesség-kapacitást a számítási Virtuálisgép-példány és a teljesítmény optimalizálása és a késés csökkentése érdekében a blokk tárolóskálázási egységben között. Ez a funkció hozzáadása a Foglalás számítunk fel, amelyek csak áll elő, ha ultranagy lemez funkció a virtuális gépen engedélyezve van, anélkül, hogy egy ultra lemez csatolása a virtuális gép eredményeket. Ha egy ultra lemezt a ultranagy kompatibilis virtuális gép csatlakozik, a díj nem fogjuk alkalmazni. Ez a díj a virtuális gép kiépítése vCPU történik.

Tekintse meg a [Azure Disks díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/managed-disks/) részletekért az új ultranagy lemezek ár korlátozott előzetes verzióban érhető el.

### <a name="ultra-disk-preview-scope-and-limitations"></a>Ultranagy lemez előzetes hatókör és korlátozások

Előzetes verzióban, ultranagy lemezek:

- Kezdetben az támogatni fogja az USA keleti RÉGIÓJA 2, egy rendelkezésre állási zóna  
- Csak akkor használható a rendelkezésre állási zónák (rendelkezésre állási csoportok és egyetlen virtuális gépek üzembe helyezése zónák lesz kívül nem tudnak egy ultra lemezt)
- Csak a gen/DS v3-as virtuális gépek támogatottak
- Az adatlemezeket és a csak 4k támogatási fizikai szektormérettel csak elérhetők  
- Csak üres lemezként hozható létre  
- Jelenleg kizárólag telepíthető Resource Manager-sablonok, a CLI és a Python SDK használatával.
- Rendszer (még nem) támogatás lemez pillanatképeinek, Virtuálisgép-rendszerképek, a rendelkezésre állási csoportok, Virtual Machine Scale Sets és az Azure Disk Encryption.
- Nem (még) integráció az Azure Backup és az Azure Site Recovery támogatja.
- A [legtöbb előzetes verziók](https://azure.microsoft.com/support/legal/preview-supplemental-terms/), ez a funkció nem használható éles számítási feladatokra amíg általánosan elérhető (GA).

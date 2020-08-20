---
title: Az Azure Backup díjszabása
description: Megtudhatja, hogyan becsülheti meg a költségvetési Azure Backup díjszabásának költségeit.
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: 03ec0076d3089562ddaace6db413fb3f1ba949a6
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88654531"
---
# <a name="azure-backup-pricing"></a>Az Azure Backup díjszabása

Ha többet szeretne megtudni a Azure Backup díjszabásáról, látogasson el a [Azure Backup díjszabási oldalára](https://azure.microsoft.com/pricing/details/backup/).

## <a name="download-detailed-estimates-for-azure-backup-pricing"></a>Részletes becslések letöltése Azure Backup díjszabásról

Ha szeretné megbecsülni a költségvetést vagy a költségek összehasonlítását, töltse le a részletes [Azure Backup díjszabását](https://aka.ms/AzureBackupCostEstimates).  

### <a name="what-does-the-estimator-contain"></a>Mit tartalmaz a kalkulátor?

A Azure Backup Cost kalkulátor adatlapjának segítségével megbecsülheti az összes lehetséges munkaterhelést, amelyről biztonsági másolatot szeretne készíteni Azure Backup használatával. Ezek a számítási feladatok a következők:

- Azure-beli virtuális gépek
- Helyszíni kiszolgálók
- SQL Azure-beli virtuális gépeken
- SAP HANA Azure-beli virtuális gépeken
- Azure Files-megosztások

## <a name="estimate-costs-for-backing-up-azure-vms-or-on-premises-servers"></a>Az Azure-beli virtuális gépek vagy a helyszíni kiszolgálók biztonsági mentésével kapcsolatos költségek becslése

Az Azure-beli virtuális gépek vagy a helyszíni kiszolgálók Azure Backup használatával történő biztonsági mentésével kapcsolatos költségek megbecsléséhez a következő paramétereket kell megadnia:

- Azon virtuális gépek vagy helyszíni kiszolgálók mérete, amelyekről biztonsági mentést szeretne készíteni
  - Adja meg a biztonsági mentéshez szükséges lemezek vagy kiszolgálók "felhasznált méretét".

- A mérettel rendelkező kiszolgálók száma

- Milyen mennyiségű adatforgalom várható a kiszolgálókon?<br>
  A forgalom az adatváltozások mennyiségére utal. Ha például a virtuális gép 200 GB-nyi adatról készített biztonsági mentést, és minden nap 10 GB-ot módosít, a napi forgalom 5%.

  - A nagyobb adatforgalom azt jelenti, hogy több adattal kell biztonsági másolatot készíteni

  - A fájlkiszolgálók és a **magas** , ha adatbázisok futtatásakor **alacsony** vagy **közepes** értéket vesz igénybe.

  - Ha ismeri a **(z)%-os**adatváltozást, használhatja a **saját% megadása** lehetőséget.

- Válassza ki a biztonsági mentési szabályzatot

  - Meddig várható a napi biztonsági másolatok megőrzése? (napban)

  - Mennyi ideig tart a heti biztonsági másolatok megőrzése? (hetek)

  - Meddig várható a "havi" biztonsági mentések megőrzése? (hónap)

  - Meddig várható az "éves" biztonsági mentések megőrzése? (években)

  - Mennyi ideig tart az azonnali visszaállítási Pillanatképek megőrzése? (1-5 nap)

    - Ez a beállítás lehetővé teszi, hogy a lemezeken tárolt Pillanatképek gyors használatával akár hét nap múlva visszaállítsa a visszaállítást.

- **Opcionális** – szelektív lemezes biztonsági mentés

  - Ha a **szelektív lemezes biztonsági mentés** lehetőséget használja az Azure-beli virtuális gépek biztonsági mentése során, válassza a **lemez kizárása** lehetőséget, és adja meg a biztonsági mentésből kizárt lemezek százalékos arányát a méret szempontjából. Ha például van egy virtuális gép, amely az egyes lemezeken a 200 GB-ot használó három lemezhez csatlakozik, és ha ki szeretne zárni kettőt a biztonsági mentésből, írja be a következőt: 66,7%.

- **Opcionális** – biztonsági mentési tárterület-redundancia

  - Ez azt jelzi, hogy a biztonsági mentési adataiba bekerülő Storage-fiók redundancia. Javasoljuk, hogy a **GRS** használatát a legmagasabb rendelkezésre állás érdekében. Mivel biztosítja, hogy a biztonsági mentési adatait egy másik régióban tárolja, így több megfelelőségi szabványnak is megfelel. Módosítsa a redundanciát a **LRS** , ha olyan fejlesztési vagy tesztelési környezetekről készít biztonsági másolatot, amelyeknek nincs szükségük vállalati szintű biztonsági mentésre. Válassza a **RAGRS** lehetőséget a lapon, ha szeretné megismerni a költségeket, amikor a [régiók közötti visszaállítás](backup-azure-arm-restore-vms.md#cross-region-restore) engedélyezve van a biztonsági másolatokhoz.

- **Opcionális** – regionális díjszabás módosítása vagy kedvezményes díjszabás alkalmazása

  - Ha egy másik régióra vagy kedvezményes díjszabásra vonatkozó becsléseket szeretne megtekinteni, válassza az **Igen** lehetőséget **egy másik régióra vonatkozó kipróbálási becslések esetében?** lehetőségre, és adja meg azokat a díjakat, amelyeknek a becsléseit futtatni kívánja.

## <a name="estimate-costs-for-backing-up-sql-servers-in-azure-vms"></a>Az SQL-kiszolgálók Azure-beli virtuális gépeken történő biztonsági mentésének becsült költségei

Ha az Azure-beli virtuális gépeken futó SQL Server-kiszolgálók biztonsági mentésének költségeit szeretné megbecsülni Azure Backup használatával, a következő paramétereket kell megadnia:

- Azon SQL-kiszolgálók mérete, amelyekről biztonsági mentést szeretne készíteni

- A fenti mérettel rendelkező SQL-kiszolgálók száma

- Mi a várt tömörítés az SQL Server biztonsági mentési adataihoz?

  - A legtöbb Azure Backup ügyfél azt látja, hogy a biztonsági mentési adatmennyiség 80%-os tömörítéssel van összehasonlítva az SQL-kiszolgáló méretével, ha az SQL-tömörítés **engedélyezve**van

  - Ha várhatóan eltérő tömörítést szeretne látni, adja meg az ebben a mezőben szereplő számot.

- Mi a naplózott biztonsági másolatok várható mérete?

  - A (z)% a napi naplózási méretet jelöli az SQL Server méretének%-ában

- Milyen mennyiségű napi adatforgalom várható a kiszolgálókon?

  - Az adatbázisok jellemzően "magas" adatforgalommal rendelkeznek

  - Ha ismeri a **(z)%-os**adatváltozást, használhatja a **saját% megadása** lehetőséget.

- Válassza ki a biztonsági mentési szabályzatot

  - Biztonsági mentés típusa

    - A leghatékonyabb kiválasztható szabályzat **napi különbség** a heti/havi/éves teljes biztonsági mentéssel. A Azure Backup egy kattintással is visszaállíthatja a különbségeket.

    - Azt is beállíthatja, hogy a szabályzat napi/heti/havi/éves teljes biztonsági mentéssel rendelkezzen. Ezzel a beállítással a rendszer valamivel több tárterületet használ, mint az első lehetőség.

  - Mennyi ideig tart a "napló" biztonsági mentések megőrzése? (napban) [7-35]

  - Meddig várható a napi biztonsági másolatok megőrzése? (napban)

  - Mennyi ideig tart a heti biztonsági másolatok megőrzése? (hetek)

  - Meddig várható a "havi" biztonsági mentések megőrzése? (hónap)

  - Meddig várható az "éves" biztonsági mentések megőrzése? (években)

- **Opcionális** – biztonsági mentési tárterület-redundancia

  - Ez azt jelzi, hogy a biztonsági mentési adataiba bekerülő Storage-fiók redundancia. Javasoljuk, hogy a **GRS** használatát a legmagasabb rendelkezésre állás érdekében. Mivel biztosítja, hogy a biztonsági mentési adatait egy másik régióban tárolja, így több megfelelőségi szabványnak is megfelel. Módosítsa a redundanciát a **LRS** , ha olyan fejlesztési vagy tesztelési környezetekről készít biztonsági másolatot, amelyeknek nincs szükségük vállalati szintű biztonsági mentésre.

- **Opcionális** – regionális díjszabás módosítása vagy kedvezményes díjszabás alkalmazása

  - Ha egy másik régióra vagy kedvezményes díjszabásra vonatkozó becsléseket szeretne megtekinteni, válassza az **Igen** lehetőséget **egy másik régióra vonatkozó kipróbálási becslések esetében?** lehetőségre, és adja meg azokat a díjakat, amelyeknek a becsléseit futtatni kívánja.

## <a name="estimate-costs-for-backing-up-sap-hana-servers-in-azure-vms"></a>A SAP HANA-kiszolgálók Azure-beli virtuális gépeken történő biztonsági mentésével kapcsolatos költségek becslése

Az Azure-beli virtuális gépeken futó SAP HANA-kiszolgálók Azure Backup használatával történő biztonsági mentésével kapcsolatos költségek megbecsléséhez a következő paraméterekre lesz szüksége:

- Azon SAP HANA adatbázisok teljes mérete, amelyekről biztonsági mentést szeretne készíteni. Az egyes adatbázisok teljes biztonsági mentési méretének összegének kell lennie, amelyet SAP HANA jelentett.
- A fenti mérettel rendelkező SAP HANA kiszolgálók száma
- Mi a naplózott biztonsági másolatok várható mérete?
  
  - A (z)% megadja az átlagos napi naplózási méretet a SAP HANA adatbázisok teljes méretének%-ában, amelyekről biztonsági másolatot készít a SAP HANA-kiszolgálón
- Milyen mennyiségű napi adatforgalom várható a kiszolgálókon?
  - A (z)% az átlagos napi adatváltozási méretet jelzi SAP HANA adatbázisok teljes méretének%-ában, amelyekről biztonsági másolatot készít a SAP HANA-kiszolgálón
  - Az adatbázisok jellemzően "magas" adatforgalommal rendelkeznek
  - Ha ismeri a **(z)%-os**adatváltozást, használhatja a **saját% megadása** lehetőséget.
- Válassza ki a biztonsági mentési szabályzatot
  - Biztonsági mentés típusa
    - A leghatékonyabb kiválasztható szabályzat **napi különbség** a **heti/havi/éves** teljes biztonsági mentéssel. A Azure Backup egy kattintással is visszaállíthatja a különbségeket.
    - Azt is beállíthatja, hogy a szabályzat **napi/heti/havi/éves** teljes biztonsági mentéssel rendelkezzen. Ezzel a beállítással a rendszer valamivel több tárterületet használ, mint az első lehetőség.
  - Mennyi ideig tart a "napló" biztonsági mentések megőrzése? (napban) [7-35]
  - Meddig várható a napi biztonsági másolatok megőrzése? (napban)
  - Mennyi ideig tart a heti biztonsági másolatok megőrzése? (hetek)
  - Meddig várható a "havi" biztonsági mentések megőrzése? (hónap)
  - Meddig várható az "éves" biztonsági mentések megőrzése? (években)
- **Opcionális** – biztonsági mentési tárterület-redundancia
  
  - Ez azt jelzi, hogy a biztonsági mentési adataiba bekerülő Storage-fiók redundancia. Javasoljuk, hogy a **GRS** használatát a legmagasabb rendelkezésre állás érdekében. Mivel biztosítja, hogy a biztonsági mentési adatait egy másik régióban tárolja, így több megfelelőségi szabványnak is megfelel. Módosítsa a redundanciát a **LRS** , ha olyan fejlesztési vagy tesztelési környezetekről készít biztonsági másolatot, amelyeknek nincs szükségük vállalati szintű biztonsági mentésre.
- **Opcionális** – regionális díjszabás módosítása vagy kedvezményes díjszabás alkalmazása
  
  - Ha egy másik régióra vagy kedvezményes díjszabásra vonatkozó becsléseket szeretne megtekinteni, válassza az **Igen** lehetőséget **egy másik régióra vonatkozó kipróbálási becslések esetében?** lehetőségre, és adja meg azokat a díjakat, amelyeknek a becsléseit futtatni kívánja.
  
## <a name="estimate-costs-for-backing-up-azure-file-shares"></a>Az Azure-fájlmegosztás biztonsági mentésével kapcsolatos költségek becslése

Az Azure-fájlmegosztás biztonsági mentésének költségeit a Azure Backup által kínált [Pillanatkép-alapú biztonsági mentési megoldással](azure-file-share-backup-overview.md) lehet megbecsülni, a következő paraméterekre lesz szüksége:

- Azon fájlmegosztás mérete (**GB-ban**), amelyekről biztonsági másolatot szeretne készíteni.

- Ha biztonsági mentést szeretne készíteni a fájlmegosztás több Storage-fiók között, adja meg a fenti mérettel rendelkező fájlmegosztás tárolására szolgáló tárolási fiókok számát.

- Az adatváltozás várható mennyisége azon fájlmegosztás esetében, amelyekről biztonsági másolatot szeretne készíteni. <br>A forgalom az adatok változásának mennyiségére utal, és közvetlenül befolyásolja a pillanatképek tárolási méretét. Ha például egy fájlmegosztás 200 GB-nyi adatról készít biztonsági mentést, és minden nap 10 GB-nyi változás történik, a napi forgalom 5%.
  - A magasabb adatforgalom azt jelenti, hogy a fájlmegosztás tartalmának minden napján nagy mennyiségű adat változik, így a növekményes pillanatfelvétel (csak az adatváltozások rögzítése) is nagyobb.
  - Válassza az alacsony (1%), a mérsékelt (3%) vagy a magas (5%) értéket a fájlmegosztás jellemzői és használata alapján.
  - Ha ismeri a **fájlmegosztás pontos** adatforgalmát, akkor a legördülő menüből kiválaszthatja a **saját% megadása** lehetőséget. Értékek meghatározása (%) napi, heti, havi és éves adatforgalom esetén.

- A Storage-fiók típusa (standard vagy prémium) és a tároló-redundancia beállítása a biztonsági másolatba mentett fájlmegosztást tároló fiók számára. <br>Az Azure-fájlmegosztás jelenlegi biztonsági mentési megoldásában a pillanatképek tárolása ugyanabban a Storage-fiókban történik, mint a mentett fájlmegosztás. Így a pillanatképekhez kapcsolódó tárolási költség az Azure Files számla részeként kerül kiszámlázásra, a biztonsági másolatban szereplő fájlmegosztás és Pillanatképek tárolási fiókjának a fiók típusa és a redundancia beállítása alapján.

- Különböző biztonsági másolatok megőrzése
  - Meddig várható a napi biztonsági másolatok megőrzése? (napban)
  - Mennyi ideig tart a heti biztonsági másolatok megőrzése? (hetek)
  - Meddig várható a "havi" biztonsági mentések megőrzése? (hónap)
  - Meddig várható az "éves" biztonsági mentések megőrzése? (években)

  Tekintse meg az [Azure file share támogatási mátrixot](azure-file-share-support-matrix.md#retention-limits) az egyes kategóriákban támogatott adatmegőrzési értékek maximális számaként.

- **Opcionális** – a regionális díjszabás módosítása vagy kedvezményes díjszabás alkalmazása.
  - Az USA keleti régiója számára az alapértelmezett érték a pillanatkép-tároláshoz és a védett példányok díja a kalkulátorban. Ha egy másik régióra vagy kedvezményes díjszabásra vonatkozó becsléseket szeretne megtekinteni, válassza az **Igen** lehetőséget **egy másik régióra vonatkozó kipróbálási becslésekhez?** lehetőségre, és adja meg azokat a díjakat, amelyeken a becsléseket futtatni kívánja.

## <a name="next-steps"></a>További lépések

[Mi az az Azure App Service?](backup-overview.md)

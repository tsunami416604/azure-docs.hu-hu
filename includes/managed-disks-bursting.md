---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/29/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 84736b7f1dcdf8b186fddbced5dd773e008c0dd2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80887426"
---
A Disk burst prémium SSD-k esetén támogatott. A burst bármely prémium szintű SSD-lemez mérete <= 512 GiB (P20 vagy lent). Ezeknek a lemezeknek a mérete támogatja a legjobb erőfeszítést, és egy kreditrendszer használatával kezeli a kitörést. A kreditek felhalmozódnak egy burst gyűjtőben, amikor a lemezes forgalom a lemez méretéhez tartozó kiépített teljesítmény alatt van, és kreditet használ, ha a forgalom a célponton túl van. A lemezes forgalom a kiépített cél IOPS és sávszélességével is nyomon követhető. A lemezek kitörése nem fogja megkerülni a virtuális gép (VM) méretének korlátozásait a IOPS vagy az átviteli sebesség tekintetében.

A lemez kitörése alapértelmezés szerint engedélyezve van az azt támogató lemezek új központi telepítésen. A meglévő lemezek mérete, ha támogatják a lemezek kitörését, a következő módszerek egyikével engedélyezheti a kitörést:

- Válassza le és csatlakoztassa újra a lemezt.
- Állítsa le és indítsa el a virtuális gépet.

## <a name="burst-states"></a>Burst állapotok

Az összes érintett lemez mérete a teljes burst kredit gyűjtővel kezdődik, amikor a lemez csatlakoztatva van egy virtuális géphez. A kitörés maximális időtartamát a burst kredit gyűjtő mérete határozza meg. A fel nem használt kreditek csak a kredit gyűjtő méretére gyűjthetők fel. A lemez burst kreditje a következő három állapot egyikében lehet: 

- Ha a lemez forgalma kevesebb, mint a kiépített teljesítmény. Kreditet gyűjthet, ha a lemezes forgalom a IOPS vagy a sávszélességre, vagy mindkettőre esik. Az i/o-kreditek továbbra is felhalmozható, ha teljes lemezes sávszélességet használ, fordítva.  

- Csökken, ha a lemez forgalma több, mint a kiosztott teljesítményű célpontot használja. A burst forgalom egymástól függetlenül fogja felhasználni a IOPS vagy a sávszélességtől kapott krediteket. 

- Hátralévő állandó, ha a lemez forgalma pontosan a kiépített teljesítmény célpontja. 

A burst-támogatást biztosító lemez méretét az alábbi táblázat foglalja össze.

## <a name="regional-availability"></a>Régiónkénti rendelkezésre állás

A lemez kitörése a nyilvános felhőben található összes régióban elérhető.

## <a name="disk-sizes"></a>Lemezek mérete

[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

## <a name="example-scenarios"></a>Példaforgatókönyvek

A működésének jobb megismeréséhez tekintse meg az alábbi példákat:

- Egy gyakori forgatókönyv, amely kihasználhatja a lemezek betöltését, gyorsabb virtuálisgép-rendszerindítást és alkalmazások indítását teszi lehetővé operációsrendszer-lemezeken. Egy Linux rendszerű virtuális gépet egy 8 GiB operációsrendszer-képpel is használhat példaként. Ha P2-lemezt használ operációsrendszer-lemezként, a kiépített cél 120 IOPS és 25 MiB. A virtuális gép indításakor a rendszerindító fájlok betöltésére szolgáló olvasási tüske jelenik meg az operációsrendszer-lemezen. A burst bevezetésével elolvashatja a 3500 IOPS és a 170 MiB maximális burst sebességét, így a betöltési időt legalább 6x-ra felgyorsíthatja. A virtuális gép rendszerindítását követően az operációsrendszer-lemezen lévő forgalmi szint általában alacsony, mivel az alkalmazás legtöbb adatművelete a csatlakoztatott adatlemezekre fog esni. Ha a forgalom a kiépített cél alá esik, akkor a kreditek felhalmozódnak.

- Ha távoli virtuális asztali környezetet üzemeltet, amikor egy aktív felhasználó olyan alkalmazást indít el, mint az AutoCAD, az operációsrendszer-lemezre irányuló forgalom jelentős mértékben megnő. Ebben az esetben a burst forgalom felhasználja a felhalmozódott krediteket, így a kiépített célponton túl is haladhat, és sokkal gyorsabban elindíthatja az alkalmazást.

- A P1-lemezek 120 IOPS és 25 MiB kiépített céllal rendelkeznek. Ha a lemez tényleges forgalma 100 IOPS és 20 MiB volt az elmúlt 1 másodpercben, akkor a nem használt 20 IOs és 5 MB a lemez burst-gyűjtője számára van jóváírva. A burst-gyűjtőben lévő kreditek később is használhatók, ha a forgalom meghaladja a kiépített célt, a maximális burst korlátig. A maximális burst korlát határozza meg a lemez forgalmának felső határát, még akkor is, ha a rendszer felhasználja a forgalmat. Ebben az esetben még akkor is, ha a kredit gyűjtőben már van 10 000 IOs, a P1 lemez nem tud több, mint a 3 500 IO maximális száma másodpercenként.  

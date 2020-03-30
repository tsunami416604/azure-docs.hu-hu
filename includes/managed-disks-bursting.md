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
ms.openlocfilehash: 871a3edf70690a09d3747703e8bc999dfcce967c
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385180"
---
A lemezfelszakítás prémium szintű SSD-k esetében támogatott. A bursting minden prémium szintű SSD lemezméreten támogatott, <= 512 GiB (P20 vagy az alatt). Ezek a lemezméretek támogatják a felszakítást a legjobb erőfeszítés alapján, és egy kreditrendszert használnak a felszakítás kezeléséhez. A kreditek egy burst-gyűjtőben halmozódnak fel, ha a lemezforgalom a lemezméretük kiosztott teljesítménycél alatt van, és krediteket használnak fel, amikor a forgalom a célon túl tör. A lemezforgalom nyomon követhető az IOPS és a sávszélesség a kiépített cél. A lemezfelszakítás nem kerüli meg a virtuális gép (VM) méretkorlátait az IOPS vagy az átviteli rendszer esetében.

A lemezfelszakítás alapértelmezés szerint engedélyezve van az azt támogató lemezméretek új központi telepítésein. A meglévő lemezméretek, ha támogatják a lemezfelszakítást, az alábbi módszerek bármelyikén keresztül engedélyezhetik a felszakítást:

- Válassza le, majd csatlakoztassa újra a lemezt.
- Állítsa le és indítsa el a virtuális gép.

## <a name="burst-states"></a>Burst állapotok

Az összes sorozattal rendelkező lemezméret egy teljes burst jóváírási gyűjtővel kezdődik, amikor a lemez egy virtuális géphez van csatlakoztatva. A felszakítás maximális időtartamát a burst hitelgyűjtő mérete határozza meg. A fel nem használt krediteket csak a kreditgyűjtő méretéig halmozhatja fel. A lemezsorozat-sorozathitel-gyűjtő bármikor a következő három állapot valamelyikében lehet: 

- Felhalmozódó, ha a lemezforgalom kevesebb, mint a kiosztott teljesítménycél. Kreditet halmozhat fel, ha a lemezforgalom meghaladja az IOPS- vagy sávszélesség-célokat vagy mindkettőt. Az IO-krediteket akkor is felhalmozhatja, ha teljes lemezsávszélességet fogyaszt, fordítva.  

- Csökkenő, ha a lemezforgalom a kiosztott teljesítménycélnál többet használ. A burst forgalom önállóan fogyaszt krediteket IOPS vagy sávszélesség. 

- Változatlan ult, ha a lemezforgalom pontosan a kiosztott teljesítménycél. 

A felszakítási támogatást nyújtó lemezméretek, valamint a burst specifikációk az alábbi táblázatban kerülnek összegzésre.

## <a name="regional-availability"></a>Régiónkénti rendelkezésre állás

A lemezfelszakítás a nyilvános felhő minden régiójában elérhető.

## <a name="disk-sizes"></a>Lemezméretek

[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

## <a name="example-scenarios"></a>Példaforgatókönyvek

Annak érdekében, hogy jobb képet kapjaarról, hogyan működik ez, az alábbi példa:

- Az egyik gyakori forgatókönyv, amely előnyös lehet a lemezfelszakítás gyorsabb virtuális gép indítása és az alkalmazás indítása az operációs rendszer lemezein. Vegyünk egy Linux virtuális gép egy 8 GiB os rendszerkép példaként. Ha egy P2-lemezt használunk operációsrendszer-lemezként, a kiosztott cél 120 IOPS és 25 Mb/s. Amikor a virtuális gép elindul, lesz egy olvasási tüske az operációs rendszer lemeze betöltése a rendszerindító fájlokat. A felszakadás bevezetésével 3500 IOPS és 170 Mb/s maximális lökéssebességgel olvashat, ami legalább 6-kal gyorsítja a betöltési időt. Virtuális gép indítása után az operációs rendszer lemezének forgalmi szintje általában alacsony, mivel az alkalmazás által végzett legtöbb adatművelet a csatolt adatlemezekellen lesz. Ha a forgalom a kiosztott cél alatt van, krediteket halmoz fel.

- Ha távoli virtuális asztali környezetet üzemeltet, amikor egy aktív felhasználó elindítja az AutoCAD-hez hasonló alkalmazást, jelentősen megnő az operációs rendszer lemezére irányuló forgalom olvasása. Ebben az esetben a burst forgalom felgyülemlett krediteket fog használni, lehetővé téve, hogy túllépjön a kiosztott célon, és sokkal gyorsabban indítja el az alkalmazást.

- A P1-lemez kiosztott cél120 IOPS és 25 Mb/s. Ha a lemez tényleges forgalma 100 IOPS és 20 Mb/s volt az elmúlt 1 másodpercben, akkor a fel nem használt 20 IOs és 5 MB jóváírásra kerül a lemez burst gyűjtője. A burst vödrében lévő kreditek később használhatók, ha a forgalom meghaladja a kiosztott célt, a maximális burst limitig. A maximális burst limit határozza meg a lemezforgalom felső határát, még akkor is, ha burst krediteket használ. Ebben az esetben, még akkor is, ha 10 000 IOs van a hitelgyűjtőben, a P1 lemez nem adhat ki többet, mint a másodpercenként 3500 i/o maximális sorozat.  

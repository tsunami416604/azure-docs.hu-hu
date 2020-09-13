---
title: fájlbefoglalás
description: fájlbefoglalás
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 07/07/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 65f6c239f34775efff6a2ea2e399064a7702606a
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2020
ms.locfileid: "89664145"
---
![A Dsv3 dokumentációja](media/vm-disk-performance/dsv3-documentation.jpg)

A nem **gyorsítótárazott** lemez maximális átviteli sebessége a virtuális gép által kezelhető alapértelmezett tárolási korlát. A **gyorsítótárazott** tárterület maximális átviteli sebessége a gazdagép gyorsítótárazásának engedélyezésekor külön korlát. A gazdagép-gyorsítótárazás engedélyezése a virtuális gép létrehozásakor és a lemezek csatlakoztatásakor végezhető el. Azt is megteheti, hogy be-és kikapcsolja az állomás gyorsítótárazza a lemezeket egy meglévő virtuális gépen:

![Gazdagép gyorsítótárazása](media/vm-disk-performance/host-caching.jpg)

A gazdagép gyorsítótárazása beállítható úgy, hogy az megfeleljen az egyes lemezek munkaterhelési követelményeinek. Beállíthatja, hogy a gazdagép gyorsítótárazása csak olvasható legyen az olyan munkaterhelések esetében, amelyek csak olvasási műveleteket hajtanak végre, és olvasási/írási műveleteket végeznek az olvasási és írási műveletek egyenlegét biztosító munkaterhelések esetében. Ha a munkaterhelés egyike sem követi ezeket a mintákat, sajnos nem fogja tudni használni a gazdagép gyorsítótárazását. 

Folytassa a Standard_D8s_v3 virtuális géppel folytatott példával. Ebben az esetben a gazdagépek gyorsítótárazását engedélyezjük a lemezeken, és most a virtuális gép IOPS korlátja 16 000 IOPS. A virtuális géphez csatolt három mögöttes P30-lemez, amely képes kezelni a 5 000 IOPS.

Beállítás:
- Standard_D8s_v3 
    - Gyorsítótárazott IOPS: 16 000
    - Nem gyorsítótárazott IOPS: 12 800
- P30 operációsrendszer-lemez 
    - IOPS: 5 000
    - Gazdagép gyorsítótárazása engedélyezve 
- 2 P30 adatlemez
    - IOPS: 5 000
    - Gazdagép gyorsítótárazása engedélyezve

![Gazdagép gyorsítótárazási példája](media/vm-disk-performance/host-caching-example-without-remote.jpg)

Mostantól az ezt a Standard_D8s_v3 virtuális gépet használó alkalmazás a gyorsítótárazást engedélyező kérelem 15 000 IOPS. Ezeket a kérelmeket 5 000 IOPS kell bontani az egyes mögöttes lemezekhez, és nincs szükség a teljesítményre.

## <a name="combined-uncached-and-cached-limits"></a>Kombinált gyorsítótár nélküli és gyorsítótárazott korlátok

A virtuális gép gyorsítótárazási korlátai eltérnek a gyorsítótárazatlan korlátoktól. Ez azt jelenti, hogy engedélyezheti a gazdagépek gyorsítótárazását a virtuális géphez csatlakoztatott lemezeken, miközben más lemezeken nem engedélyezi a gazdagépek gyorsítótárazását, így a virtuális gépek a gyorsítótárazott korlát és a gyorsítótár nélküli korlát teljes tárolási i/o-értékének megszerzését teszik lehetővé. Futtassunk egy példát erre, hogy megszilárdítsuk, hogyan működnek együtt a korlátok, és folytatjuk a Standard_D8s_v3 virtuális gép és a prémium szintű lemezek csatolt konfigurációját.

Beállítás:
- Standard_D8s_v3 
    - Gyorsítótárazott IOPS: 16 000
    - Nem gyorsítótárazott IOPS: 12 800
- P30 operációsrendszer-lemez 
    - IOPS: 5 000
    - Gazdagép gyorsítótárazása engedélyezve 
- 2 P30 adatlemez X 2
    - IOPS: 5 000
    - Gazdagép gyorsítótárazása engedélyezve
- 2 P30 adatlemez X 2
    - IOPS: 5 000
    - Gazdagép gyorsítótárazása letiltva

![Gazdagép-gyorsítótárazási példa távoli tárolóval](media/vm-disk-performance/host-caching-example-with-remote.jpg)

A Standard_D8s_v3 virtuális gépen futó alkalmazás a 25 000 IOPS-ra vonatkozó kérelmet tesz elérhetővé. Ez a kérelem 5 000 IOPS-ként van lebontva az egyes mögöttes lemezekhez, ahol a lemezek 3-a gazdagép-gyorsítótárazást használ, és a lemezek közül 2 nem. Mivel a 3 a gazdagép-gyorsítótárazást használja a 16 000-es gyorsítótáras korláton belül, ezek a kérések sikeresen befejeződtek, és nem történik meg a tárolási teljesítmény korlátozása. Továbbá, mivel a 2 lemez nem használ gazdagép-gyorsítótárazást, a 12 800-as gyorsítótáras korláton belül vannak, ezek a kérések is sikeresen befejeződtek, és nem történik meg a maximális korlátozás.

## <a name="metrics-for-disk-performance"></a>A lemezek teljesítményének mérőszámai
Az Azure-ban olyan mérőszámok találhatók, amelyek betekintést nyújtanak a virtuális gépek és lemezek teljesítményére. Ezek a metrikák a Azure Portal vizuálisan megtekinthetők, vagy egy API-hívással kérhetők le. A metrikák kiszámítása egyperces időközökkel történik. A következő mérőszámok érhetők el a virtuális gép és a lemez i/o-és átviteli teljesítményének megismeréséhez:
- **Operációsrendszer-lemez várólistájának mélysége** – az operációsrendszer-lemezre való beolvasásra vagy írásra váró aktuálisan fennálló i/o-kérelmek száma.
- **Operációsrendszer-lemez olvasási sebessége (bájt/s)** – az operációsrendszer-lemezről egy másodpercben olvasott bájtok száma.
- **Operációsrendszer-lemez olvasási műveletei/mp** – az operációsrendszer-lemezről egy másodpercben beolvasott bemeneti műveletek száma.
- **Operációsrendszer-lemez írási sebessége (bájt/s)** – az operációs rendszer lemezének másodpercében írt bájtok száma.
- **Operációsrendszer-lemez írási műveletei/mp** – az operációsrendszer-lemez másodpercben írt kimeneti műveleteinek száma.
- **Adatlemez-várólista mélysége** – az adatlemez (ek) re való beolvasásra vagy az azokba való írásra váró aktuálisan fennálló i/o-kérelmek száma.
- **Adatlemez-olvasási sebesség (bájt/mp** ) – az adatlemez (ek) egy másodpercben olvasott bájtok száma.
- **Adatlemez olvasási műveletei/mp** – a másodpercenként beolvasott bemeneti műveletek száma egy adatlemez (ek) ről.
- **Adatlemez-írási sebesség (bájt/mp** ) – az adatlemez (ek) második részében írt bájtok száma.
- **Adatlemez írási műveletei/mp** – az adatlemez (ek) másodpercben írt kimeneti műveleteinek száma.
- **Lemez olvasási sebessége (bájt/s)** – a virtuális géphez csatolt összes lemezről beolvasott bájtok másodpercenkénti száma.
- **Lemez olvasási műveletei/mp** – a virtuális géphez csatolt összes lemezről beolvasott bemeneti műveletek másodpercenkénti száma.
- **Lemez írási sebessége (bájt/s)** – a virtuális géphez csatolt összes lemezről másodpercek alatt írt bájtok száma.
- **Lemez írási műveletei/mp** – a virtuális géphez csatolt összes lemezről másodpercek alatt írt kimeneti műveletek száma.

## <a name="storage-io-utilization-metrics"></a>Storage IO kihasználtsági metrikái
A lemez i/o-korlátjának diagnosztizálását segítő mérőszámok:
- **Adatlemez IOPS** kihasználtsága (%) – az adatlemez IOPS kiszámított százalékos arány a kiépített adatlemez IOPS. Ha ez az érték 100%-os, az alkalmazás futása az adatlemez IOPS-korlátja alapján lesz lekorlátozva.
- **Adatlemez sávszélességének** kihasználtsága (%) – az adatlemez átviteli sebessége által kiszámított százalékos arány a kiépített adatlemez átviteli sebessége után. Ha ez az érték 100%-os, az alkalmazás futása az adatlemez sávszélesség-korlátja alapján lesz lekorlátozva.
- **Operációsrendszer-lemez IOPS felhasznált százaléka** – az operációsrendszer-lemez IOPS kiszámított százalék a kiépített operációsrendszer-lemez IOPS. Ha ez az érték 100%-os, akkor az alkalmazás futása az operációsrendszer-lemez IOPS-korlátja alapján lesz lekorlátozva.
- **Operációsrendszer-lemez sávszélességének** kihasználtsága (százalék) – az operációsrendszer-lemez átviteli sebessége által kiszámított százalék a kiépített operációsrendszer-lemez átviteli sebessége után. Ha ez az érték 100%-os, az alkalmazás futása az operációsrendszer-lemez sávszélesség-korlátja alapján lesz lekorlátozva.
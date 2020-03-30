---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/15/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 72d87142f9b9c1f7bcb2b02281851bd1e29bc9c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78262594"
---
Az Azure által felügyelt lemezek jelenleg négy lemeztípust kínálnak, minden típus konkrét ügyfél-forgatókönyvekre irányul.

## <a name="disk-comparison"></a>Lemezek összehasonlítása

Az alábbi táblázat az ultralemezek, a prémium szintű SSD-meghajtók, a szabványos SSD-k és a felügyelt lemezek szabványos merevlemez-meghajtóinak (HDD) összehasonlítását tartalmazza, hogy segítsen eldönteni, hogy mit használjon.

|   | Ultralemez   | Prémium SSD   | Standard SSD   | Standard HDD   |
|---------|---------|---------|---------|---------|
|Lemeztípus   |SSD   |SSD   |SSD   |HDD   |
|Forgatókönyv   |Io-igényes számítási feladatok, például [az SAP HANA,](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)legfelső szintű adatbázisok (például SQL, Oracle) és más tranzakciós nagy számítási feladatok.   |Éles, teljesítményérzékeny számítási feladatok   |Webkiszolgálók, kisebb terhelésű vállalati alkalmazások és fejlesztés/tesztelés   |Biztonsági mentés, nem kritikus, ritka hozzáférés   |
|Lemez maximális mérete   |65 536 gibibájt (GiB)    |32 767 GiB    |32 767 GiB   |32 767 GiB   |
|Maximális átviteli sebesség   |2000 MiB/s    |900 MiB/s   |750 MiB/s   |500 MiB/s   |
|Maximális IOPS-érték   |160 000    |20000   |6.000   |2000   |

## <a name="ultra-disk"></a>Ultralemez

Az Azure ultralemezei magas átviteli sebességet, magas IOPS-t, és konzisztensen alacsony késésű lemeztárhelyet biztosítanak Azure Iaas virtuális gépek számára. Az ultralemezek további előnyei közé tartozik a lemez teljesítményének dinamikus módosítása a számítási feladatokmellett, anélkül, hogy újra kellene indítani a virtuális gépeket (VM). Az ultralemezek olyan adatigényes számítási feladatokhoz használhatók, mint az SAP HANA, a felső szintű adatbázisok és a tranzakcióigényes számítási feladatok. Az ultralemezek csak adatlemezként használhatók. Prémium SSD-lemezeket célszerű operációsrendszer-lemezként használnia.

### <a name="performance"></a>Teljesítmény

Amikor egy ultra lemez kiépítése, önállóan konfigurálhatja a kapacitás és a lemez teljesítményét. Az ultralemezek több rögzített méretben kaphatók, a 4 GiB-től a 64 TiB-ig, és rugalmas teljesítménykonfigurációs modellel rendelkeznek, amely lehetővé teszi az IOPS és az átviteli teljesítmény független konfigurálását.

Az ultralemezek néhány kulcsfontosságú képessége a következő:

- Lemezkapacitás: Az ultralemezek kapacitása 4 GiB és 64 TiB között mozog.
- Disk IOPS: Az Ultra-lemezek 300 IOPS/GiB IOPS-korlátot támogatnak, lemezenként legfeljebb 160 K IOPS-ig. A kiépített IOPS eléréséhez győződjön meg arról, hogy a kiválasztott disk IOPS kisebb, mint a Virtuálisgép IOPS-korlát. A minimális garantált IOPS lemezenként 2 IOPS/GiB, a teljes alapterv minimum 100 IOPS. Például ha volt egy 4 GiB ultra lemez, akkor legalább 100 IOPS, ahelyett, hogy nyolc IOPS.
- Lemezátviteli sebesség: Az ultralemezek esetében egyetlen lemez átviteli korlátja 256 KiB/s minden egyes kiosztott IOPS esetén, lemezenként legfeljebb 2000 Mb/s-ig (ahol Mb/s = 10^6 bájt másodpercenként). A minimális garantált átviteli sebesség lemezenként 4KiB/s minden egyes kiosztott IOPS, a teljes alapterv minimum 1 MBps.
- Az Ultra-lemezek támogatják a lemez teljesítményjellemzőinek (IOPS és átviteli teljesítmény) futásidőben történő módosítását anélkül, hogy le választanák a lemezt a virtuális gépről. Miután egy lemez teljesítményének átméretezési műveletét kiadták egy lemezen, a módosítás tényleges érvénybe léptetése akár egy órát is igénybe vehet. Egy 24 órás időszakban legfeljebb négy teljesítmény-átméretezési művelet lehet. A teljesítményátméretezési művelet a teljesítmény sávszélesség-kapacitásának hiánya miatt sikertelen lehet.

### <a name="disk-size"></a>Lemezméret

|Lemez méret (GiB)  |IOPS sapka  |Átviteli sebesség kap (MBps)  |
|---------|---------|---------|
|4     |1200         |300         |
|8     |2,400         |600         |
|16     |4800         |1200         |
|32     |9600         |2000         |
|64     |19,200         |2000         |
|128     |38,400         |2000         |
|256     |76 800         |2000         |
|512     |80,000         |2000         |
|1 024-65 536 (ebben a tartományban a méretek 1 TiB-es lépésekben növekednek)     |160 000         |2000         |

### <a name="ga-scope-and-limitations"></a>GA hatálya és korlátai

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](managed-disks-ultra-disks-GA-scope-and-limitations.md)]

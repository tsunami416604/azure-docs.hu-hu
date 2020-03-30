---
title: StorSimple 8000 sorozatrendszer korlátok | Microsoft dokumentumok
description: A StorSimple 8000 sorozatú összetevők és kapcsolatok rendszerkorlátaiés ajánlott méretei.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: c7392678-0924-46c6-9c59-1665cb9b6586
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/28/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 70f2d9542082ddf7ecf1d1e7361b0ecdb14c5ef8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68963375"
---
# <a name="what-are-storsimple-8000-series-system-limits"></a>Mik azok a StorSimple 8000 sorozatú rendszerkorlátok?

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Áttekintés

A StorSimple méretezhető és rugalmas tárhelyet biztosít az adatközpont számára. Vannak azonban olyan korlátok, amelyeket érdemes szem előtt tartania a StorSimple-megoldás tervezése, üzembe helyezése és működtetése közben. Az alábbi táblázat ismerteti ezeket a korlátokat, és néhány javaslatot tartalmaz, hogy a legtöbbet hozhassa ki a StorSimple-megoldásból.

| Korlátazonosító | Korlát | Megjegyzések |
| --- | --- | --- |
| Tárfiók hitelesítő adatainak maximális száma |64 | |
| A kötettárolók maximális száma |64 | |
| A kötetek maximális száma |255 | |
| Helyileg rögzített kötetek maximális száma |32 | |
| Az ütemezések maximális száma sávszélességsablononként |168 |A menetrend minden órában, minden nap a héten (24 * 7). |
| Többszintű kötet maximális mérete fizikai eszközökön |64 TB 8100 és 8600 esetén |A 8100 és a 8600 fizikai eszköz. |
| Többszintű kötet maximális mérete az Azure-beli virtuális eszközökön |30 TB 8010-hez <br></br> 64 TB 8020 esetén |A 8010-es és 8020-as verzió olyan virtuális eszközök az Azure-ban, amelyek standard szintű tárhelyet, illetve prémium szintű tárhelyet használnak. |
| A helyileg rögzített kötet maximális mérete fizikai eszközökön |8,5 TB 8100-hoz <br></br> 22,5 TB 8600 esetén |A 8100 és a 8600 fizikai eszköz. |
| Az iSCSI-kapcsolatok maximális száma |512 | |
| A kezdeményezőitől származó iSCSI-kapcsolatok maximális száma |512 | |
| A hozzáférés-vezérlési rekordok maximális száma eszközönként |64 | |
| A kötetek maximális száma biztonsági mentési házirendenként |20 | |
| Ütemezésenként megőrzött biztonsági mentések maximális száma (biztonsági mentési házirendben) |64 | |
| Az ütemezések maximális száma biztonsági mentési házirendenként |10 | |
| Kötetenként megtartható típusú pillanatképek maximális száma |256 |Ez a szám tartalmazza a helyi pillanatképek és a felhőbeli pillanatképek. |
| Bármely eszközön megtekinthető pillanatképek maximális száma |10,000 | |
| A párhuzamosan feldolgozható kötetek maximális száma biztonsági mentéshez, visszaállításhoz vagy klónozáshoz |16 |<ul><li>Ha 16-nál több kötet van, a feldolgozási tárolóhelyek elérhetővé válásakor a feldolgozási tárolóhelyek egymás után kerülnek feldolgozásra.</li><li>A klónozott vagy visszaállított rétegzett kötetek új biztonsági mentései nem történhetnek meg, amíg a művelet be nem fejeződik. Helyi kötet esetén azonban a biztonsági mentések akkor engedélyezettek, ha a kötet online állapotba kerül.</li></ul> |
| Rétegzett kötetek visszaállítása és klónozása |< 2 perc |<ul><li>A kötet a visszaállítási vagy klónozási művelettől számított 2 percen belül elérhetővé válik, a kötet méretétől függetlenül.</li><li>A kötet teljesítménye kezdetben a szokásosnál lassabb lehet, mivel a legtöbb adat és metaadat továbbra is a felhőben található. A teljesítmény növekedhet, ahogy az adatok a felhőből a StorSimple eszközre áramlanak.</li><li>A metaadatok teljes letöltési ideje a lefoglalt kötet méretétől függ. A metaadatok automatikusan a háttérben kerülnek az eszközbe, a lefoglalt kötetadatok TB-nkénti 5 perces sebességgel. Ezt a mértéket befolyásolhatja a felhőbe irányuló internetes sávszélesség.</li><li>A visszaállítási vagy klónozási művelet akkor fejeződik be, ha az összes metaadat az eszközön található.</li><li>A biztonsági mentési műveletek nem hajthatók végre, amíg a visszaállítási vagy klónozási művelet teljesen be nem fejeződik. |
| A helyileg rögzített kötetek helyreállítási idejének visszaállítása |< 2 perc |<ul><li>A kötet a visszaállítási művelettől számított 2 percen belül elérhetővé válik, a kötet méretétől függetlenül.</li><li>A kötet teljesítménye kezdetben a szokásosnál lassabb lehet, mivel a legtöbb adat és metaadat továbbra is a felhőben található. A teljesítmény növekedhet, ahogy az adatok a felhőből a StorSimple eszközre áramlanak.</li><li>A metaadatok teljes letöltési ideje a lefoglalt kötet méretétől függ. A metaadatok automatikusan a háttérben kerülnek az eszközbe, a lefoglalt kötetadatok TB-nkénti 5 perces sebességgel. Ezt a mértéket befolyásolhatja a felhőbe irányuló internetes sávszélesség.</li><li>A rétegzett kötetekkel ellentétben a helyileg rögzített kötetek esetében a kötetadatok helyileg is letöltődnek az eszközön. A visszaállítási művelet akkor fejeződik be, ha az összes kötetadat az eszközre lett hozva.</li><li>A visszaállítási műveletek hosszúak lehetnek. A visszaállítás befejezéséhez szükséges teljes idő a kiosztott helyi kötet méretétől, az internetes sávszélességtől és az eszközön meglévő adatoktól függ. A helyileg rögzített köteten a biztonsági mentési műveletek engedélyezettek, amíg a visszaállítási művelet folyamatban van. |
| A felhőbeli pillanatképek feldolgozási sebessége |15 perc/TB |<ul><li>Minimális idő, hogy a felhőbeli pillanatkép készen áll a feltöltésre, a biztonsági mentésben lefoglalt kötetadatok TB-je szerint. </li><li> A felhőbeli pillanatkép teljes idejét úgy számítja ki, hogy ezt az időt hozzáadja a pillanatkép feltöltési idejéhez, amelyet az internetes sávszélesség a felhőbe befolyásol. |
| Az ügyfél maximális olvasási/írási átviteli /maximális átviteli -átmenője (az SSD-rétegből kiszolgálva)* |920/720 MB/s egyetlen 10 GbE hálózati interfésszel |Akár 2x MPIO-val és két hálózati csatolóval. |
| Az ügyfelek maximális olvasási/írási átviteli /maximális átviteli -átmenője (a HDD-rétegből kiszolgálva)* |120/250 MB/s | |
| Az ügyfél maximális olvasási/írási átviteli /maximális átviteli -átmenője (a felhőrétegről kiszolgálva)* a 3. |40/60 MB/s rétegzett kötetek esetén<br><br>60/80 MB/s rétegzett kötetek esetén, ha a kötet létrehozása kor van kijelölve archiválási beállítás |Az olvasási átviteli kapacitás attól függ, hogy az ügyfelek megfelelő I/O-várólista-mélységet generálnak és karbantartanak. <br><br>Az elért sebesség a használt mögöttes tárfiók sebességétektől függ. |

&#42; I/O-típusonkénti maximális átviteli teljesítmény 100 százalékos olvasási és 100 százalékos írási forgatókönyvvel lett mérve. A tényleges átviteli kapcsolat alacsonyabb lehet, és az I/O-keveréstől és a hálózati feltételektől függ.

&#42;&#42; 3.

## <a name="next-steps"></a>További lépések
Tekintse át a [StorSimple rendszerkövetelményeit.](storsimple-8000-system-requirements.md)


---
title: A StorSimple 8000 series rendszer korlátok |} Microsoft Docs
description: Ismerteti a rendszer korlátozásai és a StorSimple 8000 series összetevők és kapcsolatok az ajánlott méreteket.
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
ms.openlocfilehash: cc3c0ad193af7625c8c4c1c2e82b6bdc8be33310
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
ms.locfileid: "23874849"
---
# <a name="what-are-storsimple-8000-series-system-limits"></a>A StorSimple 8000 series rendszer korlátai

## <a name="overview"></a>Áttekintés

A StorSimple méretezhető és rugalmas tárolási nyújt az adatközpontban. Van azonban néhány korlátot, érdemes figyelembe venni, tervezése, üzembe helyezéséhez és üzemeltetéséhez a a StorSimple megoldásban. A következő táblázat ismerteti ezeket a határértékeket, és néhány javaslatot is tartalmaz, hogy a legtöbbet a StorSimple megoldásban kaphat.

| Korlátazonosító | Korlát | Megjegyzések |
| --- | --- | --- |
| Tárfiók hitelesítő adatainak maximális száma |64 | |
| Kötettárolók maximális száma |64 | |
| Kötetek maximális száma |255 | |
| Helyileg rögzített kötetek maximális száma |32 | |
| Ütemezésének per sávszélességsablon maximális száma |168 |Óránként, naponta, a hét (24 * 7) ütemezését. |
| A rétegzett kötetek fizikai eszközök maximális mérete |A 8100 és 8600 64 TB |8100 és 8600 különböző fizikai eszközök. |
| A virtuális eszközök, az Azure-ban a rétegzett kötetek maximális mérete |30 TB for 8010 <br></br> 64 TB for 8020 |8010-es és a 8020-as modell olyan virtuális Azure használó eszközöket Standard és Premium tárolására kulcsattribútumokkal. |
| Fizikai eszközön helyileg rögzített kötet maximális mérete |A 8100 8.5 TB <br></br> A 8600 22.5 TB |8100 és 8600 különböző fizikai eszközök. |
| ISCSI-kapcsolatok maximális száma |512 | |
| A kezdeményezők iSCSI-kapcsolatok maximális száma |512 | |
| Hozzáférés-vezérlési rekordokat eszközönként maximális száma |64 | |
| A biztonsági mentési házirend / kötetek maximális száma |20 | |
| Biztonsági mentés ütemezése (a biztonsági mentési házirend) tárolása maximális száma |64 | |
| Egy biztonsági mentési házirend ütemezések maximális száma |10 | |
| Bármilyen típusú, amelynek meg kell őrizni kötetenként pillanatképek maximális száma |256 |Ez a szám tartalmazza helyi pillanatképeket és felhőbeli pillanatképeket. |
| Lehet, hogy bármely eszköz megtalálható a pillanatképek maximális száma |10,000 | |
| A biztonsági másolat, visszaállítási párhuzamosan is, vagy a klónozáshoz kötetek maximális számát |16 |<ul><li>Ha több mint 16 kötetek, ezért a feldolgozásuk egymás után amint elérhetővé válnak a feldolgozási tárolóhelye.</li><li>Új biztonsági másolatot egy klónozott vagy visszaállított rétegzett kötet nem kerülhet sor, amíg a művelet befejeződött. Egy helyi kötet, azonban biztonsági mentések engedélyezettek után a kötet online állapotban.</li></ul> |
| Visszaállítás és helyreállítás ideje rétegzett kötetek klónozása |< 2 perc |<ul><li>A kötet szeretné elérhetővé tenni a restore vagy a Klónozási művelet, függetlenül a kötet mérete 2 percen belül.</li><li>Lehet, hogy a kötet teljesítmény kezdetben lassabb, mint a szokásos módon legtöbb adatok és metaadatok továbbra is a felhőben található. Teljesítmény, a StorSimple eszközön a felhőből adatfolyamok növelheti.</li><li>Metaadatok letöltése teljes idő függ a lefoglalt kötet mérete. Az eszköz 5 perc TB-nyi lefoglalt kötet adatait a díj a háttérben automatikusan megadhatjuk a metaadatok. Ez érinthet internetes sávszélességet a felhőbe.</li><li>A visszaállítás vagy a Klónozás befejeződött, ha a metaadatokat az eszközön.</li><li>Biztonsági mentési művelet nem hajtható végre, amíg a visszaállítás vagy a Klónozás teljesen befejeződött. |
| Állítsa vissza a helyileg rögzített kötetek helyreállítása ideje |< 2 perc |<ul><li>A köteten a visszaállítási művelet, függetlenül a kötet mérete 2 percen belül legyen elérhető.</li><li>Lehet, hogy a kötet teljesítmény kezdetben lassabb, mint a szokásos módon legtöbb adatok és metaadatok továbbra is a felhőben található. Teljesítmény, a StorSimple eszközön a felhőből adatfolyamok növelheti.</li><li>Metaadatok letöltése teljes idő függ a lefoglalt kötet mérete. Az eszköz 5 perc TB-nyi lefoglalt kötet adatait a díj a háttérben automatikusan megadhatjuk a metaadatok. Ez érinthet internetes sávszélességet a felhőbe.</li><li>Ellentétben a rétegzett kötetek, a helyileg rögzített kötetekhez a kötet adatait is tölt le helyileg az eszközön. A visszaállítási művelet befejeződött, ha a kötet adatait az eszköz állapotba nem kerül.</li><li>A visszaállítási műveletek hosszú lehet. A helyreállítás befejezéséhez teljes idő függ a kiépített helyi kötet, az internetes sávszélességet, és az eszközön a meglévő adatok méretét. A helyileg rögzített kötetet a biztonsági mentési műveletek engedélyezettek, amíg a visszaállítási művelet van folyamatban. |
| A felhőalapú pillanatfelvételek feldolgozási sebessége |15 perc/TB |<ul><li>Ellenőrizze a felhő minimálisan mennyi ideig pillanatkép-feltöltésének TB lefoglalt kötet adatok biztonsági mentése / kész. </li><li> Teljes felhőalapú pillanatfelvétel idő kiszámítása a időtartamát hozzáadja a pillanatkép ideje, amely hatással van az internetes sávszélességet felhőbe. |
| Maximális ügyfél olvasási/írási teljesítmény (ha az SSD-réteghez a kiszolgált) * |920/720 MB/s az egyetlen 10 GbE hálózati adapter |Az MPIO legfeljebb 2 x és két hálózati adapterrel. |
| Maximális ügyfél olvasási/írási teljesítmény (Ha a HDD-réteget a kiszolgált) * |120/250 MB/s | |
| Maximális ügyfél olvasási/írási teljesítmény (amikor a felhő rétegtől kiszolgált) * Update 3 és újabb ** |40/60 MB/s, rétegzett kötet<br><br>60/80 MB/s, rétegzett kötet archiválási jelölőnégyzetet a kötet létrehozása során |Olvasás átviteli ügyfelek létrehozása és fenntartása elegendő i/o-várólistamélység függ. <br><br>Sebesség érhető el az alapul szolgáló használt tárfiók sebességétől függ. |

&#42;I/o-típusonkénti maximális átviteli sebesség és 100 százalék olvasási 100 százalék írási forgatókönyvek mérték. Tényleges átviteli alacsonyabb lehet, és attól függ, i/o kombinálhatók és a hálózati feltételek.

&#42;&#42;3. frissítés előtt számok alacsonyabb lehet.

## <a name="next-steps"></a>További lépések
Tekintse át a [StorSimple rendszerkövetelmények](storsimple-8000-system-requirements.md).


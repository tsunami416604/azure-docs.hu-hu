---
title: A StorSimple 8000 sorozat rendszer korlátok |} A Microsoft Docs
description: Ismerteti a rendszer korlátok és az ajánlott méreteket a StorSimple 8000 sorozat összetevők és -kapcsolatokhoz.
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
ms.openlocfilehash: a0053f950b36351b06d08630cbf9977f53f2ed47
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64721694"
---
# <a name="what-are-storsimple-8000-series-system-limits"></a>Mik a StorSimple 8000 sorozat rendszer korlátai?

## <a name="overview"></a>Áttekintés

A StorSimple méretezhető és rugalmas tárolási nyújt az adatközpontban. Azonban bizonyos korlátozások érvényesek, amelyek akkor érdemes szem előtt tartani, tervezése, telepítése és üzemeltetése a StorSimple-megoldásokra. Az alábbi táblázat ismerteti ezeket a korlátokat, és javaslatokat is tartalmaz, hogy kihasználhassa a StorSimple-megoldásokra is kap.

| Korlátazonosító | Korlát | Megjegyzések |
| --- | --- | --- |
| Tárfiók hitelesítő adatainak maximális száma |64 | |
| Kötettárolók maximális száma |64 | |
| Kötetek maximális száma |255 | |
| A gyors helyi kötetek maximális száma |32 | |
| Az ütemezések sávszélességsablon / maximális száma |168 |Óránként, naponta, a hét (24 * 7) ütemezését. |
| A fizikai eszközökön a rétegzett kötetek maximális mérete |A 8100-as és 8600-as 64 TB |8100-as és 8600-as olyan fizikai eszközöket. |
| Az Azure-beli virtuális eszközökön a rétegzett kötetek maximális mérete |30 TB for 8010 <br></br> A 8020-64 TB |8010 és 8020 a virtuális eszközök az Azure-ban, illetve használó Standard Storage és a Premium Storage. |
| A fizikai eszközökön helyileg rögzített kötetet maximális mérete |A 8100-as 8,5 TB <br></br> A 8600-as 22,5 TB |8100-as és 8600-as olyan fizikai eszközöket. |
| ISCSI-kapcsolatok maximális száma |512 | |
| Az iSCSI kezdeményezők kapcsolatok maximális száma |512 | |
| Eszközönként hozzáférés-vezérlési rekordok maximális száma |64 | |
| Legfeljebb ennyi kötet biztonsági mentési házirend szerint |20 | |
| (A biztonsági mentési szabályzat) ütemezés szerint megőrzött biztonsági mentések maximális száma |64 | |
| Biztonsági mentési házirend szerint ütemezések maximális száma |10 | |
| A megtartható kötetenként bármilyen típusú pillanatképek maximális száma |256 |Ez a szám magában a helyi pillanatképeket és felhőbeli pillanatképeket. |
| Bármilyen eszközön használható a pillanatképek maximális száma |10,000 | |
| Biztonsági mentési és visszaállítási esetében a párhuzamosan feldolgozható, vagy a klónozáshoz kötetek maximális számát |16 |<ul><li>Ha több mint 16 köteteket, azok feldolgozása sorrendben történik amint elérhetővé válnak a feldolgozási tárolóhelyek.</li><li>Új biztonsági másolatot egy klónozott vagy visszaállított a rétegzett kötetek nem fordulhat elő, amíg a művelet be nem fejeződik. Egy helyi kötet, azonban biztonsági mentések engedélyezettek után a kötet online állapotban.</li></ul> |
| Visszaállítás és helyreállítás ideje a rétegzett kötetek klónozása |< 2 perc |<ul><li>A kötet visszaállítási vagy -klónozási művelet, függetlenül a kötet mérete 2 percen belül legyen elérhető.</li><li>A kötet teljesítmény kezdetben lehet lassabb, mint a szokásos módon az adatokat és a metaadatokat a legtöbb továbbra is a felhőben található. Teljesítmény, a StorSimple-eszközre a felhőből adatfolyamok növelhető.</li><li>Metaadatok letöltése teljes ideje a kiosztott kötet méretétől függ. A háttérben a díj 5 perc TB-nyi lefoglalt kötet adatait az eszköz automatikusan megadhatjuk a metaadatokat. Ez a díjszabás hatással lehet a felhőbe internetes sávszélességet.</li><li>A visszaállítás vagy a Klónozás befejeződött, az eszközön lévő összes metaadatot esetén.</li><li>Biztonsági mentési művelet nem hajtható végre, amíg a visszaállítás vagy Klónozás teljesen kész. |
| Helyreállítás ideje a gyors helyi kötetek visszaállítása |< 2 perc |<ul><li>A köteten a visszaállítási művelet, függetlenül a kötet mérete 2 percen belül legyen elérhető.</li><li>A kötet teljesítmény kezdetben lehet lassabb, mint a szokásos módon az adatokat és a metaadatokat a legtöbb továbbra is a felhőben található. Teljesítmény, a StorSimple-eszközre a felhőből adatfolyamok növelhető.</li><li>Metaadatok letöltése teljes ideje a kiosztott kötet méretétől függ. A háttérben a díj 5 perc TB-nyi lefoglalt kötet adatait az eszköz automatikusan megadhatjuk a metaadatokat. Ez a díjszabás hatással lehet a felhőbe internetes sávszélességet.</li><li>Ellentétben a rétegzett kötetek, a helyileg rögzített kötetekhez a kötet adatait is tölt le helyileg az eszközön. A visszaállítási művelet befejeződött, ha a kötet adatait a rendszer visszaállította az eszközön.</li><li>A visszaállítási műveleteket hosszú lehet. A visszaállítás elvégzéséhez szükséges teljes idő függ az üzembe helyezett helyi kötet, az internetes sávszélességet és az eszközön a meglévő adatok méretét. A helyileg rögzített kötetet a biztonsági mentési műveletek engedélyezettek, amíg a visszaállítási művelet folyamatban van. |
| A felhőbeli pillanatképekkel feldolgozási sebessége |15 perc/TB |<ul><li>Minimális idő a felhő a pillanatkép-feltöltéshez, biztonsági mentéshez lefoglalt adatmennyiség TB / kész. </li><li> Ezúttal a pillanatkép feltöltési időpontot, amely hatással van az internetes sávszélesség a felhőbe való hozzáadásával teljes felhőbeli pillanatkép idő kiszámítása. |
| Maximális írási/olvasási átviteli sebesség (ha az SSD-réteg által szolgáltatott) * |920/720 MB/s az egyetlen 10 GbE hálózati adapter |Legfeljebb 2 x az MPIO-t, és két hálózati adaptert. |
| Maximális írási/olvasási átviteli sebesség (Ha a HDD-réteg által szolgáltatott) * |120/250 MB/s | |
| Maximális írási/olvasási átviteli sebesség (Ha a felhő szintjén szolgáltatja) * Update 3 és újabb ** |40/60 MB/s a rétegzett kötet<br><br>60/80 MB/s a rétegzett köteteket a kötet létrehozása során a archiválási jelölőnégyzetet |Olvasás átviteli ügyfelek létrehozása és karbantartása elegendő i/o-várólistamélységének függ. <br><br>A sebesség érhető el az alapul szolgáló használt tárfiók sebességétől függ. |

&#42;Maximális átviteli sebesség i/o-típusonként 100 %-os olvasási és írási 100 %-os forgatókönyvek mérték. Tényleges átviteli sebesség lehet alacsonyabb, és függ az i/o használhatók vegyesen és hálózati feltételek.

&#42;&#42;3. frissítés előtti teljesítményszámokhoz alacsonyabb lehet.

## <a name="next-steps"></a>További lépések
Tekintse át a [StorSimple követelményei](storsimple-8000-system-requirements.md).


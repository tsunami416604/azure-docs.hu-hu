---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 93f4f74d435cc14130668da102d1246c5fad5872
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67179517"
---
| Korlátazonosító | Korlát | Megjegyzések |
| --- | --- | --- |
| Tárfiók hitelesítő adatainak maximális száma |64 | |
| A kötettárolók maximális száma |64 | |
| A kötetek maximális száma |255 | |
| Az ütemezések maximális száma sávszélességsablononként |168 |Menetrend minden órára, a hét minden napjára. |
| Többszintű kötet maximális mérete fizikai eszközökön |64 TB StorSimple 8100 és StorSimple 8600 esetén |A StorSimple 8100 és a StorSimple 8600 fizikai eszközök. |
| Többszintű kötet maximális mérete az Azure-beli virtuális eszközökön |30 TB StorSimple 8010 esetén <br></br> 64 TB StorSimple 8020 esetén |A StorSimple 8010 és a StorSimple 8020 olyan virtuális eszközök az Azure-ban, amelyek standard szintű tárhelyet és prémium szintű tárhelyet használnak. |
| A helyileg rögzített kötet maximális mérete fizikai eszközökön |9 TB StorSimple 8100 esetén <br></br> 24 TB StorSimple 8600 esetén |A StorSimple 8100 és a StorSimple 8600 fizikai eszközök. |
| Az iSCSI-kapcsolatok maximális száma |512 | |
| A kezdeményezőitől származó iSCSI-kapcsolatok maximális száma |512 | |
| A hozzáférés-vezérlési rekordok maximális száma eszközönként |64 | |
| A kötetek maximális száma biztonsági mentési házirendenként |24 | |
| Biztonsági mentési házirendenként megőrzött biztonsági mentések maximális száma |64 | |
| Az ütemezések maximális száma biztonsági mentési házirendenként |10 | |
| Kötetenként megtartható típusú pillanatképek maximális száma |256 |Ez az összeg tartalmazza a helyi pillanatképek et és a felhőbeli pillanatképeket. |
| Bármely eszközön megtekinthető pillanatképek maximális száma |10,000 | |
| A párhuzamosan feldolgozható kötetek maximális száma biztonsági mentéshez, visszaállításhoz vagy klónozáshoz |16 |<ul><li>Ha 16-nál több kötet van, a feldolgozási tárolóhelyek elérhetővé válása kortikál.</li><li>A klónozott vagy visszaállított rétegzett kötetek új biztonsági mentései nem fordulhatnak elő a művelet befejezéséig. Helyi kötet esetén a biztonsági mentések akkor engedélyezettek, ha a kötet online állapotba kerül.</li></ul> |
| Rétegzett kötetek visszaállítása és klónozása |<2 perc |<ul><li>A kötet a visszaállítási vagy klónozási művelettől számított 2 percen belül elérhetővé válik, a kötet méretétől függetlenül.</li><li>A kötet teljesítménye kezdetben lehet lassabb, mint a normál, mint a legtöbb adat és metaadat továbbra is a felhőben található. A teljesítmény növekedhet, ahogy az adatok a felhőből a StorSimple eszközre áramlanak.</li><li>A metaadatok teljes letöltési ideje a lefoglalt kötet méretétől függ. A metaadatok automatikusan a háttérben kerülnek az eszközbe, a lefoglalt kötetadatok TB-nkénti 5 perces sebességgel. Ezt a mértéket befolyásolhatja a felhőbe irányuló internetes sávszélesség.</li><li>A visszaállítási vagy klónozási művelet akkor fejeződik be, ha az összes metaadat az eszközön található.</li><li>Biztonsági mentési műveletek nem hajthatók végre, amíg a visszaállítási vagy klónozási művelet teljesen be nem fejeződött. |
| A helyileg rögzített kötetek helyreállítási idejének visszaállítása |<2 perc |<ul><li>A kötet a visszaállítási művelettől számított 2 percen belül elérhetővé válik, a kötet méretétől függetlenül.</li><li>A kötet teljesítménye kezdetben lehet lassabb, mint a normál, mint a legtöbb adat és metaadat továbbra is a felhőben található. A teljesítmény növekedhet, ahogy az adatok a felhőből a StorSimple eszközre áramlanak.</li><li>A metaadatok teljes letöltési ideje a lefoglalt kötet méretétől függ. A metaadatok automatikusan a háttérben kerülnek az eszközbe, a lefoglalt kötetadatok TB-nkénti 5 perces sebességgel. Ezt a mértéket befolyásolhatja a felhőbe irányuló internetes sávszélesség.</li><li>A rétegzett kötetekkel ellentétben, ha vannak helyileg rögzített kötetek, a kötetadatok is helyileg töltődnek le az eszközön. A visszaállítási művelet akkor fejeződik be, ha az összes kötetadat az eszközre lett hozva.</li><li>A visszaállítási műveletek hosszúak lehetnek, és a visszaállítás teljes ideje a kiosztott helyi kötet méretétől, az internetes sávszélességtől és az eszközön meglévő adatoktól függ. A helyileg rögzített köteten a biztonsági mentési műveletek engedélyezettek, amíg a visszaállítási művelet folyamatban van. |
| Vékony-visszaállítás elérhetősége |Utolsó feladatátvétel | |
| Az ügyfél maximális olvasási/írási átviteli/olvasási átviteli szintje az SSD-rétegből kiszolgálva* |920/720 MB/mp egyetlen 10 gigabites Ethernet hálózati csatlakozóval |Akár két alkalommal több mint pio és két hálózati interfész. |
| Az ügyfelek maximális olvasási/írási átviteli/maximális átviteli szintje a HDD-rétegből kiszolgálva* |120/250 MB/mp | |
| Az ügyfelek maximális olvasási/írási átviteli áteresztőjoga, ha a felhőrétegről szolgálják ki* |11/41 MB/mp |Az olvasási átviteli kapacitás attól függ, hogy az ügyfelek megfelelő I/O-várólista-mélységet generálnak és karbantartanak. |

&#42;I/O-típusonkénti maximális átviteli teljesítmény 100 százalékos olvasási és 100 százalékos írási forgatókönyvvel lett mérve. A tényleges átviteli kapcsolat alacsonyabb lehet, és az I/O-keveréstől és a hálózati feltételektől függ.


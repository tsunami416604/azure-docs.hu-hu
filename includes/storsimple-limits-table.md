---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 93f4f74d435cc14130668da102d1246c5fad5872
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553513"
---
| Korlátazonosító | Korlát | Megjegyzések |
| --- | --- | --- |
| Tárfiók hitelesítő adatainak maximális száma |64 | |
| Kötettárolók maximális száma |64 | |
| Kötetek maximális száma |255 | |
| Az ütemezések sávszélességsablon / maximális száma |168 |Minden órában, a hét minden napján ütemezése. |
| A fizikai eszközökön a rétegzett kötetek maximális mérete |A StorSimple 8100 és StorSimple 8600-as 64 TB |A StorSimple 8100 és StorSimple 8600 olyan fizikai eszközöket. |
| Az Azure-beli virtuális eszközökön a rétegzett kötetek maximális mérete |A storsimple 8010-es 30 TB <br></br> A storsimple 8020-as 64 TB |StorSimple 8010-es és a StorSimple 8020-as a virtuális eszközök az Azure-ban, amely a Standard storage és a prémium szintű storage, illetve használja. |
| A fizikai eszközökön helyileg rögzített kötetet maximális mérete |A storsimple 8100-as 9 TB <br></br> A storsimple 8600-as 24 TB |A StorSimple 8100 és StorSimple 8600 olyan fizikai eszközöket. |
| ISCSI-kapcsolatok maximális száma |512 | |
| Az iSCSI kezdeményezők kapcsolatok maximális száma |512 | |
| Eszközönként hozzáférés-vezérlési rekordok maximális száma |64 | |
| Legfeljebb ennyi kötet biztonsági mentési házirend szerint |24 | |
| Megőrzött biztonsági mentési házirend szerint biztonsági mentések maximális száma |64 | |
| Biztonsági mentési házirend szerint ütemezések maximális száma |10 | |
| A megtartható kötetenként bármilyen típusú pillanatképek maximális száma |256 |Ez a mennyiség magában foglalja a helyi pillanatképeket és felhőbeli pillanatképeket. |
| Bármilyen eszközön használható a pillanatképek maximális száma |10,000 | |
| Biztonsági mentési és visszaállítási esetében a párhuzamosan feldolgozható, vagy a klónozáshoz kötetek maximális számát |16 |<ul><li>Ha több mint 16 köteteket, azok van feldolgozása sorrendben történik amint elérhetővé válnak a feldolgozási tárolóhelyek.</li><li>Új biztonsági másolatot egy klónozott vagy visszaállított a rétegzett kötetek nem fordulhat elő, amíg a művelet be nem fejeződik. Egy helyi kötet a biztonsági mentések engedélyezettek után a kötet online állapotban.</li></ul> |
| Visszaállítás és helyreállítás ideje a rétegzett kötetek klónozása |< 2 perc |<ul><li>A köteten a visszaállítási vagy -klónozási a műveletet, függetlenül a kötet mérete 2 percen belül legyen elérhető.</li><li>Előfordulhat, hogy a kötet teljesítmény kezdetben lassabb, mint a szokásos módon az adatokat és a metaadatokat a legtöbb továbbra is a felhőben található. A StorSimple-eszközre a felhőből adatfolyam-gyűjteményre, növelheti a teljesítmény.</li><li>Metaadatok letöltése teljes ideje a kiosztott kötet méretétől függ. A háttérben a díj 5 perc TB-nyi lefoglalt kötet adatait az eszköz automatikusan megadhatjuk a metaadatokat. Ez a díjszabás a felhőbe internetes sávszélesség hatással lehetnek.</li><li>A visszaállítás vagy a Klónozás befejeződött, az eszközön lévő összes metaadatot esetén.</li><li>Biztonsági mentési művelet nem hajtható végre, amíg a visszaállítás vagy Klónozás teljesen kész. |
| Helyreállítás ideje a gyors helyi kötetek visszaállítása |< 2 perc |<ul><li>A köteten a visszaállítási művelet, függetlenül a kötet mérete 2 percen belül legyen elérhető.</li><li>Előfordulhat, hogy a kötet teljesítmény kezdetben lassabb, mint a szokásos módon az adatokat és a metaadatokat a legtöbb továbbra is a felhőben található. A StorSimple-eszközre a felhőből adatfolyam-gyűjteményre, növelheti a teljesítmény.</li><li>Metaadatok letöltése teljes ideje a kiosztott kötet méretétől függ. A háttérben a díj 5 perc TB-nyi lefoglalt kötet adatait az eszköz automatikusan megadhatjuk a metaadatokat. Ez a díjszabás a felhőbe internetes sávszélesség hatással lehetnek.</li><li>Ellentétben a rétegzett kötetek ha vannak a helyileg rögzített kötetekről, a kötet adatait is tölt le helyileg az eszközön. A visszaállítási művelet befejeződött, ha a kötet adatait a rendszer visszaállította az eszközön.</li><li>A visszaállítási műveleteket hosszú lehet, és a visszaállítás elvégzéséhez szükséges teljes idő függ az üzembe helyezett helyi kötet, az internetes sávszélességet és az eszközön a meglévő adatok méretét. A helyileg rögzített kötetet a biztonsági mentési műveletek engedélyezettek, amíg a visszaállítási művelet folyamatban van. |
| Rendelkezésre állási dinamikusan-visszaállítás |Legutóbbi feladatátvétel | |
| Maximális írási/olvasási átviteli sebességet, amikor szolgáltatja az SSD-réteg * |Az egyetlen 10 gigabites Ethernet hálózati adapter 920/720 MB/mp |Legfeljebb két alkalommal az MPIO-t, és két hálózati adaptert. |
| Maximális írási/olvasási átviteli sebességet, amikor szolgáltatja a HDD-réteg * |120/250 MB/mp | |
| Maximális írási/olvasási átviteli sebességet, amikor szolgáltatja a felhő csomag * |11/41 MB/mp |Olvasás átviteli ügyfelek létrehozása és karbantartása elegendő i/o-várólistamélységének függ. |

&#42;Maximális átviteli sebesség i/o-típusonként 100 %-os olvasási és írási 100 %-os forgatókönyvek mérték. Tényleges átviteli sebesség lehet alacsonyabb, és attól függ, i/o használhatók vegyesen és hálózati feltételek.


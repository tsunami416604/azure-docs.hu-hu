<!--author=alkohli last changed: 12/15/15-->

| Korlátazonosító | Korlát | Megjegyzések |
| --- | --- | --- |
| Tárfiók hitelesítő adatainak maximális száma |64 | |
| Kötettárolók maximális száma |64 | |
| Kötetek maximális száma |255 | |
| Az ütemezések sávszélességsablon / maximális száma |168 |Óránként, naponta, a hét (24 * 7) ütemezését. |
| A fizikai eszközökön a rétegzett kötetek maximális mérete |A 8100-as és 8600-as 64 TB |8100-as és 8600-as olyan fizikai eszközöket. |
| Az Azure-beli virtuális eszközökön a rétegzett kötetek maximális mérete |30 TB for 8010 <br></br> A 8020-64 TB |8010 és 8020 a virtuális eszközök az Azure-ban, illetve használó Standard Storage és a Premium Storage. |
| A fizikai eszközökön helyileg rögzített kötetet maximális mérete |A 8100-as 9 TB <br></br> A 8600-as 24 TB |8100-as és 8600-as olyan fizikai eszközöket. |
| ISCSI-kapcsolatok maximális száma |512 | |
| Az iSCSI kezdeményezők kapcsolatok maximális száma |512 | |
| Eszközönként hozzáférés-vezérlési rekordok maximális száma |64 | |
| Legfeljebb ennyi kötet biztonsági mentési házirend szerint |24 | |
| Megőrzött biztonsági mentési házirend szerint biztonsági mentések maximális száma |64 | |
| Biztonsági mentési házirend szerint ütemezések maximális száma |10 | |
| A megtartható kötetenként bármilyen típusú pillanatképek maximális száma |256 |Ez magában foglalja a helyi pillanatképeket és felhőbeli pillanatképeket. |
| Bármilyen eszközön használható a pillanatképek maximális száma |10,000 | |
| Biztonsági mentési és visszaállítási esetében a párhuzamosan feldolgozható, vagy a klónozáshoz kötetek maximális számát |16 |<ul><li>Ha több mint 16 köteteket, azok szerint lesz feldolgozva egymás után feldolgozási tárhelyek elérhetővé válnak.</li><li>Új biztonsági másolatot egy klónozott vagy visszaállított a rétegzett kötetek nem fordulhat elő, amíg a művelet be nem fejeződik. Egy helyi kötet, azonban biztonsági mentések engedélyezettek után a kötet online állapotban.</li></ul> |
| Visszaállítás és helyreállítás ideje a rétegzett kötetek klónozása |< 2 perc |<ul><li>A kötet visszaállítási vagy -klónozási művelet, függetlenül a kötet mérete 2 percen belül legyen elérhető.</li><li>A kötet teljesítmény kezdetben lehet lassabb, mint a szokásos módon az adatokat és a metaadatokat a legtöbb továbbra is a felhőben található. Teljesítmény, a StorSimple-eszközre a felhőből adatfolyamok növelhető.</li><li>Metaadatok letöltése teljes ideje a kiosztott kötet méretétől függ. A háttérben a díj 5 perc TB-nyi lefoglalt kötet adatait az eszköz automatikusan megadhatjuk a metaadatokat. Ez a díjszabás hatással lehet a felhőbe internetes sávszélességet.</li><li>A visszaállítás vagy a Klónozás befejeződött, az eszközön lévő összes metaadatot esetén.</li><li>Biztonsági mentési művelet nem hajtható végre, amíg a visszaállítás vagy Klónozás teljesen kész. |
| Helyreállítás ideje a gyors helyi kötetek visszaállítása |< 2 perc |<ul><li>A köteten a visszaállítási művelet, függetlenül a kötet mérete 2 percen belül legyen elérhető.</li><li>A kötet teljesítmény kezdetben lehet lassabb, mint a szokásos módon az adatokat és a metaadatokat a legtöbb továbbra is a felhőben található. Teljesítmény, a StorSimple-eszközre a felhőből adatfolyamok növelhető.</li><li>Metaadatok letöltése teljes ideje a kiosztott kötet méretétől függ. A háttérben a díj 5 perc TB-nyi lefoglalt kötet adatait az eszköz automatikusan megadhatjuk a metaadatokat. Ez a díjszabás hatással lehet a felhőbe internetes sávszélességet.</li><li>Ellentétben a rétegzett kötetek esetén a gyors helyi kötetek a kötet adatait is tölt le helyileg az eszközön. A visszaállítási művelet befejeződött, ha a kötet adatait a rendszer visszaállította az eszközön.</li><li>Lehet, hogy a visszaállítási műveleteket hosszú, és a visszaállítás elvégzéséhez szükséges teljes idő függ az üzembe helyezett helyi kötet, az internetes sávszélességet és az eszközön a meglévő adatok méretét. A helyileg rögzített kötetet a biztonsági mentési műveletek engedélyezettek, amíg a visszaállítási művelet folyamatban van. |
| Rendelkezésre állási dinamikusan-visszaállítás |Legutóbbi feladatátvétel | |
| Maximális írási/olvasási átviteli sebesség (ha az SSD-réteg által szolgáltatott) * |920/720 MB/s és a egy egyetlen 10 gbe hálózati adapter |Legfeljebb 2 x az MPIO-t, és két hálózati adaptert. |
| Maximális írási/olvasási átviteli sebesség (Ha a HDD-réteg által szolgáltatott) * |120/250 MB/s | |
| Maximális írási/olvasási átviteli sebesség (Ha a felhő szintjén szolgáltatja) * |11/41 MB/s |Olvasás átviteli ügyfelek létrehozása és karbantartása elegendő i/o-várólistamélységének függ. |

&#42;Maximális átviteli sebesség i/o-típusonként 100 %-os olvasási és írási 100 %-os forgatókönyvek mérték. Tényleges átviteli sebesség lehet alacsonyabb, és függ az i/o használhatók vegyesen és hálózati feltételek.


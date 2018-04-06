<!--author=alkohli last changed: 12/15/15-->

| Korlátazonosító | Korlát | Megjegyzések |
| --- | --- | --- |
| Tárfiók hitelesítő adatainak maximális száma |64 | |
| Kötettárolók maximális száma |64 | |
| Kötetek maximális száma |255 | |
| Ütemezésének per sávszélességsablon maximális száma |168 |Óránként, naponta, a hét (24 * 7) ütemezését. |
| A rétegzett kötetek fizikai eszközök maximális mérete |A 8100 és 8600 64 TB |8100 és 8600 különböző fizikai eszközök. |
| A virtuális eszközök, az Azure-ban a rétegzett kötetek maximális mérete |30 TB for 8010 <br></br> 64 TB for 8020 |8010-es és a 8020-as modell olyan virtuális Azure használó eszközöket Standard és Premium tárolására kulcsattribútumokkal. |
| Fizikai eszközön helyileg rögzített kötet maximális mérete |A 8100 9 TB <br></br> 24 TB for 8600 |8100 és 8600 különböző fizikai eszközök. |
| ISCSI-kapcsolatok maximális száma |512 | |
| A kezdeményezők iSCSI-kapcsolatok maximális száma |512 | |
| Hozzáférés-vezérlési rekordokat eszközönként maximális száma |64 | |
| A biztonsági mentési házirend / kötetek maximális száma |24 | |
| A biztonsági mentési házirend tárolása biztonsági másolatok maximális száma |64 | |
| Egy biztonsági mentési házirend ütemezések maximális száma |10 | |
| Bármilyen típusú, amelynek meg kell őrizni kötetenként pillanatképek maximális száma |256 |Ez magában foglalja a helyi pillanatképeket és felhőbeli pillanatképeket. |
| Lehet, hogy bármely eszköz megtalálható a pillanatképek maximális száma |10,000 | |
| A biztonsági másolat, visszaállítási párhuzamosan is, vagy a klónozáshoz kötetek maximális számát |16 |<ul><li>Ha több mint 16 köteteket, azok dolgoz fel egymás után amint elérhetővé válnak a feldolgozási tárolóhelye.</li><li>Új biztonsági másolatot egy klónozott vagy visszaállított rétegzett kötet nem kerülhet sor, amíg a művelet befejeződött. Egy helyi kötet, azonban biztonsági mentések engedélyezettek után a kötet online állapotban.</li></ul> |
| Visszaállítás és helyreállítás ideje rétegzett kötetek klónozása |< 2 perc |<ul><li>A kötet szeretné elérhetővé tenni a restore vagy a Klónozási művelet, függetlenül a kötet mérete 2 percen belül.</li><li>Lehet, hogy a kötet teljesítmény kezdetben lassabb, mint a szokásos módon legtöbb adatok és metaadatok továbbra is a felhőben található. Teljesítmény, a StorSimple eszközön a felhőből adatfolyamok növelheti.</li><li>Metaadatok letöltése teljes idő függ a lefoglalt kötet mérete. Az eszköz 5 perc TB-nyi lefoglalt kötet adatait a díj a háttérben automatikusan megadhatjuk a metaadatok. Ez érinthet internetes sávszélességet a felhőbe.</li><li>A visszaállítás vagy a Klónozás befejeződött, ha a metaadatokat az eszközön.</li><li>Biztonsági mentési művelet nem hajtható végre, amíg a visszaállítás vagy a Klónozás teljesen befejeződött. |
| Állítsa vissza a helyileg rögzített kötetek helyreállítása ideje |< 2 perc |<ul><li>A köteten a visszaállítási művelet, függetlenül a kötet mérete 2 percen belül legyen elérhető.</li><li>Lehet, hogy a kötet teljesítmény kezdetben lassabb, mint a szokásos módon legtöbb adatok és metaadatok továbbra is a felhőben található. Teljesítmény, a StorSimple eszközön a felhőből adatfolyamok növelheti.</li><li>Metaadatok letöltése teljes idő függ a lefoglalt kötet mérete. Az eszköz 5 perc TB-nyi lefoglalt kötet adatait a díj a háttérben automatikusan megadhatjuk a metaadatok. Ez érinthet internetes sávszélességet a felhőbe.</li><li>Ellentétben a rétegzett kötetek, helyileg rögzített kötetek esetében a kötet adatait is tölt le helyileg az eszközön. A visszaállítási művelet befejeződött, ha a kötet adatait az eszköz állapotba nem kerül.</li><li>Lehet, hogy a visszaállítási műveletek hosszú, és a helyreállítás befejezéséhez teljes idő függ a kiépített helyi kötet, az internetes sávszélességet, és az eszközön a meglévő adatok méretét. A helyileg rögzített kötetet a biztonsági mentési műveletek engedélyezettek, amíg a visszaállítási művelet van folyamatban. |
| Vékony visszaállítási rendelkezésre állása |Legutóbbi feladatátvétele | |
| Maximális ügyfél olvasási/írási teljesítmény (ha az SSD-réteghez a kiszolgált) * |920/720 MB/s a egy egyetlen 10 gbe hálózati adapter |Az MPIO legfeljebb 2 x és két hálózati adapterrel. |
| Maximális ügyfél olvasási/írási teljesítmény (Ha a HDD-réteget a kiszolgált) * |120/250 MB/s | |
| Maximális ügyfél olvasási/írási teljesítmény (amikor a felhő rétegtől kiszolgált) * |11/41 MB/s |Olvasás átviteli ügyfelek létrehozása és fenntartása elegendő i/o-várólistamélység függ. |

&#42;I/o-típusonkénti maximális átviteli sebesség és 100 százalék olvasási 100 százalék írási forgatókönyvek mérték. Tényleges átviteli alacsonyabb lehet, és attól függ, i/o kombinálhatók és a hálózati feltételek.


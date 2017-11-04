

| **Korlát azonosítója** | **Korlát** | **Megjegyzések** |
| --- | --- | --- |
| Teljes kapacitás (például felhő) |Akár 64 TB-os virtuális eszközönként |Feladatokat átveheti a teljes StorSimple virtuális tömb egy másik üres tömb. Ha megpróbál visszaállítani ugyanarra az eszközre, győződjön meg arról, hogy rendelkezik-e elegendő lemezterület a művelet végrehajtásához az eszközön. Miután túllépte 32 TB, nem állítható vissza ugyanarra az eszközre. |
| Tárfiók hitelesítő adatainak eszközönként maximális száma |1 | |
| Kötetek vagy megosztások maximális száma |16 | |
| A rétegzett megosztás minimális mérete |500 GB | |
| A rétegzett kötetek minimális mérete |500 GB | |
| A rétegzett megosztás maximális mérete |20 TB | |
| A rétegzett kötetek maximális mérete |5 TB | |
| Egy helyileg rögzített megosztás minimális mérete |50 GB | |
| Egy helyileg rögzített kötet minimális mérete |50 GB | |
| Egy helyileg rögzített megosztás maximális mérete |2 TB | |
| Egy helyileg rögzített kötet maximális mérete |200 GB | |
| A kezdeményezők iSCSI-kapcsolatok maximális száma |512 | |
| Hozzáférés-vezérlési rekordokat eszközönként maximális száma |64 | |
| Megőrzi a virtuális eszköz a biztonsági másolatok maximális száma *.backups* fájlkiszolgáló mappa |5 |Ez magában foglalja a legújabb (az alapértelmezett biztonsági mentési házirend által generált) ütemezett és manuális biztonsági mentés. |
| Ütemezett biztonsági mentések őrződnek meg az eszköz maximális száma |55 |30 napi biztonsági mentései<br>12 havi biztonsági mentései<br>13 éves biztonsági mentést |
| Manuális biztonsági mentések őrződnek meg az eszköz maximális száma |45 | |
| Egy fájlkiszolgáló megosztásonként fájlok maximális száma |1 millió |Ha egy eszköz visszaállítási folyamat végrehajtása, a helyreállítási időt legyenek azzal arányos, hogy a fájlok száma az eszközön a megosztásokat. |
| Az iSCSI-kiszolgáló kötetenként fájlok maximális száma |1 millió | |
| Virtuális tömb fájlok maximális száma |4 millió | |
| Állítsa vissza a helyreállítás ideje |Gyors helyreállítás |A visszaállítási a hőtérkép alapul, és a kötet méretétől függ.<br>Biztonsági mentési műveletek akkor fordulhat elő, amíg folyamatban van egy helyreállítási műveletéhez. |


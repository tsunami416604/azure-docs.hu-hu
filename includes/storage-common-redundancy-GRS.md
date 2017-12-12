Georedundáns tárolás (GRS) szolgál a legalább 99.99999999999999 % (16 9 tartozó) tartóssági objektumok egy adott évben által az adatok replikálása egy másodlagos régióban, amelyek több száz miles elhagyja az elsődleges régióban van. Ha a tárfiók Georedundáns engedélyezve van, az adatok akkor tartós teljes regionális kimaradás vagy az elsődleges régióban nincs helyreállítható katasztrófa esetén is.

A GRS engedélyezve van a tárfiókon egy frissítés először véglegesítése az elsődleges régióban. Majd a frissítés a rendszer replikálja aszinkron módon a másodlagos régióban, ahol azt is replikálódnak.

A GRS az elsődleges és másodlagos régióban külön tartalék tartományok közötti replikák kezelése, és frissítse a tárolási méretezési egység LRS leírt tartományban.

Szempontok:

* Mivel a aszinkron replikációs késés, regionális katasztrófa esetén is lehetséges, hogy még nem replikálódott a másodlagos régióba módosítások elvesznek az adatokat nem lehet helyreállítani az elsődleges régióban.
* A replika nem érhető el, kivéve, ha a Microsoft kezdeményezi a másodlagos régióba. Ha a Microsoft kezdeményezzen feladatátvételt a másodlagos régióban, akkor lesz olvasási és írási engedéllyel a feladatátvételt követően befejeződött. További információkért lásd: [vész-helyreállítási útmutatást](../articles/storage/common/storage-disaster-recovery-guidance.md).
* Ha egy alkalmazás szeretné a másodlagos régióba olvasni, a felhasználó engedélyeznie kell a RA-GRS.

A storage-fiók létrehozásakor ki kell választania a fiók az elsődleges régióban. A másodlagos régióba az elsődleges régió alapján történik, és nem módosítható. A következő táblázat az elsődleges és másodlagos régióban párosítása.

| Elsődleges | Másodlagos |
| --- | --- |
| USA északi középső régiója | USA déli középső régiója |
| USA déli középső régiója | USA északi középső régiója |
| USA keleti régiója | USA nyugati régiója |
| USA nyugati régiója | USA keleti régiója |
| USA 2. keleti régiója | USA középső régiója |
| USA középső régiója | USA 2. keleti régiója |
| Észak-Európa | Nyugat-Európa |
| Nyugat-Európa | Észak-Európa |
| Délkelet-Ázsia | Kelet-Ázsia |
| Kelet-Ázsia | Délkelet-Ázsia |
| Kelet-Kína | Észak-Kína |
| Észak-Kína | Kelet-Kína |
| Kelet-Japán | Nyugat-Japán |
| Nyugat-Japán | Kelet-Japán |
| Dél-Brazília | USA déli középső régiója |
| Kelet-Ausztrália | Délkelet-Ausztrália |
| Délkelet-Ausztrália | Kelet-Ausztrália |
| Dél-India | Közép-India |
| Közép-India | Dél-India |
| Nyugat-India | Dél-India |
| USA-beli államigazgatás – Iowa | USA-beli államigazgatás – Virginia |
| USA-beli államigazgatás – Virginia | USA-beli államigazgatás – Texas |
| USA-beli államigazgatás – Texas | USA-beli államigazgatás – Arizona |
| USA-beli államigazgatás – Arizona | USA-beli államigazgatás – Texas |
| Közép-Kanada | Kelet-Kanada |
| Kelet-Kanada | Közép-Kanada |
| Az Egyesült Királyság nyugati régiója | Az Egyesült Királyság déli régiója |
| Az Egyesült Királyság déli régiója | Az Egyesült Királyság nyugati régiója |
| Közép-Németország | Északkelet-Németország |
| Északkelet-Németország | Közép-Németország |
| USA nyugati régiója, 2. | USA nyugati középső régiója |
| USA nyugati középső régiója | USA nyugati régiója, 2. |

Használható az Azure-régiók naprakész információkat, lásd: [Azure-régiók](https://azure.microsoft.com/regions/).

>[!NOTE]  
> USA – (kormányzati) Virginia másodlagos régióba Velünk – (kormányzati) Texas. Velünk – (kormányzati) Virginia korábban, mint egy másodlagos régióban be a US – (kormányzati) Iowa. Tárfiókok továbbra is használhatja a US – (kormányzati) Iowa egy másodlagos régióban, egy másodlagos régióban, a US – (kormányzati) Texas is áttelepíti.
>
>

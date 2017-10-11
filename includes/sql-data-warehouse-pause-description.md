
<!--
includes/sql-data-warehouse-include-pause-description.md

Latest Freshness check:  2016-04-22 , barbkess.

As of circa 2016-04-22, the following topics might include this include:
articles/sql-data-warehouse/sql-data-warehouse-manage-scale-out-tasks.md
articles/sql-data-warehouse/sql-data-warehouse-manage-scale-out-tasks-powershell.md
articles/sql-data-warehouse/sql-data-warehouse-manage-scale-out-tasks-rest-api.md

-->
Költségek csökkentése érdekében szüneteltetése, és folytassa a számítási erőforrások igény. Például nem fogja használni az adatbázis éjszaka és hétvégén, akkor is ilyen alkalmakkor szünetelteti, és folytatásához azt a nap folyamán. Ön nem számlázni dwu-k, amíg az adatbázis fel van függesztve.

Ha az egérmutatót egy adatbázisban:

* Számítási és memória-erőforrások a rendszer visszairányítja az adatközpontban lévő rendelkezésre álló erőforrások készlete
* DWU költségek a szünet időtartama nulla.
* Ez nem befolyásolja az adatok tárolási és az adatok érintetlen marad. 
* Az SQL Data Warehouse visszavonja az összes futó vagy aszinkron művelet.


---
title: Replikációs késés – Azure Database for MySQL
description: Megtudhatja, hogyan lehet elhárítani a replikációs késést Azure Database for MySQL olvasási replikákkal
keywords: MySQL, hibakeresés, replikálás késése másodpercben
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: troubleshooting
ms.date: 10/08/2020
ms.openlocfilehash: 16a502a53b4441faf68ea342e0bc865731d38b1a
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91877112"
---
# <a name="troubleshoot-replication-latency-in-azure-database-for-mysql"></a>Azure Database for MySQL replikációs késésének hibáinak megoldása

Az [olvasási replika](concepts-read-replicas.md) funkció lehetővé teszi az adatok replikálását egy Azure Database for MySQL-kiszolgálóról egy írásvédett replika kiszolgálóra. Az olvasási replikák a számítási feladatok felskálázására szolgálnak az alkalmazásból a replika kiszolgálókra küldött olvasási és jelentéskészítési lekérdezések útválasztásával. Ez csökkenti a terhelést az elsődleges kiszolgálón, és növeli az alkalmazás teljes teljesítményét és késését. A replikák aszinkron módon frissülnek a MySQL-motor natív bináris naplójának (binlog) fájlpozíció-alapú replikációs technológiájával. A BinLog-replikációval kapcsolatos további tudnivalókért tekintse meg a [MySQL BinLog-replikáció áttekintése](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)című témakört. 

A másodlagos olvasási replikák replikációs késése a faktorok számától függ, többek között a következőktől 

- Hálózati késleltetés
- Tranzakciós kötet a forráskiszolgálón
- Forrás-és másodlagos olvasási replika kiszolgáló számítási szintje
- Az elsődleges és a másodlagos kiszolgálón futó lekérdezések. 

Ebből a dokumentumból megismerheti, hogyan lehet elhárítani a Azure Database for MySQL replikációs késését. Emellett a replikációs késések néhány gyakori okát is megtudhatja a replika-kiszolgálókon.

## <a name="replication-concepts"></a>Replikációs fogalmak

Ha engedélyezve van a bináris napló, a forráskiszolgáló a replikáláshoz használt bináris naplóba írja a véglegesített tranzakciót. A bináris napló alapértelmezés szerint be van kapcsolva minden olyan újonnan kiosztott kiszolgáló esetében, amely legfeljebb 16 TB tárterületet támogat. A replika-kiszolgálókon két szál fut a másodpéldány-kiszolgálón, az egyik az IO-szál, a másik pedig az SQL-szál.

- Az **i/o-szál** csatlakozik a forráskiszolgálón, és a frissített bináris naplókat kéri. Miután ez a szál megkapja a bináris napló frissítéseit, a rendszer egy replika-kiszolgálóra menti őket egy, a Relay-napló nevű helyi naplóban.
- Az **SQL-szál** beolvassa a Relay-naplót, és alkalmazza az adatmódosítás (oka) t a replika-kiszolgálókon.

## <a name="monitoring-replication-latency"></a>Replikáció késésének figyelése

A Azure Database for MySQL a replikáció késését a [Azure monitor](concepts-monitoring.md)másodpercben mért metrikája biztosítja. Ez a metrika csak olvasási replika kiszolgálókon érhető el. Ez a metrika a MySQL-ben elérhető seconds_behind_master metrika használatával számítható ki. A replikációs késés kiváltó okainak megismeréséhez kapcsolódjon a replika kiszolgálóhoz a [MySQL Workbench](connect-workbench.md) vagy az [Azure Cloud Shell](https://shell.azure.com) használatával, és hajtsa végre a következő parancsot:

 Cserélje le az értékeket a tényleges replika-kiszolgáló nevére és a rendszergazda felhasználói bejelentkezési nevére. A rendszergazdai felhasználónévhez a (z) Azure Database for MySQL neve szükséges \<servername> :

  ```azurecli-interactive
  mysql --host=myreplicademoserver.mysql.database.azure.com --user=myadmin@mydemoserver -p 
  ```

  Az alábbi módon néz ki a élmény a Cloud Shell terminálon?
  ```
  Requesting a Cloud Shell.Succeeded.
  Connecting terminal...

  Welcome to Azure Cloud Shell

  Type "az" to use Azure CLI
  Type "help" to learn about Cloud Shell

  user@Azure:~$mysql -h myreplicademoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
  Enter password:
  Welcome to the MySQL monitor.  Commands end with ; or \g.
  Your MySQL connection id is 64796
  Server version: 5.6.42.0 Source distribution

  Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

  Oracle is a registered trademark of Oracle Corporation and/or its
  affiliates. Other names may be trademarks of their respective
  owners.

  Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
  mysql>
  ```
  Ugyanebben a Azure Cloud Shell terminálban hajtsa végre a következő parancsot:

  ```
  mysql> SHOW SLAVE STATUS;
  ```

  Egy tipikus kimenet A következőképpen fog kinézni:
  
>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/howto-troubleshoot-replication-latency/show-status.png" alt-text="Replikáció késésének figyelése&quot;:::


A kimenet számos információt tartalmaz, de általában csak a következő oszlopokra koncentrálhat:

|Metrika|Leírás|
|---|---|
|Slave_IO_State| Az IO-szál aktuális állapota. Az állapot általában &quot;Várakozás a főkiszolgálónak az esemény küldésére&quot;, ha szinkronizálást végez. Ha azonban &quot;Csatlakozás a főkiszolgálóhoz&quot; állapot jelenik meg, akkor a replika elvesztette a kapcsolatot a főkiszolgálóval. Ellenőrizze, hogy a főkiszolgáló fut-e, vagy hogy a tűzfal blokkolja-e a kapcsolatokat.|
|Master_Log_File| A bináris naplófájl, amelybe a főkiszolgáló írást készít.|
|Read_Master_Log_Pos| Azt a pozíciót jelöli a fenti bináris naplófájlban, amelyben a főkiszolgáló írást készít.|
|Relay_Master_Log_File| A jelzett érték azt a bináris naplófájlt jelöli, amelyet a replika kiszolgáló a főkiszolgálóról olvas.|
|Slave_IO_Running| Azt jelzi, hogy fut-e az IO-szál. Ennek a következőnek kell lennie: &quot;yes&quot;. Ha a &quot;nem&quot;, a replikálás valószínűleg megszakad.|
|Slave_SQL_Running| Azt jelzi, hogy fut-e az SQL-szál. Ennek a következőnek kell lennie: &quot;yes&quot;. Ha a &quot;nem&quot;, a replikálás valószínűleg megszakad.|
|Exec_Master_Log_Pos| Megjeleníti a replika által alkalmazott Relay_Master_Log_File pozícióját. Késés esetén ennek a pozíciónak kisebbnek kell lennie, mint Read_Master_Log_Pos.|
|Relay_Log_Space|Megjeleníti a Relay-napló méretének felső határát. A méretet a globális változók megjelenítése (például &quot;relay_log_space_limit&quot;) lekérdezésével tekintheti meg.|
|Seconds_Behind_Master| A replikációs késést jeleníti meg másodpercben.|
|Last_IO_Errno|Az i/o-szál hibakódját jeleníti meg, ha van ilyen. További információt ezekről a kódokról a [MySQL dokumentációjában](https://dev.mysql.com/doc/refman/5.7/en/server-error-reference.html)talál.|
|Last_IO_Error| Az i/o-szál hibaüzenetét jeleníti meg, ha van ilyen.|
|Last_SQL_Errno|Megjeleníti az SQL-szál hibakódját, ha van ilyen. További információt ezekről a kódokról a [MySQL dokumentációjában](https://dev.mysql.com/doc/refman/5.7/en/server-error-reference.html)talál.|
|Last_SQL_Error|Az SQL-szál hibaüzenetét jeleníti meg, ha van ilyen.|
|Slave_SQL_Running_State| Az aktuális SQL-szál állapotát jelzi. Vegye figyelembe, hogy az ebben az állapotban látható &quot;rendszerzárolás&quot; normális viselkedés. Normális, hogy a &quot;függő tranzakció elutasításra vár" állapotot tekinti a rendszer. Azt jelzi, hogy a replika arra vár, hogy a főkiszolgáló frissítse a véglegesített tranzakciókat.|

Ha Slave_IO_Running igen, és Slave_SQL_Running igen, akkor a replikáció rendben fut. 

Ezután ellenőriznie kell Last_IO_Errno, Last_IO_Error, Last_SQL_Errno és Last_SQL_Error.  Ezek a mezők az SQL-szál leállítását okozó legutóbbi hiba hibáját és hibaüzenetét jelenítik meg. A 0. számú hibaszám és az üres üzenet azt jelenti, hogy nincs hiba. A hibaüzenetben nem nulla értéket kell megvizsgálni a [MySQL dokumentációjában](https://dev.mysql.com/doc/refman/5.7/en/server-error-reference.html)található hibakód megkeresésével.

## <a name="common-scenarios-for-high-replication-latency"></a>Gyakori forgatókönyvek a nagy replikációs késéshez

### <a name="network-latency-or-high-cpu-on-source-server"></a>Hálózati késés vagy magas CPU a forráskiszolgálón

Ha a következő értékeket veszi figyelembe, a replikálás késésének leggyakoribb oka a hálózati késés vagy a magas CPU-használat a forráskiszolgálón. Ebben az esetben az IO-szál fut, és a főkiszolgálóra vár. A főkiszolgáló (forráskiszolgáló) már a bináris naplófájl #20 lett írva, míg a replika csak a fájl #10t kapta. Ebben a forgatókönyvben a nagy replikációs késés elsődleges tényezői a hálózati sebesség vagy a magas CPU-kihasználtság a forráskiszolgálón.  Az Azure-ban a régión belüli hálózati késés jellemzően ezredmásodpercben, a régióban pedig akár másodperceket is igénybe vehet. A legtöbb esetben az IO-szálnak a forráskiszolgáló felé való kapcsolódáshoz szükséges késése a forráskiszolgáló magas CPU-kihasználtsága okozza, ami miatt az IO-szál feldolgozásának lassúnak kell lennie. Ez a CPU-kihasználtság figyelésével, valamint a forráskiszolgáló egyidejű kapcsolatainak az Azure monitor használatával történő megfigyelésével észlelhető.

Ha nem látja a magas CPU-kihasználtságot a forráskiszolgálón, a lehetséges okok hálózati késések lehetnek. Ha a magas hálózati késést rendellenesen látja, akkor javasoljuk, hogy az [Azure állapot lapján](https://status.azure.com/status) ellenőrizze, hogy vannak-e nem ismert problémák vagy kimaradások. 

```
Slave_IO_State: Waiting for master to send event
Master_Log_File: the binary file sequence is larger then Relay_Master_Log_File, e.g. mysql-bin.00020
Relay_Master_Log_File: the file sequence is smaller than Master_Log_File, e.g. mysql-bin.00010
```

### <a name="heavy-burst-of-transactions-on-source-server"></a>A forráskiszolgáló tranzakcióinak súlyos felszakadása

Ha betartja a következő értékeket, a replikálás késésének leggyakoribb oka a forráskiszolgáló tranzakcióinak nagy mértékű felszakadása. Az alábbi kimenetben, bár a replika le tudja kérni a bináris naplót a főkiszolgáló mögött, a replika i/o-szála azt jelzi, hogy a továbbítási napló területe már megtelt. Így a hálózati sebesség nem okozza a késést, mert a replika már megpróbálta felfogni a lehető leggyorsabban. Ehelyett a frissített bináris napló mérete meghaladja a továbbítási napló területének felső korlátját. A probléma további megoldásához engedélyezni kell a [lassú lekérdezési naplót](concepts-server-logs.md) a főkiszolgálón. A lassú lekérdezési naplók lehetővé teszik a hosszú ideig futó tranzakciók azonosítását a forráskiszolgálón. Az azonosított lekérdezéseket a kiszolgáló késésének csökkentése érdekében be kell hangolni. 

```
Slave_IO_State: Waiting for the slave SQL thread to free enough relay log space
Master_Log_File: the binary file sequence is larger then Relay_Master_Log_File, e.g. mysql-bin.00020
Relay_Master_Log_File: the file sequence is smaller then Master_Log_File, e.g. mysql-bin.00010
```

Ebben a kategóriában a késés leggyakoribb okai a következők:

#### <a name="replication-latency-due-to-data-load-on-source-server"></a>Replikációs késés a forráskiszolgáló adatterhelése miatt
Bizonyos esetekben a forráskiszolgálón hetente vagy havonta történik az adatterhelés. Sajnos a replikálás késése nem elkerülhető ebben az esetben. Ebben az esetben a replika-kiszolgálók a forráskiszolgáló adatterhelésének befejeződése után végül felvesznek.


### <a name="slowness-on-the-replica-server"></a>A replika-kiszolgáló lassúsága

Ha betartja a következő értékeket, a leggyakoribb ok lehet a replika-kiszolgáló olyan problémája, amely további vizsgálatot igényel. Ebben a forgatókönyvben, ahogy a kimenetben látható, az i/o-és az SQL-szálak is jól futnak, és a replika ugyanazt a bináris naplófájlt olvashatja, mint a fő írási műveletek. Azonban némi késés történik a replika-kiszolgálón, hogy ugyanazt a tranzakciót tükrözze a forráskiszolgálón. 

```
Slave_IO_State: Waiting for master to send event
Master_Log_File: The binary log file sequence equals to Relay_Master_Log_File, e.g. mysql-bin.000191
Read_Master_Log_Pos: The position of master server written to the above file is larger than Relay_Log_Pos, e.g. 103978138
Relay_Master_Log_File: mysql-bin.000191
Slave_IO_Running: Yes
Slave_SQL_Running: Yes
Exec_Master_Log_Pos: The position of slave reads from master binary log file is smaller than Read_Master_Log_Pos, e.g. 13468882
Seconds_Behind_Master: There is latency and the value here is greater than 0
```

Ebben a kategóriában a késés leggyakoribb okai a következők:

#### <a name="no-primary-or-unique-key-on-a-table"></a>Nincs elsődleges vagy egyedi kulcs egy táblában

A Azure Database for MySQL sor alapú replikálást használ. A sor-alapú replikációval a főkiszolgáló az egyes táblázatos sorok módosítására vonatkozó bináris naplóba írja az eseményeket. Az SQL-szál bekapcsolásával végrehajtja ezeket a módosításokat a replika-kiszolgálón lévő megfelelő táblázat soraiban. Egy tábla elsődleges vagy egyedi kulcsa nem a replikációs késés leggyakoribb okai közé esik. Az elsődleges vagy az egyedi kulcsok hiánya azt eredményezi, hogy az SQL-szál ellenőrzi a cél tábla összes sorát a módosítások alkalmazásához.

A MySQL-ben az elsődleges kulcs egy társított index, amely gyors lekérdezési teljesítményt biztosít, mivel nem tartalmazhat NULL értékeket. A InnoDB-tároló motorja fizikailag úgy van rendszerezve, hogy az elsődleges kulcs alapján rendkívül gyors keresési és rendezési műveleteket hajtson végre. Ezért javasoljuk, hogy adjon hozzá egy elsődleges kulcsot a forráskiszolgálón lévő táblákhoz a replika kiszolgáló létrehozása előtt. Ebben a forgatókönyvben elsődleges kulcsokat kell hozzáadnia a forráskiszolgálón, és újból létre kell hoznia az olvasási replikákat a replikálási késés javítása érdekében.

A következő lekérdezéssel határozhatja meg a forráskiszolgáló elsődleges kulcsával rendelkező táblákat:

```sql 
select tab.table_schema as database_name, tab.table_name 
from information_schema.tables tab left join 
information_schema.table_constraints tco 
on tab.table_schema = tco.table_schema 
and tab.table_name = tco.table_name 
and tco.constraint_type = 'PRIMARY KEY' 
where tco.constraint_type is null 
and tab.table_schema not in('mysql', 'information_schema', 'performance_schema', 'sys') 
and tab.table_type = 'BASE TABLE' 
order by tab.table_schema, tab.table_name;

```

#### <a name="replication-latency-due-to-long-running-queries-on-replica-server"></a>Replikációs késés a replika-kiszolgáló hosszan futó lekérdezéseinek miatt

Előfordulhat, hogy a replika kiszolgáló munkaterhelése megakadályozhatja, hogy az SQL-szál lépést tudjon tartani az IO-szálral. Ez az egyik gyakori oka a nagy replikációs késésnek, ha hosszú ideig futó lekérdezés van a másodpéldány-kiszolgálón. Ebben az esetben a [lassú lekérdezési naplót](concepts-server-logs.md) engedélyezni kell a replika-kiszolgálón a probléma elhárítása érdekében. A lassú lekérdezések növelhetik az erőforrások felhasználását, vagy lelassítják a kiszolgálót, így a replika nem fogja tudni felfogni a főkiszolgálóval. Ebben az esetben a lassú lekérdezéseket kell hangolni. A gyorsabb lekérdezések megakadályozzák az SQL-szál blokkolását, és jelentősen növelik a replikálás késését.


#### <a name="replication-latency-due-to-ddl-queries-on-source-server"></a>Replikációs késés a forráskiszolgálón található DDL-lekérdezések miatt
Ha van egy hosszú ideig futó DDL-parancs, például az [ALTER TABLE](https://dev.mysql.com/doc/refman/5.7/en/alter-table.html) utasítás végrehajtása a forráskiszolgálón, és azt jelenti, hogy a végrehajtás 1 órát vett igénybe. Ebben az időszakban előfordulhat, hogy több ezer más lekérdezés fut párhuzamosan a forráskiszolgálón. Ha a DDL replikálása a replikára történik, az adatbázis konzisztenciájának biztosítása érdekében a MySQL motornak egyetlen replikációs szálban kell futtatnia a DDL-t. Így minden más replikált lekérdezés le lesz tiltva, és várnia kell egy órát vagy többet, amíg a DDL-művelet be nem fejeződik a replika kiszolgálón. Ez az online DDL-művelettől függetlenül igaz. A DDL-műveletek esetében a replikálás várhatóan nagyobb replikálási késést tapasztal.

Ha a forráskiszolgálón a [lassú lekérdezési napló](concepts-server-logs.md) engedélyezve van, ez a forgatókönyv a lassú lekérdezési naplók segítségével deríti fel, hogy a FORRÁSKISZOLGÁLÓN a DDL-parancs végrehajtása megtörtént-e. Bár az indexek eldobása, az átnevezés és a létrehozás a MÓDOSÍTÁSi TÁBLÁZAThoz a inplace algoritmust kell használnia, a táblázatos adatok másolását, és a tábla újraépítését is. Általában a beléptetési algoritmus párhuzamos DML-je támogatott, de a tábla exkluzív metaadat-zárolása a művelet előkészítési és végrehajtási fázisaiban rövid ideig is elvégezhető. Így a CREATE INDEX utasításhoz a záradékok ALGORITMUSa és ZÁROLÁSa befolyásolhatja a táblázat másolási módszerét és a párhuzamosság szintjét az olvasáshoz és az íráshoz, azonban a teljes SZÖVEGES vagy térbeli index hozzáadása továbbra is megakadályozza a DML-műveleteket. Tekintse meg az alábbi példát egy index létrehozásához az ALGORITMUS és a LOCK záradék használatával:

```sql
ALTER TABLE table_name ADD INDEX index_name (column), ALGORITHM=INPLACE, LOCK=NONE;
```

A zárolást igénylő DDL-utasítások esetében sajnos nem lehet elkerülni a replikálási késést, hanem a DDL-műveletek időpontját a nyitvatartási idő alatt, a lehetséges hatás csökkentése érdekében.

#### <a name="replication-latency-due-to-replica-server-lower-sku"></a>Replikációs késés a replika-kiszolgáló alsó SKU-jának miatt

Azure Database for MySQL olvasási replikák a főkiszolgálóval megegyező kiszolgáló-konfigurációval jönnek létre. A replika-kiszolgáló konfigurációja a létrehozása után módosítható. Ha azonban a replika-kiszolgálót visszaminősítik, a munkaterhelés magasabb erőforrás-felhasználást eredményezhet, amely viszont replikációs késéshez vezethet. Ez megfigyelhető a replika CPU-és memória-felhasználásának figyelésével Azure Monitorról. Ebben a forgatókönyvben azt javasoljuk, hogy a replika-kiszolgáló konfigurációját a forrásnál egyenlő vagy annál nagyobb értékekkel kell megőrizni, hogy a replika képes legyen lépést tartani a főkiszolgálóval.

#### <a name="improving-replication-latency-using-server-parameter-tuning-on-source-server"></a>Replikációs késés javítása a forráskiszolgáló kiszolgálói paraméterének finomhangolásával

Azure Database for MySQL a replikáció alapértelmezés szerint párhuzamos szálon fut a replikák esetében. A magas egyidejűségi számítási feladatokhoz a forráskiszolgálón, ahol a replika-kiszolgáló nem tud felzárkózni, a replikálás késése a forráskiszolgáló binlog_group_commit_sync_delay paraméterének konfigurálásával növelhető. Ezzel a paraméterrel megtudhatja, hogy hány másodpercenként a bináris napló véglegesíti a bináris naplófájl szinkronizálását. Ennek az az előnye, hogy ahelyett, hogy azonnal alkalmazza az összes véglegesített tranzakciót, a főkiszolgáló tömegesen küldi el a bináris napló frissítéseit. Ez csökkenti az IO-t a replikán, és segít a teljesítmény javításában. Ebben a forgatókönyvben hasznos lehet a binlog_group_commit_sync_delay beállítása 1000-re vagy a replikáció késésének figyelésére. Ezt a paramétert óvatosan kell beállítani, és a magas egyidejű számítási feladatokhoz csak a nagy teljesítményű számítási feladatok használhatók. Ha az alacsony egyidejűségi forgatókönyvhöz sok különálló tranzakció van, akkor a binlog_group_commit_sync_delay beállítása a késéshez adható, mert az IO szál a tömeges bináris naplók frissítésére vár, miközben csak néhány tranzakció véglegesíthető. 

## <a name="next-steps"></a>Következő lépések
További információ a [MySQL BinLog-replikáció áttekintése](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

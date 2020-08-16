---
title: Alkalmazás-fejlesztés – ajánlott eljárások – Azure Database for MySQL
description: Ismerje meg az alkalmazások Azure Database for MySQL-vel való létrehozásával kapcsolatos ajánlott eljárásokat
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 36f9cfa2369032351f6ed2948fc0c98c1648bcb6
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2020
ms.locfileid: "88259248"
---
# <a name="best-practices-for-building-applications-with-azure-database-for-mysql"></a>Ajánlott eljárások alkalmazások létrehozásához Azure Database for MySQL 

Íme néhány ajánlott eljárás a felhőalapú alkalmazások Azure Database for MySQL használatával történő létrehozásához, amely csökkentheti az alkalmazás fejlesztési idejét. 

## <a name="application-and-database-resource-configuration"></a>Alkalmazás-és adatbázis-erőforrás konfigurálása

### <a name="application-and-database-in-the-same-region"></a>Az alkalmazás és az adatbázis ugyanabban a régióban
Győződjön meg arról, hogy az alkalmazás az Azure-ban való üzembe helyezése során **minden függőség ugyanabban a régióban van**  . A különböző régiókon vagy rendelkezésre állási zónákban lévő példányok terjesztése hálózati késést okoz, ami hatással lehet az alkalmazás általános teljesítményére. 

### <a name="keep-your-mysql-server-secure"></a>A MySQL-kiszolgáló biztonságának megőrzése
A MySQL-kiszolgálót úgy kell konfigurálni, hogy [biztonságos](https://docs.microsoft.com/azure/mysql/concepts-security) és nyilvánosan nem elérhető legyen. A következő lehetőségek közül bármelyiket használhatja a kiszolgáló biztonságossá tételéhez: 
- [Tűzfalszabályok](https://docs.microsoft.com/azure/mysql/concepts-firewall-rules) vagy
- [Virtuális hálózatok](https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet) vagy 
- [Privát kapcsolat](https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link)

A biztonság érdekében mindig **SSL** -kapcsolaton keresztül kell csatlakoznia a MySQL-kiszolgálóhoz, és konfigurálnia kell a MySQL-kiszolgálót és az alkalmazást a **TLS 1.2**használatára. Lásd: [az SSL/TLS konfigurálása](https://docs.microsoft.com/azure/mysql/concepts-ssl-connection-security). 

### <a name="tune-your-server-parameters"></a>Kiszolgáló paramétereinek hangolása
Az olvasási és nagy teljesítményű számítási feladatokhoz a "tmp_table_size és max_heap_table_size" segítségével optimalizálhatja a jobb teljesítményt. A tmp_table_size és max_heap_table_size szükséges értékek kiszámításához tekintse meg a kapcsolatonként a teljes és az alapmemóriát. A-kapcsolatok memóriájának paramétereinek összege, a tmp_table_size kivételével, a kiszolgáló teljes memóriájának alapmemória-fiókjaival együtt.

Tmp_table_size és max_heap_table_size lehető legnagyobb méretének kiszámításához használja a következő képletet:

```(total memory - (base memory + (sum of per-connection memory * # of connections)) / # of connections```

>[!NOTE]
> A teljes memória azt jelzi, hogy a kiszolgáló mennyi memóriát tartalmaz a virtuális mag kiépítve.  Ha például egy általános célú 2 virtuális mag Azure Database for MySQL-kiszolgáló, a teljes memória 5 GB/s lesz.  Az egyes szintek memóriájának további részletei a [díjszabási](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers) csomag dokumentációjában találhatók.
> Az alapmemória a memória változóit jelzi, például query_cache_size és innodb_buffer_pool_size, a MySQL inicializálása és lefoglalása a kiszolgáló indításakor történik.  A kapcsolati memória (például a sort_buffer_size és a join_buffer_size) olyan memória, amely csak akkor van lefoglalva, ha egy lekérdezés igényli.

### <a name="create-a-non-admin-user"></a>Nem rendszergazda felhasználó létrehozása 
[Hozzon létre nem rendszergazda felhasználókat](https://docs.microsoft.com/azure/mysql/howto-create-users) az egyes adatbázisokhoz. A felhasználónevek általában adatbázis-nevekként vannak azonosítva.

### <a name="resetting-your-password"></a>Jelszó alaphelyzetbe állítása
A MySQL-kiszolgáló jelszavát Azure Portal használatával [állíthatja alaphelyzetbe](https://docs.microsoft.com/azure/mysql/howto-create-manage-server-portal#update-admin-password) . 

Az éles adatbázis-kiszolgáló jelszavának alaphelyzetbe állítása leállíthatja az alkalmazást. Az éles számítási feladatokhoz tartozó jelszó alaphelyzetbe állítása az alkalmazás végfelhasználói általi hatásának csökkentése érdekében jó példa.

## <a name="performance-and-resiliency"></a>Teljesítmény és rugalmasság 
Az alábbiakban néhány olyan eszközt és mintát talál, amelyek segítségével az alkalmazással kapcsolatos teljesítményproblémák hibakeresését végezheti el.

### <a name="enable-slow-query-logs-identify-performance-issues"></a>Lassú lekérdezési naplók engedélyezése a teljesítménnyel kapcsolatos problémák azonosításához
Engedélyezheti a [lassú lekérdezési naplókat](https://docs.microsoft.com/azure/mysql/concepts-server-logs) [és a naplókat a kiszolgálón](https://docs.microsoft.com/azure/mysql/concepts-audit-logs) . A lassú lekérdezési naplók elemzése segíthet a teljesítménybeli szűk keresztmetszetek azonosításában a hibaelhárításhoz. 

A naplók az Azure diagnosztikai naplóin keresztül is elérhetők Azure Monitor naplókban, Event Hubsban és a Storage-fiókban is. Lásd: [a lekérdezések teljesítményével kapcsolatos problémák elhárítása](https://docs.microsoft.com/azure/mysql/howto-troubleshoot-query-performance).

### <a name="use-connection-pooling"></a>A kapcsolatok készletezésének használata
Az adatbázis-kapcsolatok kezelése jelentős hatással lehet az alkalmazás teljesítményére. A teljesítmény optimalizálása érdekében csökkentenie kell a kapcsolatok számát, valamint a kapcsolatok létrehozásához szükséges időt a kulcs kódjának elérési útjaiban.  A rugalmasság és a teljesítmény növelése érdekében a [kapcsolati készletezés](https://docs.microsoft.com/azure/mysql/concepts-connectivity#access-databases-by-using-connection-pooling-recommended) használatával kapcsolódjon Azure Database for MySQLhoz. 

A [ProxySQL](https://proxysql.com/), amely a kapcsolat Pooler hatékonyan használható a kapcsolatok kezeléséhez. A kapcsolati Pooler csökkentheti az üresjárati kapcsolatokat, és a meglévő kapcsolatok újbóli felhasználása segít elkerülni a problémákat. További információért lásd: [a ProxySQL beállítása](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/connecting-efficiently-to-azure-database-for-mysql-with-proxysql/ba-p/1279842) . 

### <a name="retry-logic-to-handle-transient-errors"></a>Újrapróbálkozási logika az átmeneti hibák kezelésére
Az alkalmazás [átmeneti hibákat](https://docs.microsoft.com/azure/mysql/concepts-connectivity#handling-transient-errors) tapasztalhat, amikor az adatbázishoz való kapcsolódást időnként eldobják vagy elveszítik. Ilyen helyzetekben a kiszolgáló egy-két újrapróbálkozás után 5-10 másodperc múlva működik. 

Az újrapróbálkozások közötti helyes minta 5 másodpercig vár az első újrapróbálkozás előtt, majd az egyes újrapróbálkozások végrehajtásával növelje a várakozást fokozatosan, 60 másodpercig. Korlátozza az újrapróbálkozások maximális számát, amikor az alkalmazás úgy véli, hogy a művelet meghiúsult, így tovább vizsgálhatja. További információt [a kapcsolódási hibák elhárítása](https://docs.microsoft.com/azure/mysql/howto-troubleshoot-common-connection-issues) című témakörben talál. 

### <a name="enable-read-replication-to-mitigate-failovers"></a>Olvasási replikálás engedélyezése a feladatátvételek enyhítéséhez
A feladatátvételi forgatókönyvek esetében [adatreplikálást](https://docs.microsoft.com/azure/mysql/howto-data-in-replication) is használhat. Olvasási replikák használata esetén nem történik automatikus feladatátvétel a fő-és a replika-kiszolgálók között. A replikáció aszinkron módon fog megjelenni a főkiszolgáló és a replika között. A hálózati késést számos tényező befolyásolhatja, például a főkiszolgálón futó munkaterhelés, valamint az adatközpontok közötti késleltetés. A legtöbb esetben a replika-késés néhány másodperc és néhány perc között mozog.

## <a name="database-deployment"></a>Adatbázis központi telepítése 

### <a name="configure-azure-database-for-mysql-task-in-your-cicd-deployment-pipeline"></a>A MySQL-hez készült Azure Database-feladat konfigurálása a CI/CD üzembe helyezési folyamatában
Időnként az adatbázis módosításait is telepítenie kell. Ilyen esetekben a folyamatos integráció (CI) és a folyamatos kézbesítés (CD) használatával is létrehozhatja az [Azure-folyamatokat](https://azure.microsoft.com/services/devops/pipelines/) , és felhasználhat egy feladatot a [MySQL-kiszolgáló](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-mysql-deployment?view=azure-devops) számára az adatbázis frissítéséhez egy egyéni parancsfájl futtatásával az adatbázison.

### <a name="manual-database-deployment"></a>Adatbázis manuális üzembe helyezése 
Az adatbázis kézi üzembe helyezése során itt egy jó példa az állásidő minimalizálása vagy a sikertelen üzembe helyezés kockázatának csökkentése érdekében: 

1. Éles adatbázis másolatának létrehozása egy új adatbázisban a [mysqldump](https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html) vagy a [MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-admin-export-import-management.html) használatával 
2. Frissítse az új adatbázist az adatbázisához szükséges új séma-módosításokkal vagy frissítésekkel. 
3. Helyezze az éles adatbázist írásvédett állapotba. Az üzemi adatbázisban nem lehet írási művelet, amíg az üzembe helyezés be nem fejeződik. 
4. Tesztelje az alkalmazást az 1. lépésben újonnan frissített adatbázissal.
5. Telepítse az alkalmazás módosításait, és győződjön meg arról, hogy az alkalmazás mostantól a legújabb frissítésekkel rendelkező új adatbázist használja. 
6. Tartsa meg a régi éles adatbázist, hogy vissza tudja állítani a módosításokat. Ezután kiértékelheti, hogy törli-e a régi éles adatbázist, vagy ha szükséges, exportálja azt az Azure Storage-ba. 

>[!NOTE]
>  - Ha az alkalmazás olyan, mint az e-kereskedelmi alkalmazás, ahol előfordulhat, hogy nem tudja csak olvasható állapotba helyezni, akkor a biztonsági mentés után közvetlenül az éles adatbázisban helyezheti üzembe a módosításokat.  A tézisek változását az alkalmazással való alacsony adatforgalom miatti minimze kell végrehajtani, hogy az egyes felhasználók sikertelen kérelmeket is tapasztalhatnak. 
>  - Győződjön meg arról, hogy az alkalmazás kódja a sikertelen kérelmeket is kezeli.

### <a name="use-mysql-native-metrics-to-see-if-your-workload-is-exceeding-in-memory-temporary-table-sizes"></a>A MySQL natív metrikáinak használatával megtudhatja, hogy a számítási feladat meghaladja-e a memóriabeli ideiglenes táblázat méretét
A nagy olvasási idejű számítási feladatokkal a MySQL-kiszolgálón futtatott lekérdezések túllépik a memóriában tárolt ideiglenes táblázatos méreteket. Ez azt eredményezheti, hogy a kiszolgáló olyan ideiglenes táblákat ír be, amelyek hatással vannak az alkalmazás teljesítményére. Annak megállapításához, hogy a kiszolgáló lemezre írása az ideiglenes táblázat méretének meghaladása miatt történt-e, tekintse meg a következő metrikákat:

```
show global status like 'created_tmp_disk_tables';
show global status like 'created_tmp_tables';
```
A created_tmp_disk_tables metrika azt jelzi, hogy hány tábla lett létrehozva a lemezen, míg a created_tmp_table metrika azt adja meg, hogy hány ideiglenes táblát kell létrehozni a memóriában a számítási feladatok miatt. Annak megállapításához, hogy egy adott lekérdezés futtatása ideiglenes táblákat fog-e használni, futtassa a magyarázatot a lekérdezésben. Ha a lekérdezés ideiglenes táblák használatával fut, az "extra" oszlopban az "ideiglenes" használata érték jelenik meg.

Ha a számítási feladatok százalékos arányát a lemezekre kiömlött lekérdezésekkel szeretné kiszámítani, használja a mérőszám értékeit az alábbi képletben:

```(created_tmp_disk_tables / (created_tmp_disk_tables + created_tmp_tables)) * 100```

Ideális esetben ennek a százaléknak kevesebb 25%-nak kell lennie. Ha úgy látja, hogy a százalékos arány 25% vagy nagyobb, javasoljuk, hogy módosítsa a két kiszolgáló paraméterét, tmp_table_size és max_heap_table_si


## <a name="database-schema-and-queries"></a>Adatbázis-séma és lekérdezések

Az alábbi tippeket és trükköket érdemes figyelembe venni, amikor létrehozza az adatbázis-sémát és a lekérdezéseket.

### <a name="always-use-the-right-datatype-for-your--table-columns"></a>Mindig használja a táblázat oszlopainak megfelelő adattípust
A megfelelő adattípusok használata a tárolni kívánt adattípus alapján optimalizálható a tárterület, és csökkentheti a helytelen adattípusok miatt előforduló hibákat.

### <a name="use-indexes"></a>Indexek használata
A lassú lekérdezések elkerüléséhez használhat indexeket. Az indexek segítségével gyorsan megtalálhatja az adott oszlopokból álló sorokat. Lásd: [az indexek használata a MySQL-ben](https://dev.mysql.com/doc/refman/8.0/en/mysql-indexes.html).

### <a name="explain-your-select-queries"></a>A VÁLASZTÓ lekérdezések ismertetése
A [magyarázat](https://dev.mysql.com/doc/refman/8.0/en/explain.html) segítségével megismerheti, hogy a MySQL hogyan hajtja végre a lekérdezést. Ez segíthet a lekérdezés szűk keresztmetszetének vagy problémáinak észlelésében. Tekintse meg, [hogyan használhatja a magyarázatot a profil lekérdezési teljesítményére](https://docs.microsoft.com/azure/mysql/howto-troubleshoot-query-performance).



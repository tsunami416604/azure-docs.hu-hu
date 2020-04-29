---
title: Korlátozások – Azure Database for MySQL
description: Ez a cikk a Azure Database for MySQL korlátozásait ismerteti, például a kapcsolatok számát és a tárolási motor beállításait.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/1/2020
ms.openlocfilehash: 6ca09ab0578fb88e443d6e9e1f920c22457eb042
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80548472"
---
# <a name="limitations-in-azure-database-for-mysql"></a>A Azure Database for MySQL korlátozásai
A következő szakaszok ismertetik a kapacitást, a tárolási motor támogatását, a jogosultságok támogatását, az adatmanipulációs nyilatkozatok támogatását és az adatbázis-szolgáltatás működési korlátait. Lásd még a MySQL-adatbázismotor esetében alkalmazandó [általános korlátozásokat](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) is.

## <a name="server-parameters"></a>Kiszolgálóparaméterek

Számos népszerű kiszolgálói paraméter minimális és maximális értékét a díjszabási szint és a virtuális mag határozza meg. A korlátokat az alábbi táblázatokban találja.

### <a name="max_connections"></a>max_connections

|**Díjszabási csomag**|**Virtuális mag (ok)**|**Alapértelmezett érték**|**Minimális érték**|**Maximális érték**|
|---|---|---|---|---|
|Basic|1|50|10|50|
|Basic|2|100|10|100|
|Általános célú|2|300|10|600|
|Általános célú|4|625|10|1250|
|Általános célú|8|1250|10|2500|
|Általános célú|16|2500|10|5000|
|Általános célú|32|5000|10|10000|
|Általános célú|64|10000|10|20000|
|Memóriaoptimalizált|2|600|10|800|
|Memóriaoptimalizált|4|1250|10|2500|
|Memóriaoptimalizált|8|2500|10|5000|
|Memóriaoptimalizált|16|5000|10|10000|
|Memóriaoptimalizált|32|10000|10|20000|

Ha a kapcsolatok túllépik a korlátot, a következő hibaüzenet jelenhet meg:
> HIBA 1040 (08004): túl sok kapcsolat

> [!IMPORTANT]
> A legjobb megoldás érdekében javasoljuk, hogy használjon olyan kapcsolati Pooler, mint a ProxySQL, hogy hatékonyan kezelhesse a kapcsolatokat.

A MySQL-hez való új ügyfélkapcsolatok létrehozása időt vesz igénybe, a kapcsolatok pedig adatbázis-erőforrásokat foglalnak magukban, még akkor is, ha tétlenek. A legtöbb alkalmazás sok rövid életű kapcsolatot igényel, amely ezt a helyzetet összeképezi. Ennek eredményeképpen kevesebb erőforrás érhető el a tényleges munkaterheléshez, ami csökkenti a teljesítményt. Egy kapcsolati Pooler, amely csökkenti az üresjárati kapcsolatokat, és a meglévő kapcsolatokat újra felhasználva segít elkerülni ezt. A ProxySQL beállításával kapcsolatos további információkért látogasson el a [blogbejegyzésbe](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042).

### <a name="query_cache_size"></a>query_cache_size

A lekérdezés gyorsítótára alapértelmezés szerint ki van kapcsolva. A lekérdezési gyorsítótár engedélyezéséhez konfigurálja a `query_cache_type` paramétert. 

A paraméterrel kapcsolatos további információkért tekintse meg a [MySQL dokumentációját](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_query_cache_size) .

> [!NOTE]
> A lekérdezési gyorsítótár elavult a MySQL-5.7.20 telepítése, és el lett távolítva a MySQL 8,0-ben.

|**Díjszabási csomag**|**Virtuális mag (ok)**|**Alapértelmezett érték**|**Minimális érték**|**Maximális érték**|
|---|---|---|---|---|
|Basic|1|Alapszintű csomag nem konfigurálható|N/A|N/A|
|Basic|2|Alapszintű csomag nem konfigurálható|N/A|N/A|
|Általános célú|2|0|0|16777216|
|Általános célú|4|0|0|33554432|
|Általános célú|8|0|0|67108864|
|Általános célú|16|0|0|134217728|
|Általános célú|32|0|0|134217728|
|Általános célú|64|0|0|134217728|
|Memóriaoptimalizált|2|0|0|33554432|
|Memóriaoptimalizált|4|0|0|67108864|
|Memóriaoptimalizált|8|0|0|134217728|
|Memóriaoptimalizált|16|0|0|134217728|
|Memóriaoptimalizált|32|0|0|134217728|

### <a name="sort_buffer_size"></a>sort_buffer_size

A paraméterrel kapcsolatos további információkért tekintse meg a [MySQL dokumentációját](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_sort_buffer_size) .

|**Díjszabási csomag**|**Virtuális mag (ok)**|**Alapértelmezett érték**|**Minimális érték**|**Maximális érték**|
|---|---|---|---|---|
|Basic|1|Alapszintű csomag nem konfigurálható|N/A|N/A|
|Basic|2|Alapszintű csomag nem konfigurálható|N/A|N/A|
|Általános célú|2|524288|32768|4194304|
|Általános célú|4|524288|32768|8388608|
|Általános célú|8|524288|32768|16777216|
|Általános célú|16|524288|32768|33554432|
|Általános célú|32|524288|32768|33554432|
|Általános célú|64|524288|32768|33554432|
|Memóriaoptimalizált|2|524288|32768|8388608|
|Memóriaoptimalizált|4|524288|32768|16777216|
|Memóriaoptimalizált|8|524288|32768|33554432|
|Memóriaoptimalizált|16|524288|32768|33554432|
|Memóriaoptimalizált|32|524288|32768|33554432|

### <a name="join_buffer_size"></a>join_buffer_size

A paraméterrel kapcsolatos további információkért tekintse meg a [MySQL dokumentációját](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_join_buffer_size) .

|**Díjszabási csomag**|**Virtuális mag (ok)**|**Alapértelmezett érték**|**Minimális érték**|**Maximális érték**|
|---|---|---|---|---|
|Basic|1|Alapszintű csomag nem konfigurálható|N/A|N/A|
|Basic|2|Alapszintű csomag nem konfigurálható|N/A|N/A|
|Általános célú|2|262144|128|268435455|
|Általános célú|4|262144|128|536870912|
|Általános célú|8|262144|128|1073741824|
|Általános célú|16|262144|128|2147483648|
|Általános célú|32|262144|128|4294967295|
|Általános célú|64|262144|128|4294967295|
|Memóriaoptimalizált|2|262144|128|536870912|
|Memóriaoptimalizált|4|262144|128|1073741824|
|Memóriaoptimalizált|8|262144|128|2147483648|
|Memóriaoptimalizált|16|262144|128|4294967295|
|Memóriaoptimalizált|32|262144|128|4294967295|

### <a name="max_heap_table_size"></a>max_heap_table_size

A paraméterrel kapcsolatos további információkért tekintse meg a [MySQL dokumentációját](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_max_heap_table_size) .

|**Díjszabási csomag**|**Virtuális mag (ok)**|**Alapértelmezett érték**|**Minimális érték**|**Maximális érték**|
|---|---|---|---|---|
|Basic|1|Alapszintű csomag nem konfigurálható|N/A|N/A|
|Basic|2|Alapszintű csomag nem konfigurálható|N/A|N/A|
|Általános célú|2|16777216|16384|268435455|
|Általános célú|4|16777216|16384|536870912|
|Általános célú|8|16777216|16384|1073741824|
|Általános célú|16|16777216|16384|2147483648|
|Általános célú|32|16777216|16384|4294967295|
|Általános célú|64|16777216|16384|4294967295|
|Memóriaoptimalizált|2|16777216|16384|536870912|
|Memóriaoptimalizált|4|16777216|16384|1073741824|
|Memóriaoptimalizált|8|16777216|16384|2147483648|
|Memóriaoptimalizált|16|16777216|16384|4294967295|
|Memóriaoptimalizált|32|16777216|16384|4294967295|

### <a name="tmp_table_size"></a>tmp_table_size

A paraméterrel kapcsolatos további információkért tekintse meg a [MySQL dokumentációját](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_tmp_table_size) .

|**Díjszabási csomag**|**Virtuális mag (ok)**|**Alapértelmezett érték**|**Minimális érték**|**Maximális érték**|
|---|---|---|---|---|
|Basic|1|Alapszintű csomag nem konfigurálható|N/A|N/A|
|Basic|2|Alapszintű csomag nem konfigurálható|N/A|N/A|
|Általános célú|2|16777216|1024|67108864|
|Általános célú|4|16777216|1024|134217728|
|Általános célú|8|16777216|1024|268435456|
|Általános célú|16|16777216|1024|536870912|
|Általános célú|32|16777216|1024|1073741824|
|Általános célú|64|16777216|1024|1073741824|
|Memóriaoptimalizált|2|16777216|1024|134217728|
|Memóriaoptimalizált|4|16777216|1024|268435456|
|Memóriaoptimalizált|8|16777216|1024|536870912|
|Memóriaoptimalizált|16|16777216|1024|1073741824|
|Memóriaoptimalizált|32|16777216|1024|1073741824|

### <a name="time_zone"></a>time_zone

Az időzóna-táblázatok úgy tölthetők fel, hogy `mysql.az_load_timezone` meghívja a tárolt eljárást egy eszközről, például a MySQL-parancssorból vagy a MySQL Workbenchből. A tárolt eljárás meghívásához és a globális vagy munkamenet szintű időzónák beállításához tekintse meg a [Azure Portal](howto-server-parameters.md#working-with-the-time-zone-parameter) vagy az [Azure CLI](howto-configure-server-parameters-using-cli.md#working-with-the-time-zone-parameter) -cikkeket.

## <a name="storage-engine-support"></a>A Storage Engine támogatása

### <a name="supported"></a>Támogatott
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [MEMÓRIA](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>Nem támogatott
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [BLACKHOLE](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [Archívum](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [ÖSSZEVONT](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privilege-support"></a>Jogosultságok támogatása

### <a name="unsupported"></a>Nem támogatott
- DBA-szerepkör: számos kiszolgáló-paraméter és-beállítás akaratlanul csökkentheti a kiszolgáló teljesítményét, vagy megtagadja az adatbázis-kezelők által nyújtott savas tulajdonságokat. A szolgáltatás integritásának és az SLA-nak a termék szintjén történő fenntartása érdekében ez a szolgáltatás nem teszi elérhetővé a DBA-szerepkört. Az alapértelmezett felhasználói fiók, amely új adatbázis-példány létrehozásakor jön létre, lehetővé teszi, hogy a felhasználó a felügyelt adatbázis-példányban a DDL-és DML-utasítások többségét elvégezze. 
- SZUPER jogosultság: a hasonló [Super jogosultság](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) is korlátozott.
- Leszűkítés: a létrehozáshoz és a korlátozásához Super jogosultságok szükségesek. Ha biztonsági másolat használatával importálja az adatimportálást, távolítsa el `CREATE DEFINER` manuálisan `--skip-definer` a parancsokat, vagy használja a parancsot a mysqldump végrehajtásakor.

## <a name="data-manipulation-statement-support"></a>Az adatkezelési utasítás támogatása

### <a name="supported"></a>Támogatott
- `LOAD DATA INFILE`támogatott, de a paramétert meg kell adni, és egy UNC elérési útra kell irányítani (az `[LOCAL]` Azure Storage csatlakoztatva van az SMB protokollon keresztül).

### <a name="unsupported"></a>Nem támogatott
- `SELECT ... INTO OUTFILE`

## <a name="functional-limitations"></a>Funkcionális korlátozások

### <a name="scale-operations"></a>Skálázási műveletek
- A dinamikus skálázás az alapszintű díjszabási szintekre és a rendszerből nem támogatott.
- A kiszolgáló tárolási méretének csökkentése nem támogatott.

### <a name="server-version-upgrades"></a>Kiszolgáló verziófrissítése
- A fő adatbázismotor-verziók közötti automatikus áttelepítés jelenleg nem támogatott. Ha a következő főverzióra szeretne frissíteni, hozzon létre egy [memóriaképet, és állítsa vissza](./concepts-migrate-dump-restore.md) egy olyan kiszolgálóra, amely az új motor verziójával lett létrehozva.

### <a name="point-in-time-restore"></a>Időponthoz kötött visszaállítás
- A PITR szolgáltatás használatakor az új kiszolgáló ugyanazzal a konfigurációval jön létre, mint a kiszolgáló.
- A törölt kiszolgáló visszaállítása nem támogatott.

### <a name="vnet-service-endpoints"></a>VNet-szolgáltatásvégpontok
- A VNet szolgáltatás-végpontok támogatása csak a általános célú és a memóriára optimalizált kiszolgálók esetében támogatott.

### <a name="storage-size"></a>Tárterület mérete
- Tekintse meg [a díjszabási](concepts-pricing-tiers.md) szinten a tárterületre vonatkozó korlátozásokat.

## <a name="current-known-issues"></a>Aktuális ismert problémák
- A MySQL-kiszolgáló példánya a kiszolgáló nem megfelelő verzióját jeleníti meg a kapcsolatok létrehozása után. A megfelelő kiszolgálópéldány-motor verziójának lekéréséhez használja `select version();` a parancsot.

## <a name="next-steps"></a>További lépések
- [Az egyes szolgáltatási szinteknél elérhető szolgáltatások](concepts-pricing-tiers.md)
- [Támogatott MySQL-adatbázis-verziók](concepts-supported-versions.md)

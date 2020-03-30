---
title: Korlátozások – Azure-adatbázis a MySQL-hez
description: Ez a cikk az Azure Database for MySQL korlátait ismerteti, például a kapcsolat és a tárolómotor beállításainak számát.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: bc4694928eceed57692a0d4b0469543c1a8f9678
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79532756"
---
# <a name="limitations-in-azure-database-for-mysql"></a>A MySQL Azure-adatbázisának korlátai
A következő szakaszok a kapacitást, a tárolómotor-támogatást, a jogosultság-támogatást, az adatkezelési utasítások támogatását és az adatbázis-szolgáltatás funkcionális korlátait ismertetik. Lásd még a MySQL adatbázis-motorra vonatkozó [általános korlátozásokat.](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html)

## <a name="server-parameters"></a>Kiszolgálóparaméterek

Több népszerű kiszolgálóparaméter minimális és maximális értékét a tarifacsomag és a virtuális magok határozzák meg. A korlátokat az alábbi táblázatokban talál.

### <a name="max_connections"></a>max_connections

|**Árképzési szint**|**virtuális mag(k)**|**Alapértelmezett érték**|**Min érték**|**Maximális érték**|
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
> 1040-es hiba (08004): Túl sok kapcsolat

> [!IMPORTANT]
> A legjobb élmény érdekében azt javasoljuk, hogy a kapcsolatok hatékony kezeléséhez használjon egy kapcsolatpoolert, például a ProxySQL-t.

Új ügyfélkapcsolatok létrehozása a MySQL időt vesz igénybe, és miután létre, ezek a kapcsolatok foglalják el adatbázis-erőforrásokat, még akkor is, ha tétlen. A legtöbb alkalmazás sok rövid életű kapcsolatot kér, ami összetette ezt a helyzetet. Az eredmény kevesebb erőforrás áll rendelkezésre a tényleges munkaterheléshez, ami a teljesítmény csökkenéséhez vezet. Az alapjárati kapcsolatokat csökkentő és a meglévő kapcsolatokat újrafeltöltő kapcsolatgyűjtő segít elkerülni ezt. Ha többet szeretne megtudni a ProxySQL beállításáról, látogasson el [blogbejegyzésünkbe.](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042)

### <a name="query_cache_size"></a>query_cache_size

A lekérdezési gyorsítótár alapértelmezés szerint ki van kapcsolva. A lekérdezési gyorsítótár engedélyezéséhez konfigurálja a `query_cache_type` paramétert. 

Tekintse át a [MySQL dokumentációját,](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_query_cache_size) ha többet szeretne megtudni erről a paraméterről.

> [!NOTE]
> A lekérdezési gyorsítótár a MySQL 5.7.20-tól elavult, és a MySQL 8.0-ban eltávolították

|**Árképzési szint**|**virtuális mag(k)**|**Alapértelmezett érték**|**Min érték**|**Maximális érték**|
|---|---|---|---|---|
|Basic|1|Nem konfigurálható az alapszinten|N/A|N/A|
|Basic|2|Nem konfigurálható az alapszinten|N/A|N/A|
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

Tekintse át a [MySQL dokumentációját,](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_sort_buffer_size) ha többet szeretne megtudni erről a paraméterről.

|**Árképzési szint**|**virtuális mag(k)**|**Alapértelmezett érték**|**Min érték**|**Maximális érték**|
|---|---|---|---|---|
|Basic|1|Nem konfigurálható az alapszinten|N/A|N/A|
|Basic|2|Nem konfigurálható az alapszinten|N/A|N/A|
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

Tekintse át a [MySQL dokumentációját,](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_join_buffer_size) ha többet szeretne megtudni erről a paraméterről.

|**Árképzési szint**|**virtuális mag(k)**|**Alapértelmezett érték**|**Min érték**|**Maximális érték**|
|---|---|---|---|---|
|Basic|1|Nem konfigurálható az alapszinten|N/A|N/A|
|Basic|2|Nem konfigurálható az alapszinten|N/A|N/A|
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

Tekintse át a [MySQL dokumentációját,](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_max_heap_table_size) ha többet szeretne megtudni erről a paraméterről.

|**Árképzési szint**|**virtuális mag(k)**|**Alapértelmezett érték**|**Min érték**|**Maximális érték**|
|---|---|---|---|---|
|Basic|1|Nem konfigurálható az alapszinten|N/A|N/A|
|Basic|2|Nem konfigurálható az alapszinten|N/A|N/A|
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

Tekintse át a [MySQL dokumentációját,](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_tmp_table_size) ha többet szeretne megtudni erről a paraméterről.

|**Árképzési szint**|**virtuális mag(k)**|**Alapértelmezett érték**|**Min érték**|**Maximális érték**|
|---|---|---|---|---|
|Basic|1|Nem konfigurálható az alapszinten|N/A|N/A|
|Basic|2|Nem konfigurálható az alapszinten|N/A|N/A|
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

## <a name="storage-engine-support"></a>A tárolómotor támogatása

### <a name="supported"></a>Támogatott
- [Innodb](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [Memória](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>Nem támogatott
- [Myisam](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [FEKETELYUK](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [Archívum](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [Összevont](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privilege-support"></a>Jogosultság támogatása

### <a name="unsupported"></a>Nem támogatott
- DBA-szerepkör: Számos kiszolgálóparaméter és -beállítás véletlenül ronthatja a kiszolgáló teljesítményét, vagy elháríthatja a DBMS ACID-tulajdonságait. Ennek megfelelően a szolgáltatás integritásának és az SLA termékszinten való fenntartásához ez a szolgáltatás nem teszi elérhetővé a DBA szerepkört. Az alapértelmezett felhasználói fiók, amely egy új adatbázispéldány létrehozásakor jön létre, lehetővé teszi, hogy a felhasználó a kezelt adatbázispéldányban a DDL- és DML-utasítások nagy részét végrehajtsa. 
- SUPER jogosultság: Hasonlóképpen [SUPER jogosultság](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) is korlátozott.
- DEFINER: Létrehozásához szuperjogosultságokat igényel, és korlátozott. Ha biztonsági másolat tal importál `CREATE DEFINER` adatokat, távolítsa `--skip-definer` el a parancsokat manuálisan vagy a paranccsal mysqldump végrehajtásakor.

## <a name="data-manipulation-statement-support"></a>Adatkezelési nyilatkozat támogatása

### <a name="supported"></a>Támogatott
- `LOAD DATA INFILE`támogatott, de `[LOCAL]` a paramétert meg kell adni, és egy UNC elérési útra kell irányítani (az Azure storage smb-n keresztül csatlakoztatva).

### <a name="unsupported"></a>Nem támogatott
- `SELECT ... INTO OUTFILE`

## <a name="functional-limitations"></a>Funkcionális korlátozások

### <a name="scale-operations"></a>Méretezési műveletek
- Az alapszintű tarifacsomagok dinamikus skálázása jelenleg nem támogatott.
- A kiszolgáló tárhelyének csökkentése nem támogatott.

### <a name="server-version-upgrades"></a>Kiszolgálóverzió-frissítések
- A fő adatbázis-motorverziók közötti automatikus áttelepítés jelenleg nem támogatott. Ha a következő főverzióra szeretne frissíteni, fogjon egy [memóriaképet, és állítsa vissza](./concepts-migrate-dump-restore.md) egy olyan kiszolgálóra, amelyet az új motorverzióval hoztak létre.

### <a name="point-in-time-restore"></a>Időponthoz kötött visszaállítás
- A PITR szolgáltatás használatakor az új kiszolgáló ugyanolyan konfigurációkkal jön létre, mint az a kiszolgáló, amelyen alapul.
- A törölt kiszolgáló visszaállítása nem támogatott.

### <a name="vnet-service-endpoints"></a>VNet-szolgáltatásvégpontok
- A Virtuálishálózat-szolgáltatás végpontjainak támogatása csak általános célú és memóriaoptimalizált kiszolgálókhoz érhető el.

### <a name="storage-size"></a>Tárhely mérete
- Kérjük, tekintse meg a [tarifacsomagok](concepts-pricing-tiers.md) a tárolási méret korlátok tarifacsomagonként.

## <a name="current-known-issues"></a>Aktuális ismert problémák
- A MySQL kiszolgálópéldány a kapcsolat létrejötte után rossz kiszolgálóverziót jeleníti meg. A megfelelő kiszolgálópéldánymotor-verzió beszerezéséhez használja a `select version();` parancsot.

## <a name="next-steps"></a>További lépések
- [Mi érhető el az egyes szolgáltatási szinteken?](concepts-pricing-tiers.md)
- [Támogatott MySQL adatbázis-verziók](concepts-supported-versions.md)

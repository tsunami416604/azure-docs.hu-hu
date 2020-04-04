---
title: Korlátozások – Azure-adatbázis a MariaDB-hez
description: Ez a cikk a MariaDB Azure Database korlátait ismerteti, például a kapcsolat és a tárolómotor beállításainak számát.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 4/1/2020
ms.openlocfilehash: 18f227c1888e0565eebb640fa61ced56dc994865
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632339"
---
# <a name="limitations-in-azure-database-for-mariadb"></a>A MariaDB Azure-adatbázisának korlátai
A következő szakaszok a kapacitást, a tárolómotor-támogatást, a jogosultság-támogatást, az adatkezelési utasítások támogatását és az adatbázis-szolgáltatás funkcionális korlátait ismertetik.

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

A MariaDB-vel létesített új ügyfélkapcsolatok létrehozása időt vesz igénybe, és a létrehozásután ezek a kapcsolatok még tétlen járat esetén is elfoglalják az adatbázis-erőforrásokat. A legtöbb alkalmazás sok rövid életű kapcsolatot kér, ami összetette ezt a helyzetet. Az eredmény kevesebb erőforrás áll rendelkezésre a tényleges munkaterheléshez, ami a teljesítmény csökkenéséhez vezet. Az alapjárati kapcsolatokat csökkentő és a meglévő kapcsolatokat újrafeltöltő kapcsolatgyűjtő segít elkerülni ezt. Ha többet szeretne megtudni a ProxySQL beállításáról, látogasson el [blogbejegyzésünkbe.](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042)

### <a name="query_cache_size"></a>query_cache_size

A lekérdezési gyorsítótár alapértelmezés szerint ki van kapcsolva. A lekérdezési gyorsítótár engedélyezéséhez konfigurálja a `query_cache_type` paramétert. 

A [paraméterről a MariaDB dokumentációjában](https://mariadb.com/kb/en/server-system-variables/#query_cache_size) olvashat bővebben.

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

A [paraméterről a MariaDB dokumentációjában](https://mariadb.com/kb/en/server-system-variables/#sort_buffer_size) olvashat bővebben.

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

A [paraméterről a MariaDB dokumentációjában](https://mariadb.com/kb/en/server-system-variables/#join_buffer_size) olvashat bővebben.

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

A [paraméterről a MariaDB dokumentációjában](https://mariadb.com/kb/en/server-system-variables/#max_heap_table_size) olvashat bővebben.

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

A [paraméterről a MariaDB dokumentációjában](https://mariadb.com/kb/en/server-system-variables/#tmp_table_size) olvashat bővebben.

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

### <a name="time_zone"></a>time_zone

Az időzóna-táblák feltölthetők `mysql.az_load_timezone` a tárolt eljárás hívásával egy eszközről, például a MySQL parancssorból vagy a MySQL Workbench-ből. Tekintse meg az [Azure Portalon](howto-server-parameters.md#working-with-the-time-zone-parameter) vagy az [Azure CLI](howto-configure-server-parameters-cli.md#working-with-the-time-zone-parameter) cikkeket, hogyan hívja meg a tárolt eljárást, és állítsa be a globális vagy munkamenet-szintű időzónák.

## <a name="storage-engine-support"></a>A tárolómotor támogatása

### <a name="supported"></a>Támogatott
- [Innodb](https://mariadb.com/kb/en/library/xtradb-and-innodb/)
- [Memória](https://mariadb.com/kb/en/library/memory-storage-engine/)

### <a name="unsupported"></a>Nem támogatott
- [Myisam](https://mariadb.com/kb/en/library/myisam-storage-engine/)
- [FEKETELYUK](https://mariadb.com/kb/en/library/blackhole/)
- [Archívum](https://mariadb.com/kb/en/library/archive/)

## <a name="privilege-support"></a>Jogosultság támogatása

### <a name="unsupported"></a>Nem támogatott
- DBA-szerepkör: Számos kiszolgálóparaméter és -beállítás véletlenül ronthatja a kiszolgáló teljesítményét, vagy elháríthatja a DBMS ACID-tulajdonságait. Ennek megfelelően a szolgáltatás integritásának és az SLA termékszinten való fenntartásához ez a szolgáltatás nem teszi elérhetővé a DBA szerepkört. Az alapértelmezett felhasználói fiók, amely egy új adatbázispéldány létrehozásakor jön létre, lehetővé teszi, hogy a felhasználó a kezelt adatbázispéldányban a DDL- és DML-utasítások nagy részét végrehajtsa.
- SUPER jogosultság: Hasonlóképpen [SUPER jogosultság](https://mariadb.com/kb/en/library/grant/#global-privileges) is korlátozott.
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
- A fő adatbázis-motorverziók közötti automatikus áttelepítés jelenleg nem támogatott.

### <a name="point-in-time-restore"></a>Időponthoz kötött visszaállítás
- A PITR szolgáltatás használatakor az új kiszolgáló ugyanolyan konfigurációkkal jön létre, mint az a kiszolgáló, amelyen alapul.
- A törölt kiszolgáló visszaállítása nem támogatott.

### <a name="subscription-management"></a>Előfizetés-kezelés
- Az előfizetések és az erőforráscsoportok közötti, előre létrehozott kiszolgálók dinamikus áthelyezése jelenleg nem támogatott.

### <a name="vnet-service-endpoints"></a>VNet-szolgáltatásvégpontok
- A Virtuálishálózat-szolgáltatás végpontjainak támogatása csak általános célú és memóriaoptimalizált kiszolgálókhoz érhető el.

### <a name="storage-size"></a>Tárhely mérete
- Kérjük, tekintse meg a [tarifacsomagok](concepts-pricing-tiers.md) a tárolási méret korlátok tarifacsomagonként.

## <a name="current-known-issues"></a>Aktuális ismert problémák
- A MariaDB kiszolgálópéldány a kapcsolat létrehozása után a helytelen kiszolgálóverziót jeleníti meg. A megfelelő kiszolgálópéldánymotor-verzió beszerezéséhez használja a `select version();` parancsot.

## <a name="next-steps"></a>További lépések
- [Mi érhető el az egyes szolgáltatási szinteken?](concepts-pricing-tiers.md)
- [Támogatott MariaDB adatbázis-verziók](concepts-supported-versions.md)

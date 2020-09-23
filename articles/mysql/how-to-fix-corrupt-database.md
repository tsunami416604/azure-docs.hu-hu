---
title: Adatbázis-sérülés feloldása – Azure Database for MySQL
description: Tudnivalók a Azure Database for MySQL adatbázis-sérüléssel kapcsolatos problémáinak javításáról
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: 815b24d25e674e5460cc50d7eb6871f740994893
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90935938"
---
# <a name="troubleshoot-database-corruption-on-azure-database-for-mysql"></a>A Azure Database for MySQL adatbázis-sérülésének hibáinak megoldása
[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

Az adatbázis-sérülés az alkalmazás leállását okozhatja, és az adatvesztés elkerülése érdekében az is fontos, hogy időben megoldja a problémát. Ha az adatbázis sérült, akkor a kiszolgáló naplózza ezt a hibát **InnoDB: az adatbázis-oldal sérülése lemezen vagy sikertelen**.

Ebből az útmutatóból megtudhatja, hogyan lehet megállapítani, hogy az adatbázis vagy a tábla sérült-e. Azure Database for MySQL a InnoDB motort használja, és automatizált sérülés-ellenőrzési és javítási műveleteket végez. A InnoDB ellenőrzi, hogy az összes beolvasott oldalon ellenőrzőösszegeket végzünk-e, és ha az ellenőrzőösszeget észlel, akkor a rendszer automatikusan leállítja a MySQL-kiszolgálót.

Az alábbi lehetőségek közül bármelyiket kipróbálva gyorsan csökkentheti az adatbázis-sérüléssel kapcsolatos problémákat.

## <a name="restart-your-mysql-server"></a>A MySQL-kiszolgáló újraindítása

Általában megfigyelheti, hogy egy adatbázis vagy egy tábla sérült, amikor az alkalmazás hozzáfér a Table ro-adatbázishoz. Mivel a InnoDB olyan összeomlás-helyreállítási mechanizmust tartalmaz, amely a kiszolgáló újraindításakor a legtöbb problémát képes megoldani. A kiszolgáló újraindítása ezért segítheti a kiszolgáló helyreállítását egy olyan összeomlásból, amely miatt az adatbázis helytelen állapotban volt.

##  <a name="resolve-data-corruption-with-dump-and-restore-method"></a>Az adatsérülés feloldása a Memóriaképtel és a visszaállítási módszerrel

Azt javasoljuk, hogy hárítsa el a sérüléssel kapcsolatos problémát egy **memóriakép és visszaállítási módszer**használatával. Ez magában foglalja a sérült tábla elérését, a **mysqldump** segédprogrammal a tábla logikai biztonsági másolatának létrehozásához, amely megőrzi a tábla szerkezetét és az abban belüli adatmennyiséget, majd újra betölti a táblát az adatbázisba.

### <a name="backup-your-database-or-tables"></a>Adatbázis vagy táblák biztonsági mentése

> [!Important]
> - Győződjön meg arról, hogy konfigurálta a tűzfalszabály beállításait ahhoz, hogy hozzáférjen a kiszolgálóhoz az ügyfélgépről. Tekintse meg a [Tűzfalszabályok konfigurálása az egyetlen kiszolgálón](howto-manage-firewall-using-portal.md) és a [Tűzfalszabály a rugalmas kiszolgálón](flexible-server/how-to-connect-tls-ssl.md)című témakört.
> - SSL-beállítás használata ```--ssl-cert``` a **mysqldump** , ha az SSL engedélyezve van

Biztonságimásolat-fájl létrehozása a parancssorból a mysqldump használatával ezzel a paranccsal

```
$ mysqldump [--ssl-cert=/path/to/pem] -h [host] -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

A megadható paraméterek a következők:
- [SSL-tanúsítvány =/Path/to/PEM] Töltse le az SSL-tanúsítványt az ügyfélszámítógépen, és állítsa be az elérési utat a parancsban. Ne használja ezt az SSL le van tiltva.
- a (z) [Host] a Azure Database for MySQL-kiszolgáló
- [uname] a kiszolgáló-rendszergazdai felhasználóneve
- [pass] a rendszergazda felhasználó jelszava
- [dbname] az adatbázis neve
- [biztonságimentésfájlja. SQL] Ha az adatbázis biztonsági másolatának fájlneve

> [!Important]
> - Az egyes kiszolgálók esetében az ```admin-user@servername``` alábbi parancsokkal cserélje le a formátumot ```myserveradmin``` .
> - A rugalmas kiszolgálók esetében az ```admin-user``` alábbi parancsokkal cserélje le a formátumot ```myserveradmin``` .

Ha egy adott tábla sérült, akkor válassza ki az adatbázis adott táblázatait a példa használatával történő biztonsági mentéshez.
```
$ mysqldump --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb table1 table2 > testdb_tables_backup.sql
```

Egy vagy több adatbázis biztonsági mentéséhez használja az--Database kapcsolót, és sorolja fel az adatbázis nevét szóközökkel elválasztva.

```
$ mysqldump --ssl-cert=</path/to/pem>  -h mydemoserver.mysql.database.azure.com -u myserveradmin -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql
```

###  <a name="restore-your-database-or-tables"></a>Adatbázis vagy táblák visszaállítása

A következő lépések bemutatják, hogyan állíthatja vissza a TP az adatbázist vagy a táblákat. A biztonságimásolat-fájl létrehozása után a ***MySQL** segédprogram használatával visszaállíthatja a táblát vagy az adatbázisokat. Futtassa a parancsot az alább látható módon:

```
mysql  --ssl-cert=</path/to/pem> -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
Íme egy példa a ```testdb``` **mysqldump**-mel létrehozott biztonságimásolat-fájl visszaállítására. 

> [!Important]
> - Az egyetlen kiszolgáló esetében az ```admin-user@servername``` alábbi parancsban cserélje le a formátumot ```myserveradmin``` .
> - A rugalmas kiszolgálók esetében az ```admin-user``` alábbi parancs használatával cserélje le a formátumot ```myserveradmin``` . 

```
$ mysql --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb < testdb_backup.sql
```

## <a name="next-steps"></a>Következő lépések
Ha a fenti lépések nem segítenek a probléma megoldásában, bármikor visszaállíthatja a teljes kiszolgálót.
- [Azure Database for MySQL egyetlen kiszolgáló visszaállítása](howto-restore-server-portal.md)
- [Azure Database for MySQL rugalmas kiszolgáló visszaállítása](flexible-server/how-to-restore-server-portal.md)




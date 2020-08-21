---
title: Gyakori hibák elhárítása – Azure Database for MySQL
description: Ismerje meg, hogy miként lehet elhárítani a gyakori áttelepítési hibákat a Azure Database for MySQL szolgáltatás új felhasználóinak.
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 8/20/2020
ms.openlocfilehash: ebe9f936e3d0dfafec23842fcdbfd225995d546b
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2020
ms.locfileid: "88720250"
---
# <a name="common-errors"></a>Gyakori hibák

A Azure Database for MySQL egy teljes körűen felügyelt szolgáltatás, amelyet a MySQL közösségi verziója működtet. A felügyelt szolgáltatási környezetekben a MySQL-élmény eltérhet a saját környezetében futó MySQL-től. Ebből a cikkből megtudhatja, hogy a felhasználók milyen gyakori hibákba ütköznek a Azure Database for MySQL szolgáltatásba való Migrálás vagy fejlesztés során.

## <a name="errors-due-to-lack-of-super-privilege-and-dba-role"></a>A SUPER Privilege és a DBA szerepkör hiánya miatti hibák

A SUPER Privilege és a DBA szerepkör nem támogatott a szolgáltatásban. Ennek eredményeképpen előfordulhat, hogy az alábbi gyakori hibák merülhetnek fel:

#### <a name="error-1419-you-do-not-have-the-super-privilege-and-binary-logging-is-enabled-you-might-want-to-use-the-less-safe-log_bin_trust_function_creators-variable"></a>1419-es hiba: nem rendelkezik a SUPER jogosultsággal, és a bináris naplózás engedélyezve van ( *Előfordulhat* , hogy a kevésbé biztonságos log_bin_trust_function_creators változót szeretné használni)

A fenti hiba akkor fordulhat elő, amikor egy függvényt hoz létre, az alábbi triggert vagy egy sémát importál. A DDL-utasítások, például a CREATE FUNCTION vagy a CREATE TRIGGER a bináris naplóba íródnak, így a másodlagos replika is végrehajthatja azokat. A replika SQL-szála teljes körű jogosultságokkal rendelkezik, amelyek felhasználhatók a jogosultságok kiterjesztésére. Ha ezt a veszélyforrást a bináris naplózást engedélyező kiszolgálókon szeretné védeni, a MySQL-motornak a tárolt függvények létrehozásához a SUPER jogosultsággal kell rendelkeznie, a szokásos CREATE RUTIN jogosultsággal kiegészítve. 

```sql
CREATE FUNCTION f1(i INT)
RETURNS INT
DETERMINISTIC
READS SQL DATA
BEGIN
  RETURN i;
END;
```

**Megoldás**: a hiba elhárításához állítsa a portálon a log_bin_trust_function_creators [server parameters](howto-server-parameters.md) 1 értékre, majd a kívánt objektumok létrehozásához HAJTsa végre a DDL-utasításokat, vagy importálja a sémát, hogy létrehozza a kívánt objektumokat, majd visszaállítja a log_bin_trust_function_creators paramétert az előző értékre a létrehozás után.

#### <a name="error-1227-42000-at-line-101-access-denied-you-need-at-least-one-of-the-super-privileges-for-this-operation-operation-failed-with-exitcode-1"></a>1227-es hiba (42000) a következő 101 sorban: hozzáférés megtagadva; Ehhez a művelethez szükség van a SUPER jogosultság (ok) ra (legalább az egyikre). A művelet nem sikerült az 1. ExitCode

A fenti hiba akkor fordulhat elő, amikor egy memóriaképfájl importálása vagy a [definomabb](https://dev.mysql.com/doc/refman/5.7/en/create-procedure.html)eljárásokat tartalmazó eljárás létrehozása történik. 

**Megoldás**: a hiba megoldásához a rendszergazda felhasználó jogosultságokat adhat az eljárások létrehozásához vagy végrehajtásához az alábbi példáknak megfelelően:

```sql
GRANT CREATE ROUTINE ON mydb.* TO 'someuser'@'somehost';
GRANT EXECUTE ON PROCEDURE mydb.myproc TO 'someuser'@'somehost';
```
Azt is megteheti, hogy lecseréli az importálási folyamatot futtató rendszergazda felhasználó nevét az alább látható módon.

```sql
DELIMITER;;
/*!50003 CREATE*/ /*!50017 DEFINER=`root`@`127.0.0.1`*/ /*!50003
DELIMITER;;

/* Modified to */

DELIMITER ;;
/*!50003 CREATE*/ /*!50017 DEFINER=`AdminUserName`@`ServerName`*/ /*!50003
DELIMITER ;
```

## <a name="next-steps"></a>További lépések
Ha nem találta meg a keresett választ, vegye figyelembe a következőket:
- Tegye fel kérdéseit a [Microsoft Q&a kérdéses oldalra](https://docs.microsoft.com/answers/topics/azure-database-mysql.html) vagy [stack Overflowra](https://stackoverflow.com/questions/tagged/azure-database-mysql).
- Küldjön e-mailt a MySQL-hez készült Azure Database for MySQL Team [ @Ask Azure db](mailto:AskAzureDBforMySQL@service.microsoft.com)-nek. Ez az e-mail-cím nem technikai támogatási alias.
- Vegye fel a kapcsolatot az Azure támogatási szolgálatával, és kérjen [jegyet a Azure Portalból](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Ha a fiókjával van probléma, nyújtson be [támogatási kérelmet](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) az Azure Portalon.
- Ha visszajelzést szeretne küldeni vagy új szolgáltatásokat kérne, hozzon létre egy bejegyzést a [UserVoice-on](https://feedback.azure.com/forums/597982-azure-database-for-mysql).

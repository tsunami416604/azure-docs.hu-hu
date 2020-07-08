---
title: Replikáció konfigurálása az Azure SQL Edge-be (előzetes verzió)
description: Tudnivalók az Azure SQL Edge (előzetes verzió) replikálásának konfigurálásáról.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: a33933e63cc7c15de7d60430521f810f8546988c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84667879"
---
# <a name="configure-replication-to-azure-sql-edge-preview"></a>Replikáció konfigurálása az Azure SQL Edge-be (előzetes verzió) 

Az Azure SQL Edge egy példányát leküldéses előfizetőként konfigurálhatja egyirányú tranzakciós replikáláshoz vagy pillanatkép-replikációhoz. Ez a példány nem működhet közzétevőként vagy terjesztőként a tranzakciós replikálási konfigurációhoz. Vegye figyelembe, hogy az Azure SQL Edge nem támogatja az egyesítéses replikálást, a társközi replikálást vagy az Oracle-közzétételt.

## <a name="supported-configurations"></a>Támogatott konfigurációk
  
- Az Azure SQL Edge példányának leküldéses előfizetőnek kell lennie a közzétevő számára.
- A közzétevő és a terjesztő a következők egyike lehet:
   - Helyszíni vagy SQL Server Azure-beli virtuális gépen futó példánya SQL Server. További információ: [SQL Server az Azure Virtual Machines áttekintése](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-infrastructure-services/). SQL Server példányoknak a SQL Server 2016-nál későbbi verziót kell használnia.
   - Az Azure SQL felügyelt példányának egy példánya. Az SQL felügyelt példánya képes a közzétevő, a terjesztő és az előfizetői adatbázisok üzemeltetésére. További információ: [replikáció SQL Database felügyelt példánnyal](https://docs.microsoft.com/azure/sql-database/replication-with-sql-database-managed-instance/).

- A terjesztési adatbázis és a replikációs ügynökök nem helyezhetők el az Azure SQL Edge egy példányán.  

> [!NOTE]
> Ha nem támogatott verzióval kísérli meg a replikáció konfigurálását, akkor a következő két hiba jelenhet meg: MSSQL_REPL20084 ("a folyamat nem tudott csatlakozni az előfizetőhöz.") és a MSSQL_REPL40532 ("nem nyitható meg \<name> a bejelentkezés által kért kiszolgáló. A bejelentkezés sikertelen. ").  

## <a name="remarks"></a>Megjegyzések

Az alábbi követelmények és ajánlott eljárások fontosak a replikáció konfigurálásához:

- [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)használatával is konfigurálhatja a replikációt. Azt is megteheti, hogy Transact-SQL-utasításokat futtat a közzétevőn SQL Server Management Studio vagy [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/download-azure-data-studio)használatával.
- Az Azure SQL Edge egy példányára való replikáláshoz SQL Server hitelesítést kell használnia a bejelentkezéshez.
- A replikált tábláknak elsődleges kulccsal kell rendelkezniük.
- SQL Server egyetlen kiadványa képes támogatni az Azure SQL Edge és a SQL Server (helyszíni és SQL Server Azure-beli virtuális gépeken) előfizetők számára.  
- A replikációs felügyeletet, a figyelést és a hibaelhárítást a SQL Server példányból kell végrehajtani.  
- Csak az Azure SQL Edge-re irányuló leküldéses előfizetések támogatottak.  
- Csak az `@subscriber_type = 0` `sp_addsubscription` Azure SQL Edge tárolt eljárása támogatja.  
- Az Azure SQL Edge nem támogatja a kétirányú, az azonnali, a frissíthető vagy a társközi replikálást.
- Az Azure SQL Edge csak SQL Server vagy SQL felügyelt példányban elérhető szolgáltatások egy részhalmazát támogatja. Ha egy vagy több nem támogatott funkciót tartalmazó adatbázist (vagy az adatbázison belüli objektumokat) próbál replikálni, a kísérlet meghiúsul. Ha például olyan adatbázist próbál replikálni, amely térbeli adattípusú objektumokat tartalmaz, hibaüzenetet kap. További információ: [Az Azure SQL Edge támogatott funkciói](features.md).

## <a name="initialize-reference-data-on-an-instance-of-azure-sql-edge"></a>Referenciák inicializálása az Azure SQL Edge egy példányán

Előfordulhat, hogy inicializálni szeretné a példányt az időbeli változásokkal rendelkező referenciákkal. Előfordulhat például, hogy frissíteni szeretné a gépi tanulási modelleket az Azure SQL Edge-példányán, miután betanítják őket egy SQL Server példányra. A következőképpen inicializálhatja a példányát egy ilyen forgatókönyvben:

1. Tranzakciós replikálási kiadvány létrehozása SQL Server adatbázison.  
2. Az SQL Server-példányon az **új előfizetés varázsló** vagy a Transact-SQL-utasítások segítségével hozzon létre egy leküldéses előfizetést az Azure SQL Edge-be.  
3. A replikált adatbázist az Azure SQL Edge-ben inicializálhatja a pillanatkép-ügynök által generált pillanatkép használatával, és a terjesztési ügynök által terjesztett és továbbított. Azt is megteheti, hogy az adatbázisról készült biztonsági másolat használatával inicializálja a közzétevőt. Ne feledje, hogy ha az adatbázis biztonsági másolata olyan objektumokat vagy szolgáltatásokat tartalmaz, amelyeket az Azure SQL Edge nem támogat, a visszaállítási művelet sikertelen lesz.

## <a name="limitations"></a>Korlátozások

Az Azure SQL Edge-előfizetések esetében az alábbi beállítások nem támogatottak:

- Fájlcsoportok társításának másolása  
- Táblázat particionálási sémáinak másolása  
- Index particionálási sémáinak másolása  
- Felhasználó által definiált statisztikák másolása  
- Alapértelmezett kötések másolása  
- Szabály-kötések másolása  
- Teljes szöveges indexek másolása  
- XML XSD másolása  
- XML-indexek másolása  
- Másolási engedélyek  
- Térbeli indexek másolása  
- Szűrt indexek másolása  
- Adattömörítési attribútum másolása  
- Ritka oszlop attribútumának másolása  
- FileStream, `hierarchyid` vagy térbeli adattípusok másolása
- Konvertálás `hierarchyid` maximális adattípusra  
- Térbeli érték konvertálása maximális adattípusra  
- Kiterjesztett tulajdonságok másolása  
- Másolási engedélyek  

## <a name="examples"></a>Példák

Hozzon létre egy kiadványt és egy leküldéses előfizetést. További információkért lásd:
  
- [Kiadvány létrehozása](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Hozzon létre egy leküldéses előfizetést](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/) az Azure SQL Edge-kiszolgáló nevével és IP-címével előfizetőként (például **myEdgeinstance, 1433**), valamint egy adatbázisnevet az Azure SQL Edge-példányon célként megadott adatbázisként (például **AdventureWorks**).  

## <a name="next-steps"></a>További lépések  

- [Kiadvány létrehozása](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Leküldéses előfizetés létrehozása](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Replikálási típusok](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Figyelés (replikálás)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Előfizetés inicializálása](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  



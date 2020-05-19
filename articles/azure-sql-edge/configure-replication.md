---
title: Replikáció konfigurálása az Azure SQL Edge-be (előzetes verzió)
description: Tudnivalók az Azure SQL Edge-re való replikálás konfigurálásáról (előzetes verzió)
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: e2b37e0f3ccf5fcebe4723c05d644f2cbb7c1d56
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83596945"
---
# <a name="configure-replication-to-azure-sql-edge-preview"></a>Replikáció konfigurálása az Azure SQL Edge-be (előzetes verzió) 

Az Azure SQL Edge-példány leküldéses előfizetőként is konfigurálható egyirányú tranzakciós replikáláshoz vagy pillanatkép-replikációhoz. Az Azure SQL Edge-példány nem működhet közzétevőként vagy terjesztőként a tranzakciós replikálási konfigurációhoz. Az Azure SQL Edge nem támogatja az egyesítéses replikációt, a P2P replikációt és az Oracle-közzétételt.

## <a name="supported-configurations"></a>**Támogatott konfigurációk**:
  
- Az Azure SQL Edge-példánynak leküldéses előfizetőnek kell lennie a közzétevő számára.
- A kiadó és a terjesztő a következők egyike lehet
   - A SQL Server egy példánya, amely helyszíni vagy SQL Server egy példányát futtatja egy Azure-beli virtuális gépen. További információ: [SQL Server az Azure Virtual Machines áttekintése](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-infrastructure-services/). SQL Server példányoknak a SQL Server 2016-nál nagyobb verziószámot kell használnia.
   - Azure SQL Database felügyelt példány egy példánya. A felügyelt példányok közzétevői, terjesztői és előfizetői adatbázisokat is futtathatnak. További információ: [replikáció SQL Database felügyelt példánnyal](https://docs.microsoft.com/azure/sql-database/replication-with-sql-database-managed-instance/).

- A terjesztési adatbázis és a replikációs ügynökök nem helyezhetők üzembe Azure SQL Edge-példányon.  

> [!NOTE]
> Ha nem támogatott verzióval konfigurálja a replikálást, a hiba MSSQL_REPL20084 (a folyamat nem tudott csatlakozni az előfizetőhöz.) és a MSSQL_REPL40532 (nem nyitható meg \< a bejelentkezés által kért kiszolgálónév>. A bejelentkezés sikertelen.)  

Az Azure SQL Edge összes funkciójának használatához a [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) és [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)legújabb verzióit kell használnia.  

## <a name="remarks"></a>Megjegyzések

- A replikáció a [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) használatával vagy a SQL Server Management Studio vagy az [Azure Database Studio](https://docs.microsoft.com/sql/azure-data-studio/download-azure-data-studio) használatával konfigurálható a közzétevőn a Transact-SQL-utasítások végrehajtásával
- A replikáció csak SQL Server hitelesítési bejelentkezéseket használhat egy Azure SQL Edge-példányhoz való kapcsolódáshoz.
- A replikált tábláknak elsődleges kulccsal kell rendelkezniük.
- SQL Server egyetlen kiadványa képes támogatni az Azure SQL Edge és a SQL Server (helyszíni és SQL Server Azure-beli virtuális gépeken) előfizetők számára.  
- A replikációs felügyeletet, a figyelést és a hibaelhárítást a helyszíni SQL Server kell végrehajtania.  
- Csak az Azure SQL Edge-re irányuló leküldéses előfizetések támogatottak.  
- Csak az `@subscriber_type = 0` Azure SQL Edge **sp_addsubscription** támogatja.  
- Az Azure SQL Edge nem támogatja a kétirányú, azonnali, frissíthető vagy egyenrangú replikálást.
- Az Azure SQL Edge csak a SQL Server vagy Azure SQL Database felügyelt példányban elérhető szolgáltatások egy részhalmazát támogatja, mivel egy vagy több nem támogatott funkciót tartalmazó adatbázis (vagy az adatbázison belüli objektumok) replikálására tett kísérlet hibát eredményezhet. Ha például olyan adatbázist próbál replikálni, amely térbeli adattípusú objektumokat tartalmaz, hibaüzenetet fog eredményezni. Az Azure SQL Edge által támogatott szolgáltatásokkal kapcsolatos további információkért lásd: [Az Azure SQL Edge támogatott funkciói](features.md).

## <a name="scenarios"></a>Forgatókönyvek  

### <a name="initializing-reference-data-on-an-edge-instance"></a>Az Edge-példányra vonatkozó hivatkozási adatinicializálás

Gyakori forgatókönyv, ahol a replikálás hasznos lehet, ha az Edge-példányt inicializálni kell olyan hivatkozási adatokkal, amelyek idővel változnak. Például, ha egy helyszíni SQL Server-példányon már betanított, a peremhálózati példányon egy ML-modellt frissít.

1. Hozzon létre egy tranzakciós replikációs kiadványt egy helyszíni SQL Server adatbázison.  
2. A helyszíni SQL Server az **új előfizetés varázsló** vagy a Transact-SQL-utasítások segítségével hozzon létre egy leküldéses előfizetést az Azure SQL Edge-be.  
3. A replikált adatbázis az Azure SQL Edge-ben inicializálható a pillanatkép-ügynök által generált pillanatkép használatával, valamint a terjesztési ügynök által terjesztett és továbbított, vagy az adatbázis a közzétevőről történő biztonsági másolatának használatával. Ha az adatbázis biztonsági másolata olyan objektumokat vagy szolgáltatásokat tartalmaz, amelyeket az Azure SQL Edge nem támogat, a visszaállítási művelet sikertelen lesz.

## <a name="limitations"></a>Korlátozások

Az Azure SQL Edge-előfizetések nem támogatják az alábbi beállításokat:

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
- FileStream, hierarchyid vagy térbeli adattípusok másolása.
- Hierarchyid konvertálása maximális adattípusra  
- Térbeli érték konvertálása maximális adattípusra  
- Kiterjesztett tulajdonságok másolása  
- Másolási engedélyek  

## <a name="examples"></a>Példák

Hozzon létre egy kiadványt és egy leküldéses előfizetést. További információkért lásd:
  
- [Kiadvány létrehozása](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Hozzon létre egy leküldéses előfizetést](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/) az Azure SQL Edge-kiszolgáló neve/IP-címe alapján előfizetőként (például **myEdgeinstance, 1433**) és egy adatbázisnevet az Azure SQL Edge-példányon célként megadott adatbázisként (például **AdventureWorks**).  

## <a name="see-also"></a>További információ  

- [Kiadvány létrehozása](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Leküldéses előfizetés létrehozása](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [A replikáció típusai](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Figyelés (replikálás)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Előfizetés inicializálása](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  



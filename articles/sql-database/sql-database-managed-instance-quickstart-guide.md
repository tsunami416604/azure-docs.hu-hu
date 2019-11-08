---
title: Gyors útmutató – felügyelt SQL-példány
description: Megtudhatja, hogyan kezdheti meg gyorsan a Azure SQL Database felügyelt példányát
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlr
ms.date: 07/11/2019
ms.openlocfilehash: 602de3e23eb5419958f84b071e2220550d1d04d0
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821722"
---
# <a name="getting-started-with-azure-sql-database-managed-instance"></a>Azure SQL Database felügyelt példány első lépései

A [felügyelt példány](sql-database-managed-instance-index.yml) központi telepítési lehetősége egy olyan adatbázist hoz létre, amely közel 100%-os kompatibilitással rendelkezik a legújabb SQL Server helyszíni (Enterprise Edition) adatbázis-motorral, amely egy natív [virtuális hálózati (VNet)](../virtual-network/virtual-networks-overview.md) implementációt biztosít, amely a cím gyakori biztonsági vonatkozások és [üzleti modellek](https://azure.microsoft.com/pricing/details/sql-database/) kedvezőek a helyszíni SQL Server ügyfelek számára. Ebből a cikkből megtudhatja, hogyan konfigurálhat és hozhat létre felügyelt példányt, és hogyan telepítheti át az adatbázisait.

## <a name="quickstart-overview"></a>A gyors üzembe helyezés áttekintése

A következő rövid útmutatók segítségével gyorsan létrehozhat egy felügyelt példányt, konfigurálhat egy virtuális gépet, vagy megadhatja az ügyfélalkalmazás VPN-kapcsolatát, és visszaállíthatja az adatbázist az új felügyelt példányra egy `.bak` fájl használatával.

### <a name="configure-environment"></a>A környezet konfigurálása

Első lépésként létre kell hoznia az első felügyelt példányt a hálózati környezettel, ahol a rendszer elhelyezi és engedélyezi a kapcsolódást a számítógép vagy a virtuális gép között, ahol a felügyelt példányra vonatkozó lekérdezéseket hajtja végre. A következő útmutatók használhatók:

- [Felügyelt példány létrehozása a Azure Portal használatával](sql-database-managed-instance-get-started.md). A Azure Portal a szükséges paramétereket (felhasználónév/jelszó, a magok számát és a maximális tárterületet) konfigurálja, és automatikusan létrehozza az Azure hálózati környezetét anélkül, hogy ismernie kellene a hálózatkezelés részleteit és az infrastruktúra követelményeit. Győződjön meg arról, hogy rendelkezik olyan [előfizetési típussal](sql-database-managed-instance-resource-limits.md#supported-subscription-types) , amely jelenleg jogosult felügyelt példány létrehozására. Ha van saját hálózata, amelyet használni szeretne, vagy testre szeretné szabni a hálózatot, tekintse meg a [meglévő virtuális hálózat konfigurálása Azure SQL Database felügyelt példányhoz](sql-database-managed-instance-configure-vnet-subnet.md) , vagy [hozzon létre egy virtuális hálózatot Azure SQL Database felügyelt példányhoz](sql-database-managed-instance-create-vnet-subnet.md).
- A felügyelt példányok a saját VNet, nyilvános végpont nélkül jönnek létre. Az ügyfélalkalmazások hozzáféréséhez **létrehozhat egy virtuális gépet ugyanabban a VNet (más alhálózatban)** , vagy **létrehozhat egy pont – hely típusú VPN-kapcsolatot az ügyfélszámítógép VNet** az alábbi rövid útmutatók egyikével:
  - A felügyelt példányon engedélyezze a [nyilvános végpontot](sql-database-managed-instance-public-endpoint-configure.md) , hogy közvetlenül a környezetből férhessen hozzá az adatokhoz.
  - Hozzon létre [Azure-beli virtuális gépet a felügyelt példányok VNet](sql-database-managed-instance-configure-vm.md) az ügyfél-alkalmazási kapcsolathoz, beleértve a SQL Server Management Studio.
  - Hozzon létre [pont – hely VPN-kapcsolatot a felügyelt példányhoz](sql-database-managed-instance-configure-p2s.md) az ügyfélszámítógépen, amelyen SQL Server Management Studio és más ügyfélkapcsolati alkalmazásokat. Ez a két lehetőség a felügyelt példányhoz és a saját VNet való kapcsolódáshoz.

  > [!NOTE]
  > A helyi hálózatról Express Route vagy helyek közötti kapcsolat is használható, de ezek a módszerek nem tartoznak a rövid útmutatók körébe.

A felügyelt példány manuális létrehozása helyett használhatja a [PowerShellt](scripts/sql-database-create-configure-managed-instance-powershell.md), [a PowerShellt Resource Manager-sablonnal](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md)vagy az [Azure CLI](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create) -vel a folyamat parancsfájlokhoz és automatizálásához.

### <a name="migrate-your-databases"></a>Adatbázisok migrálása

A felügyelt példány létrehozása és a hozzáférés konfigurálása után megkezdheti az adatbázisok áttelepítését SQL Server helyszíni vagy Azure-beli virtuális gépekről. Az áttelepítés meghiúsul, ha az áttelepíteni kívánt forrásadatbázis nem támogatott funkciói vannak. A hibák elkerüléséhez és a kompatibilitás ellenőrzéséhez telepítheti [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) szolgáltatást, amely az adatbázisokat a SQL Server elemzi, és megkeresi a felügyelt példányra való áttelepítést megakadályozó problémákat, például a [FileStream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) vagy a több naplófájl. Ha megoldja ezeket a problémákat, az adatbázisok készen állnak a felügyelt példányra való áttelepítésre. [Database Experimentation Assistant](https://blogs.msdn.microsoft.com/datamigration/2018/08/06/release-database-experimentation-assistant-dea-v2-6/) egy másik hasznos eszköz, amely rögzíti a számítási feladatokat SQL Server és visszajátszhatja azt egy felügyelt példányon, így megállapítható, hogy a felügyelt példányra való Migrálás során problémákba ütközik-e.

Ha biztos benne, hogy áttelepítheti az adatbázist egy felügyelt példányra, a natív SQL Server visszaállítási képességekkel állíthatja vissza az adatbázist egy felügyelt példányra egy `.bak` fájlból. Ezzel a módszerrel áttelepítheti az adatbázisokat a helyszíni vagy Azure-beli virtuális gépen telepített SQL Server-adatbázismotor használatával. A gyors útmutatóért lásd: [biztonsági másolat visszaállítása felügyelt példányra](sql-database-managed-instance-get-started-restore.md). Ebben a rövid útmutatóban egy, az Azure Blob Storage-ban tárolt `.bak` fájlból állítja vissza az `RESTORE` Transact-SQL parancs használatával.

> [!TIP]
> Ha a `BACKUP` Transact-SQL parancs használatával szeretne biztonsági másolatot készíteni az adatbázisról az Azure Blob Storage-ban, tekintse [meg a SQL Server biztonsági mentés URL-címére](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url)című témakört.

Ezek a rövid útmutatók lehetővé teszik az adatbázis biztonsági másolatának gyors létrehozását, konfigurálását és visszaállítását egy felügyelt példányra. Bizonyos esetekben testre kell szabnia vagy automatizálni a felügyelt példányok üzembe helyezését és a szükséges hálózati környezetet. A következő forgatókönyvek leírását alább találja.

## <a name="customize-network-environment"></a>Hálózati környezet testreszabása

Bár a VNet/alhálózat automatikusan konfigurálható, ha a példányt [a Azure Portal](sql-database-managed-instance-get-started.md)használatával hozza létre, érdemes lehet létrehozni a felügyelt példányok létrehozása előtt, mert beállíthatja a VNet és az alhálózat paramétereit. A hálózati környezet létrehozásának és konfigurálásának legegyszerűbb módja az [Azure Resource Deployment](sql-database-managed-instance-create-vnet-subnet.md) sablon használata, amely létrehozza és konfigurálja a hálózatot és az alhálózatot, ahol a példány el lesz helyezve. Csak a Azure Resource Manager üzembe helyezése gombot kell megnyomnia, és fel kell töltenie az űrlapot paraméterekkel.

Ezt a [PowerShell-szkriptet](https://www.powershellmagazine.com/2018/07/23/configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/) is használhatja a hálózat létrehozásának automatizálására.

Ha már rendelkezik egy VNet és alhálózattal, ahol telepíteni szeretné a felügyelt példányt, meg kell győződnie arról, hogy a VNet és az alhálózat megfelel a [hálózati követelményeknek](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Ezzel a [PowerShell-parancsfájllal ellenőrizheti, hogy az alhálózat megfelelően van-e konfigurálva](sql-database-managed-instance-configure-vnet-subnet.md). Ez a szkript érvényesíti a hálózatot, és jelentést készít az esetleges problémákról, és tájékoztatja arról, hogy mit kell módosítania, majd a VNet/alhálózatban elérhetővé tenni a szükséges módosításokat. Futtassa ezt a parancsfájlt, ha nem szeretné manuálisan konfigurálni a VNet/alhálózatot. A hálózati infrastruktúra bármely jelentős újrakonfigurálását követően is futtathatja azt. Ha saját hálózatot szeretne létrehozni és konfigurálni, olvassa el a [kapcsolati architektúra](sql-database-managed-instance-connectivity-architecture.md) és ez a [végső útmutató a felügyelt példányok környezetének létrehozásához és konfigurálásához](https://medium.com/azure-sqldb-managed-instance/the-ultimate-guide-for-creating-and-configuring-azure-sql-managed-instance-environment-91ff58c0be01)című témakört.

## <a name="migrate-to-a-managed-instance"></a>Migrálás felügyelt példányra

A rövid útmutatókban található cikkek segítségével gyorsan beállíthatja a felügyelt példányokat, és áthelyezheti az adatbázisokat a natív `RESTORE` képességgel. Ez jó kiindulási pont, ha gyors ellenőrzéseket szeretne végezni, és ellenőrizze, hogy a megoldás tud-e dolgozni a felügyelt példányon. 

Ahhoz azonban, hogy a termelési adatbázist vagy akár fejlesztési, illetve tesztelési adatbázisokat is át szeretne telepíteni az egyes teljesítménytesztekhez, érdemes megfontolnia néhány további módszer használatát, például a következőket:
- Teljesítményteszt – mérje fel az alapkonfigurációt a forrás SQL Server példányán, és hasonlítsa össze azokat a célként kezelt példány teljesítményével, ahol áttelepítette az adatbázist. További információ a [teljesítmény-összehasonlítással kapcsolatos ajánlott eljárásokról](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210).
- Online áttelepítés – a jelen cikkben ismertetett natív `RESTORE` meg kell várnia az adatbázisok visszaállítását (és az Azure Blob Storage-ba való másolását, ha ott még nem tárolja őket). Ez az alkalmazás bizonyos állásidőt okoz, különösen nagyobb adatbázisok esetén. Az éles adatbázis áthelyezéséhez használja az [adatáttelepítési szolgáltatást (DMS)](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance?toc=/azure/sql-database/toc.json) az adatbázis minimális állásidővel való áttelepítéséhez. A DMS ezt úgy hajtja végre, hogy a forrásadatbázis módosításait fokozatosan visszaküldi a felügyelt példány-adatbázisba. Így gyorsan válthat az alkalmazás forrásról a cél adatbázisára a minimális állásidővel.

További információ az [ajánlott áttelepítési folyamatról](sql-database-managed-instance-migrate.md).

## <a name="next-steps"></a>További lépések

- Tekintse meg a [felügyelt példány támogatott funkcióinak magas szintű listáját itt](sql-database-features.md) , valamint az [itt olvasható részleteket és ismert problémákat](sql-database-managed-instance-transact-sql-information.md).
- Ismerje meg a [felügyelt példány technikai jellemzőit](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).
- További speciális útmutató a [felügyelt példányok Azure SQL Database](sql-database-howto-managed-instance.md)-ben való használatához.
- [Azonosítsa a helyszíni adatbázis megfelelő Azure SQL Database/felügyelt példányának SKU-](/sql/dma/dma-sku-recommend-sql-db/)t.

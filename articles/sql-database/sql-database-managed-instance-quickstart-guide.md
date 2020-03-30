---
title: Gyorsútmutató – SQL felügyelt példány
description: Ismerje meg, hogyan kezdheti el gyorsan az Azure SQL Database szolgáltatást – felügyelt példány
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "73821722"
---
# <a name="getting-started-with-azure-sql-database-managed-instance"></a>Ismerkedés az Azure SQL Database-példányokkal

A [felügyelt példánytelepítési](sql-database-managed-instance-index.yml) beállítás létrehoz egy adatbázist, amely közel 100%-os kompatibilitást biztosít a legújabb SQL Server helyszíni (Enterprise Edition) adatbázis-motorral, és olyan natív [virtuális hálózati (VNet)](../virtual-network/virtual-networks-overview.md) megvalósítást biztosít, amely a gyakori biztonsági problémákat kezeli, valamint egy olyan [üzleti modellt,](https://azure.microsoft.com/pricing/details/sql-database/) amely kedvező a helyszíni SQL Server-ügyfelek számára. Ebből a cikkből megtudhatja, hogyan konfigurálhatja és hozhat létre gyorsan felügyelt példányt, és migrálhatja az adatbázisokat.

## <a name="quickstart-overview"></a>Gyors üzembe helyezés áttekintése

A következő rövid útmutatók lehetővé teszik egy felügyelt példány gyors létrehozását, egy virtuális gép vagy pont-hely VPN-kapcsolat konfigurálását az ügyfélalkalmazáshoz, és egy adatbázis visszaállítását az új felügyelt példányba egy `.bak` fájl használatával.

### <a name="configure-environment"></a>A környezet konfigurálása

Első lépésként létre kell hoznia az első felügyelt példányt azzal a hálózati környezettel, ahová el helyezi, és engedélyeznie kell a kapcsolatot attól a számítógéptől vagy virtuális géptől, amelyen lekérdezéseket hajt végre a felügyelt példányhoz. A következő útmutatókat használhatja:

- [Hozzon létre egy felügyelt példányt az Azure Portal használatával.](sql-database-managed-instance-get-started.md) Az Azure Portalon konfigurálja a szükséges paramétereket (felhasználónév/jelszó, magok száma és maximális tárolási összeg), és automatikusan létrehozza az Azure hálózati környezetet anélkül, hogy tudnia kellene a hálózati részletekről és az infrastruktúra-követelményekről. Csak győződjön meg arról, hogy rendelkezik egy [előfizetési típus,](sql-database-managed-instance-resource-limits.md#supported-subscription-types) amely jelenleg engedélyezett egy felügyelt példány létrehozása. Ha saját hálózattal rendelkezik, amelyet használni szeretne, vagy testre szeretné szabni a hálózatot, olvassa el [egy meglévő virtuális hálózat konfigurálása az Azure SQL Database felügyelt példányához,](sql-database-managed-instance-configure-vnet-subnet.md) vagy [hozzon létre egy virtuális hálózatot az Azure SQL Database felügyelt példányához.](sql-database-managed-instance-create-vnet-subnet.md)
- A felügyelt példány jön létre a saját virtuális hálózat nyilvános végpont nélkül. Az ügyfélalkalmazások eléréséhez **létrehozhat egy virtuális számítógépet ugyanabban a virtuális hálózatban (különböző alhálózaton),** vagy **létrehozhat egy pont-hely VPN-kapcsolatot a virtuális hálózattal az ügyfélszámítógépről az** alábbi rövid útmutatók egyikével:
  - Engedélyezze a [nyilvános végpontot](sql-database-managed-instance-public-endpoint-configure.md) a felügyelt példányon, hogy közvetlenül a környezetből férhessen hozzá az adatokhoz.
  - Hozzon létre [Azure Virtuális gépet a felügyelt példány virtuális hálózatában az](sql-database-managed-instance-configure-vm.md) ügyfélalkalmazás-kapcsolathoz, beleértve az SQL Server Management Studio-t is.
  - Állítsa be [a pont-hely VPN-kapcsolatot a felügyelt példányhoz](sql-database-managed-instance-configure-p2s.md) azügyfélszámítógépről, amelyen az SQL Server Management Studio és más ügyfélkapcsolati alkalmazások vannak. Ez a másik két lehetőség a felügyelt példány és a virtuális hálózat kapcsolatának.

  > [!NOTE]
  > A helyi hálózatról is használhat expressz útvonalat vagy helyek közötti kapcsolatot, de ezek a megközelítések nem tartoznak a rövid útmutatók hatálya alá.

A felügyelt példány manuális létrehozása alternatívájaként használhatja a [PowerShellt](scripts/sql-database-create-configure-managed-instance-powershell.md), a [PowerShellt az Erőforrás-kezelő sablonnal](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md)vagy [az Azure CLI-t](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create) a folyamat parancsfájlba futtatásához és automatizálásához.

### <a name="migrate-your-databases"></a>Az adatbázisok áttelepítése

Miután létrehozott egy felügyelt példányt, és konfigurálja a hozzáférést, megkezdheti az adatbázisok áttelepítését az SQL Server helyszíni vagy az Azure virtuális gépekről. Az áttelepítés sikertelen, ha a forrásadatbázisban van néhány nem támogatott szolgáltatás, amelyet át szeretne telepíteni. A hibák elkerülése és a kompatibilitás ellenőrzése érdekében telepítheti [az Adatáttelepítési segédet (DMA),](https://www.microsoft.com/download/details.aspx?id=53595) amely elemzi az adatbázisokat az SQL Server kiszolgálón, és megkeresi azokat a problémákat, amelyek blokkolhatják a felügyelt példányba való áttelepítést, például [a FileStream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) vagy több naplófájl meglétét. Ha megoldja ezeket a problémákat, az adatbázisok készen állnak a felügyelt példányra való áttelepítésre. [Az Adatbázis-kísérletezési segéd](https://blogs.msdn.microsoft.com/datamigration/2018/08/06/release-database-experimentation-assistant-dea-v2-6/) egy másik hasznos eszköz, amely rögzíti a számítási feladatokat az SQL Server en, és visszajátszhatja egy felügyelt példányon, így meghatározhatja, hogy teljesítményproblémák merülnek-e fel, ha felügyelt példányra költözik.

Miután biztos abban, hogy az adatbázisát egy felügyelt példányba telepítheti át, a natív SQL `.bak` Server-visszaállítási lehetőségek segítségével visszaállíthatja az adatbázist egy felügyelt példányba egy fájlból. Ezzel a módszerrel áttelepítheti az adatbázisokat a helyszínen telepített SQL Server adatbázis-motorból vagy az Azure VM-ből. Rövid útmutató: [Visszaállítás biztonsági mentésről felügyelt példányra.](sql-database-managed-instance-get-started-restore.md) Ebben a rövid útmutatóban `.bak` az Azure Blob storage-ban `RESTORE` tárolt fájlból a Transact-SQL paranccsal állítható vissza.

> [!TIP]
> Ha a `BACKUP` Transact-SQL paranccsal szeretne biztonsági másolatot készíteni az azure Blob storage-ban az adatbázisról, olvassa el az [SQL Server URL-címre való biztonsági mentéscímű témakörét.](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url)

Ezek a rövid útmutatók lehetővé teszik az adatbázis biztonsági mentésének gyors létrehozását, konfigurálását és visszaállítását egy felügyelt példányba. Bizonyos esetekben testre kell szabnia vagy automatizálnia kell a felügyelt példányok és a szükséges hálózati környezet üzembe helyezését. Ezeket a forgatókönyveket az alábbiakban ismertetjük.

## <a name="customize-network-environment"></a>Hálózati környezet testreszabása

Bár a virtuális hálózat/alhálózat automatikusan konfigurálható, amikor a példány az [Azure Portal](sql-database-managed-instance-get-started.md)használatával jön létre, érdemes lehet létrehozni a felügyelt példányok létrehozása előtt, mert konfigurálhatja a virtuális hálózat és az alhálózat paramétereit. A hálózati környezet létrehozásának és konfigurálásának legegyszerűbb módja az [Azure Resource telepítési](sql-database-managed-instance-create-vnet-subnet.md) sablon használata, amely létrehozza és konfigurálja a hálózatot és az alhálózatot, ahol a példány el lesz helyezve. Csak meg kell nyomnia az Azure Resource Manager üzembe helyezését gombot, és az űrlap feltöltése paraméterekkel.

Alternatív megoldásként is használhatja ezt a [PowerShell-parancsfájlt](https://www.powershellmagazine.com/2018/07/23/configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/) a hálózat létrehozásának automatizálásához.

Ha már rendelkezik egy virtuális hálózattal és alhálózattal, ahol telepíteni szeretné a felügyelt példányt, meg kell győződnie arról, hogy a virtuális hálózat és az alhálózat megfelel a [hálózati követelményeknek.](sql-database-managed-instance-connectivity-architecture.md#network-requirements) Ezzel a [PowerShell-parancsfájllal ellenőrizheti, hogy az alhálózat megfelelően van-e konfigurálva.](sql-database-managed-instance-configure-vnet-subnet.md) Ez a parancsfájl ellenőrzi a hálózatot, és jelentse a problémákat, és megmondja, hogy mit kell módosítani, és majd felajánlja, hogy a szükséges módosításokat a virtuális hálózat/alhálózat. Futtassa ezt a parancsfájlt, ha nem szeretné manuálisan konfigurálni a virtuális hálózatot/alhálózatot. A hálózati infrastruktúra bármely jelentős újrakonfigurálása után is futtathatja. Ha saját hálózatot szeretne létrehozni és konfigurálni, olvassa el a [kapcsolódási architektúrát](sql-database-managed-instance-connectivity-architecture.md) és ezt [a végső útmutatót a felügyelt példánykörnyezet létrehozásához és konfigurálásához.](https://medium.com/azure-sqldb-managed-instance/the-ultimate-guide-for-creating-and-configuring-azure-sql-managed-instance-environment-91ff58c0be01)

## <a name="migrate-to-a-managed-instance"></a>Áttelepítés felügyelt példányra

Az ezekben a rövid útmutatókban található cikkek lehetővé teszik `RESTORE` egy felügyelt példány gyors beállítását és az adatbázisok áthelyezését a natív képesség használatával. Ez egy jó kiindulási pont, ha szeretné befejezni a fogalmak gyors igazolását, és ellenőrizni szeretné, hogy a megoldás működhet-e a felügyelt példányon. 

Az éles adatbázis vagy akár a teljesítményteszthez használni kívánt fejlesztési/tesztelési adatbázisok áttelepítéséhez azonban érdemes megfontolnia néhány további módszer használatát, például a következőket:
- Teljesítménytesztelés – A forrás SQL Server-példány alapkonfigurációjának teljesítményét kell mérnie, és össze kell hasonlítania az adatbázis áttelepített célfelügyelt példányának teljesítményével. További információ a [teljesítmény-összehasonlítás ajánlott módszereiről.](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210)
- Online áttelepítés – `RESTORE` A jelen cikkben ismertetett natív, meg kell várnia az adatbázisok visszaállítását (és másolja az Azure Blob storage, ha még nem tárolja ott). Ez az alkalmazás bizonyos állásidejét okozza, különösen a nagyobb adatbázisok esetében. Az éles adatbázis áthelyezéséhez használja az [Adatáttelepítés szolgáltatást (DMS)](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance?toc=/azure/sql-database/toc.json) az adatbázis minimális állásidővel történő áttelepítéséhez. A DMS ezt úgy valósítja meg, hogy a forrásadatbázisban végrehajtott módosításokat fokozatosan lenyomja a visszaállítandó felügyelt példány-adatbázisba. Így gyorsan válthat az alkalmazás forrásról a céladatbázisra a minimális állásidővel.

További információ az [ajánlott áttelepítési folyamatról.](sql-database-managed-instance-migrate.md)

## <a name="next-steps"></a>További lépések

- Itt találja [a felügyelt példány támogatott szolgáltatásainak magas szintű listáját,](sql-database-features.md) valamint a [részleteket és az ismert problémákat itt.](sql-database-managed-instance-transact-sql-information.md)
- További információ [a felügyelt példány műszaki jellemzőiről.](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)
- Az [Azure SQL Database-ben](sql-database-howto-managed-instance.md)részletesebb útmutatót talál a felügyelt példányok használatáról.
- [Azonosítsa a megfelelő Azure SQL Database/felügyelt példány termékváltozatát a helyszíni adatbázishoz.](/sql/dma/dma-sku-recommend-sql-db/)

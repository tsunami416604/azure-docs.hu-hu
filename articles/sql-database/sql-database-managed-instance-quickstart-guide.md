---
title: Rövid útmutató – Azure SQL Database felügyelt példány |} A Microsoft Docs
description: 'Útmutató: Azure SQL Database – felügyelt példány használatának gyors megkezdéséhez'
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 77deed43c106a451d3de768989233c749e1280e1
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55468172"
---
# <a name="getting-started-with-azure-sql-database-managed-instance"></a>Ismerkedés az Azure SQL Database felügyelt példánya

[Az Azure SQL Database felügyelt példányain](sql-database-managed-instance-index.yml) teljes körűen felügyelt PaaS-verzióját az SQL Server Azure-felhőben lévő üzemeltetett, és a saját privát IP-címmel rendelkező virtuális hálózatban helyez. Ebben a szakaszban megtudhatja, gyorsan konfigurálása és a felügyelt példány létrehozása és az adatbázisokat.

## <a name="quickstart-overview"></a>A rövid útmutató – áttekintés

Ebben a szakaszban láthatja a cikkek, amelyek segítségével a felügyelt példány használatának gyors megkezdéséhez áttekintése. A az első felügyelt példány létrehozásának legegyszerűbb módja az, hogy használja [az Azure Portalon](sql-database-managed-instance-get-started.md) , amelyen konfigurálhatja a szükséges paramétereket (felhasználónév/jelszó, magok, a maximális tárolókapacitás), és automatikusan létrehozza az Azure hálózati környezet nélkül kell tudni hálózati részletei és infrastrukturális követelményei. Ügyeljen rá, hogy rendelkezik-e egy [előfizetés-típus](sql-database-managed-instance-resource-limits.md#supported-subscription-types) , amely jogosult a példány létrehozása.

Ha a saját hálózatot, amelyet használni szeretne, vagy testre szabhatja a hálózat esetén lásd: hogyan [konfigurálja a hálózati környezet](#configure-network-environment) felügyelt példány számára.

A felügyelt példány létrehozásakor meg kell kapcsolódjon a példányhoz a következő módszerek egyikével:

* Hozzon létre [Azure virtuális gép](sql-database-managed-instance-configure-vm.md) telepítve van az SQL Server Management Studio és más alkalmazásokhoz, amelyek a felügyelt példány alhálózatán, ahol a felügyelt példány kerül ugyanazon a Vneten belül eléréséhez használható. A felügyelt példányok ugyanazon az alhálózaton lévő virtuális gép nem lehet.
* Állítsa be a [pont – hely kapcsolat](sql-database-managed-instance-configure-p2s.md) a számítógépen, amely lehetővé teszi, hogy a számítógép csatlakozik a virtuális hálózathoz, ahol a felügyelt példány kerül, és a felügyelt példány és minden más SQL Server, a hálózaton.

Alternatív megoldásként a helyi hálózatról használhat express route vagy helyek közötti kapcsolat, de ezek a módszerek az alábbi rövid útmutatókkal a hatókörén kívül.

Felügyelt példány létrehozása és hozzáférés konfigurálásakor, indítsa el az adatbázisok helyezni helyszíni SQL Server- vagy Azure virtuális gépek migrálását. Vegye figyelembe, hogy az áttelepítés sikertelen lesz, ha a forrás-adatbázis, amely az áttelepíteni kívánt egyes nem támogatott funkciók. Sikertelen végrehajtásának elkerülése és kompatibilitás-ellenőrzés, telepíthet [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) , amely elemzi az adatbázisok SQL Server-kiszolgálón, és keresse meg, hogy nem blokkolja-e migrálás felügyelt példányra például létezik-e bármilyen probléma A FileStream vagy több naplófájlt. A problémák megoldásához, ha az adatbázisok fel egy felügyelt példányra. [Adatbázis-Kísérletezési Segéd](https://blogs.msdn.microsoft.com/datamigration/2018/08/06/release-database-experimentation-assistant-dea-v2-6/) egy másik hasznos eszköz, amely képes a számítási feladatok SQL Server és a visszajátszás, így megadhatja, hogy felügyelt példányon van gazdakiszolgálói lesznek okoznak teljesítményproblémákat, ha áttelepíti a felügyelt példány.

Ha biztos abban, hogy az adatbázis áttelepítheti egy felügyelt példányra, [natív VISSZAÁLLÍTÁSA](sql-database-managed-instance-get-started-restore.md) funkció, amely lehetővé teszi, hogy készítsen biztonsági másolatot az adatbázis a Transact-SQL paranccsal töltse fel az Azure blob storage és a Állítsa vissza az adatbázist a Transact-SQL-parancs használata blob storage-ból.

Ezen rövid útmutatók gyorsan konfigurálhatja, létrehozása és adatbázisok helyezi a felügyelt példányok lehetővé teszik. Bizonyos esetekben kell testreszabásához, vagy a felügyelt példány és a szükséges hálózati környezet üzembe helyezésének automatizálása. Ezek a forgatókönyvek alábbiakban lesz.

## <a name="customizing-network-environment"></a>Hálózati környezet testreszabása

Bár a virtuális hálózat/alhálózat automatikusan konfigurálható, ha a példány használatával hozható létre [az Azure Portalon](sql-database-managed-instance-get-started.md), érdemes lehet létrehozni, mielőtt kezdő felügyelt példányok létrehozása, mert konfigurálhatja úgy a virtuális hálózat a paraméterek helyes és alhálózatot. A létrehozása és konfigurálása a hálózati környezet legegyszerűbben használandó [Azure-erőforrások üzembe helyezésének](sql-database-managed-instance-create-vnet-subnet.md) létrehozása és konfigurálása, hálózatot és alhálózatot, ahol a példány kerül sablon. Csak meg kell nyomja le az Azure Resource Manager üzembe helyezése a gombot, és töltse fel az űrlap paraméterekkel. Alternatív megoldásként használható [PowerShell-parancsprogram](https://www.powershellmagazine.com/2018/07/23/configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/) automatizálásához a hálózat.

Ha már rendelkezik egy virtuális hálózatot és alhálózatot hova a felügyelt példány üzembe helyezéséhez, győződjön meg arról, hogy a virtuális hálózatot és alhálózatot felel meg, hogy kell [hálózati követelményeiben](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Ez használjon [PowerShell-parancsprogram győződjön meg arról, hogy az alhálózat megfelelően konfigurált](sql-database-managed-instance-configure-vnet-subnet.md). Ez a szkript nem csak a hálózat ellenőrzése, és nem a problémák – jelentése, megtudhatja, mit kell módosítani, és is kínálunk, hogy a szükséges módosításokat a virtuális hálózat/alhálózat. Ez a szkript futtatása, ha nem szeretné manuálisan konfigurálhatja a virtuális hálózat/alhálózat, és is kell futtatásakor, a hálózati infrastruktúra bármilyen jelentősebb újrakonfigurálás után. Ha azt szeretné létrehozni és konfigurálni, olvassa el a saját hálózati [felügyelt példányának dokumentációját](sql-database-managed-instance-connectivity-architecture.md) és [Ez az útmutató](https://medium.com/azure-sqldb-managed-instance/the-ultimate-guide-for-creating-and-configuring-azure-sql-managed-instance-environment-91ff58c0be01).

## <a name="automating-creation-of-managed-instance"></a>Felügyelt példány létrehozásának automatizálása

 Ha nem hozott létre a hálózati környezet, az előző lépésben leírtak szerint, az Azure Portalon teheti meg – az egyetlen hátránya az a tény, hogy azt fogja konfigurálni, néhány alapértelmezett paraméterek, amelyek később nem módosítható. Alternatív megoldásként használható [PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/), [PowerShell Resource Manager-sablonnal](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md), vagy [Azure CLI-vel](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/11/14/create-azure-sql-managed-instance-using-azure-cli/).

## <a name="migrating-to-managed-instance-with-minimal-downtime"></a>Felügyelt példányába történő migrálás minimális állásidővel

Ezen rövid útmutatók cikkeit lehetővé teszi, gyorsan állítsa be a felügyelt példány, és az adatbázisok áthelyezéséhez. Azonban az a natív visszaállítási vissza kell állítani az adatbázisok várnia kell, amely miatt némi állásidővel, az alkalmazás különösen akkor, ha az adatbázis nagyobb méretű. Az éles adatbázis áthelyezéséhez, valószínűleg kell áttelepíteni egy hatékony megoldást, amely garantálja a migrálás minimális állásidővel. [Data Migration service](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance?toc=/azure/sql-database/toc.json) migrálási szolgáltatás, amely az adatbázis a minimális állásidővel telepíthet át egy adatbázis a forrásadatbázis végzett módosításokat növekményes küldésével, hogy a rendszer a felügyelt példány visszaállítása. Így gyorsan válthat a minimális állásidővel céladatbázis forrásból alkalmazását.

## <a name="next-steps"></a>További lépések

* Keresse meg a [itt felügyelt példányon a támogatott funkciók listájának magas szintű](sql-database-features.md) és [részleteit és ismert problémák Itt](sql-database-managed-instance-transact-sql-information.md). 
* Ismerje meg [felügyelt példány műszaki jellemzők](sql-database-managed-instance-resource-limits.md#instance-level-resource-limits). 
* Oktatóanyagok a Speciális keresés [című](sql-database-howto-managed-instance.md). 

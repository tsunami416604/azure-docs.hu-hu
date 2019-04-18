---
title: Rövid útmutató – Azure SQL Database felügyelt példány |} A Microsoft Docs
description: 'Útmutató: Azure SQL Database – felügyelt példány használatának gyors megkezdéséhez'
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlr
manager: craigg
ms.date: 03/25/2019
ms.openlocfilehash: 739afe52403633b1a37f57f0005a85972cc78a39
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59360167"
---
# <a name="getting-started-with-azure-sql-database-managed-instance"></a>Ismerkedés az Azure SQL Database felügyelt példánya

A [felügyelt példány](sql-database-managed-instance-index.yml) üzembe helyezési lehetőség létrehoz egy adatbázist, a közel 100 %-os kompatibilitást a legújabb SQL Server helyi (Enterprise Edition) adatbázismotor, natív nyújtó [virtuális hálózat (VNet)](../virtual-network/virtual-networks-overview.md) végrehajtását, amelyek közös biztonsági kérdéseket, és a egy [üzleti modell](https://azure.microsoft.com/pricing/details/sql-database/) kedvező a helyszíni SQL Server-ügyfelek számára. Ebben a cikkben megtudhatja, gyorsan konfigurálása és a felügyelt példány létrehozása és az adatbázisokat.

## <a name="quickstart-overview"></a>A rövid útmutató – áttekintés

Az alábbi rövid útmutatókat, hogy gyorsan hozzon létre egy felügyelt példányt, a virtuális gép konfigurálása, vagy mutasson a használt ügyfélalkalmazás hely VPN-kapcsolat engedélyezése, és egy adatbázist az új felügyelt példány használatával egy `.bak` fájlt.

### <a name="configure-environment"></a>A környezet konfigurálása

Első lépésként kell a hálózati környezetet, ahol kerül, és a számítógép vagy virtuális gép, ahol futtatja egy felügyelt példányra lekérdezések-kapcsolat engedélyezése az első felügyelt példány létrehozása. A következő útmutatókat használhatja:

- [Az Azure portal használatával felügyelt példány létrehozása](sql-database-managed-instance-get-started.md). Az Azure Portalon konfigurálhatja a szükséges paramétereket (felhasználónév/jelszó, magok száma, és maximális tárterület), és automatikus létrehozása az Azure hálózati környezetben anélkül, hogy a hálózati részletei és infrastrukturális követelményei ismernie kellene. Ön csak győződjön meg arról, hogy rendelkezik-e egy [előfizetés-típus](sql-database-managed-instance-resource-limits.md#supported-subscription-types) , amely a felügyelt példány létrehozása jelenleg engedélyezett. Ha a saját hálózatot, amelyet használni szeretne, vagy testre szabhatja a hálózaton, tekintse meg szeretné [konfigurálása egy meglévő virtuális hálózatot az Azure SQL Database felügyelt példány](sql-database-managed-instance-configure-vnet-subnet.md) vagy [virtuális hálózat létrehozása az Azure SQL Database felügyelt példány](sql-database-managed-instance-create-vnet-subnet.md).
- Saját virtuális hálózat nem nyilvános végponttal rendelkező felügyelt példány létrejön. Ügyféloldali alkalmazás-hozzáférési választhatja **virtuális gép létrehozása ugyanazon a virtuális hálózaton (másik alhálózat)** vagy **a virtuális hálózathoz pont – hely VPN-kapcsolatot az ügyfélszámítógépről hozzon létre** egyikének használatával gyorsútmutatók:

  - Hozzon létre [Azure virtuális gépet a felügyelt példány VNet](sql-database-managed-instance-configure-vm.md) ügyfélkapcsolat-alkalmazást, beleértve az SQL Server Management Studio.
  - Állítsa be a [pont – hely VPN-kapcsolat a felügyelt példány](sql-database-managed-instance-configure-p2s.md) az ügyfélszámítógépről, amelyen az SQL Server Management Studio és az egyéb ügyfélalkalmazások kapcsolat van. Ez a másik két lehetőség közül választhat a felügyelt példány, és a virtuális hálózatok közötti kapcsolat.

  > [!NOTE]
  > Is használhatja express route vagy helyek közötti kapcsolat a helyi hálózatról, de ezek a módszerek az alábbi rövid útmutatókkal a hatókörén kívül.

### <a name="migrate-your-databases"></a>Az adatbázisok migrálása

Felügyelt példány létrehozása és konfigurálása a hozzáférés, után indítsa el az adatbázisok migrálását a helyszíni SQL Server-vagy Azure virtuális gépek. Áttelepítés meghiúsul, ha a forrás-adatbázis, amely az áttelepíteni kívánt egyes nem támogatott funkciók. Sikertelen végrehajtásának elkerülése és kompatibilitás-ellenőrzés, telepíthet [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) , amely elemzi az adatbázisok SQL Server-kiszolgálón, és talál meg minden hibája, hogy nem blokkolja-e migrálás felügyelt példányra, például a megléte[FileStream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) vagy több naplófájlt. A problémák megoldásához, ha az adatbázisok migrálás felügyelt példányra készen áll. [Adatbázis-Kísérletezési Segéd](https://blogs.msdn.microsoft.com/datamigration/2018/08/06/release-database-experimentation-assistant-dea-v2-6/) egy másik hasznos eszköz, amely képes a számítási feladatok SQL Server és a visszajátszás egy felügyelt példányon, így megadhatja, hogy lesznek a későbbiekben okoznak teljesítményproblémákat lehet, ha áttelepíti a felügyelt példány.

Ha biztos abban, hogy az adatbázis áttelepítheti egy felügyelt példányra, használhatja a natív SQL Server-visszaállítási funkciókkal adatbázis visszaállítása egy felügyelt példány, egy `.bak` fájlt. Ez a módszer segítségével adatbázisok migrálása SQL Server database engine telepítve van a helyszíni vagy Azure virtuális Gépen. A rövid útmutatóban talál [visszaállítása biztonsági másolatból egy felügyelt példányra](sql-database-managed-instance-get-started-restore.md). A visszaállítás ebben a rövid útmutatóban egy `.bak` Azure Blob storage használatával tárolt fájlt a `RESTORE` Transact-SQL-parancsot.

> [!TIP]
> Használatához a `BACKUP` Transact-SQL-parancsot az adatbázis biztonsági másolatának létrehozása az Azure Blob storage, lásd: [SQL Server biztonsági mentés URL-címre](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url).

Ezen rövid útmutatók lehetővé teszik a gyors létrehozása, konfigurálása és adatbázis biztonsági másolatának visszaállítása felügyelt példányra. Bizonyos esetekben kell testreszabásához, vagy a felügyelt példányok üzembe helyezés és a szükséges hálózati környezetének automatizálása. Ezek a forgatókönyvek alábbiakban lesz.

## <a name="customize-network-environment"></a>Hálózati környezet testreszabása

Bár a virtuális hálózat/alhálózat automatikusan konfigurálható, ha a példány használatával hozható létre [az Azure Portalon](sql-database-managed-instance-get-started.md), érdemes lehet létrehozni, mielőtt kezdő felügyelt példányok létrehozása, mert konfigurálhatja úgy a virtuális hálózat a paraméterek helyes és alhálózatot. A létrehozása és konfigurálása a hálózati környezet legegyszerűbben használandó [Azure-erőforrások üzembe helyezésének](sql-database-managed-instance-create-vnet-subnet.md) létrehozása és konfigurálása, hálózatot és alhálózatot, ahol a példány kerül sablon. Csak meg kell nyomja le az Azure Resource Manager üzembe helyezése a gombot, és töltse fel az űrlap paraméterekkel.

Alternatív megoldásként használható [PowerShell-parancsprogram](https://www.powershellmagazine.com/20../../configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/) automatizálásához a hálózat.

Alternatív megoldásként is ezzel [PowerShell-parancsprogram](https://www.powershellmagazine.com/2018/07/23/configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/) automatizálásához a hálózat.

Ha már rendelkezik egy virtuális hálózatot és alhálózatot hova a felügyelt példány üzembe helyezéséhez, szeretné-e győződjön meg arról, hogy a virtuális hálózatot és alhálózatot felel meg a [hálózati követelményeiben](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Ezzel [PowerShell-parancsprogram győződjön meg arról, hogy az alhálózat megfelelően konfigurált](sql-database-managed-instance-configure-vnet-subnet.md). A parancsfájl ellenőrzi a hálózati és a problémákat, és azt jelzi, hogy mit kell módosítani a jelentés, majd a szükséges módosításokat a virtuális hálózat/alhálózat ajánlatokat. Ez a szkript futtatása, ha nem szeretné manuálisan konfigurálhatja a virtuális hálózat/alhálózat. Is futtathatja azt a hálózati infrastruktúra bármilyen jelentősebb újrakonfigurálás után. Ha azt szeretné, létrehozása és konfigurálása saját hálózatához, olvassa el a [kapcsolati architektúra](sql-database-managed-instance-connectivity-architecture.md) , és ez[létrehozásához és konfigurálásához a felügyelt példány környezet teljes körű útmutatót](https://medium.com/azure-sqldb-managed-instance/the-ultimate-guide-for-creating-and-configuring-azure-sql-managed-instance-environment-91ff58c0be01).

## <a name="automating-creation-of-a-managed-instance"></a>Felügyelt példány létrehozásának automatizálása

 Ha nem hozott létre a hálózati környezet, az előző lépésben leírtak szerint, az Azure Portalon teheti meg – az egyetlen hátránya az a tény, hogy azt konfigurálja, néhány alapértelmezett paraméterekkel, hogy később nem módosítható. Alternatív megoldásként használhatja:

- [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md)
- [PowerShell használata a Resource Manager-sablon](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md)
- [Azure parancssori felület (CLI)](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/11/14/create-azure-sql-managed-instance-using-azure-cli/).
- [Resource Manager-sablon](sql-database-single-database-get-started-template.md)

## <a name="migrating-to-a-managed-instance-with-minimal-downtime"></a>Minimális állásidővel egy felügyelt példányába történő migrálás

Ezen rövid útmutatók cikkeit engedélyezése, hogy gyorsan állítsa be a felügyelt példány, és helyezze át az adatbázisait a natív `RESTORE` képesség. A natív segítségével azonban `RESTORE`, meg kell várnia az adatbázisokat kell visszaállítani (és az Azure Blob storage-be másolni ha nem már az ott tárolt). Ez azt eredményezi, hogy az alkalmazás némi állásidővel, különösen a nagyobb adatbázisok esetében. Az éles adatbázis áthelyezéséhez használja a [Data Migration service (DMS)](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance?toc=/azure/sql-database/toc.json) telepíthet át az adatbázist a minimális állásidővel. A DMS úgy hajtja ezt végre növekményes leküldése a felügyelt példány éppen visszaállított adatbázishoz a forrásadatbázisban végzett módosításokat. Így gyorsan válthat a minimális állásidővel céladatbázis forrásból alkalmazását.

## <a name="next-steps"></a>További lépések

- Keresse meg a [Itt a felügyelt példány a támogatott funkciók listáját magas szintű](sql-database-features.md) és [részleteit és ismert problémák Itt](sql-database-managed-instance-transact-sql-information.md).
- Ismerje meg [felügyelt példány műszaki jellemzők](sql-database-managed-instance-resource-limits.md#instance-level-resource-limits).
- Hogyan speciális keresés-a-k [használata Azure SQL Database felügyelt példány](sql-database-howto-managed-instance.md).

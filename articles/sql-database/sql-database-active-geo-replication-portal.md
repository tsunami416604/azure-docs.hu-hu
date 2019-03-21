---
title: 'Az Azure Portalon: Az SQL Database georeplikációja |} A Microsoft Docs'
description: Egy önálló vagy készletezett adatbázis georeplikáció konfigurálása az Azure SQL Database használatával az Azure portal és a feladatátvétel kezdeményezése
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 02/13/2019
ms.openlocfilehash: 2e63c44db2391f63078f0945caa69a43c0c464cf
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58001366"
---
# <a name="configure-active-geo-replication-for-azure-sql-database-in-the-azure-portal-and-initiate-failover"></a>Aktív georeplikáció konfigurálása az Azure SQL Database az Azure portal és a feladatátvétel kezdeményezése

Ez a cikk bemutatja, hogyan konfigurálhatja [aktív georeplikáció egyetlen vagy készletezett adatbázisok](sql-database-active-geo-replication.md#active-geo-replication-terminology-and-capabilities) az Azure SQL Database-adatbázishoz a [az Azure portal](https://portal.azure.com) és, hogy feladatátvételt kezdeményezzen.

Egyetlen vagy készletezett adatbázisok automatikus feladatátvételi csoportok kapcsolatos információkért lásd: [ajánlott eljárások a feladatátvételi csoportok használatával egyetlen vagy készletezett adatbázisok](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools). Felügyelt példány (előzetes verzió) az automatikus feladatátvételi csoportok kapcsolatos információkért lásd: [ajánlott eljárások a feladatátvételi csoportok használata a felügyelt példányok](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-managed-instances).

## <a name="prerequisites"></a>Előfeltételek

Aktív georeplikáció konfigurálása az Azure portal használatával, a következő erőforrás van szükség:

* Az Azure SQL database: Az elsődleges adatbázis, amely egy másik földrajzi régióba replikálni szeretne.

> [!Note]
> Az Azure portál használata esetén csak az elsődleges, ugyanazon az előfizetésen belül egy másodlagos adatbázist hozhat létre. Ha a másodlagos adatbázis szükséges lehet egy másik előfizetésben található, használja a [adatbázis REST API létrehozása](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) vagy [az ALTER DATABASE Transact-SQL API](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql).

## <a name="add-a-secondary-database"></a>Egy másodlagos adatbázis hozzáadása

Az alábbi lépéseket egy új másodlagos adatbázis georeplikációs partnerséget létrehozni.  

Szeretne hozzáadni egy másodlagos adatbázist, az előfizetés tulajdonosa vagy társtulajdonosa kell lennie.

A másodlagos adatbázis neve megegyezik az elsődleges adatbázissal rendelkezik, és alapértelmezés szerint azt a csomagot, és a számítási méret ugyanazt a szolgáltatást. A másodlagos adatbázis egy önálló adatbázis és a egy készletezett adatbázis is lehetnek. További információkért lásd: [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md) és [Virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md).
Miután létrehozott és áttöltésekor a másodlagos, elindul az adatok az új másodlagos adatbázis replikálása az elsődleges adatbázisból.

> [!NOTE]
> Ha a partner adatbázis már létezik (például eredményeként egy előző georeplikációs kapcsolatban megszakítást okozó) a parancs sikertelen lesz.

1. Az a [az Azure portal](https://portal.azure.com), tallózással keresse meg az adatbázis, amelyet szeretne georeplikáció beállítása.
2. Az SQL database oldalon válassza ki a **georeplikációs**, majd válassza ki a régiót, a másodlagos adatbázis létrehozásához. Választhat bármelyik régióban más, a régiót az elsődleges adatbázis üzemeltetéséhez, de javasoljuk, hogy a [párosított régióra](../best-practices-availability-paired-regions.md).

    ![Aktív georeplikáció konfigurálása](./media/sql-database-geo-replication-portal/configure-geo-replication.png)
3. Válassza ki, vagy a kiszolgáló és a másodlagos adatbázis tarifacsomagjának beállításához.

    ![Másodlagos konfigurálása](./media/sql-database-geo-replication-portal/create-secondary.png)
4. Igény szerint hozzáadhat egy másodlagos adatbázist egy rugalmas készlet. A másodlagos adatbázis a készlet létrehozásához kattintson a **rugalmas készlet** , és válassza ki a készlet a célkiszolgálón. Egy készlet már léteznie kell a célkiszolgálón. Ez a munkafolyamat nem hoz létre egy készletet.
5. Kattintson a **létrehozás** a másodlagos hozzáadásához.
6. A másodlagos adatbázis jön létre, és a kiindulási megkezdődik.

    ![Másodlagos konfigurálása](./media/sql-database-geo-replication-portal/seeding0.png)
7. A kiindulási folyamat befejeződése után a másodlagos adatbázis állapotát jeleníti meg.

    ![Teljes beültetés](./media/sql-database-geo-replication-portal/seeding-complete.png)

## <a name="initiate-a-failover"></a>Kezdeményezzen feladatátvételt

A másodlagos adatbázist elsődlegessé mostantól átválthat.  

1. Az a [az Azure portal](https://portal.azure.com), tallózással keresse meg az elsődleges adatbázis georeplikációs partnerségben.
2. Az SQL Database panelen válassza ki a **minden beállítás** > **georeplikációs**.
3. Az a **másodlagos példány hozható létre** listájához, válassza ki az adatbázist az új elsődleges válnak, és kattintson a kívánt **feladatátvételi**.

    ![feladatátvétel](./media/sql-database-geo-replication-failover-portal/secondaries.png)
4. Kattintson a **Igen** , a feladatátvételi művelet elindításához.

A parancs azonnal átvált a másodlagos adatbázis, az elsődleges szerepre.

Egy rövid ideig, mely során nem érhető el (0 – 25 másodpercig) sorrendje lesz, amíg a szerepkörök bekapcsolt állapotban van. Ha az elsődleges adatbázis több másodlagos adatbázis, a parancs automatikusan újrakonfigurálja a többi másodlagos példány hozható létre az új elsődleges csatlakozni. A teljes művelet végrehajtásához a normál körülmények között kevesebb mint egy percet vesz igénybe.

> [!NOTE]
> Ez a parancs az adatbázis egy esetleges leállás gyors helyreállítás lett tervezve. Feladatátvételi elindítja az adatok szinkronizálásának (kényszerített feladatátvétel) nélkül.  Ha az elsődleges online állapotban, és a tranzakciók véglegesítése, ha a parancs kiadása adatvesztés fordulhat elő.

## <a name="remove-secondary-database"></a>Másodlagos adatbázis eltávolítása

Ez a művelet véglegesen leáll a replikálás a másodlagos adatbázishoz, illetve a szerepkör a másodlagos módosításokat egy rendszeres írható és olvasható adatbázis. A másodlagos adatbázis-kapcsolata megszakad, ha a parancs végrehajtása sikeres, de nem válik az olvasási és írási csatlakozási után a másodlagos nem helyreáll.  

1. Az a [az Azure portal](https://portal.azure.com), tallózással keresse meg az elsődleges adatbázis georeplikációs partnerségben.
2. Az SQL database oldalon válassza ki a **georeplikációs**.
3. Az a **másodlagos példány hozható létre** listában, válassza ki a el kívánja távolítani a georeplikáció partnerség az adatbázist.
4. Kattintson a **Replikációleállítás**.

    ![Távolítsa el a másodlagos](./media/sql-database-geo-replication-portal/remove-secondary.png)
5. A megerősítési ablakban nyílik meg. Kattintson a **Igen** a georeplikáció partnerség az adatbázis eltávolítása. (Állítsa írható-olvasható adatbázis nem része semmilyen replikációs.)

## <a name="next-steps"></a>További lépések

* Aktív georeplikáció kapcsolatos további információkért lásd: [aktív georeplikáció](sql-database-active-geo-replication.md).
* Automatikus feladatátvételi csoportok kapcsolatos további információkért lásd: [automatikus feladatátvételi csoportok](sql-database-auto-failover-group.md)
* Egy üzleti folytonosság – áttekintés és forgatókönyvek: [üzleti folytonosság – áttekintés](sql-database-business-continuity.md).

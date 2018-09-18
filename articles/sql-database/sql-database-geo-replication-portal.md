---
title: 'Az Azure portal: SQL Database georeplikációja |} A Microsoft Docs'
description: Georeplikáció konfigurálása az Azure SQL Database az Azure Portalon és kezdeményezhet feladatátvételt
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: ddd0fa21cebb6a6a25965b6d5e49b04cfdcc0466
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2018
ms.locfileid: "45737005"
---
# <a name="configure-active-geo-replication-for-azure-sql-database-in-the-azure-portal-and-initiate-failover"></a>Aktív georeplikáció konfigurálása az Azure SQL Database az Azure portal és a feladatátvétel kezdeményezése

Ez a cikk bemutatja, hogyan aktív georeplikáció konfigurálása az SQL Database-be a [az Azure portal](http://portal.azure.com) és, hogy feladatátvételt kezdeményezzen.

Kezdeményezzen feladatátvételt az Azure portal használatával, lásd: [tervezett vagy nem tervezett feladatátvételt kezdeményezni az Azure SQL Database és az Azure portal](sql-database-geo-replication-portal.md).

Aktív georeplikáció konfigurálása az Azure portal használatával, a következő erőforrás van szükség:

* Az Azure SQL database: az elsődleges adatbázis, amely egy másik földrajzi régióba replikálni szeretne.

> [!Note]
Aktív georeplikáció azonos előfizetésben található adatbázisok között kell lennie.

## <a name="add-a-secondary-database"></a>Egy másodlagos adatbázis hozzáadása
Az alábbi lépéseket egy új másodlagos adatbázis georeplikációs partnerséget létrehozni.  

Szeretne hozzáadni egy másodlagos adatbázist, az előfizetés tulajdonosa vagy társtulajdonosa kell lennie.

A másodlagos adatbázis neve megegyezik az elsődleges adatbázissal rendelkezik, és alapértelmezés szerint azt a csomagot, és a számítási méret ugyanazt a szolgáltatást. A másodlagos adatbázis egy önálló adatbázis és a egy adatbázist a rugalmas készlet is lehetnek. További információkért lásd: [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md) és [Virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md).
Miután létrehozott és áttöltésekor a másodlagos, elindul az adatok az új másodlagos adatbázis replikálása az elsődleges adatbázisból.

> [!NOTE]
> Ha a partner adatbázis már létezik (például eredményeként egy előző georeplikációs kapcsolatban megszakítást okozó) a parancs sikertelen lesz.
> 

1. Az a [az Azure portal](http://portal.azure.com), tallózással keresse meg az adatbázis, amelyet szeretne georeplikáció beállítása.
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

1. Az a [az Azure portal](http://portal.azure.com), tallózással keresse meg az elsődleges adatbázis georeplikációs partnerségben.
2. Az SQL Database panelen válassza ki a **minden beállítás** > **georeplikációs**.
3. Az a **másodlagos példány hozható létre** listájához, válassza ki az adatbázist az új elsődleges válnak, és kattintson a kívánt **feladatátvételi**.
   
    ![Feladatátvétel](./media/sql-database-geo-replication-failover-portal/secondaries.png)
4. Kattintson a **Igen** , a feladatátvételi művelet elindításához.

A parancs azonnal átvált a másodlagos adatbázis, az elsődleges szerepre. 

Egy rövid ideig, mely során nem érhető el (0 – 25 másodpercig) sorrendje lesz, amíg a szerepkörök bekapcsolt állapotban van. Ha az elsődleges adatbázis több másodlagos adatbázis, a parancs automatikusan újrakonfigurálja a többi másodlagos példány hozható létre az új elsődleges csatlakozni. A teljes művelet végrehajtásához a normál körülmények között kevesebb mint egy percet vesz igénybe. 

> [!NOTE]
> Ez a parancs az adatbázis egy esetleges leállás gyors helyreállítás lett tervezve. Feladatátvételi elindítja az adatok szinkronizálásának (kényszerített feladatátvétel) nélkül.  Ha az elsődleges online állapotban, és a tranzakciók véglegesítése, ha a parancs kiadása adatvesztés fordulhat elő. 
> 
> 

## <a name="remove-secondary-database"></a>Másodlagos adatbázis eltávolítása
Ez a művelet véglegesen leáll a replikálás a másodlagos adatbázishoz, illetve a szerepkör a másodlagos módosításokat egy rendszeres írható és olvasható adatbázis. A másodlagos adatbázis-kapcsolata megszakad, ha a parancs végrehajtása sikeres, de nem válik az olvasási és írási csatlakozási után a másodlagos nem helyreáll.  

1. Az a [az Azure portal](http://portal.azure.com), tallózással keresse meg az elsődleges adatbázis georeplikációs partnerségben.
2. Az SQL database oldalon válassza ki a **georeplikációs**.
3. Az a **másodlagos példány hozható létre** listában, válassza ki a el kívánja távolítani a georeplikáció partnerség az adatbázist.
4. Kattintson a **Replikációleállítás**.
   
    ![Távolítsa el a másodlagos](./media/sql-database-geo-replication-portal/remove-secondary.png)
5. A megerősítési ablakban nyílik meg. Kattintson a **Igen** a georeplikáció partnerség az adatbázis eltávolítása. (Állítsa írható-olvasható adatbázis nem része semmilyen replikációs.)

## <a name="next-steps"></a>További lépések
* Aktív georeplikáció kapcsolatos további információkért lásd: [aktív georeplikáció](sql-database-geo-replication-overview.md).
* Egy üzleti folytonosság – áttekintés és forgatókönyvek: [üzleti folytonosság – áttekintés](sql-database-business-continuity.md).


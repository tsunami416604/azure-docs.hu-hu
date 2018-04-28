---
title: 'Azure-portálon: SQL Database georeplikációja |} Microsoft Docs'
description: A georeplikáció konfigurálása az Azure SQL Database az Azure-portálon és kezdeményezhet feladatátvételi
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: article
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: 89839d18e90a75f81e78edcb7f54e77a31ea4886
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="configure-active-geo-replication-for-azure-sql-database-in-the-azure-portal-and-initiate-failover"></a>Aktív georeplikáció konfigurálása az Azure SQL Database az Azure-portálon és kezdeményezhet feladatátvételi

Ez a cikk bemutatja, hogyan aktív georeplikáció konfigurálja az SQL-adatbázis a [Azure-portálon](http://portal.azure.com) és a feladatátvétel kezdeményezése.

Kezdeményezze a feladatátvételt az Azure portálon, lásd: [kezdeményezzen tervezett vagy nem tervezett feladatátvételt az Azure SQL Database és az Azure portál](sql-database-geo-replication-portal.md).

Aktív georeplikáció konfigurálása az Azure-portál használatával, a következő erőforrás van szükség:

* Azure SQL-adatbázis: az elsődleges adatbázis egy másik földrajzi régióban replikálni kívánt.

> [!Note]
Aktív georeplikáció ugyanahhoz az előfizetéshez adatbázisok között kell lennie.

## <a name="add-a-secondary-database"></a>A másodlagos adatbázis hozzáadása
Az alábbi lépéseket a georeplikáció együttműködve hozzon létre egy új másodlagos adatbázis.  

Vegye fel a másodlagos adatbázist, az előfizetés tulajdonosa vagy a társtulajdonos kell lennie.

A másodlagos adatbázis ugyanaz a neve, mint az elsődleges adatbázissal, és alapértelmezés szerint rendelkezik ugyanazt a szolgáltatási szint. A másodlagos adatbázis egy adatbázis vagy a rugalmas készletekben található adatbázis lehet. További információkért lásd: [alapjául szolgáló vásárlási modell DTU-alapú](sql-database-service-tiers-dtu.md) és [vCore-alapú alapjául szolgáló vásárlási modell (előzetes verzió)](sql-database-service-tiers-vcore.md).
Miután a másodlagos létrehozták, és a rendezés, adatok kezdődik, az új másodlagos adatbázis replikálása az elsődleges adatbázisból.

> [!NOTE]
> A parancs futása sikertelen, ha a partner adatbázis már létezik (például miatt leállítja egy előző georeplikáció kapcsolat).
> 

1. Az a [Azure-portálon](http://portal.azure.com), keresse meg az adatbázis, amelyet a georeplikációért beállítása.
2. Az SQL-adatbázis oldalon válassza ki a **georeplikáció**, majd válassza ki a régiót, a másodlagos adatbázis létrehozásához. Kiválaszthatja a régió eltérő a régió, az elsődleges adatbázist üzemeltető, de ajánlott a [párosított régió](../best-practices-availability-paired-regions.md).
   
    ![Aktív georeplikáció konfigurálása](./media/sql-database-geo-replication-portal/configure-geo-replication.png)
3. Válasszon, vagy adja meg a kiszolgáló és a másodlagos adatbázis tarifacsomagjának.
   
    ![Másodlagos konfigurálása](./media/sql-database-geo-replication-portal/create-secondary.png)
4. Opcionálisan hozzáadhat egy másodlagos adatbázis rugalmas készlethez. A másodlagos adatbázis létrehozása a készletben, kattintson a **rugalmas készlet** , és válasszon ki egy készletet, a célkiszolgálón. Egy készlet már léteznie kell a célkiszolgálón. Ez a munkafolyamat nem hoz létre a készletet.
5. Kattintson a **létrehozása** hozzáadása a másodlagos.
6. A másodlagos adatbázis jön létre, és az index-összehangolási folyamat megkezdése.
   
    ![Másodlagos konfigurálása](./media/sql-database-geo-replication-portal/seeding0.png)
7. Ha az index-összehangolási folyamat befejeződik, a másodlagos adatbázis állapotát jeleníti meg.
   
    ![Teljes összehangolása](./media/sql-database-geo-replication-portal/seeding-complete.png)

## <a name="initiate-a-failover"></a>Kezdeményezzen feladatátvételt

A másodlagos adatbázist már kapcsolható legyen, az elsődleges.  

1. Az a [Azure-portálon](http://portal.azure.com), keresse meg az elsődleges adatbázis a georeplikáció együttműködve.
2. SQL-adatbázis paneljén válassza **összes beállítás** > **georeplikáció**.
3. Az a **másodlagos** listára, válassza ki az adatbázist az új elsődleges válik, és kattintson a kívánt **feladatátvételi**.
   
    ![feladatátvétel](./media/sql-database-geo-replication-failover-portal/secondaries.png)
4. Kattintson a **Igen** megkezdéséhez a feladatátvételt.

A parancs azonnal vált, a másodlagos adatbázist az elsődleges szerepkör. 

Egy rövid időre időintervalluma (terabájtok rendelése 0-25 másodpercig) nem érhető el, amíg a szerepkörök bekapcsolt állapotban van. Ha az elsődleges adatbázis több másodlagos adatbázist, a parancs automatikusan újrakonfigurálja a más másodlagos adatbázist az új elsődleges való kapcsolódáshoz. A teljes műveletet kell végrehajtani egy percen belül befejeződik normál körülmények között. 

> [!NOTE]
> Ez a parancs a gyors helyreállítás az adatbázis egy esetleges leállás esetén szolgál. Elindítja a feladatátvétel (kényszerített feladatátvételi) adatok szinkronizálás nélkül.  Ha az elsődleges online állapotban, és a tranzakciók végrehajtása, ha a parancs kiadása adatvesztést fordulhat elő. 
> 
> 

## <a name="remove-secondary-database"></a>Távolítsa el a másodlagos adatbázis
Ez a művelet végleg leáll a replikálás, a másodlagos adatbázishoz, és a szerepkör a másodlagos vált rendszeres írható-olvasható adatbázis. Ha a másodlagos adatbázis-kapcsolata megszakad, a parancs végrehajtása sikeres, de a másodlagos nem írható-olvasható kapcsolat után nem lett helyreáll.  

1. Az a [Azure-portálon](http://portal.azure.com), keresse meg az elsődleges adatbázis a georeplikáció együttműködve.
2. Az SQL-adatbázis oldalon válassza ki a **georeplikáció**.
3. Az a **másodlagos** listára, válassza ki a georeplikáció partnerség eltávolítása kívánt adatbázist.
4. Kattintson a **állítsa le a replikációt**.
   
    ![Távolítsa el a másodlagos](./media/sql-database-geo-replication-portal/remove-secondary.png)
5. Megnyílik egy ablak. Kattintson a **Igen** az adatbázis eltávolítása a georeplikáció partneri kapcsolat áll fenn. (Állítsa írható-olvasható adatbázis nem része semmilyen replikációs.)

## <a name="next-steps"></a>További lépések
* Aktív georeplikáció kapcsolatos további információkért lásd: [aktív georeplikáció](sql-database-geo-replication-overview.md).
* Egy üzleti folytonosság – áttekintés és forgatókönyvek: [üzleti folytonosság – áttekintés](sql-database-business-continuity.md).


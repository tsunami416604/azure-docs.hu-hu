---
title: 'Oktatóanyag: Georeplikációs & feladatátvétel a portálon'
description: Konfigurálja a georeplikációt egy vagy egyesített adatbázishoz az Azure SQL Database-ben az Azure Portal használatával, és kezdeményezze a feladatátvételt.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 02/13/2019
ms.openlocfilehash: 601c537a51e29ae1f107127e1b83c07448eee9ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256431"
---
# <a name="configure-active-geo-replication-for-azure-sql-database-in-the-azure-portal-and-initiate-failover"></a>Aktív georeplikáció konfigurálása az Azure SQL Database számára az Azure Portalon, és feladatátvétel kezdeményezése

Ez a cikk bemutatja, hogyan konfigurálhatja [az aktív georeplikációt az egy- és készletalapú adatbázisokhoz](sql-database-active-geo-replication.md#active-geo-replication-terminology-and-capabilities) az Azure SQL Database-ben az [Azure Portal](https://portal.azure.com) használatával, és kezdeményezheti a feladatátvételt.

Az egy- és készletalapú adatbázisokkal rendelkező automatikus feladatátvételi csoportokról az [Ajánlott feladatátvételi csoportok egy- és készletalapú adatbázisokkal való használatának gyakorlati tanácsai](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools)című témakörben talál. A felügyelt példányokkal rendelkező automatikus feladatátvételi csoportokról a [Feladatátvevő csoportok felügyelt példányokkal való használatának gyakorlati tanácsai](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-managed-instances)című témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

Az aktív georeplikáció konfigurálásához az Azure Portal használatával a következő erőforrásra van szükség:

* Egy Azure SQL-adatbázis: Az elsődleges adatbázis, amely replikálni szeretné egy másik földrajzi régióba.

> [!Note]
> Az Azure Portal használatakor csak egy másodlagos adatbázist hozhat létre ugyanazon az előfizetésen belül, mint az elsődleges. Ha a másodlagos adatbázisnak egy másik előfizetésben kell lennie, használja [az Adatbázis-rest API létrehozása](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) vagy az ALTER DATABASE [Transact-SQL API -t.](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql)

## <a name="add-a-secondary-database"></a>Másodlagos adatbázis hozzáadása

A következő lépések új másodlagos adatbázist hoznak létre egy georeplikációs partnerségben.  

Másodlagos adatbázis hozzáadásához az előfizetés tulajdonosának vagy társtulajdonosának kell lennie.

A másodlagos adatbázis neve megegyezik az elsődleges adatbázis nevével, és alapértelmezés szerint ugyanaz a szolgáltatási szint és a számítási méret. A másodlagos adatbázis lehet egyetlen adatbázis vagy készletbe adott adatbázis. További információ: [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md) és [virtuálismag-alapú vásárlási modell.](sql-database-service-tiers-vcore.md)
A másodlagos létrehozása és a rendszer ezáltal az adatok replikálása az elsődleges adatbázisból az új másodlagos adatbázisba.

> [!NOTE]
> Ha a partneradatbázis már létezik (például egy korábbi georeplikációs kapcsolat megszüntetésének eredményeként), a parancs sikertelen lesz.

1. Az [Azure Portalon](https://portal.azure.com)keresse meg a georeplikációhoz beállítani kívánt adatbázist.
2. Az **SQL-adatbázis**lapon válassza a georeplikáció lehetőséget, majd a másodlagos adatbázis létrehozásához válassza ki a régiót. Az elsődleges adatbázist üzemeltető régión kívül bármely régiót kiválaszthat, de a [párosított régiót](../best-practices-availability-paired-regions.md)ajánljuk.

    ![Aktív georeplikáció konfigurálása](./media/sql-database-geo-replication-portal/configure-geo-replication.png)
3. Válassza ki vagy konfigurálja a másodlagos adatbázis kiszolgálóját és tarifacsomagját.

    ![Másodlagos konfigurálás](./media/sql-database-geo-replication-portal/create-secondary.png)
4. Szükség esetén hozzáadhat egy másodlagos adatbázist egy rugalmas készlethez. Ha egy készletben szeretné létrehozni a másodlagos adatbázist, kattintson a **rugalmas készletre,** és jelöljön ki egy készletet a célkiszolgálón. A célkiszolgálón már léteznie kell egy készletnek. Ez a munkafolyamat nem hoz létre készletet.
5. A másodlagos hozzáadása a **Létrehozás** gombra kattintva.
6. A másodlagos adatbázis jön létre, és a vetési folyamat kezdődik.

    ![Másodlagos konfigurálás](./media/sql-database-geo-replication-portal/seeding0.png)
7. Amikor a vetési folyamat befejeződött, a másodlagos adatbázis megjeleníti az állapotát.

    ![Vetés kész](./media/sql-database-geo-replication-portal/seeding-complete.png)

## <a name="initiate-a-failover"></a>Feladatátvétel kezdeményezése

A másodlagos adatbázis átváltható, hogy legyen az elsődleges.  

1. Az [Azure Portalon](https://portal.azure.com)keresse meg az elsődleges adatbázist a georeplikációs partnerségben.
2. Az SQL Database panelen válassza a **Minden beállítás** > **georeplikációlehetőséget.**
3. A **SECONDARIES** listában jelölje ki azt az adatbázist, amelyet az új elsődlegesvé kíván tenni, majd kattintson a **Feladatátvétel gombra.**

    ![Feladatátvevő](./media/sql-database-geo-replication-failover-portal/secondaries.png)
4. Kattintson az **Igen** gombra a feladatátvétel megkezdéséhez.

A parancs azonnal átváltja a másodlagos adatbázist az elsődleges szerepkörre. Ennek a folyamatnak általában 30 másodpercen belül vagy annál rövidebb időn belül be kell fejeződnie.

Van egy rövid időszak, amely alatt mindkét adatbázis nem érhető el (0 és 25 másodperc között), amíg a szerepkörök átvannak kapcsolva. Ha az elsődleges adatbázis több másodlagos adatbázissal rendelkezik, a parancs automatikusan újrakonfigurálja a többi másodlagos adatbázist, hogy csatlakozzon az új elsődleges hez. Normál körülmények között az egész művelet kevesebb mint egy percet vesz igénybe.

> [!NOTE]
> Ez a parancs az adatbázis gyors helyreállítására szolgál kimaradás esetén. Adatszinkronizálás nélkül indítja el a feladatátvételt (kényszerített feladatátvétel).  Ha az elsődleges online állapotban van, és tranzakciókat követ el a parancs kiadásakor, előfordulhat adatvesztés.

## <a name="remove-secondary-database"></a>Másodlagos adatbázis eltávolítása

Ez a művelet véglegesen leállítja a replikációt a másodlagos adatbázisba, és a másodlagos szerepkörét rendszeres írási és olvasási adatbázissá módosítja. Ha a másodlagos adatbázissal való kapcsolat megszakad, a parancs sikeres, de a másodlagos nem válik olvasási-olvasási és írási állapottá, amíg a kapcsolat helyre nem áll.  

1. Az [Azure Portalon](https://portal.azure.com)keresse meg az elsődleges adatbázist a georeplikációs partnerségben.
2. Az SQL-adatbázis lapon válassza a **georeplikáció**lehetőséget.
3. A **SECONDARIES** listában jelölje ki azt az adatbázist, amelyet el szeretne távolítani a georeplikációs partnerségből.
4. Kattintson **a Replikáció leállítása gombra.**

    ![Másodlagos eltávolítása](./media/sql-database-geo-replication-portal/remove-secondary.png)
5. Megnyílik egy megerősítő ablak. Kattintson az **Igen** gombra az adatbázis georeplikációs partnerségből való eltávolításához. (Állítsa olyan írás-olvasási adatbázisra, amely nem része a replikációnak.)

## <a name="next-steps"></a>További lépések

* Az aktív georeplikációról az [aktív georeplikáció](sql-database-active-geo-replication.md)című témakörben olvashat bővebben.
* Az automatikus feladatátvételi csoportokról az [Automatikus feladatátvételi csoportok](sql-database-auto-failover-group.md)
* Az üzletmenet folytonosságának áttekintését és forgatókönyveit az [Üzletmenet folytonosságának áttekintése](sql-database-business-continuity.md)című témakörben találja.

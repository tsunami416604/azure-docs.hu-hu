---
title: 'Azure Portal: SQL Database geo-replikáció | Microsoft Docs'
description: Geo-replikáció konfigurálása egyetlen vagy készletezett adatbázishoz Azure SQL Database a Azure Portal használatával és a feladatátvétel kezdeményezése
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
ms.openlocfilehash: 058afdbc4aa134b5b3c4c8cc5e9e2f2ae6f53084
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569643"
---
# <a name="configure-active-geo-replication-for-azure-sql-database-in-the-azure-portal-and-initiate-failover"></a>Az aktív geo-replikáció konfigurálása Azure SQL Databasehoz a Azure Portal és a feladatátvétel kezdeményezése

Ebből a cikkből megtudhatja, hogyan konfigurálhatja az [aktív földrajzi replikálást a Azure SQL Database önálló és készletezett adatbázisaihoz](sql-database-active-geo-replication.md#active-geo-replication-terminology-and-capabilities) a [Azure Portal](https://portal.azure.com) használatával és a feladatátvétel elindításához.

Az önálló és készletezett adatbázisokkal rendelkező automatikus feladatátvételi csoportokkal kapcsolatos további információkért lásd: [a feladatátvételi csoportok használatának ajánlott eljárásai egyetlen és készletezett adatbázisokkal](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools). A felügyelt példányokkal (előzetes verzió) rendelkező automatikus feladatátvételi csoportokkal kapcsolatos további információkért lásd: [ajánlott eljárások a feladatátvételi csoportok használatával felügyelt példányokkal](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-managed-instances).

## <a name="prerequisites"></a>Előfeltételek

Az aktív geo-replikáció a Azure Portal használatával történő konfigurálásához a következő erőforrásra van szükség:

* Egy Azure SQL Database-adatbázis: Az elsődleges adatbázis, amelyet egy másik földrajzi régióba kíván replikálni.

> [!Note]
> Azure Portal használatakor a rendszer csak egy másodlagos adatbázist hozhat létre ugyanazon az előfizetésen belül, mint az elsődleges. Ha a másodlagos adatbázisnak másik előfizetéshez kell tartoznia, használja az [adatbázis létrehozása REST API](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) vagy az [Alter Database Transact-SQL API-](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql)t.

## <a name="add-a-secondary-database"></a>Másodlagos adatbázis hozzáadása

A következő lépések egy új másodlagos adatbázist hoznak létre egy földrajzi replikálási partnerségben.  

Másodlagos adatbázis hozzáadásához az előfizetés tulajdonosának vagy társtulajdonosa kell lennie.

A másodlagos adatbázis neve megegyezik az elsődleges adatbázis nevével, és alapértelmezés szerint ugyanazt a szolgáltatási szintet és számítási méretet adja meg. A másodlagos adatbázis lehet önálló adatbázis vagy készletezett adatbázis is. További információ: [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md) és [virtuális mag-alapú vásárlási modell](sql-database-service-tiers-vcore.md).
A másodlagos létrehozása és összevetése után az adatok megkezdik az elsődleges adatbázisból az új másodlagos adatbázisba való replikálást.

> [!NOTE]
> Ha a partner-adatbázis már létezik (például egy korábbi földrajzi replikálási kapcsolat megszakítása miatt), a parancs sikertelen lesz.

1. A [Azure Portal](https://portal.azure.com)keresse meg a Geo-replikációhoz beállítani kívánt adatbázist.
2. Az SQL Database lapon válassza a **geo-replikálás**lehetőséget, majd válassza ki a régiót a másodlagos adatbázis létrehozásához. Bármelyik régiót kiválaszthatja, amely nem az elsődleges adatbázist üzemeltető régió, hanem a [párosított régiót](../best-practices-availability-paired-regions.md)is ajánljuk.

    ![Aktív georeplikáció konfigurálása](./media/sql-database-geo-replication-portal/configure-geo-replication.png)
3. Válassza ki vagy konfigurálja a másodlagos adatbázis kiszolgálóját és díjszabási szintjét.

    ![Másodlagos konfigurálása](./media/sql-database-geo-replication-portal/create-secondary.png)
4. Lehetősége van másodlagos adatbázist is felvenni egy rugalmas készletbe. A másodlagos adatbázis készletben való létrehozásához kattintson a **rugalmas készlet** elemre, és jelöljön ki egy készletet a célkiszolgálón. Egy készletnek már léteznie kell a célkiszolgálón. Ez a munkafolyamat nem hoz létre készletet.
5. A másodlagos hozzáadásához kattintson a **Létrehozás** gombra.
6. A rendszer létrehozza a másodlagos adatbázist, és megkezdi az előkészítési folyamatot.

    ![Másodlagos konfigurálása](./media/sql-database-geo-replication-portal/seeding0.png)
7. A kiindulási folyamat befejezésekor a másodlagos adatbázis megjeleníti annak állapotát.

    ![Előkészítés befejezve](./media/sql-database-geo-replication-portal/seeding-complete.png)

## <a name="initiate-a-failover"></a>Feladatátvétel kezdeményezése

A másodlagos adatbázis átváltható elsődlegesként.  

1. A [Azure Portal](https://portal.azure.com)tallózással keresse meg az elsődleges adatbázist a Geo-replikálási partnerségben.
2. A SQL Database panelen válassza a **minden beállítás** > **földrajzi replikálás**lehetőséget.
3. A **formátumú másodlagos zónák** listában válassza ki azt az adatbázist, amelynek az új elsődlegesnek kell lennie, majd kattintson a **feladatátvétel**elemre.

    ![feladatátvétel](./media/sql-database-geo-replication-failover-portal/secondaries.png)
4. A feladatátvétel elindításához kattintson az **Igen** gombra.

A parancs azonnal átváltja a másodlagos adatbázist az elsődleges szerepkörbe. Ez a folyamat általában legalább 30 másodpercen belül elvégezhető.

Van egy rövid időszak, amelyben mindkét adatbázis nem érhető el (0 és 25 másodperc között), amíg a szerepkörök át nem állnak. Ha az elsődleges adatbázis több másodlagos adatbázissal rendelkezik, a parancs automatikusan újrakonfigurálja a többi formátumú másodlagos zónák az új elsődlegeshez való kapcsolódáshoz. A teljes műveletnek egy percnél rövidebbnek kell lennie, hogy a szokásos körülmények között befejeződjön.

> [!NOTE]
> Ez a parancs leállás esetén az adatbázis gyors helyreállítására szolgál. Az adatszinkronizálás (kényszerített feladatátvétel) nélkül indítja el a feladatátvételt.  Ha az elsődleges hálózat online állapotú, és tranzakciókat véglegesít, ha a parancs kiadása valamilyen adatvesztést eredményezhet.

## <a name="remove-secondary-database"></a>Másodlagos adatbázis eltávolítása

Ez a művelet véglegesen leállítja a replikálást a másodlagos adatbázisba, és a másodlagos szerepkört egy normál írási-olvasási adatbázisra módosítja. Ha a másodlagos adatbázishoz való kapcsolódás megszakad, a parancs sikeres lesz, de a másodlagos nem írható-olvasható, amíg a kapcsolat vissza nem áll.  

1. A [Azure Portal](https://portal.azure.com)tallózással keresse meg az elsődleges adatbázist a Geo-replikálási partnerségben.
2. Az SQL Database lapon válassza a **geo-replikáció**lehetőséget.
3. A **formátumú másodlagos zónák** listából válassza ki azt az adatbázist, amelyet el szeretne távolítani a földrajzi replikálási partnerségből.
4. Kattintson a **replikálás leállítása**elemre.

    ![Másodlagos eltávolítása](./media/sql-database-geo-replication-portal/remove-secondary.png)
5. Megnyílik egy megerősítő ablak. Az **Igen** gombra kattintva távolítsa el az adatbázist a földrajzi replikálási partnerségből. (Állítsa azt egy írható-olvasható adatbázisba, amely nem része egyetlen replikációnak sem.)

## <a name="next-steps"></a>További lépések

* További információ az aktív földrajzi replikálásról: [Active geo-Replication](sql-database-active-geo-replication.md).
* További információ az automatikus feladatátvételi csoportokról: [automatikus feladatátvételi csoportok](sql-database-auto-failover-group.md)
* Az üzletmenet folytonosságának áttekintése és forgatókönyvei: az [üzletmenet folytonosságának áttekintése](sql-database-business-continuity.md).

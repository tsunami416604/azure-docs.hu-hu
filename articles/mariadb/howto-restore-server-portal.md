---
title: Biztonsági mentés és visszaállítás – Azure portal – Azure Database for MariaDB
description: Ez a cikk ismerteti, hogyan lehet visszaállítani egy kiszolgálót az Azure Database for MariaDB az Azure Portal használatával.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/27/2020
ms.openlocfilehash: fa8ead8daa202f5747c134a62fbd43bcdf2af0d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369248"
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-mariadb-using-the-azure-portal"></a>Kiszolgáló biztonsági mentése és visszaállítása a MariaDB Azure Database szolgáltatásában az Azure Portal használatával

## <a name="backup-happens-automatically"></a>A biztonsági mentés automatikusan megtörténik
A MariaDB-kiszolgálók Azure Database szolgáltatásáról rendszeres időközönként készül biztonsági mentés a visszaállítási funkciók engedélyezéséhez. Ezzel a szolgáltatással visszaállíthatja a kiszolgálót és annak adatbázisait egy korábbi időpontra, egy új kiszolgálóra.

## <a name="prerequisites"></a>Előfeltételek
Az útmutató befejezéséhez a következőkre van szükség:
- [Azure-adatbázis a MariaDB-kiszolgálóhoz és -adatbázishoz](quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="set-backup-configuration"></a>Biztonsági másolat konfigurációjának beállítása

A kiszolgáló helyiredundáns biztonsági mentések vagy földrajzilag redundáns biztonsági mentések konfigurálása között választhat a kiszolgáló létrehozásakor, a **Tarifacsomag** ablakban.

> [!NOTE]
> A kiszolgáló létrehozása után a redundancia, földrajzilag redundáns vs helyileg redundáns, nem lehet váltani.
>

Kiszolgáló létrehozása közben az Azure Portalon keresztül, a **tarifacsomag** ablakban választhat **ja helyileg redundáns** vagy **földrajzilag redundáns** biztonsági mentések a kiszolgálóhoz. Ebben az ablakban választhatja ki a **biztonsági mentés megőrzési időszakát** is , hogy mennyi ideig (napok ban) szeretné tárolni a kiszolgáló biztonsági másolatait.

   ![Tarifacsomag – Biztonsági mentési redundancia kiválasztása](./media/howto-restore-server-portal/pricing-tier.png)

Az értékek létrehozás során történő beállításáról az [Azure Database for MariaDB server rövid útmutatójában](quickstart-create-mariadb-server-database-using-azure-portal.md)talál további információt.

A biztonsági mentés megőrzési ideje a kiszolgálón a következő lépésekkel módosítható:
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Válassza ki az Azure-adatbázis Tiamikiszolgálóhoz. Ez a művelet **megnyitja** az Áttekintés lapot.

3. Válassza a Beállítási szint menü **Ditaszint parancsát** a **BEÁLLÍTÁSOK csoportban.** A csúszka segítségével **módosíthatja** a biztonsági mentés megőrzési időszak a kívánt 7 és 35 nap között.
Az alábbi képernyőképen 35 napra növelték.
![A biztonsági mentés megőrzési ideje megnövelt](./media/howto-restore-server-portal/3-increase-backup-days.png)

4. A módosítás megerősítéséhez kattintson az **OK** gombra.

A biztonsági mentés megőrzési időszak határozza meg, hogy milyen messze vissza az időben egy időpontban vissza egy időpontban visszaállítási lehet letölteni, mivel ez a rendelkezésre álló biztonsági mentések alapján. Az időponthoz való időpontvisszaállítást a következő szakasz ismerteti. 

## <a name="point-in-time-restore"></a>Adott időpontnak megfelelő helyreállítás
A MariaDB Azure Database lehetővé teszi, hogy visszaállítsa a kiszolgálót egy adott időpontra és a kiszolgáló új példányára. Ezzel az új kiszolgálóval helyreállíthatja az adatokat, vagy beállíthatja, hogy az ügyfélalkalmazások erre az új kiszolgálóra mutassanak.

Ha például egy táblát véletlenül ma délben dobtak ki, visszaállíthatja a dél előtti időt, és lekérheti a hiányzó táblát és adatokat a kiszolgáló új példányából. Az időponthoz való visszaállítás kiszolgálószinten van, nem az adatbázis szintjén.

A következő lépések visszaállítják a mintakiszolgálót egy adott időpontra:
1. Az Azure Portalon válassza ki az Azure Database for MariaDB-kiszolgálót. 

2. A kiszolgáló **áttekintése** lapjának eszköztárán válassza a **Visszaállítás**lehetőséget.

   ![Azure Database for MariaDB – Áttekintés – Visszaállítás gomb](./media/howto-restore-server-portal/2-server.png)

3. Töltse ki a Visszaállítás űrlapot a szükséges információkkal:

   ![Azure Database for MariaDB – Adatok visszaállítása](./media/howto-restore-server-portal/3-restore.png)
   - **Visszaállítási pont**: Jelölje ki azt a pont-időpontot, amelybe vissza szeretné állítani.
   - **Célkiszolgáló**: Adja meg az új kiszolgáló nevét.
   - **Hely**: A régió nem választható ki. Alapértelmezés szerint ugyanaz, mint a forráskiszolgáló.
   - **Tarifacsomag**: Ezeket a paramétereket nem módosíthatja, ha időponthoz ért visszaállítást végez. Ugyanaz, mint a forráskiszolgálóé. 

4. Kattintson **az OK** gombra a kiszolgáló pont-in-time visszaállításához. 

5. A visszaállítás befejezése után keresse meg a létrehozott új kiszolgálót, amely ellenőrzi, hogy az adatok a várt módon lett-e visszaállítva.


A pont-az-idő visszaállítás sal létrehozott új kiszolgáló ugyanaza a kiszolgálórendszergazdai bejelentkezési név és jelszó, amely a meglévő kiszolgálóra érvényes volt a kiválasztott időpontban. A jelszót az új kiszolgáló áttekintése lapon **módosíthatja.**

A visszaállítás során létrehozott új kiszolgáló nem rendelkezik az eredeti kiszolgálón létező virtuális hálózat szolgáltatásvégpontokkal. Ezeket a szabályokat külön kell beállítani ehhez az új kiszolgálóhoz. Az eredeti kiszolgáló tűzfalszabályai visszaállnak.

## <a name="geo-restore"></a>Geo-visszaállítás

Ha a kiszolgálót földrajzilag redundáns biztonsági másolatokra állította be, a meglévő kiszolgáló biztonsági másolatából új kiszolgáló hozható létre. Ez az új kiszolgáló bármely olyan régióban létrehozható, amelyen az Azure Database for MariaDB elérhető.  

1. Válassza **az Adatbázisok** > **Azure-adatbázisa a MariaDB-hez**lehetőséget. A **mariadb** beírásával is keresheti a szolgáltatást.

   ![Az "Azure Database for MariaDB" opció](./media/howto-restore-server-portal/2_navigate-to-mariadb.png)

2. Az űrlap **Forrás kiválasztása** legördülő menüjében válassza a Biztonsági **másolat lehetőséget.** Ez a művelet betölti azoknak a kiszolgálóknak a listáját, amelyeken engedélyezve van a georedundáns biztonsági mentések. Válassza ki az egyik ilyen biztonsági másolatot, hogy a forrása az új kiszolgáló.
   ![Válassza a Forrás: Biztonsági mentés és georedundáns biztonsági mentések listája lehetőséget](./media/howto-restore-server-portal/2-georestore.png)

   > [!NOTE]
   > A kiszolgáló első létrehozásakor előfordulhat, hogy nem érhető el azonnal a geo-visszaállításhoz. A szükséges metaadatok feltöltése néhány órát is igénybe vehet.
   >

3. Töltse ki az űrlap többi részét a preferenciáival. Bármelyik helyet **kiválaszthatja.** A hely kiválasztása után **kiválaszthatja a Tarifacsomagot.** Alapértelmezés szerint azok a meglévő kiszolgáló paraméterei jelennek meg, amelyről visszaállítja azokat. Az **OK** gombra kattintva anélkül, hogy módosításokat végezne a beállítások örökléséhez. Vagy **módosíthatja a számítási generációt** (ha elérhető a kiválasztott régióban), a **virtuális magok**számát , a biztonsági **mentés megőrzési időszakát**és a **biztonsági mentési redundancia beállítást.** A visszaállítás során nem támogatott **a tarifacsomag** (alapszintű, általános célú vagy memóriaoptimalizált) vagy **a tárhely** méret módosítása.

A geo-visszaállítás által létrehozott új kiszolgáló ugyanazokkal a kiszolgálórendszergazdai bejelentkezési névvel és jelszóval rendelkezik, mint amely a visszaállítás indításakor érvényes volt a meglévő kiszolgálóra. A jelszó az új kiszolgáló **áttekintő** lapjáról módosítható.

A visszaállítás során létrehozott új kiszolgáló nem rendelkezik az eredeti kiszolgálón létező virtuális hálózat szolgáltatásvégpontokkal. Ezeket a szabályokat külön kell beállítani ehhez az új kiszolgálóhoz. Az eredeti kiszolgáló tűzfalszabályai visszaállnak.

## <a name="next-steps"></a>További lépések
- További információ a szolgáltatás [biztonsági másolatairól](concepts-backup.md)
- További információ a [replikákról](concepts-read-replicas.md)
- További információ az [üzletmenet-folytonossági](concepts-business-continuity.md) lehetőségekről

---
title: Biztonsági mentés és visszaállítás – Azure Portal-Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ez a cikk azt ismerteti, hogyan állíthatja vissza a kiszolgálót Azure Database for PostgreSQL – egyetlen kiszolgálón a Azure Portal használatával.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/25/2019
ms.openlocfilehash: fb13e4f062976e39c3cec607001e6982db228873
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "74765630"
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Kiszolgálók biztonsági mentése és visszaállítása Azure Database for PostgreSQL – egyetlen kiszolgálón a Azure Portal használatával

## <a name="backup-happens-automatically"></a>A biztonsági mentés automatikusan megtörténik
A visszaállítási funkciók engedélyezéséhez rendszeresen biztonsági mentést kell készíteni Azure Database for PostgreSQL-kiszolgálókról. A szolgáltatás használatával visszaállíthatja a kiszolgálót és az összes adatbázisát egy korábbi időpontra, egy új kiszolgálón.

## <a name="set-backup-configuration"></a>Biztonsági mentési konfiguráció beállítása

A kiszolgáló létrehozásakor a **díjszabási** szinten ablakban beállíthatja, hogy a kiszolgálót helyileg redundáns biztonsági mentésekhez vagy földrajzilag redundáns biztonsági másolatokhoz konfigurálja.

> [!NOTE]
> A kiszolgáló létrehozása után a redundancia, földrajzilag redundáns vagy helyileg redundáns, nem állítható be.
>

Amikor a Azure Portalon keresztül hoz létre kiszolgálót, a **díjszabási rétegek** ablakban a **helyi redundáns** vagy **földrajzilag redundáns** biztonsági másolatokat választhatja a kiszolgáló számára. Ezen az ablakban is kiválaszthatja a **biztonsági mentés megőrzési időtartamát** – mennyi ideig (hány nap múlva) kívánja tárolni a kiszolgáló biztonsági másolatait.

   ![Díjszabási réteg – a biztonsági másolatok redundanciának kiválasztása](./media/howto-restore-server-portal/pricing-tier.png)

További információ ezekről az értékekről a létrehozás során: [Azure Database for PostgreSQL Server](quickstart-create-server-database-portal.md)gyors üzembe helyezése.

A kiszolgáló biztonsági mentési megőrzési időszaka a következő lépésekkel módosítható:
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Válassza ki az Azure Database for PostgreSQL kiszolgálóját. Ez a művelet megnyitja az **Áttekintés** lapot.
3. Válassza ki az **árképzési szintet** a menü **Beállítások**területén. A csúszka használatával megváltoztathatja a **biztonsági mentés megőrzési időtartamát** 7 és 35 nap között.
Az alábbi képernyőképen a 34 napra nőtt.
![A biztonsági másolatok megőrzési időszaka nagyobb](./media/howto-restore-server-portal/3-increase-backup-days.png)

4. A módosítás megerősítéséhez kattintson **az OK** gombra.

A biztonsági másolatok megőrzési időszaka azt szabályozza, hogy az adott időpontra visszamenőleges visszaállítás hogyan kérhető le, mert az elérhető biztonsági másolatokon alapul. Az időponthoz való visszaállítást a következő szakasz ismerteti részletesebben. 

## <a name="point-in-time-restore"></a>Adott időpontnak megfelelő helyreállítás
Azure Database for PostgreSQL lehetővé teszi, hogy visszaállítsa a kiszolgálót egy adott időpontra, és a-kiszolgáló egy új példányára. Ezt az új kiszolgálót használhatja az adatok helyreállításához, vagy az ügyfélalkalmazások erre az új kiszolgálóra mutatnak.

Ha például egy táblázatot véletlenül a mai napig dobott el, akkor a visszaállíthatja a déli idő előtt, és lekérheti a hiányzó táblát és adatforrást a kiszolgáló új példányáról. Az időponthoz való visszaállítás a kiszolgáló szintjén, nem pedig az adatbázis szintjén történik.

A következő lépésekkel állíthatja vissza a kiszolgálót egy időpontra:
1. A Azure Portal válassza ki a Azure Database for PostgreSQL kiszolgálót. 

2. A kiszolgáló **Áttekintés** lapjának eszköztárán válassza a **visszaállítás**lehetőséget.

   ![Azure Database for PostgreSQL – áttekintés – visszaállítás gomb](./media/howto-restore-server-portal/2-server.png)

3. Töltse ki a Visszaállítás űrlapot a szükséges információkkal:

   ![Azure Database for PostgreSQL – adatok visszaállítása](./media/howto-restore-server-portal/3-restore.png)
   - **Visszaállítási pont**: válassza ki azt az időpontot, amelyet vissza szeretne állítani.
   - **Célkiszolgáló**: adja meg az új kiszolgáló nevét.
   - **Hely**: nem választhatja ki a régiót. Alapértelmezés szerint ugyanaz, mint a forráskiszolgáló.
   - **Díjszabási**csomag: ezeket a paramétereket nem módosíthatja az időponthoz tartozó visszaállítás során. Ugyanaz, mint a forráskiszolgálóé. 

4. Kattintson **az OK** gombra, ha vissza szeretné állítani a kiszolgálót egy adott időpontra való visszaállításhoz. 

5. A visszaállítás befejeződése után keresse meg a létrehozott új kiszolgálót annak ellenőrzéséhez, hogy az Adathelyreállítás a várt módon történt-e.

Az időponthoz tartozó visszaállítás által létrehozott új kiszolgáló ugyanazzal a kiszolgáló-rendszergazdai bejelentkezési névvel és jelszóval rendelkezik, amely érvényes volt a meglévő kiszolgáló számára a kiválasztott időpontban. A jelszót az új kiszolgáló **áttekintő** oldaláról lehet módosítani.

A visszaállítás során létrehozott új kiszolgáló nem rendelkezik az eredeti kiszolgálón található tűzfalszabályok vagy VNet szolgáltatás-végpontokkal. Ezeket a szabályokat külön kell beállítani az új kiszolgálóhoz.


## <a name="geo-restore"></a>Geo-visszaállítás

Ha a kiszolgálót földrajzilag redundáns biztonsági mentésre konfigurálta, akkor a rendszer létrehoz egy új kiszolgálót a meglévő kiszolgáló biztonsági másolatából. Ezt az új kiszolgálót bármely olyan régióban létre lehet hozni, amely Azure Database for PostgreSQL elérhető.  

1. Válassza az **erőforrás létrehozása** gombot (+) a portál bal felső sarkában. Válassza az **adatbázisok** > **Azure Database for PostgreSQL**elemet.

   ![Az „Azure Database for PostgreSQL” lehetőség](./media/howto-restore-server-portal/1-navigate-to-postgres.png)

2. Az űrlap **Select Source (forrás kiválasztása** ) legördülő menüben válassza a **biztonsági mentés**lehetőséget. Ez a művelet betölti azon kiszolgálók listáját, amelyeken engedélyezve van a földrajzi redundáns biztonsági másolatok. Válassza ki az egyik biztonsági mentést az új kiszolgáló forrásaként.
   ![Forrás kiválasztása: a redundáns biztonsági másolatok biztonsági mentése és listázása](./media/howto-restore-server-portal/2-georestore.png)

   > [!NOTE]
   > A kiszolgálók első létrehozásakor előfordulhat, hogy a Geo-visszaállításhoz nem lesz azonnal elérhető. A szükséges metaadatok feltöltése néhány órát is igénybe vehet.
   >

3. Adja meg a többi űrlapot a beállításokkal. Bármelyik **helyet**kiválaszthatja. A hely kiválasztása után kiválaszthatja az **árképzési szintet**. Alapértelmezés szerint a rendszer megjeleníti a meglévő, visszaállításra szolgáló kiszolgáló paramétereit. **Az OK** gombra kattintva módosíthatja a beállítások öröklését. Vagy módosíthatja a **számítási generációt** (ha elérhető a választott régióban), a **virtuális mag**számát, a **biztonsági mentés megőrzési idejét**és a **biztonsági mentési redundancia beállítást**. Az **árképzési szint** (alapszintű, általános célú vagy memória optimalizálása) vagy a **tárolási** méret módosítása a visszaállítás során nem támogatott.


A földrajzi visszaállítás által létrehozott új kiszolgáló ugyanazzal a kiszolgáló-rendszergazdai bejelentkezési névvel és jelszóval rendelkezik, amely a visszaállítás kezdeményezésének időpontjában érvényes a meglévő kiszolgálóhoz. A jelszót az új kiszolgáló **áttekintő** oldaláról lehet megváltoztatni.

A visszaállítás során létrehozott új kiszolgáló nem rendelkezik az eredeti kiszolgálón található tűzfalszabályok vagy VNet szolgáltatás-végpontokkal. Ezeket a szabályokat külön kell beállítani az új kiszolgálóhoz.


## <a name="next-steps"></a>További lépések
- További információ a szolgáltatás [biztonsági mentéséről](concepts-backup.md).
- További információ az [üzletmenet-folytonossági](concepts-business-continuity.md) lehetőségekről.

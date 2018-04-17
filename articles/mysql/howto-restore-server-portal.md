---
title: A MySQL egy kiszolgálóhoz az Azure-adatbázis visszaállítása
description: Ez a cikk ismerteti a kiszolgáló Azure-adatbázis visszaállítása a MySQL az Azure portál használatával.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 04/01/2018
ms.openlocfilehash: 8782befb1548606fe649909a7841eb648c15f1c6
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-mysql-using-the-azure-portal"></a>Biztonsági mentése és visszaállítása egy kiszolgálóhoz az Azure-adatbázis MySQL az Azure portál használatával

## <a name="backup-happens-automatically"></a>Automatikusan megtörténik a biztonsági mentés
A MySQL-kiszolgálók Azure-adatbázis biztonsági mentése rendszeresen visszaállítási szolgáltatások engedélyezése. Ezzel a szolgáltatással is visszaállíthatja a kiszolgáló és az adatbázisok egy korábbi-időpontban, egy új kiszolgálóra.

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató útmutató befejezéséhez lesz szüksége:
- Egy [a MySQL-kiszolgáló és az adatbázis Azure-adatbázis](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-backup-configuration"></a>Biztonsági mentési konfigurációjának beállítása

Vagy helyileg redundáns vagy földrajzilag redundáns történő biztonsági mentésekre a kiszolgáló létrehozásakor, a kiszolgáló konfigurálása közötti választás a **Tarifacsomagot** ablak.

> [!NOTE]
> Miután a kiszolgáló akkor jön létre, milyen típusú redundancia rendelkezik, helyileg redundáns földrajzilag redundáns vs nem állítható át.
>

Az Azure portálon keresztül a kiszolgáló létrehozása során a **Tarifacsomagot** időszak, ahol ki kell választania vagy **helyileg redundáns** vagy **földrajzilag redundáns** biztonsági mentés a kiszolgáló. Ebben az ablakban is, ahol ki kell választania a **biztonsági mentés megőrzési időszak** – mennyi ideig (a napok száma) a kiszolgáló biztonsági másolatait tárolni szeretné.

   ![Tarifacsomag - válassza ki a biztonsági mentési redundancia](./media/howto-restore-server-portal/pricing-tier.png)

Ezeket az értékeket létrehozásakor beállításával kapcsolatos további információkért lásd: a [MySQL server gyors üzembe helyezés az Azure Database](quickstart-create-mysql-server-database-using-azure-portal.md).

A biztonsági mentés megőrzési idő módosítható a következő lépések során a kiszolgálón:
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Válassza ki a MySQL-kiszolgálóhoz tartozó Azure-adatbázis. Ez a művelet megnyitja az **áttekintése** lap.
3. Válassza ki **Tarifacsomagot** a menüben a **beállítások**. A csúszkával módosítható a **biztonsági mentés megőrzési időszak** a 7-es vagy 35 nap közötti, igény szerint.
Az alábbi képernyőképen az értékre nőtt 34 nap.
![Biztonsági mentés megőrzési időszak nőtt](./media/howto-restore-server-portal/3-increase-backup-days.png)

4. Kattintson a **OK** a módosítás megerősítéséhez.

A biztonsági mentés megőrzési időszak irányító milyen távolságban az idő lekérhető pont időponthoz kötött visszaállítás, mivel elérhető biztonsági másolatokat alapul. A következő szakaszban leírt további pont időponthoz kötött visszaállítás. 

## <a name="point-in-time-restore"></a>Adott időpontnak megfelelő helyreállítás
Azure MySQL-adatbázis lehetővé teszi a kiszolgáló helyreállítása vissza egy-időpontban, és azokat a kiszolgáló egy új példányát. A következő új kiszolgáló használata az adatok helyreállításához, vagy az ügyfél alkalmazásokat az új kiszolgálóra mutasson.

Például ha egy táblázat véletlenül dobva délben ma, akkor sikerült visszaállítása előtt déltől idő, a hiányzó tábla és adatokat lekérdezni az új példányt a kiszolgáló. Időpontban visszaállítást a kiszolgálón szint, nem az adatbázis szintjén.

Az alábbi lépéseket a minta kiszolgáló visszaállítása a-időpontban:
1. Az Azure-portálon válassza ki a MySQL-kiszolgálóhoz tartozó Azure-adatbázis. 

2. A kiszolgáló eszköztárán **áttekintése** lapon jelölje be **visszaállítása**.

   ![Azure-adatbázis MySQL - áttekintése - Visszaállítás gomb](./media/howto-restore-server-portal/2-server.png)

3. Töltse ki a visszaállítási űrlapot a szükséges adatokkal:

   ![MySQL - visszaállítási információk az Azure-adatbázis ](./media/howto-restore-server-portal/3-restore.png)
  - **Visszaállítási pont**: válassza ki a-időpontban a visszaállítani kívánt.
  - **Célkiszolgáló**: Adjon meg egy nevet az új kiszolgálóra.
  - **Hely**: a régió nem választhatók ki. Alapértelmezés szerint az ugyanaz, mint a forráskiszolgálón.
  - **A tarifacsomag**: ezek a paraméterek nem módosítható, ha a pont időponthoz kötött visszaállítás során. Ugyanaz, mint a forráskiszolgálóé. 

4. Kattintson a **OK** visszaállítása a kiszolgálón történő visszaállításához, egy-időpontban. 

5. Miután a visszaállítás befejezését, keresse meg az új kiszolgálóra, amely az adatok helyreállt a várt módon ellenőrzése jön létre.

>[!Note]
>Vegye figyelembe a időpontban visszaállítás által létrehozott új kiszolgáló rendelkezik-e az azonos felügyeleti bejelentkezési nevet és jelszót, amely a meglévő kiszolgáló, a-időpontban érvényes választott. Módosíthatja a jelszót az új kiszolgáló **áttekintése** lap.

## <a name="geo-restore"></a>Georedundáns helyreállítás
Ha a kiszolgáló a földrajzilag redundáns biztonsági mentéshez konfigurált, egy új kiszolgálót a biztonsági mentésből meglévő kiszolgáló hozhatók létre. Az új kiszolgáló is hozható létre bármely régióban, hogy rendelkezésre áll-e MySQL az Azure-adatbázis.  

1. A portál bal felső sarkában válassza az **Erőforrás létrehozása** (+) gombot. Válassza a **Databases** > **Azure Database for MySQL** lehetőséget.

   ![Az "Azure adatbázis a MySQL" beállítás](./media/howto-restore-server-portal/2_navigate-to-mysql.png)

2. A képernyőn **forrásának kijelölése** legördülő menüben válasszon **biztonsági mentés**. Ez a művelet betölti a földrajzi redundancia biztonsági mentések engedélyezett kiszolgálók listáját. Válasszon egyet az új kiszolgáló forrásként a biztonsági mentése.
   ![Forrás kiválasztása: Biztonsági mentés és földrajzi redundancia biztonsági mentések listája](./media/howto-restore-server-portal/2-georestore.png)

   > [!NOTE]
   > A kiszolgáló létrehozásakor azt nem lehet azonnal elérhető georedundáns helyreállítás. A szükséges metaadatok kell feltöltenie néhány órát is igénybe vehet.
   >

3. Adja meg a többi a beállítások az űrlapot. Kiválaszthatja a **hely**. Miután kiválasztotta a helyen, választhatja **Tarifacsomagot**. Alapértelmezés szerint a meglévő kiszolgáló visszaállítását végzi, a paraméterek jelennek meg. Kattinthat **OK** ezek a beállítások öröklésére változtatás nélkül. Bármikor módosíthatja vagy **számítási generációs** (ha elérhető a régióban választott), a száma, **vCores**, **biztonsági mentés megőrzési időszak**, és **biztonsági mentése Redundancia beállítás**. Módosítása **Tarifacsomagot** (alapszintű, általános célú vagy Memóriaoptimalizált) vagy **tárolási** visszaállítás során mérete nem támogatott.

>[!Note]
>A földrajzi visszaállítási által létrehozott új kiszolgáló ugyanazt a rendszergazdai bejelentkezési nevet és jelszót, kezdeményezték a visszaállítás időpontjában volt érvényes a meglévő kiszolgáló rendelkezik. A jelszó módosíthatja az új kiszolgáló **áttekintése** lap.


## <a name="next-steps"></a>További lépések
- További információ a szolgáltatás [biztonsági mentések](concepts-backup.md).
- További információ [az üzletmenet folytonossága](concepts-business-continuity.md) beállítások.

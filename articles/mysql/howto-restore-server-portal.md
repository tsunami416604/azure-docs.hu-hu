---
title: Visszaállítása egy kiszolgálót az Azure Database for MySQL-hez
description: Ez a cikk ismerteti, hogyan lehet visszaállítani egy kiszolgálót az Azure Database for MySQL-hez az Azure portal használatával.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 04/01/2018
ms.openlocfilehash: 7f75950a0f61c646d6cf36ea0903ebdefbd336ee
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53138479"
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-mysql-using-the-azure-portal"></a>Biztonsági mentése és visszaállítása egy kiszolgálót az Azure Database for MySQL-hez az Azure portal használatával

## <a name="backup-happens-automatically"></a>Biztonsági mentés automatikusan történik
Azure Database for MySQL-kiszolgálók biztonsági mentése rendszeres visszaállítási szolgáltatások engedélyezése. Ezzel a funkcióval, előfordulhat, hogy állítsa vissza a kiszolgáló és az összes adatbázis egy korábbi-időponthoz, egy új kiszolgálón.

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató végrehajtásához lesz szüksége:
- Egy [, Azure Database for MySQL-kiszolgáló és adatbázis](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-backup-configuration"></a>Biztonsági mentési konfiguráció beállítása

A kiszolgáló georedundáns biztonsági másolatokat, a kiszolgáló létrehozása, vagy helyileg redundáns biztonsági mentések konfigurálása a választást, győződjön meg arról, a **Tarifacsomag** ablak.

> [!NOTE]
> Miután a kiszolgáló akkor jön létre, milyen típusú redundancia rendelkezik, a helyileg redundáns, georedundáns és nem kapcsolható.
>

Az Azure Portalon, a kiszolgáló létrehozásakor a **Tarifacsomag** időszak, ahol ki kell választania vagy **helyileg redundáns** vagy **Georedundáns** biztonsági mentése a kiszolgáló. Ezt az ablakot is, ahol ki kell választania a **biztonsági mentések megőrzési időszaka** – mennyi ideig (a napok száma) szeretné tárolni, a kiszolgáló biztonsági mentése.

   ![Tarifacsomag - válassza ki a biztonsági másolat redundancia](./media/howto-restore-server-portal/pricing-tier.png)

Ezek az értékek beállítása a létrehozás során kapcsolatos további információkért lásd: a [, Azure Database for MySQL-kiszolgáló rövid](quickstart-create-mysql-server-database-using-azure-portal.md).

A biztonsági másolat megőrzési idejének módosítható egy kiszolgálón az alábbi lépéseket:
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Válassza ki az Azure Database for MySQL-kiszolgálóhoz. Ez a művelet megnyitja a **áttekintése** lapot.
3. Válassza ki **Tarifacsomag** a menüben a **beállítások**. A csúszkával módosíthatja a **biztonsági mentések megőrzési időszaka** 7 és 35 nap a prioritást.
Az alábbi képernyőképen azt értékre nőtt 34 nap.
![Biztonsági másolat megőrzési idejének nőtt](./media/howto-restore-server-portal/3-increase-backup-days.png)

4. Kattintson a **OK** a módosítás megerősítéséhez.

A biztonsági másolat megőrzési idejének milyen időben időponthoz visszaállítást is lekérdezhetők,-alapú biztonsági mentések érhető el, mivel szabályozza. A következő szakaszban időponthoz visszaállítás erről. 

## <a name="point-in-time-restore"></a>Adott időpontnak megfelelő helyreállítás
Azure Database for MySQL lehetővé teszi, hogy a kiszolgáló helyreállítása egy-időponthoz vissza a és a egy új másolatot a kiszolgálóról való. Az új kiszolgáló használata az adatok helyreállításához, vagy az ügyfélalkalmazásokat, mutasson az új kiszolgáló rendelkezik.

Például ha véletlenül volt tábla dobja el a még ma, noon, sikerült visszaállítása előtt noon idő és a hiányzó táblát és az adatok lekérését, hogy a kiszolgáló új példányának. Időponthoz visszaállítás a kiszolgáló szintű, nem az adatbázis szintjén.

A következő lépések a mintakiszolgálót egy-időponthoz állítja vissza:
1. Az Azure Portalon válassza ki az Azure Database for MySQL-kiszolgálóhoz. 

2. Az eszköztáron a kiszolgáló **áttekintése** lapon jelölje be **visszaállítása**.

   ![Azure Database for MySQL – áttekintés – Visszaállítás gomb](./media/howto-restore-server-portal/2-server.png)

3. Adja meg a visszaállítás űrlapon a szükséges információkkal:

   ![Azure Database for MySQL - visszaállítási információ ](./media/howto-restore-server-portal/3-restore.png)
   - **Visszaállítási pont**: válassza ki a-időponthoz a visszaállítani kívánt.
   - **Célkiszolgáló**: Adja meg az új kiszolgáló nevét.
   - **Hely**: a régió nem választhat ki. Alapértelmezés szerint a ugyanaz, mint a forráskiszolgálón.
   - **A tarifacsomag**: ezek a paraméterek nem módosítható, ha egy időponthoz visszaállítás során. Ugyanaz, mint a forráskiszolgálóé. 

4. Kattintson a **OK** visszaállítása egy pont kötött visszaállítása a kiszolgálón. 

5. Ha a visszaállítás kész, keresse meg, hogy ellenőrizze az adatokat a várt módon álltak jön létre.

>[!Note]
>Vegye figyelembe a időponthoz visszaállítás által létrehozott új kiszolgáló rendelkezik-e az azonos kiszolgálói rendszergazda bejelentkezési neve és a jelszót, amely érvényes a meglévő kiszolgáló, a-időponthoz választotta. Módosíthatja a jelszót az új kiszolgáló **áttekintése** lapot.

## <a name="geo-restore"></a>GEO-visszaállítás
Ha konfigurálta a kiszolgáló georedundáns biztonsági mentésekhez, egy új kiszolgálót a biztonsági mentésből a meglévő kiszolgáló hozható létre. Az új kiszolgáló bármelyik régióban érhető el, hogy az Azure Database for MySQL hozható létre.  

1. A portál bal felső sarkában válassza az **Erőforrás létrehozása** (+) gombot. Válassza a **Databases** > **Azure Database for MySQL** lehetőséget.

   ![Az "Azure Database for MySQL" lehetőség](./media/howto-restore-server-portal/2_navigate-to-mysql.png)

2. Az űrlap **forrás kiválasztása** legördülő menüben válassza a **Backup**. Ez a művelet betölti a földrajzi georedundáns biztonsági mentés engedélyezve rendelkező kiszolgálók listáját. Válasszon ki egy ezeket a biztonsági másolatokat az új kiszolgáló forrása lehet.
   ![Forrás kiválasztása: Biztonsági mentés és földrajzi redundáns biztonsági másolatainak listáját](./media/howto-restore-server-portal/2-georestore.png)

   > [!NOTE]
   > A kiszolgáló létrehozásakor azt nem lehet geo-visszaállítás azonnal elérhetővé válik. A szükséges metaadatok kell feltöltenie néhány órát vehet igénybe.
   >

3. Adja meg a beállításokat az űrlapot a többi. Kiválaszthatja az esetleges **hely**. Helyének kiválasztása után kiválaszthatja **Tarifacsomag**. Alapértelmezés szerint a paraméterek a meglévő kiszolgáló létrehozottként jelennek meg. Kattinthat **OK** ezek a beállítások öröklődjenek módosítása nélkül. Módosíthatja vagy **számítási generáció** (ha elérhető a régióban választott), száma **virtuális magok**, **biztonsági mentések megőrzési időszaka**, és **biztonsági mentés Adatredundáns tárolási mód**. Módosítása **Tarifacsomag** (alapszintű, általános célú, vagy az optimalizált memóriájú) vagy **tárolási** méret, visszaállítás során nem támogatott.

>[!Note]
>A geo-visszaállítás által létrehozott új kiszolgáló van a kiszolgáló rendszergazdájának bejelentkezési nevét és a jelszót, amely kezdeményezte a visszaállítás időpontjában volt érvényes a meglévő kiszolgáló. A jelszó is módosítható az új kiszolgáló **áttekintése** lapot.


## <a name="next-steps"></a>További lépések
- További információ a szolgáltatásról [biztonsági mentések](concepts-backup.md).
- Tudjon meg többet [üzletmenet-folytonossági](concepts-business-continuity.md) beállítások.

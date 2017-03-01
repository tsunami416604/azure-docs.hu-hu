---
title: "Azure Portal: Az Azure SQL Database biztonsági mentése és visszaállítása | Microsoft Docs"
description: "Az oktatóanyag bemutatja, hogyan állíthatja vissza az adatokat automatikus biztonsági mentésekből egy adott időpontra, hogyan tárolhatja az automatikus biztonsági másolatokat az Azure helyreállítási tárban, és hogyan állíthatja vissza az Azure helyreállítási tárból azokat"
keywords: "sql database-oktatóanyag"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: aeb8c4c3-6ae2-45f7-b2c3-fa13e3752eed
ms.service: sql-database
ms.custom: business continuity
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 93efe1a08149e7c027830b03a9e426ac5a05b27b
ms.openlocfilehash: 444954fa2152fbd8b7e25b9787a13fa4a62ec77f


---
# <a name="tutorial-back-up-and-restore-an-azure-sql-database-using-the-azure-portal"></a>Oktatóanyag: Az Azure SQL Database biztonsági mentése és visszaállítása az Azure Portal segítségével
Ez az oktatóanyag bemutatja, hogyan hozhatja létre a következőket az Azure Portal használatával:

- Adatbázisok meglévő biztonsági másolatainak megtekintése
- Adatbázis visszaállítása egy korábbi időpontra
- Adatbázisokról készült biztonsági mentések fájljai hosszú távú megőrzésének konfigurálása az Azure helyreállítási tárban
- Adatbázis visszaállítása az Azure helyreállítási tárból

**Becsült időtartam**: Az oktatóanyag teljesítése körülbelül 30 percet vesz igénybe (feltéve, hogy megfelel a szükséges előfeltételeknek).

> [!TIP]
> Ugyanezen feladatokat a [PowerShell](sql-database-get-started-backup-recovery-powershell.md) használatával is végrehajthatja egy, a kezdeti lépéseket ismertető oktatóanyagban.
>

## <a name="prerequisites"></a>Előfeltételek

* Rendelkeznie kell Azure-fiókkal. [Nyithat egy ingyenes Azure-fiókot](https://azure.microsoft.com/free/) vagy [aktiválhatja a Visual Studio előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/). 

* Az előfizetés-tulajdonosi vagy közreműködői szerepkörhöz tartozó fiókok képesek csatlakozni az Azure Portalhoz. A szerepköralapú hozzáférés-vezérlésről (RBAC) többet is megtudhat az [Azure Portalon végzett hozzáférés-vezérlés alapvető tudnivalóit ismertető](../active-directory/role-based-access-control-what-is.md) témakörben.

* Ön elvégezte [Az Azure SQL Database-kiszolgálók, -adatbázisok és -tűzfalszabályok Azure Portallal és az SQL Server Management Studióval történő használatának első lépései](sql-database-get-started.md) oktatóanyagot vagy az azzal egyenértékű [PowerShell-verziót](sql-database-get-started-powershell.md). Ha még nem tette, végezze el ezt az előfeltételként szolgáló oktatóanyagot, vagy hajtsa végre az oktatóanyag [PowerShell-verziójának](sql-database-get-started-powershell.md) végén található PowerShell-szkriptet a továbblépés előtt.


> [!NOTE]
> Ez az oktatóanyag a következő témakörök tartalmát segít elsajátítani: [SQL Database biztonsági mentések](sql-database-automated-backups.md), [Biztonsági másolat hosszú távú megőrzése](sql-database-long-term-retention.md) és [Azure SQL-adatbázis helyreállítása az automatikus biztonsági adatbázismentés használatával](sql-database-recovery-using-backups.md).
>  

## <a name="sign-in-to-the-azure-portal-using-your-azure-account"></a>Jelentkezzen be az Azure Portalra az Azure-fiókkal.
[Meglévő előfizetés](https://account.windowsazure.com/Home/Index) használata esetén kövesse az alábbi lépéseket az Azure Portalhoz való csatlakozáshoz.

1. Nyisson meg egy tetszőleges böngészőt, és csatlakozzon az [Azure Portalhoz](https://portal.azure.com/).
2. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
3. A **Bejelentkezés** oldalon adja meg az előfizetése hitelesítő adatait.
   
   ![Bejelentkezés](./media/sql-database-get-started/login.png)

<a name="create-logical-server-bk"></a>

## <a name="view-the-oldest-restore-point-from-the-service-generated-backups-of-a-database"></a>A legrégebbi visszaállítási pont megtekintése egy adatbázis szolgáltatás által létrehozott biztonsági másolatai közül

Az oktatóanyag ezen szakaszában az adatbázis [szolgáltatás által létrehozott automatikus biztonsági másolatai](sql-database-automated-backups.md) közül a legrégebbi visszaállítási pontra vonatkozó információkat tekintjük át. 

1. Kattintson az adatbázis (**sqldbtutorialdb**) **SQL Database** paneljére.

   ![új mintaadatbázis panel](./media/sql-database-get-started/new-sample-db-blade.png)

2. Az eszköztáron kattintson a **Visszaállítás** elemre.

   ![visszaállítási eszköztár](./media/sql-database-get-started-backup-recovery/restore-toolbar.png)

3. A Visszaállítás panelen tekintse át a legrégebbi visszaállítási pontot.

   ![legrégebbi visszaállítási pont](./media/sql-database-get-started-backup-recovery/oldest-restore-point.png)

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Adatbázis visszaállítása egy korábbi időpontra

Az oktatóanyag ezen szakaszában az adatbázist egy új adatbázisba állítja vissza egy adott időpontra.

1. Az adatbázis **Visszaállítás** paneljén tekintse át az új adatbázis alapértelmezett nevét, amelyik adatbázisba vissza szeretné állítani az adatbázist egy korábbi időpontra (a név a meglévő adatbázis neve egy időbélyeggel kiegészítve). A név a következő lépésekben megadott időpont alapján módosul.

   ![visszaállított adatbázis neve](./media/sql-database-get-started-backup-recovery/restored-database-name.png)

2. Kattintson a **naptár** ikonra a **Visszaállítási pont (UTC)** beviteli mezőben.

   ![visszaállítási pont](./media/sql-database-get-started-backup-recovery/restore-point.png)

2. A naptárban válasszon ki egy olyan dátumot, amely a megőrzési időtartamba esik.

   ![visszaállítási pont dátuma](./media/sql-database-get-started-backup-recovery/restore-point-date.png)

3. A **Visszaállítási pont (UTC)** beviteli mezőben adja meg az adott dátumon azt az időpontot, amelyikre vissza szeretné állítani az adatbázis adatait az adatbázis automatikus biztonsági másolatai közül.

   ![visszaállítási pont időpontja](./media/sql-database-get-started-backup-recovery/restore-point-time.png)

   >[!NOTE]
   >Az adatbázis neve módosult, és a kiválasztott dátumot és időpontot tükrözi. Nem módosíthatja a kiszolgálót, amelyikre az adott időpontra való visszaállítást végzi. Eltérő kiszolgálóra való visszaállításhoz használja a [georedundáns visszaállítást](sql-database-disaster-recovery.md#recover-using-geo-restore). Végezetül vegye figyelembe, hogy egy [rugalmas készletet](sql-database-elastic-jobs-overview.md) vagy másik tarifacsomagot is választhat a visszaállításhoz. 
   >

4. Kattintson az **OK** gombra az adatbázis korábbi időpontra, az új adatbázisba való visszaállításához.

5. Az eszköztáron kattintson az értesítési ikonra a visszaállítási feladat állapotának megtekintéséhez.

   ![visszaállítási feladat állapota](./media/sql-database-get-started-backup-recovery/restore-job-progress.png)

6. Miután a visszaállítási feladat befejeződött, nyissa meg az **SQL-adatbázisok** panelt az újonnan visszaállított adatbázis megtekintéséhez.

   ![visszaállított adatbázis](./media/sql-database-get-started-backup-recovery/restored-database.png)

> [!NOTE]
> Innen az SQL Server Management Studióval csatlakozhat a visszaállított adatbázishoz a szükséges feladatok végrehajtásához, például [egy adatelem kinyeréséhez a visszaállított adatbázisból a meglévő adatbázisba való beillesztés érdekében, vagy a meglévő adatbázis törléséhez és a visszaállított adatbázis átnevezéséhez a meglévő adatbázis nevére](sql-database-recovery-using-backups.md#point-in-time-restore).
>

## <a name="configure-long-term-retention-of-automated-backups-in-an-azure-recovery-services-vault"></a>Automatikus biztonsági mentések hosszú távú megőrzésének konfigurálása Azure helyreállítási tárban 

Az oktatóanyag ezen szakaszában [konfigurál egy Azure helyreállítási tárat az automatikus biztonsági másolatok megőrzésére](sql-database-long-term-retention.md) a szolgáltatásszint megőrzési idejénél hosszabb időtartamra. 


> [!TIP]
> A hosszú távon megőrzött biztonsági másolatok törlésével kapcsolatban lásd [a biztonsági másolatok hosszú távú megőrzésének PowerShell-lel történő kezelésével](sql-database-manage-long-term-backup-retention-powershell.md) foglalkozó témakört.
>

1. Nyissa meg a kiszolgáló (**sqldbtutorialserver**) **SQL Server** paneljét.

   ![sql server panel](./media/sql-database-get-started/sql-server-blade.png)

2. Kattintson a **Biztonsági másolat hosszú távú megőrzése** elemre.

   ![biztonsági másolat hosszú távú megőrzése hivatkozás](./media/sql-database-get-started-backup-recovery/long-term-backup-retention-link.png)

3. Az **sqldbtutorial – Biztonsági másolat hosszú távú megőrzése** panelen tekintse át és fogadja el az előzetes verziójú szolgáltatás feltételeit (kivéve, ha már korábban megtette, vagy a szolgáltatás már nem előzetes verziójú).

   ![előzetes verziójú szolgáltatás feltételeinek elfogadása](./media/sql-database-get-started-backup-recovery/accept-the-preview-terms.png)

4. Hosszú távú megőrzés konfigurálásához az sqldbtutorialdb számára jelölje ki az adatbázist a táblázatban, majd kattintson a **Konfigurálás** gombra az eszköztáron.

   ![adatbázis kijelölése biztonsági mentés hosszú távú megőrzéséhez](./media/sql-database-get-started-backup-recovery/select-database-for-long-term-backup-retention.png)

5. A **Konfigurálás** panel **Helyreállítási tár** területén kattintson a **Kötelező beállítások konfigurálása** elemre.

   ![tároló konfigurálása hivatkozás](./media/sql-database-get-started-backup-recovery/configure-vault-link.png)

6. A **Helyreállítási tár** panelen jelöljön ki egy meglévő tárolót, ha van ilyen. Ha nem találhatók helyreállítási tárak az előfizetésben, kattintással lépjen ki a folyamatból, és hozzon létre egyet.

   ![új tároló létrehozása hivatkozás](./media/sql-database-get-started-backup-recovery/create-new-vault-link.png)

7. A **Helyreállítási tárak** panelen kattintson a **Hozzáadás** elemre.

   ![új tároló hozzáadása hivatkozás](./media/sql-database-get-started-backup-recovery/add-new-vault-link.png)
   
8. A **Helyreállítási tár** panelen adjon meg egy érvényes nevet az új helyreállítási tár számára.

   ![új tároló neve](./media/sql-database-get-started-backup-recovery/new-vault-name.png)

9. Válassza ki az előfizetést és az erőforráscsoportot, majd válassza ki a tároló helyét. Ha befejezte, kattintson a **Létrehozás** elemre.

   ![új tároló létrehozása](./media/sql-database-get-started-backup-recovery/create-new-vault.png)

   > [!IMPORTANT]
   > A tárolónak ugyanabban a régióban kell lennie, mint az Azure SQL logikai kiszolgálónak, és ugyanazt az erőforráscsoportot kell használnia.
   >

10. Az új tároló létrehozása után hajtsa végre a szükséges lépéseket a **Helyreállítási tár** panelre való visszalépéshez.

11. A **Helyreállítási tár** panelen kattintson a tárolóra, majd a **Kiválasztás** elemre.

   ![meglévő tároló kiválasztása](./media/sql-database-get-started-backup-recovery/select-existing-vault.png)

12. A **Konfigurálás** panelen adjon meg egy érvényes nevet az új adatmegőrzési házirend számára, módosítsa igény szerint az alapértelmezett házirendet, majd kattintson az **OK** gombra.

   ![adatmegőrzési házirend definiálása](./media/sql-database-get-started-backup-recovery/define-retention-policy.png)

13. Az **sqldbtutorial – Biztonsági másolat hosszú távú megőrzése** panelen kattintson a **Mentés**, majd az **OK** gombra a hosszú távú adatmegőrzési házirend a kiválasztott adatbázisokra való alkalmazásához.

   ![adatmegőrzési házirend definiálása](./media/sql-database-get-started-backup-recovery/save-retention-policy.png)

14. Kattintson a **Mentés** gombra a hosszú távú adatmegőrzés engedélyezéséhez az új házirend használatával a konfigurált Azure helyreállítási táron.

   ![adatmegőrzési házirend definiálása](./media/sql-database-get-started-backup-recovery/enable-long-term-retention.png)

15. A biztonsági másolat hosszú távú megőrzésének engedélyezése után nyissa meg az **sqldbtutorialvault** panelt (lépjen az **Összes erőforrás** területre, és válassza ki azt az előfizetéséhez elérhető erőforrások listájából).

   ![helyreállítási tár megtekintése](./media/sql-database-get-started-backup-recovery/view-recovery-services-vault.png)

> [!IMPORTANT]
> A konfigurálást követően a biztonsági másolatok hét napon belül megjelennek a tárolóban. Ne folytassa az oktatóanyagot, amíg a biztonsági másolatok meg nem jelentek a tárolóban.
>

## <a name="view-backups-in-long-term-retention"></a>Biztonsági másolatok megtekintése hosszú távú megőrzés alatt

Az oktatóanyag ezen szakaszában az adatbázisok biztonsági másolataival kapcsolatos információkat tekint meg a [biztonsági másolatok hosszú távú megőrzése alatt](sql-database-long-term-retention.md). 

1. Nyissa meg az **sqldbtutorialvault** panelt (lépjen az **Összes erőforrás** területre, és válassza ki azt az előfizetéséhez elérhető erőforrások listájáról), ahol megtekintheti, hogy az adatbázisok a tárolóban lévő biztonsági másolatai mennyi tárterületet foglalnak.

   ![biztonsági másolatokat tartalmazó helyreállítási tár megtekintése](./media/sql-database-get-started-backup-recovery/view-recovery-services-vault-with-data.png)

2. Kattintson az adatbázis (**sqldbtutorialdb**) **SQL Database** paneljére.

   ![új mintaadatbázis panel](./media/sql-database-get-started/new-sample-db-blade.png)

3. Az eszköztáron kattintson a **Visszaállítás** elemre.

   ![visszaállítási eszköztár](./media/sql-database-get-started-backup-recovery/restore-toolbar.png)

4. A Visszaállítás panelen kattintson a **Hosszú távú** gombra.

5. Az Azure-tárolók biztonsági másolatai alatt kattintson a **Biztonsági másolat kiválasztása** gombra az adatbázisok a biztonsági másolatok hosszú távú megőrzése alatt álló biztonsági másolatainak megtekintéséhez.

   ![biztonsági másolatok a tárolóban](./media/sql-database-get-started-backup-recovery/view-backups-in-vault.png)

## <a name="restore-a-database-from-a-backup-in-long-term-backup-retention"></a>Adatbázis visszaállítása hosszú távú megőrzés alatt álló biztonsági másolatból

Az oktatóanyag ezen szakaszában az adatbázist egy új adatbázisba állítja helyre egy, az Azure helyreállítási tárban elérhető biztonsági másolatból.

1. Az **Azure-tárolók biztonsági másolatai** panelen kattintson a visszaállítani kívánt biztonsági másolatra, majd a **Kiválasztás** elemre.

   ![tárolóban lévő biztonsági másolat kiválasztása](./media/sql-database-get-started-backup-recovery/select-backup-in-vault.png)

2. Az **Adatbázis neve** szövegmezőben adjon meg egy nevet a visszaállított adatbázis számára.

   ![új adatbázis neve](./media/sql-database-get-started-backup-recovery/new-database-name.png)

3. Kattintson az **OK** gombra az adatbázis visszaállításához a tárolóban lévő biztonsági másolatból az új adatbázisba.

4. Az eszköztáron kattintson az értesítési ikonra a visszaállítási feladat állapotának megtekintéséhez.

   ![tárolóból való visszaállítási feladat állapota](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

5. Miután a visszaállítási feladat befejeződött, nyissa meg az **SQL-adatbázisok** panelt az újonnan visszaállított adatbázis megtekintéséhez.

   ![tárolóból visszaállított adatbázis](./media/sql-database-get-started-backup-recovery/restored-database-from-vault.png)

> [!NOTE]
> Innen az SQL Server Management Studióval csatlakozhat a visszaállított adatbázishoz a szükséges feladatok végrehajtásához, például [egy adatelem kinyeréséhez a visszaállított adatbázisból a meglévő adatbázisba való beillesztés érdekében, vagy a meglévő adatbázis törléséhez és a visszaállított adatbázis átnevezéséhez a meglévő adatbázis nevére](sql-database-recovery-using-backups.md#point-in-time-restore).
>

## <a name="next-steps"></a>Következő lépések

- A szolgáltatás által létrehozott automatikus biztonsági másolatokkal kapcsolatos további információkért lásd az [automatikus biztonsági másolatokkal](sql-database-automated-backups.md) foglalkozó témakört.
- A biztonsági másolatok hosszú távú megőrzésével kapcsolatos további információkért lásd: [biztonsági másolatok hosszú távú megőrzése](sql-database-long-term-retention.md)
- A biztonsági másolatokból való visszaállítással kapcsolatos további információkért lásd: [visszaállítás biztonsági másolatból](sql-database-recovery-using-backups.md)



<!--HONumber=Feb17_HO3-->



---
title: "Ismerkedés az Azure SQL Database-adatbázisok biztonsági mentésével és visszaállításával az adatok védelme és helyreállítása érdekében | Microsoft Docs"
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
ms.sourcegitcommit: 7f26cd0f6c5f9c7a2fe692bfcdc6ef60d1b2200f
ms.openlocfilehash: d4ea089ed4b5d29c261b25e95f4d304611f9a857


---
<!------------------
This topic is annotated with TEMPLATE guidelines for TUTORIAL TOPICS.


Metadata guidelines

title
    60 characters or less. Tells users clearly what they will do (deploy an ASP.NET web app to App Service). Not the same as H1. It's 60 characters or fewer including all characters between the quotes and the Microsoft Docs site identifier.

description
    115-145 characters. Duplicate of the first sentence in the introduction. This is the abstract of the article that displays under the title when searching in Bing or Google. 

    Example: "This tutorial shows how to deploy an ASP.NET web application to a web app in Azure App Service by using Visual Studio 2015."
------------------>

<!----------------

TEMPLATE GUIDELINES for tutorial topics

The tutorial topic shows users how to solve a problem using a product or service. It includes the prerequisites and steps users need to be successful.  

It is a "solve a problem" topic, not a "learn concepts" topic.

DO include this:
    • What users will do
    • What they will create or accomplish by the end of the tutorial
    • Time estimate
    • Optional but useful: Include a diagram or video. Diagrams help users see the big picture of what they are doing. A video of the steps can be used by customers as an alternative to following the steps in the topic.
    • Prerequisites: Technical expertise and software requirements
    • End-to-end steps. At the end, include next steps to deeper or related tutorials so users can learn more about the service

DON'T include this:
    • Conceptual info about the service. This info is in overview topics that you can link to in the prerequisites section if necessary

------------------->

<!------------------
GUIDELINES for the H1 
    
    The H1 should answer the question "What will I do in this topic?" Write the H1 heading in conversational language and use search keywords as much as possible. Since this is a "solve a problem" topic, make sure the title indicates that. Use a strong, specific verb like "Deploy."  
        
    Heading must use an industry standard term. If your feature is a proprietary name like "elastic pools", use a synonym. For example: "Learn about elastic pools for multi-tenant databases." In this case multi-tenant database is the industry-standard term that will be an anchor for finding the topic.

-------------------->

# <a name="get-started-with-backup-and-restore-for-data-protection-and-recovery"></a>Ismerkedés a biztonsági mentéssel és a visszaállítással az adatok védelme és helyreállítása érdekében

<!------------------
    GUIDELINES for introduction
    
    The introduction is 1-2 sentences.  It is optimized for search and sets proper expectations about what to expect in the article. It should contain the top keywords that you are using throughout the article.The introduction should be brief and to the point of what users will do and what they will accomplish. 

    In this example:
     

Sentence #1 Explains what the user will do. This is also the metadata description. 
    This tutorial shows how to deploy an ASP.NET web application to a web app in Azure App Service by using Visual Studio 2015. 

Sentence #2 Explains what users will learn and the benefit.  
    When you’re finished, you’ll have a simple web application up and running in the cloud.

-------------------->


Ez a kezdeti lépéseket ismertető oktatóanyag bemutatja, hogyan hajthatja végre a következő műveleteket az Azure Portalon:

- Adatbázisok meglévő biztonsági másolatainak megtekintése
- Adatbázis visszaállítása egy korábbi időpontra
- Adatbázisokról készült biztonsági mentések fájljai hosszú távú megőrzésének konfigurálása az Azure helyreállítási tárban
- Adatbázis visszaállítása az Azure helyreállítási tárból

**Becsült időtartam**: Az oktatóanyag teljesítése körülbelül 30 percet vesz igénybe (feltéve, hogy megfelel a szükséges előfeltételeknek).


## <a name="prerequisites"></a>Előfeltételek

* Rendelkeznie kell Azure-fiókkal. [Nyithat egy ingyenes Azure-fiókot](/pricing/free-trial/?WT.mc_id=A261C142F) vagy [aktiválhatja a Visual Studio előfizetői előnyeit](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). 

* Az előfizetés-tulajdonosi vagy közreműködői szerepkörhöz tartozó fiókok képesek csatlakozni az Azure Portalhoz. A szerepköralapú hozzáférés-vezérlésről (RBAC) többet is megtudhat az [Azure Portalon végzett hozzáférés-vezérlés alapvető tudnivalóit ismertető](../active-directory/role-based-access-control-what-is.md) témakörben.

* Ön elvégezte [Az Azure SQL Database-kiszolgálók, -adatbázisok és -tűzfalszabályok Azure Portallal és az SQL Server Management Studióval történő használatának első lépései](sql-database-get-started.md) oktatóanyagot vagy az azzal egyenértékű [PowerShell-verziót](sql-database-get-started-powershell.md). Ha még nem tette, végezze el ezt az előfeltételként szolgáló oktatóanyagot, vagy hajtsa végre az oktatóanyag [PowerShell-verziójának](sql-database-get-started-powershell.md) végén található PowerShell-szkriptet a továbblépés előtt.


> [!TIP]
> Ugyanezen feladatokat a [PowerShell](sql-database-get-started-backup-recovery-powershell.md) használatával is végrehajthatja egy, a kezdeti lépéseket ismertető oktatóanyagban.


## <a name="sign-in-by-using-your-existing-account"></a>Bejelentkezés meglévő fiókkal
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
> A biztonsági másolatok törléséről lásd a [hosszú távú megőrzési időtartamú biztonsági mentések törlésével](sql-database-long-term-retention-delete.md) foglalkozó témakört.


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


<!--**Next steps**: *Reiterate what users have done, and give them interesting and useful next steps so they want to go on.*-->

## <a name="next-steps"></a>Következő lépések

- A szolgáltatás által létrehozott automatikus biztonsági másolatokkal kapcsolatos további információkért lásd az [automatikus biztonsági másolatokkal](sql-database-automated-backups.md) foglalkozó témakört.
- A biztonsági másolatok hosszú távú megőrzésével kapcsolatos további információkért lásd: [biztonsági másolatok hosszú távú megőrzése](sql-database-long-term-retention.md)
- A biztonsági másolatokból való visszaállítással kapcsolatos további információkért lásd: [visszaállítás biztonsági másolatból](sql-database-recovery-using-backups.md)



<!--HONumber=Dec16_HO4-->



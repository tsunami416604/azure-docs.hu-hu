---
title: "A Stream Analytics: Elforgatása bejelentkezési hitelesítő adatok a be- és kimenetekkel |} Microsoft Docs"
description: "Útmutató a Stream Analytics bemenetekhez és kimenetekhez hitelesítő adatait."
keywords: "a bejelentkezési hitelesítő adatok"
services: stream-analytics
documentationcenter: 
author: SnehaGunda
manager: kfile
editor: cgronlun
ms.assetid: 42ae83e1-cd33-49bb-a455-a39a7c151ea4
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 01/11/2018
ms.author: sngun
ms.openlocfilehash: c1aded8fefc7b56acd2e9ff36bb2c9641665db76
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2018
---
# <a name="rotate-login-credentials-for-inputs-and-outputs-of-a-stream-analytics-job"></a>Bejelentkezési hitelesítő adatait a be- és a Stream Analytics-feladat kimenetének elforgatása

Amikor újragenerálja egy bemeneti vagy a Stream Analytics-feladat eredményének hitelesítő adataival, frissítenie kell a feladat új hitelesítő adatokkal. A hitelesítő adatok frissítése előtt le kell állítania a feladatot, a feladat futása közben a hitelesítő adatok nem cserélhető le. Leállítása és újraindítása a feladat közötti időeltérés csökkentése érdekében a Stream Analytics támogatja az utolsó kimenetből egy feladat folytatása. Ez a témakör ismerteti a folyamat végrehajtásával rotálhatóak a bejelentkezési hitelesítő adatokat, és új hitelesítő adatokkal a feladat újraindítása.

## <a name="regenerate-new-credentials-and-update-your-job-with-the-new-credentials"></a>Generálja újra az új hitelesítő adatokat, és frissítse a feladat az új hitelesítő adatok 

Ez a szakasz azt haladhat végig újragenerálása hitelesítő adatokat a Blob Storage tárolóban, az Event Hubs, SQL-adatbázis és a Table Storage. 

### <a name="blob-storagetable-storage"></a>A BLOB storage-tároló vagy tábla
1. Jelentkezzen be az Azure portál > keresse meg a tárfiók-adatforrásnevet bemeneti/kimeneti a Stream Analytics-feladathoz.    
2. Nyissa meg a beállítások szakaszban **hívóbetűk**. A két alapértelmezett kulcsok (key1, key2) között válassza ki azt, amelyik nem használják a feladatot, majd ismét:  
   ![A tárfiók kulcsainak újragenerálása](media/stream-analytics-login-credentials-inputs-outputs/image1.png)
3. Másolja ki az újonnan létrehozott kulcsot.    
4. Azure-portálról, keresse meg a Stream Analytics-feladat > Válasszon **leállítása** és várja meg a feladat leállítása.    
5. Keresse meg a Blob vagy tábla tárolási bemeneti/kimeneti legyen hitelesítő adatainak frissítése.    
6. Keresés a **Tárfiók kulcsa** mezőben, majd illessze be az újonnan létrehozott kulcs > kattintson **mentése**.    
7. A kapcsolat tesztelése automatikusan elindul, amikor a módosítások mentése, megtekintheti az értesítések lapon. Két értesítések egy az egyhez megfelel-e a frissítés mentése és egyéb megfelel-e a kapcsolat tesztelése:  
   ![A kulcs módosítása után értesítések](media/stream-analytics-login-credentials-inputs-outputs/image4.png)
8. [A feladat indítása leállított utoljára] folytassa (#start-your-job-from-the-last-stopped-time) szakaszban.

### <a name="event-hubs"></a>Event Hubs

1. Jelentkezzen be az Azure portál > keresse meg az Event Hubs-adatforrásnevet bemeneti/kimeneti a Stream Analytics-feladathoz.    
2. Nyissa meg a beállítások szakaszban **megosztott elérési házirendek** és válassza ki a hozzáférési házirendet. Között a **elsődleges kulcs** és **másodlagos kulcs**, válassza ki azt, amelyik nem használják a feladatot, majd ismét:  
   ![Az Event Hubs kulcsok újragenerálása](media/stream-analytics-login-credentials-inputs-outputs/image2.png)
3. Másolja ki az újonnan létrehozott kulcsot.    
4. Azure-portálról, keresse meg a Stream Analytics-feladat > Válasszon **leállítása** és várja meg a feladat leállítása.    
5. Keresse meg a Event hubs bemeneti/kimeneti legyen hitelesítő adatainak frissítése.    
6. Keresés a **Event Hub házirend kulcs** mezőben, majd illessze be az újonnan létrehozott kulcs > kattintson **mentése**.    
7. A kapcsolat tesztelése automatikusan elindul, amikor menti a módosításokat, győződjön meg arról, hogy azt sikeresen megfelelt.    
8. Folytassa a [indítsa el a feladat leállt utoljára](#start-your-job-from-the-last-stopped-time) szakasz.

### <a name="sql-database"></a>SQL Database

Egy meglévő felhasználó bejelentkezési hitelesítő adatok frissítése az SQL-adatbázishoz való kapcsolódáshoz szükséges. Hitelesítő adatok Azure-portálon vagy a megfelelő ügyféloldali eszköz, például az SQL Server Management Studio használatával frissítheti. Ez a szakasz azt mutatja be hitelesítő adatok frissítését az Azure portál használatával.

1. Jelentkezzen be az Azure portál > keresse meg az SQL-adatbázis, amelyet a Stream Analytics-feladat kimeneti használni.    
2. A **adatkezelő**, bejelentkezés/kapcsolódás saját adatbázishoz > hitelesítési típus kiválasztása **SQL server-hitelesítés** > írja be a **bejelentkezési** és  **Jelszó** Részletek > Válasszon **Ok**.  
   ![SQL-adatbázis hitelesítő adatok újbóli létrehozása](media/stream-analytics-login-credentials-inputs-outputs/image3.png)

3. A lekérdezés lap alter egyet a felhasználó jelszavát a következő lekérdezés futtatásával (Győződjön meg arról, hogy `<user_name>` ezzel a felhasználónévvel és `<new_password>` az új jelszóval):  

   ```SQL
   Alter user `<user_name>` WITH PASSWORD = '<new_password>'
   Alter role db_owner Add member `<user_name>`
   ```

4. Jegyezze fel az új jelszót.    
5. Azure-portálról, keresse meg a Stream Analytics-feladat > Válasszon **leállítása** és várja meg a feladat leállítása.    
6. Keresse meg az SQL adatbázis kimeneti legyen elforgatása hitelesítő adatokat. Frissítse a jelszavát, és a módosítások mentéséhez.    
7. A kapcsolat tesztelése automatikusan elindul, amikor menti a módosításokat, győződjön meg arról, hogy azt sikeresen megfelelt.    
8. Folytassa a [indítsa el a feladat leállt utoljára](#start-your-job-from-the-last-stopped-time) szakasz.

### <a name="power-bi"></a>Power BI
1. Jelentkezzen be az Azure portál > keresse meg a Stream Analytics-feladat > Válasszon **leállítása** és várja meg a feladat leállítása.    
2. Keresse meg a legyen megújítani hitelesítő adatait a Power BI kimeneti > kattintson a **újra a portálon** (megjelenik a sikerről szóló üzenetet) > **mentése** a módosításokat.    
3. A kapcsolat tesztelése automatikusan elindul, amikor a módosítások mentése, győződjön meg arról, hogy sikeresen megfelelt.    
4. Folytassa a [indítsa el a feladat leállt utoljára](#start-your-job-from-the-last-stopped-time) szakasz.

## <a name="start-your-job-from-the-last-stopped-time"></a>Indítsa el a feladat leállt utoljára

1. Keresse meg a feladat **áttekintése** ablaktábla > Válasszon **Start** elindítja a feladatot.    
2. Válassza ki **utolsó leállításakor** > kattintson **Start**. Vegye figyelembe, hogy a "Ha az utolsó Leállítás" lehetőség csak akkor jelenik meg, ha korábban a feladat lefutott, és néhány kimeneti volna létre. A feladat a legutóbbi kimeneti érték idő alapján újraindult.
   ![Indítsa el a feladatot](media/stream-analytics-login-credentials-inputs-outputs/image5.png)

## <a name="next-steps"></a>További lépések
* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)

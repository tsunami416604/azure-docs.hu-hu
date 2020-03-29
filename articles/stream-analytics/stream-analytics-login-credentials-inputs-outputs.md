---
title: Bejelentkezési hitelesítő adatok elforgatása az Azure Stream Analytics-feladatokban
description: Ez a cikk ismerteti, hogyan frissítheti a bemenetek és a kimeneti fogadók az Azure Stream Analytics-feladatok hitelesítő adatait.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: 3ae639dd7c5a42fc6880240988f0fb2817b09f43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75425976"
---
# <a name="rotate-login-credentials-for-inputs-and-outputs-of-a-stream-analytics-job"></a>A Stream Analytics-feladat bemeneteinek és kimeneteinek bejelentkezési hitelesítő adatainak elforgatása

Amikor újragenerálja a hitelesítő adatokat egy Stream Analytics-feladat bemeneti vagy kimeneti, frissítenie kell a feladatot az új hitelesítő adatokkal. Le kell állítania a feladatot a hitelesítő adatok frissítése előtt, nem helyettesítheti a hitelesítő adatokat, amíg a feladat fut. A feladat leállítása és újraindítása közötti késés csökkentése érdekében a Stream Analytics támogatja a feladat utolsó kimenetről való folytatását. Ez a témakör a bejelentkezési hitelesítő adatok elforgatásának és a feladat új hitelesítő adatokkal történő újraindításának folyamatát ismerteti.

## <a name="regenerate-new-credentials-and-update-your-job-with-the-new-credentials"></a>Új hitelesítő adatok újragenerálása és a feladat frissítése az új hitelesítő adatokkal 

Ebben a szakaszban bemutatjuk a Blob Storage, az Event Hubs, az SQL Database és a Table Storage hitelesítő adatainak regenerálását. 

### <a name="blob-storagetable-storage"></a>Blob-tároló/táblatároló
1. Jelentkezzen be az Azure Portalon, > böngésszen a Stream Analytics-feladat bemeneti/kimeneti ként használt tárfiókban.    
2. A beállítások szakaszban nyissa meg az **Access-billentyűket**. A két alapértelmezett kulcs (key1, key2) között válassza ki azt, amelyiket a feladat nem használja, és hozza létre újra:  
   ![A tárfiók kulcsainak újragenerálása](media/stream-analytics-login-credentials-inputs-outputs/regenerate-storage-keys.png)
3. Másolja az újonnan létrehozott kulcsot.    
4. Az Azure Portalon keresse meg a Stream Analytics-feladatot, > válassza **a Leállítás** lehetőséget, és várja meg, amíg leáll a feladat.    
5. Keresse meg azt a Blob/Table storage bemeneti/kimeneti, amelyhez frissíteni szeretné a hitelesítő adatokat.    
6. Keresse meg a **Tárfiókkulcs** mezőt, és illessze be az újonnan létrehozott kulcsot, > kattintson a **Mentés gombra.**    
7. A kapcsolatteszt automatikusan elindul a módosítások mentésekor, és az értesítések lapon tekintheti meg. Két értesítés van: az egyik megfelel a frissítés mentésének, a másik pedig a kapcsolat tesztelésének:  
   ![Értesítések a kulcs szerkesztése után](media/stream-analytics-login-credentials-inputs-outputs/edited-key-notifications.png)
8. Folytassa [a feladat elindításával az utoljára leállított idő szakaszból.](#start-your-job-from-the-last-stopped-time)

### <a name="event-hubs"></a>Event Hubs

1. Jelentkezzen be az Azure Portalon, > böngésszen a Stream Analytics-feladat bemeneti/kimeneti ként használt Event Hub között.    
2. A beállítások szakaszban nyissa meg a **Megosztott hozzáférési házirendeket,** és válassza ki a szükséges hozzáférési szabályzatot. Az **elsődleges kulcs** és a **másodlagos kulcs**között válassza ki azt, amelyik nem a feladat által használt, és hozza létre újra:  
   ![Az Eseményközpontok kulcsainak újragenerálása](media/stream-analytics-login-credentials-inputs-outputs/regenerate-event-hub-keys.png)
3. Másolja az újonnan létrehozott kulcsot.    
4. Az Azure Portalon keresse meg a Stream Analytics-feladatot, > válassza **a Leállítás** lehetőséget, és várja meg, amíg leáll a feladat.    
5. Keresse meg az eseményközpontok bemeneti/kimeneti bemeneti/kimeneti adatait, amelyek hitelesítő adatait frissíteni szeretné.    
6. Keresse meg az **Eseményközpont házirendkulcs** mezőjét, és illessze be az újonnan létrehozott kulcsot > kattintson a **Mentés gombra.**    
7. A kapcsolatteszt automatikusan elindul, amikor menti a módosításokat, és győződjön meg arról, hogy sikeresen átesett.    
8. Folytassa [a feladat elindításával az utoljára leállított idő szakaszból.](#start-your-job-from-the-last-stopped-time)

### <a name="sql-database"></a>SQL Database

Egy meglévő felhasználó bejelentkezési hitelesítő adatainak frissítéséhez csatlakoznia kell az SQL-adatbázishoz. A hitelesítő adatokat az Azure Portal vagy egy ügyféloldali eszköz, például az SQL Server Management Studio használatával frissítheti. Ez a szakasz bemutatja a hitelesítő adatok frissítésének folyamatát az Azure Portal használatával.

1. Jelentkezzen be az Azure Portalon, > böngésszen a Stream Analytics-feladat kimeneteként használt SQL-adatbázisban.    
2. Az **Adatkezelőben**jelentkezzen be/csatlakozzon az adatbázishoz, > válassza az Engedélyezés típusát **SQL-kiszolgáló hitelesítéseként** > írja be a **bejelentkezési** és **jelszóadatokat** > válassza az **Ok lehetőséget.**  
   ![Hitelesítő adatok újragenerálása SQL-adatbázishoz](media/stream-analytics-login-credentials-inputs-outputs/regenerate-sql-credentials.png)

3. A lekérdezés lapon módosítsa a felhasználó jelszavát a következő lekérdezés futtatásával (győződjön meg róla, hogy lecseréli `<user_name>` a felhasználónevét és `<new_password>` az új jelszót):  

   ```SQL
   Alter user `<user_name>` WITH PASSWORD = '<new_password>'
   Alter role db_owner Add member `<user_name>`
   ```

4. Jegyezze fel az új jelszót.    
5. Az Azure Portalon keresse meg a Stream Analytics-feladatot, > válassza **a Leállítás** lehetőséget, és várja meg, amíg leáll a feladat.    
6. Keresse meg azt az SQL-adatbázis-kimenetet, amelynek hitelesítő adatait el szeretné forgatni. Frissítse a jelszót, és mentse a módosításokat.    
7. A kapcsolatteszt automatikusan elindul, amikor menti a módosításokat, és győződjön meg arról, hogy sikeresen átesett.    
8. Folytassa [a feladat elindításával az utoljára leállított idő szakaszból.](#start-your-job-from-the-last-stopped-time)

### <a name="power-bi"></a>Power BI
1. Jelentkezzen be az Azure Portalon, > böngésszen a Stream Analytics-feladatban, > válassza a **Leállítás** lehetőséget, és várja meg, amíg leáll a feladat.    
2. Keresse meg azt a Power BI-kimenetet, amelynek hitelesítő adatait meg szeretné újítani, > kattintson az **Engedélyezés megújítása** (sikeres üzenet jelenik meg) > A módosítások **mentése.**    
3. A csatlakozási teszt automatikusan elindul, amikor menti a módosításokat, és győződjön meg arról, hogy sikeresen átesett.    
4. Folytassa [a feladat elindításával az utoljára leállított idő szakaszból.](#start-your-job-from-the-last-stopped-time)

## <a name="start-your-job-from-the-last-stopped-time"></a>A feladat indítása az utolsó leállított időponttól

1. Nyissa meg a feladat **áttekintése** ablaktáblát, > válassza az **Indítás** lehetőséget a feladat elindításához.    
2. Válassza **a Mikor leállított utolsó >** kattintson a Start **gombra.** Ne feledje, hogy az "Utolsó leállításkor" lehetőség csak akkor jelenik meg, ha korábban futtatta a feladatot, és generált néhány kimenetet. A feladat az utolsó kimeneti érték ideje alapján újraindul.
   ![A Stream Analytics feladat indítása](media/stream-analytics-login-credentials-inputs-outputs/start-stream-analytics-job.png)

## <a name="next-steps"></a>További lépések
* [Bevezetés az Azure Stream Analytics szolgáltatásba](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs (Azure Stream Analytics-feladatok méretezése)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Referencia az Azure Stream Analytics lekérdezési nyelvhez)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)

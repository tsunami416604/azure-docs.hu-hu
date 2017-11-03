---
title: "A Stream Analytics: Elforgatása napló hitelesítő adataival bemenetekhez és kimenetekhez |} Microsoft Docs"
description: "Útmutató a Stream Analytics bemenetekhez és kimenetekhez hitelesítő adatait."
keywords: "a bejelentkezési hitelesítő adatok"
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 42ae83e1-cd33-49bb-a455-a39a7c151ea4
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: a1a927fa9c34b38e54fdb22782e80fd13bf430c7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="rotate-login-credentials-for-inputs-and-outputs-in-stream-analytics-jobs"></a>Forgassa el a be- és kimenetekkel a Stream Analytics-feladatok bejelentkezési hitelesítő adatok
## <a name="abstract"></a>Absztrakt
Az Azure Stream Analytics jelenleg nem engedélyezi a felhasználó hitelesítő adatai egy bemeneti/kimeneti cseréje a feladat futása közben.

Azure Stream Analytics nem támogatja az utolsó kimenetből egy feladat folytatása, amíg meg akartunk ahhoz, hogy minimalizálja a leállítási közötti időeltérés és a feladat elindítása és elforgatása a bejelentkezési hitelesítő adatok a teljes folyamatot.

## <a name="part-1---prepare-the-new-set-of-credentials"></a>1. rész - készítse elő az újonnan létrehozott hitelesítő adatokat:
Ez a kijelző tulajdonság a következő bemenetek/kimenetek vonatkozik:

* Blob Storage
* Event Hubs
* SQL Database
* Table Storage

Az egyéb bemenetek/kimenetek folytassa a 2. rész.

### <a name="blob-storagetable-storage"></a>A BLOB storage-tároló vagy tábla
1. A tárolási kiterjesztés nyissa meg az Azure felügyeleti portálra:  
   ![graphic1][graphic1]
2. Keresse meg a tároló, a feladat által használt, és lépjen be:  
   ![graphic2][graphic2]
3. Kattintson a elérési kulcsok kezelése parancsot:  
   ![graphic3][graphic3]
4. Az elsődleges elérési kulcsot és a másodlagos hozzáférési kulcsot közötti **válassza ki a feladat nem használja azt**.
5. Találati újragenerálása:  
   ![graphic4][graphic4]
6. Másolja ki az újonnan létrehozott kulcsot:  
   ![graphic5][graphic5]
7. Folytassa a 2.

### <a name="event-hubs"></a>Az Event hubs
1. Nyissa meg az Azure felügyeleti portálra a Service Bus-bővítmény:  
   ![graphic6][graphic6]
2. Keresse meg a Service Bus Namespace a feladat által használt, és lépjen be:  
   ![graphic7][graphic7]
3. Ha a feladat egy megosztott elérési házirendet használja a Service Bus Namespace, Ugrás a 6. lépés  
4. Ugrás az Event Hubs lapon:  
   ![graphic8][graphic8]
5. Keresse meg a feladat által használt Eseményközpont, és lépjen be:  
   ![graphic9][graphic9]
6. Ugrás a konfigurálása lapon:  
   ![graphic10][graphic10]
7. A házirendnév legördülő menüben keresse meg a megosztott elérési házirendet, a feladat használja:  
   ![graphic11][graphic11]
8. Az elsődleges és a másodlagos kulcs közötti **válassza ki a feladat nem használja azt**.  
9. Találati újragenerálása:  
   ![graphic12][graphic12]
10. Másolja ki az újonnan létrehozott kulcsot:  
   ![graphic13][graphic13]
11. Folytassa a 2.  

### <a name="sql-database"></a>SQL Database
> [!NOTE]
> Megjegyzés: szüksége lesz a SQL Database szolgáltatáshoz való kapcsolódáshoz. Fogjuk mutatják be ehhez a felügyeleti élmény használja az Azure felügyeleti portálon, de néhány ügyféloldali eszköz például az SQL Server Management Studio is választhatja, hogy.
>
> 

1. Az SQL-adatbázis bővítmény nyissa meg az Azure felügyeleti portálra:  
   ![graphic14][graphic14]
2. Keresse meg a feladat által használt SQL-adatbázis és **a kiszolgálón kattintson** ugyanazon hivatkozásra:  
   ![graphic15][graphic15]
3. Kattintson a kezelés parancsot:  
   ![graphic16][graphic16]
4. Adatbázis fő típusa:  
   ![graphic17][graphic17]
5. Írja be a felhasználónevét és jelszavát, és kattintson a napló a:  
   ![graphic18][graphic18]
6. Kattintson az új lekérdezés:  
   ![graphic19][graphic19]
7. Írja be a következő lekérdezést a < login_name > lecserélését a felhasználó nevét, és lecserél <enterStrongPasswordHere> az új jelszóval:  
   `CREATE LOGIN <login_name> WITH PASSWORD = '<enterStrongPasswordHere>'`
8. Kattintson a Futtatás gombra:  
   ![graphic20][graphic20]
9. Vissza a lépés 2 és az idő kattintson az adatbázisban:  
   ![graphic21][graphic21]
10. Kattintson a kezelés parancsot:  
   ![graphic22][graphic22]
11. Írja be a felhasználónevét és jelszavát, és kattintson a bejelentkezés:  
   ![graphic23][graphic23]
12. Kattintson az új lekérdezés:  
   ![graphic24][graphic24]
13. Adja meg a következő lekérdezés < felhasználónév > lecserélését egy nevet, amely szerint ehhez a bejelentkezéshez (biztosíthat < login_name >, például adott ugyanarra az értékre) adatbázis környezetében azonosítani szeretné és lecserél < login_name > az új felhasználónév:  
   `CREATE USER <user_name> FROM LOGIN <login_name>`
14. Kattintson a Futtatás gombra:  
   ![graphic25][graphic25]
15. Az új felhasználó most már azonos a szerepkörök és az eredeti felhasználó rendelkezik-e jogosultsággal kell biztosítania.
16. Folytassa a 2.

## <a name="part-2-stopping-the-stream-analytics-job"></a>2. lépés: A Stream Analytics-feladat leállítása
1. Nyissa meg az Azure felügyeleti portálra a Stream Analytics-bővítmény:  
   ![graphic26][graphic26]
2. Keresse meg a feladat, és lépjen be:  
   ![graphic27][graphic27]
3. Nyissa meg a bemeneti adatok vagy az alapján, hogy a hitelesítő adatok bemenettel vagy kimenettel vannak elforgatása kimenetek lapra.  
   ![graphic28][graphic28]
4. Kattintson a leállítási parancsot, és erősítse meg a feladat leállt:  
   ![graphic29][graphic29] várja meg a feladat leállítása.
5. Keresse meg a bemeneti/kimeneti forgassa el a hitelesítő adatokat a, és lépjen be:  
   ![graphic30][graphic30]
6. Folytassa a 3.

## <a name="part-3-editing-the-credentials-on-the-stream-analytics-job"></a>3. lépés: A Stream Analytics-feladat a hitelesítő adatok szerkesztése
### <a name="blob-storagetable-storage"></a>A BLOB storage-tároló vagy tábla
1. A Tárfiók kulcsa mező található, és az újonnan létrehozott kulcs beillesztése:  
   ![graphic31][graphic31]
2. Kattintson a Mentés parancsot, és ellenőrizze, hogy a módosítások mentése:  
   ![graphic32][graphic32]
3. A kapcsolat tesztelése automatikusan elindul, amikor menti a módosításokat, győződjön meg arról, amely sikeresen megfelelt.
4. Folytassa a 4.

### <a name="event-hubs"></a>Az Event hubs
1. Az Event Hub házirend kulcs mező található, és az újonnan létrehozott kulcs beillesztése:  
   ![graphic33][graphic33]
2. Kattintson a Mentés parancsot, és ellenőrizze, hogy a módosítások mentése:  
   ![graphic34][graphic34]
3. A kapcsolat tesztelése automatikusan elindul, amikor menti a módosításokat, győződjön meg arról, hogy azt sikeresen megfelelt.
4. Folytassa a 4.

### <a name="power-bi"></a>Power BI
1. Kattintson a megújítás engedélyezése:  

   ![graphic35][graphic35]
2. A következő megerősítő jelenik meg:  

   ![graphic36][graphic36]
3. Kattintson a Mentés parancsot, és ellenőrizze, hogy a módosítások mentése:  
   ![graphic37][graphic37]
4. A kapcsolat tesztelése automatikusan elindul, amikor a módosítások mentése, győződjön meg arról, hogy sikeresen megfelelt.
5. Folytassa a 4.

### <a name="sql-database"></a>SQL Database
1. A felhasználónév és a jelszó mező található, és illessze be az újonnan létrehozott hitelesítőadat-készletet a őket:  
   ![graphic38][graphic38]
2. Kattintson a Mentés parancsot, és ellenőrizze, hogy a módosítások mentése:  
   ![graphic39][graphic39]
3. A kapcsolat tesztelése automatikusan elindul, amikor menti a módosításokat, győződjön meg arról, hogy azt sikeresen megfelelt.  
4. Folytassa a 4.

## <a name="part-4-starting-your-job-from-last-stopped-time"></a>4. lépés: A feladat kiindulva leállított legutóbbi
1. A bemeneti/kimeneti elhagyni:  
   ![graphic40][graphic40]
2. Kattintson az indítási parancsot:  
   ![graphic41][graphic41]
3. Válassza ki a feladat utolsó befejezési időpontja, és kattintson az OK gombra:  
   ![graphic42][graphic42]
4. Folytassa az 5.  

## <a name="part-5-removing-the-old-set-of-credentials"></a>5. lépés: Régi megadott hitelesítői eltávolítása
Ez a kijelző tulajdonság a következő bemenetek/kimenetek vonatkozik:

* Blob Storage
* Event Hubs
* SQL Database
* Table Storage

### <a name="blob-storagetable-storage"></a>A BLOB storage-tároló vagy tábla
Ismételje meg az 1. rész az elérési kulcsot, amely korábban a használt a feladat már nem használt a hozzáférési kulcs megújításához.

### <a name="event-hubs"></a>Az Event hubs
Ismételje meg a feladat már nem használt kulcs megújításához korábban használt kulcs 1. rész.

### <a name="sql-database"></a>SQL Database
1. A rész 1 7. lépés és írja be a következő lekérdezés < previous_login_name > lecserélését a felhasználó nevét, a feladat által korábban használt lépjen vissza a lekérdezési ablakban:  
   `DROP LOGIN <previous_login_name>`  
2. Kattintson a Futtatás gombra:  
   ![graphic43][graphic43]  

A következő megerősítő szerezheti be: 

    Command(s) completed successfully.

## <a name="get-help"></a>Segítségkérés
További támogatásért keresse fel az [Azure Stream Analytics-fórumot](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Következő lépések
* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)

[graphic1]: ./media/stream-analytics-login-credentials-inputs-outputs/1-stream-analytics-login-credentials-inputs-outputs.png
[graphic2]: ./media/stream-analytics-login-credentials-inputs-outputs/2-stream-analytics-login-credentials-inputs-outputs.png
[graphic3]: ./media/stream-analytics-login-credentials-inputs-outputs/3-stream-analytics-login-credentials-inputs-outputs.png
[graphic4]: ./media/stream-analytics-login-credentials-inputs-outputs/4-stream-analytics-login-credentials-inputs-outputs.png
[graphic5]: ./media/stream-analytics-login-credentials-inputs-outputs/5-stream-analytics-login-credentials-inputs-outputs.png
[graphic6]: ./media/stream-analytics-login-credentials-inputs-outputs/6-stream-analytics-login-credentials-inputs-outputs.png
[graphic7]: ./media/stream-analytics-login-credentials-inputs-outputs/7-stream-analytics-login-credentials-inputs-outputs.png
[graphic8]: ./media/stream-analytics-login-credentials-inputs-outputs/8-stream-analytics-login-credentials-inputs-outputs.png
[graphic9]: ./media/stream-analytics-login-credentials-inputs-outputs/9-stream-analytics-login-credentials-inputs-outputs.png
[graphic10]: ./media/stream-analytics-login-credentials-inputs-outputs/10-stream-analytics-login-credentials-inputs-outputs.png
[graphic11]: ./media/stream-analytics-login-credentials-inputs-outputs/11-stream-analytics-login-credentials-inputs-outputs.png
[graphic12]: ./media/stream-analytics-login-credentials-inputs-outputs/12-stream-analytics-login-credentials-inputs-outputs.png
[graphic13]: ./media/stream-analytics-login-credentials-inputs-outputs/13-stream-analytics-login-credentials-inputs-outputs.png
[graphic14]: ./media/stream-analytics-login-credentials-inputs-outputs/14-stream-analytics-login-credentials-inputs-outputs.png
[graphic15]: ./media/stream-analytics-login-credentials-inputs-outputs/15-stream-analytics-login-credentials-inputs-outputs.png
[graphic16]: ./media/stream-analytics-login-credentials-inputs-outputs/16-stream-analytics-login-credentials-inputs-outputs.png
[graphic17]: ./media/stream-analytics-login-credentials-inputs-outputs/17-stream-analytics-login-credentials-inputs-outputs.png
[graphic18]: ./media/stream-analytics-login-credentials-inputs-outputs/18-stream-analytics-login-credentials-inputs-outputs.png
[graphic19]: ./media/stream-analytics-login-credentials-inputs-outputs/19-stream-analytics-login-credentials-inputs-outputs.png
[graphic20]: ./media/stream-analytics-login-credentials-inputs-outputs/20-stream-analytics-login-credentials-inputs-outputs.png
[graphic21]: ./media/stream-analytics-login-credentials-inputs-outputs/21-stream-analytics-login-credentials-inputs-outputs.png
[graphic22]: ./media/stream-analytics-login-credentials-inputs-outputs/22-stream-analytics-login-credentials-inputs-outputs.png
[graphic23]: ./media/stream-analytics-login-credentials-inputs-outputs/23-stream-analytics-login-credentials-inputs-outputs.png
[graphic24]: ./media/stream-analytics-login-credentials-inputs-outputs/24-stream-analytics-login-credentials-inputs-outputs.png
[graphic25]: ./media/stream-analytics-login-credentials-inputs-outputs/25-stream-analytics-login-credentials-inputs-outputs.png
[graphic26]: ./media/stream-analytics-login-credentials-inputs-outputs/26-stream-analytics-login-credentials-inputs-outputs.png
[graphic27]: ./media/stream-analytics-login-credentials-inputs-outputs/27-stream-analytics-login-credentials-inputs-outputs.png
[graphic28]: ./media/stream-analytics-login-credentials-inputs-outputs/28-stream-analytics-login-credentials-inputs-outputs.png
[graphic29]: ./media/stream-analytics-login-credentials-inputs-outputs/29-stream-analytics-login-credentials-inputs-outputs.png
[graphic30]: ./media/stream-analytics-login-credentials-inputs-outputs/30-stream-analytics-login-credentials-inputs-outputs.png
[graphic31]: ./media/stream-analytics-login-credentials-inputs-outputs/31-stream-analytics-login-credentials-inputs-outputs.png
[graphic32]: ./media/stream-analytics-login-credentials-inputs-outputs/32-stream-analytics-login-credentials-inputs-outputs.png
[graphic33]: ./media/stream-analytics-login-credentials-inputs-outputs/33-stream-analytics-login-credentials-inputs-outputs.png
[graphic34]: ./media/stream-analytics-login-credentials-inputs-outputs/34-stream-analytics-login-credentials-inputs-outputs.png
[graphic35]: ./media/stream-analytics-login-credentials-inputs-outputs/35-stream-analytics-login-credentials-inputs-outputs.png
[graphic36]: ./media/stream-analytics-login-credentials-inputs-outputs/36-stream-analytics-login-credentials-inputs-outputs.png
[graphic37]: ./media/stream-analytics-login-credentials-inputs-outputs/37-stream-analytics-login-credentials-inputs-outputs.png
[graphic38]: ./media/stream-analytics-login-credentials-inputs-outputs/38-stream-analytics-login-credentials-inputs-outputs.png
[graphic39]: ./media/stream-analytics-login-credentials-inputs-outputs/39-stream-analytics-login-credentials-inputs-outputs.png
[graphic40]: ./media/stream-analytics-login-credentials-inputs-outputs/40-stream-analytics-login-credentials-inputs-outputs.png
[graphic41]: ./media/stream-analytics-login-credentials-inputs-outputs/41-stream-analytics-login-credentials-inputs-outputs.png
[graphic42]: ./media/stream-analytics-login-credentials-inputs-outputs/42-stream-analytics-login-credentials-inputs-outputs.png
[graphic43]: ./media/stream-analytics-login-credentials-inputs-outputs/43-stream-analytics-login-credentials-inputs-outputs.png


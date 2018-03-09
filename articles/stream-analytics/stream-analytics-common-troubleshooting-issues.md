---
title: "Az Azure Stream Analytics helytelen formátumú bemeneti eseményeket hibaelhárítása |} Microsoft Docs"
description: "Honnan tudhatom, hogy melyik esemény szerepel a bemeneti adatok okozza a problémát a Stream Analytics-feladatok"
keywords: 
documentationcenter: 
services: stream-analytics
author: SnehaGunda
manager: Kfile
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/05/2018
ms.author: sngun
ms.openlocfilehash: 6b6c154568fe97b7495ae70dc162dc475169afea
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="common-issues-in-stream-analytics-and-steps-to-troubleshoot"></a>Gyakori problémák hibaelhárítása a Stream Analytics és a lépéseket a

## <a name="troubleshoot-for-malformed-input-events"></a>Helytelen formátumú bemeneti események hibaelhárítása

Ha a bemeneti adatfolyam a Stream Analytics-feladat nem megfelelő alakú üzenetek tartalmaz, szerializálási problémákat okoz. Nem megfelelő alakú üzenetek közé tartoznak például a hiányzó zárójel helytelen szerializációs JSON-objektumból, vagy helytelen időformátum stamp A Stream Analytics-feladat helytelenül formázott üzenetet kap, amikor elutasítja azokat az üzenetet, és értesíti a felhasználót egy figyelmeztetést. Figyelmeztetés szimbólum jelenik meg a **bemenetek** csempe a Stream Analytics-feladat (Ez a figyelmeztetés bejelentkezési létezik mindaddig, amíg a feladat nem fut,):

![Bemenetek csempe](media/stream-analytics-malformed-events/inputs_tile.png)

Engedélyezheti a diagnosztikai naplókat a figyelmeztetés részletes adatainak megtekintéséhez. Malformatted bemeneti események, a végrehajtási naplót tartalmaz bejegyzést, amely a következőképpen néz. üzenet: "üzenet: nem sikerült deszerializálni a bemeneti esemény erőforrásból <blob URI> json-ként)". 

### <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

1. Keresse meg a bemeneti csempe, és kattintson ide a figyelmeztetéseket.
2. A bemeneti részletek csempe információkhoz juthat a problémáról a figyelmeztetések megjelenítése. Az alábbiakban látható egy példa figyelmeztető üzenet, a figyelmeztető üzenetet jeleníti meg, a partíció, az eltolás és a sorozatszámok nem megfelelően formázott JSON-adatok esetén. 

   ![Figyelmeztető üzenet eltolású](media/stream-analytics-malformed-events/warning_message_with_offset.png)

3. Ahhoz, hogy a JSON-adatokat, amelyek formátuma nem megfelelő, a CheckMalformedEvents.cs kódot futtatni, akkor létrehozhat az informatikai a [minták GitHub-tárházban](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). Ezzel a kóddal olvassa be a partíció azonosítója, eltolás:, adott eltolás található adatok kinyomtatása. 

4. Ha olvasni az adatokat, elemezheti és javítsa ki a szerializálási formátum. 

## <a name="handling-duplicate-records-when-using-azure-sql-database-as-output-for-a-stream-analytics-job"></a>Ismétlődő rekordok kezelése az Azure SQL Database használata kimenetként a Stream Analytics-feladat

Azure SQL-adatbázis egy Stream Analytics-feladat kimeneti konfigurálásakor tömeges beilleszti rögzíti a céltáblába történő. Általában az Azure stream analytics biztosítja, hogy az [legalább egyszer kézbesítési]( https://msdn.microsoft.com/azure/stream-analytics/reference/event-delivery-guarantees-azure-stream-analytics) a kimeneti fogadóját egy is még [pontosan elérése-egyszer kézbesítési]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) SQL kimeneti Ha SQL táblázat rendelkezik egy egyedi korlátozást. 

Miután egyedi kulcsra vonatkozó megkötések a SQL van beállítva, és SQL táblába beszúrt ismétlődő rekordot, Azure Stream Analytics az ismétlődő rekord eltávolítja az adatok rendezze a kötegek és rekurzív módon a kötegek beszúrása csak egyetlen duplikált rekord található. Ha a folyamat ismétlődő sorok jelentős számú, felosztása és a rendszer figyelmen kívül hagyja az ismétlődő egyenként adatok beszúrása rekurzív módon időigényes. Ha több kulcs megsértése figyelmeztető üzenetek a tevékenységnapló az elmúlt egy órában belül jelenik meg, valószínű, hogy az SQL kimeneti lelassult van-e a teljes feladat. A probléma megoldásához, akkor [konfigurálása az index]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) okozza, hogy a kulcs megsértése azáltal, hogy az IGNORE_DUP_KEY beállítást. Ez a beállítás lehetővé teszi, hogy által SQL tömeges Beszúrások során figyelmen kívül az ismétlődő értékek, és SQL Azure egyszerűen hoz létre egy figyelmeztető üzenet hiba helyett. Az Azure Stream Analytics nem eredményez már elsődleges kulcs megsértése hibák.

Vegye figyelembe a következő megfigyelések, számos különböző típusú indexeket az IGNORE_DUP_KEY konfigurálásakor:

* Az elsődleges kulcs vagy egyedi megkötésen, amely használja az ALTER INDEX IGNORE_DUP_KEY nem állítható be, meg kell dobja el és hozza létre újból az indexet.  
* Beállíthatja az ALTER INDEX használatával, amely nem azonos az elsődleges kulcs vagy UNIQUE megkötést, és a CREATE INDEX vagy az INDEX definícióját használatával létrehozott egyedi index IGNORE_DUP_KEY beállítást.  
* IGNORE_DUP_KEY nem vonatkozik az oszlopcentrikus indexek, mert az ilyen indexek egyedisége nem érvényesíthetők.  

## <a name="next-steps"></a>További lépések

* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)


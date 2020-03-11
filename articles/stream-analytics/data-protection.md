---
title: Adatvédelem a Azure Stream Analyticsban
description: Ez a cikk azt ismerteti, hogyan titkosíthatja a Azure Stream Analytics feladatok által használt személyes adatait.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 34cbe5f23728c1fcff0555256575cc34b2b6a869
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2020
ms.locfileid: "79079833"
---
# <a name="data-protection-in-azure-stream-analytics"></a>Adatvédelem a Azure Stream Analyticsban 

A Azure Stream Analytics egy teljes körűen felügyelt platform-szolgáltatás, amellyel valós idejű elemzési folyamatokat hozhat létre. Az összes nehéz megoldás, mint például a fürt üzembe helyezése, a csomópontok méretezése a használathoz, és a belső ellenőrzőpontok kezelése a háttérben történik.

## <a name="encrypt-your-data"></a>Az adatai titkosítása

A Stream Analytics automatikusan alkalmazza a legjobb színvonalú titkosítási szabványokat az infrastruktúrájában, hogy titkosítsa és védje az adatait. Egyszerűen megbízhat Stream Analytics az összes adatainak biztonságos tárolásához, így nem kell aggódnia az infrastruktúra kezelésével kapcsolatban.

Ha ügyfél által felügyelt kulcsokat (CMK) kíván használni az adatok titkosításához, használhatja a saját Storage-fiókját (általános célú v1 vagy v2) a Stream Analytics futtatókörnyezet által igényelt személyes adategységek tárolásához. A Storage-fiók igény szerint titkosítható. A privát adategységek egyikét sem tárolja véglegesen a Stream Analytics-infrastruktúra. 

Ezt a beállítást a Stream Analytics feladatok létrehozásának időpontjában kell konfigurálni, és nem módosítható a feladatok életciklusa során. A Stream Analytics által használt tárterület módosítása vagy törlése nem ajánlott. Ha törli a Storage-fiókját, akkor véglegesen törli az összes magánjellegű adategységet, ami miatt a feladat sikertelen lesz. 

A Storage-fiók kulcsainak frissítése vagy elforgatása nem lehetséges a Stream Analytics portál használatával. A kulcsokat a REST API-k használatával frissítheti.


## <a name="configure-storage-account-for-private-data"></a>A Storage-fiók konfigurálása magánjellegű adatként 

A következő lépésekkel konfigurálhatja a Storage-fiókot a privát adategységekhez. Ez a konfiguráció a Stream Analyticsi feladatokból, és nem a Storage-fiókból történik.

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com/).

1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra. 

1. Válassza az **elemzési** > **Stream Analytics a feladatok** lehetőséget az eredmények listából. 

1. Töltse ki a Stream Analytics feladatot tartalmazó oldalt a szükséges adatokkal, például a név, a régió és a skála lehetőséggel. 

1. Jelölje be a jelölőnégyzetet, amely azt adja meg, hogy a *jelen feladatokban a saját Storage-fiókomban szükséges összes privát adategység biztonságos*legyen.

1. Válasszon egy Storage-fiókot az előfizetésből. Vegye figyelembe, hogy ez a beállítás a feladatok életciklusa során nem módosítható. 

   ![Magánjellegű adattárolási fiók beállításai](./media/data-protection/storage-account-create.png)

## <a name="private-data-assets-that-are-stored"></a>Tárolt privát adategységek

Az Stream Analytics által megőrzött személyes adatait a Storage-fiókban kell tárolni. A privát adategységek például a következők: 

* A létrehozott lekérdezések és a hozzájuk kapcsolódó konfigurációk  

* Felhasználó által meghatározott függvények 

* A bemenetek mintavételi adatainak eredményei 

* Az Stream Analytics futtatókörnyezet által igényelt ellenőrzőpontok

* A hivatkozási adatpillanatképek 

A Stream Analytics-feladatban használt erőforrások kapcsolati adatait is tárolja a rendszer. Titkosítsa a Storage-fiókját az összes adatai védelméhez. 

A megfelelőségi kötelezettségeinek bármely szabályozott iparágban vagy környezetben való kielégítése érdekében további információkat olvashat a [Microsoft megfelelőségi ajánlatáról](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942). 

## <a name="next-steps"></a>Következő lépések

* [Azure Storage-fiók létrehozása](../storage/common/storage-account-create.md)
* [Azure Stream Analytics-bemenetek ismertetése](stream-analytics-add-inputs.md)
* [Ellenőrzőpont-és visszajátszás-fogalmak a Azure Stream Analytics-feladatokban](stream-analytics-concepts-checkpoint-replay.md)
* [A Stream Analytics-keresések hivatkozási adatainak használata](stream-analytics-use-reference-data.md)

---
title: Adatvédelem a Azure Stream Analyticsban
description: Ez a cikk azt ismerteti, hogyan titkosíthatja a Azure Stream Analytics feladatok által használt személyes adatait.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 09/23/2020
ms.openlocfilehash: fa37c251e61b1f920edc55ead38f745439f2de92
ms.sourcegitcommit: 5abc3919a6b99547f8077ce86a168524b2aca350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/07/2020
ms.locfileid: "91812862"
---
# <a name="data-protection-in-azure-stream-analytics"></a>Adatvédelem a Azure Stream Analyticsban 

A Azure Stream Analytics egy teljes körűen felügyelt platform-szolgáltatás, amellyel valós idejű elemzési folyamatokat hozhat létre. Az összes nehéz megoldás, mint például a fürt üzembe helyezése, a csomópontok méretezése a használathoz, és a belső ellenőrzőpontok kezelése a háttérben történik.

## <a name="private-data-assets-that-are-stored"></a>Tárolt privát adategységek

A Azure Stream Analytics a következő metaadatokat és adatokat őrzi meg a futtatásához: 

* Lekérdezés definíciója és kapcsolódó konfigurációja  

* Felhasználó által definiált függvények vagy összesítések  

* Az Stream Analytics futtatókörnyezet által igényelt ellenőrzőpontok

* A hivatkozási adatpillanatképek 

* A Stream Analytics-feladathoz használt erőforrások kapcsolati adatai

A megfelelőségi kötelezettségeinek bármely szabályozott iparágban vagy környezetben való kielégítése érdekében további információkat olvashat a [Microsoft megfelelőségi ajánlatáról](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942). 

## <a name="in-region-data-residency"></a>Régión belüli adattárolás
A Azure Stream Analytics az ügyféladatokat és a fent leírt egyéb metaadatokat tárolja. A rendszer alapértelmezés szerint egyetlen régióban Azure Stream Analytics tárolja az ügyféladatokat, így a szolgáltatás automatikusan megfelel a régiókban tárolt adattárolási követelményeknek, beleértve a [megbízhatósági központban](https://azuredatacentermap.azurewebsites.net/)megadott követelményeket is.
Emellett dönthet úgy is, hogy a stream Analytics-feladathoz kapcsolódó összes adategységet (ügyféladatokat és egyéb metaadatokat) egyetlen régióban tárolja úgy, hogy az Ön által választott Storage-fiókban titkosítja őket.

## <a name="encrypt-your-data"></a>Adatok titkosítása

A Stream Analytics automatikusan alkalmazza a legjobb színvonalú titkosítási szabványokat az infrastruktúrájában, hogy titkosítsa és védje az adatait. Egyszerűen megbízhat Stream Analytics az összes adatainak biztonságos tárolásához, így nem kell aggódnia az infrastruktúra kezelésével kapcsolatban.

Ha ügyfél által felügyelt kulcsokat (CMK) kíván használni az adatok titkosításához, használhatja a saját Storage-fiókját (általános célú v1 vagy v2) a Stream Analytics futtatókörnyezet által igényelt személyes adategységek tárolásához. A Storage-fiók igény szerint titkosítható. A privát adategységek egyikét sem tárolja véglegesen a Stream Analytics-infrastruktúra. 

Ezt a beállítást a Stream Analytics feladatok létrehozásának időpontjában kell konfigurálni, és nem módosítható a feladatok életciklusa során. A Stream Analytics által használt tárterület módosítása vagy törlése nem ajánlott. Ha törli a Storage-fiókját, akkor véglegesen törli az összes magánjellegű adategységet, ami miatt a feladat sikertelen lesz. 

A Storage-fiók kulcsainak frissítése vagy elforgatása nem lehetséges a Stream Analytics portál használatával. A kulcsokat a REST API-k használatával frissítheti.


### <a name="configure-storage-account-for-private-data"></a>A Storage-fiók konfigurálása magánjellegű adatként 


Titkosítsa a Storage-fiókját, hogy biztosítsa az összes adatait, és explicit módon válassza ki a személyes adatai helyét. 

A megfelelőségi kötelezettségeinek bármely szabályozott iparágban vagy környezetben való kielégítése érdekében további információkat olvashat a [Microsoft megfelelőségi ajánlatáról](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942). 



A következő lépésekkel konfigurálhatja a Storage-fiókot a privát adategységekhez. Ez a konfiguráció a Stream Analyticsi feladatokból, és nem a Storage-fiókból történik.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra. 

1. Válassza az **elemzési**   >  **stream Analytics feladatot**   az eredmények listából. 

1. Töltse ki a Stream Analytics feladatot tartalmazó oldalt a szükséges adatokkal, például a név, a régió és a skála lehetőséggel. 

1. Jelölje be a jelölőnégyzetet, amely azt adja meg, hogy a *jelen feladatokban a saját Storage-fiókomban szükséges összes privát adategység biztonságos*legyen.

1. Válasszon egy Storage-fiókot az előfizetésből. Vegye figyelembe, hogy ez a beállítás a feladatok életciklusa során nem módosítható. 

   ![Magánjellegű adattárolási fiók beállításai](./media/data-protection/storage-account-create.png)



### <a name="known-issues"></a>Ismert problémák
Jelenleg létezik egy ismert korlátozás, amelyben az ügyfél által felügyelt kulcsot használó feladatok hibákkal futnak, amikor felügyelt identitást használ a bemenetek és kimenetek hitelesítéséhez.

## <a name="next-steps"></a>Következő lépések

* [Azure Storage-fiók létrehozása](../storage/common/storage-account-create.md)
* [Azure Stream Analytics-bemenetek ismertetése](stream-analytics-add-inputs.md)
* [Ellenőrzőpont-és visszajátszás-fogalmak a Azure Stream Analytics-feladatokban](stream-analytics-concepts-checkpoint-replay.md)
* [A Stream Analytics-keresések hivatkozási adatainak használata](stream-analytics-use-reference-data.md)

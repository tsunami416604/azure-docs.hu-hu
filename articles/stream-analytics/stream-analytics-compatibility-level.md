---
title: Kompatibilitási szintje az Azure Stream Analytics-feladatok ismertetése
description: Ismerje meg, hogyan állíthatja be az Azure Stream Analytics-feladat és a fontosabb változását foglalja össze a kompatibilitási szintje a legújabb kompatibilitási szinten
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/08/2019
ms.openlocfilehash: 6fb93152263d253de983b17d25f02f4c68a172fd
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2019
ms.locfileid: "59361387"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Az Azure Stream Analytics-feladatok kompatibilitási szintje
 
Kompatibilitási szint egy Azure Stream Analytics szolgáltatás kiadás-specifikus viselkedéseit hivatkozik. Az Azure Stream Analytics egy felügyelt szolgáltatás, amelynek rendszeres szolgáltatásfrissítések, és a teljesítménnyel kapcsolatos fejlesztések. Általában frissítések automatikusan elérhetővé a végfelhasználók számára. Olyan új funkciókat azonban vezeti be a fontosabb változását foglalja össze az ilyen,-módosítási található egy meglévő feladat, módosítsa ezeket a feladatokat stb származó adatokat használ, a folyamatokban. A kompatibilitási szint jelent meg a Stream Analytics főbb változást megjelenítésére szolgál. Nagyobb módosítások mindig egy új kompatibilitási szintű jelennek meg. 

Kompatibilitási szint biztosítja, hogy meglévő feladatok futtatását minden hiba nélkül. Amikor létrehoz egy új Stream Analytics-feladat, célszerű a legújabb kompatibilitási szint használatával létrehozott. 
 
## <a name="set-a-compatibility-level"></a>Kompatibilitási szint beállítása 

Kompatibilitási szint szabályozza a stream analytics-feladat működését. A Stream Analytics-feladat kompatibilitási szintje is megadhatja a portálon vagy az a [feladat REST API-hívás létrehozásához](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job). Az Azure Stream Analytics jelenleg támogatja a két kompatibilitási szintek-"1.0" és "1.1". Alapértelmezés szerint a kompatibilitási szintje "1.0", az Azure Stream Analytics általános rendelkezésre állás időszakában megjelent. Az alapértelmezett érték frissítéséhez keresse meg a meglévő Stream Analytics-feladat > Válassza ki a **kompatibilitási szint** beállítást **konfigurálása** szakaszt, és módosítsa az értéket. 

Győződjön meg arról, hogy a kompatibilitási szint frissítése előtt állítsa le a feladatot. A kompatibilitási szint nem frissíthető, ha a feladat nem futó állapotban. 

![Stream Analytics kompatibilitási szintje az Azure Portalon](media/stream-analytics-compatibility-level/stream-analytics-compatibility.png)

 
Amikor frissíti a kompatibilitási szintet, a T-SQL nyelvű fordítón belülre érvényesíti a feladat a szintaxissal, amely megfelel a kiválasztott kompatibilitási szintet. 

## <a name="major-changes-in-the-latest-compatibility-level-12"></a>Fontosabb változását foglalja össze a legújabb kompatibilitási szinten (1.2)

1.2-es kompatibilitási szinten a következő jelentős változások történtek:

### <a name="geospatial-functions"></a>Térinformatikai funkciók 

**Korábbi verziók:** Az Azure Stream Analytics földrajzi számítások használja.

**jelenlegi verziója:** Az Azure Stream Analytics geometriai tervezett geo koordináták számítási teszi lehetővé. Nem történik változás a térinformatikai függvények az aláírás. Azonban a szemantika, kissé eltérő, így pontosabb számítási mint előtt.

Az Azure Stream Analytics támogatja a térinformatikai referencia adatok indexelése. Térinformatikai elemeket tartalmazó referenciaadatok is lehet indexelni gyorsabb illesztési számítást.

A frissített térinformatikai funkciók a teljes kifejezőképessége Well Known Text (wkt) formátumú térinformatikai formátum használata. A GeoJson is megadhat más térinformatikai összetevők, amelyek korábban nem támogatott.

További információkért lásd: [térinformatikai szolgáltatások az Azure Stream Analytics – Felhőbeli és az IoT Edge a frissítések](https://azure.microsoft.com/blog/updates-to-geospatial-functions-in-azure-stream-analytics-cloud-and-iot-edge/).

### <a name="parallel-query-execution-for-input-sources-with-multiple-partitions"></a>A bemeneti források több partícióval rendelkező párhuzamos lekérdezés-végrehajtás 

**Korábbi verziók:** Az Azure Stream Analytics-lekérdezések párhuzamosíthatja a lekérdezés-feldolgozás bemeneti forrás partíciók között a PARTITION BY záradék használata szükséges.

**jelenlegi verziója:** Lekérdezés logikája képes bemeneti forrás partíciójára méretezésnek megfelelően, ha az Azure Stream Analytics példányok külön lekérdezést hoz létre, és számításokat párhuzamosan fut.

### <a name="native-bulk-api-integration-with-cosmosdb-output"></a>A kimenet a cosmos DB natív tömeges API-integráció

**Korábbi verziók:** Az upsert viselkedés volt *beszúrása vagy merge*.

**jelenlegi verziója:** Natív integráció tömeges API cosmos db kimeneti maximalizálja a teljesítményt, és hatékonyan kezeli a kérelmek szabályozása.

Az upsert működése *beszúrása vagy lecserélése*.

### <a name="datetimeoffset-when-writing-to-sql-output"></a>A DateTimeOffset SQL kimeneti való írás esetén

**Korábbi verziók:** [DateTimeOffset](https://docs.microsoft.com/sql/t-sql/data-types/datetimeoffset-transact-sql?view=sql-server-2017) típusok (UTC) lett igazítva.

**jelenlegi verziója:** DateTimeOffset már nem módosul.

### <a name="strict-validation-of-prefix-of-functions"></a>Szigorú ellenőrzési előtag-függvényekkel

**Korábbi verziók:** Hiba történt az előtagok függvény nem szigorú ellenőrzési.

**jelenlegi verziója:** Az Azure Stream Analytics egy szigorú ellenőrzési az előtagok függvény rendelkezik. Előtag hozzáadása egy beépített függvény hibát okoz. Ha például`myprefix.ABS(…)` nem támogatott.

Emellett beépített összesítések ad hozzá egy előtagot hiba eredményez. Ha például `myprefix.SUM(…)` nem támogatott.

Hiba történt a felhasználó által definiált függvények eredményt "rendszer" előtagot használja.

### <a name="disallow-array-and-object-as-key-properties-in-cosmos-db-output-adapter"></a>A Cosmos DB kimeneti adapter kulcs tulajdonságai, a tömb- és letiltása

**Korábbi verziók:** Tömb- és típusok kulcstulajdonságként is támogatott.

**jelenlegi verziója:** Tömb- és típusok kulcstulajdonságként már nem támogatottak.


## <a name="next-steps"></a>További lépések
* [Az Azure Stream Analytics bemenetek hibaelhárítása](stream-analytics-troubleshoot-input.md)
* [Stream Analytics-erőforrás állapota](stream-analytics-resource-health.md)

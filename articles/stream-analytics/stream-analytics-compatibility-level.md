---
title: Kompatibilitási szintje az Azure Stream Analytics-feladatok ismertetése
description: Ismerje meg, hogyan állíthatja be az Azure Stream Analytics-feladat és a fontosabb változását foglalja össze a kompatibilitási szintje a legújabb kompatibilitási szinten
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/12/2019
ms.openlocfilehash: b5c833798f8533e7c6fbe3595a726ac6ce56e2d2
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59682814"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Az Azure Stream Analytics-feladatok kompatibilitási szintje

Ez a cikk ismerteti a kompatibilitási szint lehetőség az Azure Stream Analytics szolgáltatásban. Stream Analytics egy felügyelt szolgáltatás, amelynek rendszeres szolgáltatásfrissítések, és a teljesítménnyel kapcsolatos fejlesztések a. A szolgáltatás futtatókörnyezetek frissítések a legtöbb esetben automatikusan elérhetővé a végfelhasználók számára. 

Azonban egyes új funkciókat a szolgáltatásban egy jelentős változás, például egy meglévő feladat a működésében változást vezethet be, vagy az adatok megjelenítésének módosítása a futó feladatok használja fel. Beállíthatja, hogy a meglévő, ha a beállítás alacsonyabb kompatibilitási szint jelentős módosítások nélkül futó Stream Analytics feladatok. Ha készen áll a legfrissebb futtatókörnyezet viselkedéseket, vehetnek részt a kompatibilitási szint megemelése szerint. 

## <a name="choose-a-compatibility-level"></a>Válassza ki a kompatibilitási szintje

Kompatibilitási szint szabályozza a stream analytics-feladat működését. 

Az Azure Stream Analytics jelenleg három kompatibilitási szinteket támogatja:

* 1.0 - alapértelmezett szint
* 1.1 – jelenlegi kiadási működése
* 1.2-es (előzetes verzió) – a kiértékelés legfrissebb fejlesztései legújabb viselkedés

Az eredeti 1.0-kompatibilitási szint jelent során általános rendelkezésre állás az Azure Stream Analytics évekkel ezelőtt.

Amikor létrehoz egy új Stream Analytics-feladat, célszerű a legújabb kompatibilitási szint használatával létrehozott. Indítsa el a a legújabb viselkedések hagyatkozna feladat tervezési hozzáadott módosítása és a bonyolultságot később elkerülése érdekében.

## <a name="set-the-compatibility-level"></a>A kompatibilitási szint

A Stream Analytics-feladat kompatibilitási szintje az Azure Portalon vagy a használatával beállíthatja a [feladat REST API-hívás létrehozásához](/rest/api/streamanalytics/stream-analytics-job).

A kompatibilitási szintje az Azure Portalon a feladat frissítése:

1. Használja a [az Azure portal](https://portal.azure.com) keresse meg a Stream Analytics-feladathoz.
2. **Állítsa le** a feladatot, mielőtt frissítené a kompatibilitási szintet. A kompatibilitási szint nem frissíthető, ha a feladat nem futó állapotban.
3. Alatt a **konfigurálása** szakaszban kattintson **kompatibilitási szint**.
4. Válassza ki a kívánt kompatibilitási szint értékét.
5. Válassza ki **mentése** az oldal alján.

![Stream Analytics kompatibilitási szintje az Azure Portalon](media/stream-analytics-compatibility-level/stream-analytics-compatibility.png)

Amikor frissíti a kompatibilitási szintet, a T-SQL nyelvű fordítón belülre érvényesíti a feladat a szintaxissal, amely megfelel a kiválasztott kompatibilitási szintet.

## <a name="compatibility-level-12"></a>Kompatibilitási szint 1.2

1.2-es kompatibilitási szinten a következő jelentős változások történtek:

### <a name="geospatial-functions"></a>Térinformatikai funkciók

**Előző szintek:** Az Azure Stream Analytics földrajzi számítások használja.

**1.2-es szint:** Az Azure Stream Analytics geometriai tervezett geo koordináták számítási teszi lehetővé. Nem történik változás a térinformatikai függvények az aláírás. Azonban a szemantika, kissé eltérő, így pontosabb számítási mint előtt.

Az Azure Stream Analytics támogatja a térinformatikai referencia adatok indexelése. Térinformatikai elemeket tartalmazó referenciaadatok is lehet indexelni gyorsabb illesztési számítást.

A frissített térinformatikai funkciók a teljes kifejezőképessége Well Known Text (wkt) formátumú térinformatikai formátum használata. A GeoJson is megadhat más térinformatikai összetevők, amelyek korábban nem támogatott.

További információkért lásd: [térinformatikai szolgáltatások az Azure Stream Analytics – Felhőbeli és az IoT Edge a frissítések](https://azure.microsoft.com/blog/updates-to-geospatial-functions-in-azure-stream-analytics-cloud-and-iot-edge/).

### <a name="parallel-query-execution-for-input-sources-with-multiple-partitions"></a>A bemeneti források több partícióval rendelkező párhuzamos lekérdezés-végrehajtás

**Előző szintek:** Az Azure Stream Analytics-lekérdezések párhuzamosíthatja a lekérdezés-feldolgozás bemeneti forrás partíciók között a PARTITION BY záradék használata szükséges.

**1.2-es szint:** Lekérdezés logikája képes bemeneti forrás partíciójára méretezésnek megfelelően, ha az Azure Stream Analytics példányok külön lekérdezést hoz létre, és számításokat párhuzamosan fut.

### <a name="native-bulk-api-integration-with-cosmosdb-output"></a>A kimenet a cosmos DB natív tömeges API-integráció

**Előző szintek:** Az upsert viselkedés volt *beszúrása vagy merge*.

**1.2-es szint:** Natív integráció tömeges API cosmos db kimeneti maximalizálja a teljesítményt, és hatékonyan kezeli a kérelmek szabályozása.

Az upsert működése *beszúrása vagy lecserélése*.

### <a name="datetimeoffset-when-writing-to-sql-output"></a>A DateTimeOffset SQL kimeneti való írás esetén

**Előző szintek:** [DateTimeOffset](https://docs.microsoft.com/sql/t-sql/data-types/datetimeoffset-transact-sql?view=sql-server-2017) típusok (UTC) lett igazítva.

**1.2-es szint:** DateTimeOffset már nem módosul.

### <a name="strict-validation-of-prefix-of-functions"></a>Szigorú ellenőrzési előtag-függvényekkel

**Előző szintek:** Hiba történt az előtagok függvény nem szigorú ellenőrzési.

**1.2-es szint:** Az Azure Stream Analytics egy szigorú ellenőrzési az előtagok függvény rendelkezik. Előtag hozzáadása egy beépített függvény hibát okoz. Ha például`myprefix.ABS(…)` nem támogatott.

Emellett beépített összesítések ad hozzá egy előtagot hiba eredményez. Ha például `myprefix.SUM(…)` nem támogatott.

Hiba történt a felhasználó által definiált függvények eredményt "rendszer" előtagot használja.

### <a name="disallow-array-and-object-as-key-properties-in-cosmos-db-output-adapter"></a>A Cosmos DB kimeneti adapter kulcs tulajdonságai, a tömb- és letiltása

**Előző szintek:** Tömb- és típusok kulcstulajdonságként is támogatott.

**1.2-es szint:** Tömb- és típusok kulcstulajdonságként már nem támogatottak.

## <a name="compatibility-level-11"></a>1.1-es kompatibilitási szint

1.1-kompatibilitási szinten a következő jelentős változások történtek:

### <a name="service-bus-xml-format"></a>Service Bus XML-formátuma

**1.0-ás szintje:** Az Azure Stream Analytics DataContractSerializer, használja, ezért az üzenet tartalmának XML-címkéket tartalmazza. Példa:

`@\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{ "SensorId":"1", "Temperature":64\}\u0001`

**1.1 szintje:** Az üzenet tartalma közvetlenül a nincsenek további címkék a streamet tartalmaz. Például:`{ "SensorId":"1", "Temperature":64}`

### <a name="persisting-case-sensitivity-for-field-names"></a>A mezőnevek megőrzése Kisbetű/nagybetű megkülönböztetése

**1.0-ás szintje:** Alacsonyabb olyan esetekben, amikor az Azure Stream Analytics-motor általi feldolgozásának mezőnevek módosult.

**1.1-es szint:** Kisbetű/nagybetű megkülönböztetése mezők nevét a rendszer megőrzi, az Azure Stream Analytics-motor feldolgozásakor.

> [!NOTE]
> Persisting Kisbetű/nagybetű megkülönböztetése még nem érhető el az Edge-környezetben üzemeltetett Stream elemzési feladatok. Ennek eredményeképpen minden mezőnevek vannak kisbetűssé konvertált, ha a feladat az Edge-ben üzemel.

### <a name="floatnandeserializationdisabled"></a>FloatNaNDeserializationDisabled

**1.0-ás szintje:** CREATE TABLE parancs nem fejeződött az NaN (nem szám az események szűrése. Például végtelenig, - Infinity) egy LEBEGŐPONTOS oszlop írja be, mert ezek a számok a dokumentált tartományon vannak.

**1.1 szintje:** CREATE TABLE lehetővé teszi, hogy adjon meg egy erős sémát. A Stream Analytics-motor ellenőrzi, hogy az adatok megfelel-e a séma. Ebben a modellben a parancs szűrheti NaN értéket tartalmazó eseményeket.

### <a name="disable-automatic-upcast-for-datetime-strings-in-json"></a>Tiltsa le az automatikus upcast dátum/idő karakterlánc JSON-ban

**1.0-ás szintje:** A JSON-elemző ehhez automatikusan upcast karakterláncértékek dátum/idő típus dátum/időzóna információkat, és átalakítása (UTC). Ez a viselkedés eredményezett elvesztése az időzóna adatait.

**1.1 szintje:** Nincs több automatikusan upcast, a karakterlánc-értékeket dátum/idő típus dátum/időzóna információkat. Ennek eredményeképpen az időzónaadatok tartani.

## <a name="next-steps"></a>További lépések

* [Az Azure Stream Analytics bemenetek hibaelhárítása](stream-analytics-troubleshoot-input.md)
* [Stream Analytics-erőforrás állapota](stream-analytics-resource-health.md)

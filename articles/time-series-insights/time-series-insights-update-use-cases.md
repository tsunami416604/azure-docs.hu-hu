---
title: Az Azure Time Series Insights (előzetes verzió) használati esetek |} A Microsoft Docs
description: A használati esetek ismertetése az Azure Time Series Insights (előzetes verzió)
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/03/2018
ms.openlocfilehash: 67be21ae7f0cb997563f17130b9d5ecb7d359b31
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52873869"
---
# <a name="azure-time-series-insights-preview-use-cases"></a>Az Azure Time Series Insights (előzetes verzió) használati esetek

Ez a cikk számos gyakori alkalmazási helyzetek áttekintést nyújt az Azure Time Series Insights (TSI). Ebben a cikkben szereplő ajánlások szolgálhat a kiindulási pontként, alkalmazások és a TSI-megoldások fejlesztése.

Ez a cikk elolvasása után is elérheti az alábbi kérdések megválaszolásához:

* Mik az Azure TSI gyakori alkalmazási esetei?
* Milyen előnyökkel jár az adatok feltárása és vizuális anomáliadetektálás Azure TSI?
* Mik a működési elemzés és a folyamatok hatékonyságát az Azure TSI használatának előnyei?
* Mik az Azure TSI használata speciális elemzésekre előnyei?

Ez a dokumentum áttekintést a használati esetek, amely az Azure TSI privát előzetes verzió lett tervezve.

## <a name="introduction"></a>Bevezetés

Az Azure TSI egy teljes körű Platform A – szolgáltatásként nyújtott betöltési, dolgozza fel, tárolása és lekérdezése rendkívül contextualized, a time series-optimalizált IoT-méretű adatokra. Mint ilyen az Azure TSI ideális az adatok feltárása ad hoc, valamint a működési elemzés. A TSI egy egyedi bővíthető, testre szabott, szolgáltatás, amely az, hogy megfelel-e a széles körű kell az ipari IoT-környezetét.

## <a name="data-exploration-and-visual-anomaly-detection"></a>Adatfeltárás és vizuális anomáliadetektálás

Több milliárd eseményt vizsgálhat meg és elemezhet azonnal, és feltárhatja a rendellenességeket, és megismerheti az adatok rejtett trendjeit. Az Azure TSI tesz lehetővé, közel valós idejű teljesítmény az IoT- és fejlesztési és üzemeltetési elemzési számítási feladatokhoz.

![– adatkezelő][1]

Az összes Azure TSI erőssége az ügyfelek többsége egyetért, hogy van-e idő-betekintés többek között a legerősebb. A TSI használatához nem szükséges adatok előzetes előkészítése, és működése gyors, hogy az Azure IoT Hub-vagy Event Hub eseményeinek milliárdjait percek alatt.  A csatlakozás után megjelenítése és elemzése, Észreveheti a rendellenességeket milliárd, és feltárhatja a rejtett trendeket az adatokban.  Mivel a TSI egyszerű és intuitív, kezdi, egy egyetlen sor kód megírása nélkül interakció adataival. Új nyelvet sem kell megtanulnia: a Time Series Insights az SQL-szintaxisban járatos haladó felhasználók számára részletes, szövegalapú lekérdezéseket tesz elérhetővé, míg a kezdők egyszerűen kijelölésekkel és kattintásokkal is kezelhetik azt.

Láthatjuk, hogy ügyfeleink a gyorsaság kihasználhatja az eszközintelligencia szolgáltatással kapcsolatos problémák, gyorsan diagnosztizálhatja a fejlesztési és üzemeltetési eléréséhez és a egy IoT-megoldások és a területek azonosítása a hiba oka az adatelemzési kezdeményezések vizsgálata során.  

A TSI-ben tárolt adatokkal folytatott interakciót a három elsődleges módja van:

1. A első és a legkönnyebben a használatának első lépései, a képi megjelenítés, a kezelő, amely lehetővé teszi, hogy gyorsan megjeleníteni az összes IoT-adatait egy helyen. Intenzitástérkép, például vizuálisan kinyerhető rendellenességek ellenőrizze az adatok egyszerű eszközöket biztosít. A perspektíva nézetet, mely lehetővé teszi, hogy összehasonlítja legfeljebb négy nézeteket egy vagy több TSI környezetek egyetlen irányítópulton, így biztosítva az idősorozat-adatok nézete minden a helyek közötti is tartalmazza. Tudjon meg többet a [TSI explorer](./time-series-insights-update-explorer.md). Tervezze meg a TSI-frissítési környezet, olvassa el [TSI Tervezés módosítása](./time-series-insights-update-plan.md).

1. A második lehetőség, hogy gyorsan hatékony diagramokat és ábrákat beágyazása saját webes alkalmazásainak a JavaScript SDK használatával. Mindössze néhány sornyi kóddal hozhat létre hatékony lekérdezések vonaldiagramot, tortadiagramot, sávdiagramot, intenzitástérképek, adatrácsok és további feltöltéséhez. A-beépített az SDK-val található minden elemet. Az SDK-t is kivonatolja a TSI lekérdezési API-k, lehetővé téve a hozhat létre SQL-szerű predikátumok az irányítópulton megjelenítendő adatokat. A megjelenítési réteg hibrid megoldásokat TSI kínál a paraméteres URL-címek, amelyek zökkenőmentes kapcsolódási pontok Explorer deep – ismertetőinken adatokká. További információ a JavaScript SDK-t, olvassa el a [TSI node.js ügyféloldali kódtár](https://docs.microsoft.com/azure/time-series-insights/tutorial-explore-js-client-lib) és a [TSI ügyfél](https://github.com/Microsoft/tsiclient) dokumentációját. További információ a paraméteres URL-címek-olvassa el a cikket a [paraméteres URL-címek](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-parameterized-urls).  

1. Végül a TSI hatékony API-kat biztosít a TSI-ben tárolt adatok lekérdezésére. Hasonlóan, első és utolsó, összesítések és átalakítások, például átlag, min, max, felosztási szempont, rendezési és DateHistogram és szűrési operátorok például rendelkezik, a TSI rendelkezik az ideiglenes operátorok és, vagy nagyobb, mint REGEX stb. Minden operátor lehetővé teszik az alárendelt alkalmazások érdekes trendek és mintázatok gyorsan megtalálhatja az adatokban, és azonosíthatja a rendellenességeket a saját készítésű Vizualizációk feltöltéséhez használható.  

## <a name="operational-analysis-and-driving-process-efficiency"></a>Üzemeltetéselemzés és a folyamatok hatékonyságának növelése

Engedélyezze a állapotára, használatának és teljesítményének berendezések a nagy mennyiségű, helyezzék egyszerűen mérheti a működési hatékonyságot. A Time Series Insights használatával azonnali és rugalmas skálázhatósággal kezelheti a változatos és kiszámíthatatlan IoT-terheléseket a betöltések és a lekérdezések teljesítményének csökkenése nélkül.

![Áttekintés][2]

Streamelés és folyamatos üzemeltetési folyamatokat származó adatok feldolgozása sikeresen alakíthatja át minden olyan cégnek, ha a megfelelő technológiát/megoldás szolgáltatással párosítva. Ezek a megoldások gyakran kerülnek, és több rendszerek, amelyek lehetővé teszik a feltárás kombinációját, amely kifejezetten az IoT-tartomány folyamatosan változik, és megoszthatja a gyakori minta adatok elemzése.

Ezek a minták gyakran első engedélyezve IoT platformok, amely az eszközök és érzékelők különböző területi beállításokat átfedés milliárd fogadására. Ezek a rendszerek feldolgozásához, és elemezheti a streamelt adatokat, hogy a valós idejű elemzések és műveletek, általában archivált adatok közel valós idejű és kötegelt elemzésre az online és offline tárolóba. Az összegyűjtött adatok halad végig tanfolyamunkra, és az alsóbb rétegbeli lekérdezési és elemzési forgatókönyvek contextualize feldolgozási sorát. A Microsoft Azure (az Eszközintelligencia karbantartási, gyártási, stb.) ezen IoT-forgatókönyvek alkalmazhatók gazdag szolgáltatásokat biztosít. Ezek közé tartozik az Azure TSI, Azure IoT Hub, az Azure Event Hubs, Azure Stream Analytics, Azure Functions, Azure Logic Apps, az Azure Databricks, az Azure Machine Learning és a Microsoft Power bi-ban.

A megoldásarchitektúra elérhető módon – kiolvasni az adatokat az Azure IoT Hub vagy az Azure Event Hub kategóriaelső a biztonsági, az átviteli sebesség és a késés. Az adatok feldolgozására és számításokat végez funneling szolgáltatások, például az Azure Stream Analytics szolgáltatásban az Azure Logic Apps, az Azure Functions adott adatfeldolgozási igényeitől függően a feldolgozott adatokat. Számított jelek a feldolgozási folyamat az Azure TSI kerüljenek tárolására és az analytics. Az Azure Time Series Insights kínál közel valós idejű adatok feltárása és eszköz-alapú insights korábbi adatok alapján. Üzleti igényeitől függően a MapReduce és a Hive-feladatok futtatni a Time Series Insights csatlakozhat a HDInsight a Time Series Insightsban tárolt adatokat. A Time Series Insights-ban tárolt adatok a Power BI és egyéb ügyfél-alkalmazásba a Time Series Insights nyilvános felület lekérdezési API-k kiterjedt üzleti és működési intelligenciával forgatókönyvekhez elérhetővé tehetők.

## <a name="advanced-analytics"></a>Bővített analitika

Fejlett elemzési szolgáltatások, például az Azure Machine Learning és az Azure Databricks integrálható. A Time Series Insights eszközök millióinak nyers adatait tölti be, és olyan környezeti adatokat ad hozzá, amelyek egyszerűen felhasználhatóak az Azure elemzési szolgáltatási csomagjaival.

![analytics][3]

Fejlett elemzési és gépi tanulási használják, és az adatvezérelt döntések és prediktív elemzéseket végezhet a nagy adatmennyiségek feldolgozása. IoT-használati esetek fejlett analitikai algoritmusok megtudhatja, az eszközöket, amelyek is továbbíthat adatokat többször másodpercenként több millió begyűjtött adatokat. Azonban az IoT-eszközökről gyűjtött adatok nyers és nem áll rendelkezésre környezeti információkat, például az eszközök, az érzékelő olvasási stb., ezáltal megnehezítve a az adatok közvetlenül fejlett elemzésekhez használt mértékegység helyét.

Az Azure Time Series Insights közötti szakadék IoT-adatok és fejlett analitikai két egyszerű és költséghatékony módon. Először a Time Series Insights update nyers telemetriai adatokat gyűjti össze az eszközöket az IoT hub több millió, adatokat és környezeti adatokat bővíti és alakítja át az adatokat könnyen integrálhatók a fejlett elemzési szolgáltatások számos ilyen "parquet formátum" az Azure Machine Learning, az Azure Databricks és más gyártóktól származó alkalmazások.  A Time Series Insights alapul szolgálhat a forrás-az-hiteles minden adat vállalaton belül, így létrehozása egy központi tárházban alsóbb rétegbeli elemzési számítási feladatok a felhasználni.  A Time Series Insights óta van közel valós idejű tárolási szolgáltatás, fejlett analitikai modellek bejövő IoT-telemetriai adatokat pontosabb előrejelzéseket készíthet a folyamatosan tanul.

Második a Time Series Insights is lehet adatkéréseket a kimenet a machine learning és az előrejelzési modellek jelenítheti meg, és tárolja az eredményeket, így segítve a szervezeteket optimalizálása és a modellek a Teljesítménybeállítások.  A Time Series Insights lehetővé teszi egyszerű jeleníthetik meg az azonos adatsík-telemetriai adatait streamelési adatok adattudománnyal foglalkozó csapatai rendellenességeket és a minták azonosítása érdekében kiírja a betanított modell szerint.  

## <a name="next-steps"></a>További lépések

Tudjon meg többet a [TSI explorer](./time-series-insights-update-explorer.md).

Tervezze meg a környezetben, olvassa el [TSI (előzetes verzió) tervezési](./time-series-insights-update-plan.md).

Olvassa el a [TSI ügyfél](https://github.com/Microsoft/tsiclient) dokumentációját.

<!-- Images -->
[1]: media/v2-update-use-cases/data-explorer.svg
[2]: media/v2-update-use-cases/overview.svg
[3]: media/v2-update-use-cases/advanced-analytics.svg

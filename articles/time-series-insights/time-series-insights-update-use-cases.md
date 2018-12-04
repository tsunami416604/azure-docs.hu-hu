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
ms.date: 11/27/2018
ms.openlocfilehash: 9d9fab9f0a515cacdf2a1425c4da06c9e3d4c364
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52856557"
---
# <a name="azure-time-series-insights-preview-use-cases"></a>Az Azure Time Series Insights (előzetes verzió) használati esetek

Ez a cikk számos gyakori alkalmazási helyzetek áttekintést nyújt az Azure Time Series Insights (TSI). Ebben a cikkben szereplő ajánlások szolgálhat a kiindulási pontként, alkalmazások és a TSI-megoldások fejlesztése.

Ez a cikk elolvasása után is elérheti az alábbi kérdések megválaszolásához:

* Mik az Azure TSI gyakori alkalmazási esetei?
* Milyen előnyökkel jár az adatok feltárása és vizuális anomáliadetektálás Azure TSI?
* Mik a működési elemzés és a folyamatok hatékonyságát az Azure TSI használatának előnyei?
* Mik az Azure TSI használata speciális elemzésekre előnyei?

Ez a dokumentum áttekintést a használati esetek, amely a Azure Time Series Insights privát előzetes verzió lett tervezve.

## <a name="data-exploration-and-visual-anomaly-detection"></a>Adatfeltárás és vizuális anomáliadetektálás

Több milliárd eseményt vizsgálhat meg és elemezhet azonnal, és feltárhatja a rendellenességeket, és megismerheti az adatok rejtett trendjeit. Az Azure TSI tesz lehetővé, közel valós idejű teljesítmény az IoT- és fejlesztési és üzemeltetési elemzési számítási feladatokhoz.

![– adatkezelő][1]

Az összes Azure TSI erőssége az ügyfelek többsége egyetért, hogy van-e idő-betekintés többek között a legerősebb. A TSI használatához nem szükséges adatok előzetes előkészítése, és működése gyors, hogy az Azure IoT Hub-vagy Event Hub eseményeinek milliárdjait percek alatt.  A csatlakozás után megjelenítése és elemzése, Észreveheti a rendellenességeket milliárd, és feltárhatja a rejtett trendeket az adatokban.  Mivel a TSI egyszerű és intuitív, kezdi, egy egyetlen sor kód megírása nélkül interakció adataival. Új nyelvet sem kell megtanulnia: a Time Series Insights az SQL-szintaxisban járatos haladó felhasználók számára részletes, szövegalapú lekérdezéseket tesz elérhetővé, míg a kezdők egyszerűen kijelölésekkel és kattintásokkal is kezelhetik azt.

Láthatjuk, hogy ügyfeleink a gyorsaság kihasználhatja az eszközintelligencia szolgáltatással kapcsolatos problémák, gyorsan diagnosztizálhatja a fejlesztési és üzemeltetési eléréséhez és a egy IoT-megoldások és a területek azonosítása a hiba oka az adatelemzési kezdeményezések vizsgálata során.  

A TSI-ben tárolt adatokkal folytatott interakciót a három elsődleges módja van:

1. A első és a legkönnyebben a használatának első lépései, a képi megjelenítés, a kezelő, amely lehetővé teszi, hogy gyorsan megjeleníteni az összes IoT-adatait egy helyen. Eszközök, mint például az intenzitástérkép, amelyek a vizuálisan kinyerhető rendellenességek teszik az adatok egyszerű, valamint a perspektíva nézet, amely lehetővé teszi legfeljebb négy nézeteket egy vagy több egy irányítópulton, így az idősorozat-adatok között nézetét a TSI-környezetek összehasonlítása biztosít a helyek. Tudjon meg többet a [TSI Explorer](./time-series-insights-update-explorer.md). Tervezze meg a TSI-frissítési környezet, olvassa el [TSI Tervezés módosítása](./time-series-insights-update-plan.md).

1. A második lehetőség, hogy gyorsan hatékony diagramokat és ábrákat beágyazása saját webes alkalmazásainak a JavaScript SDK használatával. Mindössze néhány sornyi kóddal hozhat létre hatékony lekérdezések vonaldiagramot, tortadiagramot, sávdiagramot, intenzitástérképek, adatrácsok és további feltöltéséhez. A-beépített az SDK-val található minden elemet. Az SDK-t is kivonatolja a TSI lekérdezési API-k, lehetővé téve a hozhat létre SQL-szerű predikátumok az irányítópulton megjelenítendő adatokat. A megjelenítési réteg hibrid megoldásokat TSI kínál a paraméteres URL-címek, amelyek zökkenőmentes kapcsolódási pontok Explorer deep – ismertetőinken adatokká. További információ a JavaScript SDK-t, olvassa el a [TSI node.js ügyféloldali kódtár](https://docs.microsoft.com/azure/time-series-insights/tutorial-explore-js-client-lib) és a [TSI ügyfél](https://github.com/Microsoft/tsiclient) dokumentációját. További információ a paraméteres URL-címek-olvassa el a cikket a [paraméteres URL-címek](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-parameterized-urls).  

1. Végül a TSI hatékony API-kat biztosít a TSI-ben tárolt adatok lekérdezésére. Hasonlóan, első és utolsó, összesítések és átalakítások, például átlag, min, max, felosztási szempont, rendezési és DateHistogram és szűrési operátorok például rendelkezik, a TSI rendelkezik az ideiglenes operátorok és, vagy nagyobb, mint REGEX stb. Minden operátor lehetővé teszik az alárendelt alkalmazások érdekes trendek és mintázatok gyorsan megtalálhatja az adatokban, és azonosíthatja a rendellenességeket a saját készítésű Vizualizációk feltöltéséhez használható.  

Az Azure IoT-ajánlatok további információkért lásd: [létrehozása a saját IOT-](https://www.microsoft.com/internet-of-things).

## <a name="operational-analysis-and-driving-process-efficiency"></a>Üzemeltetéselemzés és a folyamatok hatékonyságának növelése

Engedélyezze a állapotára, használatának és teljesítményének berendezések a nagy mennyiségű, helyezzék egyszerűen mérheti a működési hatékonyságot. A Time Series Insights használatával azonnali és rugalmas skálázhatósággal kezelheti a változatos és kiszámíthatatlan IoT-terheléseket a betöltések és a lekérdezések teljesítményének csökkenése nélkül.

![Áttekintés][2]

Streamelés és folyamatos üzemeltetési folyamatokat származó adatok feldolgozása sikeresen alakíthatja át minden olyan cégnek, ha a megfelelő technológiát/megoldás szolgáltatással párosítva. Ezek a megoldások gyakran kerülnek, és elemzési adatok, amelyek kifejezetten az IoT-tartomány folyamatosan változnak, amelyek lehetővé teszik a feltárás több rendszer kombinációját. Ezeket a forgatókönyveket, amelyek megosztása a gyakori minta esetén, a betöltési, a folyamat mentése rendszerek együtt világos tárolásához, elemzéséhez és jelenítheti meg az IoT-adatokat.

A megoldás, a rendszerek kell, hogy az eszközök és érzékelők különböző területi beállításokat átfedés milliárd részét. Ezután ezek a rendszerek adatainak feldolgozására és elemzésére streamelési a valós idejű elemzéseket. Az adatok ezután archivált közel valós idejű és kötegelt elemzésre az online és offline tárolóba.

Ezután ezek a rendszerek kell folyamat engedélyezéséhez begyűjtött adatok megtisztításához és contextualization alsóbb rétegbeli lekérdezési és elemzési forgatókönyvek engedélyezéséhez szükséges adatok tárolása közben. A Microsoft Azure biztosít sokoldalú szolgáltatásai, beleértve az Azure TSI, Azure IoT Hub, az Azure Event Hubs, az Azure Stream Analytics, Azure Functions, Azure Logic Apps, az Azure Databricks, az Azure Machine Learning és a Microsoft Power BI ezen IoT-forgatókönyvek esetén is alkalmazható.

A fenti megoldás beállítása az adatok át olvasódnak keresztül az Azure IoT- és Event Hubs módon, nagy átviteli sebességű adatbetöltést biztosít alacsony késéssel. Betöltött adatok, fel kell dolgozni a valós idejű elemzéseket az Azure Stream Analytics, az Azure Logic Apps és az Azure Functions funneled is lehet. Az eredmény lehet majd adni, hogy a Power BI a valós idejű dashboarding, valamint a figyelmeztetések és monitorozás, hasonlítsa össze a korábbi beültetés tölti be az Azure Time Series Insights lehet. Szüksége van az adatok feltárása a betöltött adatok közel valós időben, vagy ad hoc ad hoc adatlekérdezést, az előzmények trendjének tölthetők be közvetlenül az Azure Time Series Insights. Az adatok betöltése készen áll a működési elemzés és-folyamatok maximális hatékonyság optimalizálása analytics korlátlan előzményadatok együtt kérdezhető le. Összes adat vagy csak a betöltött adatok módosításait legutóbb is használható valós idejű elemzési részeként referenciaadatok. Emellett adatok tovább lehet alakítva és dolgozza fel az Azure Time Series Insights adatait csatlakozhat a HDInsight Map/Reduce, a Hive, a feladatok stb. Végül pedig az adatok elérhetővé tétele a Power bi-ban, és a nyilvános felület lekérdezési API-k segítségével bármely ügyfél alkalmazásban.

## <a name="advanced-analytics"></a>Bővített analitika

Fejlett elemzési szolgáltatások, például az Azure Machine Learning és az Azure Databricks integrálható. A TSI ingresses nyers adatok eszközök millióiról érkező, és hozzáadja a kontextuális adatokat gyűjt, amelyek zökkenőmentesen felhasználhassák Azure elemzési szolgáltatáscsomag.

![analytics][3]

Fejlett elemzési és gépi tanulási használják, és az adatvezérelt döntések és prediktív elemzéseket végezhet a nagy adatmennyiségek feldolgozása. IoT-használati esetek fejlett analitikai algoritmusok megtudhatja, az eszközöket, amelyek is továbbíthat adatokat többször másodpercenként több millió begyűjtött adatokat. Azonban az IoT-eszközökről gyűjtött adatok nyers és nem áll rendelkezésre környezeti információkat, például az eszközök, érzékelők leolvasott stb egysége helyét. Ezeket az adatokat közvetlenül a speciális elemzésekhez nem lehet használni.

Az Azure TSI közötti szakadék IoT-adatok és fejlett analitikai egyszerű és költséghatékony módon. A TSI eszközök millióiról érkező nyers telemetriai adatokat gyűjt, és környezeti adatokat adatok bővíti és alakítja át az adatokat egyszerűen integrálhatja az Azure fejlett elemzési szolgáltatásokat, mint az Azure Machine Learning, az Azure számos "parquet formátum" DataBricks, és a saját harmadik féltől származó alkalmazások. Fejlett analitikai modellek folyamatosan tudhat meg az IoT pontosabb előrejelzéseket készíthet a beérkező telemetriai adatokat.

## <a name="next-steps"></a>További lépések

* Tudjon meg többet a [TSI Explorer](./time-series-insights-update-explorer.md).

* Tervezze meg a környezetben, olvassa el [TSI (előzetes verzió) tervezési](./time-series-insights-update-plan.md).

* Olvassa el a [TSI ügyfél](https://github.com/Microsoft/tsiclient) dokumentációját.

<!-- Images -->
[1]: media/v2-update-use-cases/data-explorer.png
[2]: media/v2-update-use-cases/overview.png
[3]: media/v2-update-use-cases/advanced-analytics.png

---
title: Azure Stream Analytics-lekérdezések – problémamegoldás
description: Ez a cikk az Azure Stream Analytics-feladatokban a lekérdezések hibaelhárítási technikáit ismerteti.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: bf0740bbdd4754aeba43e64f1076a1bea33cffc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844419"
---
# <a name="troubleshoot-azure-stream-analytics-queries"></a>Azure Stream Analytics-lekérdezések – problémamegoldás

Ez a cikk a Stream Analytics-lekérdezések fejlesztésével kapcsolatos gyakori problémákat ismerteti, és a hibaelhárításukat.

## <a name="query-is-not-producing-expected-output"></a>A lekérdezés nem hoz létre várt kimenetet
1.  Vizsgálja meg a hibákat helyi teszteléssel:
    - Az Azure Portal **on Query** lapon válassza a **Tesztelés lehetőséget.** A lekérdezés [teszteléséhez](stream-analytics-test-query.md)használja a letöltött mintaadatokat. Vizsgálja meg a hibákat, és próbálja meg kijavítani őket.   
    - A [lekérdezést helyileg](stream-analytics-live-data-local-testing.md) is tesztelheti az Azure Stream Analytics-eszközökkel a Visual Studio vagy a [Visual Studio Code számára.](visual-studio-code-local-run-live-input.md) 

2.  [Hibakeresési lekérdezések lépésről lépésre helyileg az](debug-locally-using-job-diagram.md) Azure Stream Analytics-eszközök visual studio feladatdiagram használatával. A feladatdiagram bemutatja, hogyan áramlik a bemeneti forrásokból (Event Hub, IoT Hub, stb.) származó adatok több lekérdezési lépésen keresztül, és végül kimeneti fogadók. Minden lekérdezési lépés a parancsfájlban a WITH utasítás használatával definiált ideiglenes eredményhalmazhoz van rendelve. Megtekintheti az adatokat, valamint a metrikák minden lekérdezési lépés minden köztes eredményhalmaz, hogy megtalálják a probléma forrását.
    ![Feladatdiagram előnézetének eredménye](./media/debug-locally-using-job-diagram/preview-result.png)

3.  Ha [**a Timestamp By,**](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics)használatával ellenőrizze, hogy az események időbélyegei nagyobbak-e, mint a [feladat kezdési ideje.](stream-analytics-out-of-order-and-late-events.md)

4.  Kiküszöbölheti a gyakori buktatókat, mint például:
    - A [**WHERE**](https://docs.microsoft.com/stream-analytics-query/where-azure-stream-analytics) záradék a lekérdezésben kiszűrte az összes eseményt, megakadályozva a kimenet ek keletkezését.
    - Egy [**CAST**](https://docs.microsoft.com/stream-analytics-query/cast-azure-stream-analytics) függvény meghibásodik, ami a feladat sikertelensítését okozza. A típusleadott hibák elkerülése érdekében használja [**helyette TRY_CAST.**](https://docs.microsoft.com/stream-analytics-query/try-cast-azure-stream-analytics)
    - Ablakfüggvények használatakor várja meg, amíg az ablak teljes időtartama megjelenik a lekérdezés ből származó kimenet.
    - Az események időbélyege megelőzi a feladat kezdési idejét, ezért az események et eldobják.

5.  Győződjön meg arról, hogy az eseményrendezési házirendek a várt módon vannak konfigurálva. Nyissa meg a **Beállítások lapot,** és válassza [**az Eseményrendezés**](stream-analytics-out-of-order-and-late-events.md)lehetőséget. A házirend *nem* lesz alkalmazva, ha a **Teszt** gombbal teszteli a lekérdezést. Ez az eredmény az egyik különbség a böngészőn ként végzett tesztelés és a feladat éles környezetben való futtatása között. 

6. Hibakeresés naplózási és diagnosztikai naplók használatával:
    - Használja [a Naplók at,](../azure-resource-manager/resource-group-audit.md)és szűrje a hibák azonosítását és hibakeresését.
    - A [feladatdiagnosztikai naplók](stream-analytics-job-diagnostic-logs.md) segítségével azonosíthatja és debugelhatja a hibákat.

## <a name="job-is-consuming-too-many-streaming-units"></a>A feladat túl sok streamelési egységet fogyaszt
Győződjön meg arról, hogy kihasználja a párhuzamosítás előnyeit az Azure Stream Analytics szolgáltatásban. A streamanalytics-feladatok [lekérdezési párhuzamosításával a](stream-analytics-parallelization.md) bemeneti partíciók konfigurálásával és az elemzési lekérdezésdefiníció finomhangolásával skálázhat.

## <a name="debug-queries-progressively"></a>Lekérdezések fokozatos hibakeresése

A valós idejű adatfeldolgozás, tudva, hogy az adatok hogyan néz ki a lekérdezés közepén hasznos lehet. Mivel az Azure Stream Analytics-feladat bemenetei vagy lépései többször is olvashatók, további SELECT INTO utasítások írása. Ezzel köztes adatokat ad ki a tárolóba, és lehetővé teszi az adatok helyességének vizsgálatát, ugyanúgy, mint a *figyelési változók,* amikor hibakeresést végez egy program hibakeresésével.

A következő példa lekérdezés egy Azure Stream Analytics-feladat ban egy stream bemenet, két referencia-adatbemenetek és egy kimenet az Azure Table Storage-ba. A lekérdezés az eseményközpontból és két hivatkozási blobból származó adatokat egyesít a név- és kategóriaadatok lekérdezéséhez:

![Példa Stream Analytics SELECT INTO lekérdezésre](./media/stream-analytics-select-into/stream-analytics-select-into-query1.png)

Vegye figyelembe, hogy a feladat fut, de nem események készülnek a kimenetben. Az itt látható **Figyelés** csempén láthatja, hogy a bemenet adatokat hoz létre, de nem tudja, hogy a **JOIN** melyik lépése okozta az összes esemény eldobását.

![A Stream Analytics figyelési csempéje](./media/stream-analytics-select-into/stream-analytics-select-into-monitor.png)

Ebben a helyzetben hozzáadhat néhány további SELECT INTO utasítást a köztes JOIN eredmények és a bemenetből beolvasott adatok "naplózásához".

Ebben a példában két új "ideiglenes kimenetet" adtunk hozzá. Olyan mosogatók lehetnek, amilyet csak akarsz. Az Azure Storage-t példaként használjuk:

![További SELECT INTO utasítások hozzáadása a Stream Analytics lekérdezéshez](./media/stream-analytics-select-into/stream-analytics-select-into-outputs.png)

Ezután átírhatja a lekérdezést a következőknek:

![Átírt SELECT INTO Stream Analytics lekérdezés](./media/stream-analytics-select-into/stream-analytics-select-into-query2.png)

Most indítsa újra a feladatot, és hagyja futni néhány percig. Ezután a Visual Studio Cloud Explorer segítségével a temp1 és a temp2 lekérdezése a következő táblázatokat hozza létre:

**temp1 tábla**
![SELECT INTO temp1 table Stream Analytics lekérdezés](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-1.png)

**temp2 tábla**
![SELECT INTO temp2 table Stream Analytics lekérdezés](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-2.png)

Mint látható, temp1 és temp2 egyaránt rendelkeznek adatokkal, és a név oszlop helyesen van feltöltve temp2. Mivel azonban még mindig nincs adat a kimenetben, valami nincs rendben:

![SELECT INTO output1 tábla adatstream analytics-lekérdezés nélkül](./media/stream-analytics-select-into/stream-analytics-select-into-out-table-1.png)

Az adatok mintavételezésével szinte biztos lehet abban, hogy a probléma a második JOIN-mal van. Letöltheti a referenciaadatokat a blobból, és megnézheti a következőket:

![SELECT INTO REF TÁBLA Stream Analytics lekérdezés](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-1.png)

Mint látható, a formátum a GUID ebben a referencia adatok eltér a formátum a [from] oszlop temp2. Ezért az adatok nem a várt módon érkeztek meg a output1-ben.

Javíthatja az adatformátumot, feltöltheti a hivatkozási blobba, majd újra próbálkozhat:

![SELECT INTO TEMP TABLE Stream Analytics lekérdezés](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-2.png)

Ezúttal a kimenetben lévő adatok formázása és a várt módon történik.

![SELECT INTO döntő táblázat Stream Analytics lekérdezés](./media/stream-analytics-select-into/stream-analytics-select-into-final-table.png)

## <a name="get-help"></a>Segítségkérés

További segítségért próbálja ki [az Azure Stream Analytics fórumunkat.](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>További lépések

* [Bevezetés az Azure Stream Analytics szolgáltatásba](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs (Azure Stream Analytics-feladatok méretezése)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Referencia az Azure Stream Analytics lekérdezési nyelvhez)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)

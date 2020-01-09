---
title: Azure Stream Analytics lekérdezések hibáinak megoldása
description: Ez a cikk azokat a technikákat ismerteti, amelyekkel Azure Stream Analytics-feladatokban található lekérdezéseket lehet elhárítani.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 22e542715afa8c87ffb742bec6c22f758cd16587
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75354262"
---
# <a name="troubleshoot-azure-stream-analytics-queries"></a>Azure Stream Analytics lekérdezések hibáinak megoldása

Ez a cikk a Stream Analytics-lekérdezések fejlesztésével és hibaelhárításával kapcsolatos gyakori problémákat ismerteti.

## <a name="query-is-not-producing-expected-output"></a>A lekérdezés nem hozza létre a várt kimenetet 
1.  Hibák vizsgálata helyi teszteléssel:
    - A **lekérdezés** lapon válassza a **teszt**elemet. A letöltött mintaadatok használatával [tesztelheti a lekérdezést](stream-analytics-test-query.md). Vizsgálja meg a hibákat, és próbálja meg kijavítani azokat.   
    - A lekérdezést a Visual studióhoz készült Stream Analytics Tools használatával [közvetlenül is tesztelheti élő bemeneten](stream-analytics-live-data-local-testing.md) .

2.  Ha [**időbélyegzőt**](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics)használ, ellenőrizze, hogy az események időbélyegei nagyobbak-e a [feladatok kezdési idejénél](stream-analytics-out-of-order-and-late-events.md).

3.  Távolítsa el a gyakori buktatókat, például a következőket:
    - A lekérdezés [**Where**](https://docs.microsoft.com/stream-analytics-query/where-azure-stream-analytics) záradéka szűrte az összes eseményt, ami megakadályozza a kimenet generálását.
    - A [**Cast**](https://docs.microsoft.com/stream-analytics-query/cast-azure-stream-analytics) függvény meghiúsul, így a feladat sikertelen lesz. Ha el szeretné kerülni a leadott hibák beírását, használja a [**TRY_CAST**](https://docs.microsoft.com/stream-analytics-query/try-cast-azure-stream-analytics) helyet.
    - Ha a Window functions funkciót használja, várja meg a teljes ablak időtartamát, hogy megjelenjen a lekérdezés kimenete.
    - Az események időbélyegzője megelőzi a feladatok kezdési idejét, ezért az események el lesznek dobva.

4.  Győződjön meg arról, hogy az esemény-rendezési házirendek a várt módon vannak konfigurálva. Lépjen a **Beállítások** elemre, és válassza az [**események rendezése**](stream-analytics-out-of-order-and-late-events.md)lehetőséget. A rendszer *nem* alkalmazza a házirendet, ha a **teszt** gombot használja a lekérdezés teszteléséhez. Ez az eredmény az egyik különbség a böngészőn belüli tesztelés és az éles környezetben futó feladatok között. 

5. Hibakeresés naplózási és diagnosztikai naplók használatával:
    - Használjon [naplókat](../azure-resource-manager/resource-group-audit.md), és szűrje a hibákat a hibák azonosításához és hibakereséséhez.
    - A hibák azonosításához és hibakereséséhez használja a [feladatok diagnosztikai naplóit](stream-analytics-job-diagnostic-logs.md) .

## <a name="job-is-consuming-too-many-streaming-units"></a>A feladatok túl sok átviteli egységet fogyasztanak
Győződjön meg arról, hogy kihasználja Azure Stream Analytics párhuzamos előnyeit. Megtudhatja, hogyan méretezheti Stream Analytics feladatok [lekérdezési párhuzamos](stream-analytics-parallelization.md) a bemeneti partíciók konfigurálásával és az elemzési lekérdezés definíciójának finomhangolásával.

## <a name="debug-queries-progressively"></a>Hibakeresési lekérdezések fokozatosan

A valós idejű adatfeldolgozás során hasznos lehet annak ismerete, hogy az adatmennyiség hogyan néz ki a lekérdezés közepén. Mivel az Azure Stream Analytics feladatok bemenetei vagy lépései többször is beolvashatók, további SELECT INTO utasítások is megírhatók. Így a rendszer a közbenső adatokat a tárolóba helyezi, és lehetővé teszi az adatok helyességének vizsgálatát, mint ahogy a program hibakeresése során *megtekinti a változókat* .

Egy Azure Stream Analytics-feladatban az alábbi lekérdezés egy stream-bemenettel, két hivatkozási adatbemenettel és egy Azure Table Storage kimenettel rendelkezik. A lekérdezés összekapcsolja az adatokat az Event hub-ból és két hivatkozási blobot a név és a kategória információinak lekéréséhez:

![Példa Stream Analytics kijelölés a lekérdezésben](./media/stream-analytics-select-into/stream-analytics-select-into-query1.png)

Vegye figyelembe, hogy a művelet fut, de a kimenetben nem készül esemény. Az itt látható **figyelési** csempén láthatja, hogy a bemenet adatokat állít elő, de nem tudja, hogy az **illesztés** melyik lépése okozta az összes eldobott eseményt.

![A Stream Analytics monitorozási csempe](./media/stream-analytics-select-into/stream-analytics-select-into-monitor.png)
 
Ebben az esetben hozzáadhat néhány további KIJELÖLÉSt az utasításokhoz a köztes összekapcsolási eredmények és a bemenetből beolvasott adatok között.

Ebben a példában két új "ideiglenes kimenetet" adtunk hozzá. Az Ön számára bármilyen fogadó lehet. Itt az Azure Storage-t használjuk példaként:

![További SELECT INTO utasítások hozzáadása a Stream Analytics lekérdezéshez](./media/stream-analytics-select-into/stream-analytics-select-into-outputs.png)

Ezután a következőhöz hasonló módon írhatja át a lekérdezést:

![KIJELÖLÉS átírása Stream Analytics lekérdezésbe](./media/stream-analytics-select-into/stream-analytics-select-into-query2.png)

Most indítsa újra a feladatot, és néhány percen belül futtassa. Ezután lekérdezheti a temp1 és a temp2 a Visual Studio Cloud Explorerben a következő táblázatok létrehozásához:

**temp1 table**
![válassza ki a temp1 táblát Stream Analytics lekérdezés](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-1.png)

**temp2 table**
![válassza ki a temp2 táblát Stream Analytics lekérdezés](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-2.png)

Amint láthatja, a temp1 és a temp2 egyaránt tartalmaz adatokat, és a Name (név) oszlop helyesen van feltöltve a temp2. Mivel azonban még mindig nincsenek adatokat a kimenetben, valami nem stimmel:

![Válassza ki a output1 táblát, amely nem rendelkezik adatStream Analytics lekérdezéssel](./media/stream-analytics-select-into/stream-analytics-select-into-out-table-1.png)

Az adatmintavételezéssel szinte biztos lehet abban, hogy a probléma a második ILLESZTÉSsel van ellátva. A hivatkozásokat letöltheti a blobból, és megtekintheti a következőket:

![Válassza ki a ref Table Stream Analytics lekérdezést](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-1.png)

Amint láthatja, az ebben a hivatkozási adatokban található GUID formátuma eltér a (temp2) [from] oszlopának formátumával. Ezért nem érkeznek meg az output1 a várt módon.

Megjavíthatja az adatformátumot, feltöltheti a hivatkozási blobra, majd próbálkozzon újra:

![Válassza ki a temp Table Stream Analytics lekérdezést](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-2.png)

Ezúttal a kimenetben lévő adatok formázása és feltöltése a várt módon történik.

![Utolsó táblázat Stream Analytics lekérdezés kiválasztása](./media/stream-analytics-select-into/stream-analytics-select-into-final-table.png)

## <a name="get-help"></a>Segítség

További segítségért próbálja ki a [Azure stream Analytics fórumot](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Következő lépések

* [Bevezetés a Azure Stream Analyticsba](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)

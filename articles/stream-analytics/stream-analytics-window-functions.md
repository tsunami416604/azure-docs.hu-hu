---
title: Bevezetés az Azure Stream Analytics ablakos funkcióiba
description: Ez a cikk négy ablakos függvényt (bukdácsolás, ugrás, csúsztatás, munkamenet) ismerteti, amelyek et az Azure Stream Analytics-feladatokban használnak.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 872eec62e7a629d76533aa6c9906cbdb64c32236
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745553"
---
# <a name="introduction-to-stream-analytics-windowing-functions"></a>Bevezetés a Stream Analytics ablakos funkcióiba

Idő-streamelési forgatókönyvekben a temporális ablakokban lévő adatokon végzett műveletek végrehajtása gyakori minta. A Stream Analytics natív támogatást nyújt az ablakos funkciókhoz, lehetővé téve a fejlesztők számára, hogy minimális erőfeszítéssel összetett streamfeldolgozási feladatokat hoznak létre.

Négyféle időbeli ablak közül választhat: [**Bukdácsoló,**](https://docs.microsoft.com/stream-analytics-query/tumbling-window-azure-stream-analytics) [**Hopping**](https://docs.microsoft.com/stream-analytics-query/hopping-window-azure-stream-analytics), [**Csúszó**](https://docs.microsoft.com/stream-analytics-query/sliding-window-azure-stream-analytics)és [**Munkamenet**](https://docs.microsoft.com/stream-analytics-query/session-window-azure-stream-analytics) ablakok.  A Stream Analytics-feladatokban a lekérdezés szintaxisának [**GROUP BY**](https://docs.microsoft.com/stream-analytics-query/group-by-azure-stream-analytics) záradékában található ablakfüggvényeket használhatja. Az eseményeket több ablakon is összesítheti a [ **Windows()** funkció val.](https://docs.microsoft.com/stream-analytics-query/windows-azure-stream-analytics)

Az [ablakolási](https://docs.microsoft.com/stream-analytics-query/windowing-azure-stream-analytics) műveletek kimeneti eredményei az ablak **végén** jelennek meg. Az ablak kimenete egyetlen esemény lesz a használt összesítő függvény alapján. A kimeneti esemény az ablak végének időbélyegzővel fog rendelkezni, és az összes ablakfüggvény rögzített hosszúságú lesz. 

![A Stream Analytics ablakfüggvényeinek fogalmai](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>Bukdácsoló ablak
A bukdácsoló ablakfüggvények arra szolgálnak, hogy az adatfolyamot különböző időszegmensekre szegmentálják, és funkciót hajtsanak végre ellenük, például az alábbi példában. Az átfedésmentes ablak fő sajátossága, hogy ismétlődik, nincs átfedésben, és egy esemény csak egy átfedésmentes ablakhoz tartozhat.

![A Stream Analytics bukdácsoló ablaka](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>Ugráló ablak
Az ugróablak típusú függvények egy adott időtartamot ugranak előre az időben. Tulajdonképpen olyan átfedésmentes ablakok, amelyek lehetnek átfedésben, az események így több ugróablak eredményhalmazához is tartozhatnak. Ha azt szeretné, hogy az Ugráló ablak megegyezik a Tumbling ablakmérettel, adja meg, hogy az ugrás mérete megegyezik-e az ablak méretével. 

![A Stream Analytics ugrási ablaka](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>Csúszó ablak
A csúszó ablakfunkciók a Bukdácsoló vagy az Ugró ablakoktól eltérően **csak** akkor hoznak létre kimenetet, ha esemény történik. Minden ablaknak legalább egy eseménye lesz, és az ablak folyamatosan halad előre egy ε (epszilon) által. Az ugróablakhoz hasonlóan egy esemény több csúszóablakhoz is tartozhat.

![Stream Analytics csúszóablak](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="session-window"></a>Munkamenet ablak
A munkamenetablak-függvények csoportosítják a hasonló időpontokban érkező eseményeket, kiszűrve azokat az időszakokat, amikor nincsenek adatok. Három fő paraméterrel rendelkezik: időtúllépés, maximális időtartam és particionálókulcs (nem kötelező).

![A Stream Analytics munkamenetablaka](media/stream-analytics-window-functions/stream-analytics-window-functions-session-intro.png)

A munkamenetablak az első esemény bekövetkeztekor kezdődik. Ha egy másik esemény történik a megadott időtúloldalon az utolsó beadott esemény, majd az ablak kiterjeszti, hogy tartalmazza az új esemény. Ellenkező esetben, ha az időidőn belül nem történik esemény, akkor az ablak az időhosszabbításkor bezárul.

Ha az események továbbra is a megadott időtúllépésen belül következnek be, a munkamenet-ablak a maximális időtartam eléréséig folyamatosan meghosszabbodik. A maximális időtartam-ellenőrzési időközök a megadott maximális időtartammal megegyező méretűre vannak beállítva. Ha például a maximális időtartam 10, akkor az ablak maximális időtartamának túllépése t = 0, 10, 20, 30 stb.

Partíciókulcs megadva, az események a kulcs szerint vannak csoportosítva, és a munkamenet-ablak az egyes csoportokra egymástól függetlenül lesz alkalmazva. Ez a particionálás olyan esetekben hasznos, amikor különböző munkamenet-ablakokra van szükség a különböző felhasználók vagy eszközök számára.


## <a name="next-steps"></a>További lépések
* [Bevezetés az Azure Stream Analytics szolgáltatásba](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs (Azure Stream Analytics-feladatok méretezése)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Referencia az Azure Stream Analytics lekérdezési nyelvhez)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)


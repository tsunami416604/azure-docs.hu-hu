---
title: Kiszolgálóoldali teljesítménylekérdezések
description: A kiszolgálóoldali teljesítménylekérdezések API-hívásokon keresztüli lekérdezései
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: 9a28dee2d1e6d1355b729a56e8eeb8447e4ed8c8
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80682025"
---
# <a name="server-side-performance-queries"></a>Kiszolgálóoldali teljesítménylekérdezések

A kiszolgálón nyújtott jó renderelési teljesítmény kritikus fontosságú a stabil képkockasebesség és a jó felhasználói élmény érdekében. Fontos, hogy gondosan figyelje a kiszolgáló teljesítményjellemzőit, és szükség esetén optimalizálja a teljesítményt. A teljesítményadatok lekérdezhetők dedikált API-függvényeken keresztül.

A renderelési teljesítmény leghatásosabb a modell bemeneti adatai. A bemeneti adatokat a [modellkonvertálás konfigurálása](../../how-tos/conversion/configure-model-conversion.md)című témakörben leírtak szerint módosíthatja.

Az ügyféloldali alkalmazások teljesítménye is szűk keresztmetszetet jelenthet. Az ügyféloldali teljesítmény mélyreható elemzéséhez ajánlott [teljesítmény-nyomkövetést](../../how-tos/performance-tracing.md)végezni.

## <a name="clientserver-timeline"></a>Ügyfél/kiszolgáló ütemterve

Mielőtt részletesen kifejtené a különböző késési értékeket, érdemes megtekinteni az ügyfél és a kiszolgáló közötti szinkronizálási pontokat az idővonalon:

![Csővezeték ütemterve](./media/server-client-timeline.png)

Az ábra bemutatja, hogyan:

* a *Pose becslést* az ügyfél állandó 60 Hz-es képkockasebességgel (16,6 ms-onként) kezdi el
* a szerver ezután elindul a renderelés, a póz alapján
* a szerver visszaküldi a kódolt videoképet
* az ügyfél dekódolja a képet, elvégzi néhány CPU és GPU munka a tetején, majd megjeleníti a képet

## <a name="frame-statistics-queries"></a>Keretstatisztikai lekérdezések

A keretstatisztikák magas szintű információkat szolgáltatnak az utolsó képkockáról, például a késésről. A struktúrában `FrameStatistics` megadott adatokat az ügyféloldalon mérik, így az API egy szinkron hívás:

````c#
void QueryFrameData(AzureSession session)
{
    FrameStatistics frameStatistics;
    if (session.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        // do something with the result
    }
}
````

A beolvasott `FrameStatistics` objektum a következő tagokat tartalmazza:

| Tag | Magyarázat |
|:-|:-|
| latencyPoseToReceive | A kamera készletbecslésének késleltetése az ügyféleszközön addig, amíg az ehhez a pózhoz készült kiszolgálókeret teljes mértékben elérhető vé nem válik az ügyfélalkalmazás számára. Ez az érték magában foglalja a hálózati oda-vissza, a kiszolgáló leképezési idejét, a videó dekódolását és a vibrátor kompenzációt. Lásd **az 1.**|
| latencyReceiveToPresent | Késés a fogadott távoli keret rendelkezésre állásából, amíg az ügyfélalkalmazás meg nem hívja a PresentFrame-et a processzoron. |
| latencyPresentToDisplay  | A processzor on-termésének megjelenítésétől a kijelző kivilágításáig késleltetése. Ez az érték magában foglalja az ügyfél GPU-idejét, az operációs rendszer által végrehajtott keretpufferezést, a hardverújravetítést és az eszközfüggő kijelző-kifutási időt. Lásd **a fenti ábrán a 2.**|
| timeSinceLastPresent | A cpu-n lévő PresentFrame későbbi hívásai között. A kijelző időtartamánál nagyobb értékek (például 16,6 ms egy 60 Hz-es ügyféleszközön) azt jelzik, hogy az ügyfélalkalmazás nem fejezi be időben a CPU-terhelést. Lásd **a fenti ábrán a 3.**|
| videoFramesReceived | A kiszolgálótól az utolsó másodpercben fogadott keretek száma. |
| videoFrameReusedCount | Az eszközön többször használt fogadott keretek száma az utolsó másodpercben. A nem nulla értékek azt jelzik, hogy a képkockákat újra fel kellett használni, és újra kellett felvetíteni a hálózati vibrezés vagy a kiszolgáló túlzott megjelenítési ideje miatt. |
| videoFramesed | Az utolsó másodpercben dekódolt, de nem megjelenített fogadott keretek száma, mert újabb képkocka érkezett. A nem nulla értékek azt jelzik, hogy a hálózati vibrálás miatt több képkocka késik, majd egy adatfelvételben együtt érkezik meg az ügyféleszközre. |
| videoFramediscarded | Nagyon hasonlít **a videoFramesSkipped,** de az oka annak, hogy elvetették, hogy a keret jött olyan későn, hogy nem is lehet korrelál minden függőben lévő jelent többé. Ha ez megtörténik, van néhány súlyos hálózati viszálykodás.|
| videoFrameMinDelta | Az utolsó másodpercben érkező két egymást követő képkocka közötti minimális időtartam. A videoFrameMaxDelta-val együtt ez a tartomány jelzi a hálózati vagy videokodek által okozott vibrációt. |
| videoFrameMaxDelta | Az utolsó másodpercben érkező két egymást követő képkocka közötti maximális idő. A videoFrameMinDelta-val együtt ez a tartomány jelzi a hálózati vagy videokodek által okozott vibrációt. |

Az összes késési érték összege általában sokkal nagyobb, mint a rendelkezésre álló keretidő 60 Hz-en. Ez rendben van, mert több képkocka párhuzamosan repül, és az új képkockakérelmek a kívánt képkockasebességgel indulnak el, ahogy az az ábrán is látható. Azonban, ha a késés túl nagy, ez befolyásolja a [késői szakaszban újravetítés](../../overview/features/late-stage-reprojection.md)minőségét, és veszélyeztetheti az általános tapasztalat.

`videoFramesReceived`, `videoFrameReusedCount`és `videoFramesDiscarded` a hálózat és a kiszolgáló teljesítményének mérésére használható. Ha `videoFramesReceived` alacsony `videoFrameReusedCount` és magas, az hálózati torlódást vagy gyenge kiszolgálói teljesítményt jelezhet. A `videoFramesDiscarded` nagy érték a hálózati torlódást is jelzi.

Végül`timeSinceLastPresent`, `videoFrameMinDelta`és `videoFrameMaxDelta` adjon képet a bejövő videoképkockák és a helyi behívások varianciájáról. A nagy variancia instabil képkockasebességet jelent.

A fenti értékek egyike sem jelzi egyértelműen a hálózat tiszta késését (az ábrán látható piros nyilakat), mivel a kiszolgáló `latencyPoseToReceive`foglalt megjelenítési idejét ki kell vonni a körbeútra szóló értékből. A teljes késés kiszolgálóoldali része olyan információ, amely nem érhető el az ügyfél számára. A következő bekezdés azonban azt ismerteti, hogy ez az érték hogyan `networkLatency` közelíthető meg a kiszolgáló további bevitelével, és hogyan érhető el az értéken keresztül.

## <a name="performance-assessment-queries"></a>Teljesítményértékelési lekérdezések

*A teljesítményértékelési lekérdezések* részletesebb információkat nyújtanak a kiszolgáló processzor- és GPU-munkaterheléséről. Mivel az adatokat a kiszolgálótól kérik, a teljesítmény-pillanatkép lekérdezése a szokásos aszinkron mintát követi:

``` cs
PerformanceAssessmentAsync _assessmentQuery = null;

void QueryPerformanceAssessment(AzureSession session)
{
    _assessmentQuery = session.Actions.QueryServerPerformanceAssessmentAsync();
    _assessmentQuery.Completed += (PerformanceAssessmentAsync res) =>
    {
        // do something with the result:
        PerformanceAssessment result = res.Result;
        // ...

        _assessmentQuery = null;
    };
}
```

Az objektummal `FrameStatistics` ellentétben `PerformanceAssessment` az objektum kiszolgálóoldali információkat tartalmaz:

| Tag | Magyarázat |
|:-|:-|
| timeCPU | A kiszolgáló átlagos processzora képkockánkénti átlaga ezredmásodpercben |
| timeGPU | Kiszolgáló GPU-s átlaga képkockánkénti átlaga ezredmásodpercben |
| kihasználtságCPU | A kiszolgáló teljes cpu-kihasználtsága százalékban |
| használatGPU | A kiszolgáló GPU-jának teljes kihasználtsága százalékban |
| memóriaCPU | A kiszolgáló fő memóriakihasználtságának teljes százaléka |
| memória GPU | A dedikált videomemória teljes kihasználtsága a kiszolgáló GPU-jának százalékában |
| hálózatLatency | A hozzávetőleges átlagos oda-vissza hálózati késés ezredmásodpercben. A fenti ábrán ez megfelel a piros nyilak összegének. Az értéket úgy számítjuk ki, hogy a `latencyPoseToReceive` kiszolgáló `FrameStatistics`tényleges renderelési idejét kivonjuk a értékéből. Bár ez a közelítés nem pontos, ez ad némi jelzést a hálózati késés, izolálva az ügyfélkésszámított késési értékek. |
| sokszögekRenderelt | Az egy keretben megjelenített háromszögek száma. Ez a szám tartalmazza azokat a háromszögeket is, amelyeket a renderelés során később selejteznek le. Ez azt jelenti, hogy ez a szám nem változik sokat a különböző kamerapozíciókban, de a teljesítmény drasztikusan változhat, a háromszög selejtezési sebességétől függően.|

Az értékek értékelésének segítése érdekében minden egyes rész minőségi besorolással érkezik, mint **a Nagy**, **Jó**, **Középszerű**vagy **a Rossz**.
Ez az értékelési metrika a kiszolgáló állapotának durva jelzését adja meg, de nem tekinthető abszolútnak. Tegyük fel például, hogy a GPU-idő "középszerű" pontszáma jelenik meg. Úgy vélik, középszerű, mert megközelíti a határértéket a teljes frame időkeret költségvetést. Az Ön esetében azonban lehet, hogy egy jó érték mégis, mert ön teszi egy összetett modell.

## <a name="statistics-debug-output"></a>Statisztikai hibakeresési kimenet

Az `ARRServiceStats` osztály körbejárja mind a keretstatisztikákat, mind a teljesítményértékelési lekérdezéseket, és kényelmes funkcionalitást biztosít a statisztikák összesített értékként vagy előre elkészített karakterláncként való visszaadásához. A következő kód a legegyszerűbb módja annak, hogy az ügyfélalkalmazásban a kiszolgálóoldali statisztikákat jelenítse meg.

``` cs
ARRServiceStats _stats = null;

void OnConnect()
{
    _stats = new ARRServiceStats();
}

void OnDisconnect()
{
    _stats = null;
}

void Update()
{
    if (_stats != null)
    {
        // update once a frame to retrieve new information and build average values
        _stats.Update(Service.CurrentActiveSession);

        // retrieve a string with relevant stats information
        InfoLabel.text = _stats.GetStatsString();
    }
}
```

A fenti kód a következő szöveggel feltölti a szöveges címkét:

![ArrServiceStats karakterlánc kimenete](./media/arr-service-stats.png)

Az `GetStatsString` API formázza az összes érték karakterláncát, de minden egyes érték `ARRServiceStats` programozott módon is lekérdezhető a példányból.

A tagoknak vannak olyan változatai is, amelyek az idő múlásával összesítik az értékeket. Lásd a tagokat `*Avg` `*Max`utótaggal , vagy `*Total`. A `FramesUsedForAverage` tag azt jelzi, hogy hány képkocka volt használva ehhez az összesítéshez.

## <a name="next-steps"></a>További lépések

* [Teljesítmény-nyomkövetések létrehozása](../../how-tos/performance-tracing.md)
* [A modellkonvertálás konfigurálása](../../how-tos/conversion/configure-model-conversion.md)

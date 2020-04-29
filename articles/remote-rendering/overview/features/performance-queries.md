---
title: Kiszolgálóoldali teljesítménylekérdezések
description: Kiszolgálóoldali teljesítményű lekérdezések végrehajtása API-hívások használatával
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: 9a28dee2d1e6d1355b729a56e8eeb8447e4ed8c8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80682025"
---
# <a name="server-side-performance-queries"></a>Kiszolgálóoldali teljesítménylekérdezések

A megfelelő megjelenítési teljesítmény a kiszolgálón kritikus fontosságú a stabil képarányok és a jó felhasználói élmény érdekében. Fontos, hogy körültekintően figyelje a kiszolgáló teljesítmény-jellemzőit, és szükség esetén optimalizálja azokat. A teljesítményadatokat dedikált API-függvények segítségével lehet lekérdezni.

A renderelési teljesítmény legjelentősebb hatása a modell bemeneti adatai. A bemeneti adatokat a [modell átalakításának konfigurálása](../../how-tos/conversion/configure-model-conversion.md)című témakörben leírtak szerint állíthatja be.

Az ügyféloldali alkalmazások teljesítménye is szűk keresztmetszet lehet. Az ügyféloldali teljesítmény részletes elemzéséhez javasolt a [teljesítmény nyomon követése](../../how-tos/performance-tracing.md).

## <a name="clientserver-timeline"></a>Ügyfél/kiszolgáló idővonala

Mielőtt részletesen bekerül a különböző késési értékekre, érdemes megtekinteni az ügyfél és a kiszolgáló közötti szinkronizálási pontokat az idővonalon:

![Folyamat idővonala](./media/server-client-timeline.png)

Az ábrán az alábbiak láthatók:

* a *becslést* az ügyfél a 60 – Hz-es kereteken (minden 16,6 MS) állandó értékre vált.
* a kiszolgáló ezután elindítja a renderelést a póz alapján
* a kiszolgáló visszaküldi a kódolt videó képét
* az ügyfél dekódolja a rendszerképet, végrehajt néhány CPU-t és GPU-t, majd megjeleníti a rendszerképet

## <a name="frame-statistics-queries"></a>Keret statisztikai lekérdezései

A keret statisztikái magas szintű információkat biztosítanak az utolsó kerethez, például a késést. A `FrameStatistics` struktúrában megadott adat az ügyfél oldalán mérhető, így az API egy szinkron hívás:

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
| latencyPoseToReceive | A kamera késése becslést jelent az eszközön, amíg az alkalmazáshoz tartozó kiszolgálói keret teljes mértékben elérhető az ügyfélalkalmazás számára. Ez az érték magában foglalja a hálózati bemutatót, a kiszolgáló megjelenítési idejét, a videó-dekódolást és a jitter-kompenzációt. Lásd **a fenti ábrán az 1. intervallumot.**|
| latencyReceiveToPresent | A fogadott távoli keretek rendelkezésre állásának késése, amíg az ügyfélalkalmazás nem hívja meg a PresentFrame a PROCESSZORon. |
| latencyPresentToDisplay  | Egy keretnek a CPU-ban való megjelenítésének késése, amíg a kijelző fel nem villan. Ez az érték tartalmazza az ügyfél GPU-időpontját, az operációs rendszer által végrehajtott összes frame-pufferelést, a hardverek újravetítését és az eszköztől függő megjelenítési vizsgálat időpontját. Lásd **a fenti ábrán a 2. intervallumot.**|
| timeSinceLastPresent | A következő, a PROCESSZORon PresentFrame meghívások közötti idő. A megjelenítési időtartamnál nagyobb értékek (például az 16,6 MS egy 60-Hz-es ügyfélszámítógépen) jelzik, hogy az ügyfélalkalmazás által okozott problémák nem fejezik be a CPU-munkaterhelést időben. Lásd **a fenti ábrán látható 3. intervallumot.**|
| videoFramesReceived | A kiszolgálóról az elmúlt másodpercben fogadott keretek száma. |
| videoFrameReusedCount | A fogadott képkockák száma az elmúlt másodpercben, amely többször is használatban volt az eszközön. A nullától eltérő értékek azt jelzik, hogy a rendszer újra felhasználta a képkockákat, és a hálózati Jitter vagy a kiszolgáló túlzott megjelenítési ideje miatt újra feldolgozta őket. |
| videoFramesSkipped | Az elmúlt másodpercben a visszakapott képkockák száma, amelyek dekódolása megtörtént, de a megjelenítés nem látható, mert újabb keret érkezett. A nullától eltérő értékek azt jelzik, hogy a hálózati vibrálás több képkockát okozott, majd az eszközön együtt megérkezik a burst állapotba. |
| videoFramesDiscarded | Nagyon hasonlít a **videoFramesSkipped**, de az eldobásának oka, hogy egy keret olyan későn jött létre, hogy a függőben lévők már nem is korrelálnak. Ha ez bekövetkezik, a hálózati tartalom komoly.|
| videoFrameMinDelta | Az elmúlt másodpercben két egymást követő keret között érkező minimális időtartam. A videoFrameMaxDelta együtt ez a tartomány a hálózat vagy a videó kodek által okozott vibrálás jelzését adja meg. |
| videoFrameMaxDelta | Az elmúlt másodpercben két egymást követő keret között elérkező maximális időtartam. A videoFrameMinDelta együtt ez a tartomány a hálózat vagy a videó kodek által okozott vibrálás jelzését adja meg. |

Az összes késési érték összege általában jóval nagyobb, mint a rendelkezésre álló keret 60 Hz-nél. Ez azért van így, mert több keret van folyamatban párhuzamosan, és az új keretekre vonatkozó kérések a kívánt keretre kerülnek, ahogy az ábrán is látható. Ha azonban a késés túl nagy, az hatással van a [késői fázis újravetítésének](../../overview/features/late-stage-reprojection.md)minőségére, és veszélyeztetheti a teljes élményt.

`videoFramesReceived``videoFrameReusedCount`a, a `videoFramesDiscarded` és a használatával mérhető a hálózat és a kiszolgáló teljesítménye. Ha `videoFramesReceived` alacsony és `videoFrameReusedCount` magas, akkor ez a hálózati torlódást vagy a kiszolgáló gyenge teljesítményét jelezheti. A magas `videoFramesDiscarded` érték A hálózati torlódást is jelzi.

Végül,, és `videoFrameMaxDelta` a beérkező képkockák és a helyi meghívások eltérésének ötlete.`timeSinceLastPresent` `videoFrameMinDelta` A nagy variancia nem stabil képarányt jelent.

A fenti értékek egyike sem teszi egyértelművé a tiszta hálózati késést (az ábrán látható piros nyilat), mert a kiszolgáló elfoglalt állapotának pontos időpontját ki kell vonni a oda-értékből `latencyPoseToReceive`. Az általános késés kiszolgálóoldali része olyan információ, amely nem érhető el az ügyfél számára. A következő bekezdés azonban elmagyarázza, hogy ez az érték hogyan közelíthető meg a kiszolgáló további bemenetei és az `networkLatency` érték alapján.

## <a name="performance-assessment-queries"></a>Teljesítmény-értékelési lekérdezések

A *teljesítmény-értékelési lekérdezések* részletesebb információkat biztosítanak a kiszolgáló processzor-és GPU-munkaterheléséről. Mivel az adatok kérése a kiszolgálóról történik, a teljesítmény-pillanatfelvétel lekérdezése a szokásos aszinkron mintát követi:

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

`FrameStatistics` Az objektummal ellentétben az `PerformanceAssessment` objektum kiszolgálóoldali adatokat tartalmaz:

| Tag | Magyarázat |
|:-|:-|
| timeCPU | Átlagos kiszolgálói CPU-idő/keret (ezredmásodperc) |
| timeGPU | Kiszolgáló GPU-ra vonatkozó átlagos ideje (ezredmásodperc) |
| utilizationCPU | Kiszolgáló CPU-kihasználtsága összesen (százalék) |
| utilizationGPU | Kiszolgáló GPU-kihasználtsága összesen (százalék) |
| memoryCPU | Kiszolgáló összes fő memóriájának kihasználtsága százalékban |
| memoryGPU | Dedikált videomemória teljes kihasználtsága a kiszolgálói GPU százalékában |
| networkLatency | Az átlagos kétirányú hálózati késés ezredmásodpercben megadva. A fenti ábrán ez a piros nyilak összegének felel meg. Az értéket a rendszer a tényleges kiszolgáló renderelési idejének `latencyPoseToReceive` kivonásával számítja ki `FrameStatistics`. Habár ez a közelítés nem pontos, a hálózati késést is jelzi, amely az ügyfélen számított késési értékektől elkülönített. |
| polygonsRendered | Az egyik keretben megjelenített háromszögek száma Ez a szám a renderelés során később kiselejtezett háromszögeket is tartalmazza. Ez azt jelenti, hogy ez a szám nem változik a különböző kamera-pozíciók között, de a teljesítmény jelentősen változhat, a háromszög-kiselejtezési aránytól függően.|

Az értékek felmérése érdekében minden részhez tartozik egy minőségi besorolás, például **nagyszerű**, **jó**, **közepes**vagy **rossz**.
Ez az értékelési metrika a kiszolgáló állapotának durva jelzését biztosítja, de nem tekinthető abszolútnak. Tegyük fel például, hogy a GPU-idő "közepes" pontszámot tartalmaz. A rendszer közepesnek tekinti, mivel az a teljes keretre vonatkozó költségkerethez tartozó korláthoz közeledik. Ebben az esetben azonban érdemes lehet jó értéket adni, mert összetett modellt tesz elérhetővé.

## <a name="statistics-debug-output"></a>Statisztikák hibakeresési kimenete

Az osztály `ARRServiceStats` a keret statisztikái és a teljesítmény-értékelési lekérdezéseket is tartalmazza, és kényelmes funkciókat biztosít a statisztikák összesített értékként való visszaküldéséhez, vagy egy előre elkészített sztringként. A következő kód a kiszolgálóoldali statisztika megjelenítésének legegyszerűbb módja az ügyfélalkalmazás számára.

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

A fenti kód feltölti a szöveg címkéjét a következő szöveggel:

![ArrServiceStats karakterlánc kimenete](./media/arr-service-stats.png)

Az `GetStatsString` API az összes érték sztringjét formázza, de minden egyes érték programozott módon is lekérdezhető a `ARRServiceStats` példányból.

A tagoknak vannak olyan változatai is, amelyek az értékeket az idő múlásával összesítik. Tekintse meg a `*Avg`tagokat utótaggal, `*Max`vagy `*Total`. A tag `FramesUsedForAverage` azt jelzi, hogy az összesítéshez hány képkockát használtak.

## <a name="next-steps"></a>További lépések

* [Teljesítmény-nyomkövetés létrehozása](../../how-tos/performance-tracing.md)
* [A modell átalakításának konfigurálása](../../how-tos/conversion/configure-model-conversion.md)

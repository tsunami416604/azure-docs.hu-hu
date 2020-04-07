---
title: Ügyféloldali teljesítmény-nyomkövetések létrehozása
description: Gyakorlati tanácsok az ügyféloldali teljesítményprofilkészítéshez a WPF használatával
author: florianborn71
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.openlocfilehash: 1f4207a11f3ae3664023fccf6178b6db7cf253b9
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681310"
---
# <a name="create-client-side-performance-traces"></a>Ügyféloldali teljesítmény-nyomkövetések létrehozása

Számos oka lehet annak, hogy az Azure távoli renderelés teljesítménye nem olyan jó, mint a kívánt. A felhőkiszolgálón nyújtott tiszta renderelési teljesítmény mellett különösen a hálózati kapcsolat minősége jelentős hatással van az élményre. A kiszolgáló teljesítményének profiljához olvassa el a [kiszolgálóoldali teljesítménylekérdezéseket.](../overview/features/performance-queries.md)

Ez a fejezet arra összpontosít, hogy hogyan lehet azonosítani a potenciális ügyféloldali szűk keresztmetszeteket *a teljesítménynyomkövetések*révén.

## <a name="getting-started"></a>Első lépések

Ha még nem ismerkedik a Windows teljesítmény-nyomkövetési funkciójával, ez a szakasz a kezdéshez leggyakrabban megnevező kifejezéseket és alkalmazásokat fogja említeni.

### <a name="installation"></a>Telepítés

Az ARR-rel való nyomon követéshez használt alkalmazások általános célú eszközök, amelyek minden Windows fejlesztéshez használhatók. Ezek a [Windows teljesítményeszközkészletén](https://docs.microsoft.com/windows-hardware/test/wpt/)keresztül találhatók. Az eszközkészlet letöltéséhez töltse le a [Windows assessment and Deployment Kit készletet.](https://docs.microsoft.com/windows-hardware/get-started/adk-install)

### <a name="terminology"></a>Terminológia

Amikor a teljesítménynyomkövetésekkel kapcsolatos információkat keres, elkerülhetetlenül számos kifejezéssel találkozik. A legfontosabbak a következők:

* `ETW`
* `ETL`
* `WPR`
* `WPA`

**ETW** jelentése [ **E**vent **T**versenyzés **a W**indows](https://docs.microsoft.com/windows/win32/etw/about-event-tracing). Ez egyszerűen az átfogó neve a hatékony kernel szintű nyomkövetési létesítmény, amely be van építve a Windows. Ezt *eseménykövetésnek* nevezzük, mivel az ETW-t támogató alkalmazások eseményeket bocsátanak ki a teljesítményproblémák nyomon követését segítő műveletek naplózásához. Alapértelmezés szerint az operációs rendszer már olyan eseményeket bocsát ki, mint a lemezhozzáférés, a feladatkapcsolók és hasonlók. Az olyan alkalmazások, mint az ARR, egyéni eseményeket is bocsátanak ki, például az eldobott képkockákról, a hálózati késésről stb.

**Etl** jelentése **E**vent **T**verseny **L**ogging. Ez egyszerűen azt jelenti, hogy a nyomkövetés már összegyűjtött (naplózott), és ezért általában használják a fájl kiterjesztését fájlokat, hogy tárolja a nyomkövetési adatokat. Így, ha nem a nyomkövetés, \*akkor általában lesz egy .etl fájlt utána.

**A WPR** a [ **W**indows **P**erformance **R**ecorder](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-recorder) rövidítése, és annak az alkalmazásnak a neve, amely elindítja és leállítja az eseménynyomok rögzítését. A WPR egy\*profilfájlt (.wprp) vesz fel, amely konfigurálja a naplózandó pontos eseményeket. Az `wprp` ilyen fájl az ARR SDK-hoz van ellátva. Ha nyomkövetést végez asztali számítógépen, közvetlenül elindíthatja a WPR-t. Amikor a HoloLensen nyomkövetést végez, általában a webes felületen keresztül kell végigmenni.

**WPA** jelentése [ **W**indows **P**erformance **A**nalyzer](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-analyzer) és a neve \*a GUI alkalmazás, amely megnyitásához használt .etl fájlokat és szitot át az adatokat, hogy azonosítsa a teljesítmény problémákat. A WPA lehetővé teszi az adatok különböző kritériumok szerint történő rendezését, az adatok többféleképpen történő megjelenítését, a részletek beásását és az információk korrelációt.

Bár az ETL-nyomkövetések bármely Windows-eszközön (helyi PC, HoloLens, felhőkiszolgáló stb.) létrehozhatók, általában lemezre kerülnek, és asztali számítógépen elemzik a WPA-val. ETL fájlokat lehet küldeni más fejlesztők számára, hogy egy pillantást. Ne feledje, hogy a bizalmas adatok, például a fájlelérési utak és az IP-címek, lehet rögzíteni ETL-nyomokban, mégis. Az ETW kétféleképpen használható: nyomkövetések rögzítésére vagy a nyomkövetések elemzésére. A nyomkövetések rögzítése egyenesen előre halad, és minimális beállítást igényel. Elemzése nyomokat másrészt nem igényel tisztességes megértését mind a WPA eszköz és a probléma, hogy vizsgálja. A WPA tanulására szolgáló általános anyagokat az alábbiakban találjuk, valamint az ARR-specifikus nyomok értelmezésére vonatkozó iránymutatásokat.

## <a name="recording-a-trace-on-a-local-pc"></a>Nyomkövetés rögzítése helyi számítógépen

Az ARR teljesítményproblémák azonosításához inkább közvetlenül a HoloLensen kell nyomon követnie, mert ez az egyetlen módja annak, hogy pillanatképet kapjon a valódi teljesítményjellemzőkről. Ha azonban kifejezetten a HoloLens teljesítménykorlátozásai nélkül szeretne nyomozni, vagy csak meg szeretné tanulni, hogyan kell használni a WPA-t, és nem kell reális nyomon követnie, itt van, hogyan kell ezt megtenni.

### <a name="wpr-configuration"></a>WPR-konfiguráció

1. Indítsa el a [Windows Teljesítményrögzítőt](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-recorder) a *start menüből*.
1. **További beállítások kibontása**
1. Kattintson **a Profilok hozzáadása gombra...**
1. Válassza ki az *AzureRemoteRenderingNetworkProfiling.wprp*fájlt. Ezt a fájlt az ARR SDK *eszközök/ETLProfiles*területen találja.
   A profil most antól a WPR-ben jelenik meg az *Egyéni mérések*csoportban. Győződjön meg róla, hogy ez az egyetlen engedélyezett profil.
1. *Első szintű osztályozás kibontása:*
    * Ha mindössze annyit szeretne tenni, hogy rögzíti az ARR hálózati események gyors nyomkövetését, **tiltsa le** ezt a beállítást.
    * Ha az ARR hálózati eseményeket más rendszerjellemzőkkel, például a processzor- vagy memóriahasználattal kell korrelálnia, **engedélyezze** ezt a beállítást.
    * Ha engedélyezi ezt a beállítást, a nyomkövetés valószínűleg több gigabájt méretű lesz, és hosszú időt vesz igénybe a WPA-ban való mentés hez és megnyitáshoz.

Ezt követően a WPR konfiguráció így kell kinéznie:

![WPR-konfiguráció](./media/wpr.png)

### <a name="recording"></a>Felvétel

A **nyomkövetés** rögzítésének megkezdéséhez kattintson a Start gombra. A felvételt bármikor elindíthatja és leállíthatja; nem kell, hogy zárja be a kérelmet, mielőtt ezt. Mint látható, nem kell megadnia, hogy melyik alkalmazást kell nyomon követnie, mivel az ETW mindig rögzíti a nyomkövetést az egész rendszerre. A `wprp` fájl határozza meg, hogy milyen típusú eseményeket kell rögzíteni.

A **Mentés gombra** kattintva leállíthatja a felvételt, és megadhatja, hogy hol tárolja az ETL-fájlt.

Most már van egy ETL fájl, amit vagy közvetlenül a WPA-ban nyit meg, vagy elküldheti valaki másnak.

## <a name="recording-a-trace-on-a-hololens"></a>Nyomfelvétel holoLensen

Ha egy HoloLensen szeretne nyomkövetést rögzíteni, indítsa el az eszközt, és adja meg annak IP-címét egy böngészőben az *Eszközportál*megnyitásához.

![Eszközportál](./media/wpr-hl.png)

1. A bal oldalon nyissa meg a *Teljesítmény > teljesítménykövetés t.*
1. **Egyéni profilok kijelölése**
1. Kattintson **a Tallózás gombra...**
1. Válassza ki az *AzureRemoteRenderingNetworkProfiling.wprp*fájlt. Ezt a fájlt az ARR SDK *eszközök/ETLProfiles*területen találja.
1. Kattintson **a Nyomkövetés indítása gombra.**
1. A HoloLens most egy nyomot rögzít. Győződjön meg arról, hogy elindítja a vizsgálni kívánt teljesítményproblémákat. Ezután kattintson **a Nyomon követés leállítása gombra.**
1. A nyomkövetés a weblap alján jelenik meg. Kattintson a lemez ikonra a jobb oldalon az ETL fájl letöltéséhez.

Most már van egy ETL fájl, amit vagy közvetlenül a WPA-ban nyit meg, vagy elküldheti valaki másnak.

## <a name="analyzing-traces-with-wpa"></a>Nyomkövetések elemzése WPA-val

### <a name="wpa-basics"></a>A WPA alapjai

A Windows Teljesítményanalizáló az ETL-fájlok megnyitásának és a nyomkövetések vizsgálatának szabványos eszköze. A WPA működésének magyarázata nincs kivonva a cikk hatálya alá. Első lépésekhez tekintse meg az alábbi forrásokat:

* Tekintse meg a [bevezető videókat](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-analyzer) az első áttekintésért.
* A WPA-nak van egy *Első lépések* lapja, amely ismerteti a gyakori lépéseket. Tekintse meg a rendelkezésre álló témákat. Különösen az "Adatok megtekintése" alatt kap egy gyors bevezetőt, hogyan hozhat létre grafikonokat adott adatokhoz.
* Van kiváló információ [ezen a honlapon](https://randomascii.wordpress.com/2015/09/24/etw-central/), azonban nem minden fontos a kezdők számára.

### <a name="graphing-data"></a>Adatok grafikusítása

Az ARR nyomon követésének megkezdéséhez a következő darabokat jó tudni.

![Teljesítménygrafikon](./media/wpa-graph.png)

A fenti képen egy nyomkövetési adatokat bemutató táblázat és ugyanazon adatok grafikonábrázolása látható.

Az alján lévő táblázatban vegye figyelembe a sárga (arany) sávot és a kék sávot. Ezeket a sávokat bármelyik pozícióba húzhatja, és elhelyezheti.

A **sárga sávtól balra lévő összes oszlopot** **a gombként értelmezi a (nagy)** A billentyűk a fa szerkezetére szolgálnak a bal felső ablakban. Itt van két *kulcsfontosságú* oszlop, a "Szolgáltató neve" és a "Feladat neve". Ennek következtében a bal felső ablakban lévő faszerkezet két szint mély. Ha átrendezi az oszlopokat, vagy oszlopokat ad hozzá vagy távolít el a kulcsterületről, a fanézet szerkezete megváltozik.

**A kék sávtól jobbra lévő oszlopok** a jobb felső ablakban lévő **grafikonmegjelenítéséhez** használatosak. Az esetek többségében csak az első oszlopot használja a rendszer, de egyes gráfmódok több adatoszlopot igényelnek. A vonaldiagramok működéséhez be kell állítani az oszlop *összesítési módját.* Használja az "Átlagos" vagy a "Max" értéket. Az aggregációs mód a diagram értékének meghatározására szolgál egy adott képpontban, amikor egy képpont több eseményt tartalmazó tartományt fed le. Ez az aggregáció "Sum" beállításával, majd nagyítással és kicsinyítéssel figyelhető meg.

A középső oszlopoknak nincs különösebb jelentésük.

![Események nézet](./media/wpa-event-view.png)

Az *Általános események nézet szerkesztőben* beállíthatja az összes oszlop megjelenítését, az összesítési módot, a rendezést és a kulcsként vagy grafikusként használt oszlopokat. A fenti példában a **2.** A 2-es mező általában az ETW-események első *egyéni adatmezője,* és így az ARR "FrameStatistics" események, amelyek valamilyen hálózati késési értéket képviselnek. Engedélyezze, hogy más "Mező" oszlopok további értékeket láthassanak az eseményből.

### <a name="presets"></a>Készletek

A nyomkövetés megfelelő elemzéséhez ki kell találnia a saját munkafolyamatát és az előnyben részesített adatok megjelenítését. Ahhoz azonban, hogy gyors áttekintést kapjunk az ARR-specifikus eseményekről, a Windows Szoftvervédelmi Platform profilját és a készletekfájljait az *Eszközök/ ETLProfiles*mappába foglaljuk. Teljes profil betöltéséhez válassza a WPA menüsor *Profilok > Alkalmazása lehetőséget,* vagy nyissa meg a Saját készletek *panelt* *(Ablak > Saját készletek)* és válassza az *Importálás*lehetőséget. Az előbbi létrehoz egy teljes WPA konfigurációt, mint az alábbi képen. Ez utóbbi csak a különböző nézetkonfigurációkhoz való készleteket teszi elérhetővé, és lehetővé teszi, hogy gyorsan megnyisson egy nézetet az ARR eseményadatok egy adott darabjának megtekintéséhez.

![Készletek](./media/wpa-arr-trace.png)

A fenti képen a különböző ARR-specifikus események nézeteit, valamint a teljes CPU-kihasználtság nézetét jeleníti meg.

## <a name="next-steps"></a>További lépések

* [Kiszolgálóoldali teljesítménylekérdezések](../overview/features/performance-queries.md)

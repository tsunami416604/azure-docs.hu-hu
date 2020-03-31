---
title: Az Azure Monitor-munkafüzetek áttekintése
description: Az összetett jelentések egyszerűsítése előre összeállított és egyéni paraméterezett munkafüzetekkel
author: mrbullwinkle
manager: carmonm
services: azure-monitor
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 29e675f3ae35df9211f58d45ad8450566d67a588
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658234"
---
# <a name="azure-monitor-workbooks"></a>Azure Monitor-munkafüzetek

A munkafüzetek rugalmas vásznat biztosítanak az adatok elemzéséhez és az Azure Portalon belüli gazdag vizuális jelentések létrehozásához. Lehetővé teszik, hogy az Azure-ból származó több adatforrást is megaknázhassa, és egyesítse azokat egységes interaktív élményekbe. 

## <a name="data-sources"></a>Adatforrások

A munkafüzetek az Azure-on belül több forrásból származó adatokat is lekérdezhetnek. A munkafüzetek szerzői átalakíthatják ezeket az adatokat, hogy betekintést nyújtsanak az alapul szolgáló összetevők rendelkezésre állásába, teljesítményébe, használatára és általános állapotára vonatkozóan. Például a teljesítménynaplók elemzése a virtuális gépekről a magas PROCESSZOR- vagy kevés memóriapéldányok azonosítása érdekében, és az eredmények megjelenítése rácsként egy interaktív jelentésben.
  
A munkafüzetek valódi ereje azonban az, hogy egyetlen jelentésben egyesítheti a különböző forrásokból származó adatokat. Ez lehetővé teszi az összetett erőforrásnézetek létrehozását, vagy egyesítéseket az erőforrások között, amelyek gazdagabb adatokat és elemzéseket tesznek lehetővé, amelyek egyébként lehetetlenek lennének.

A munkafüzetek jelenleg kompatibilisek a következő adatforrásokkal:

* [Naplók](workbooks-data-sources.md#logs)
* [Mutatókat](workbooks-data-sources.md#metrics)
* [Azure Resource Graph](workbooks-data-sources.md#azure-resource-graph)
* [Riasztások (előnézet)](workbooks-data-sources.md#alerts-preview)
* [Számítási feladatok állapota (előzetes verzió)](workbooks-data-sources.md#workload-health-preview)
* [Azure-erőforrás állapota (előzetes verzió)](workbooks-data-sources.md#azure-resource-health)
* [Azure Adatkezelő (előzetes verzió)](workbooks-data-sources.md#azure-data-explorer-preview)

## <a name="visualizations"></a>Vizualizációk

A munkafüzetek az adatok megjelenítésére szolgáló funkciók gazdag készletét biztosítják. Az egyes vizualizációs típusokrészletes példáit az alábbi példahivatkozásokon tekintheti meg:

* [Szöveg](workbooks-visualizations.md#text)
* [Diagramok](workbooks-visualizations.md#charts)
* [Rácsok](workbooks-visualizations.md#grids)
* [Csempe](workbooks-visualizations.md#tiles)
* [Fák](workbooks-visualizations.md#trees)
* [Grafikonok](workbooks-visualizations.md#graphs)

![Példa munkafüzet-megjelenítések](./media/workbooks-overview/visualizations.png)

## <a name="getting-started"></a>Első lépések

A munkafüzetek élményének megismeréséhez először keresse meg az Azure Monitor szolgáltatást. Ez úgy teheti meg, hogy beírja a **Figyelő** t az Azure Portal keresőmezőjébe.

Ezután válassza **a Munkafüzetek (előnézet) lehetőséget.**

![Képernyőkép a munkafüzetek előnézetének gombjáról, piros mezőben kiemelve](./media/workbooks-overview/workbooks-preview.png)

### <a name="gallery"></a>Katalógus

Ezzel a munkafüzetek galériájába kerül:

![Képernyőkép az Azure Monitor munkafüzetgyűjteménynézetéről](./media/workbooks-overview/gallery.png)

### <a name="workbooks-versus-workbook-templates"></a>Munkafüzetek és munkafüzetsablonok

A _munkafüzetek_ zöld színnel és számos munkafüzetsablon lila színnel _jelennek_ meg. A sablonok válogatott jelentésekként szolgálnak, amelyeket több felhasználó és csapat rugalmas újrafelhasználására terveztek. A sablon megnyitása átmeneti munkafüzetet hoz létre, amely a sablon tartalmával van feltöltve. 

Módosíthatja a sablonalapú munkafüzet paramétereit, és anélkül végezhet elemzést, hogy félne a munkatársak jövőbeli jelentésezőélményének megtörésétől. Ha megnyit egy sablont, bizonyos módosításokat végez, majd válassza ki a mentés ikont, amelyet munkafüzetként fog menteni, amely aztán zöldszínnel jelenik meg, érintetlenül hagyva az eredeti sablont. 

A motorháztető alatt a sablonok is különböznek a mentett munkafüzetektől. A munkafüzet mentése társított Azure Resource Manager-erőforrást hoz létre, míg a sablon megnyitásakor létrehozott átmeneti munkafüzethez nincs egyedi erőforrás társítva. Ha többet szeretne megtudni arról, hogyan kezeli a hozzáférés-vezérlést a munkafüzetekben, olvassa el a [munkafüzetek hozzáférés-vezérlési cikkét.](workbooks-access-control.md)

### <a name="exploring-a-workbook-template"></a>Munkafüzetsablon feltárása

**Válassza az Alkalmazáshiba-elemzés** lehetőséget az alapértelmezett alkalmazásmunkafüzet-sablonok megtekintéséhez.

![Képernyőkép az alkalmazáshiba-elemzési sablonról](./media/workbooks-overview/failure-analysis.png)

Amint azt korábban említettük, a sablon megnyitása ideiglenes munkafüzetet hoz létre, amelyekkel kapcsolatba léphet. Alapértelmezés szerint a munkafüzet olvasási módban nyílik meg, amely csak az eredeti sablon szerzője által létrehozott tervezett elemzési élmény adatait jeleníti meg.

Ebben az esetben az adott munkafüzet, a tapasztalat interaktív. Módosíthatja az előfizetést, a célzott alkalmazásokat és a megjeleníteni kívánt adatok időtartományát. Miután elvégzett e beállításokat, a HTTP-kérelmek rácsa is interaktív, így az egyes sorok kijelölésével a jelentés alján lévő két diagramon megjelennek az adatok.

### <a name="editing-mode"></a>Szerkesztési mód

Ha meg szeretné tudni, hogyan van összerakva ez a munkafüzetsablon, a Szerkesztés lehetőséget választva szerkesztési módra kell **cserélnie.** 

![Képernyőkép az alkalmazáshiba-elemzési sablonról](./media/workbooks-overview/edit.png)

Miután áttért a szerkesztési módra, számos **szerkesztési** mező jelenik meg a jobb oldalon, amelyek megfelelnek a munkafüzet minden egyes aspektusának.

![Képernyőkép: Szerkesztés gomb](./media/workbooks-overview/edit-mode.png)

Ha közvetlenül a kérelemadatok rácsa alatt választjuk ki a szerkesztés i. gombot, láthatjuk, hogy a munkafüzetünk ezen része egy Kusto-lekérdezésből áll egy Application Insights-erőforrás ból származó adatok alapján.

![Képernyőkép az alapul szolgáló Kusto-lekérdezésről](./media/workbooks-overview/kusto.png)

A jobb **oldali Szerkesztés** gombra kattintva megjelennek a munkafüzeteket alkotó alapvető összetevők, például a markdown-alapú [szövegmezők,](workbooks-visualizations.md#text) [a paraméterválasztó](workbooks-parameters.md) felhasználói felület elemei és más [diagram/vizualizációs típusok.](workbooks-visualizations.md) 

Az előre elkészített sablonok szerkesztési módban történő feltárása, majd módosítása az igényeinek megfelelően, és saját egyéni munkafüzet mentése kiváló módja annak, hogy megismerje, mi lehetséges az Azure Monitor munkafüzetekkel.

## <a name="pinning-visualizations"></a>Képi megjelenítések rögzítése

A munkafüzetben a szöveg-, lekérdezés- és mérőszámok lépései rögzíthetők az elemek en lévő pin gombbal, miközben a munkafüzet pin módban van, vagy ha a munkafüzet szerzője engedélyezte az adott elem beállításait, hogy láthatóvá tegyék a pinikont. 

A pin mód eléréséhez kattintson a **Szerkesztés** gombra a szerkesztési módba való belépéshez, és jelölje ki a kék tű ikont a felső sávon. Ekkor egy egyedi pinikon jelenik meg a munkafüzet minden megfelelő részének *Szerkesztés* mezője felett a képernyő jobb oldalán.

![Rögzítési élmény](./media/workbooks-overview/pin-experience.png)

> [!NOTE]
> A munkafüzet állapota a rögzítéskor mentésre kerül, és az irányítópulton rögzített munkafüzetek nem frissülnek, ha az alapul szolgáló munkafüzet módosul. A rögzített munkafüzetrész frissítéséhez törölnie kell, majd újra rögzítenie kell azt a részt.

## <a name="dashboard-time-ranges"></a>Irányítópult időtartományai

A rögzített munkafüzet-lekérdezési részek tiszteletben tartják az irányítópult időtartományát, ha a rögzített elem *időtartomány-paraméter használatára* van konfigurálva. Az irányítópult időtartomány-értéke lesz az időtartomány-paraméter értéke, és az irányítópult időtartományának bármilyen módosítása a rögzített elem frissítését eredményezi. Ha egy rögzített alkatrész az irányítópult időtartományát használja, a rögzített alkatrészfrissítés alcíme jelenik meg, hogy az irányítópult időtartománya mindig megjelenjen, amikor az időtartomány megváltozik. 

Ezenkívül a rögzített munkafüzet-alkatrészek időtartomány-paraméterrel automatikusan frissülnek az irányítópult időtartománya által meghatározott sebességgel. A lekérdezés legutóbbi lefutott a rögzített rész alcímében jelenik meg.

Ha egy rögzített lépés kifejezetten beállított időtartományt tartalmaz (nem használ időtartomány-paramétert), akkor az irányítópulthoz mindig ezt az időtartományt fogja használni, függetlenül az irányítópult beállításaitól. A rögzített rész alcíme nem jeleníti meg az irányítópult időtartományát, és a lekérdezés nem frissül automatikusan az irányítópulton. A felirat a lekérdezés legutóbbi végrehajtásakor jelenik meg.

> [!NOTE]
> Az *egyesítési* adatforrást használó lekérdezések jelenleg nem támogatottak az irányítópultokra való rögzítéskor.

## <a name="sharing-workbook-templates"></a>Munkafüzetsablonok megosztása

Miután megkezdte létrehozni saját munkafüzetsablonjait, érdemes lehet megosztania azokat a szélesebb közösséggel. Ha többet szeretne megtudni, és más sablonokat is meg szeretne vizsgálni, amelyek nem részei az alapértelmezett Azure Monitor-gyűjteménynézetnek, látogasson el a [GitHub-tárházunkba.](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md) A meglévő munkafüzetek böngészéséhez keresse fel a [Munkafüzet-könyvtárat](https://github.com/microsoft/Application-Insights-Workbooks/tree/master/Workbooks) a GitHubon.

## <a name="next-step"></a>Következő lépés

* [Ismerkedés a](workbooks-visualizations.md) munkafüzetekkel, számos gazdag vizualizációs lehetőséggel.
* [Szabályozhatja](workbooks-access-control.md) és megoszthatja a munkafüzet erőforrásaihoz való hozzáférést.

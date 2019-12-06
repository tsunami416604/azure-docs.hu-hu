---
title: Azure Monitor munkafüzetek áttekintése
description: Összetett jelentéskészítés egyszerűsítése előre elkészített és egyéni paraméterekkel rendelkező munkafüzetekből
author: mrbullwinkle
manager: carmonm
services: azure-monitor
ms.service: azure-monitor
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: cf11ce3a77fb47b7d8586ef56141a72cd0a10c2e
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872859"
---
# <a name="azure-monitor-workbooks"></a>Munkafüzetek Azure Monitor

A munkafüzetek rugalmas vásznat biztosítanak az adatelemzéshez és a Azure Portalon belüli Rich vizualizációs jelentések létrehozásához. Lehetővé teszik, hogy több adatforrásra koppintson az Azure-ból, és egyesítse azokat egységes interaktív környezetekben. 

## <a name="data-sources"></a>Adatforrások

A munkafüzetek több forrásból származó adatok lekérdezésére is képesek az Azure-ban. A munkafüzetek szerzője átalakíthatja ezeket az adatokat, hogy betekintést nyújtson az alapul szolgáló összetevők rendelkezésre állásával, teljesítményével, kihasználtságával és általános állapotával. A virtuális gépekről származó teljesítményadatokat például a magas CPU-vagy alacsony memória-példányok azonosítására, valamint az eredmények egy interaktív jelentésben lévő rácsként való megjelenítésére lehet elemezni.
  
A munkafüzetek valódi ereje azonban a különböző forrásokból származó adatok egyetlen jelentésen belüli egyesítésének lehetősége. Ez lehetővé teszi az összetett erőforrás-nézetek létrehozását, illetve az erőforrások közötti összekapcsolást, amely lehetővé teszi, hogy a gazdagabb adatmennyiség és az egyéb módon lehetetlenné válik.

A munkafüzetek jelenleg a következő adatforrásokkal kompatibilisek:

* [Naplók](workbooks-data-sources.md#logs)
* [Metrikák](workbooks-data-sources.md#metrics)
* [Azure-erőforrás gráf](workbooks-data-sources.md#azure-resource-graph)
* [Riasztások (előzetes verzió)](workbooks-data-sources.md#alerts-preview)
* [Munkaterhelés állapota (előzetes verzió)](workbooks-data-sources.md#workload-health-preview)
* [Azure Resource Health (előzetes verzió)](workbooks-data-sources.md#azure-resource-health)
* [Azure Adatkezelő (előzetes verzió)](workbooks-data-sources.md#azure-data-explorer-preview)

## <a name="visualizations"></a>Vizualizációk

A munkafüzetek számos képességet biztosítanak az adatmegjelenítéshez. Az egyes vizualizációs típusok részletes példáit az alábbi hivatkozásokra kattintva tekintheti meg:

* [Szöveg](workbooks-visualizations.md#text)
* [Diagramok](workbooks-visualizations.md#charts)
* [Rácsok](workbooks-visualizations.md#grids)
* [Csempék](workbooks-visualizations.md#tiles)
* [Fák](workbooks-visualizations.md#trees)
* [Grafikonok](workbooks-visualizations.md#graphs)

![Példa munkafüzet-vizualizációk](./media/workbooks-overview/visualizations.png)

## <a name="getting-started"></a>Bevezetés

A munkafüzetek felületének megismeréséhez először navigáljon a Azure Monitor szolgáltatáshoz. Ezt úgy teheti meg, hogy beírja a **figyelő** kifejezést a Azure Portal található keresőmezőbe.

Ezután válassza a **munkafüzetek (előzetes verzió)** lehetőséget.

![Képernyőkép – a munkafüzetek előnézet gombja kiemelve egy piros mezőben](./media/workbooks-overview/workbooks-preview.png)

### <a name="gallery"></a>Galéria

Ekkor megjelenik a munkafüzetek gyűjteménye:

![Képernyőkép Azure Monitor munkafüzetek gyűjteményének nézetéről](./media/workbooks-overview/gallery.png)

### <a name="workbooks-versus-workbook-templates"></a>Munkafüzetek és munkafüzet-sablonok

Megtekintheti a _munkafüzetet_ zöld színnel és számos _munkafüzet-sablonban_ a lila nyelven. A sablonok a több felhasználó és csapat számára rugalmas újrafelhasználásra tervezett, kurátori jelentésként szolgálnak. A sablon megnyitása egy átmeneti munkafüzetet hoz létre, amely a sablon tartalmával van feltöltve. 

Módosíthatja a sablon alapú munkafüzet paramétereit, és elvégezheti az elemzést anélkül, hogy a munkatársainak jövőbeli jelentéskészítési élményét kellene megszakítani. Ha megnyit egy sablont, hajtson végre néhány módosítást, majd válassza a Mentés ikont, majd mentse a sablont egy olyan munkafüzetként, amely az eredeti sablon érintetlen állapotának elhagyásakor zöldre vált. 

A motorháztető alatt a sablonok a mentett munkafüzetektől is eltérhetnek. A munkafüzet mentése egy társított Azure Resource Manager erőforrást hoz létre, míg a sablon megnyitásakor létrehozott átmeneti munkafüzethez nincs egyedi erőforrás társítva. Ha többet szeretne megtudni arról, hogy a hozzáférés-vezérlés hogyan legyen kezelve a munkafüzetekben, tekintse meg a [munkafüzetek hozzáférés-vezérlése című cikket](workbooks-access-control.md).

### <a name="exploring-a-workbook-template"></a>Munkafüzet-sablon feltárása

Válassza az **alkalmazáshiba elemzése** lehetőséget az egyik alapértelmezett alkalmazás-munkafüzet-sablon megtekintéséhez.

![Képernyőkép az alkalmazáshiba-elemzési sablonról](./media/workbooks-overview/failure-analysis.png)

Amint azt korábban már említettük, a sablon megnyitása egy ideiglenes munkafüzetet hoz létre, amely lehetővé teszi a kommunikációt. Alapértelmezés szerint a munkafüzet olvasási módban nyílik meg, amely csak az eredeti sablon szerzője által létrehozott kívánt elemzési felület adatait jeleníti meg.

Az adott munkafüzet esetében az élmény interaktív. Módosíthatja az előfizetést, a megcélzó alkalmazásokat és a megjeleníteni kívánt adattartományt. Ha elvégezte ezeket a beállításokat, a HTTP-kérések rácsa is interaktív, amely során az egyes sorok kiválasztásakor a jelentés alján található két diagramon megváltoztathatja a megjelenített adatmennyiséget.

### <a name="editing-mode"></a>Szerkesztési mód

Annak megismeréséhez, hogy a munkafüzet-sablon hogyan legyen összekapcsolva, **a Szerkesztés gombra**kattintva kell cserélnie a szerkesztési módba. 

![Képernyőkép az alkalmazáshiba-elemzési sablonról](./media/workbooks-overview/edit.png)

Miután átváltotta a szerkesztési módot, megfigyelheti, hogy számos **szerkesztési** mező jelenik meg a munkafüzet minden egyes aspektusának megfelelő módon.

![Képernyőkép a Szerkesztés gombról](./media/workbooks-overview/edit-mode.png)

Ha a Szerkesztés gombot azonnal bejelöli a kérelmek adatainak rácsa alatt, láthatjuk, hogy a munkafüzet ezen része egy Application Insights erőforrásból származó adatokkal kapcsolatos Kusto-lekérdezést tartalmaz.

![Képernyőkép az alapul szolgáló Kusto-lekérdezésről](./media/workbooks-overview/kusto.png)

A jobb oldalon található egyéb **szerkesztési** gombokra kattintva megjelenítheti a munkafüzetek, például a Markdown-alapú [szövegmezők](workbooks-visualizations.md#text), a [paraméter-kiválasztási](workbooks-parameters.md) felhasználóifelület-elemek, valamint az egyéb [diagram/vizualizáció típusok](workbooks-visualizations.md)számát. 

Szerkessze az előre elkészített sablonokat szerkesztési módban, majd módosítsa őket az igényeinek megfelelően, és mentse a saját egyéni munkafüzetét, hogy megismerkedjen a Azure Monitor munkafüzetek lehetséges lehetőségeivel.

## <a name="pinning-visualizations"></a>Vizualizációk rögzítése

A munkafüzetben lévő szöveg-, lekérdezési és mérőszám-lépések a PIN-kód gomb használatával állíthatók be, miközben a munkafüzet PIN-módban van, vagy ha a munkafüzet szerzője engedélyezte az adott elemhez tartozó beállításokat, hogy a PIN-ikon látható legyen. 

A PIN-kód mód eléréséhez kattintson a **Szerkesztés** gombra a szerkesztési mód megadásához, majd a felső sávon a kék PIN-kód ikonra. A képernyő jobb oldalán megjelenik egy egyedi PIN-kód ikon, amely a megfelelő munkafüzet részének *szerkesztési* mezője fölé kerül.

![PIN-kód felhasználói élmény](./media/workbooks-overview/pin-experience.png)

> [!NOTE]
> A rendszer a PIN-kód időpontjában menti a munkafüzet állapotát, és az irányítópulton rögzített munkafüzetek nem frissülnek, ha módosul a mögöttes munkafüzet. Egy rögzített munkafüzet-rész frissítéséhez törölnie kell és újra rögzítenie kell ezt a részt.

## <a name="dashboard-time-ranges"></a>Irányítópult időtartományai

A rögzített munkafüzet-lekérdezési részek figyelembe veszik az irányítópult időtartományát, ha a rögzített elem *időtartomány* -paraméter használatára van konfigurálva. Az irányítópult időtartományának értéke az időtartomány paraméterének értéke lesz, az irányítópult időtartományának bármilyen módosítása pedig a rögzített elem frissítését eredményezi. Ha egy rögzített rész az irányítópult időtartományát használja, látni fogja a rögzített rész frissítésének alcímét, hogy megjelenjen az irányítópult időtartománya, amikor az időtartomány megváltozik. 

Emellett az időtartomány-paramétert használó rögzített munkafüzet-részek automatikusan frissülnek az irányítópult időtartománya által meghatározott sebességgel. Az utolsó alkalommal, amikor a lekérdezés futott, megjelenik a rögzített rész alcíme.

Ha egy rögzített lépés explicit módon beállított időtartományt tartalmaz (nem használ időtartomány-paramétert), akkor a rendszer az irányítópult beállításaitól függetlenül mindig használni fogja az adott időtartományt. A rögzített rész alcíme nem jeleníti meg az irányítópult időtartományát, és a lekérdezés nem fog automatikusan frissülni az irányítópulton. Az alcím a lekérdezés legutóbbi futásakor jelenik meg.

> [!NOTE]
> Az *egyesítési* adatforrást használó lekérdezések jelenleg nem támogatottak az irányítópultokra való rögzítéskor.

## <a name="sharing-workbook-templates"></a>Munkafüzet-sablonok megosztása

Miután elindította saját munkafüzet-sablonjait, érdemes megosztania azt a szélesebb Közösséggel. Ha többet szeretne megtudni, és más olyan sablonokat is meg szeretne vizsgálni, amelyek nem részei az alapértelmezett Azure Monitor Gallery nézetnek, látogasson el a [GitHub-tárházba](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md). A meglévő munkafüzetek tallózásához látogasson el a GitHubon található [munkafüzet-tárba](https://github.com/microsoft/Application-Insights-Workbooks/tree/master/Workbooks) .

## <a name="next-step"></a>Következő lépés

* [Ismerkedjen](workbooks-visualizations.md) meg a munkafüzetek számos gazdag vizualizációs lehetőségével.
* A munkafüzet erőforrásaihoz való hozzáférés [szabályozása](workbooks-access-control.md) és megosztása.

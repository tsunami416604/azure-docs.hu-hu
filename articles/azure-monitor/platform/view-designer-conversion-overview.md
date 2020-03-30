---
title: Az Azure Monitor nézet tervezője a munkafüzetek áttűnési útmutatója
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 234da921b4f0d1243ca8cfdb12ba2d851db2b43f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658693"
---
# <a name="azure-monitor-view-designer-to-workbooks-transition-guide"></a>Az Azure Monitor nézet tervezője a munkafüzetek áttűnési útmutatója
[A View designer](view-designer.md) az Azure Monitor egyik szolgáltatása, amely lehetővé teszi, hogy egyéni nézeteket hozzon létre, amelyek segítségével megjelenítheti az adatokat a Log Analytics-munkaterületen, diagramokkal, listákkal és ütemtervekkel. Ezeket fokozatosan megszüntetik, és olyan munkafüzetekre cserélik, amelyek további funkciókat biztosítanak. Ez a cikk áttekintést nyújt a meglévő nézetek munkafüzetekké alakításának folyamatáról.

## <a name="workbooks-overview"></a>Munkafüzetek – áttekintés
[A munkafüzetek a](../insights/vminsights-workbooks.md) szöveget, [a naplólekérdezéseket,](../log-query/query-language.md)a mutatókat és a paramétereket gazdag interaktív jelentésekké egyesítik. Az Azure-erőforrásokhoz azonos hozzáféréssel rendelkező csapattagok is szerkeszthetők a munkafüzetek.

A munkafüzetek olyan esetekben hasznosak, mint például:

-   A virtuális gép használatának feltárása, ha nem ismeri előre az érdeklődési mutatókat: CPU-használat, lemezterület, memória, hálózati függőségek stb. A többi használatelemző eszközzel ellentétben a munkafüzetek segítségével többféle vizualizációt és elemzést kombinálhat, így nagyszerűek az ilyen típusú szabad formájú feltáráshoz.
-   Elmagyarázza a csapatnak, hogyan teljesít egy nemrég kiépített virtuális gép, a fő számlálók és más naplóesemények metrikáinak megjelenítésével.
-   A virtuális gép átméretezési kísérletének eredményei megosztása a csapat többi tagjával. Elmagyarázhatja a kísérlet céljait a szöveggel, majd megjelenítheti a kísérlet kiértékeléséhez használt használati metrikákat és elemzési lekérdezéseket, valamint egyértelmű kijelentéseket arról, hogy az egyes metrikák a cél felett vagy alatt voltak-e.
-   Jelentés a szolgáltatáskimaradás hatása a virtuális gép használatára, adatok kombinálásával, szöveges magyarázattal és a jövőbeli kimaradások megelőzésére szolgáló következő lépések megvitatásával.


## <a name="why-convert-view-designer-dashboards-to-workbooks"></a>Miért érdemes a nézettervezői irányítópultokat munkafüzetté konvertálni?

A View designer lehetővé teszi különböző lekérdezésalapú nézetek és vizualizációk létrehozására. Számos általános testreszabási lehetőség azonban továbbra is korlátozott. Ezek közé tartozik a rácsok és csempék elrendezésének formázása, illetve az alternatív grafikus elemek kiválasztása az adatok szemléltetéséhez. A nézettervező összesen kilenc különböző csempére korlátozódik az adatok megjelenítéséhez.

A Munkafüzetek egy olyan platform, amellyel felszabadíthatja az adatokban rejlő teljes potenciált. A munkafüzetek nem csak megőrzik az összes képességet, hanem további funkciókat is támogatnak szöveggel, mérőszámokkal, paraméterekkel és még sok másval. A munkafüzetek például lehetővé teszik a felhasználók számára a sűrű rácsok összevonását, és keresési sávok hozzáadását az adatok egyszerű szűréséhez és elemzéséhez. 

### <a name="advantages-of-using-workbooks-over-view-designer"></a>A munkafüzetek és a Tervező nézet használatának előnyei

* Támogatja a naplók és a metrikák.
* Lehetővé teszi mind a személyes nézeteket az egyéni hozzáférés-vezérléshez, mind a megosztott munkafüzetnézetekhez.
* Egyéni elrendezési beállítások tabulátorokkal, méretezési és méretezési vezérlőkkel.
* Több Log Analytics-munkaterületen, Application Insights-alkalmazásban és előfizetésben történő lekérdezés támogatása.
* Engedélyezi azokat az egyéni paramétereket, amelyek dinamikusan frissítik a társított diagramokat és vizualizációkat.
* Sablongaléria támogatása nyilvános GitHubról.

Bár ez az útmutató egyszerű lépésekkel közvetlenül újra létrehozhat ja a gyakran használt nézettervezői nézeteket, a munkafüzetek lehetővé teszik a felhasználók számára, hogy saját egyéni vizualizációikat és mutatókat hozzanak létre és tervezzenek. A következő képernyőkép a [Munkaterület használati sablonból származik,](https://go.microsoft.com/fwlink/?linkid=874159&resourceId=Azure%20Monitor&featureName=Workbooks&itemId=community-Workbooks%2FAzure%20Monitor%20-%20Workspaces%2FWorkspace%20Usage&workbookTemplateName=Workspace%20Usage&func=NavigateToPortalFeature&type=workbook) és egy példát mutat be arra, hogy milyen munkafüzeteket tudnak létrehozni:


![Példa munkafüzet-alkalmazásra](media/view-designer-conversion-overview/workbook-template-example.jpg)


## <a name="how-to-start-using-workbooks"></a>Munkafüzetek használatának megkezdése
A munkafüzetekből származó munkafüzetek megnyitása a Log Analytics-munkaterületeken elemként engedélyezve van az oldalsó navigációs sávon, közvetlenül a nézettervező helye alatt.

![Munkafüzetek navigációja](media/view-designer-conversion-overview/workbooks-nav.png)

A kijelölést követően egy gyűjtemény jelenik meg, amely felsorolja a munkaterülethez mentett munkafüzeteket és sablonokat.

![Munkafüzetek gyűjteménye](media/view-designer-conversion-overview/workbooks-gallery.png)

Új munkafüzet indításához válassza az **Üres sablont** a **Rövid útmutató**csoportban, vagy az **Új** ikont a felső navigációs sávon. Sablonok megtekintéséhez vagy a mentett munkafüzetekhez való visszatéréshez jelölje ki az elemet a gyűjteményből, vagy keresse meg a nevet a keresősávban.

Munkafüzet mentéséhez a jelentést egy adott címmel, előfizetéssel, erőforráscsoporttal és helynel kell mentenie.
A munkafüzet automatikusan kitölti ugyanazokat a beállításokat, mint a LA munkaterület, ugyanazzal az előfizetéssel, erőforráscsoporttal, azonban a felhasználók módosíthatják ezeket a jelentésbeállításokat. A munkafüzeteket alapértelmezés szerint a Saját jelentések között menti a *program,* amelyet csak az egyes felhasználók férhetnek hozzá. Közvetlenül is menthetők a megosztott jelentésekbe, vagy később is megoszthatóak.

![Munkafüzetek mentése](media/view-designer-conversion-overview/workbooks-save.png)

## <a name="next-steps"></a>További lépések

- [Konvertálási beállítások](view-designer-conversion-options.md)

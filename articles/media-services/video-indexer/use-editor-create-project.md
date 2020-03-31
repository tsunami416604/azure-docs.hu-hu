---
title: Projektek létrehozása a Video Indexer szerkesztővel
titleSuffix: Azure Media Services
description: Ez a témakör bemutatja, hogyan lehet a Videó indexelő szerkesztővel projekteket létrehozni.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/02/2019
ms.author: juliako
ms.openlocfilehash: 9f16ab34dc9b37806f9c58b22a3f02afe839632e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73839170"
---
# <a name="use-the-video-indexer-editor-to-create-projects"></a>Projektek létrehozása a Video Indexer szerkesztővel

Video Indexer honlapján, lehetővé teszi, hogy használja a videók mély betekintést: megtalálni a megfelelő médiatartalmat, keresse meg a részeket, hogy érdekli, és használja az eredményeket, hogy hozzon létre egy teljesen új projektet. A létrehozást követően a projekt megjeleníthető és letölthető a Video Indexerből, és használható a saját szerkesztőalkalmazásokban vagy az alsóbb rétegbeli munkafolyamatokban.

Néhány forgatókönyv, ahol hasznosnak találhatja ezt a funkciót: 

* Filmkiemelések készítése előzetesekhez.
* A régi klipek videók hírek vet.
* Rövidebb tartalom létrehozása a közösségi médiában.

Ez a cikk bemutatja, hogyan hozhat létre teljesen új projektet, és hogyan hozhat létre projektet a fiókjában lévő videóból.

## <a name="create-new-project-and-manage-videos"></a>Új projekt létrehozása és videók kezelése

1. Nyissa meg a [Video Indexer](https://www.videoindexer.ai/) webhelyét, és jelentkezzen be.
1. Válassza a **Projektek** lapot. Ha korábban már létrehozott projekteket, az összes többi projektet itt láthatja.
1. Kattintson **az Új projekt létrehozása gombra.**  

    ![Új projekt](./media/video-indexer-view-edit/new-project.png)
1. Adjon nevet a projektnek a ceruza ikonra kattintva. Cserélje le a "Névtelen projekt" szöveget a projekt nevére, és kattintson az ellenőrzésre.

    ![Új projekt](./media/video-indexer-view-edit/new-project3.png)
    
### <a name="add-videos-to-the-project"></a>Videók hozzáadása a projekthez

> [!NOTE]
> Jelenleg a projektek csak ugyanazon a nyelven indexelt videókat tartalmazhatnak. Ha egy nyelven kiválasztasz egy videót, nem adhatod hozzá a fiókodban más nyelvű videókat.

1. A Videók hozzáadása lehetőséget választva adja hozzá azebben a projektben dolgozni kívánt **videókat.**

    Látni fogja az összes videót a fiókjában, és egy keresőmezőt, amely azt mondja: "Szöveg, kulcsszavak vagy vizuális tartalom keresése". Olyan videók keresése, amelyeknek meghatározott személye, címkéje, márkája, kulcsszava vagy előfordulása szerepel az átiratban és az OCR-ben.
    
    Például az alábbi képen olyan videókat keresünk, amelyek a "GitHub"-ot említik.
    
    ![GitHub](./media/video-indexer-view-edit/github.png)

    Az eredmények további szűréséhez válassza **az Eredmények szűrése**lehetőséget. Szűrhetsz, ha olyan videókat jelenítesz meg, amelyekben egy bizonyos személy van, vagy megadhatja, hogy csak olyan videóeredményeket szeretnél látni, amelyek egy adott nyelven vannak, vagy egy adott tulajdonossal rendelkeznek. <br/> Megadhatja a lekérdezés hatókörét is. Ha például az OCR-ben a "GitHub" kifejezésre szeretne keresni, válassza a **Vizuális szöveg lehetőséget.**

    ![Szűrés](./media/video-indexer-view-edit/visual-text.png)

    A lekérdezéshez több szűrőt is rétegezhet. / **-** A gombok segítségével szűrőket adhat hozzá/távolíthat **+** el. Az összes szűrő eltávolításához használja a **Szűrő szűrőket.**
1. Videók hozzáadásához jelölje ki őket, majd válassza **a Hozzáadás**lehetőséget.
1. Most látni fogja az összes kiválasztott videót. Ezek azok a videók, amelyekből klipeket fog kiválasztani a projekthez.

    A videók sorrendjét átrendezheti húzással, vagy a lista menü gombjának kiválasztásával, a **Le** vagy **a Fel**gombra kattintva. A lista menüből a videót is eltávolíthatja a projektből. 

    ![Átrendezése](./media/video-indexer-view-edit/rearrange.png)
    
    A **Videók hozzáadása**lehetőség kiválasztásával bármikor hozzáadhat további videókat a projekthez. Ugyanannak a videónak több előfordulását is hozzáadhatja a projekthez. Ezt akkor érdemes megtennie, ha az egyik videóklipet, majd egy másik klipet szeretne megjeleníteni, majd egy másik klipet az első videóból. 

### <a name="select-clips-to-use-in-your-project"></a>A projektben használni kívánt klipek kijelölése

Ha az egyes videók jobb oldalán lévő lefelé mutató nyílra kattintasz, időbélyegek (a videó klipjei) alapján megnyitod a videóban szereplő elemzéseket. 

1. Válassza **az Elemzési adatok megtekintése** lehetőséget, ha testre szabhatja, hogy mely elemzéseket szeretné látni, és melyeket nem szeretné látni. 

    ![Elemzések megtekintése](./media/video-indexer-view-edit/insights.png)
1. Adott klipeklekérdezések létrehozásához használja a "Keresés az átiratban, a vizuális szövegben, a személyekben és a címkékben" feliratú keresőmezőt.
1. Szűrők hozzáadásával további részleteket adhat meg a keresett jelenetekről a **Szűrő beállítások**kiválasztásával.

    ![Szűrési lehetőségek](./media/video-indexer-view-edit/filter-options.png)

    Előfordulhat például, hogy olyan klipeket szeretne látni, amelyekben a GitHub szerepel, miközben Donovan Brown a képernyőn van. Ehhez hozzá kell adnia egy "include" szűrőt, amelynek típusa "Emberek", mint típusú betekintést. Ezután be kell írnia a "Donovan Brown" kifejezést a szűrő keresőmezőjébe.
    
    ![Belefoglalás](./media/video-indexer-view-edit/include.png)
    
    Ha olyan klipeket szeretne, ahol a GitHub szerepel, miközben Donovan Brown _nincs_ a képernyőn, egyszerűen módosítsa a "include" szűrőt egy "kizárás" szűrővé a legördülő menü használatával. 

1. Clip-et adhat a projekthez a hozzáadni kívánt szegmens kiválasztásával. A klip kijelölésének megszüntetéséhez kattintson ismét a szegmensre.
    
    A videó összes szegmensének hozzáadásához kattintson a videó melletti lista menüre, és válassza **az Összes szegmens kijelölése**lehetőséget. 

    ![Az összes hozzáadása](./media/video-indexer-view-edit/add-all.png)

    A kijelölés törlése a Kijelölés törlése lehetőség kiválasztásával törölheti az összes kijelölést.

> [!TIP]
> A klipek kiválasztásakor és rendelése közben megtekintheti a videót az oldal jobb oldalán lévő lejátszóban. 

![Előzetes verzió](./media/video-indexer-view-edit/preview.png)

Ne felejtse el menteni a projektet, amikor módosításokat hajt végre a **Projekt mentése**lehetőség kiválasztásával. 

### <a name="render-and-download-the-project"></a>A projekt renderelése és letöltése

> [!NOTE]
> A Video Indexer fizetős fiókok esetében a projekt kódolási költségeinek megjelenítése. A Video Indexer próbafiókjai legfeljebb 5 órányi renderelésre jogosultak.

1. Miután elkészült, győződjön meg arról, hogy a projekt mentve lett. Most már renderelheti ezt a projektet. Válassza a **Render and Download**lehetőséget. 

    ![Mentés](./media/video-indexer-view-edit/save.png)

    Lesz egy felugró ablak, amely azt mondja, hogy a Videó indexelő egy fájlt jelenít meg, majd a letöltési linket elküldi az e-mailbe. Válassza a Folytatás lehetőséget. 
    
    Az oldal tetején értesítést is kap arról, hogy a projekt megjelenik. Miután megtörtént a renderelés, megjelenik egy új értesítés arról, hogy a projekt sikeresen megtörtént. Kattintson az értesítésre a projekt letöltéséhez. Ez akarat letölt a tervez -ban mp4 formátum.

    ![Renderelés kész](./media/video-indexer-view-edit/rendering-done.png)

1. A mentett projekteket a **Projektek** lapon érheti el. 

    Ha ezt a projektet választja, láthatja a projekt összes elemzési és ütemtervét. Ha a **Videószerkesztő**lehetőséget választja, folytathatja a projekt szerkesztését. A szerkesztések közé tartozik a videók és klipek hozzáadása vagy eltávolítása, illetve a projekt átnevezése.

    ![Videószerkesztő](./media/video-indexer-view-edit/video-editor.png)
     
## <a name="create-a-project-from-your-video"></a>Projekt létrehozása a videóból

Új projektet közvetlenül a fiókjában lévő videóból hozhat létre. 

1. Nyissa meg a Video Indexer webhely **Könyvtár** lapját.
1. Nyissa meg a projekt létrehozásához használni kívánt videót. Az elemzési és idővonal lapon válassza a **Videószerkesztő** gombot.

    Ezzel ugyanarra az oldalra kerül, amelyen egy új projekt et hoztál létre. Az új projekttől eltérően a videó időbélyeggel ellátott elemzési szegmenseit láthatja, amelyeket korábban már elkezdtél szerkeszteni.

## <a name="see-also"></a>Lásd még

[A Video Indexer áttekintése](video-indexer-overview.md)


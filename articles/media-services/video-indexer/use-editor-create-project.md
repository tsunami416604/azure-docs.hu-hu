---
title: Projektek létrehozása a Video Indexer Editor használatával
titleSuffix: Azure Media Services
description: Ez a témakör bemutatja, hogyan hozhat létre projekteket a Video Indexer Editor használatával.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/02/2019
ms.author: juliako
ms.openlocfilehash: 9f16ab34dc9b37806f9c58b22a3f02afe839632e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "73839170"
---
# <a name="use-the-video-indexer-editor-to-create-projects"></a>Projektek létrehozása a Video Indexer Editor használatával

Video Indexer webhely lehetővé teszi, hogy a videók mélyreható megállapításait használja: keresse meg a megfelelő médiatartalom tartalmát, keresse meg a kívánt részeket, és használja az eredményeket egy teljesen új projekt létrehozásához. A létrehozást követően a projekt megjeleníthető és letölthető Video Indexer, és használható a saját szerkesztési alkalmazásaiban vagy az alárendelt munkafolyamatokban.

Bizonyos esetekben hasznos lehet a funkció: 

* Movie Highlights for Trailers létrehozása
* A videók régi videoklipek használata a hírekben.
* Rövidebb tartalom létrehozása a közösségi médiához.

Ez a cikk bemutatja, hogyan hozhat létre egy projektet a semmiből, és hogyan hozhat létre egy projektet a fiókjában.

## <a name="create-new-project-and-manage-videos"></a>Új projekt létrehozása és videók kezelése

1. Nyissa meg a [Video Indexer](https://www.videoindexer.ai/) webhelyét, és jelentkezzen be.
1. Válassza a **projektek** fület. Ha korábban már létrehozott projekteket, az összes többi projektet itt fogja látni.
1. Kattintson az **új projekt létrehozása**lehetőségre.  

    ![Új projekt](./media/video-indexer-view-edit/new-project.png)
1. A ceruza ikonra kattintva adjon nevet a projektnek. Cserélje le a "névtelen projekt" nevű szöveget a projekt nevére, és kattintson az ellenőrzési lehetőségre.

    ![Új projekt](./media/video-indexer-view-edit/new-project3.png)
    
### <a name="add-videos-to-the-project"></a>Videók hozzáadása a projekthez

> [!NOTE]
> A projektek jelenleg csak ugyanabban a nyelven indexelt videókat tartalmazhatnak. Miután kiválasztott egy videót egy adott nyelven, nem adhat hozzá olyan videókat a fiókjában, amelyek más nyelven vannak.

1. A **videók hozzáadása**lehetőség kiválasztásával adhat hozzá a projektben használni kívánt videókat.

    Ekkor megjelenik a fiókjában található összes videó, valamint egy keresőmező, amely a "szöveg keresése, kulcsszavak vagy vizuális tartalom keresése" kifejezést tartalmazza. Olyan videók kereséséhez, amelyekben megadott személy, címke, márka, kulcsszó vagy előfordulás szerepel az átiratban és az OCR-ben.
    
    Az alábbi képen például a "GitHub" kifejezést tartalmazó videókat keresünk.
    
    ![GitHub](./media/video-indexer-view-edit/github.png)

    Az eredmények **szűrésével**további szűrést végezhet. Szűrheti, hogy megjelenjenek-e bizonyos személyekkel rendelkező videók, vagy megadhatja, hogy csak az adott nyelven vagy egy adott tulajdonossal rendelkező videó-eredményeket szeretné megjeleníteni. <br/> Megadhatja a lekérdezés hatókörét is. Ha például a "GitHub" kifejezést szeretné használni az OCR-ben, válassza a **vizuális szöveg**lehetőséget.

    ![Szűrés](./media/video-indexer-view-edit/visual-text.png)

    Több szűrőt is megadhat a lekérdezéshez. Szűrők hozzáadásához és eltávolításához használja a **+** / **-** gombokat. Az összes szűrő eltávolításához használja a **Szűrők törlése** lehetőséget.
1. Videók hozzáadásához jelölje ki őket, majd válassza a **Hozzáadás**lehetőséget.
1. Most megjelenik az összes kiválasztott videó. Ezek a videók, amelyekről a projekthez tartozó klipeket fogja kiválasztani.

    Átrendezheti a videók sorrendjét húzással vagy eldobással, vagy kiválaszthatja a lista menü gombot, majd a lejjebb vagy a **mozgatás** **lehetőséget választva.** A lista menüből a projektből is el tudja távolítani a videót. 

    ![Átrendezheti](./media/video-indexer-view-edit/rearrange.png)
    
    Bármikor hozzáadhat további videókat a projekthez a **videók hozzáadása**lehetőség kiválasztásával. A projekthez ugyanazt a videót több előfordulás is hozzáadhatja. Erre akkor lehet szükség, ha egy videóból egy klipet szeretne megjeleníteni, majd egy másikat, majd egy másikat az első videóból. 

### <a name="select-clips-to-use-in-your-project"></a>Válassza ki a projektben használni kívánt klipeket

Ha az egyes videók jobb oldalán lévő lefelé mutató nyílra kattint, akkor a videóban az időbélyegek (a videó klipei) alapján megnyithatja a bepillantást. 

1. Az információk **megtekintése** elemre kattintva testreszabhatja, hogy mely információt szeretné megtekinteni, és melyeket nem szeretne látni. 

    ![Elemzések megtekintése](./media/video-indexer-view-edit/insights.png)
1. Adott klipekhez tartozó lekérdezések létrehozásához használja a "keresés átiratban, vizuális szöveg, emberek és címkék" kifejezést.
1. Szűrők hozzáadásával további részleteket adhat meg a keresett jelenetek közül a **szűrési beállítások lehetőség**kiválasztásával.

    ![Szűrési lehetőségek](./media/video-indexer-view-edit/filter-options.png)

    Előfordulhat például, hogy meg szeretné jeleníteni a GitHubon lévő klipeket, míg a Donovan Brown a képernyőn látható. Ehhez hozzá kell adnia egy "include" szűrőt, amelynek a típusa "People". Ezután be kell írnia a "Donovan Brown" kifejezést a szűrő keresőmezőbe.
    
    ![Belefoglalás](./media/video-indexer-view-edit/include.png)
    
    Ha azt szeretné, hogy a GitHubon megjelenő, a Donovan Brown _nem_ a képernyőn lévő klipeket használja, egyszerűen módosítsa a "Belefoglalás" szűrőt egy "kizárás" szűrőre a legördülő listából. 

1. A hozzáadni kívánt szegmens kiválasztásával hozzáadhat egy klipet a projekthez. A klip kijelölését a szegmensre kattintva törölheti.
    
    A videó összes szegmensének hozzáadásához kattintson a videó melletti lista menüre, és válassza a **minden szegmens kijelölése**lehetőséget. 

    ![Az összes hozzáadása](./media/video-indexer-view-edit/add-all.png)

    A kijelölés törlése lehetőség kiválasztásával törölheti az összes kijelölést.

> [!TIP]
> A klipek kiválasztása és sorrendbe állítása közben megtekintheti a videót az oldal jobb oldalán lévő lejátszóban. 

![Előzetes verzió](./media/video-indexer-view-edit/preview.png)

Ne felejtse el menteni a projektet, amikor módosításokat végez a **projekt mentése**gombra kattintva. 

### <a name="render-and-download-the-project"></a>A projekt renderelése és letöltése

> [!NOTE]
> Video Indexer fizetős fiókok esetében a projekt kódolásának költségei. Video Indexer próbaverziós fiókok esetében 5 órányi renderelésre van korlátozva.

1. Ha elkészült, győződjön meg arról, hogy a projekt el lett mentve. Most már elvégezheti a projekt megjelenítését. Válassza **a renderelés és letöltés**lehetőséget. 

    ![Mentés](./media/video-indexer-view-edit/save.png)

    Megjelenik egy előugró ablak, amely arról tájékoztatja, hogy a video Indexer egy fájlt fog megjeleníteni, majd a letöltési hivatkozást elküldi az e-mail-címre. Válassza a Folytatás lehetőséget. 
    
    Megjelenik egy értesítés is arról, hogy a projekt az oldal tetején jelenik meg. Miután megtörtént a megjelenítés, egy új értesítés jelenik meg, amely szerint a projekt sikeresen meg lett jelenítve. A projekt letöltéséhez kattintson az értesítésre. MP4 formátumban tölti le a projektet.

    ![Megjelenítés kész](./media/video-indexer-view-edit/rendering-done.png)

1. A mentett projektek a **projektek** lapról érhetők el. 

    Ha ezt a projektet választja, megjelenik a projekt összes bepillantást és idővonala. Ha a **videó szerkesztőt**választja, akkor folytathatja a projekt szerkesztését. A szerkesztések közé tartoznak a videók és a klipek hozzáadása vagy eltávolítása, illetve a projekt átnevezése.

    ![Videó szerkesztő](./media/video-indexer-view-edit/video-editor.png)
     
## <a name="create-a-project-from-your-video"></a>Projekt létrehozása a videóból

Létrehozhat egy új projektet közvetlenül a fiókjában található videóból. 

1. Nyissa meg a Video Indexer webhely **könyvtár** lapját.
1. Nyissa meg a projekt létrehozásához használni kívánt videót. Az áttekintések és az idővonal lapon válassza a **videó szerkesztő** gombot.

    Ezzel az oldalra kerül, amelyet egy új projekt létrehozásához használt. Az új projekttől eltérően a videó időbélyegzővel ellátott bepillantást nyerhet, amelyet korábban már elkezdett szerkeszteni.

## <a name="see-also"></a>Lásd még

[A Video Indexer áttekintése](video-indexer-overview.md)


---
title: A Video Indexer-szerkesztő segítségével hozható létre
titlesuffix: Azure Media Services
description: Ez a témakör bemutatja, hogyan hozható létre a Video Indexer-szerkesztő használatával.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 04/02/2019
ms.author: juliako
ms.openlocfilehash: a9d6396cab560a201b98497e787af4b6c7c2dabb
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58896460"
---
# <a name="use-the-video-indexer-editor-to-create-projects"></a>A Video Indexer-szerkesztő segítségével hozható létre

Video Indexer-webhely lehetővé teszi, hogy a videók részletes elemzéseket: keresse meg a megfelelő médiatartalmak, keresse meg, hogy érdekelte, és az eredményeket egy teljesen új projekt létrehozásához használja a részeket. Létrehozása után a projekt által előállított-e, és hogy a Video Indexer-től letöltött és használható a saját szerkesztési alkalmazások vagy alsóbb munkafolyamatokban.

Egyes forgatókönyvekben, ahol megtalálható ez a funkció hasznos a következők: 

* Film létrehozása pótkocsikhoz emeli ki.
* A videók régi klipek hírek típuskonverziók használata.
* Közösségi média tartozó rövidebb tartalmak létrehozását.

Ez a cikk bemutatja, hogyan hozhat létre egy projektet teljesen új, és hogyan hozzon létre egy projektet egy videót a fiókjában.

## <a name="create-new-project-and-manage-videos"></a>Új projekt létrehozása és videók kezelése

1. Nyissa meg a [Video Indexer](https://www.videoindexer.ai/) webhelyét, és jelentkezzen be.
1. Válassza ki a **projektek** fülre. Projektek előtt létrehozott, ha mind a más projektek itt megjelenik.
1. Kattintson a **új projekt létrehozása**.  

    ![Új projekt](./media/video-indexer-view-edit/new-project.png)
1. Nevezze el a projektet a ceruza ikonra kattintva. Cserélje le a szöveget, amely szerint a "Névtelen projekt" a projekt nevére, majd kattintson az ellenőrzés a.

    ![Új projekt](./media/video-indexer-view-edit/new-project3.png)
    
### <a name="add-videos-to-the-project"></a>Videók hozzáadása a projekthez

> [!NOTE]
> Projektek jelenleg csak tartalmazhat nyelvével azonos nyelven indexelt videók. Ha kiválaszt egy videót egy nyelvet, a fiókban lévő különböző nyelveken nem adhat hozzá a videókat.

1. Adja hozzá a videók kiválasztásával működik együtt a projekthez használni kívánt **hozzá videókat**.

    A fiók és a egy keresőmező, amely szerint a "Szöveget, kulcsszavakat vagy vizuális tartalmak keresése" látni fogja az összes videóját. Egy megadott személy, a címke, a márka, a kulcsszó vagy a előfordulása a szövegben és optikai Karakterfelismerés videók keresése.
    
    Például az alábbi képen, hogy keres, amelyek említik a "GitHub" videók.
    
    ![GitHub](./media/video-indexer-view-edit/github.png)

    Tovább szűrheti az eredményeket kiválasztásával **szűrheti az eredményeket**. Szűrheti őket az adott személy videók megjelenítéséhez, vagy adja meg, hogy csak a videó, hogy a rendszer az eredmények egy bizonyos nyelven vagy a konkrét tulajdonost. <br/> Emellett megadhatja a lekérdezés hatókörét. Ha azt szeretné, a "GitHub" Keresés a optikai Karakterfelismerés, jelölje be például **Visual szöveg**.

    ![Szűrés](./media/video-indexer-view-edit/visual-text.png)

    A lekérdezés több szűrőt is réteg. Használja a **+** / **-** szűrők hozzáadása/eltávolítása gombra. Használat **szűrő törlése** az összes szűrő eltávolítása.
1. Videók hozzáadása, jelölje ki azokat, majd **Hozzáadás**.
1. Most megjelenik az összes kiválasztott videókat. Ezek azok a videókat, amelyből válassza ki a projekt klipek kívánja.

    A videók sorrendjét húzással, vagy a lista menü gomb kiválasztásával átrendezheti **lejjebb** vagy **feljebb**. A lista menüből is fogjuk tudni eltávolítani a videót a projekt. 

    ![Átrendezése](./media/video-indexer-view-edit/rearrange.png)
    
    Lehetősége van további videók a projekthez hozzáadandó bármikor kiválasztásával **hozzá videókat**. Az azonos videó több előfordulása is hozzá a projekthez. Előfordulhat, hogy szeretné ezt megtenni, ha meg kíván jeleníteni egy videót és egy másik videoklip lejátszása elinduljon, és a egy másik ábra a első videóját. 

### <a name="select-clips-to-use-in-your-project"></a>Válassza ki a legjobb pillanatokból összeválogatott a projekt használatához

Ha minden videó jobb oldalán lévő lefelé mutató nyílra kattint, ekkor megnyílik az elemzéseket a videóban (a videó klipek) időbélyegek alapján. 

1. Válassza ki **elemzések megtekintése** melyik insights testreszabásához tekintse meg és melyik nem szeretné megtekinteni kívánt. 

    ![Megállapítások megtekintése](./media/video-indexer-view-edit/insights.png)
1. Az adott klipek lekérdezések létrehozásához használja a keresési mezőbe, amely szerint a "Keresés a szövegben, vizuális szöveg, személyek és címkék a".
1. Vegyen fel szűrőket annak további adja meg a részleteket a milyen kiválasztásával keresett jelenetek **szűrési lehetőségek**.

    ![Szűrési lehetőségek](./media/video-indexer-view-edit/filter-options.png)

    Például érdemes, ahol említett GitHub, a képernyőn megjelenő Donovan Brown pedig klipek megtekintéséhez. Ehhez hozzá kell egy "include" szűrőt, amely rendelkezik a "Felhasználók" insight típusaként. Szeretne írja be a "Donovan Brown" kifejezést a keresőmezőbe, a szűrő.
    
    ![Belefoglalás](./media/video-indexer-view-edit/include.png)
    
    Ha azt szeretné, ha GitHub említett Donovan Brown pedig a klipek _nem_ képernyőn lenne egyszerűen módosítsa a "include" szűrő egy "kizárása" szűrési a legördülő menüben. 

1. Klip hozzáadása a projekthez hozzáadni kívánt a szegmens kiválasztásával. Ez a klip kattintson ismét a szegmens is törli.
    
    Kattintson a lista menüelem mellett látható a videó és a kiválasztja a videó minden szegmensek hozzáadása **válassza ki az összes szegmensek**. 

    ![Összes hozzáadása](./media/video-indexer-view-edit/add-all.png)

    A kijelölt összes kijelölés törlése kiválasztásával törölheti.

> [!TIP]
> Kiválasztása, és a klipek rendezése, megtekintheti az oldal jobb oldalán a lejátszóban a videó. 

![Előzetes verzió](./media/video-indexer-view-edit/preview.png)

Ne felejtse el menteni a projekthez, ha megváltozik kiválasztásával **mentés projekt**. 

### <a name="render-and-download-the-project"></a>Jelennek meg, és töltse le a projekt

> [!NOTE]
> A Video Indexer fizetős fiókok a projekt megjelenítése rendelkezik kódolási költségek. Video Indexer próbafiókokon korlátozva, renderelési 5 óra.

1. Ha elkészült, győződjön meg arról, hogy a projekt mentése megtörtént. Ez a projekt is most jelennek meg. Válassza ki **jelennek meg, és töltse le**. 

    ![Mentés](./media/video-indexer-view-edit/save.png)

    Egy előugró ablak, amely közli, hogy a Video indexer jelenik meg a fájl lesz, és így a letöltési hivatkozás fog megjelenni a e-mailek küldése. Válassza ki a folytatáshoz. 
    
    Is megjelenik egy értesítés, hogy a projekt megjelenítésekor a lap fölött. Ha elkészült, rendereli a rendszer, látni fogja, hogy a projekt sikeres megjelenített új értesítést. Kattintson az értesítésre, töltse le a projektet. Le fogja tölteni a projekt mp4 formátumban.

    ![Renderelési kész](./media/video-indexer-view-edit/rendering-done.png)

1. A mentett projektek érheti el a **projektek** fülre. 

    Ha ez a projekt, láthatja az elemzés és a támadás idővonalának, a projekt. Ha **videó szerkesztő**, folytathatja a szerkesztéseket a projekthez. Módosítások fel videókat és a klipek eltávolítása vagy átnevezése a projekt tartalmazza.

    ![Videószerkesztő](./media/video-indexer-view-edit/video-editor.png)
     
## <a name="create-a-project-from-your-video"></a>Hozzon létre egy projektet a videót

Létrehozhat egy új projektet közvetlenül egy videót a fiókjában található. 

1. Nyissa meg a **könyvtár** a Video Indexer webhely lapjára.
1. Nyissa meg a videót, a projekt létrehozásához használni szeretne. Az insights és ütemterv lapot, válassza ki a **videó szerkesztő** gombra.

    Ekkor megjelenik egy új projekt létrehozása során használt ugyanazon az oldalon. Ellentétben az új projekt láthatja a videót, szerkesztési korábban rendelkezett indított időbélyegzővel insights szegmenseinek.

## <a name="see-also"></a>Lásd még

[A Video Indexer áttekintése](video-indexer-overview.md)


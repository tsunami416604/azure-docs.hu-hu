---
title: Videó moderálása a felülvizsgálati eszközzel – Content Moderator
titleSuffix: Azure Cognitive Services
description: A nem megfelelő tartalom kihasználása a számítógép által támogatott videók moderálásával és a felülvizsgálati eszközzel
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: pafarley
ms.openlocfilehash: 392cc06c6e0bce7ec2304da61033fc508d940bbb
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2020
ms.locfileid: "93143771"
---
# <a name="video-moderation-with-the-review-tool"></a>Videó moderálása a felülvizsgálati eszközzel

A legjobb eredmények elérése érdekében használja a Content Moderator gépi támogatású [videó-moderálási](video-moderation-api.md) és [felülvizsgálati eszközét](Review-Tool-User-Guide/human-in-the-loop.md) a felnőtt (explicit) és a zamatos (szuggesztív) tartalomhoz.

## <a name="view-videos-under-review"></a>Videók megtekintése a felülvizsgálat alatt

Az irányítópulton válassza ki bármelyik felülvizsgálati várólistát a videotartalom típusától függően. Ez elindít egy felülvizsgálatot, és megnyitja a videotartalom moderálása lapot.

> [!div class="mx-imgBorder"]
> ![Videó moderálásának részletes nézete a felülvizsgálati eszközben](./Review-Tool-User-Guide/images/video-moderation-detailed.png)

### <a name="review-count"></a>Vélemények száma

A jobb felső sarokban lévő csúszkával állíthatja be az oldalon megjeleníteni kívánt értékelések számát.

### <a name="view-type"></a>Nézet típusa

A különböző tartalmi bejegyzéseket mozaikként vagy részletes nézetben tekintheti meg. A **részletes** nézet lehetővé teszi a kulcstárolók és a kiválasztott videóval kapcsolatos egyéb információk megtekintését. 

> [!NOTE]
> A keretek rendszeres időközönként történő kihelyezése helyett a video moderációs szolgáltatás csak a lehetséges teljes (jó) kereteket azonosítja és jeleníti meg. Ez a funkció lehetővé teszi a hatékony keret-generálást a frame-szintű felnőtt és a zamatos elemzésekhez.

A **Mozaik** nézet minden videót egyetlen csempével fog megjeleníteni. A videó keretén belül a Kibontás gombra kattintva nagyíthatja a videót, és elrejtheti a többi elemet.

### <a name="content-obscuring-effects"></a>Tartalomra kitakarható hatások

Az **összes életlenítés** és **fekete és fehér** kapcsoló használatával állítsa be ezeket a tartalmat kitakaró hatásokat. Alapértelmezés szerint be vannak kapcsolva. A **csempés** nézetben az egyes videókban egyenként válthat ki hatásokat.

## <a name="check-video-details"></a>Videó részleteinek megtekintése

A **részletek** nézetben a jobb oldali ablaktábla több olyan lapot is megjelenít, amely a videó részleteit tartalmazza.

* Válassza a **Megjegyzések** fület, hogy egyéni megjegyzéseket adjon a videókhoz.
* Válassza a **átirat** fület, hogy a videó átirata a szolgáltatás automatikusan Kinyeri a &mdash; videóban található beszédek átiratát. Ha kijelöl egy szövegrészt, a videolejátszó a videó erre a részére fog ugrani.
* A metaadatok megtekintéséhez válassza a **meta-adatok** fület.
* Az **Előzmények** lapon megtekintheti a felülvizsgálat előzményeit, például a létrehozásuk és módosításuk módját.

> [!div class="mx-imgBorder"]
> ![Videó moderálása – tömeges címkék gomb](./Review-Tool-User-Guide/images/video-moderation-video-details.png)

## <a name="apply-moderation-tags"></a>Moderálási címkék alkalmazása

A videós felülvizsgálat fő feladata, hogy a moderálási címkéket a videókon vagy a videók egyes részein alkalmazza vagy távolítsa el.

### <a name="bulk-tagging"></a>Csoportos címkézés

A **tömeges címkék** eszköztár lehetővé teszi címkék hozzáadását egyszerre több kiválasztott videóhoz. Válasszon ki egy vagy több videót, majd válassza ki az alkalmazni kívánt címkéket, és kattintson a **Submit (Küldés** ) gombra. 

> [!div class="mx-imgBorder"]
> ![Videó moderálása – tömeges címkék gomb](./Review-Tool-User-Guide/images/video-moderation-bulk-tags.png)


### <a name="key-frame-tagging"></a>Kulcs keretének címkézése

A moderálási címkéket adott kulcstárolóhoz is hozzáadhatja. Válassza ki a kereteket a kulcstároló csempe paneljén, majd válassza a **kulcsképek címkék +** elemet a kívánt címkék alkalmazásához.

> [!NOTE]
> Ha a szolgáltatás nem tudta kinyerni a kulcstárolókat, a kulcstároló csempéje panel nem jeleníti meg a **rendelkezésre álló kereteket** , és a kulcstárolók kiválasztásának lehetősége szürkén jelenik meg. Ebben az esetben csak a teljes videóra vonatkozó címkéket lehet alkalmazni (a **videó címkék +** gomb használatával).

> [!div class="mx-imgBorder"]
> ![Videó moderálásának részletes nézete a felülvizsgálati eszközben](./Review-Tool-User-Guide/images/video-moderation-tagging-options.png)

## <a name="put-a-review-on-hold"></a>Ellenőrzés betartása

A videó ablaktábla alján található **tartás** gomb lehetővé teszi, hogy a rendszer visszatekintse a visszatartást, így később lekérdezheti és elvégezheti azt. Ezt olyan áttekintéshez is elvégezheti, amelyhez egy másik csapattag vagy felettes, aki jelenleg nem érhető el. 

A megtartott videókat megtekintheti a képernyő felső részén látható **tartás** gombra kattintva. A Hold panel a jobb oldalon jelenik meg. Itt kiválaszthatja a megtartott több felülvizsgálatot, vagy visszaállíthatja őket a várólistába, vagy megadhatja a lejárat idejét. Az előre konfigurált időtartam után a rendszer visszaküldi a visszatartási felülvizsgálatokat a várólistára. Válassza a **Mentés** lehetőséget, hogy az aktuálisan kijelölt lejárati idő alapján kezdődjön a számbavétel.

> [!div class="mx-imgBorder"]
> ![Videó moderálásának részletes nézete a felülvizsgálati eszközben](./Review-Tool-User-Guide/images/video-moderation-hold.png)

## <a name="submit-a-review"></a>Felülvizsgálat elküldése

A címkék alkalmazása után a videó ablaktábla alján kattintson a **Submit (Küldés** ) gombra. Ha több videót is címkézett, elküldheti azokat egyetlen felülvizsgálati vagy különálló felülvizsgálatok alapján.

## <a name="limbo-state"></a>Börtön állapota

Miután elküldte a felülvizsgálatot, a rendszer áthelyezi a videót a **börtönbe** , amelyet a képernyő felső részén található **börtön** gombra kattintva tekinthet meg. A videók egy előre konfigurált időtartamon belül maradnak a börtönben (amelyet az alján lévő menüben lehet módosítani), vagy amíg újra nem vizsgálják őket, vagy manuálisan elküldik őket.

Miután a videók lejárnak a börtönből, a felülvizsgálatok készként vannak megjelölve.

## <a name="next-steps"></a>Következő lépések

- Ismerkedjen meg a [videó moderálási](video-moderation-api.md)útmutatójának használatába.
- Ebből a témakörből megtudhatja, hogyan hozhatja ki az emberi [felülvizsgálók videós felülvizsgálatait](video-reviews-quickstart-dotnet.md) a moderált kimenetből.
- [Videó](video-transcript-reviews-quickstart-dotnet.md) -visszajelzéseket adhat hozzá a videókhoz.
- Tekintse meg a [teljes videó-moderálási megoldás](video-transcript-moderation-review-tutorial-dotnet.md)kidolgozásának részletes leírását.
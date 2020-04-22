---
title: Az Azure Media Player beállításai
description: Az Azure Media Player beágyazási kód egyszerűen egy HTML5 videó címke, így számos lehetőséget használhatja a szabványos címke attribútumok a beállítások at.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: e26215115b4c4484e5e05a2fd94a4d2c6680a4d0
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727163"
---
# <a name="options"></a>Beállítások #

## <a name="setting-options"></a>Beállítás lehetőségei ##

Az Azure Media Player beágyazási kód egyszerűen egy HTML5 videó címke, így számos lehetőséget használhatja a szabványos címke attribútumok a beállítások at.

`<video controls autoplay ...>`

Azt is megteheti, hogy az adatbeállítási attribútummal [JSON](http://json.org/example.html) formátumban biztosít beállításokat. Ez az a feladata is, hogy olyan beállításokat állítson be, amelyek nem szabványosak a videocímkéhez.

`<video data-setup='{ "controls": true, "autoplay": false }'...>`

Végül, ha nem használja az adat-setup attribútumot a lejátszó beállításának elindításához, akkor a JavaScript beállítási funkciójának második argumentumaként átadhat egy objektumot a lejátszó beállításaival.

`amp("vid1", { "controls": true, "autoplay": false });`

> [!NOTE]
> A konstruktor beállításai csak a forrás beállítása előtt vannak beállítva az első inicializáláskor.  Ha módosítani szeretné a beállításokat ugyanazon az inicializált Azure Media Player-elemen, frissítenie kell a beállításokat a forrás módosítása előtt. A JavaScript beállításait a `myPlayer.options({/*updated options*/});`használatával frissítheti. Ne feledje, hogy csak a módosított beállításokat érinti, az összes többi korábban megadott beállítás megmarad.

## <a name="individual-options"></a>Egyéni beállítások ##

> [!NOTE]
>A videocímke-attribútumok csak igazak vagy hamisak (logikai értékek), egyszerűen megkell adni a bekapcsoláshoz vagy kizáráshoz az attribútumot (egyenlőségjel nélkül). Például a vezérlők bekapcsolására: ROSSZ `<video controls="true" ...>` HELYES `<video controls ...>` A legnagyobb probléma, amellyel az emberek befutnak, hamis értékként hamis ra állítja be ezeket az értékeket (pl. controls="false"), amely valójában az ellenkezőjét teszi, és az értéket igazértékre állítja, mert az attribútum továbbra is szerepel.

### <a name="controls"></a>vezérlők ###

A vezérlők beállítás beállítja, hogy a lejátszó rendelkezik-e olyan vezérlőkkel, amelyekkel a felhasználó interakcióba léphet. Szabályozás nélkül a videó lejátszásának egyetlen módja az automatikus lejátszás attribútum vagy az API.Without controls the only way to start the video playing is with the autoplay tribute or through the API.

`<video controls ...>` vagy `{ "controls": true }`

### <a name="autoplay"></a>autoplay ###

Ha az automatikus lejátszás igaz, a videó lejátszása az oldal betöltésekor (a felhasználó beavatkozása nélkül) elindul.

> [!NOTE]
> Ezt a lehetőséget a mobileszközök, például a Windows Phone, az Apple iOS és az Android nem támogatják. A mobileszközök blokkolják az automatikus lejátszási funkciót, hogy megakadályozzák a fogyasztói havi adatcsomagok túlzott használatát (gyakran drágák). Ebben az esetben a videó elindításához felhasználói érintésre/kattintásra van szükség.

`<video autoplay ...>`Vagy`{ "autoplay": true }`

### <a name="poster"></a>Poszter ###
A poszter attribútum beállítja a videó lejátszása előtt megjelenő képet. Ez gyakran a videó vagy az egyéni címképernyő kerete. Amint a felhasználó rákattint játszani a kép elmúlik.

`<video poster="myPoster.jpg" ...>` vagy `{ "poster": "myPoster.jpg" }`

### <a name="width"></a>szélesség ###

A width attribútum beállítja a videó megjelenítési szélességét.

`<video width="640" ...>` vagy `{ "width": 640 }`

### <a name="height"></a>magasság ###

A magasságattribútum beállítja a videó megjelenítési magasságát.

`<video height="480" ...>` vagy `{ "height": 480 }`

### <a name="plugins"></a>Dugó ###

A plugins JSON határozza meg, hogy mely dugó betöltődik, hogy a példány az AMP segítségével konfigurálhatja a lehetőségeket, hogy a plugin lehet.

   `<video... data-setup='{plugins: { "contentTitle": {"name": "Azure Medi Services Overview"}}}'...>`

További információ a plugin fejlesztés és használat, lásd [írásban dugó](azure-media-player-writing-plugins.md)

### <a name="other-options"></a>egyéb lehetőségek ###

Más beállításokat is `<video>` meg lehet `data-setup` állítani a címkén a JSON paraméter használatával.
`<video ... data-setup='{"nativeControlsForTouch": false}'>`

#### <a name="nativecontrolsfortouch"></a>natívControlsForTouch ####

Ez kifejezetten hamis. Ha hamis ra állítja, lehetővé teszi, hogy az Azure Media Player felszíne azonos legyen a különböző platformokon.  Továbbá, ellentétben a név, touch továbbra is engedélyezve van.

### <a name="fluid"></a>Folyadék ###

Ha ezt a beállítást valódi videoelemre állítja be, akkor a szülőtároló teljes szélessége megtörténik, a magasság pedig úgy lesz beállítva, hogy a normál 16:9-es képaránnyal illeszkedjen a videóhoz.

`<video ... data-setup='{"fluid": true}'>`

`fluid`opció felülbírálja `width` `height` az explicit és a beállításokat. Ez a beállítás csak az `2.0.0` Azure Media Player és újabb verzióban érhető el.

### <a name="playbackspeed"></a>lejátszásSebesség ###

`playbackSpeed`opció szabályozza a lejátszássebesség-vezérlést és a felhasználó számára elérhető lejátszási sebességbeállításokat. `playbackSpeed`egy tárgyat vesz igénybe. A vezérlősáv lejátszási sebességének szabályozásához `enabled` az objektum tulajdonságát true értékre kell állítani. Példa a lejátszási sebesség engedélyezésére a korrektúrákban:

`<video ... data-setup='{"playbackSpeed": {"enabled": true}}'>`


A beállítás `playbackSpeed` egyéb tulajdonságait a [PlaybackSpeedOptions](https://docs.microsoft.com/javascript/api/azuremediaplayer/amp.player.playbackspeedoptions) objektum adja meg.

Példa a lejátszási sebesség beállításainak beállítására JavaScriptben:

```javascript
    var myPlayer = amp('vid1', {
        fluid: true,
        playbackSpeed: {
            enabled: true,
            initialSpeed: 1.0,
            speedLevels: [
                { name: "x4.0", value: 4.0 },
                { name: "x3.0", value: 3.0 },
                { name: "x2.0", value: 2.0 },
                { name: "x1.75", value: 1.75 },
                { name: "x1.5", value: 1.5 },
                { name: "x1.25", value: 1.25 },
                { name: "normal", value: 1.0 },
                { name: "x0.75", value: 0.75 },
                { name: "x0.5", value: 0.5 },
            ]
        }
    });
```

Ez a beállítás csak az Azure Media Player 2.0.0-s és újabb verziójában érhető el.

### <a name="staledatatimelimitinsec"></a>staleDataTimeLimitInSec ###

A `staleDataTimeLimitInSec` beállítás egy optimalizálás, amely lehetővé teszi annak konfigurálását, hogy hány másodpercnyi elavult adatot szeretne megtartani a mediaSource pufferekben. A beállítás alapértelmezés szerint le van tiltva.

### <a name="cea708captionssettings"></a>cea708CaptionsBeállítások ###

Az igaz beállítás lehetővé teszi, hogy élő CEA feliratozást jelenítsen meg az élő közvetítésekben és az élő archívumokban. A címke attribútum nem szükséges, ha nem tartalmazza a játékos esik vissza az alapértelmezett címkét.

```javascript
     cea708CaptionsSettings: {
                enabled: true,
                srclang: 'en',
                label: 'Live CC'
            }
```

Ez a beállítás csak az Azure Media Player 2.1.1-es és újabb verziójában érhető el.

## <a name="next-steps"></a>További lépések ##

- [Az Azure Media Player rövid útmutatója](azure-media-player-quickstart.md)
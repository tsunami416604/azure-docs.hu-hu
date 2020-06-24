---
title: Fizikai alapú renderelési anyagok beállítása a Mayában
description: A cikk azt ismerteti, hogyan állíthatók be fizikailag alapú renderelési anyagok a Mayaba, és hogyan exportálhatók a FBX formátumba
author: muxanickms
ms.author: misams
ms.date: 06/16/2020
ms.topic: tutorial
ms.openlocfilehash: 5579994b0746a2de4b0f2ca927027ac709940024
ms.sourcegitcommit: 9bfd94307c21d5a0c08fe675b566b1f67d0c642d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2020
ms.locfileid: "84977827"
---
# <a name="tutorial-setting-up-physically-based-rendering-materials-in-maya"></a>Oktatóanyag: fizikailag alapú renderelési anyagok beállítása a Maya-ban

## <a name="overview"></a>Áttekintés
Az oktatóanyag a következőket ismerteti:

> [!div class="checklist"]
>
> * a speciális világítási modellel rendelkező anyagokat a jelenetben lévő objektumokhoz rendelheti hozzá.
> * objektumok és anyagok egypéldányos kezelése.
> * egy jelenet exportálása FBX formátumba és fontos beállításokkal.

A [fizikai alapú renderelési (pbr) anyagok](../../overview/features/pbr-materials.md) a-ben `Maya` viszonylag egyszerű továbbítási feladat, amely a pbr számos módszeréhez hasonlóan más tartalom-létrehozási alkalmazásokban, például: `3DS Max` . A következő oktatóanyag egy útmutató az alapszintű PBR shader üzembe helyezéséhez és a FBX exportálásához az ARR-projektekhez. 

Az oktatóanyagban szereplő minta jelenet számos olyan objektumot tartalmaz, `Polygon Box` amelyek számos különböző anyaghoz lettek hozzárendelve – fa, fém, festett fém, műanyag és gumi. Általánosságban elmondható, hogy minden anyag a következő textúrákat tartalmazza 

* `Albedo`, az anyagok színleképezése, más néven `Diffuse` vagy`BaseColor`
* `Metalness`, amely meghatározza, hogy egy anyag fémes-e, és hogy mely részek fémesek. 
* `Roughness`, amely meghatározza, hogy a felület milyen durva vagy zökkenőmentes legyen, ami hatással van a reflexiók és a felszínek egy felületének élességére vagy homályos.
* `Normals`, amely részletes adatokat ad hozzá egy felülethez, például egy fémes felületen vagy egy faszínen lévő fatüzeléssel, és nem kell további sokszögeket hozzáadnia.
* `Ambient Occlusion`, amely lágy árnyékolás hozzáadására és az árnyékok a modellhez való kapcsolódására szolgál. Ez egy szürkeárnyalatos Térkép, amely azt jelzi, hogy a modell mely területein kap teljes világítást (fehér) vagy teljes árnyalatot (fekete). 

## <a name="prerequisites"></a>Előfeltételek
* `Autodesk Maya 2017`vagy újabb

## <a name="setting-up-materials-in-the-scene"></a>Anyagok beállítása a jelenetben
A Maya-ben a PBR-anyagok beállításának folyamata a következő:

A kezdéshez, ahogy látni fogja a minta jelenetben, számos Box objektumot hoztunk létre, amelyek mindegyike más típusú anyagokat képvisel. Vegye figyelembe, ahogy az alábbi képen is látható, hogy mindegyik objektum a saját megfelelő nevét kapta. 

> Érdemes megjegyezni, mielőtt megkezdi az Azure távoli renderelés (ARR) eszközeinek létrehozását, hogy mérőszámokat használjon, és a felfelé irányított Y tengely legyen. Ezért javasoljuk, hogy állítsa be a jelenet egységeit a Maya-ben. Emellett ajánlott, hogy az exportálás során a FBX exportálási beállításainál a mérőszámok egységei értékre legyenek beállítva. 

> [!TIP]
Célszerű a modell eszközeinek megfelelő nevet készíteni, általában az érintett rész vagy anyag típusával, mivel a nevek megkönnyítik az objektumok nagy méretű jelenetek közötti navigálást.

![Objektumok nevei](media/object-names.jpg)

Miután létrehozta/beszerezte a textúrákat – az igényeitől függően érdemes lehet egyedi textúrákat létrehozni a textúrát használó alkalmazásokhoz, például a `Quixel Suite` , `Photoshop` vagy `Substance Suite` más forrásokból származó általános csempe-textúrákat, a következőképpen alkalmazhatja a modelljét:

* A jelenet nézőpontjában válassza ki a modellt/geometriát, és kattintson rá a jobb gombbal. A megjelenő menüben kattintson a`Assign New Material`
* A beállítások területen válassza a következőt: `Assign New Material` `Maya` > `Stingray PBS` . Ez a művelet egy PBR-anyagot rendel hozzá a modellhez. 

A-ben `Maya 2020` számos különböző pbr-árnyékolás érhető el – `Maya Standard Surface` , `Arnold Standard Surface` és `Stingray PBR` . A `Maya Standard Surface Shader` még nem exportálható a on keresztül `FBX plugin 2020` , míg a `Arnold Standard Surface Shader` FBX-fájlokkal is exportálható. A legtöbb esetben ez megegyezik a-vel, és a `Maya Standard Surface Shader` `Physical Material` következőhöz hasonló: `3D Studio Max` .

**`The Stingray PBR Shader`** számos más alkalmazással kompatibilis, és a legszorosabban megfelel a követelményeinek, `ARR` és azóta is támogatott `Maya 2017` . Emellett kényelmes, hogy az ilyen típusú anyagok vizualizációja a nézetablakban hasonló, az ARR-ban később láthatóvá válik.

!["Rája" anyag](media/stingray-material.jpg)

Ha az eszközhöz hozzárendelt anyagot és a megfelelő nevet látja, most már folytathatja a különböző textúrák hozzárendelését. A következő képek részletesen ismertetik, hogy az egyes textúra-típusok hogyan illeszkednek a PBR-anyagokhoz. Az `Stingray PBR` anyag lehetővé teszi, hogy kiválassza az aktiválni kívánt attribútumokat, így a `plug in` textúra térképei előtt aktiválnia kell a megfelelő attribútumokat: 

![Anyag beállítása](media/material-setup.jpg)

> [!TIP]
Célszerű az anyagok megfelelő nevet használni, figyelembe véve a használatukat és/vagy típusát. Előfordulhat, hogy az egyedi részekben használni kívánt anyag neve az adott részhez tartozik, míg a szélesebb körben használható anyagokat a tulajdonságai vagy típusaik alapján lehet megnevezni.

A textúrákat a következőképpen rendelheti hozzá:

![Textúra beállítása](media/texture-setup.jpg)

Ha létrehozta és beállította a PBR-anyagokat, érdemes meggondolni a [egypéldányos objektumokat](../../how-tos/conversion/configure-model-conversion.md#instancing) a jelenetben. A egypéldányos hasonló objektumok, például a NUTS, a csavarok, a csavaros alátétek – lényegében minden olyan objektum, amely azonos, jelentős megtakarítást eredményezhet a fájlméret szempontjából. A főobjektum példányai rendelkezhetnek a saját méretével, elforgatásával és átalakításával, amelyeket szükség szerint helyezhetnek el a jelenetben. A Maya-ben a egypéldányos folyamata egyszerű.

* A `Edit` menüben `Duplicate Special` nyissa meg a parancsot, és nyissa meg a következőt: `Options` 
* A `Duplicate Special` beállítások között váltson a `Geometry Type` következőre: `Copy` `Instance` 
* Kattintson a következőre: `Duplicate Special`

![Egypéldányos](media/instancing.jpg)

Ez a művelet létrehozza az objektum egy példányát, amely a szülőtől és a szülő más példányaitól függetlenül elforgatható vagy méretezhető. 
>Azonban – ha egy példányon végrehajtott módosításokat az összetevő módban végez, a rendszer továbbítja az objektum összes példányára, így ha egy példányban lévő objektum-(csúcspont-), sokszög-és sokszög-elemekkel dolgozik, először győződjön meg arról, hogy az összes ilyen példányra hatással van.

A minta jelenetben minden egyes Box-objektum példánya megtörtént. Ez a művelet a jelenet FBX formátumba való exportálásakor lesz releváns.

![A jelenet áttekintése](media/scene-overview.jpg)

>A jelenet egypéldányos kapcsolatos ajánlott eljárás az, hogy a későbbiekben is létrehozza őket, mivel a példányokat tartalmazó példányok később rendkívül megnehezítik a másolást. 

## <a name="fbx-export-process"></a>FBX-exportálási folyamat

Most már beléphetünk a jelenet vagy a jelenet FBX exportálására. Általánosságban elmondható, hogy az eszközök exportálásával csak a kívánt jelenetből exportálja ezeket az objektumokat/eszközöket. Ha egy jelenetben 100 objektum van, de csak 30 közülük szeretné használni, akkor a teljes jelenet exportálására nincs pont. Tehát ha nem szeretné exportálni a teljes jelenetet, válassza ki a kijelölést, és lépjen a következőre:

* `File` > `Export Selection`az Exportálás párbeszédpanelen válassza a lent, majd a értékre `Files of Type` `FBX Export` . Ez az ablak teszi elérhetővé a FBX exportálási beállításait. Az FBX-exportálás elsődleges beállításai piros színnel jelennek meg az alábbi képen.

![FBX exportálása](media/FBX-exporting.jpg)

A követelményektől függően – előfordulhat például, hogy egy adategységet szeretne küldeni egy ügyfélnek, de nem szeretne nagy számú textúrát küldeni az objektummal, a textúrákat az exportált FBX-fájlba ágyazhatja. Ez a beállítás azt jelenti, hogy csak egy fájlt kell becsomagolni, de jelentősen növeli az adott FBX-eszköz méretét. Engedélyezheti a textúrák beágyazásának lehetőségét az `Embed Media` alább látható módon történő váltással.

> [!TIP]
> Figyelje meg, hogy ebben az esetben a fájl neve ennek a feltételnek megfelelően van elnevezve. Ez jó gyakorlat az eszközök nyomon követésének biztosításához. 

Miután befejezte a konfiguráció exportálását, kattintson a jobb alsó sarokban található kiválasztás exportálása gombra.

![Adathordozók beágyazása](media/embedding-media.jpg)

## <a name="conclusion"></a>Összegzés

Általánosságban elmondható, hogy az ilyen típusú anyagok élethűek, mivel a fény valós fizikáján alapulnak. Ez egy további, a valós világban elérhetővé válik.

## <a name="next-steps"></a>További lépések

Most már ismeri a legfontosabb funkciókat a speciális világítású anyagok beállításához a jelenetben lévő objektumoknál, és exportálja azt az ARR által támogatott FBX formátumba. A következő lépés a FBX-fájl konvertálása és az ARR-ban való megjelenítése.

> [!div class="nextstepaction"]
> [Gyors útmutató: modell átalakítása renderelésre](../../quickstarts\convert-model.md)
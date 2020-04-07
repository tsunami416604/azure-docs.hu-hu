---
title: A modellkonvertálás konfigurálása
description: Az összes modellátalakítási paraméter leírása
author: florianborn71
ms.author: flborn
ms.date: 03/06/2020
ms.topic: how-to
ms.openlocfilehash: eb287b812c477b2e472c48d7bd8f44574a398bac
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681570"
---
# <a name="configure-the-model-conversion"></a>A modellkonvertálás konfigurálása

Ez a fejezet a modellátalakítás lehetőségeit tartalmazza.

## <a name="settings-file"></a>Beállítások fájl

Ha egy `ConversionSettings.json` megnevezett fájl található a bemeneti modell melletti bemeneti tárolóban, akkor a modell átalakítási folyamatának további konfigurációját biztosítja.

A fájl tartalmának meg kell felelnie a következő json sémának:

```json
{
    "$schema" : "http://json-schema.org/schema#",
    "description" : "ARR ConversionSettings Schema",
    "type" : "object",
    "properties" :
    {
        "scaling" : { "type" : "number", "exclusiveMinimum" : 0, "default" : 1.0 },
        "recenterToOrigin" : { "type" : "boolean", "default" : false },
        "opaqueMaterialDefaultSidedness" : { "type" : "string", "enum" : [ "SingleSided", "DoubleSided" ], "default" : "DoubleSided" },
        "material-override" : { "type" : "string", "default" : "" },
        "gammaToLinearMaterial" : { "type" : "boolean", "default" : false },
        "gammaToLinearVertex" : { "type" : "boolean", "default" : false },
        "sceneGraphMode": { "type" : "string", "enum" : [ "none", "static", "dynamic" ], "default" : "dynamic" },
        "generateCollisionMesh" : { "type" : "boolean", "default" : true },
        "unlitMaterials" : { "type" : "boolean", "default" : false },
        "fbxAssumeMetallic" : { "type" : "boolean", "default" : true },
        "axis" : {
            "type" : "array",
            "items" : {
                "type" : "string",
                "enum" : ["default", "+x", "-x", "+y", "-y", "+z", "-z"]
            },
            "minItems": 3,
            "maxItems": 3
        }
    },
    "additionalProperties" : false
}
```

Egy `ConversionSettings.json` példa fájl lehet:

```json
{
    "scaling" : 0.01,
    "recenterToOrigin" : true,
    "material-override" : "box_materials_override.json"
}
```

### <a name="geometry-parameters"></a>Geometriai paraméterek

* `scaling`- Ez a paraméter egyenletesen méretezi a modellt. A skálázás segítségével növekszik vagy zsugorít egy modellt, például egy épületmodell megjelenítése egy asztallapon. Mivel a renderelési motor várakozásai szerint a hosszok méterben vannak megadva, a paraméter egy másik fontos használata akkor merül fel, amikor egy modellt különböző egységekben határoznak meg. Ha például egy modell centiméterben van definiálva, akkor a 0,01-es skálázás alkalmazása a modellt a megfelelő méretben jeleníti meg.
Egyes forrásadat-formátumok (például .fbx) egységskálázási tippet biztosítanak, amely esetben a konvertálás implicit módon a modellt mérőegységekre méretezi. A forrásformátum által biztosított implicit méretezés a méretezési paraméteren felül lesz alkalmazva.
A végső méretezési tényező a geometriai csúcsokra és a jelenetdiagram-csomópontok helyi transzformációira lesz alkalmazva. A gyökérentitás átalakításának skálázása változatlan marad.

* `recenterToOrigin`- kimondja, hogy a modellt úgy kell átalakítani, hogy a határolókerete az origó közepére legyen.
A központosítás akkor fontos, ha a forrásmodell az eredetitől távol odébb van, mivel ebben az esetben a lebegőpontos pontossági problémák renderelési hibákat okozhatnak.

* `opaqueMaterialDefaultSidedness`- A renderelő motor feltételezi, hogy az átlátszatlan anyagok kétoldalasak.
Ha nem ez a kívánt viselkedés, akkor ezt a paramétert "SingleSided" (SingleSided) beállításra kell állítani. További információt az [egyoldalas renderelés](../../overview/features/single-sided-rendering.md)című témakörben talál.

### <a name="material-overrides"></a>Anyag-felülbírálások

* `material-override`- Ez a paraméter lehetővé teszi az anyagok feldolgozását [az átalakítás során.](override-materials.md)

### <a name="color-space-parameters"></a>Színtér paraméterei

A renderelési motor arra számít, hogy a színértékek lineáris térben vannak.
Ha egy modell gammatérrel van definiálva, akkor ezeket a beállításokat igaz értékre kell állítani.

* `gammaToLinearMaterial`- Konvertálja anyag színek gamma tér lineáris tér
* `gammaToLinearVertex`- Konvertálja a csúcspont színeit a gamma térből a lineáris térbe

> [!NOTE]
> Az FBX-fájlok esetében `true` ezek a beállítások alapértelmezés szerint be vannak állítva. Az összes többi fájltípus `false`esetében az alapértelmezett érték a .

### <a name="scene-parameters"></a>Jelenet paraméterei

* `sceneGraphMode`- Meghatározza, hogy a jelenet grafikon a forrásfájlban konvertálja:
  * `dynamic`(alapértelmezett): A fájlban lévő összes objektum [entitásként](../../concepts/entities.md) van elérhető az API-ban, és egymástól függetlenül átalakítható. A csomóponthierarchia futásidőben megegyezik a forrásfájl ban lévő szerkezettel.
  * `static`: Az ÖSSZES objektum elérhető az API-ban, de egymástól függetlenül nem alakítható át.
  * `none`: A jelenetdiagram egy objektumba van összecsukva.

Minden mód különböző futásidejű teljesítménnyel rendelkezik. Módban `dynamic` a teljesítményköltség lineárisan skálázódik a diagramon lévő [entitások](../../concepts/entities.md) számával, még akkor is, ha egyetlen alkatrész táta kerül áthelyezésre. Csak akkor használható, ha az alkalmazáshoz külön-külön mozgó alkatrészek szükségesek, például "robbanásnézet" animáció esetén.

A `static` mód exportálja a teljes jelenetdiagramot, de a diagramon belüli részek a gyökérrészhez képest állandó transzformációval rendelkeznek. Az objektum gyökércsomópontja azonban továbbra is áthelyezhető, elforgatható vagy méretezhető jelentős teljesítményköltség nélkül. Ezenkívül [a térbeli lekérdezések](../../overview/features/spatial-queries.md) egyes részeket adnak vissza, és minden egyes rész [állapotfelülírással](../../overview/features/override-hierarchical-state.md)módosítható. Ebben a módban az objektumonkénti futásidejű terhelés elhanyagolható. Ideális olyan nagy jelenetekhez, ahol még mindig szükség van objektumonkénti ellenőrzésre, de nem változtat az objektumonkénti átalakításonként.

A `none` mód rendelkezik a legkisebb futásidejű terheléssel és valamivel jobb betöltési idővel. Ebben az üzemmódban nem lehetséges az egyes objektumok ellenőrzése vagy átalakítása. Használati esetek, például a fotogrammetriai modellek, amelyek nem rendelkeznek értelmes jelenet grafikon az első helyen.

> [!TIP]
> Számos alkalmazás több modellt tölt be. A használat módjától függően optimalizálnia kell az egyes modellek konverziós paramétereit. Ha például meg szeretné jeleníteni egy autó modelljét, hogy a felhasználó szétszedje és `dynamic` részletesen megvizsgálja, akkor módban kell átalakítania. Ha azonban az autót bemutatóterem-környezetbe szeretné helyezni, akkor a `sceneGraphMode` modell `static` átalakítható a beállítással vagy akár `none`.

### <a name="physics-parameters"></a>Fizikai paraméterek

* `generateCollisionMesh`- Ha egy modell [térbeli lekérdezéseinek](../../overview/features/spatial-queries.md) támogatására van szüksége, ezt a beállítást engedélyezni kell. A legrosszabb esetben az ütközési háló létrehozása megduplázhatja az átváltási időt. Az ütközési kapcsolatokkal rendelkező modellek betöltése hosszabb időt vesz igénybe, és a `dynamic` jelenetgrafikon használatakor nagyobb futásidejű teljesítményük is van. Az általános optimális teljesítmény érdekében tiltsa le ezt a beállítást minden olyan modellen, amelyen nincs szükség térbeli lekérdezésekre.

### <a name="unlit-materials"></a>Megvilágítatlan anyagok

* `unlitMaterials`- Alapértelmezés szerint az átalakítás inkább [létre PBR anyagok](../../overview/features/pbr-materials.md). Ez a beállítás arra utasítja a konvertert, hogy az összes anyagot [színes anyagként](../../overview/features/color-materials.md) kezelje. Ha olyan adatokkal rendelkezik, amelyek már tartalmaznak világítást, például a fotogrammetria segítségével létrehozott modelleket, ez a beállítás lehetővé teszi, hogy gyorsan érvényesítse a megfelelő átalakítást az összes anyagra vonatkozóan, anélkül, hogy minden egyes anyagot külön-külön [felül kellene bírálnia.](override-materials.md)

### <a name="converting-from-older-fbx-formats-with-a-phong-material-model"></a>Konvertálás régebbi FBX formátumokból, Phong anyagmodellel

* `fbxAssumeMetallic`- Az FBX formátum régebbi verziói phong anyagmodell segítségével határozzák meg anyagaikat. Az átalakítási folyamatnak arra kell következtetnie, hogy ezek az anyagok hogyan felelnek meg a renderelő [PBR modelljének](../../overview/features/pbr-materials.md). Általában ez jól működik, de kétértelműség merülhet fel, ha egy anyagnak nincs textúrája, magas specular értéke idoben és nem szürke albedo színe. Ebben az esetben, az átalakítás kell választani a rangsorban a magas specular értékek, meghatározása erősen fényvisszaverő, fémes anyag, ahol az albedo szín feloldódik, vagy rangsorolja az albedo szín, meghatározó valami, mint egy fényes színes műanyag. Alapértelmezés szerint az átalakítási folyamat azt feltételezi, hogy a nagy specular értékek fémes anyagot jelentenek olyan esetekben, amikor kétértelműség alkalmazandó. Ez a paraméter `false` beállítható úgy, hogy az ellenkező irányba váltson.

### <a name="coordinate-system-overriding"></a>A rendszer felülbírálásának koordinálása

* `axis`- Hogy felülbírálja a koordináta-rendszer egység-vektorokat. Az alapértelmezett `["+x", "+y", "+z"]`értékek a . Elméletileg az FBX formátumnak van egy fejléce, ahol ezek a vektorok definiálódnak, és a konverzió ezt az információt használja a jelenet átalakításához. A glTF formátum rögzített koordináta-rendszert is meghatároz. A gyakorlatban egyes eszközök vagy helytelen adatokkal rendelkeznek a fejlécükben, vagy más koordináta-rendszerkonvencióval lettek mentve. Ez a beállítás lehetővé teszi a koordináta-rendszer felülbírálását. Például: `"axis" : ["+x", "+z", "-y"]` kicseréli a Z tengelyt és az Y tengelyt, és megtartja a koordináta-rendszer átadási adottságát az Y tengely irányának megfordításával.

### <a name="vertex-format"></a>Csúcspont formátum

Lehetőség van a háló csúcsformátumának beállítására, a pontosság kereskedeleméhez a memória megtakarításérdekében. Az alacsonyabb memóriaigény lehetővé teszi nagyobb modellek betöltését vagy jobb teljesítmény elérését. Az adatoktól függően azonban a nem megfelelő formátum jelentősen befolyásolhatja a renderelés minőségét.

> [!CAUTION]
> A csúcspont formátumának módosítása a legvégső megoldás, ha a modellek már nem férnek bele a memóriába, vagy ha a lehető legjobb teljesítményt nyújtják. A módosítások könnyen bevezethetik a renderelési leleteket, mind a nyilvánvalóakat, mind a finomakat. Hacsak nem tudja, mire kell vigyáznia, ne módosítsa az alapértelmezett értéket.

Ezek a kiigazítások lehetségesek:

* Bizonyos adatfolyamok explicit módon szerepelhetnek vagy kizárhatók.
* Az adatfolyamok pontossága csökkenthető a memóriahely csökkentése érdekében.

A `vertex` fájl következő `.json` szakasza nem kötelező. Minden olyan részesetében, amely nincs kifejezetten megadva, az átalakítási szolgáltatás visszaáll az alapértelmezett beállításra.

```json
{
    ...
    "vertex" : {
        "position"  : "32_32_32_FLOAT",
        "color0"    : "NONE",
        "color1"    : "NONE",
        "normal"    : "NONE",
        "tangent"   : "NONE",
        "binormal"  : "NONE",
        "texcoord0" : "32_32_FLOAT",
        "texcoord1" : "NONE"
    },
    ...
```

Ha egy összetevőt `NONE`a rendszerre kényszerít, akkor garantált, hogy a kimeneti háló nem rendelkezik a megfelelő adatfolyamkal.

#### <a name="component-formats-per-vertex-stream"></a>Összetevőformátumok csúcspont-adatfolyamonként

Ezek a formátumok a megfelelő összetevőkhöz engedélyezettek:

| Csúcspont-összetevő | Támogatott formátumok (félkövér = alapértelmezett) |
|:-----------------|:------------------|
|pozíció| **32_32_32_FLOAT,** 16_16_16_16_FLOAT |
|szín0| **8_8_8_8_UNSIGNED_NORMALIZED,** NINCS |
|szín1| 8_8_8_8_UNSIGNED_NORMALIZED, **NINCS**|
|Normál| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, NINCS |
|Érintő| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, NINCS |
|binormális| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, NINCS |
|texcoord0 között| **32_32_FLOAT**, 16_16_FLOAT, NINCS |
|texcoord1| **32_32_FLOAT**, 16_16_FLOAT, NINCS |

#### <a name="supported-component-formats"></a>Támogatott összetevőformátumok

A formátumok memórialábnyomai a következők:

| Formátum | Leírás | Csúcspontonkénti bájt |
|:-------|:------------|:---------------|
|32_32_FLOAT|kétkomponensű teljes lebegőpontos pontosság|8
|16_16_FLOAT|kétkomponensű fél lebegőpontos pontosság|4
|32_32_32_FLOAT|háromkomponensű teljes lebegőpontos pontosság|12
|16_16_16_16_FLOAT|négykomponensű fél lebegőpontos pontosság|8
|8_8_8_8_UNSIGNED_NORMALIZED|négykomponensű bájt, tartományra `[0; 1]` normalizálva|4
|8_8_8_8_SIGNED_NORMALIZED|négykomponensű bájt, tartományra `[-1; 1]` normalizálva|4

#### <a name="best-practices-for-component-format-changes"></a>Gyakorlati tanácsok az összetevők formátumának módosításához

* `position`: Ritka, hogy a csökkentett pontosság elegendő. **16_16_16_16_FLOAT** észrevehető kvantálási leleteket vezet be, még a kis modellek esetében is.
* `normal`, `tangent` `binormal`, : Ezek az értékek általában együtt változnak. Hacsak nincsenek észrevehető megvilágítási leletek, amelyek a normál kvantálásból származnak, nincs ok a pontosság növelésére. Bizonyos esetekben azonban ezek az összetevők nem **állíthatók:**
  * `normal`, `tangent`és `binormal` csak akkor van szükség, ha a modellben legalább egy anyagot meg kell világítani. Az ARR-ben ez a helyzet akkor, ha a modellen bármikor [PBR anyagot](../../overview/features/pbr-materials.md) használnak.
  * `tangent`és `binormal` csak akkor van szükség, ha a megvilágított anyagok bármelyike normál térképtextúrát használ.
* `texcoord0`, `texcoord1` : A textúrakoordináták csökkentett pontosságot `[0; 1]` (**16_16_FLOAT**) használhatnak, ha értékeik a tartományban maradnak, és amikor a címzett textúrák maximális mérete 2048 x 2048 képpont. Ha ezeket a határértékeket túllépik, a textúraleképezés minősége romlik.

#### <a name="example"></a>Példa

Tegyük fel, hogy van egy fotogrammetriai modell, amely világítás sült a textúrák. A modell rendereléséhez mindössze a csúcspontok és a textúrakoordináták szükségesek.

Alapértelmezés szerint a konverternek azt kell feltételeznie, hogy a modellen a `normal` `tangent`PBR-anyagokat valamikor használni szeretné, így a , és `binormal` az adatokat hozza létre. Ennek `position` következtében a csúcspontmemória-használat (12 bájt) + `texcoord0` (8 `normal` bájt) + `tangent` (4 bájt) `binormal` + (4 bájt) + (4 bájt) = 32 bájt. Az ilyen típusú nagyobb modellek könnyen több millió csúcsot okozhatnak, ami olyan modelleket eredményez, amelyek több gigabájt memóriát is képesek igénybe venni. Az ilyen nagy mennyiségű adat hatással van a teljesítményre, és akár elfogyhat is a memória.

Tudva, hogy soha nem kell dinamikus megvilágítás a modellen, és tudva, hogy `NONE` az `texcoord0` összes textúra`16_16_FLOAT`koordináták hatótávolságon belül, `[0; 1]` beállíthatja `normal`, , `tangent`és `binormal` a fele pontosság ( ), ami csak 16 byte per csúcspont. A hálóadatok félbe vágása lehetővé teszi a nagyobb modellek betöltését és potenciálisan javítja a teljesítményt.

## <a name="typical-use-cases"></a>Tipikus használati esetek

Egy adott használati eset hez jó importálási beállítások keresése fárasztó folyamat lehet. Másrészt a konvertálási beállítások jelentős hatással lehetnek a futásidejű teljesítményre.

Vannak bizonyos használati esetek, amelyek megfelelnek a konkrét optimalizálás. Néhány példa az alábbiakban található.

### <a name="use-case-architectural-visualization--large-outdoor-maps"></a>Használati eset: Építészeti képi megjelenítés / nagy szabadtéri térképek

* Az ilyen típusú jelenetek általában statikus, ami azt jelenti, hogy nem kell mozgatható alkatrészeket. Ennek megfelelően `sceneGraphMode` a `static` beállítható, `none`vagy akár , ami javítja a futásidejű teljesítményt. Mód `static` esetén a jelenet gyökércsomópontja továbbra is mozgatható, elforgatható és méretezhető, például az 1:1 arányú skála (első személyű nézetesetén) és az asztallap nézet közötti dinamikus váltáshoz.

* Ha alkatrészeket kell mozgatnia, ez általában azt is jelenti, hogy szüksége van a sugárképek vagy más [térbeli lekérdezések támogatására,](../../overview/features/spatial-queries.md)hogy ezeket az alkatrészeket elsősorban kiválaszthassa. Másrészt, ha nem kíván mozogni valamit, nagy az esélye, hogy ön is nem kell, hogy részt vegyenek a térbeli lekérdezések, és ezért kapcsolja ki a `generateCollisionMesh` zászlót. Ez a kapcsoló jelentős hatással van a konverziós időkre, a betöltési időkre és a futásidejű per frame frissítési költségekre.

* Ha az alkalmazás nem használ `opaqueMaterialDefaultSidedness` [kivágott síkokat,](../../overview/features/cut-planes.md)a jelzőt ki kell kapcsolni. A teljesítménynövekedés általában 20%-30%. Cut síkok továbbra is használható, de nem lesz back-arcok, amikor belenéz a belső része kontró, amely úgy néz ki, ellentétes intuitív. További információt az [egyoldalas renderelés](../../overview/features/single-sided-rendering.md)című témakörben talál.

### <a name="use-case-photogrammetry-models"></a>Használati eset: Fotogrammetriai modellek

A fotogrammetriai modellek renderelésekén általában nincs szükség jelenetdiagramra, így a `sceneGraphMode` beállítás a . `none` Mivel ezek a modellek ritkán tartalmaznak összetett jelenet grafikon kezdeni, a hatása ennek a lehetőségnek jelentéktelennek kell lennie, mégis.

Mivel a világítás már sült a textúrák, nincs dinamikus világítás szükséges. Ezért:

* Állítsa `unlitMaterials` be `true` a zászlót, hogy kapcsolja be az összes anyag ot világít [színes anyagok](../../overview/features/color-materials.md).
* Távolítsa el a szükségtelen adatokat a csúcspont formátumból. Lásd a fenti [példát.](#example)

### <a name="use-case-visualization-of-compact-machines-etc"></a>Használati eset: Kompakt gépek megjelenítése stb.

Ezekben a használati esetekben a modellek gyakran nagyon nagy részletességgel egy kis mennyiségben. A renderelő erősen optimalizált kezelni az ilyen eseteket is. Az előző használati esetben említett optimalizálások többsége azonban itt nem vonatkozik:

* Az egyes részeknek választhatónak és `sceneGraphMode` mozgathatónak `dynamic`kell lenniük, ezért a részeket a számára kell hagyni.
* A sugáröntvények általában az alkalmazás szerves részét képezik, ezért ütközési tűnéseket kell létrehozni.
* A kivágott síkok jobban néznek ki, ha a `opaqueMaterialDefaultSidedness` jelző engedélyezve van.

## <a name="next-steps"></a>További lépések

* [Modell átalakítása](model-conversion.md)
* [Színes anyagok](../../overview/features/color-materials.md)
* [PBR anyagok](../../overview/features/pbr-materials.md)
* [Anyagok felülbírálása a modell átalakítása során](override-materials.md)

---
title: Anyagok felülbírálása a modell átalakítása során
description: A konverziós időpontban az anyagfelülbírálási munkafolyamat magyarázata
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: how-to
ms.openlocfilehash: 90653db4c572877a728964851a99beebf2e823a4
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681479"
---
# <a name="override-materials-during-model-conversion"></a>Anyagok felülbírálása a modell átalakítása során

Az átalakítás során a forrásmodell anyagbeállításai taszították a renderelő által használt [PBR-anyagok](../../overview/features/pbr-materials.md) meghatározására.
Néha az [alapértelmezett konverzió](../../reference/material-mapping.md) nem adja meg a kívánt eredményt, és módosításokat kell végrehajtania.
Amikor egy modellt konvertálnak az Azure távoli renderelésben való használatra, anyag-felülbírálási fájlt biztosíthat az anyagkonverzió anyagonkénti elvégzésének testreszabásához.
A [modellkonvertálás konfigurálásának](configure-model-conversion.md) szakasza utasításokat tartalmaz az anyag felülbírálási fájlnevének deklarálásához.

## <a name="the-override-file-used-during-conversion"></a>Az átalakítás során használt felülíró fájl

Egyszerű példaként tegyük fel, hogy egy dobozmodell egyetlen anyaggal rendelkezik, az "Alapértelmezett". Az albedo színét módosítani kell az ARR-ben való használatra.
Ebben az esetben `box_materials_override.json` a fájl a következőképpen hozható létre:

```json
[
    {
        "name": "Default",
        "albedoColor": {
            "r": 0.33,
            "g": 0.33,
            "b": 0.33,
            "a": 1.0
        }
    }
]
```

A `box_materials_override.json` fájl a bemeneti tárolóba `ConversionSettings.json` kerül, `box.fbx`és egy hozzáadódik a , amely megmondja az átalakításnak, hogy hol keresse a felülíró fájlt (lásd [A modellkonvertálás konfigurálása):](configure-model-conversion.md)

```json
{
    "material-override" : "box_materials_override.json"
}
```

A modell konvertálásakor az új beállítások lépnek érvénybe.

### <a name="color-materials"></a>Színes anyagok

A [színanyag-modell](../../overview/features/color-materials.md) egy folyamatosan árnyékolt felületet ír le, amely független a megvilágítástól.
Ez például a fotogrammetriai algoritmusok által készített eszközök esetében hasznos.
Az anyag felülbírálási fájlokban az anyag színanyagnak `unlit` deklarálható a `true`beállítással.

```json
[
    {
        "name": "Photogrametry_mat1",
        "unlit" : true
    },
    {
        "name": "Photogrametry_mat2",
        "unlit" : true
    }
]
```

### <a name="ignore-specific-texture-maps"></a>Adott textúratérképek figyelmen kívül hagyása

Előfordulhat, hogy azt szeretné, hogy az átalakítási folyamat figyelmen kívül hagyja az egyes textúratérképeket. Ez akkor fordulhat elő, ha a modellt olyan eszköz hozta létre, amely a renderelő által nem megfelelően értelmezett speciális térképeket hoz létre. Például egy "Opacitástérkép", amely az opacitáson kívül más meghatározására szolgál, vagy egy modell, ahol a "NormalMap" "BumpMap" néven van tárolva. (Az utóbbi esetben figyelmen kívül szeretné hagyni a "NormalMap", ami miatt a konverter használni "BumpMap" a "NormalMap".)

Az elv egyszerű. Csak adjon hozzá `ignoreTextureMaps` egy nevű tulajdonságot, és adjon hozzá minden olyan textúratérképet, amelyet figyelmen kívül szeretne hagyni:

```json
[
    {
        "name": "Default",
        "ignoreTextureMaps": ["OpacityMap", "NormalMap"]
    }
]
```

A textúratérképek teljes listáját figyelmen kívül hagyhatja, lásd az alábbi JSON-sémát.

## <a name="json-schema"></a>JSON-séma

Az anyagfájlok teljes JSON-sémáját itt adják meg. A rendelkezésre `unlit` `ignoreTextureMaps`álló tulajdonságok kivételével a [színanyag](../../overview/features/color-materials.md) és a [PBR-anyagmodellek](../../overview/features/pbr-materials.md) szakaszaiban leírt tulajdonságok egy részhalmaza.

```json
{
    "definitions" :
    {
        "color":
        {
            "type" : "object",
            "description" : "Color as 4 components vector",
            "properties":
            {
                "r": {"type":"number"},
                "g": {"type":"number"},
                "b": {"type":"number"},
                "a": {"type":"number"}
            },
            "required": ["r", "g", "b"]
        },
        "alpha":
        {
            "type" : "object",
            "description" : "Alpha channel for color",
            "properties":
            {
                "a": {"type":"number"}
            },
            "required": ["a"]
        },
        "colorOrAlpha":
        {
            "anyOf": [
                {"$ref": "#/definitions/color"},
                {"$ref": "#/definitions/alpha"}
            ]
        },
        "listOfMaps":
        {
            "type": "array",
            "items": {
                "type": "string",
                "enum": ["AlbedoMap",
                            "EmissiveMap",
                            "NormalMap",
                            "OcclusionMap",
                            "RoughnessMap",
                            "MetalnessMap",
                            "ReflectivityMap",
                            "BumpMap",
                            "OpacityMap",
                            "DiffuseMap",
                            "SpecularMap",
                            "ShininessMap",
                            "MetallicRoughnessMap",
                            "SpecularGlossinessMap"]
            }
        }
    },
    "type" : "array",
    "description" : "List of materials to override",
    "items":
    {
        "type" : "object",
        "description" : "List of parameters to override",
        "properties":
        {
            "name": { "type" : "string"},
            "unlit": { "type" : "boolean" },
            "albedoColor": { "$ref": "#/definitions/colorOrAlpha" },
            "roughness": { "type": "number" },
            "metalness": { "type": "number" },
            "transparent": { "type" : "boolean" },
            "alphaClipEnabled": { "type" : "boolean" },
            "alphaClipThreshold": { "type": "number" },
            "useVertexColor": { "type" : "boolean" },
            "isDoubleSided": { "type" : "boolean" },
            "ignoreTextureMaps": { "$ref" : "#/definitions/listOfMaps" }
        },
        "required": ["name"],
        "additionalProperties" : false
    }
}
```

## <a name="next-steps"></a>További lépések

* [Színes anyagok](../../overview/features/color-materials.md)
* [PBR anyagok](../../overview/features/pbr-materials.md)
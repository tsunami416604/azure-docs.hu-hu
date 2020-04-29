---
title: Anyagok felülírása a modellátalakítás során
description: A munkafolyamatot átváltási időben mutatja be
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: how-to
ms.openlocfilehash: 90653db4c572877a728964851a99beebf2e823a4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681479"
---
# <a name="override-materials-during-model-conversion"></a>Anyagok felülírása a modellátalakítás során

Az átalakítás során a rendszer a forrás modell anyag-beállításait használja a megjelenítő által használt [pbr-anyagok](../../overview/features/pbr-materials.md) definiálásához.
Előfordulhat, hogy az [alapértelmezett konverzió](../../reference/material-mapping.md) nem adja meg a kívánt eredményeket, és módosítania kell a módosításokat.
Ha egy modellt az Azure Remote rendering szolgáltatásban való használatra konvertál, megadhat egy anyag-felülbírálási fájlt, amellyel testreszabhatja, hogy a rendszer milyen módon végezze el az anyag-átalakítást.
A [modell átalakításának konfigurálásáról](configure-model-conversion.md) szóló szakasz útmutatást nyújt az anyag felülbírálási fájlnevének deklarálása során.

## <a name="the-override-file-used-during-conversion"></a>Az átalakítás során használt felülbírálási fájl

Egyszerű Példaként tegyük fel, hogy egy box-modell egyetlen, "default" nevű anyagot tartalmaz. A albedó színét az ARR-ben való használatra kell beállítani.
Ebben az esetben a következő `box_materials_override.json` módon hozhat létre egy fájlt:

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

A `box_materials_override.json` fájlt a rendszer a bemeneti tárolóba helyezi, `ConversionSettings.json` a pedig a `box.fbx`mellé kerül, amely megadja, hogy hol található a felülbírálási fájl (lásd: [a modell átalakításának konfigurálása](configure-model-conversion.md)):

```json
{
    "material-override" : "box_materials_override.json"
}
```

A modell átalakításakor az új beállítások lesznek érvényesek.

### <a name="color-materials"></a>Színes anyagok

A [színanyag](../../overview/features/color-materials.md) modell egy állandóan árnyékolt felületet ír le, amely független a megvilágítástől.
Ez a photogrammetry algoritmusok által készített eszközök esetében hasznos, például:.
Az anyag-felülbírálási fájlokban egy anyag beállítható úgy, hogy a következőre `unlit` van `true`beállítva:.

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

### <a name="ignore-specific-texture-maps"></a>Adott textúratérkép figyelmen kívül hagyása

Esetenként előfordulhat, hogy az átalakítási folyamat figyelmen kívül hagyja az adott textúra-térképeket. Ez akkor fordulhat elő, ha a modellt olyan eszköz hozta létre, amely speciális térképeket hoz létre, amelyeket a megjelenítő nem megfelelően értelmezett. Például egy "OpacityMap", amely nem az opacitást definiálja, vagy egy olyan modellt, amelyben a "NormalMap" a "BumpMap" néven van tárolva. (Az utóbbi esetben figyelmen kívül hagyja a "NormalMap" kifejezést, ami azt eredményezi, hogy a konverter "BumpMap"-t használ "NormalMap"-ként.)

Az elv egyszerű. Csak vegyen fel egy `ignoreTextureMaps` nevű tulajdonságot, és adja hozzá a figyelmen kívül hagyni kívánt textúrát:

```json
[
    {
        "name": "Default",
        "ignoreTextureMaps": ["OpacityMap", "NormalMap"]
    }
]
```

Az figyelmen kívül hagyható textúratérkép teljes listájáért tekintse meg az alábbi JSON-sémát.

## <a name="json-schema"></a>JSON-séma

Az anyagokhoz tartozó fájlok teljes JSON-sémája itt van megadva. A `unlit` és a esetében a `ignoreTextureMaps`(z) és a (z) és a (z) a (z) [és a (](../../overview/features/color-materials.md) z) a (z) és a (z [) és a](../../overview/features/pbr-materials.md) (z)

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
* [PBR-anyagok](../../overview/features/pbr-materials.md)
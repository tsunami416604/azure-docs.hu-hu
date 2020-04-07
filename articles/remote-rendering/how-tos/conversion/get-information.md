---
title: Információ a konvertált modellről
description: Az összes modellátalakítási paraméter leírása
author: malcolmtyrrell
ms.author: matyrr
ms.date: 03/05/2020
ms.topic: how-to
ms.openlocfilehash: d5f843add0649682bae8c472bc50b6beea33bf93
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681518"
---
# <a name="get-information-about-a-converted-model"></a>Információ a konvertált modellről

A konverziós szolgáltatás által létrehozott arrAsset fájl kizárólag a renderelési szolgáltatás általi felhasználásra szolgál. Előfordulhatnak azonban olyan esetek, amikor a modellel kapcsolatos információkat renderelési munkamenet indítása nélkül szeretné elérni. Ezért a konverziós szolgáltatás egy JSON-fájlt helyez el az arrAsset fájl mellett a kimeneti tárolóban. Ha például egy `buggy.gltf` fájlt konvertálnak, a kimeneti `buggy.info.json` tároló a `buggy.arrAsset`konvertált eszköz mellett megnevezett fájlt fog tartalmazni. Információkat tartalmaz a forrásmodellről, a konvertált modellről és magáról az átalakításról.

## <a name="example-info-file"></a>Példa *információs* fájl

Íme egy példa *info* fájl által létrehozott `buggy.gltf`konvertáló nevű fájl:

```JSON
{
    "files": {
        "input": "Buggy.gltf"
    },
    "conversionSettings": {
        "recenterToOrigin": true
    },
    "inputInfo": {
        "sourceAssetExtension": ".gltf",
        "sourceAssetFormat": "glTF2 Importer",
        "sourceAssetFormatVersion": "2.0",
        "sourceAssetGenerator": "COLLADA2GLTF"
    },
    "inputStatistics": {
        "numMeshes": 148,
        "numFaces": 308306,
        "numVertices": 245673,
        "numMaterial": 149,
        "numFacesSmallestMesh": 2,
        "numFacesBiggestMesh": 8764,
        "numNodes": 206,
        "numMeshUsagesInScene": 236,
        "maxNodeDepth": 3
    },
    "outputInfo": {
        "conversionToolVersion": "3b28d840de9916f9d628342f474d38c3ab949590",
        "conversionHash": "CCDB1F7A4C09F565"
    },
    "outputStatistics": {
        "numMeshPartsCreated": 236,
        "numMeshPartsInstanced": 88,
        "recenteringOffset": [
            -24.1,
            -50.9,
            -16.5974
        ],
        "boundingBox": {
            "min": [
                -43.52,
                -61.775,
                -79.6416
            ],
            "max": [
                43.52,
                61.775,
                79.6416
            ]
        }
    }
}
```

## <a name="information-in-the-info-file"></a>Információ az információs fájlban

### <a name="the-files-section"></a>A *fájlok* szakasz

Ez a szakasz a megadott fájlneveket tartalmazza.

* `input`: A forrásfájl neve.
* `output`: A kimeneti fájl neve, ha a felhasználó nem alapértelmezett nevet adott meg.

### <a name="the-conversionsettings-section"></a>A *conversionSettings* szakasz

Ez a szakasz a modell konvertálásakor megadott [ConversionSettings](configure-model-conversion.md#settings-file) egy példányát tartalmazza.

### <a name="the-inputinfo-section"></a>A *inputInfo* szakasz

Ez a szakasz a forrásfájl formátumával kapcsolatos információkat rögzíti.

* `sourceAssetExtension`: A forrásfájl kiterjesztése.
* `sourceAssetFormat`: A forrásfájl formátumának leírása.
* `sourceAssetFormatVersion`: A forrásfájl formátumának verziója.
* `sourceAssetGenerator`: A forrásfájlt létrehozó eszköz neve, ha rendelkezésre áll.

### <a name="the-inputstatistics-section"></a>A *inputStatistics* szakasz

Ez a szakasz a forrásjelenettel kapcsolatos információkat tartalmazza. Gyakran lesznek eltérések az ebben a szakaszban szereplő értékek és a forrásmodellt létrehozó eszköz egyenértékű értékei között. Ilyen különbségek várhatók, mert a modell az exportálási és átalakítási lépések során módosul.

* `numMeshes`: A hálórészek száma, ahol minden alkatrész egyetlen anyagra hivatkozhat.
* `numFaces`: A _háromszögek_ teljes száma a teljes modellben. Vegye figyelembe, hogy a háló háromszögelése az átalakítás során történik.
* `numVertices`: A csúcspontok teljes száma az egész modellben.
* `numMaterial`: Az anyagok teljes száma az egész modellben.
* `numFacesSmallestMesh`: A modell legkisebb hálójában lévő háromszögek száma.
* `numFacesBiggestMesh`: A modell legnagyobb hálójában lévő háromszögek száma.
* `numNodes`: A modell jelenetgrafikonjában lévő csomópontok száma.
* `numMeshUsagesInScene`: A csomópontok által a véletlenek hivatkozásának száma. Egynél több csomópont hivatkozhat ugyanarra a hálóra.
* `maxNodeDepth`: A csomópont maximális mélysége a jelenetdiagramon belül.

### <a name="the-outputinfo-section"></a>A *outputInfo* szakasz

Ez a szakasz a létrehozott kimenetáltalános adatait rögzíti.

* `conversionToolVersion`: A modellkonverter verziója.
* `conversionHash`: Az arrAsset-en belüli adatok kivonata, amely hozzájárulhat a rendereléshez. Annak megértésére használható, hogy a konverziós szolgáltatás más eredményt hozott-e, amikor ugyanazon a fájlon fut.

### <a name="the-outputstatistics-section"></a>A *kimeneti statisztika* szakasz

Ez a szakasz az átalakított eszközből számított adatokat rögzíti.

* `numMeshPartsCreated`: Az arrAsset ben lévő buborékok száma. Ez eltérhet `numMeshes` `inputStatistics` a szakaszban, mert instancing befolyásolja az átalakítási folyamat.
* `numMeshPartsInstanced`: Az arrAsset-ben újrafelhasznált buborékok száma.
* `recenteringOffset`: Ha `recenterToOrigin` a [ConversionSettings](configure-model-conversion.md) beállítás engedélyezve van, ez az érték az a fordítás, amely visszahelyezné a konvertált modellt az eredeti helyére.
* `boundingBox`: A modell határait.

## <a name="next-steps"></a>További lépések

* [Modell átalakítása](model-conversion.md)
* [A modellkonvertálás konfigurálása](configure-model-conversion.md)

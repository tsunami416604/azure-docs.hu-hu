---
title: A konvertált modell adatainak lekérése
description: Az összes modell-átalakítási paraméter leírása
author: malcolmtyrrell
ms.author: matyrr
ms.date: 03/05/2020
ms.topic: how-to
ms.openlocfilehash: f5c38ac88503416b37b720a091c9e46d819a3146
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88509297"
---
# <a name="get-information-about-a-converted-model"></a>A konvertált modell adatainak lekérése

Az átalakítási szolgáltatás által létrehozott arrAsset-fájl kizárólag a renderelési szolgáltatás általi felhasználásra szolgál. Előfordulhat azonban, hogy a modellre vonatkozó információkat a renderelési munkamenet elindítása nélkül szeretné elérni. Ezért az átalakítási szolgáltatás egy JSON-fájlt helyez el a kimeneti tároló arrAsset fájlja mellett. Ha például egy fájl `buggy.gltf` konvertálása történik, a kimeneti tároló tartalmazni fog egy nevű fájlt `buggy.info.json` a konvertált eszköz mellett `buggy.arrAsset` . A forrás modellről, a konvertált modellről, valamint magáról a konverzióról tartalmaz információkat.

## <a name="example-info-file"></a>Példa *információs* fájlra

Íme egy példa a fájl konvertálásával létrehozott *információs* fájlra `buggy.gltf` :

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

### <a name="the-files-section"></a>A *Files (fájlok* ) szakasz

Ez a szakasz tartalmazza a megadott fájlneveket.

* `input`: A forrásfájl neve.
* `output`: A kimeneti fájl neve, ha a felhasználó nem alapértelmezett nevet adott meg.

### <a name="the-conversionsettings-section"></a>A *conversionSettings* szakasz

Ez a szakasz a modell átalakításakor megadott [ConversionSettings](configure-model-conversion.md#settings-file) másolatát tartalmazza.

### <a name="the-inputinfo-section"></a>A *inputInfo* szakasz

Ez a szakasz a forrásfájl formátumával kapcsolatos adatokat rögzíti.

* `sourceAssetExtension`: A forrásfájl fájlkiterjesztés.
* `sourceAssetFormat`: A forrásfájl formátumának leírása.
* `sourceAssetFormatVersion`: A forrásfájl formátumának verziója.
* `sourceAssetGenerator`: A forrásfájlt létrehozó eszköz neve, ha van ilyen.

### <a name="the-inputstatistics-section"></a>A *inputStatistics* szakasz

Ez a szakasz a forrás jelenetről tartalmaz információkat. Az ebben a szakaszban szereplő értékek és a forrás modellt létrehozó eszköz egyenértékű értékei között gyakran előfordulnak eltérések. Ilyen eltérések várhatók, mert a modell az exportálási és átalakítási lépések során módosul.

* `numMeshes`: A rácsvonalak száma, amelyben az egyes részek hivatkozhatnak egyetlen anyagra.
* `numFaces`: A _háromszögek_ teljes száma a teljes modellben. Vegye figyelembe, hogy a háló a konverzió során háromszögárfolyam-számítást végez. Ez a szám a [szabványos renderelési kiszolgáló méretében](../../reference/vm-sizes.md#how-the-renderer-evaluates-the-number-of-polygons)járul hozzá a sokszög korlátozásához.
* `numVertices`: A teljes modellben lévő csúcspontok teljes száma.
* `numMaterial`: Az anyagok teljes száma a teljes modellben.
* `numFacesSmallestMesh`: A modell legkisebb rácsvonalában található háromszögek száma.
* `numFacesBiggestMesh`: A modell legnagyobb hálójában lévő háromszögek száma.
* `numNodes`: A modell Scene gráf csomópontjainak száma.
* `numMeshUsagesInScene`: A csomópontok száma, amikor a csomópontok hivatkozási rácsvonalak. Több csomópont is hivatkozhat ugyanarra a rácsvonalra.
* `maxNodeDepth`: A jelenet gráfon belüli csomópontok maximális mélysége.

### <a name="the-outputinfo-section"></a>A *outputInfo* szakasz

Ez a szakasz a generált kimenet általános információit rögzíti.

* `conversionToolVersion`: A modell átalakító verziója.
* `conversionHash`: A arrAsset belül található, a rendereléshez hozzájáruló adatokat tartalmazó kivonat. Azt is megtudhatja, hogy az átalakítási szolgáltatás eltérő eredményt adott-e meg, ha ugyanazon a fájlon fut újra.

### <a name="the-outputstatistics-section"></a>A *outputStatistics* szakasz

Ez a szakasz a konvertált eszközről kiszámított adatokat rögzíti.

* `numMeshPartsCreated`: A arrAsset lévő rácsvonalak száma. Ez a `numMeshes` szakasztól eltérő lehet `inputStatistics` , mert a egypéldányos az átalakítási folyamat érinti.
* `numMeshPartsInstanced`: A arrAsset újrafelhasznált rácsvonalak száma.
* `recenteringOffset`: Ha a `recenterToOrigin` [ConversionSettings](configure-model-conversion.md) engedélyezve van, ez az érték a fordítás, amely áthelyezi a konvertált modellt az eredeti helyére.
* `boundingBox`: A modell határai.

## <a name="next-steps"></a>További lépések

* [Modell átalakítása](model-conversion.md)
* [A modellátalakítás konfigurálása](configure-model-conversion.md)

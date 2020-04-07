---
title: Anyagleképezés modellformátumokhoz
description: A modellforrás-formátumokból PBR-anyaggá történő alapértelmezett átalakítás t ismerteti.
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: ce287ed94066aac4b900d2ddb02579a54b8550f6
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680387"
---
# <a name="material-mapping-for-model-formats"></a>Anyagleképezés modellformátumokhoz

A forráseszköz [modellként történő konvertálásakor](../how-tos/conversion/model-conversion.md)a konverter minden [hálóhoz](../concepts/meshes.md) [létrehoz anyagokat](../concepts/materials.md) . Az anyagok létrehozásának módja [felülbírálható.](../how-tos/conversion/override-materials.md) Alapértelmezés szerint azonban az átalakítás [PBR-anyagokat](../overview/features/pbr-materials.md)hoz létre . Mivel minden forrásfájl-formátum, például az FBX, saját konvenciókat használ az anyagok meghatározásához, ezeket a konvenciókat le kell képeznie az Azure távoli renderelés PBR-anyagparamétereire. 

Ez a cikk a forráseszközökből származó anyagok futásidejű anyagokká konvertálásához használt pontos leképezéseket sorolja fel.

## <a name="gltf"></a>glTF

Szinte mindent a glTF 2.0 specifikáció támogatja az Azure Remote Rendering, kivéve *EmissiveFactor* és *EmissiveTexture*.

Az alábbi táblázat a leképezést mutatja be:

| glTF | Azure Remote Rendering |
|:-------------------|:--------------------------|
|   baseColorFactor   |   albedoColor              |
|   baseColorTexture  |   albedoTérkép                |
|   metallicFactor    |   fémesség                |
|   fémes textúra   |   metalnessTérkép             |
|   érdességFaktor   |   Érdesség                |
|   érdességTextúra  |   érdességTérkép             |
|   okclusionFactor   |   Elzáródás                |
|   okklúziótextúra  |   okklúziós térkép             |
|   normalTexture     |   normalMap                |
|   alfaCutoff       |   alphaClipThreshold       |
|   alphaMode.OPAQUE  |   alphaClipEnabled = hamis, átlátszó = hamis |
|   alphaMode.MASK    |   alphaClipEnabled = igaz, átlátszó = hamis  |
|   alphaMode.BLEND   |   isTransparent = igaz     |
|   kétoldalas       |   isDoubleSided            |
|   emissiveFactor    |   -                        |
|   emissiveTextúra   |   -                        |

A glTF minden textúrájának lehet egy `texCoord` értéke, amelyet az Azure távoli renderelési anyagok is támogatnak.

### <a name="embedded-textures"></a>Beágyazott textúrák

A * \*.bin* vagy * \*.glb* fájlokba ágyazott textúrák támogatottak.

### <a name="supported-gltf-extension"></a>Támogatott glTF kiterjesztés

Az alapszolgáltatáskészleten kívül az Azure Remote Rendering a következő glTF-bővítményeket támogatja:

* [MSFT_packing_occlusionRoughnessMetallic](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Vendor/MSFT_packing_occlusionRoughnessMetallic/README.md)
* [MSFT_texture_dds](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Vendor/MSFT_texture_dds/README.md)
* [KHR_materials_unlit](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_materials_unlit/README.md): [Színanyagoknak](../overview/features/color-materials.md)felel meg. *Az emissive* anyagok esetében ajánlott ezt a bővítményt használni.
* [KHR_materials_pbrSpecularGlossiness](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_materials_pbrSpecularGlossiness/README.md): Fémes érdességi textúrák helyett szórt-specular-glossiness textúrákat adhat. Az Azure távoli leképezési implementációja közvetlenül követi a bővítmény konverziós képleteit.

## <a name="fbx"></a>FBX

Az FBX formátum zárt forráskódú, és az FBX anyagok általában nem kompatibilisek a PBR anyagokkal. Az FBX a felületek összetett leírását használja, sok egyedi paraméterrel és tulajdonsággal, és **nem mindegyiket használja az Azure távoli leadási folyamat.**

> [!IMPORTANT]
> Az Azure Remote Rendering modell konverziós folyamat csak támogatja **az FBX 2011 és újabb**.

Az FBX formátum konzervatív megközelítést határoz meg az anyagokra vonatkozóan, a hivatalos FBX specifikációban csak két típus van:

* *Lambert* - Nem általánosan használt jó ideje már, de még mindig támogatja a konvertáló Phong a konverziós idő.
* *Phong* - Szinte minden anyag és a legtöbb tartalom eszközök használja ezt a típust.

A Phong modell pontosabb, és ezt használják, mint az *egyetlen* modell FBX anyagok. Az alábbiakban az *FBX anyagaként*fogják emlegetni.

> Maya két egyéni kiterjesztést használ az FBX-hez, és egyéni tulajdonságokat határoz meg az anyag PBR- és Stingray-típusaira vonatkozóan. Ezek az adatok nem szerepelnek az FBX-specifikációban, ezért az Azure Remote Rendering jelenleg nem támogatja.

Az FBX Materials a Diffúz-Specular-SpecularLevel koncepciót használja, így a diffúz textúráról az albedo térképre való átalakításához ki kell számolnunk a többi paramétert, hogy kivonjuk őket a diffúzból.

> Az FBX összes színe és textúrája sRGB-térben (más néven Gamma térben) található, de az Azure Remote Rendering lineáris térrel működik a vizualizáció során, és a keret végén mindent sRGB-térré alakít át. Az Azure Remote Renderelési eszközfolyamat átalakítja mindent lineáris térben, hogy küldje el előkészített adatokat a renderelő.

Ez a táblázat bemutatja, hogyan vannak leképezve a textúrák az FBX-anyagokról az Azure remote rendering anyagokra. Némelyiket nem közvetlenül, hanem a képletekben részt vevő más textúrákkal kombinálva (például a diffúz textúra):

| FBX | Azure Remote Rendering |
|:-----|:----|
| AmbientColor (Környezetiszín) | Okklúziós térkép   |
| Szórt színező | *használt Albedo, Metalness* |
| Átlátszószín | *használt alfa-csatorna Albedo* |
| TransparencyFactor (Átláthatósági tényező) | *használt alfa-csatorna Albedo* |
| Opacitás | *használt alfa-csatorna Albedo* |
| SpecularColor | *használt Albedo, Fémesség, Érdesség* |
| SpecularFactor| *használt Albedo, Fémesség, Érdesség* |
| ShininessExponent között | *használt Albedo, Fémesség, Érdesség* |
| NormalMap | NormalMap |
| Bump | *normál térképé alakítva* |
| EmissiveColor | - |
| EmissiveFactor | - |
| ReflectionColor (Tükröződésszín) | - |
| DisplacementColor (ElmozdulásSzín) | - |

A fenti feltérképezés az anyagátalakítás legösszetettebb része, számos feltételezés miatt, amelyeket meg kell tenni. Ezeket a feltételezéseket az alábbiakban tárgyaljuk.

Az alábbiakban használt néhány meghatározás:

* `Specular` =  `SpecularColor` * `SpecularFactor`
* `SpecularIntensity` = `Specular`. Piros ≥ 0,2125 + `Specular`. Zöld ≥ 0,7154 + `Specular`. Kék ( 0,0721 )
* `DiffuseBrightness`= 0,299 `Diffuse`* . Piros<sup>2</sup> + 0,587 * `Diffuse`. Zöld<sup>2</sup> + 0,114 * `Diffuse`. Kék<sup>2</sup>
* `SpecularBrightness`= 0,299 `Specular`* . Piros<sup>2</sup> + 0,587 * `Specular`. Zöld<sup>2</sup> + 0,114 * `Specular`. Kék<sup>2</sup>
* `SpecularStrength`= max(`Specular`. Piros, `Specular`. Zöld, `Specular`. Kék)

A SpecularIntensity képlet [itt](https://en.wikipedia.org/wiki/Luma_(video))érhető el .
A fényerő képletét ez a [specifikáció](http://www.itu.int/dms_pubrec/itu-r/rec/bt/R-REC-BT.601-7-201103-I!!PDF-E.pdf)ismerteti.

### <a name="roughness"></a>Érdesség

`Roughness`ebből `Specular` a `ShininessExponent` képletből számítják ki és [használjuk.](https://www.cs.cornell.edu/~srm/publications/EGSR07-btdf.pdf) A képlet a Phong specular exponens érdességének közelítése:

```Cpp
Roughness = sqrt(2 / (ShininessExponent * SpecularIntensity + 2))
```

### <a name="metalness"></a>Fémesség

`Metalness`ebből `Diffuse` a `Specular` képletből számítják ki és használjuk [a glTF specifikációból.](https://github.com/bghgary/glTF/blob/gh-pages/convert-between-workflows-bjs/js/babylon.pbrUtilities.js)

Az ötlet itt az, hogy megoldjuk az egyenletet: Ax<sup>2</sup> + Bx + C = 0.
Alapvetően, dielektromos felületek tükrözik mintegy 4%-a fény egy specular módon, és a többi diffúz. Fémes felületek nem tükrözik a fényt diffúz módon, de minden egy specular módon.
Ennek a képletnek van néhány hátránya, mert nincs mód arra, hogy különbséget tegyünk a fényes műanyag és a fényes fémfelületek között. Feltételezzük, hogy a felület legtöbbször fémes tulajdonságokkal rendelkezik, és ennek következtében a fényes műanyag/gumi felületek nem a várt módon néznek ki.
```cpp
dielectricSpecularReflectance = 0.04
oneMinusSpecularStrength = 1 - SpecularStrength

A = dielectricSpecularReflectance
B = (DiffuseBrightness * (oneMinusSpecularStrength / (1 - A)) + SpecularBrightness) - 2 * A
C = A - SpecularBrightness
squareRoot = sqrt(max(0.0, B * B - 4 * A * C))
value = (-B + squareRoot) / (2 * A)
Metalness = clamp(value, 0.0, 1.0);
```

### <a name="albedo"></a>Albedo között

`Albedo`a számítás `Diffuse`a `Specular`lehetőségből történik, és `Metalness`a.

Ametalness részben leírtak szerint a dielektromos felületek a fény mintegy 4%-át tükrözik.  
Az ötlet itt az, hogy `Dielectric` lineárisan interpolálja között és `Metal` a színek segítségével `Metalness` érték tényezőként. Ha a `0.0`fémesség , akkor attól függően, hogy specular lesz vagy egy sötét színű (ha a lénfém magas), vagy diffúz nem változik (ha nincs specular jelen). Ha a fémesség nagy érték, akkor a diffúz szín eltűnik a fényvisszaverődési szín javára.

```Cpp
dielectricSpecularReflectance = 0.04
oneMinusSpecularStrength = 1 - SpecularStrength

dielectricColor = diffuseColor * (oneMinusSpecularStrength / (1.0f - dielectricSpecularReflectance) / max(1e-4, 1.0 - metalness))
metalColor = (Specular - dielectricSpecularReflectance * (1.0 - metalness)) * (1.0 / max(1e-4, metalness))
albedoRawColor = lerpColors(dielectricColor, metalColor, metalness * metalness)
AlbedoRGB = clamp(albedoRawColor, 0.0, 1.0);
```

`AlbedoRGB`a fenti képlet alapján számították ki, de az alfa-csatorna további számításokat igényel. Az FBX formátum homályos az átláthatóságról, és sokféleképpen definiálhatja azt. A különböző tartalomeszközök különböző módszereket használnak. Az ötlet itt az, hogy egyesítse őket egy képlet. Egyes eszközöket azonban helytelenül átláthatóként jelenít meg, ha nem közös módon hozták létre őket.

Ezt a számítás `TransparentColor` `TransparencyFactor`a `Opacity`, , :

ha `Opacity` van meghatározva, akkor `AlbedoAlpha`  =  `Opacity` használja közvetlenül: else  
ha `TransparencyColor` meg van `AlbedoAlpha` adva, akkor =`TransparentColor`1,0 - (( . Piros `TransparentColor`+ . Zöld `TransparentColor`+ . Kék) / 3.0) más  
ha `TransparencyFactor`, `AlbedoAlpha` akkor = 1,0 -`TransparencyFactor`

A `Albedo` végső szín négy csatornával rendelkezik, amelyek a `AlbedoRGB` . `AlbedoAlpha`

### <a name="summary"></a>Összefoglalás

Összefoglalva itt `Albedo` lesz nagyon közel az `Diffuse`eredeti `Specular` , ha közel van a nulla. Ellenkező esetben a felület úgy néz ki, mint egy fémes felület, és elveszti a diffúz színt. A felület fog kinézni polírozott és fényvisszaverő, ha `ShininessExponent` elég nagy és `Specular` világos. Ellenkező esetben a felület fog kinézni durva, és alig tükrözik a környezetet.

### <a name="known-issues"></a>Ismert problémák

* A jelenlegi képlet nem működik jól az egyszerű színes geometria. Ha `Specular` elég világos, akkor minden geometria fényvisszaverő fémfelületekké válik, szín nélkül. A megoldás az, `Specular` hogy az eredetihez képest 30%-ra csökken, vagy az [fbxAssumeMetallic](../how-tos/conversion/configure-model-conversion.md#converting-from-older-fbx-formats-with-a-phong-material-model)konverziós beállítást használja.
* PBR-anyagok at nemrég `Maya` `3DS Max` adtak hozzá, és a tartalom-létrehozási eszközök. Az általuk használt egyéni felhasználó által definiált fekete doboz tulajdonságait, hogy adja át az FBX. Az Azure távoli renderelés nem olvassa ezeket a további tulajdonságokat, mert azok nincsenek dokumentálva, és a formátum zárt forrásból.

## <a name="next-steps"></a>További lépések

* [Modell átalakítása](../how-tos/conversion/model-conversion.md)
* [Felülíró anyagok a modell átalakítása során](../how-tos/conversion/override-materials.md)

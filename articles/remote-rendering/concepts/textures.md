---
title: Textúrák
description: Textúra erőforrás-munkafolyamata
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 09fa22d33377dfcbafd84f0caeb5f33a575b1bce
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681661"
---
# <a name="textures"></a>Textúrák

A textúrák egy megváltoztathatatlan [megosztott erőforrás](../concepts/lifetime.md). A textúrák a [blob Storage](../how-tos/conversion/blob-storage.md) -ból tölthetők be, és közvetlenül a modellekre alkalmazhatók, ahogy az [oktatóanyagban is látható: a környezet és az anyagok módosítása](../tutorials/unity/changing-environment-and-materials.md). Leggyakrabban azonban a textúrák egy [átalakított modell](../how-tos/conversion/model-conversion.md)részei lesznek, ahol az [anyagok](materials.md)hivatkoznak rájuk.

## <a name="texture-types"></a>Textúra típusai

A különböző típusú anyagminták különböző használati esetekkel rendelkeznek:

* a **2D-textúrák** főleg [anyagokban](materials.md)használatosak.
* A **Cubemaps** használható az [égbolton](../overview/features/sky.md).

## <a name="supported-texture-formats"></a>Támogatott textúra-formátumok

Az ARR-nek adott összes textúráknak [DDS formátumúnak](https://en.wikipedia.org/wiki/DirectDraw_Surface)kell lennie. Lehetőleg a Mipmap és a textúra tömörítéssel. Ha szeretné automatizálni az átalakítási folyamatot, tekintse meg [a TexConv parancssori eszközt](../resources/tools/tex-conv.md) .

## <a name="loading-textures"></a>Textúrák betöltése

Textúra betöltésekor meg kell adnia a várt típust. Ha a típus nem egyezik, a textúra betöltése sikertelen lesz.
Ha egy textúrát kétszer tölt be ugyanazzal az URI-val, akkor ugyanazt a textúrát fogja visszaadni, mivel ez egy [megosztott erőforrás](../concepts/lifetime.md).

A modellek betöltéséhez hasonlóan két változatban kell foglalkoznia a forrás blob Storage-ban található textúra-objektummal:

* A textúrát az SAS URI-ja tudja kezelni. A megfelelő betöltési `LoadTextureFromSASAsync` függvény `LoadTextureFromSASParams`paraméterrel van ellátva. A [beépített textúrák](../overview/features/sky.md#built-in-environment-maps)betöltésekor ezt a változatot is használhatja.
* A textúrát közvetlenül a blob Storage-paraméterekkel lehet megoldani, abban az esetben, ha a [blob Storage a fiókhoz van társítva](../how-tos/create-an-account.md#link-storage-accounts). Ebben az esetben `LoadTextureAsync` a megfelelő betöltési függvény `LoadTextureParams`a paraméter.

Az alábbi mintakód bemutatja, hogyan tölthető be egy textúra az SAS URI-n keresztül (vagy beépített textúra) – vegye figyelembe, hogy csak a loading Function/paraméter különbözik a másik esettől:

``` cs
LoadTextureAsync _textureLoad = null;
void LoadMyTexture(AzureSession session, string textureUri)
{
    _textureLoad = session.Actions.LoadTextureAsync(new LoadTextureParams(textureUri, TextureType.Texture2D));
    _textureLoad.Completed +=
        (LoadTextureAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                System.Console.WriteLine("Texture loading failed!");
            }
            _textureLoad = null;
        };
}
```

Attól függően, hogy mit kell használni a textúrában, a textúra típusa és tartalma korlátozásokat is tartalmazhat. Például a [pbr-anyagokhoz](../overview/features/pbr-materials.md) tartozó érdesség térképének szürkeárnyalatos kell lennie.

> [!CAUTION]
> Az *ARR összes aszinkron függvénye aszinkron műveleti* objektumokat ad vissza. A művelet befejezését követően az objektumokra mutató hivatkozást kell tárolnia. Ellenkező esetben előfordulhat, hogy a C# Garbage Collector már korán törli a műveletet, és soha nem tud befejezni. A (z) "_textureLoad" tag változó feletti mintakód egy hivatkozás tárolására szolgál, amíg a *befejezett* esemény megérkezik.

## <a name="next-steps"></a>További lépések

* [Anyagok](materials.md)
* [Ég](../overview/features/sky.md)

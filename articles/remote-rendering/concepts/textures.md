---
title: Textúrák
description: Textúra erőforrás munkafolyamat
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 09fa22d33377dfcbafd84f0caeb5f33a575b1bce
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681661"
---
# <a name="textures"></a>Textúrák

A textúrák nem módosítható [megosztott erőforrást jelentenek.](../concepts/lifetime.md) Textúrák lehet betölteni [blob storage](../how-tos/conversion/blob-storage.md) és a modellekre közvetlenül alkalmazható, amint azt [az oktatóanyag: A környezet és az anyagok módosítása](../tutorials/unity/changing-environment-and-materials.md). A textúrák azonban leggyakrabban egy átalakított [modell](../how-tos/conversion/model-conversion.md)részét képezik, ahol az [anyagok](materials.md)hivatkoznak rá.

## <a name="texture-types"></a>Textúratípusok

A különböző textúratípusok különböző használati esetekben:

* **2D textúrák** elsősorban [anyagokban](materials.md).
* **Cubemaps** lehet használni az [égen](../overview/features/sky.md).

## <a name="supported-texture-formats"></a>Támogatott textúraformátumok

Az ARR-nek adott összes textúrának [DDS formátumban](https://en.wikipedia.org/wiki/DirectDraw_Surface)kell lennie. Lehetőleg mipmaps és textúra tömörítés. Ha automatizálni szeretné az átalakítási folyamatot, tekintse meg [a TexConv parancssori eszközt.](../resources/tools/tex-conv.md)

## <a name="loading-textures"></a>Textúrák betöltése

A textúra betöltésekor meg kell adnia a várt típusát. Ha a típus nem egyezik, a textúra terhelése sikertelen lesz.
Ha egy textúrát kétszer tölt be ugyanazzal az URI-val, ugyanazt a textúraobjektumot adja vissza, mivel az [megosztott erőforrás.](../concepts/lifetime.md)

A modellek betöltéséhez hasonlóan a textúraeszköz címzésének két változata van a forrásblob-tárolóban:

* A textúraeszköz a SAS URI-val kezelhető. A megfelelő `LoadTextureFromSASAsync` terhelési `LoadTextureFromSASParams`funkció a paraméterrel van eladva. Használja ezt a változatot [a beépített textúrák betöltésekor](../overview/features/sky.md#built-in-environment-maps)is.
* A textúra közvetlenül a blob storage paramétereivel kezelhető, ha a [blobstorage a fiókhoz van csatolva.](../how-tos/create-an-account.md#link-storage-accounts) A vonatkozó terhelési `LoadTextureAsync` funkció `LoadTextureParams`ebben az esetben a paraméterrel van eladva.

A következő mintakód bemutatja, hogyan kell betölteni egy textúrát a SAS URI-n (vagy beépített textúrán) keresztül - vegye figyelembe, hogy csak a betöltési funkció/paraméter különbözik a másik esetben:

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

Attól függően, hogy a textúrát mire kellene használni, korlátozások lehetnek a textúra típusára és tartalmára vonatkozóan. A [PBR-anyag](../overview/features/pbr-materials.md) érdességi térképének például szürkeárnyalatosnak kell lennie.

> [!CAUTION]
> Az ARR összes *Async* függvénye aszinkron műveleti objektumokat ad vissza. A művelet befejezéséig meg kell tárolnia az objektumokra mutató hivatkozást. Ellenkező esetben a C# szemétgyűjtő törölheti a műveletet korán, és soha nem fejeződhet be. A fenti mintakódban a "_textureLoad" tagváltozó tartjuk a hivatkozást, amíg a *Befejezett* esemény meg nem érkezik.

## <a name="next-steps"></a>További lépések

* [Nyersanyagok](materials.md)
* [Ég](../overview/features/sky.md)

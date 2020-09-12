---
title: Színes anyagok
description: A színanyag típusát írja le.
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: cda4aa9a811bac0ccf20caec32ee38da9b46b6c7
ms.sourcegitcommit: f845ca2f4b626ef9db73b88ca71279ac80538559
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/09/2020
ms.locfileid: "89613774"
---
# <a name="color-materials"></a>Színes anyagok

A *színanyagok* az Azure távoli renderelés egyik támogatott [anyag-típusa](../../concepts/materials.md) . Ezeket a [hálókat olyan rácsvonalak](../../concepts/meshes.md) használják, amelyek nem kapnak semmiféle megvilágítást, hanem minden esetben teljes fényerővel rendelkeznek. Ez lehet a "ragyogó" anyagok, például az autós irányítópultok, a villanykörték, vagy a már meglévő, statikus megvilágítás, például a [photogrammetry](https://en.wikipedia.org/wiki/Photogrammetry)-on keresztül beszerzett modellek.

A színanyagok hatékonyabbak, mint a [pbr-anyagok](pbr-materials.md) , az egyszerűbb árnyékolási modelljük miatt. Különböző átlátszósági módokat is támogatnak.

## <a name="common-material-properties"></a>Gyakori anyagok tulajdonságai

Ezek a tulajdonságok az összes anyagnál közösek:

* **albedoColor:** Ez a szín más színekkel, például a *albedoMap* vagy a * :::no-loc text="vertex"::: színekkel*együtt van megszorozva. Ha az *átlátszóság* engedélyezve van egy adott anyagon, az alfa-csatorna az opacitás beállítására szolgál, amely `1` teljes mértékben átlátszatlan és `0` teljes átláthatóságot jelent. Az alapértelmezett érték fehér.

  > [!NOTE]
  > Mivel a színanyagok nem tükrözik a környezetet, a teljes átlátszó színanyag láthatatlanná válik. Ez a pbr- [anyagok](pbr-materials.md)esetében különbözik.

* **albedoMap:** [2D-textúra](../../concepts/textures.md) a képpont albedó értékekhez.

* **alphaClipEnabled** és **alphaClipThreshold:** ha a *alphaClipEnabled* értéke igaz, az összes képpont, ahol a albedó alfa-értéke alacsonyabb, mint a *alphaClipThreshold* , nem lesz kirajzolva. Az Alpha-levágást még az átlátszóság engedélyezése nélkül is használhatja, és a renderelés sokkal gyorsabb. Az alfa-levágott anyagok továbbra is lassabbak, mint a teljes mértékben átlátszatlan anyagok, mégis. Alapértelmezés szerint az Alpha levágása le van tiltva.

* **textureCoordinateScale** és **textureCoordinateOffset:** a skála az UV-textúra koordinátáiba van szorozva, az eltolás hozzá lesz adva. A textúrák nyújtására és átváltására használható. Az alapértelmezett skála (1, 1) és az eltolás (0, 0).

* **useVertexColor:** Ha a háló :::no-loc text="vertex"::: színeket tartalmaz, és ez a beállítás engedélyezve van, a rácsvonalak :::no-loc text="vertex"::: színe megszorozva a *AlbedoColor* és a *albedoMap*. Alapértelmezés szerint a *useVertexColor* le van tiltva.

* **isDoubleSided:** Ha a kétoldalas megjelenítés értéke TRUE (igaz), akkor az ezzel az anyaggal rendelkező háromszögek akkor is jelennek meg, ha a kamera a háttérben néz. Alapértelmezés szerint ez a beállítás le van tiltva. Lásd még: [ :::no-loc text="Single-sided"::: renderelés](single-sided-rendering.md).

## <a name="color-material-properties"></a>Színanyag tulajdonságai

A következő tulajdonságok a színanyagokra jellemzőek:

* **vertexMix:** Ez az érték `0` és `1` annak megadása, hogy a :::no-loc text="vertex"::: [Rácsvonalak](../../concepts/meshes.md) színe milyen mértékben járul hozzá a végső színhez. Az alapértelmezett 1 értéknél a szín a :::no-loc text="vertex"::: albedó teljes színének szorzata. A 0 értékkel a :::no-loc text="vertex"::: színek figyelmen kívül lesznek hagyva.

* **transparencyMode:** A [pbr-anyagokkal](pbr-materials.md)ellentétben a színanyagok különbséget tesznek a különböző áttetszőségi módok között:

  1. **Átlátszatlan:** Az alapértelmezett mód letiltja az átlátszóságot. Az Alpha-levágás továbbra is lehetséges, de érdemes előnyben részesíteni, ha szükséges.
  
  1. **AlphaBlended:** Ez a mód hasonló a PBR-anyagok átlátszósági módjához. A rendszer az olyan áttekinthető anyagokhoz használható, mint például az üveg.

  1. **Adalékanyag:** Ez a legegyszerűbb és leghatékonyabb átlátszósági mód. Az anyag hozzájárulása hozzáadódik a megjelenített képhez. Ezzel a móddal szimulálhatja a fénylő (de még transzparens) objektumokat, például a fontos objektumok kiemeléséhez használt jelölőket.

## <a name="api-documentation"></a>API-dokumentáció

* [C# ColorMaterial osztály](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.colormaterial)
* [C# RemoteManager. CreateMaterial ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.remotemanager.creatematerial)
* [C++ ColorMaterial osztály](https://docs.microsoft.com/cpp/api/remote-rendering/colormaterial)
* [C++ RemoteManager:: CreateMaterial ()](https://docs.microsoft.com/cpp/api/remote-rendering/remotemanager#creatematerial)

## <a name="next-steps"></a>Következő lépések

* [PBR-anyagok](pbr-materials.md)
* [Textúrák](../../concepts/textures.md)
* [Hálók](../../concepts/meshes.md)

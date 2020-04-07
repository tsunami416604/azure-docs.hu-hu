---
title: Színes anyagok
description: A színanyag típusát írja le.
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 7cbcaefcc087c9f1c7c09668a27fbdef9a4802d3
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681076"
---
# <a name="color-materials"></a>Színes anyagok

*A színes anyagok* az Azure távoli renderelése egyik támogatott [anyagtípusa.](../../concepts/materials.md) Olyan egyszerű [használatra](../../concepts/meshes.md) használják őket, amelyek nem kapnak semmilyen világítást, hanem mindig teljes fényerőt. Ez lehet a helyzet az "izzó" anyagok, mint például az autó műszerfal, izzók, vagy az adatok, amelyek már tartalmaz statikus világítás, mint például a modellek keresztül kapott [fotogrammetria](https://en.wikipedia.org/wiki/Photogrammetry).

A színes anyagok egyszerűbb árnyékolási modelljüknek köszönhetően hatékonyabbak a [PBR-anyagok](pbr-materials.md) rendereléséhez. Támogatják a különböző átláthatósági módokat is.

## <a name="common-material-properties"></a>Közös anyagtulajdonságok

Ezek a tulajdonságok minden anyagra közösek:

* **albedoColor:** Ezt a színt megszorozzuk más színekkel, például az *albedoMap* vagy *a csúcspont színekkel.* Ha egy anyagon engedélyezve van az *átlátszóság,* az alfa-csatorna az opacitás beállítására szolgál, teljesen átlátszatlan és `1` `0` teljesen átlátszó jelentéssel. Az alapértelmezett érték a fehér.

  > [!NOTE]
  > Mivel a színes anyagok nem tükrözik a környezetet, egy teljesen átlátszó színanyag láthatatlanná válik. Ez más a [PBR anyagok](pbr-materials.md).

* **albedoMap:** [2D textúra](../../concepts/textures.md) a képpontonkénti albedo értékekhez.

* **alphaClipEnabled** és **alphaClipThreshold:** Ha *alphaClipEnabled* igaz, minden pixel, ahol az albedo alfa értéke alacsonyabb, mint *alphaClipThreshold* nem lesz megrajzolva. Alfa nyírás lehet használni anélkül, hogy az átláthatóság és sokkal gyorsabb teszi. Alpha nyírt anyagok még mindig lassabb, hogy tegye, mint a teljesen átlátszatlan anyagok, mégis. Alapértelmezés szerint az alfa-vágás le van tiltva.

* **textureCoordinateScale** és **textureCoordinateOffset:** A skála megszorozva lesz az UV textúra koordinátáiban, az eltolás hozzáadódik. Lehet használni, hogy nyúlik, és a shift a textúrák. Az alapértelmezett lépték (1, 1) és az eltolás (0, 0).

* **useVertexColor:** Ha a háló csúcspontszíneket tartalmaz, és ez a beállítás engedélyezve van, a hálók csúcspontjainak csúcspontjai megszorozzák az *albedoColor* és *albedoMap színeket.* Alapértelmezés szerint a csúcspont színei le vannak tiltva.

* **isDoubleSided:** Ha a kétoldalas ság igaz értékre van állítva, az ezzel az anyaggal rendelkező háromszögek akkor is megjelennek, ha a kamera a hátukon van. Alapértelmezés szerint ez a beállítás le van tiltva. Lásd még: [Egyoldalas renderelés](single-sided-rendering.md).

## <a name="color-material-properties"></a>Színanyag tulajdonságai

A következő tulajdonságok a színes anyagokra vonatkoznak:

* **vertexMix:** Ez az `0` `1` érték a háló között van, és azt határozza meg, hogy a [háló](../../concepts/meshes.md) csúcspontja milyen erősen járul jon hozzá a végső színhez. Az alapértelmezett 1 értéknél a csúcspont színe teljesen megszorozza az albedo színt. 0 értékkel a csúcspont színeit a program teljesen figyelmen kívül hagyja.

* **transparencyMode:** A [PBR-anyagokkal](pbr-materials.md)ellentétben a színes anyagok megkülönböztetik a különböző átlátszósági módokat:

  1. **Átlátszatlan:** Az alapértelmezett mód letiltja az áttetszőséget. Alfa nyírás még mindig lehetséges, mégis, és előnyben kell részesíteni, ha elegendő.
  
  1. **AlfaKevert:** Ez a mód hasonló a PBR-anyagok átlátszósági módjához. Ezt kell használni átlátszó anyagok, mint az üveg.

  1. **Adalékanyag:** Ez a mód a legegyszerűbb és leghatékonyabb átlátszósági mód. Az anyag hozzájárulása hozzáadódik a renderelt képhez. Ezzel a móddal ragyogó (de még átlátszó) objektumokat szimulálhat, például fontos objektumok kiemelésére használt jelölőket.

## <a name="next-steps"></a>További lépések

* [PBR anyagok](pbr-materials.md)
* [Textúrák](../../concepts/textures.md)
* [Rácsvonalak](../../concepts/meshes.md)

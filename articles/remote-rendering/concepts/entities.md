---
title: Entitások
description: Az Azure távoli renderelési API hatókörében lévő entitások meghatározása
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: d7b9ecd048b080ae0ec9fd3fb7a4fb35009551b8
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681947"
---
# <a name="entities"></a>Entitások

Az *entitás* egy mozgatható objektumot képvisel a térben, és a távolról megjelenített tartalom alapvető építőköve.

## <a name="entity-properties"></a>Entitás tulajdonságai

Az entitások egy pozíció, elforgatás és lépték által meghatározott átalakítással rendelkeznek. Önmagukban entitások nem rendelkeznek megfigyelhető funkcióval. Ehelyett a viselkedés az entitásokhoz kapcsolódó összetevőkön keresztül kerül hozzáadásra. Például egy [CutPlaneComponent](../overview/features/cut-planes.md) csatlakoztatásával egy kivágott síkot hoz létre az entitás pozíciójában.

Maga az entitás legfontosabb szempontja a hierarchia és az eredményül kapott hierarchikus átalakítás. Ha például több entitás kapcsolódik gyermekként egy megosztott fölérendelt entitáshoz, az összes ilyen entitás áthelyezhető, elforgatható és skálázható a fölérendelt entitás átalakításának módosításával.

Az entitás egyedi tulajdonában van a szülő, ami azt `Entity.Destroy()`jelenti, hogy ha a szülő megsemmisül , így a gyermekek és az összes kapcsolódó [összetevő](components.md). Így egy modell eltávolítása a helyszínről úgy `Destroy` érhető el, hogy meghívja `AzureSession.Actions.LoadModelAsync()` egy modell gyökércsomópontját, amelyet a SAS-változat vagy annak SAS-változata `AzureSession.Actions.LoadModelFromSASAsync()`ad vissza.

Az entitások akkor jönnek létre, amikor a kiszolgáló betölti a tartalmat, vagy amikor a felhasználó objektumot szeretne hozzáadni a jelenethez. Ha például egy felhasználó egy háló belsejének megjelenítéséhez hozzá szeretne adni egy kivágott síkot, a felhasználó létrehozhat egy entitást, ahol a síknak léteznie kell, majd hozzáadhatja hozzá a kivágott sík összetevőt.

## <a name="query-functions"></a>Lekérdezési függvények

Az entitásokon kétféle lekérdezési függvény létezik: szinkron és aszinkron hívások. Szinkron lekérdezések csak az ügyfélen található adatokhoz használhatók, és nem tartalmaznak sok számítást. Ilyenek például az összetevők, a relatív objektumátalakítások vagy a szülő-gyermek kapcsolatok lekérdezése. Aszinkron lekérdezések olyan adatokhoz használatosak, amelyek csak a kiszolgálón találhatók, vagy olyan extra számítást tartalmaznak, amely túl drága lenne az ügyfélen való futtatáshoz. Ilyenek például a térbeli határlekérdezések vagy metaadat-lekérdezések.

### <a name="querying-components"></a>Összetevők lekérdezése

Egy adott típus összetevőjének `FindComponentOfType`megkereséséhez használja a következőt:

```cs
CutPlaneComponent cutplane = (CutPlaneComponent)entity.FindComponentOfType(ObjectType.CutPlaneComponent);

// or alternatively:
CutPlaneComponent cutplane = entity.FindComponentOfType<CutPlaneComponent>();
```

### <a name="querying-transforms"></a>Átalakítások lekérdezése

Az átalakítási lekérdezések az objektum szinkron hívásai. Fontos megjegyezni, hogy az API-n keresztül lekérdezett átalakítások helyi térátalakítások, az objektum szülőjéhez képest. Kivételt képeznek a gyökérobjektumok, amelyek esetében a helyi tér és a világtér azonos.

> [!NOTE]
> Nincs külön API az tetszőleges objektumok világtér-átalakításának lekérdezésére.

```cs
// local space transform of the entity
Double3 translation = entity.Position;
Quaternion rotation = entity.Rotation;
```

### <a name="querying-spatial-bounds"></a>Térbeli határok lekérdezése

A határlekérdezések olyan aszinkron hívások, amelyek teljes objektumhierarchián működnek, és egy entitást használnak gyökérként. Tekintse meg az objektumhatárokról szóló külön [fejezetet.](object-bounds.md)

### <a name="querying-metadata"></a>Metaadatok lekérdezése

A metaadatok az objektumokon tárolt további adatok, amelyeket a kiszolgáló figyelmen kívül hagy. Az objektum metaadatai lényegében (név, érték) párok halmaza, ahol az _érték_ numerikus, logikai vagy karakterlánctípusú lehet. A metaadatok exportálhatók a modellel.

A metaadat-lekérdezések egy adott entitás aszinkron hívásai. A lekérdezés csak egyetlen entitás metaadatait adja vissza, az algráf egyesített adatait nem.

```cs
MetadataQueryAsync metaDataQuery = entity.QueryMetaDataAsync();
metaDataQuery.Completed += (MetadataQueryAsync query) =>
{
    if (query.IsRanToCompletion)
    {
        ObjectMetaData metaData = query.Result;
        ObjectMetaDataEntry entry = metaData.GetMetadataByName("MyInt64Value");
        System.Int64 intValue = entry.AsInt64;

        // ...
    }
};
```

A lekérdezés akkor is sikeres lesz, ha az objektum nem tartalmaz metaadatokat.

## <a name="next-steps"></a>További lépések

* [Összetevők](components.md)
* [Objektumhatárok](object-bounds.md)

---
title: Címkék hozzáadása a digitális ikrekhez
titleSuffix: Azure Digital Twins
description: 'Lásd: címkék implementálása a digitális ikrekben'
author: baanders
ms.author: baanders
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 59f68909e2f3704fea5c38e3f1535f5996b284ab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87099174"
---
# <a name="add-tags-to-digital-twins"></a>Címkék hozzáadása a digitális ikrekhez 

A címkék fogalmát a digitális ikrek további azonosítására és kategorizálására használhatja. Bizonyos esetekben előfordulhat, hogy a felhasználók a meglévő rendszerekről, például a szénakazalban- [címkékről](https://project-haystack.org/doc/TagModel)kívánják replikálni a címkéket az Azure digitális Twins példányain belül. 

Ez a dokumentum a címkék digitális ikreken való megvalósításához használható mintákat ismerteti.

A rendszer először hozzáadja a címkéket a [modellen](concepts-models.md) belül, amely a digitális Twin-et ismerteti. Ezt a tulajdonságot ezután a Twin értékre állítja be, amikor a modell alapján jön létre. Ezt követően a címkék használhatók a [lekérdezésekben](concepts-query-language.md) az ikrek azonosítására és szűrésére.

## <a name="marker-tags"></a>Jelölő címkék 

A **jelölő címke** egy egyszerű karakterlánc, amely egy digitális iker jelölésére vagy kategorizálására szolgál, például "Blue" vagy "Red". Ez a karakterlánc a címke neve, és a jelölő címkék nem rendelkeznek értelmes értékkel – a címke csak a jelenléte (vagy a távollét) alapján jelentős. 

### <a name="add-marker-tags-to-model"></a>Jelölő címkék hozzáadása a modellhez 

A jelölő címkék [DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) -leképezésként vannak modellezve `string` `boolean` . A logikai értéket a `mapValue` rendszer figyelmen kívül hagyja, mivel a címke jelenléte minden fontos. 

Íme egy részlet egy olyan kettős modellből, amely a jelölő címkét tulajdonságként implementálja:

```json
{
  "@type": "Property",
  "name": "tags",
  "schema": {
    "@type": "Map",
    "mapKey": {
      "name": "tagName",
      "schema": "string"
    },
    "mapValue": {
      "name": "tagValue",
      "schema": "boolean"
    }
  }
}
```

### <a name="add-marker-tags-to-digital-twins"></a>Jelölő címkék hozzáadása a digitális ikrekhez

Ha a `tags` tulajdonság egy digitális Twin modell részét képezi, beállíthatja a jelölő címkét a digitális Twin-ben a tulajdonság értékének megadásával. 

Íme egy példa, amely `tags` három ikrek számára tölti fel a jelölőt:

```csharp
entity-01: "tags": { "red": true, "round": true } 
entity-02: "tags": { "blue": true, "round": true } 
entity-03: "tags": { "red": true, "large": true } 
```

### <a name="query-with-marker-tags"></a>Lekérdezés jelölő címkékkel

Miután hozzáadta a címkéket a digitális ikrekhez, a címkék használatával szűrheti az ikreket a lekérdezésekben. 

Itt látható egy lekérdezés, amely a "Red" címkével jelölt összes ikreket lekérdezi: 

```sql
select * from digitaltwins where is_defined(tags.red) 
```

Az összetettebb lekérdezésekhez címkék is kombinálhatók. Itt látható egy lekérdezés, amely az összes olyan ikreket lekérdezi, amely nem piros: 

```sql
select * from digitaltwins where not is_defined(tags.red) and is_defined(tags.round) 
```

## <a name="value-tags"></a>Érték Címkék 

Az **érték címke** egy kulcs-érték pár, amely az egyes címkék értékének megadására szolgál (például `"color": "blue"` vagy `"color": "red"` ). Miután létrehozta az érték címkét, azt jelölő címkeként is használhatja a címke értékének figyelmen kívül hagyásával. 

### <a name="add-value-tags-to-model"></a>Érték Címkék hozzáadása a modellhez 

Az érték címkék [DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) -leképezésként vannak modellezve `string` `string` . A `mapKey` és a `mapValue` is jelentős. 

Itt látható egy olyan kettős modellből származó részlet, amely egy érték címkét valósít meg tulajdonságként:

```json
{
  "@type": "Property",
  "name": "tags",
  "schema": {
    "@type": "Map",
    "mapKey": {
      "name": "tagName",
      "schema": "string"
    },
    "mapValue": {
      "name": "tagValue",
      "schema": "string"
    }
  }
} 
```

### <a name="add-value-tags-to-digital-twins"></a>Érték Címkék hozzáadása a digitális ikrekhez

Ahogy a jelölő címkék esetében is, a Value (érték) címkét egy digitális ikeren állíthatja be úgy, hogy a `tags` tulajdonság értékét a modellből állítja be. Ha az érték címkét jelölő címkeként szeretné használni, beállíthatja a `tagValue` mezőt az üres sztring () értékre `""` . 

Íme egy példa, amely három ikrek értékét tölti `tags` fel:

```csharp
entity-01: "tags": { "red": "", "size": "large" } 
entity-02: "tags": { "purple": "", "size": "small" } 
entity-03: "tags": { "red": "", "size": "small" } 
```

Vegye figyelembe, hogy a `red` `purple` példában jelölő címkékként használjuk.

### <a name="query-with-value-tags"></a>Lekérdezés érték címkékkel

Mint a jelölő címkék esetében, az érték címkék használatával szűrheti az ikreket a lekérdezésekben. Az érték címkék és a jelölő címkék együtt is használhatók.

A fenti példában `red` jelölő címkeként használjuk. Itt látható egy lekérdezés, amely a "Red" címkével jelölt összes ikreket lekérdezi: 

```sql
select * from digitaltwins where is_defined(tags.red) 
```

Itt látható egy lekérdezés, amely az összes olyan entitást beolvassa, amely kis (érték címkével), nem piros: 

```sql
select * from digitaltwins where not is_defined(tags.red) and tags.size = 'small' 
```

## <a name="next-steps"></a>További lépések

További információ a digitális kettős modellek tervezéséről és kezeléséről:
* [*Útmutató: egyéni modellek kezelése*](how-to-manage-model.md)

További információ a Twin Graph lekérdezéséről:
* [*Útmutató: a Twin gráf lekérdezése*](how-to-query-graph.md)
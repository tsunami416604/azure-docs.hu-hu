---
title: Alkalmazás sémájának definíciója
description: A LUIS-alkalmazás a vagy a vagy a, `.json` `.lu` és tartalmazza az összes szándékot, az entitásokat, például a hosszúságú kimondott szöveg, a funkciókat és a beállításokat.
ms.topic: reference
ms.date: 05/05/2020
ms.openlocfilehash: 21b58f79ffd2baf553c6f8b07daa84473e620f77
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599369"
---
# <a name="app-schema-definition"></a>Alkalmazás sémájának definíciója

A LUIS-alkalmazás a vagy a vagy a, `.json` `.lu` és tartalmazza az összes szándékot, az entitásokat, például a hosszúságú kimondott szöveg, a funkciókat és a beállításokat.

## <a name="format"></a>Formátum

Az alkalmazás importálásakor és exportálásakor válassza a vagy a lehetőséget `.json` `.lu` .

|Formátum|Információ|
|--|--|
|`.json`| Szabványos programozási formátum|
|`.lu`|A bot Framework [bot Builder eszközei](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown/docs/lu-file-format.md)támogatják.|

## <a name="version-7x"></a>7-es verzió. x

* A 7. x verzióra való áttéréskor az entitások beágyazott gépi megtanult entitásként jelennek meg.
* Beágyazott gépi megtanult entitások létrehozása `enableNestedChildren` a következő szerzői API-kkal:
    * [Címke hozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c08)
    * [Batch-címke hozzáadása](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c09)
    * [Címkék áttekintése](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c0a)
    * [Az entitások végponti lekérdezésének javaslata](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2e)
    * [Végponti lekérdezések javaslata a leképezésekhez](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2d)

```json
{
  "luis_schema_version": "7.0.0",
  "intents": [
    {
      "name": "None",
      "features": []
    }
  ],
  "entities": [],
  "hierarchicals": [],
  "composites": [],
  "closedLists": [],
  "prebuiltEntities": [],
  "utterances": [],
  "versionId": "0.1",
  "name": "example-app",
  "desc": "",
  "culture": "en-us",
  "tokenizerVersion": "1.0.0",
  "patternAnyEntities": [],
  "regex_entities": [],
  "phraselists": [],
  "regex_features": [],
  "patterns": [],
  "settings": []
}
```

## <a name="version-6x"></a>6-os verzió. x

* A 6. x verzióra való áttéréssel az új, [géppel megtanult entitást](reference-entity-machine-learned-entity.md) használhatja az entitások ábrázolásához.

```json
{
  "luis_schema_version": "6.0.0",
  "intents": [
    {
      "name": "None",
      "features": []
    }
  ],
  "entities": [],
  "hierarchicals": [],
  "composites": [],
  "closedLists": [],
  "prebuiltEntities": [],
  "utterances": [],
  "versionId": "0.1",
  "name": "example-app",
  "desc": "",
  "culture": "en-us",
  "tokenizerVersion": "1.0.0",
  "patternAnyEntities": [],
  "regex_entities": [],
  "phraselists": [],
  "regex_features": [],
  "patterns": [],
  "settings": []
}
```

## <a name="version-4x"></a>4. x verzió

```json
{
  "luis_schema_version": "4.0.0",
  "versionId": "0.1",
  "name": "example-app",
  "desc": "",
  "culture": "en-us",
  "tokenizerVersion": "1.0.0",
  "intents": [
    {
      "name": "None"
    }
  ],
  "entities": [],
  "composites": [],
  "closedLists": [],
  "patternAnyEntities": [],
  "regex_entities": [],
  "prebuiltEntities": [],
  "model_features": [],
  "regex_features": [],
  "patterns": [],
  "utterances": [],
  "settings": []
}
```

## <a name="next-steps"></a>További lépések

* Migrálás a [v3 authoring API](luis-migration-authoring-entities.md) -kra
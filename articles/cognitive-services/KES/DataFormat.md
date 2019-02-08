---
title: Adatformátum – Knowledge Exploration Service API
titlesuffix: Azure Cognitive Services
description: Ismerje meg az adatok formátumát az a Knowledge Exploration Service (KES) API.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: f17bc3c0588476fdc4f8414fb020b16718bf1d90
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55854795"
---
# <a name="data-format"></a>Adatformátum

Az adatfájlban az objektumok listájában, index ismerteti.
A fájl minden sorához meghatározza az objektum az attribútumértékek [JSON formátumban](http://json.org/) az UTF-8 kódolást.
A meghatározott attribútumok mellett a [séma](SchemaFormat.md), minden objektum rendelkezik egy "logprob" attribútumot, amely meghatározza az objektumok közötti relatív log valószínűségét.
A szolgáltatás objektumok valószínűség csökkenő sorrendben adja vissza, ha a "logprob" visszatérési sorrendjét, egyező objektumok is használjuk.
Adott valószínűség *p* 0 és 1 közötti megfelelő napló valószínűségét, log számított (*p*), ahol a log() a természetes logaritmusát függvény.
Ha nem ad meg értéket a logprob, az alapértelmezett érték 0 használatos.

```json
{"logprob":-5.3, "Title":"latent dirichlet allocation", "Year":2003, "Author":{"Name":"david m blei", "Affiliation":"uc berkeley"}, "Author":{"Name":"andrew y ng", "Affiliation":"stanford"}, "Author":{"Name":"michael i jordan", "Affiliation":"uc berkeley"}}
{"logprob":-6.1, "Title":"probabilistic latent semantic indexing", "Year":1999, "Author":{"Name":"thomas hofmann", "Affiliation":"uc berkeley"}}
...
```

Karakterlánc, a GUID Azonosítót és a Blob attribútumok az érték jelenik meg a határolójeles JSON-karakterláncot.  A numerikus attribútumokhoz (Int32, Int64, dupla) az érték jelenik meg egy JSON-szám.  Összetett attribútum értéke a JSON-objektum, amely meghatározza az alárendelt attribútumértékek.  A gyorsabb index buildek esetében presort log valószínűség csökkentésével az objektumok.

Általában egy attribútum 0 vagy több érték rendelkeznek.  Az attribútum nem tartalmaz értéket, ha a Microsoft egyszerűen cserélje a JSON-ból.  Ha attribútum 2 vagy több értéket tartalmaz, hogy ismételje meg a az attribútum-érték pár a JSON-objektumban.  Azt is megteheti hogy rendelhet hozzá a az attribútum több értéket tartalmazó JSON-tömböt.

```json
{"logprob":0, "Title":"0 keyword"}
{"logprob":0, "Title":"1 keyword", "Keyword":"foo"}
{"logprob":0, "Title":"2 keywords", "Keyword":"foo", "Keyword":"bar"}
{"logprob":0, "Title":"2 keywords (alt)", "Keyword":["foo", "bar"]}
```

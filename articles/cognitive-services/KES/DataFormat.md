---
title: A Tudásbázis feltárása Service API adatformátum |} Microsoft Docs
description: További tudnivalók az adatformátum a a Tudásbázis feltárása szolgáltatás (KES) API kognitív szolgáltatásban.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: a763505ac6458d68df74ae73e71029b81202ec8b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346935"
---
# <a name="data-format"></a>Adatok formátuma
Az adatfájl indexelésre objektum-lista ismerteti.
A fájlban minden sor határozza meg az objektumok attribútumértékek [JSON formátumban](http://json.org/) UTF-8 kódolással.
Az attribútumok meghatározott mellett a [séma](SchemaFormat.md), az egyes objektumok egy választható "logprob" attribútummal rendelkezik, amely meghatározza az objektumok között a relatív napló valószínűségét.
A szolgáltatás objektumok valószínűség csökkenő sorrendben adja vissza, ha a "logprob" visszatérési sorrendjét a megfelelő objektumokat is használjuk.
A valószínűségi megadott *p* 0 és 1 között, a napló számítható ki a megfelelő naplót valószínűség (*p*), ahol a log() a természetes log függvény.
Ha nincs érték megadva az logprob, az alapértelmezett érték 0 szolgál.

```json
{"logprob":-5.3, "Title":"latent dirichlet allocation", "Year":2003, "Author":{"Name":"david m blei", "Affiliation":"uc berkeley"}, "Author":{"Name":"andrew y ng", "Affiliation":"stanford"}, "Author":{"Name":"michael i jordan", "Affiliation":"uc berkeley"}}
{"logprob":-6.1, "Title":"probabilistic latent semantic indexing", "Year":1999, "Author":{"Name":"thomas hofmann", "Affiliation":"uc berkeley"}}
...
```

Karakterlánc, a GUID Azonosítót és a Blob attribútumokat a rendszer helyőrzővel jelzett Eredménymező értéke JSON idézőjelek közé zárt karakterlánc.  A numerikus attribútumokhoz (Int32, Int64, dupla) a rendszer helyőrzővel jelzett Eredménymező értéke egy JSON-számot.  Összetett attribútumok értéke egy JSON-objektum, amely meghatározza az alárendelt attribútumértékek.  A gyorsabb index buildek presort napló valószínűség csökkentik az objektumok.

Általában a attribútum 0 vagy több érték lehet.  Az attribútum nem rendelkezik értékkel, ha azt egyszerűen eltávolíthatja azt az a JSON-NÁ.  Ha egy attribútum 2 vagy több értékkel rendelkezik, azt megismétlésével a attribútum-érték pár a JSON-objektumból.  Azt is megteheti azt az attribútumot a több értéket tartalmazó JSON-tömb lehet társítani.

```json
{"logprob":0, "Title":"0 keyword"}
{"logprob":0, "Title":"1 keyword", "Keyword":"foo"}
{"logprob":0, "Title":"2 keywords", "Keyword":"foo", "Keyword":"bar"}
{"logprob":0, "Title":"2 keywords (alt)", "Keyword":["foo", "bar"]}
```

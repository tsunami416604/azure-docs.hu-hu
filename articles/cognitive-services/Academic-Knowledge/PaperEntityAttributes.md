---
title: Papír entitás attribútumai – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Megtudhatja, hogy mely attribútumok használhatók a Academic Knowledge API papír entitásával.
services: cognitive-services
author: DarrinEide
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: 1d16668e2c0f52c0824016c977251e64c800c54d
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161732"
---
# <a name="paper-entity"></a>Papír entitás

<sub>* Az alábbi attribútumok a papír entitásra jellemzőek. (Ty = "0")</sub>

Név | Leírás | Type (Típus) | Műveletek
--- | --- | --- | ---
AA. AfId | Szerzői kapcsolat azonosítója | Int64 | Egyenlő
AA. AfN | Szerzői kapcsolat neve | Sztring | Egyenlő, StartsWith
AA. AuId | Szerző azonosítója | Int64 | Egyenlő
AA. AuN | Normalizált szerző neve | Sztring | Egyenlő, StartsWith
AA. DAuN | Eredeti szerző neve | Sztring | None
AA. DAfN | Eredeti kapcsolat neve | Sztring | None
AA. S | Numerikus pozíció a szerző listában | Int32 | Egyenlő
CC | Idézetek száma | Int32 | None  
C. CId | Konferencia-sorozat azonosítója | Int64 | Egyenlő
C.CN | Konferencia adatsorozatának neve | Sztring | Egyenlő, StartsWith
D | Közzététel dátuma éééé-hh-nn formátumban | Dátum | Egyenlő, IsBetween
E | Kiterjesztett metaadatok (lásd az alábbi táblázatot) | Sztring | –  
ECC | Becsült idézetek száma | Int32 | None
F. DFN | A tanulmány nevének eredeti mezője | Sztring | None
F. FId | Tanulmányi azonosító mező | Int64 | Egyenlő
F. FN | A tanulmány neve normalizált mező | Sztring | Egyenlő, StartsWith
Azonosító | Papír azonosítója | Int64 | Egyenlő
J. JId | Napló azonosítója | Int64 | Egyenlő
J. JN | Napló neve | Sztring | Egyenlő, StartsWith
PT | Kiadvány típusa (0: ismeretlen, 1: Journal-cikk, 2: szabadalom, 3: konferencia papír, 4: könyv fejezet, 5: könyv, 6: könyv hivatkozási bejegyzés, 7: adatkészlet, 8: adattár | Sztring | Egyenlő
RId | Hivatkozott papír-azonosítók listája | Int64 [] | Egyenlő
Ti | Normalizált cím | Sztring | Egyenlő, StartsWith
W | Egyedi szavak a címben | Karakterlánc [] | Egyenlő
I | Év közzététele | Int32 | Egyenlő, IsBetween

## <a name="extended-metadata-attributes"></a>Bővített metaadatok attribútumai ##

Név | Leírás               
--- | ---
BT | BibTex dokumentumtípus ("a": Journal-cikk, "b": könyv, "c": könyv fejezet, "p": konferencia-könyv)
BV | BibTex-helyszín neve
CC | Idézetek kontextusai – a hivatkozott papír AZONOSÍTÓjának és a dokumentum megfelelő környezetének listája (például [{123: ["Brown rókák ismertek a 123-es papíron hivatkozott jumping-szövegekben", "a lusta kutyák a korábbi, a 123-es papíron látható"]})
DN | Eredeti papír címe
DOI | Digitális objektum azonosítója
FP | A dokumentum első lapja a kiadványban
I | Közzétételi probléma
IA | Fordított absztrakt
IA. IndexLength | Az indexben lévő elemek száma (absztrakt szavak száma)
IA. InvertedIndex | Az absztrakt szavak és a hozzájuk tartozó pozíciók listája az eredeti absztraktban (például [{"": [0, 15, 30]}, {"Brown": [1]}, {"Fox": [2]}])
. LP | A dokumentum utolsó lapja a kiadványban
PB | Gyártó/kiadó
S | Források – a papír webes forrásainak listája, a statikus rangsor szerint rendezve
S. Ty | Forrás típusa (1: HTML, 2: Text, 3: PDF, 4: DOC, 5: PPT, 6: XLS, 7: PS)
S. U | Forrás URL-címe
V | Kiadvány kötete
VFN | A napló vagy a konferencia helyszínének teljes neve
VSN | A napló vagy a konferencia helyszínének rövid neve

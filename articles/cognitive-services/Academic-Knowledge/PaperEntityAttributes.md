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
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: fc3bb5987c31fe718951a3cae02ed7c4ddc29957
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123078"
---
# <a name="paper-entity"></a>Papír entitás

> [!NOTE]
> Az alábbi attribútumok a papír entitásra jellemzőek. (Ty = "0")

Name (Név) | Leírás | Típus | Műveletek
--- | --- | --- | ---
AA.AfId | Szerzői kapcsolat azonosítója | Int64 | Egyenlő
AA.AfN | Szerzői kapcsolat neve | Sztring | Egyenlő, StartsWith
AA.AuId | Szerző azonosítója | Int64 | Egyenlő
AA. AuN | Normalizált szerző neve | Sztring | Egyenlő, StartsWith
AA. DAuN | Eredeti szerző neve | Sztring | Nincs
AA. DAfN | Eredeti kapcsolat neve | Sztring | Nincs
AA.S | Numerikus pozíció a szerző listában | Int32 | Egyenlő
BT | BibTex dokumentumtípus ("a": Journal-cikk, "b": könyv, "c": könyv fejezet, "p": konferencia-könyv) | Sztring | Nincs
BV | BibTex-helyszín neve | Sztring | Nincs
C.CId | Konferencia-sorozat azonosítója | Int64 | Egyenlő
C.CN | Konferencia adatsorozatának neve | Sztring | Egyenlő, StartsWith
CC | Idézetek száma | Int32 | Nincs  
CitCon | Idézetek kontextusai</br></br>A hivatkozott papír AZONOSÍTÓjának és a dokumentum megfelelő környezetének (például [{123: ["Brown rókák", az 123-es papíron hivatkozott jumping-nek a neve "," a lusta 123 kutyák a " | Egyéni | Nincs
D | Közzététel dátuma éééé-hh-nn formátumban | Dátum | Egyenlő, IsBetween
DN | Eredeti papír címe | Sztring | Nincs
DOI | Digitális objektum azonosítója | Sztring | Egyenlő, StartsWith
E | Kiterjesztett metaadatok</br></br>**Fontos**: ez az attribútum elavult, és csak örökölt alkalmazások esetében támogatott. Ha ezt az attribútumot külön kéri (például attribútumok = azonosító, ti, E), a rendszer az összes kibővített metaadat-attribútumot visszaadja egy *szerializált JSON-karakterláncban* .</br></br>A kiterjesztett metaadatokban szereplő összes attribútum mostantól legfelső szintű attribútumként érhető el, és a következő módon kérhető le: (pl. attribútumok = azonosító, ti, DOI, IA) | [Kiterjesztett](#extended) | Nincs
ECC | Becsült idézetek száma | Int32 | Nincs
F. DFN | A tanulmány nevének eredeti mezője | Sztring | Nincs
F.FId | Tanulmányi azonosító mező | Int64 | Egyenlő
F. FN | A tanulmány neve normalizált mező | Sztring | Egyenlő, StartsWith
FP | A dokumentum első lapja a kiadványban | Sztring | Egyenlő
I | Közzétételi probléma | Sztring | Egyenlő
IA | Fordított absztrakt | [InvertedAbstract](#invertedabstract) | Nincs
Azonosító | Papír azonosítója | Int64 | Egyenlő
J. JId | Napló azonosítója | Int64 | Egyenlő
J. JN | Napló neve | Sztring | Egyenlő, StartsWith
. LP | A dokumentum utolsó lapja a kiadványban | Sztring | Egyenlő
PB | Közzétevő | Sztring | Nincs
PT | Kiadvány típusa (0: ismeretlen, 1: Journal-cikk, 2: szabadalom, 3: konferencia papír, 4: könyv fejezet, 5: könyv, 6: könyv hivatkozási bejegyzés, 7: adatkészlet, 8: adattár | Sztring | Egyenlő
RId | Hivatkozott papír-azonosítók listája | Int64 [] | Egyenlő
S | A papír URL-címeinek listája relevancia szerint rendezve | [Forrás](#source)[] | Nincs
Ti | Normalizált cím | Sztring | Egyenlő, StartsWith
V | Kiadvány kötete | Sztring | Egyenlő
VFN | A napló vagy a konferencia helyszínének teljes neve | Sztring | Nincs
VSN | A napló vagy a konferencia helyszínének rövid neve | Sztring | Nincs
W | Egyedi szavak a címben | Karakterlánc [] | Egyenlő
I | Év közzététele | Int32 | Egyenlő, IsBetween

## <a name="extended"></a>Kiterjesztett
> [!IMPORTANT]
> Ez az attribútum elavult, és csak örökölt alkalmazások esetében támogatott. Ha ezt az attribútumot külön kéri (például attribútumok = azonosító, ti, E), a rendszer az összes kibővített metaadat-attribútumot visszaadja egy *szerializált JSON-karakterláncban* .</br></br>A kiterjesztett metaadatokban szereplő összes attribútum mostantól legfelső szintű attribútumként érhető el, és a következő módon kérhető le: (pl. attribútumok = azonosító, ti, DOI, IA)

> [!IMPORTANT]
> Egyéni kiterjesztett attribútumok kérésének támogatása az "E" használatával a hatókör, azaz az "E. DN" elavult. Habár ez továbbra is technikailag támogatott, az "E" használatával az egyes kiterjesztett attribútumokat is kéri. a hatókör azt eredményezi, hogy a rendszer az attribútum értékét a JSON-válasz két helyén adja vissza, az "E" objektum és a legfelső szintű attribútum részeként.

Name (Név) | Leírás | Típus | Műveletek
--- | --- | --- | ---
BT | BibTex dokumentumtípus ("a": Journal-cikk, "b": könyv, "c": könyv fejezet, "p": konferencia-könyv) | Sztring | Nincs
BV | BibTex-helyszín neve | Sztring | Nincs
CC | Idézetek kontextusai</br></br>A hivatkozott papír AZONOSÍTÓjának és a dokumentum megfelelő környezetének (például [{123: ["Brown rókák", az 123-es papíron hivatkozott jumping-nek a neve "," a lusta 123 kutyák a " | Egyéni | Nincs
DN | Eredeti papír címe | Sztring | Nincs
DOI | Digitális objektum azonosítója | Sztring | Nincs
FP | A dokumentum első lapja a kiadványban | Sztring | Nincs
I | Közzétételi probléma | Sztring | Nincs
IA | Fordított absztrakt | [InvertedAbstract](#invertedabstract) | Nincs
. LP | A dokumentum utolsó lapja a kiadványban | Sztring | Nincs
PB | Közzétevő | Sztring | Nincs
S | A papír URL-címeinek listája relevancia szerint rendezve | [Forrás](#source)[] | Nincs
V | Kiadvány kötete | Sztring | Nincs
VFN | A napló vagy a konferencia helyszínének teljes neve | Sztring | Nincs
VSN | A napló vagy a konferencia helyszínének rövid neve | Sztring | Nincs

## <a name="invertedabstract"></a>InvertedAbstract

> [!IMPORTANT]
> A InvertedAbstract attribútumokat nem lehet közvetlenül visszaküldési attribútumként kérni. Ha egyéni attribútumra van szüksége, a legfelső szintű "IA" attribútumot kell megadnia, azaz a következőt: IA. IndexLength-kérelem attribútumai = IA

Name (Név) | Leírás | Típus | Műveletek
--- | --- | --- | ---
IndexLength | Az indexben lévő elemek száma (absztrakt szavak száma) | Int32 | Nincs
InvertedIndex | Az absztrakt szavak és a hozzájuk tartozó pozíciók listája az eredeti absztraktban (például [{"": [0, 15, 30]}, {"Brown": [1]}, {"Fox": [2]}]) | Egyéni | Nincs

## <a name="source"></a>Forrás

> [!IMPORTANT]
> A forrás attribútumai nem igényelhetők vissza attribútumként. Ha egyéni attribútumra van szüksége, akkor a legfelső szintű "S" attribútumot kell megadnia, azaz az S. U kérelem attribútumait kell kérnie = S

Name (Név) | Leírás | Típus | Műveletek
--- | --- | --- | ---
Úlyossága | Forrás URL-cím típusa (1: HTML, 2: Text, 3: PDF, 4: DOC, 5: PPT, 6: XLS, 7: PS) | Sztring | Nincs
U | Forrás URL-címe | Sztring | Nincs

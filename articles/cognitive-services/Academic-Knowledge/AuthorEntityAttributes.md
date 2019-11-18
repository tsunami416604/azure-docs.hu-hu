---
title: Szerzői entitás attribútumai – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: A Academic Knowledge API szerző entitásával használható attribútumok megismerése.
services: cognitive-services
author: darrine
manager: kuansanw
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: d5fc770c380397f605f8810fa41d3a8907f2358e
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74146492"
---
# <a name="author-entity"></a>Szerzői entitás

> [!NOTE]
> A következő attribútumok a szerző entitásra jellemzőek. (Ty = "1")

Name (Név) | Leírás | Típus | Műveletek
--- | --- | --- | ---
Azonosító      | Entitás azonosítója                             |Int64      |Egyenlő
AuN     | Normalizált név szerzője                    |Sztring     |Egyenlő
CC      | Szerzői teljes idézetek száma           |Int32      |Nincs  
DAuN    | Szerző megjelenítendő neve                   |Sztring     |Nincs
E | Kiterjesztett metaadatok</br></br>**Fontos**: ez az attribútum elavult, és csak örökölt alkalmazások esetében támogatott. Ha ezt az attribútumot külön kéri (például attribútumok = azonosító, ti, E), a rendszer az összes kibővített metaadat-attribútumot visszaadja egy *szerializált JSON-karakterláncban* .</br></br>A kiterjesztett metaadatokban szereplő összes attribútum mostantól legfelső szintű attribútumként érhető el, és a következő módon kérhető le: (pl. attribútumok = azonosító, ti, DOI, IA) | [Kiterjesztett](#extended) | Nincs
ECC     | Szerzői teljes becsült idézetek száma |Int32      |Nincs
LKA.AfId | A szerzőhöz tartozó utolsó ismert kapcsolat entitásának azonosítója | Int64 | Nincs
LKA. AfN | A szerzőhöz tartozó utolsó ismert kapcsolat normalizált neve | Sztring | Nincs
PC | Szerző teljes példányszáma | Int32 | Nincs

## <a name="extended"></a>Kiterjesztett

> [!IMPORTANT]
> Ez az attribútum elavult, és csak örökölt alkalmazások esetében támogatott. Ha ezt az attribútumot külön kéri (például attribútumok = azonosító, ti, E), a rendszer az összes kibővített metaadat-attribútumot visszaadja egy *szerializált JSON-karakterláncban* .</br></br>A kiterjesztett metaadatokban szereplő összes attribútum mostantól legfelső szintű attribútumként érhető el, és a következő módon kérhető le: (pl. attribútumok = azonosító, ti, DOI, IA)

> [!IMPORTANT]
> Egyéni kiterjesztett attribútumok kérésének támogatása az "E" használatával a hatókör, azaz az "E. DN" elavult. Habár ez továbbra is technikailag támogatott, az "E" használatával az egyes kiterjesztett attribútumokat is kéri. a hatókör azt eredményezi, hogy a rendszer az attribútum értékét a JSON-válasz két helyén adja vissza, az "E" objektum és a legfelső szintű attribútum részeként.

Name (Név) | Leírás | Típus | Műveletek
--- | --- | --- | ---
LKA.AfId | A szerzőhöz tartozó utolsó ismert kapcsolat entitásának azonosítója | Int64 | Nincs
LKA. AfN | A szerzőhöz tartozó utolsó ismert kapcsolat normalizált neve | Sztring | Nincs
PC | Szerző teljes példányszáma | Int32 | Nincs

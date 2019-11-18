---
title: Konferencia-példány entitás attribútumai – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Megtudhatja, hogy mely attribútumok használhatók a Academic Knowledge API konferencia-példány entitásával.
services: cognitive-services
author: darrine
manager: kuansanw
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: c6761206a58724dae96d9dc6f6234658892d4d18
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74146561"
---
# <a name="conference-instance-entity"></a>Konferencia-példány entitása

> [!NOTE]
> A következő attribútumok a konferencia példány entitására vonatkoznak. (Ty = "4")

Name (Név) | Leírás | Típus | Műveletek
--- | --- | --- | ---
CC      |Konferencia-példányok teljes idézetek száma           |Int32      |Nincs  
CD. D    |Konferencia-példány eseményének dátuma    |Dátum       |Egyenlő, IsBetween
CD. T    |Konferencia-példány eseményének címe   |Dátum       |Egyenlő, IsBetween
CIARD   |A konferencia-példány absztrakt regisztrációjának határideje  |Dátum       |Egyenlő, IsBetween
CIED    |A konferencia példányának befejező dátuma    |Dátum       |Egyenlő, IsBetween
CIFVD   |A konferencia példányának végleges verziójának határideje  |Dátum       |Egyenlő, IsBetween
CIL     |A konferencia-példány helye    |Sztring     |Egyenlő, StartsWith
CIN     |Konferencia-példány normalizált neve |Sztring        |Egyenlő
CINDD   |A konferencia-példány értesítési dátuma   |Dátum       |Egyenlő, IsBetween
CISD    |A konferencia példányának kezdési dátuma  |Dátum       |Egyenlő, IsBetween
CISDD   |A konferencia-példány beküldésének határideje     |Dátum       |Egyenlő, IsBetween
DCN     |Konferencia-példány megjelenítendő neve  |Sztring      |Nincs
E | Kiterjesztett metaadatok</br></br>**Fontos**: ez az attribútum elavult, és csak örökölt alkalmazások esetében támogatott. Ha ezt az attribútumot külön kéri (például attribútumok = azonosító, ti, E), a rendszer az összes kibővített metaadat-attribútumot visszaadja egy *szerializált JSON-karakterláncban* .</br></br>A kiterjesztett metaadatokban szereplő összes attribútum mostantól legfelső szintű attribútumként érhető el, és a következő módon kérhető le: (pl. attribútumok = azonosító, ti, DOI, IA) | [Kiterjesztett](#extended) | Nincs
ECC     |Konferencia-példány teljes becsült idézetek száma |Int32      |Nincs
FN | Konferencia-példány teljes neve | Sztring | Nincs
Azonosító      |Entitás azonosítója                              |Int64      |Egyenlő
PC | Konferencia-példány teljes közzétételének száma | Int32 | Nincs
PCS.CN  |A példány fölérendelt konferencia-adatsorozatának neve |Sztring  |Egyenlő
PCS.CId |A példány fölérendelt konferencia-sorozatának azonosítója |Int64     |Egyenlő

## <a name="extended"></a>Kiterjesztett

> [!IMPORTANT]
> Ez az attribútum elavult, és csak örökölt alkalmazások esetében támogatott. Ha ezt az attribútumot külön kéri (például attribútumok = azonosító, ti, E), a rendszer az összes kibővített metaadat-attribútumot visszaadja egy *szerializált JSON-karakterláncban* .</br></br>A kiterjesztett metaadatokban szereplő összes attribútum mostantól legfelső szintű attribútumként érhető el, és a következő módon kérhető le: (pl. attribútumok = azonosító, ti, DOI, IA)

> [!IMPORTANT]
> Egyéni kiterjesztett attribútumok kérésének támogatása az "E" használatával a hatókör, azaz az "E. DN" elavult. Habár ez továbbra is technikailag támogatott, az "E" használatával az egyes kiterjesztett attribútumokat is kéri. a hatókör azt eredményezi, hogy a rendszer az attribútum értékét a JSON-válasz két helyén adja vissza, az "E" objektum és a legfelső szintű attribútum részeként.

Name (Név) | Leírás | Típus | Műveletek
--- | --- | --- | ---
FN | Konferencia-példány teljes neve | Sztring | Nincs
PC | Konferencia-példány teljes közzétételének száma | Int32 | Nincs

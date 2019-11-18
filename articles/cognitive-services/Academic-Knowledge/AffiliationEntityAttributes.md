---
title: Kapcsolati entitás attribútumai a Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Megtudhatja, hogy mely attribútumok használhatók a Academic Knowledge API a kapcsolati entitással.
services: cognitive-services
author: darrine
manager: kuansanw
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: 52dcccebe1a1cbab7a6afadeb6b543b34b8b1eb7
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74143936"
---
# <a name="affiliation-entity"></a>Kapcsolati entitás

> [!NOTE]
> A következő attribútumok a kapcsolati entitásra vonatkoznak. (Ty = "5")

Name (Név) | Leírás | Típus | Műveletek
--- | --- | --- | ---
AfN | Kapcsolat normalizált neve |Sztring |Egyenlő
CC | Kapcsolatok teljes bibliográfiai száma |Int32        |Nincs  
DAfN | Kapcsolat megjelenített neve |Sztring |Nincs
E | Kiterjesztett metaadatok</br></br>**Fontos**: ez az attribútum elavult, és csak örökölt alkalmazások esetében támogatott. Ha ezt az attribútumot külön kéri (például attribútumok = azonosító, ti, E), a rendszer az összes kibővített metaadat-attribútumot visszaadja egy *szerializált JSON-karakterláncban* .</br></br>A kiterjesztett metaadatokban szereplő összes attribútum mostantól legfelső szintű attribútumként érhető el, és a következő módon kérhető le: (pl. attribútumok = azonosító, ti, DOI, IA) | [Kiterjesztett](#extended) | Nincs
ECC | Kapcsolat teljes becsült idézetek száma |Int32 |Nincs
Azonosító | Entitás azonosítója |Int64 |Egyenlő
PC | Az entitással való tagsággal írt kiadványok teljes száma | Int32 | Nincs

## <a name="extended"></a>Kiterjesztett

> [!IMPORTANT]
> Ez az attribútum elavult, és csak örökölt alkalmazások esetében támogatott. Ha ezt az attribútumot külön kéri (például attribútumok = azonosító, ti, E), a rendszer az összes kibővített metaadat-attribútumot visszaadja egy *szerializált JSON-karakterláncban* .</br></br>A kiterjesztett metaadatokban szereplő összes attribútum mostantól legfelső szintű attribútumként érhető el, és a következő módon kérhető le: (pl. attribútumok = azonosító, ti, DOI, IA)

> [!IMPORTANT]
> Egyéni kiterjesztett attribútumok kérésének támogatása az "E" használatával a hatókör, azaz az "E. DN" elavult. Habár ez továbbra is technikailag támogatott, az "E" használatával az egyes kiterjesztett attribútumokat is kéri. a hatókör azt eredményezi, hogy a rendszer az attribútum értékét a JSON-válasz két helyén adja vissza, az "E" objektum és a legfelső szintű attribútum részeként.

Name (Név) | Leírás | Típus | Műveletek
--- | --- | --- | ---
PC | Az entitással való tagsággal írt kiadványok teljes száma | Int32 | Nincs

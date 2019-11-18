---
title: Tanulmányi entitás attribútumainak mezője – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: A Academic Knowledge API tanulmányi entitás mezőjében használható attribútumok megismerése.
services: cognitive-services
author: darrine
manager: kuansanw
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: b6bb5e0fd822541a0a4c10ef5f275d46664f8e10
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74146473"
---
# <a name="field-of-study-entity"></a>Tanulmányi entitás mezője

> [!NOTE]
> A következő attribútumok a tanulmányi entitások mezőjére jellemzőek. (Ty = "6")

Name (Név) | Leírás | Típus | Műveletek
--- | --- | --- | ---
CC      |A tanulmány teljes bibliográfiai számának mezője    |Int32      |Nincs  
DFN     |A tanulmány megjelenítendő neve            |Sztring     |Nincs
ECC     |A becsült idézetek összesített számának mezője|Int32      |Nincs
FL      |Szint a tanulmányi hierarchia mezőiben     |Int32      |Egyenlő, IsBetween
FN      |A vizsgálati mező normalizált neve         |Sztring     |Egyenlő
FC.FId  |Tanulmányi azonosító gyermek mezője                |Int64      |Egyenlő
FC. FN   |Tanulmány neve gyermek mező              |Sztring     |Egyenlő
FP.FId  |Tanulmányi azonosító szülő mezője               |Int64      |Egyenlő
FP. FN   |Tanulmány neve szülő mező             |Sztring     |Egyenlő
Azonosító      |Entitás azonosítója                              |Int64      |Egyenlő
PC    | A tanulmány teljes példányszáma | Int32 | Nincs

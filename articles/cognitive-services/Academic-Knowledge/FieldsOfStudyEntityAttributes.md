---
title: Tanulmányi entitás attribútumainak mezője – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: A Academic Knowledge API tanulmányi entitás mezőjében használható attribútumok megismerése.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: d54f3c72462e6702b09068092b7c18ea50f12048
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704999"
---
# <a name="field-of-study-entity"></a>Tanulmányi entitás mezője

<sub>* A következő attribútumok a tanulmányi entitások mezőjére jellemzőek. (Ty = "6")</sub>

Name (Név)    |Leírás                            |Type       | Műveletek
------- | ------------------------------------- | --------- | ----------------------------
Id      |Entitásazonosító                              |Int64      |Egyenlő
FN      |A vizsgálati mező normalizált neve         |Sztring     |Egyenlő
DFN     |A tanulmány megjelenítendő neve            |Sztring     |nincs
Másolat      |A tanulmány teljes bibliográfiai számának mezője    |Int32      |nincs  
ECC     |A becsült idézetek összesített számának mezője|Int32      |nincs
FL      |Szint a tanulmányi hierarchia mezőiben     |Int32      |Egyenlő <br/>IsBetween
FP. FN   |Tanulmány neve szülő mező             |Sztring     |Egyenlő
FP.FId  |Tanulmányi azonosító szülő mezője               |Int64      |Egyenlő
FC. FN   |Tanulmány neve gyermek mező              |Sztring     |Egyenlő
FC.FId  |Tanulmányi azonosító gyermek mezője                |Int64      |Egyenlő

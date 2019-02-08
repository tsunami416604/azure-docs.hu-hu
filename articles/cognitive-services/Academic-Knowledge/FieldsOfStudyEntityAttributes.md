---
title: A tanulmány tevékenységentitás-attribútumok – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Ismerje meg, hogy az attribútumok a tanulmány az entitás az Academic Knowledge API használható.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: alch
ms.openlocfilehash: e9d6badf76efd03c0520a728af7b3e47b25f200a
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55878952"
---
# <a name="field-of-study-entity"></a>Tanulmány entitás mezője

<sub> * A következő attribútumok tanulmány entitás mezője jellemzőek. (Folyó évi = "6") </sub>

Name (Név)    |Leírás                            |Typo       | Műveletek
------- | ------------------------------------- | --------- | ----------------------------
Azonosító      |Entitásazonosító                              |Int64      |Egyenlő
FN      |Tanulmány normalizált név mező         |String     |Egyenlő
DFN     |Tanulmány megjelenítendő név mezőt            |String     |nincs
Másolat      |A mező tanulmány idézetet teljes száma    |Int32      |nincs  
ECC     |A mező teljes becsült idézetet száma|Int32      |nincs
FL      |Mezők tanulmány hierarchia szintű     |Int32      |Egyenlő, <br/>IsBetween
FP.FN   |Tanulmány név mező             |String     |Egyenlő
FP.FId  |Tanulmány azonosító mező               |Int64      |Egyenlő
FC.FN   |Gyermek mező tanulmány neve              |String     |Egyenlő
FC.FId  |Gyermek tanulmány azonosító mezője                |Int64      |Egyenlő

---
title: A tanulmány tevékenységentitás-attribútumok – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Ismerje meg, hogy az attribútumok a tanulmány az entitás az Academic Knowledge API használható.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: alch
ms.openlocfilehash: 862fd6d506d5f1ca6f7f532f80f53a29200f33db
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900427"
---
# <a name="field-of-study-entity"></a>Tanulmány entitás mezője

<sub> * A következő attribútumok tanulmány entitás mezője jellemzőek. (Folyó évi = "6") </sub>

Name (Név)    |Leírás                            |Típus       | Műveletek
------- | ------------------------------------- | --------- | ----------------------------
Azonosító      |Entitásazonosító                              |Int64      |Egyenlő
FN      |Tanulmány normalizált név mező         |Sztring     |Egyenlő
DFN     |Tanulmány megjelenítendő név mezőt            |Sztring     |nincs
Másolat      |A mező tanulmány idézetet teljes száma    |Int32      |nincs  
ECC     |A mező teljes becsült idézetet száma|Int32      |nincs
FL      |Mezők tanulmány hierarchia szintű     |Int32      |Egyenlő, <br/>IsBetween
PI. FN   |Tanulmány név mező             |Sztring     |Egyenlő
PI. FId  |Tanulmány azonosító mező               |Int64      |Egyenlő
FC. FN   |Gyermek mező tanulmány neve              |Sztring     |Egyenlő
FC. FId  |Gyermek tanulmány azonosító mezője                |Int64      |Egyenlő
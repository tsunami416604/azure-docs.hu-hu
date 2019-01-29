---
title: A tanulmány tevékenységentitás-attribútumok – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Ismerje meg, hogy az attribútumok a tanulmány az entitás az Academic Knowledge API használható.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: alch
ms.openlocfilehash: 793b35d9c6412c40a87f3f91fcd772476d57584f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55154567"
---
# <a name="field-of-study-entity"></a>Tanulmány entitás mezője

<sub> * A következő attribútumok tanulmány entitás mezője jellemzőek. (Folyó évi = "6") </sub>

Name (Név)    |Leírás                            |Típus       | Műveletek
------- | ------------------------------------- | --------- | ----------------------------
Azonosító      |Entitásazonosító                              |Int64      |Egyenlő
FN      |Tanulmány normalizált név mező         |Karakterlánc     |Egyenlő
DFN     |Tanulmány megjelenítendő név mezőt            |Karakterlánc     |nincs
Másolat      |A mező tanulmány idézetet teljes száma    |Int32      |nincs  
ECC     |A mező teljes becsült idézetet száma|Int32      |nincs
FL      |Mezők tanulmány hierarchia szintű     |Int32      |Egyenlő, <br/>IsBetween
FP.FN   |Tanulmány név mező             |Karakterlánc     |Egyenlő
FP.FId  |Tanulmány azonosító mező               |Int64      |Egyenlő
FC.FN   |Gyermek mező tanulmány neve              |Karakterlánc     |Egyenlő
FC.FId  |Gyermek tanulmány azonosító mezője                |Int64      |Egyenlő

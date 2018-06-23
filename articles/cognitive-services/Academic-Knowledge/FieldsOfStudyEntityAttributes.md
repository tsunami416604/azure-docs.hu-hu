---
title: A tanulmány entitásattribútumhoz Academic Knowledge API-ban |} Microsoft Docs
description: Ismerje meg, az attribútumokat, a mező a vizsgálat entitás kognitív szolgáltatásban a Academic Knowledge API használatával.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/31/2017
ms.author: alch
ms.openlocfilehash: a8176370f5b2f63b7741f7e892ed5a8c775f19c1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346858"
---
# <a name="field-of-study-entity"></a>Vizsgálat entitás mezője

<sub> * A következő attribútumok használhatók vizsgálat entitás mezője. (Most = "6") </sub>

Name (Név)    |Leírás                            |Típus       | Műveletek
------- | ------------------------------------- | --------- | ----------------------------
Azonosító      |Entitásazonosító                              |Int64      |Egyenlő
FN      |A vizsgálat normalizált név mező         |Sztring     |Egyenlő
DFN     |Vizsgálat megjelenített név mezője            |Sztring     |nincs
Másolat      |A mező vizsgálat teljes hivatkozás száma    |Int32      |nincs  
ECC     |A mező teljes becsült hivatkozás száma|Int32      |nincs
FL      |A vizsgálat hierarchia mezők szinten     |Int32      |Egyenlő, <br/>IsBetween
PI. FN   |A vizsgálat név mező             |Sztring     |Egyenlő
PI. FId  |Vizsgálat azonosító mező               |Int64      |Egyenlő
FC. FN   |A vizsgálat név gyermek mező              |Sztring     |Egyenlő
FC. FId  |Gyermek mező vizsgálat azonosító                |Int64      |Egyenlő
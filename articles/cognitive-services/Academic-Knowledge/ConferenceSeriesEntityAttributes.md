---
title: Konferencia sorozat tevékenységentitás-attribútumok – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Ismerje meg az attribútumok a Konferenciasorozat entitással is használhatja.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: ff71b489cce01d8d6ea29e09905d7d3ac8429e34
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55155601"
---
# <a name="conference-series-entity"></a>Konferencia sorozat entitás

<sub> * A következő attribútumok konferencia sorozat entitás jellemzőek. (Folyó évi = "3") </sub>

Name (Név)    |Leírás                            |Typo       | Műveletek
------- | ------------------------------------- | --------- | ----------------------------
Azonosító      |Entitásazonosító                              |Int64      |Egyenlő
CN      |Konferenciasorozat normalized neve      |Sztring     |Egyenlő
DCN     |Konferencia sorozat megjelenített neve         |Sztring     |nincs
Másolatot kap      |Konferenciasorozat idézetet száma összesen         |Int32      |nincs  
ECC     |Konferencia sorozat becsült idézetet teljes száma   |Int32      |nincs
F.FId   |A konferenciasorozat társított tanulmány Entitásazonosító mezője |Int64  | Egyenlő
F.FN    |A konferenciasorozat társított tanulmány név mező  | Egyenlő,<br/>StartsWith

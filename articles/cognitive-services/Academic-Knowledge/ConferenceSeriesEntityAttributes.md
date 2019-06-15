---
title: Konferencia sorozat tevékenységentitás-attribútumok – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Ismerje meg az attribútumok a Konferenciasorozat entitással is használhatja.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 38b4aa4c899668a68041f042ce6981ddd8c58219
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "61340196"
---
# <a name="conference-series-entity"></a>Konferencia sorozat entitás

<sub> * A következő attribútumok konferencia sorozat entitás jellemzőek. (Folyó évi = "3") </sub>

Name (Név)    |Leírás                            |Típus       | Műveletek
------- | ------------------------------------- | --------- | ----------------------------
Azonosító      |Entitásazonosító                              |Int64      |Egyenlő
CN      |Konferenciasorozat normalized neve      |String     |Egyenlő
DCN     |Konferencia sorozat megjelenített neve         |String     |Egyik sem
CC      |Konferenciasorozat idézetet száma összesen         |Int32      |Egyik sem  
ECC     |Konferencia sorozat becsült idézetet teljes száma   |Int32      |Egyik sem
F.FId   |A konferenciasorozat társított tanulmány Entitásazonosító mezője |Int64  | Egyenlő
F.FN    |A konferenciasorozat társított tanulmány név mező  | Egyenlő,<br/>StartsWith

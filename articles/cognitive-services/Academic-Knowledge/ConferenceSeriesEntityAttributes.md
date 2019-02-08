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
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55884919"
---
# <a name="conference-series-entity"></a>Konferencia sorozat entitás

<sub> * A következő attribútumok konferencia sorozat entitás jellemzőek. (Folyó évi = "3") </sub>

Name (Név)    |Leírás                            |Typo       | Műveletek
------- | ------------------------------------- | --------- | ----------------------------
Azonosító      |Entitásazonosító                              |Int64      |Egyenlő
CN      |Konferenciasorozat normalized neve      |String     |Egyenlő
DCN     |Konferencia sorozat megjelenített neve         |String     |nincs
Másolat      |Konferenciasorozat idézetet száma összesen         |Int32      |nincs  
ECC     |Konferencia sorozat becsült idézetet teljes száma   |Int32      |nincs
F.FId   |A konferenciasorozat társított tanulmány Entitásazonosító mezője |Int64  | Egyenlő
F.FN    |A konferenciasorozat társított tanulmány név mező  | Egyenlő,<br/>StartsWith

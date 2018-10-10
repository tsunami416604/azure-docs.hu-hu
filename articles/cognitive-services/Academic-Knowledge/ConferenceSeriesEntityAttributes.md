---
title: Konferencia sorozat tevékenységentitás-attribútumok – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Ismerje meg az attribútumok a Konferenciasorozat entitással is használhatja.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: f9f28afd7005d7a61aa0d2f4dba69ca598034b52
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900748"
---
# <a name="conference-series-entity"></a>Konferencia sorozat entitás

<sub> * A következő attribútumok konferencia sorozat entitás jellemzőek. (Folyó évi = "3") </sub>

Name (Név)    |Leírás                            |Típus       | Műveletek
------- | ------------------------------------- | --------- | ----------------------------
Azonosító      |Entitásazonosító                              |Int64      |Egyenlő
CN      |Konferenciasorozat normalized neve      |Sztring     |Egyenlő
DCN     |Konferencia sorozat megjelenített neve         |Sztring     |nincs
Másolat      |Konferenciasorozat idézetet száma összesen         |Int32      |nincs  
ECC     |Konferencia sorozat becsült idézetet teljes száma   |Int32      |nincs
F.FId   |A konferenciasorozat társított tanulmány Entitásazonosító mezője |Int64  | Egyenlő
F.FN    |A konferenciasorozat társított tanulmány név mező  | Egyenlő,<br/>StartsWith
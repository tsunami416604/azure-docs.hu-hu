---
title: Conference Series entitás attribútumai – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Ismerkedjen meg a Conference Series entitással használható attribútumokkal.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: feed324202f6a75ceb7e9089875b899c51cd8ae6
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705045"
---
# <a name="conference-series-entity"></a>Konferencia sorozatának entitása

<sub>* A következő attribútumok a Conference Series entitásra jellemzőek. (Ty = "3")</sub>

Name (Név)    |Leírás                            |Type       | Műveletek
------- | ------------------------------------- | --------- | ----------------------------
Id      |Entitásazonosító                              |Int64      |Egyenlő
CN      |Konferencia sorozat normalizált neve      |Sztring     |Egyenlő
DCN     |Konferencia-adatsorozat megjelenítendő neve         |Sztring     |nincs
Másolat      |Konferencia-sorozatok teljes idézetek száma         |Int32      |nincs  
ECC     |Konferencia-sorozat – becsült idézetek száma   |Int32      |nincs
F.FId   |A konferencia sorozathoz társított tanulmányi entitás AZONOSÍTÓjának mezője |Int64  | Egyenlő
F. FN    |A konferencia-sorozathoz társított tanulmányi név mezője  | Egyenlő<br/>StartsWith

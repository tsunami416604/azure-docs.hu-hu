---
title: Konferencia-példány entitás attribútumai – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Megtudhatja, hogy mely attribútumok használhatók a Academic Knowledge API konferencia-példány entitásával.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 37a353fbb86ca199b2316dcfba5904f4b46b0276
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705060"
---
# <a name="conference-instance-entity"></a>Konferencia-példány entitása

<sub>* A következő attribútumok a konferencia-példány entitásra jellemzőek. (Ty = "4")</sub>

Name (Név)    |Leírás                            |Type       | Műveletek
------- | ------------------------------------- | --------- | ----------------------------
Id      |Entitásazonosító                              |Int64      |Egyenlő
CIN     |Konferencia-példány normalizált neve ({ConferenceSeriesNormalizedName} {ConferenceInstanceYear})        |Sztring     |Egyenlő
DCN     |Konferencia-példány megjelenítendő neve ({ConferenceSeriesName}: {ConferenceInstanceYear})       |Karakterlánc     |nincs
CIL     |A konferencia-példány helye    |Sztring     |Egyenlő<br/>StartsWith
CISD    |A konferencia példányának kezdési dátuma  |Date       |Egyenlő<br/>IsBetween
CIED    |A konferencia példányának befejező dátuma    |Date       |Egyenlő<br/>IsBetween
CIARD   |A konferencia-példány absztrakt regisztrációjának határideje  |Date       |Egyenlő<br/>IsBetween
CISDD   |A konferencia-példány beküldésének határideje     |Date       |Egyenlő<br/>IsBetween
CIFVD   |A konferencia példányának végleges verziójának határideje  |Date       |Egyenlő<br/>IsBetween
CINDD   |A konferencia-példány értesítési dátuma   |Date       |Egyenlő<br/>IsBetween
CD. T    |Konferencia-példány eseményének címe   |Date       |Egyenlő<br/>IsBetween
CD. D    |Konferencia-példány eseményének dátuma    |Date       |Egyenlő<br/>IsBetween
PCS.CN  |A példány konferencia-adatsorozatának neve |Sztring     |Egyenlő
PCS.CId |A példány konferencia-sorozatának azonosítója |Int64    |Egyenlő
Másolat      |Konferencia-példányok teljes idézetek száma           |Int32      |nincs  
ECC     |Konferencia-példány teljes becsült idézetek száma |Int32      |nincs


## <a name="extended-metadata-attributes"></a>Bővített metaadatok attribútumai ##

Name (Név)    | Leírás               
--------|---------------------------    
FN      | Konferencia-példány teljes neve

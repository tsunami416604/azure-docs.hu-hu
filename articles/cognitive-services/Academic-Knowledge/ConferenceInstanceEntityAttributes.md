---
title: Konferencia példány tevékenységentitás-attribútumok – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Ismerje meg, hogy az attribútumok a Konferenciaesemény entitás az Academic Knowledge API használható.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 183a307159adb5dfdb248eb0cf4862462a626db6
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55879190"
---
# <a name="conference-instance-entity"></a>Konferencia példány entitás

<sub> * A következő attribútumok konferencia példány entitás jellemzőek. (Folyó évi = "4") </sub>

Name (Név)    |Leírás                            |Typo       | Műveletek
------- | ------------------------------------- | --------- | ----------------------------
Azonosító      |Entitásazonosító                              |Int64      |Egyenlő
CIN SZÁM     |Normalizált konferencia-példány neve ({ConferenceSeriesNormalizedName} {ConferenceInstanceYear})        |String     |Egyenlő
DCN     |Konferencia példány megjelenített neve ({ConferenceSeriesName}: {ConferenceInstanceYear})       |String     |nincs
CIL-HEZ     |A konferencia-példány helye    |String     |Egyenlő,<br/>StartsWith
CISD    |A konferencia példány kezdő dátuma  |Dátum       |Egyenlő,<br/>IsBetween
CIED    |A konferencia-példány a záró dátum    |Dátum       |Egyenlő,<br/>IsBetween
CIARD   |Regisztrációs absztrakt határideje a konferenciaesemény  |Dátum       |Egyenlő,<br/>IsBetween
CISDD   |Benyújtásának határideje a konferenciaesemény     |Dátum       |Egyenlő,<br/>IsBetween
CIFVD   |Végleges verzió az konferenciaesemény határideje  |Dátum       |Egyenlő,<br/>IsBetween
CINDD   |A konferencia példány értesítés dátuma   |Dátum       |Egyenlő,<br/>IsBetween
CD-RŐL. T    |A konferencia példány esemény címe   |Dátum       |Egyenlő,<br/>IsBetween
CD-RŐL. D    |A konferencia példány esemény dátuma    |Dátum       |Egyenlő,<br/>IsBetween
PCS.CN  |A példány konferencia adatsorozat neve |String     |Egyenlő
PCS.CId |A példány konferencia sorozat azonosítója |Int64    |Egyenlő
Másolat      |Konferencia összes idézetet példányszám           |Int32      |nincs  
ECC     |Konferencia összes becsült idézetet példányszám |Int32      |nincs


## <a name="extended-metadata-attributes"></a>Kiterjesztett attribútumok metaadatok ##

Name (Név)    | Leírás               
--------|---------------------------    
FN      | Teljes név konferenciaesemény

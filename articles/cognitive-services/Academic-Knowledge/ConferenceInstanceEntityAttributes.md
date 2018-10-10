---
title: Konferencia példány tevékenységentitás-attribútumok – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Ismerje meg, hogy az attribútumok a Konferenciaesemény entitás az Academic Knowledge API használható.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 6111ad00044943f12b2e098c4fd07ffb40185799
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902412"
---
# <a name="conference-instance-entity"></a>Konferencia példány entitás

<sub> * A következő attribútumok konferencia példány entitás jellemzőek. (Folyó évi = "4") </sub>

Name (Név)    |Leírás                            |Típus       | Műveletek
------- | ------------------------------------- | --------- | ----------------------------
Azonosító      |Entitásazonosító                              |Int64      |Egyenlő
CIN SZÁM     |Normalizált konferencia-példány neve ({ConferenceSeriesNormalizedName} {ConferenceInstanceYear})        |Sztring     |Egyenlő
DCN     |Konferencia példány megjelenített neve ({ConferenceSeriesName}: {ConferenceInstanceYear})       |Sztring     |nincs
CIL-HEZ     |A konferencia-példány helye    |Sztring     |Egyenlő,<br/>StartsWith
CISD    |A konferencia példány kezdő dátuma  |Dátum       |Egyenlő,<br/>IsBetween
CIED    |A konferencia-példány a záró dátum    |Dátum       |Egyenlő,<br/>IsBetween
CIARD   |Regisztrációs absztrakt határideje a konferenciaesemény  |Dátum       |Egyenlő,<br/>IsBetween
CISDD   |Benyújtásának határideje a konferenciaesemény     |Dátum       |Egyenlő,<br/>IsBetween
CIFVD   |Végleges verzió az konferenciaesemény határideje  |Dátum       |Egyenlő,<br/>IsBetween
CINDD   |A konferencia példány értesítés dátuma   |Dátum       |Egyenlő,<br/>IsBetween
CD-RŐL. T    |A konferencia példány esemény címe   |Dátum       |Egyenlő,<br/>IsBetween
CD-RŐL. D    |A konferencia példány esemény dátuma    |Dátum       |Egyenlő,<br/>IsBetween
SZÁMÍTÓGÉPEK. CN  |A példány konferencia adatsorozat neve |Sztring     |Egyenlő
SZÁMÍTÓGÉPEK. CId |A példány konferencia sorozat azonosítója |Int64    |Egyenlő
Másolat      |Konferencia összes idézetet példányszám           |Int32      |nincs  
ECC     |Konferencia összes becsült idézetet példányszám |Int32      |nincs


## <a name="extended-metadata-attributes"></a>Kiterjesztett attribútumok metaadatok ##

Name (Név)    | Leírás               
--------|---------------------------    
FN      | Teljes név konferenciaesemény
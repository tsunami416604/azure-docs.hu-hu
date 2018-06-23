---
title: Konferencia példány entitásattribútumhoz Academic Knowledge API-ban |} Microsoft Docs
description: Ismerje meg, hogy az attribútumok csak akkor használhatja a konferencia példány entitás kognitív szolgáltatásokban Academic Knowledge API-ban.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: ef2bca4346a4666905f3dfb7bd448720f3b0ef8b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346882"
---
# <a name="conference-instance-entity"></a>Konferencia példány entitás

<sub> * A következő attribútumok konferencia példány entitás vonatkoznak. (Most = "4") </sub>

Name (Név)    |Leírás                            |Típus       | Műveletek
------- | ------------------------------------- | --------- | ----------------------------
Azonosító      |Entitásazonosító                              |Int64      |Egyenlő
CIN SZÁM     |Normalizált konferencia-példány neve ({ConferenceSeriesNormalizedName} {ConferenceInstanceYear})        |Sztring     |Egyenlő
DCN     |Konferencia példány megjelenített neve ({ConferenceSeriesName}: {ConferenceInstanceYear})       |Sztring     |nincs
CIL-HEZ     |A konferencia-példány helye    |Sztring     |Egyenlő,<br/>StartsWith
CISD    |Kezdő dátum konferencia-példány  |Dátum       |Egyenlő,<br/>IsBetween
CIED    |A konferencia példány befejező dátuma    |Dátum       |Egyenlő,<br/>IsBetween
CIARD   |Absztrakt regisztrációs határidő konferencia-példány  |Dátum       |Egyenlő,<br/>IsBetween
CISDD   |Küldésének határidő konferencia-példány     |Dátum       |Egyenlő,<br/>IsBetween
CIFVD   |Végleges verziójú határidő konferencia-példány  |Dátum       |Egyenlő,<br/>IsBetween
CINDD   |Értesítési dátum konferencia-példány   |Dátum       |Egyenlő,<br/>IsBetween
CD-RŐL. T    |Cím konferencia példány esemény   |Dátum       |Egyenlő,<br/>IsBetween
CD-RŐL. D    |A konferencia példány esemény dátuma    |Dátum       |Egyenlő,<br/>IsBetween
SZÁMÍTÓGÉPEK. CN  |A példány konferencia adatsorozat neve |Sztring     |Egyenlő
SZÁMÍTÓGÉPEK. CId |A példány konferencia Azonosítót |Int64    |Egyenlő
Másolat      |Teljes hivatkozás konferencia példányszámot.           |Int32      |nincs  
ECC     |Teljes becsült hivatkozás konferencia példányszámot. |Int32      |nincs


## <a name="extended-metadata-attributes"></a>Bővített metaadat-attribútumok ##

Name (Név)    | Leírás               
--------|---------------------------    
FN      | Teljes név konferencia-példány
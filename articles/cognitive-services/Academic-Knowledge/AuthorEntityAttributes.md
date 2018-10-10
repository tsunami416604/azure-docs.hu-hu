---
title: Szerző tevékenységentitás-attribútumok – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Ismerje meg, hogy az attribútumok a szerző entitás az Academic Knowledge API használható.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 62e6da6d558a0494fb83115b1e307081099399d4
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900689"
---
# <a name="author-entity"></a>Szerző entitás
<sub> * A következő attribútumok Szerző entitás jellemzőek. (Folyó évi = "1") </sub>

Name (Név)    |Leírás                            |Típus       | Műveletek
------- | ------------------------------------- | --------- | ----------------------------
Azonosító      |Entitásazonosító                              |Int64      |Egyenlő
AuN     |Szerző normalizált neve                 |Sztring     |Egyenlő
DAuN    |Szerző megjelenített neve                    |Sztring     |nincs
Másolat      |Szerző idézetet teljes száma            |Int32      |nincs  
ECC     |Szerző becsült idézetet teljes száma  |Int32      |nincs
E       |Kiterjesztett metaadatok (lásd a "Bővített Meta attribútumok" tábla)  |Sztring     |nincs  


## <a name="extended-metadata-attributes"></a>Kiterjesztett attribútumok metaadatok ##

Name (Név)    | Leírás               
--------|---------------------------    
LKA. Afn     | a szerző társított viszony megjelenített neve  
LKA. AfId        | a szerző társított viszony Entitásazonosító
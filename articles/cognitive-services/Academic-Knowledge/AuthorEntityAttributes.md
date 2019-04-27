---
title: Szerző tevékenységentitás-attribútumok – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Ismerje meg, hogy az attribútumok a szerző entitás az Academic Knowledge API használható.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: d4b33c06ab023023aadf403cf0ef0b08c2bafc5f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60609756"
---
# <a name="author-entity"></a>Szerző entitás
<sub> * A következő attribútumok Szerző entitás jellemzőek. (Folyó évi = "1") </sub>

Name (Név)    |Leírás                            |Típus       | Műveletek
------- | ------------------------------------- | --------- | ----------------------------
Azonosító      |Entitásazonosító                              |Int64      |Egyenlő
AuN     |Szerző normalizált neve                 |String     |Egyenlő
DAuN    |Szerző megjelenített neve                    |String     |nincs
Másolat      |Szerző idézetet teljes száma            |Int32      |nincs  
ECC     |Szerző becsült idézetet teljes száma  |Int32      |nincs
E       |Kiterjesztett metaadatok (lásd a "Bővített Meta attribútumok" tábla)  |String     |nincs  


## <a name="extended-metadata-attributes"></a>Kiterjesztett attribútumok metaadatok ##

Name (Név)    | Leírás               
--------|---------------------------    
LKA.Afn     | a szerző társított viszony megjelenített neve  
LKA.AfId        | a szerző társított viszony Entitásazonosító

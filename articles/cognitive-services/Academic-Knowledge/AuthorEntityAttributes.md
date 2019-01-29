---
title: Szerző tevékenységentitás-attribútumok – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Ismerje meg, hogy az attribútumok a szerző entitás az Academic Knowledge API használható.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 48758ac9ec8c993bbdb490229ae20fcce1fb0a49
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55175179"
---
# <a name="author-entity"></a>Szerző entitás
<sub> * A következő attribútumok Szerző entitás jellemzőek. (Folyó évi = "1") </sub>

Name (Név)    |Leírás                            |Typo       | Műveletek
------- | ------------------------------------- | --------- | ----------------------------
Azonosító      |Entitásazonosító                              |Int64      |Egyenlő
AuN     |Szerző normalizált neve                 |Karakterlánc     |Egyenlő
DAuN    |Szerző megjelenített neve                    |Karakterlánc     |nincs
Másolat      |Szerző idézetet teljes száma            |Int32      |nincs  
ECC     |Szerző becsült idézetet teljes száma  |Int32      |nincs
E       |Kiterjesztett metaadatok (lásd a "Bővített Meta attribútumok" tábla)  |Karakterlánc     |nincs  


## <a name="extended-metadata-attributes"></a>Kiterjesztett attribútumok metaadatok ##

Name (Név)    | Leírás               
--------|---------------------------    
LKA.Afn     | a szerző társított viszony megjelenített neve  
LKA.AfId        | a szerző társított viszony Entitásazonosító

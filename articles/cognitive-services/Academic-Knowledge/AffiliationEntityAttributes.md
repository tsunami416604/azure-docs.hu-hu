---
title: Az Academic Knowledge API a tagságot tevékenységentitás-attribútumok
titlesuffix: Azure Cognitive Services
description: Ismerje meg, az attribútumokat, a tagságot entitás az Academic Knowledge API használható.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 344b26b16f74cd44982e3c93fa69295792daa9a0
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55190632"
---
# <a name="affiliation-entity"></a>A tagságot entitás

<sub> * A következő attribútumokat a tagságot entitás jellemzőek. (Folyó évi = "5") </sub>

Name (Név)    |Leírás                            |Típus       | Műveletek
------- | ------------------------------------- | --------- | ----------------------------
Azonosító      |Entitásazonosító                              |Int64      |Egyenlő
AfN     |A tagságot normalizált neve        |Karakterlánc     |Egyenlő
DAfN    |A tagságot megjelenített neve       |Karakterlánc     |nincs
Másolat      |A tagságot idézetet teljes száma           |Int32      |nincs  
ECC     |A tagságot becsült idézetet teljes száma |Int32      |nincs

## <a name="extended-metadata-attributes"></a>Kiterjesztett attribútumok metaadatok ##

Name (Név)    | Leírás               
--------|---------------------------    
PC      |A tagságot a papír száma

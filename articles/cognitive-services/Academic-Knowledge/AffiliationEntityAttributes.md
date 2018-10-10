---
title: Az Academic Knowledge API a tagságot tevékenységentitás-attribútumok
titlesuffix: Azure Cognitive Services
description: Ismerje meg, az attribútumokat, a tagságot entitás az Academic Knowledge API használható.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 177fe9da8bbe821a69eae02d89a225e5d4009331
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900478"
---
# <a name="affiliation-entity"></a>A tagságot entitás

<sub> * A következő attribútumokat a tagságot entitás jellemzőek. (Folyó évi = "5") </sub>

Name (Név)    |Leírás                            |Típus       | Műveletek
------- | ------------------------------------- | --------- | ----------------------------
Azonosító      |Entitásazonosító                              |Int64      |Egyenlő
AfN     |A tagságot normalizált neve        |Sztring     |Egyenlő
DAfN    |A tagságot megjelenített neve       |Sztring     |nincs
Másolat      |A tagságot idézetet teljes száma           |Int32      |nincs  
ECC     |A tagságot becsült idézetet teljes száma |Int32      |nincs

## <a name="extended-metadata-attributes"></a>Kiterjesztett attribútumok metaadatok ##

Name (Név)    | Leírás               
--------|---------------------------    
PC      |A tagságot a papír száma
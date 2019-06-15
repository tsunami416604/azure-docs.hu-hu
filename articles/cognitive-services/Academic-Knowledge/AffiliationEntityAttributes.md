---
title: Az Academic Knowledge API a tagságot tevékenységentitás-attribútumok
titlesuffix: Azure Cognitive Services
description: Ismerje meg, az attribútumokat, a tagságot entitás az Academic Knowledge API használható.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 82e6a5b66342e58e62da029d617cbd1d74c28149
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "61340519"
---
# <a name="affiliation-entity"></a>A tagságot entitás

<sub> * A következő attribútumokat a tagságot entitás jellemzőek. (Folyó évi = "5") </sub>

Name (Név)    |Leírás                            |Típus       | Műveletek
------- | ------------------------------------- | --------- | ----------------------------
Azonosító      |Entitásazonosító                              |Int64      |Egyenlő
AfN     |A tagságot normalizált neve        |String     |Egyenlő
DAfN    |A tagságot megjelenített neve       |String     |Egyik sem
CC      |A tagságot idézetet teljes száma           |Int32      |Egyik sem  
ECC     |A tagságot becsült idézetet teljes száma |Int32      |Egyik sem

## <a name="extended-metadata-attributes"></a>Kiterjesztett attribútumok metaadatok ##

Name (Név)    | Leírás               
--------|---------------------------    
PC      |A tagságot a papír száma

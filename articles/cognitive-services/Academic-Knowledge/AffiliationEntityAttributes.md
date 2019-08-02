---
title: Kapcsolati entitás attribútumai a Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Megtudhatja, hogy mely attribútumok használhatók a Academic Knowledge API a kapcsolati entitással.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 333875472d9b859196c4d828061301b9d32c4d5a
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705116"
---
# <a name="affiliation-entity"></a>Kapcsolati entitás

<sub>* A következő attribútumok a kapcsolati entitásra vonatkoznak. (Ty = "5")</sub>

Name (Név)    |Leírás                            |Type       | Műveletek
------- | ------------------------------------- | --------- | ----------------------------
Id      |Entitásazonosító                              |Int64      |Egyenlő
AfN     |Kapcsolat normalizált neve        |Sztring     |Egyenlő
DAfN    |Kapcsolat megjelenített neve       |Sztring     |nincs
Másolat      |Kapcsolatok teljes bibliográfiai száma           |Int32      |nincs  
ECC     |Kapcsolat teljes becsült idézetek száma |Int32      |nincs

## <a name="extended-metadata-attributes"></a>Bővített metaadatok attribútumai ##

Name (Név)    | Leírás               
--------|---------------------------    
PC      |Kapcsolat papírjának száma

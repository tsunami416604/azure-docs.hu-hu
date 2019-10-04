---
title: Szerzői entitás attribútumai – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: A Academic Knowledge API szerző entitásával használható attribútumok megismerése.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: e63e9d3f8f17a2473caf3d31b83e318ddb132b43
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705093"
---
# <a name="author-entity"></a>Szerzői entitás
<sub>* A következő attribútumok a szerző entitásra jellemzőek. (Ty = "1")</sub>

Name (Név)    |Leírás                            |Type       | Műveletek
------- | ------------------------------------- | --------- | ----------------------------
Id      |Entitásazonosító                              |Int64      |Egyenlő
AuN     |Normalizált név szerzője                 |Sztring     |Egyenlő
DAuN    |Szerző megjelenítendő neve                    |Sztring     |nincs
Másolat      |Szerzői teljes idézetek száma            |Int32      |nincs  
ECC     |Szerzői teljes becsült idézetek száma  |Int32      |nincs
E       |Kiterjesztett metaadatok (lásd a "bővített meta attribútumok" táblázatot)  |Karakterlánc     |nincs  


## <a name="extended-metadata-attributes"></a>Bővített metaadatok attribútumai ##

Name (Név)    | Leírás               
--------|---------------------------    
LKA.Afn     | a szerzőhöz társított kapcsolat megjelenítendő neve  
LKA.AfId        | a szerzőhöz társított tulajdonosi entitás azonosítója

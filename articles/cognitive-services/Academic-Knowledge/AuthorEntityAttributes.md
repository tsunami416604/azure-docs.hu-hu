---
title: Academic Knowledge API-ja entitásattribútumhoz szerzői |} Microsoft Docs
description: Ismerje meg, hogy az attribútumok csak akkor használhatja a szerző entitás kognitív szolgáltatásokban Academic Knowledge API-ban.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: afcc3257c49522a4631c4aae0e0c2b88373f9af1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346854"
---
# <a name="author-entity"></a>Szerző entitás
<sub> * A következő attribútumok Szerző entitás vonatkoznak. (Most = "1") </sub>

Name (Név)    |Leírás                            |Típus       | Műveletek
------- | ------------------------------------- | --------- | ----------------------------
Azonosító      |Entitásazonosító                              |Int64      |Egyenlő
AuN     |Normalizált Szerző neve                 |Sztring     |Egyenlő
DAuN    |Szerző megjelenített neve                    |Sztring     |nincs
Másolat      |Szerző teljes hivatkozás száma            |Int32      |nincs  
ECC     |Szerző becsült hivatkozás teljes száma  |Int32      |nincs
E       |Kiterjesztett metaadatok (lásd a "Kiterjesztett Meta attribútumok" tábla)  |Sztring     |nincs  


## <a name="extended-metadata-attributes"></a>Bővített metaadat-attribútumok ##

Name (Név)    | Leírás               
--------|---------------------------    
LKA. Afn     | a szerző társított viszony megjelenített neve  
LKA. AfId        | a szerző társított viszony entitás azonosítója
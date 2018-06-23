---
title: Adatsorozat entitásattribútumhoz konferencia Academic Knowledge API-ban |} Microsoft Docs
description: További tudnivalók az attribútumok csak akkor használhatja a konferencia adatsorozat entitás kognitív szolgáltatásban.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 332736c927bdaa00334546f626a6eabb8e11d3b5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346859"
---
# <a name="conference-series-entity"></a>Konferencia adatsorozat entitás

<sub> * A következő attribútumok konferencia adatsorozat entitás vonatkoznak. (Most = "3") </sub>

Name (Név)    |Leírás                            |Típus       | Műveletek
------- | ------------------------------------- | --------- | ----------------------------
Azonosító      |Entitásazonosító                              |Int64      |Egyenlő
CN      |Konferencia adatsorozat normalizált neve      |Sztring     |Egyenlő
DCN     |Konferencia adatsorozat megjelenített neve         |Sztring     |nincs
Másolat      |Konferencia adatsorozat hivatkozás száma összesen         |Int32      |nincs  
ECC     |Konferencia adatsorozat becsült hivatkozás teljes száma   |Int32      |nincs
F.FId   |A vizsgálat entitás azonosítója az konferencia adatsorozathoz tartozó mező |Int64  | Egyenlő
F.FN    |A konferencia adatsorozat társított vizsgálat név mezője  | Egyenlő,<br/>StartsWith
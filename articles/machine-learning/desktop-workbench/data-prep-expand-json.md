---
title: Bontsa ki az Azure Machine Learning Workbench használatával JSON-átalakítás
description: A referenciadokumentum bontsa ki a JSON-átalakításhoz
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc, reference
ms.topic: article
ms.date: 09/14/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 0a5cbca114b220686d656f93edb00a199e3cbeeb
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46989821"
---
# <a name="expand-json-transformation"></a>Bontsa ki a JSON-átalakítás

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


A **bontsa ki a JSON** átalakító lehetővé teszi a felhasználóknak egy létező oszlop érvényes JSON-szöveget tartalmazó több oszlopba.

## <a name="how-to-perform-this-transformation"></a>Az átalakítás végrehajtása

Kövesse az alábbi lépéseket az átalakítás végrehajtásához:
1. Válassza ki a forrás JSON-szöveget tartalmazó oszlop.
2. Válassza ki **bontsa ki a JSON** származó a **alakítja át az** menü. Vagy a jobb gombbal a forrás oszlopra, és válassza a fejlécében **bontsa ki a JSON** a helyi menüből. 
3. Kattintson az **OK** gombra. 
 
Új oszlopokat ad hozzá a forrásoszlop mellett. Ezekben az oszlopokban a JSON-szövegben a hierarchia következő szintjére tulajdonságokat tartalmazzák. Tulajdonság kulcsát, ha van ilyen, segítségével hozzon létre a megfelelő oszlop neve. Beágyazott tulajdonságok megmaradnak JSON-szöveget, hogy a felhasználó iteratív kibontható vagy vesse el, igény szerint.

## <a name="examples"></a>Példák

A forrásoszlop *ügyfél* ki van bontva, két oszlopra *Customer.Name* és *Customer.Phone*.

| Ügyfél                                                | Customer.Name   | Customer.Phone |
|---------------------------------------------------------|-----------------|----------------|
| {"Name": "Carrie Dodson", "Phone": "123-4567-890"}   | Carrie Dodson   | 123-4567-890   |
| {"Name": "Leonard Robledo", "Phone": "456-7890-123"} | Leonard Robledo | 456-7890-123   |


---
title: "Bontsa ki az Azure Machine Learning-munkaterület használatával JSON átalakítása"
description: "A hivatkozás a dokumentum a bontsa ki a JSON-transzformáció"
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, reference
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: 21de94d2d0d3cc12aabcb8e9e8b0eec39b0a2710
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2018
---
# <a name="expand-json-transformation"></a>Bontsa ki a JSON átalakítása
A **bontsa ki a JSON** átalakítás lehetővé teszi a felhasználók több oszlopba érvényes JSON-szöveg tartalmazó oszlop kibontásához.

## <a name="how-to-perform-this-transformation"></a>Ez a transzformáció végrehajtása

Kövesse az alábbi lépéseket a transzformáció elvégzéséhez:
1. Válassza ki a forrás JSON-szöveg tartalmazó oszlop.
2. Válassza ki **bontsa ki a JSON** a a **átalakítja** menü. Vagy kattintson jobb gombbal a forrás oszlopban, és válasszon fejlécének **bontsa ki a JSON** a helyi menüből. 
3. Kattintson az **OK** gombra. 
 
Új oszlopokat ad hozzá a forrás oszlop mellett. Ezek az oszlopok a következő szintről a JSON-szövegben hierarchia tulajdonságokat tartalmazzák. Tulajdonság kulcs, ha van ilyen, használatával hozzon létre a megfelelő oszlop neve. Beágyazott tulajdonságok megmaradnak JSON-szövegként, hogy a felhasználó ismételt is kibontása vagy vesse el az igény szerint.

## <a name="examples"></a>Példák

A forrás columnn *ügyfél* két oszlopra ki van bontva *Customer.Name* és *Customer.Phone*.

| Ügyfél                                                | Customer.Name   | Customer.Phone |
|---------------------------------------------------------|-----------------|----------------|
| { "Name" : "Carrie Dodson", "Phone" : "123-4567-890"}   | Carrie Dodson   | 123-4567-890   |
| {"Name": "Leonard Robledo", "Phone": "456-7890 – 123"} | Leonard Robledo | 456-7890-123   |


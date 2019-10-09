---
title: Azure Data Factory leképezési adatfolyam-hivatkozás csomópontja
description: Data Factory adatfolyamhoz hozzá kell adni egy hivatkozási csomópontot az illesztésekhez, a keresésekhez és a szakszervezetekhez.
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 4ed17114cc0ce586c68c5b3e087acffdb82ea96c
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030497"
---
# <a name="mapping-data-flow-reference-node"></a>Adatfolyam-hivatkozás leképezése csomópont



![Hivatkozási csomópont](media/data-flow/referencenode.png "hivatkozási csomópontja")

A rendszer automatikusan hozzáadja a vászonhoz egy hivatkozási csomópontot, amely azt jelzi, hogy a csomópont hozzá van csatolva a vászon egy másik létező csomópontjára hivatkozik. Tekintse át a hivatkozási csomópontot mutatóként vagy egy másik adatfolyam-átalakításra mutató hivatkozást.

Példa: Ha egynél több adatfolyamot csatlakoztat vagy egyesít, az adatfolyam-vászon olyan hivatkozási csomópontot adhat hozzá, amely a nem elsődleges bejövő adatfolyam nevét és beállításait tükrözi.

A hivatkozási csomópont nem helyezhető át és nem törölhető. A csomópontra kattintva azonban módosíthatja a kezdeményező átalakítás beállításait.

Azok a felhasználói felületi szabályok, amelyek meghatározzák, hogy az adatfolyam hozzáadásakor a hivatkozási csomópont a rendelkezésre álló területtől és a sorok közötti függőleges térköztől függ.

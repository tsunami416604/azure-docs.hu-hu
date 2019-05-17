---
title: Az Azure Data Factory adatokat folyamat áthelyezési csomópontok
description: Hogyan kívánja áthelyezni a csomópontok a egy Azure Data Factory leképezési adatok folyamatábrája
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 951a5d4fcbd561b085b0377bde48e820dc8972a2
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2019
ms.locfileid: "65519972"
---
# <a name="mapping-data-flow-move-nodes"></a>Leképezés Adatcsomópontok folyamat áthelyezése

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Átalakítási beállítások összesített](media/data-flow/agghead.png "gyűjtő fejléc")

Az Azure Data Factory adatfolyam tervezőfelületére az a "fejlesztés" felület, ahol hozhat létre az adatok folyamatok fentről lefelé, balról jobbra. Az eszközkészletet, plusz az egyes átalakítási csatlakoztatva van (+) jelet. Csomópontok élek szabad formátumú DAG környezetben keresztül csatlakozó helyett az üzleti logikára összpontosíthat.

Tehát nélkül egy fogd és vidd paradigm, az átalakítási csomópont, "áthelyezése" módja módosítani a beérkező streamben. Ehelyett mozgatnia átalakítások a "bejövő streamet" módosításával.

## <a name="streams-of-data-inside-of-data-flow"></a>Adatok folyamaton adatfolyam

Az Azure Data Factory adatfolyam Streamek jelölik az adatok áramlását. Átalakítási beállítások panelen megjelenik egy "Bejövő Steam" mezőt. Ez jelzi, mely beérkező adatfolyam van számból az átalakítás. Az átalakítási csomópont a diagramon használt fizikai helynek bejövő Stream nevére kattint, és a egy másik adatfolyam kiválasztásával módosíthatja. Az aktuális átalakítás együtt az adott adatfolyam minden ezt követő átalakítások majd áthelyezi az új helyen.

Egy vagy több átalakítások átalakításban utána áthelyezéséhez, majd az új hely az adatok az adatfolyam tartományhoz fog csatlakozni egy új ág keresztül.

Ha nincsenek további átalakítások után a kiválasztott csomópontot, majd csak az adott átalakítási át az új helyen.

## <a name="next-steps"></a>További lépések

Miután befejezte az adatfolyam-Tervező, kapcsolja be a hibakeresési gombra és a tesztelés közben ki hibakeresési módban vagy közvetlenül a [adatok folyamattervezőben](concepts-data-flow-debug-mode.md) vagy [folyamat hibakeresési](control-flow-execute-data-flow-activity.md).

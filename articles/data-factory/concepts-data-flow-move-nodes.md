---
title: Az Azure Data Factory adatokat folyamat áthelyezési csomópontok
description: Hogyan kívánja áthelyezni a csomópontok a egy Azure Data Factory adatokat folyamatábrája
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: d82b32e876144a626333f3df1481c5fce9862067
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56732177"
---
# <a name="mapping-data-flow-move-nodes"></a>Leképezés Adatcsomópontok folyamat áthelyezése

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Átalakítási beállítások összesített](media/data-flow/agghead.png "gyűjtő fejléc")

Az Azure Data Factory adatfolyam tervezőfelületére az a "fejlesztés" felület, ahol hozhat létre az adatok folyamatok fentről lefelé, balról jobbra. Az eszközkészletet, plusz az egyes átalakítási csatlakoztatva van (+) jelet. Csomópontok élek szabad formátumú DAG környezetben keresztül csatlakozó helyett az üzleti logikára összpontosíthat.

Tehát nélkül egy fogd és vidd paradigm, az átalakítási csomópont, "áthelyezése" módja módosítani a beérkező streamben. Ehelyett mozgatnia átalakítások a "bejövő streamet" módosításával.

Az Azure Data Factory adatfolyam Streamek jelölik az adatok áramlását. Átalakítási beállítások panelen megjelenik egy "Bejövő Steam" mezőt. Ez jelzi, mely beérkező adatfolyam van számból az átalakítás. Az átalakítási csomópont a diagramon használt fizikai helynek bejövő Stream nevére kattint, és a egy másik adatfolyam kiválasztásával módosíthatja. Az aktuális átalakítás együtt az adott adatfolyam minden ezt követő átalakítások majd áthelyezi az új helyen.

Egy vagy több átalakítások átalakításban utána áthelyezéséhez, majd az új hely az adatok az adatfolyam tartományhoz fog csatlakozni egy új ág keresztül.

Ha nincsenek további átalakítások után a kiválasztott csomópontot, majd csak az adott átalakítási át az új helyen.

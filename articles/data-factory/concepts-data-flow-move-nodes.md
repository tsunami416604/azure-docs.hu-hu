---
title: Az Azure Data Factory adatokat folyamat áthelyezési csomópontok
description: Hogyan kívánja áthelyezni a csomópontok a egy Azure Data Factory adatokat folyamatábrája
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 4328a090478747e9acbfb25cca45b330cd4a300b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56213404"
---
#<a name="data-factory-data-flow-move-nodes"></a>Data Factory folyamat áthelyezési Adatcsomópontok

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Átalakítási beállítások összesített](media/data-flow/agghead.png "gyűjtő fejléc")

Az Azure Data Factory adatfolyam tervezőfelületére az a "fejlesztés" felület, ahol hozhat létre az adatok folyamatok fentről lefelé, balról jobbra. Az eszközkészletet, plusz az egyes átalakítási csatlakoztatva van (+) jelet. Csomópontok élek szabad formátumú DAG környezetben keresztül csatlakozó helyett az üzleti logikára összpontosíthat.

Ebben az esetben egy fogd és vidd paradigmát nélkül, egy átalakítási csomópont "áthelyezése" módja a beérkező streamben módosíthatja. Ehelyett mozgatnia átalakítások a "bejövő streamet" módosításával.

Az Azure Data Factory adatfolyam Streamek jelölik az adatok áramlását. Átalakítási beállítások panelen megjelenik egy "Bejövő Steam" mezőt. Ez jelzi, mely beérkező adatfolyam van számból az átalakítás. Az átalakítási csomópont a diagramon használt fizikai helynek bejövő Stream nevére kattint, és a egy másik adatfolyam kiválasztásával módosíthatja. Az aktuális átalakítás együtt az adott adatfolyam minden ezt követő átalakítások majd áthelyezi az új helyen.

Egy vagy több átalakítások átalakításban utána áthelyezéséhez, majd az új hely az adatok az adatfolyam tartományhoz fog csatlakozni egy új ág keresztül.

Ha nincsenek további átalakítások után a kiválasztott csomópontot, majd csak az adott átalakítási át az új helyen.

---
title: fájlbefoglalás
description: fájlbefoglalás
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 11/19/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: c37e2c28eb6aee9edf4bdd97066ce5f15e7447c1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005630"
---
## <a name="message-headers"></a>Üzenetek fejlécei
Ezek az üzenetek fejlécében kapott tulajdonságok:

| Tulajdonság neve | Leírás |
| ------------- | ----------- | 
| AEG-előfizetés – név | Az esemény-előfizetés neve. |
| AEG – kézbesítés – darabszám | Az eseményre tett kísérletek száma. |
| AEG – eseménytípus | <p>Az esemény típusa.</p><p>A következő értékek egyike lehet:</p><ul><li>SubscriptionValidation</li><li>Értesítés</li><li>SubscriptionDeletion</li></ul> | 
| AEG – metaadatok – verzió | <p>Az esemény metaadat-verziója.<p> **Event Grid Event Schema** esetében ez a tulajdonság a metaadat-verziót és a **Felhőbeli esemény sémáját** jelöli, amely a **spec verziót** jelöli. </p>|
| AEG – adatverzió | <p>Az esemény adatverziója.</p><p>**Event Grid Event Schema** esetében ez a tulajdonság az adatverziót és a **Felhőbeli esemény sémáját** jelöli, nem érvényes.</p> |
| AEG-output-Event-ID | A Event Grid esemény azonosítója. |



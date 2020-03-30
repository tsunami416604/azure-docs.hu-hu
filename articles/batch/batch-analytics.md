---
title: Azure Batch-statisztika
description: A Batch Analytics témakörei a Batch service-erőforrásokhoz elérhető eseményekre és riasztásokra vonatkozó referenciainformációkat tartalmaznak.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: labrenne
ms.openlocfilehash: d9538461dfa48a961f9325566b2adaf265e8daeb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025962"
---
# <a name="batch-analytics"></a>Batch-elemzés
A Batch Analytics témakörei a Batch service-erőforrásokhoz elérhető eseményekre és riasztásokra vonatkozó referenciainformációkat tartalmaznak.

A Batch diagnosztikai naplók engedélyezésével és használatával kapcsolatos további információkért tekintse meg az [Azure Batch diagnosztikai naplózását.](https://azure.microsoft.com/documentation/articles/batch-diagnostics/)

## <a name="diagnostic-logs"></a>Diagnosztikai naplók

Az Azure Batch szolgáltatás a következő diagnosztikai naplóeseményeket bocsátja ki bizonyos Batch-erőforrások élettartama alatt.

**Szolgáltatásnapló eseményei**
* [Készlet létrehozása](batch-pool-create-event.md)
* [Készlet törlése kezdete](batch-pool-delete-start-event.md)
* [Készlet törlése kész](batch-pool-delete-complete-event.md)
* [Készlet átméretezési kezdete](batch-pool-resize-start-event.md)
* [Készlet átméretezése kész](batch-pool-resize-complete-event.md)
* [Tevékenység kezdete](batch-task-start-event.md)
* [Feladat befejezve](batch-task-complete-event.md)
* [A feladat sikertelen](batch-task-fail-event.md)
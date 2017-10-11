---
title: Az Azure Batch Analytics |} Microsoft Docs
description: "Az Azure Batch Analytics hivatkozását."
services: batch
author: tamram
manager: timlt
ms.assetid: 
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: tamram
ms.openlocfilehash: 7d634e1bb595a84b8af339e5bc5a483a7849e7f7
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="batch-analytics"></a>Kötegelt elemzés
Kötegelt elemzés témakörei az események és riasztások érhető el a Batch szolgáltatás erőforrásainak referencia jellegű információt tartalmaz.

Lásd: [Azure Batch diagnosztikai naplózás](https://azure.microsoft.com/documentation/articles/batch-diagnostics/) engedélyezése és kötegelt diagnosztikai naplók fel további információt.

## <a name="diagnostic-logs"></a>Diagnosztikai naplók

Az Azure Batch szolgáltatás a következő diagnosztikai naplóeseményeket bizonyos kötegelt erőforrások élettartama során bocsát ki.

**Szolgáltatás bejelentkezési események**
* [Alkalmazáskészlet létrehozása](batch-pool-create-event.md)
* [Készlet törlése indítása](batch-pool-delete-start-event.md)
* [Teljes készlet törlése](batch-pool-delete-complete-event.md)
* [Készlet átméretezési indítása](batch-pool-resize-start-event.md)
* [Teljes készlet átméretezése](batch-pool-resize-complete-event.md)
* [A feladat indítása](batch-task-start-event.md)
* [A feladat befejezése](batch-task-complete-event.md)
* [A feladat sikertelen](batch-task-fail-event.md)
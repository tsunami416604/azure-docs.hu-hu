---
title: Az Azure Batch-elemzés |} A Microsoft Docs
description: Az Azure Batch-elemzés dokumentációja.
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: 999c3037196044250b8a12d6b6b380553e58c6ba
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55460182"
---
# <a name="batch-analytics"></a>Batch-elemzés
A Batch-elemzés témakörei az események és riasztások érhető el, a Batch szolgáltatás erőforrásainak referenciaadatai tartalmaznak.

Lásd: [diagnosztikai naplózás az Azure Batch](https://azure.microsoft.com/documentation/articles/batch-diagnostics/) engedélyezése és a diagnosztikai naplók a Batchben felhasználása további tájékoztatást.

## <a name="diagnostic-logs"></a>Diagnosztikai naplók

Az Azure Batch szolgáltatás a következő diagnosztikai napló események egyes Batch-erőforrások teljes élettartama során bocsát ki.

**Szolgáltatás bejelentkezési események**
* [Készlet létrehozása](batch-pool-create-event.md)
* [Készlet törlésének indítása](batch-pool-delete-start-event.md)
* [Készlet törlése kész](batch-pool-delete-complete-event.md)
* [Készlet átméretezésének indítása](batch-pool-resize-start-event.md)
* [Készlet átméretezése kész](batch-pool-resize-complete-event.md)
* [Zahájení úlohy](batch-task-start-event.md)
* [A feladat befejezése](batch-task-complete-event.md)
* [A feladat sikertelen](batch-task-fail-event.md)
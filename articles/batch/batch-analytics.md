---
title: Az Azure Batch Analytics |} Microsoft Docs
description: Az Azure Batch Analytics hivatkozását.
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: danlep
ms.openlocfilehash: 4c81acb282b24bbd899227c4dcc449fed4ba6c7d
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
ms.locfileid: "30312677"
---
# <a name="batch-analytics"></a>Batch-elemzés
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
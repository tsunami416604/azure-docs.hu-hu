---
title: Azure Batch Analitika | Microsoft Docs
description: Azure Batch Analitika referenciája.
services: batch
author: ju-shim
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: jushiman
ms.openlocfilehash: 39a8bfb6a48bf55ae9f2ec36f7716959e6ada9dd
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "76027400"
---
# <a name="batch-analytics"></a>Batch-elemzés
A Batch Analytics témakörei a Batch szolgáltatás erőforrásaihoz elérhető eseményekre és riasztásokra vonatkozó információkat tartalmaznak.

A Batch diagnosztikai naplók engedélyezésével és elfogyasztásával kapcsolatos további információkért tekintse meg a [Azure batch diagnosztikai naplózást](https://azure.microsoft.com/documentation/articles/batch-diagnostics/) ismertető témakört.

## <a name="diagnostic-logs"></a>Diagnosztikai naplók

A Azure Batch szolgáltatás a következő diagnosztikai naplózási eseményeket bocsátja ki bizonyos batch-erőforrások élettartama alatt.

**Szolgáltatás-naplózási események**
* [Készlet létrehozása](batch-pool-create-event.md)
* [Készlet törlésének kezdete](batch-pool-delete-start-event.md)
* [Készlet törlése kész](batch-pool-delete-complete-event.md)
* [Készlet átméretezésének kezdete](batch-pool-resize-start-event.md)
* [A készlet átméretezése befejeződött](batch-pool-resize-complete-event.md)
* [Feladat indítása](batch-task-start-event.md)
* [A feladat befejeződött](batch-task-complete-event.md)
* [Sikertelen feladat](batch-task-fail-event.md)
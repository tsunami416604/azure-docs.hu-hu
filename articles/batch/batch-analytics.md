---
title: Azure Batch Analitika | Microsoft Docs
description: Azure Batch Analitika referenciája.
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: 03cb6231a42e27c474e20f4c6ae91095156bb766
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70095517"
---
# <a name="batch-analytics"></a>Batch-elemzés
A Batch Analytics témakörei a Batch szolgáltatás erőforrásaihoz elérhető eseményekre és riasztásokra vonatkozó információkat tartalmaznak.

A Batch diagnosztikai naplók engedélyezésével és elfogyasztásával kapcsolatos további információkért tekintse meg a [Azure batch diagnosztikai naplózást](https://azure.microsoft.com/documentation/articles/batch-diagnostics/) ismertető témakört.

## <a name="diagnostic-logs"></a>Diagnosztikai naplók

A Azure Batch szolgáltatás a következő diagnosztikai naplózási eseményeket bocsátja ki bizonyos batch-erőforrások élettartama alatt.

**Szolgáltatás bejelentkezési események**
* [Készlet létrehozása](batch-pool-create-event.md)
* [Készlet törlésének indítása](batch-pool-delete-start-event.md)
* [Készlet törlése kész](batch-pool-delete-complete-event.md)
* [Készlet átméretezésének indítása](batch-pool-resize-start-event.md)
* [Készlet átméretezése kész](batch-pool-resize-complete-event.md)
* [Zahájení úlohy](batch-task-start-event.md)
* [A feladat befejezése](batch-task-complete-event.md)
* [A feladat sikertelen](batch-task-fail-event.md)
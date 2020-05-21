---
title: Azure Batch Analitika
description: A Batch Analytics témakörei a Batch szolgáltatás erőforrásaihoz elérhető eseményekre és riasztásokra vonatkozó információkat tartalmaznak.
ms.topic: reference
ms.date: 04/20/2017
ms.openlocfilehash: b07d362c3cc2d3b086cf0a82325025604907a393
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83726859"
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
---
title: Azure Batch Analitika
description: A Batch Analytics témakörei a Batch szolgáltatás erőforrásaihoz elérhető eseményekre és riasztásokra vonatkozó információkat tartalmaznak.
ms.topic: article
ms.date: 04/20/2017
ms.openlocfilehash: d35bf249a3ae5dd06f42b89c3d5c2773f9508603
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82113068"
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
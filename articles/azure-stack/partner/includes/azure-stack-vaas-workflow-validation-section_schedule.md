---
author: mattbriggs
ms.service: azure-stack
ms.topic: include
ms.date: 11/26/2018
ms.author: mabrigg
ms.openlocfilehash: be9b3b3a9cbb39386d13f76c595ce5e34688adac
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52336071"
---
Az érvényesítés munkafolyamatokban **ütemezés** egy tesztet a munkafolyamat-szintű gyakori paramétereket, a munkafolyamat létrehozása során megadott használja (lásd: [az Azure Stack érvényesítésiszolgáltatásáltalánosmunkafolyamat-paraméterek](../azure-stack-vaas-parameters.md)). Ha bármelyik teszt paraméterértékek érvénytelenné válik, meg kell resupply őket a utasításai [munkafolyamat-paraméterek módosítása](../azure-stack-vaas-monitor-test.md#change-workflow-parameters).

> [!NOTE]
> Teszt ütemezés egy meglévő példányát keresztül fog létrehozni a régi példány helyett egy új példányt a portálon. A régi példány naplók megőrzi a rendszer, de nem érhetők el a portálon.  
Egy teszt sikeres végrehajtása után a **ütemezés** művelet le lesz tiltva.

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](azure-stack-vaas-workflow-step_select-agent.md)]

1. Válassza ki **ütemezés** nyissa meg a tevékenységütemezést a test-példány ehhez a helyi menüből.

1. Tekintse át a teszt paramétereket, majd **küldés** ütemezni a teszt végrehajtásához.
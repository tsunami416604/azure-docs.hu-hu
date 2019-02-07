---
author: mattbriggs
ms.service: azure-stack
ms.topic: include
ms.date: 11/26/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/26/2018
ms.openlocfilehash: 5cd64b806392162fd3bee14ddaf607385ac05264
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55807120"
---
Az érvényesítés munkafolyamatokban **ütemezés** egy tesztet a munkafolyamat-szintű gyakori paramétereket, a munkafolyamat létrehozása során megadott használja (lásd: [az Azure Stack érvényesítésiszolgáltatásáltalánosmunkafolyamat-paraméterek](../azure-stack-vaas-parameters.md)). Ha bármelyik teszt paraméterértékek érvénytelenné válik, meg kell resupply őket a utasításai [munkafolyamat-paraméterek módosítása](../azure-stack-vaas-monitor-test.md#change-workflow-parameters).

> [!NOTE]
> Teszt ütemezés egy meglévő példányát keresztül fog létrehozni a régi példány helyett egy új példányt a portálon. A régi példány naplók megőrzi a rendszer, de nem érhetők el a portálon.  
Egy teszt sikeres végrehajtása után a **ütemezés** művelet le lesz tiltva.

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](azure-stack-vaas-workflow-step_select-agent.md)]

1. Válassza ki **ütemezés** nyissa meg a tevékenységütemezést a test-példány ehhez a helyi menüből.

1. Tekintse át a teszt paramétereket, majd **küldés** ütemezni a teszt végrehajtásához.
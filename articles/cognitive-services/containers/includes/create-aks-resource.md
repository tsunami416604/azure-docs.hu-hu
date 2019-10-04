---
title: Azure Kubernetes Service-fürt erőforrásának létrehozása
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan hozhat létre egy Azure Kubernetes-szolgáltatás (ak) erőforrást.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/3/2019
ms.author: dapine
ms.openlocfilehash: 5e442f38a8e81170eeeac2f31f00a7d0eeb08d2b
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377444"
---
## <a name="create-an-azure-kubernetes-service-cluster-resource"></a>Azure Kubernetes Service-fürt erőforrásának létrehozása

1. Nyissa meg az [Azure Kubernetes szolgáltatást](https://ms.portal.azure.com/#create/microsoft.aks), és válassza a **Létrehozás**lehetőséget.

1. Az **alapvető beállítások** lapon adja meg a következő adatokat:

    |Beállítás|Value|
    |--|--|
    |Subscription|Válassza ki a megfelelő előfizetést.|
    |Resource group|Válasszon ki egy rendelkezésre álló erőforráscsoportot.|
    |Kubernetes-fürt neve|Adjon meg egy nevet (kisbetűs).|
    |Régió|Válasszon egy közeli helyet.|
    |Kubernetes-verzió|1.12.8 (alapértelmezett).|
    |DNS name prefix|A rendszer automatikusan létrehozta, de felülbírálhatja.|
    |Csomópontméret|Standard DS2 v2:<br>`2 vCPUs`, `7 GB`|
    |Csomópontok száma|Hagyja meg a csúszkát az alapértelmezett értéknél.|

1. A **skála** lapon hagyja a **virtuális csomópontok** és a virtuálisgép-méretezési csoportok **(előzetes verzió)** beállítást az alapértelmezett értékekre.
1. A **hitelesítés** lapon hagyja változatlanul az **egyszerű szolgáltatásnév** beállítást, és **engedélyezze a RBAC** beállítását az alapértelmezett értékekre.
1. A **hálózatkezelés** lapon adja meg a következő beállításokat:

    |Beállítás|Érték|
    |--|--|
    |HTTP-alkalmazásútválasztás|Nem|
    |Hálózati konfiguráció|Alapszintű|

1. A **figyelés** lapon győződjön meg arról, hogy a **tárolók figyelésének engedélyezése** beállítás az **Igen**értékre van állítva, és hagyja meg **log Analytics munkaterület** alapértelmezett értékeként.
1. A **címkék** lapon hagyja üresen a név/érték párokat.
1. Válassza **a felülvizsgálat és létrehozás**lehetőséget.
1. Az érvényesítési fázisok után válassza a **Létrehozás**lehetőséget.

> [!NOTE]
> Ha az érvényesítés sikertelen, akkor előfordulhat, hogy egy "egyszerű szolgáltatásnév" hiba történt. Lépjen vissza a **hitelesítés** lapra, és térjen vissza az **ellenőrzés + létrehozás**elemre, ahol az érvényesítésnek futnia kell, majd át kell lépnie.

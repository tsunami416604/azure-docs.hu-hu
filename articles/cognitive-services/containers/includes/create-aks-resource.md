---
title: Azure Kubernetes Service-fürt erőforrásának létrehozása
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan hozhat létre egy Azure Kubernetes-szolgáltatás (ak) erőforrást.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 4e3102912e88ef904fed3e680f8cdd23242b1f17
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383419"
---
## <a name="create-an-azure-kubernetes-service-cluster-resource"></a>Azure Kubernetes Service-fürt erőforrásának létrehozása

1. Nyissa meg az [Azure Kubernetes szolgáltatást](https://ms.portal.azure.com/#create/microsoft.aks), és válassza a **Létrehozás**lehetőséget.

1. Az **alapvető beállítások** lapon adja meg a következő adatokat:

    |Beállítás|Érték|
    |--|--|
    |Előfizetést|Válassza ki a megfelelő előfizetést.|
    |Erőforráscsoport|Válasszon ki egy rendelkezésre álló erőforráscsoportot.|
    |Kubernetes-fürt neve|Adjon meg egy nevet (kisbetűs).|
    |Régió|Válasszon egy közeli helyet.|
    |Kubernetes verziója|Bármilyen érték van megjelölve **(alapértelmezett)** .|
    |DNS name prefix|A rendszer automatikusan létrehozta, de felülbírálhatja.|
    |Csomópont mérete|Standard DS2 v2:<br>`2 vCPUs`, `7 GB`|
    |Csomópontok száma|Hagyja meg a csúszkát az alapértelmezett értéknél.|

1. A **skála** lapon hagyja a **virtuális csomópontok** és a virtuálisgép- **méretezési** csoportok beállítást az alapértelmezett értékre.
1. A **hitelesítés** lapon hagyja változatlanul az **egyszerű szolgáltatásnév** beállítást, és **engedélyezze a RBAC** alapértelmezett értékre való beállítását.
1. A **hálózatkezelés** lapon adja meg a következő beállításokat:

    |Beállítás|Érték|
    |--|--|
    |HTTP-alkalmazások útválasztása|Nem|
    |Hálózati konfiguráció|Alapszintű|

1. A **figyelés** lapon győződjön meg arról, hogy a **tárolók figyelésének engedélyezése** beállítás az **Igen**értékre van állítva, és hagyja meg **log Analytics munkaterület** alapértelmezett értékeként.
1. A **címkék** lapon hagyja üresen a név/érték párokat.
1. Válassza **a felülvizsgálat és létrehozás**lehetőséget.
1. Az érvényesítési fázisok után válassza a **Létrehozás**lehetőséget.

> [!NOTE]
> Ha az érvényesítés sikertelen, akkor előfordulhat, hogy egy "egyszerű szolgáltatásnév" hiba történt. Lépjen vissza a **hitelesítés** lapra, és térjen vissza az **ellenőrzés + létrehozás**elemre, ahol az érvényesítésnek futnia kell, majd át kell lépnie.

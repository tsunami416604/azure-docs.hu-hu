---
title: Azure Kubernetes-szolgáltatás fürterőforrásának létrehozása
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan hozhat létre egy Azure Kubernetes-szolgáltatás (AKS) erőforrást.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 4e3102912e88ef904fed3e680f8cdd23242b1f17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74383419"
---
## <a name="create-an-azure-kubernetes-service-cluster-resource"></a>Azure Kubernetes-szolgáltatás fürterőforrásának létrehozása

1. Nyissa meg az [Azure Kubernetes-szolgáltatást,](https://ms.portal.azure.com/#create/microsoft.aks)és válassza **a Létrehozás lehetőséget.**

1. Az **Alapok** lapon adja meg a következő adatokat:

    |Beállítás|Érték|
    |--|--|
    |Előfizetés|Válassza ki a megfelelő előfizetést.|
    |Erőforráscsoport|Válasszon ki egy elérhető erőforráscsoportot.|
    |Kubernetes-fürt neve|Írjon be egy nevet (kisbetű).|
    |Régió|Válasszon ki egy közeli helyet.|
    |Kubernetes verzió|Bármilyen érték van megjelölve **(alapértelmezett)**.|
    |DNS-név előtagja|Automatikusan létrehozva, de felülírhatja.|
    |Csomópont mérete|Szabványos DS2 v2:<br>`2 vCPUs`, `7 GB`|
    |Csomópontszám|Hagyja a csúszkát az alapértelmezett értéken.|

1. A **Méretezés** lapon hagyja **a virtuális csomópontokat** és a **virtuálisgép-méretezési készleteket** az alapértelmezett értékekre állítva.
1. A **Hitelesítés** lapon hagyja **az egyszerű szolgáltatás és** az **RBAC engedélyezése** beállítást az alapértelmezett értékekre.
1. A **Hálózat** lapon adja meg a következő beállításokat:

    |Beállítás|Érték|
    |--|--|
    |HTTP-alkalmazások útválasztása|Nem|
    |Hálózati konfiguráció|Basic|

1. A **Figyelés** lapon győződjön meg arról, hogy **a tárolófigyelés engedélyezése** **értéke Igen**, és hagyja a Log **Analytics munkaterületet** alapértelmezett értékként.
1. A **Címkék** lapon egyelőre hagyja üresen a név-/értékpárokat.
1. Válassza a **Véleményezés és létrehozás lehetőséget.**
1. Az érvényesítési fázisok után válassza a **Létrehozás gombot.**

> [!NOTE]
> Ha az érvényesítés sikertelen, annak oka lehet egy "Egyszerű szolgáltatás" hiba. Lépjen vissza a **Hitelesítés** lapra, majd lépjen vissza a **Véleményezés + létrehozás**laphoz, ahol az érvényesítésnek futnia kell, majd át kell mennie.

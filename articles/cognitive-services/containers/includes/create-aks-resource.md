---
title: Azure Kubernetes-szolgáltatás fürterőforrásának létrehozása
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan hozhat létre egy Azure Kubernetes-szolgáltatás (AKS) erőforrást.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: d01245a76c5210a1843307b543f98d4f1d59d2cc
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877826"
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

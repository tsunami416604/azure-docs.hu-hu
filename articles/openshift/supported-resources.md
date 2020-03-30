---
title: Támogatott erőforrások az Azure Red Hat OpenShift-hez
description: Ismerje meg, hogy a Microsoft Azure Red Hat OpenShift mely Azure-régiókat és virtuálisgép-méreteket támogatja.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 1b794852390141d31a3218a1a434c21133db914c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243665"
---
# <a name="azure-red-hat-openshift-resources"></a>Azure Red Hat OpenShift-erőforrások

Ez a témakör a Microsoft Azure Red Hat OpenShift szolgáltatás által támogatott Azure-régiókat és virtuálisgép-méreteket sorolja fel.

## <a name="azure-regions"></a>Azure-régiók

Tekintse meg [a termékek régiónként elérhető](https://azure.microsoft.com/global-infrastructure/services/?products=openshift&regions=all) a régiók aktuális listáját, ahol telepítheti az Azure Red Hat OpenShift fürtök.

## <a name="virtual-machine-sizes"></a>Virtuálisgép-méretek

Az alábbiakban az Azure Red Hat OpenShift fürt számítási csomópontjaihoz megadott támogatott virtuálisgép-méretek láthatók.

> [!Important]
> Minden virtuális gép különböző számú meghajtóval rendelkezik, amelyek csatolhatók. Ez nem lehet olyan azonnal tiszta, mint a memória vagy a PROCESSZOR mérete.
> Nem minden virtuális gép mérete érhető el minden régióban. Még akkor is, ha az API támogatja a megadott méretet, hibaüzenetet kaphat, ha a méret nem érhető el a megadott régióban.
> További információ [a támogatott virtuálisgép-méretek aktuális listája régiónként.](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)

## <a name="compute-node-sizes"></a>Számítási csomópontméretek

Az Azure Red Hat OpenShift REST API a következő számítási csomópontméreteket támogatja:

|Méret|vCPU|RAM|
|-|-|-|
|Szabványos D4s v3|4|16 GB|
|Szabványos D8s v3|8|32 GB|
|Szabványos D16s v3|16|64 GB|
|Szabványos D32s v3|32|128 GB|
|-|-|-|
|Szabványos E4s v3|4|32 GB|
|Szabványos E8s v3|8|64 GB|
|Szabványos E16s v3|16|128 GB|
|Szabványos E32s v3|32|256 GB|
|-|-|-|
|Szabványos F8s v2|8|16 GB|
|Szabványos F16s v2|16|32 GB|
|Szabványos F32s v2|32|64 GB|

## <a name="master-node-sizes"></a>Fő csomópontméretek

Az Azure Red Hat OpenShift REST API a következő fő/ infrastruktúra csomópontméreteket támogatja:

|Méret|vCPU|RAM|
|-|-|-|
|Szabványos D4s v3|4|16 GB|
|Szabványos D8s v3|8|32 GB|
|Szabványos D16s v3|16|64 GB|
|Szabványos D32s v3|32|128 GB|

## <a name="next-steps"></a>További lépések

Próbálja ki az [Azure Red Hat OpenShift fürt létrehozása](tutorial-create-cluster.md) oktatóanyagot.

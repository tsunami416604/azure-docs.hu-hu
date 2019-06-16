---
title: Támogatott az erőforrások az Azure Red Hat OpenShift |} A Microsoft Docs
description: Ismerje meg, melyik Azure-régiók és a virtuálisgép-méretek támogatottak a Microsoft Azure Red Hat OpenShift.
services: container-service
author: jimzim
ms.author: jzim
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/15/2019
ms.openlocfilehash: c226227797802ab58d1bcbaadb7e97e780b30560
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66306222"
---
# <a name="azure-red-hat-openshift-resources"></a>Red Hat Openshiftet Azure-erőforrások

Ez a témakör felsorolja az Azure-régiók és a Microsoft Azure Red Hat OpenShift szolgáltatás által támogatott virtuálisgép-méretek.

## <a name="azure-regions"></a>Azure-régiók

Lásd: [elérhető termékek régiók szerint](https://azure.microsoft.com/global-infrastructure/services/?products=openshift&regions=all) fürtök régióban, ahol telepítheti az Azure Red Hat OpenShift aktuális listáját.

## <a name="virtual-machine-sizes"></a>Virtuálisgép-méretek

Az alábbiakban a támogatott virtuálisgép-méretek is megadhat a számítási csomópontok az Azure Red Hat OpenShift fürtben.

> [!Important]
> Egyes virtuális gépekhez csatolt meghajtók oszlopaik száma különböző. Nem lehet azonnal szerint egyértelmű memória vagy a CPU-méretet.
> Nem minden Virtuálisgép-méret érhető el minden régióban. Akkor is, ha az API támogatja a mérete határozza meg, előfordulhat, hogy hibaüzenetet kap Ha a méret nem érhető el a megadott régióban.
> Lásd: [támogatott virtuális gép jelenlegi listája úgy méretezi régiónként](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) további információt.

## <a name="compute-node-sizes"></a>Számítási csomópontok méretei

A következő számítási csomópontok méretét az Azure Red Hat OpenShift REST API támogatja:

|Méret|vCPU|RAM|
|-|-|-|
|Standard D4s v3|4|16 GB|
|Standard szintű D8s v3|8|32 GB|
|Standard D16s v3|16|64 GB|
|Standard szintű D32s v3|32|128 GB|
|-|-|-|
|Standard szintű E4s v3|4|32 GB|
|Standard szintű E8s v3|8|64 GB|
|Standard szintű E16s v3|16|128 GB|
|Standard E32s v3|32|256 GB|
|-|-|-|
|Standard F8s v2|8|16 GB|
|Standard F16s v2|16|32 GB|
|Standard F32s v2|32|64 GB|

## <a name="master-node-sizes"></a>Fő csomópontok méretei

A következő fő / infrastruktúra csomópontok méretei az Azure Red Hat OpenShift REST API által támogatott:

|Méret|vCPU|RAM|
|-|-|-|
|Standard D4s v3|4|16 GB|
|Standard szintű D8s v3|8|32 GB|
|Standard D16s v3|16|64 GB|
|Standard szintű D32s v3|32|128 GB|

## <a name="next-steps"></a>További lépések

Próbálja ki a [hozzon létre egy Azure Red Hat OpenShift fürtöt](tutorial-create-cluster.md) oktatóanyag.
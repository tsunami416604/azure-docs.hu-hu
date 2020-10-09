---
title: Az Azure Red Hat OpenShift 3.11 támogatott erőforrásai
description: Megtudhatja, hogy mely Azure-régiók és virtuálisgép-méretek támogatottak Microsoft Azure Red Hat-OpenShift.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 38203cede755d776ba9142ad16e1bea1fd1637a6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "82203675"
---
# <a name="azure-red-hat-openshift-resources"></a>Azure Red Hat OpenShift-erőforrások

Ez a témakör a Microsoft Azure Red Hat OpenShift 3,11 szolgáltatás által támogatott Azure-régiókat és virtuálisgép-méreteket sorolja fel.

## <a name="azure-regions"></a>Azure-régiók

Az Azure Red Hat OpenShift-fürtök üzembe helyezéséhez tekintse meg a régiók [által elérhető termékek](https://azure.microsoft.com/global-infrastructure/services/?products=openshift&regions=all) listáját.

## <a name="virtual-machine-sizes"></a>Virtuálisgép-méretek

Az alábbiakban az Azure Red Hat OpenShift-fürt számítási csomópontjaihoz megadható támogatott virtuálisgép-méretek láthatók.

> [!Important]
> Minden virtuális gép különböző számú meghajtót tud csatolni. Előfordulhat, hogy a memória vagy a CPU mérete nem lehet azonnal egyértelmű.
> Nem minden virtuálisgép-méret érhető el minden régióban. Ha az API támogatja a megadott méretet, akkor hibaüzenet jelenik meg, ha a méret nem érhető el a megadott régióban.
> További információkért tekintse meg régiónként a támogatott virtuálisgép- [méretek aktuális listáját](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) .

## <a name="compute-node-sizes"></a>Számítási csomópontok méretei

Az Azure Red Hat OpenShift REST API a következő számítási csomópont-méreteket támogatja:

|Méret|vCPU|RAM|
|-|-|-|
|Standard D4s v3|4|16 GB|
|Standard D8s v3|8|32 GB|
|Standard D16s v3|16|64 GB|
|Standard D32s v3|32|128 GB|
|-|-|-|
|Standard E4s v3|4|32 GB|
|Standard E8s v3|8|64 GB|
|Standard E16s v3|16|128 GB|
|Standard E32s v3|32|256 GB|
|-|-|-|
|Standard F8s v2|8|16 GB|
|Standard F16s v2|16|32 GB|
|Standard F32s v2|32|64 GB|

## <a name="master-node-sizes"></a>Fő csomópontok méretei

Az Azure Red Hat OpenShift REST API a következő fő/infrastrukturális csomópontok méreteit támogatja:

|Méret|vCPU|RAM|
|-|-|-|
|Standard D4s v3|4|16 GB|
|Standard D8s v3|8|32 GB|
|Standard D16s v3|16|64 GB|
|Standard D32s v3|32|128 GB|

## <a name="next-steps"></a>További lépések

Próbálja ki az [Azure Red Hat OpenShift-fürt létrehozásával](tutorial-create-cluster.md) foglalkozó oktatóanyagot.

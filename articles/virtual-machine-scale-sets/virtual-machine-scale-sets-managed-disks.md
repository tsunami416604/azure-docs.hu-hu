---
title: Felügyelt lemezek használata Azure-beli virtuálisgép-méretezési csoportokkal | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan használhat felügyelt lemezeket virtuálisgép-méretezési csoportokkal
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 6/01/2017
ms.author: negat
ms.openlocfilehash: 82fa518e6c0498a13f950ce33c51be8581918f9b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2018
---
# <a name="azure-vm-scale-sets-and-managed-disks"></a>Azure-beli virtuálisgép-méretezési csoportok és felügyelt lemezek

Az Azure-beli [virtuálisgép-méretezési csoportok](/azure/virtual-machine-scale-sets/) támogatják a felügyelt lemezekkel rendelkező virtuális gépeket. A felügyelt lemezek méretezési csoportokkal való használatának több előnye van, például:

* Nem kell előre létrehoznia és felügyelnie tárfiókokat a méretezési csoportban lévő virtuális gépek operációsrendszer-lemezeinek tárolásához.

* Felügyelt adatlemezeket csatlakoztathat a méretezési csoporthoz.

* Felügyelt lemezzel a méretezési csoportok kapacitása akár platformlemezkép-alapú 1000 vagy 300 egyéni lemezképen alapuló virtuális gép lehet.

## <a name="get-started"></a>Bevezetés

A felügyelt lemezes méretezési csoportok használatát úgy kezdheti el a legegyszerűbben, ha az Azure Portalon üzembe helyez egyet. További információkért tekintse meg [ezt a cikket](./virtual-machine-scale-sets-portal-create.md). Az első lépések másik egyszerű módja, ha az [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2)-s verziójával üzembe helyez egy méretezési csoportot. A következő példa bemutatja, hogyan hozhat létre Ubuntu-alapú méretezési csoportot 10 virtuális géppel, amelyek mindegyike 50 GB-os és 100 GB-os adatlemezzel rendelkezik:

```azurecli
az group create -l southcentralus -n dsktest
az vmss create -g dsktest -n dskvmss --image ubuntults --instance-count 10 --data-disk-sizes-gb 50 100
```

Vagy az [Azure-gyorsindítási sablonok GitHub-adattárában](https://github.com/Azure/azure-quickstart-templates) a `vmss` elemet tartalmazó mappákra rákeresve megtekintheti a méretezési csoportokat üzembe helyező sablonok előre felépített példáit. [Ebből a listából](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md) megtudhatja, hogy melyik sablon használ már felügyelt lemezeket.

## <a name="next-steps"></a>További lépések

A felügyelt lemezekkel kapcsolatban [ebben a cikkben](../virtual-machines/windows/managed-disks-overview.md) talál további információt.

[Ebben a cikkben](./virtual-machine-scale-sets-convert-template-to-md.md) láthatja, hogyan alakíthat át egy Resource Manager-sablont, hogy felügyelt lemezekkel lássa el a méretezési csoportokat. A Resource Manager-sablonok ugyanezen módosításai érvényesek az Azure REST API-ra is.

A felügyelt adatlemezek méretezési csoportokkal való használatáról [ebben a cikkben](./virtual-machine-scale-sets-attached-disks.md) talál további információt.

A nagyméretű méretezési csoportok használatának elkezdéséről [ebben a cikkben](./virtual-machine-scale-sets-placement-groups.md) olvashat.



---
title: Azure CLI 2.0-minták | Microsoft Docs
description: Azure CLI 2.0-minták
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 69fc81d4d0569ee7a66fbda5ab500ef2ee15c694
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="azure-cli-20-samples-for-virtual-machine-scale-sets"></a>Azure CLI 2.0-minták virtuálisgép-méretezési csoportokhoz

A következő táblázat az Azure CLI 2.0 használatával létrehozott bash szkriptekre mutató hivatkozásokat tartalmaz.

| | |
|---|---|
|**Méretezési csoport létrehozása és felügyelete**||
| [Virtuálisgép-méretezési csoport létrehozása](scripts/cli-sample-create-simple-scale-set.md?toc=%2fcli%2fazure%2ftoc.json) | Egy virtuálisgép-méretezési csoportot hoz létre minimális konfigurációval. |
| [Méretezési csoport létrehozása egyéni virtuálisgép-rendszerképből](scripts/cli-sample-create-scale-set-from-custom-image.md?toc=%2fcli%2fmodule%2ftoc.json) | Létrehoz egy az egyéni virtuálisgép-rendszerképet használó virtuálisgép-méretezési csoportot. |
| [Alkalmazások telepítése méretezési csoportokban](scripts/cli-sample-install-apps.md?toc=%2fcli%2fmodule%2ftoc.json) | Az egyéni Azure-szkriptbővítménnyel telepíthet alapszintű webalkalmazásokat a méretezési csoportokban. |
|**Tárhely kezelése**||
| [Lemezek létrehozása és méretezési csoporthoz való csatolása](scripts/cli-sample-attach-disks.md?toc=%2fcli%2fmodule%2ftoc.json) | Egy csatlakoztatott adatlemezekkel rendelkező virtuálisgép-méretezési csoportot hoz létre. |
|**Méretezés és redundancia kezelése**||
| [Gazdagépalapú automatikus méretezés engedélyezése](scripts/cli-sample-enable-autoscale.md?toc=%2fcli%2fazure%2ftoc.json) | Egy virtuálisgép-méretezést hoz létre, amely úgy van konfigurálva, hogy automatikusan a CPU kihasználtságához igazodjon. |
| [Egyzónás méretezési csoport létrehozása](scripts/cli-sample-single-availability-zone-scale-set.md?toc=%2fcli%2fazure%2ftoc.json) | Olyan virtuális-gép méretezést hoz létre, amely egyetlen rendelkezésre állási zónát használ. |
| [Zónaredundáns méretezési csoport létrehozása](scripts/cli-sample-zone-redundant-scale-set.md?toc=%2fcli%2fazure%2ftoc.json) | Egy több rendelkezésre állási zónára kiterjedő virtuálisgép-méretezést hoz létre. |
| | |
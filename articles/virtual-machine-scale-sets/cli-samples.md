---
title: Azure CLI-minták
description: Egy táblázat, amely az Azure CLI használatával létrehozott bash-szkriptekre mutató hivatkozásokat tartalmaz, például egy méretezési csoport létrehozását és felügyeletét.
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.devlang: azurecli
ms.topic: sample
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: ca1c85224959840e4137eab68e3d089283827bb5
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76275147"
---
# <a name="azure-cli-samples-for-virtual-machine-scale-sets"></a>Azure CLI-minták virtuálisgép-méretezési csoportokhoz

A következő táblázat az Azure CLI használatával létrehozott bash parancsfájlokra mutató hivatkozásokat tartalmaz.

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

---
title: Azure CLI-minták
description: Egy táblázat, amely az Azure CLI használatával létrehozott bash-szkriptekre mutató hivatkozásokat tartalmaz, például egy méretezési csoport létrehozását és felügyeletét.
author: mimckitt
ms.author: mimckitt
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.subservice: cli
ms.date: 03/27/2018
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: d89b44f982e1bf2c69a06436adaefdc65df99f27
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83195828"
---
# <a name="azure-cli-samples-for-virtual-machine-scale-sets"></a>Azure CLI-minták virtuálisgép-méretezési csoportokhoz

A következő táblázat az Azure CLI használatával létrehozott bash parancsfájlokra mutató hivatkozásokat tartalmaz.

| | |
|---|---|
|**Méretezési csoport létrehozása és felügyelete**||
| [Virtuálisgép-méretezési csoport létrehozása](scripts/cli-sample-create-simple-scale-set.md?toc=%2fcli%2fazure%2ftoc.json) | Egy virtuálisgép-méretezési csoportot hoz létre minimális konfigurációval. |
| [Méretezési csoport létrehozása egyéni virtuálisgép-rendszerképből](scripts/cli-sample-create-scale-set-from-custom-image.md?toc=%2fcli%2fmodule%2ftoc.json) | Létrehoz egy az egyéni virtuálisgép-rendszerképet használó virtuálisgép-méretezési csoportot. |
| [Alkalmazások telepítése méretezési csoportokban](scripts/cli-sample-install-apps.md?toc=%2fcli%2fmodule%2ftoc.json) | Az egyéni Azure-szkriptbővítménnyel telepíthet alapszintű webalkalmazásokat a méretezési csoportokban. |
|**Tárolás kezelése**||
| [Lemezek létrehozása és méretezési csoporthoz való csatolása](scripts/cli-sample-attach-disks.md?toc=%2fcli%2fmodule%2ftoc.json) | Egy csatlakoztatott adatlemezekkel rendelkező virtuálisgép-méretezési csoportot hoz létre. |
|**Méretezés és redundancia kezelése**||
| [Gazdagépalapú automatikus méretezés engedélyezése](scripts/cli-sample-enable-autoscale.md?toc=%2fcli%2fazure%2ftoc.json) | Egy virtuálisgép-méretezést hoz létre, amely úgy van konfigurálva, hogy automatikusan a CPU kihasználtságához igazodjon. |
| [Egyzónás méretezési csoport létrehozása](scripts/cli-sample-single-availability-zone-scale-set.md?toc=%2fcli%2fazure%2ftoc.json) | Olyan virtuális-gép méretezést hoz létre, amely egyetlen rendelkezésre állási zónát használ. |
| [Zónaredundáns méretezési csoport létrehozása](scripts/cli-sample-zone-redundant-scale-set.md?toc=%2fcli%2fazure%2ftoc.json) | Egy több rendelkezésre állási zónára kiterjedő virtuálisgép-méretezést hoz létre. |
| | |

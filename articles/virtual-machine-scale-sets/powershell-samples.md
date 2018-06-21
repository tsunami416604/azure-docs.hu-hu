---
title: Azure PowerShell-minták | Microsoft Docs
description: Azure PowerShell-minták
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 27b8c9391de4014f45257e3808647b3e49c250b2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34651947"
---
# <a name="azure-powershell-samples-for-virtual-machine-scale-sets"></a>Azure PowerShell-minták virtuálisgép-méretezési csoportokhoz

A következő táblázat az Azure PowerShell használatával létrehozott bash szkriptekre mutató hivatkozásokat tartalmaz.

| | |
|---|---|
|**Méretezési csoport létrehozása és felügyelete**||
| [Egyszerű virtuálisgép-méretezési csoport létrehozása](scripts/powershell-sample-create-simple-scale-set.md?toc=%2fpowershell%2fazure%2ftoc.json) | Egy virtuálisgép-méretezési csoportot hoz létre minimális konfigurációval. |
| [Teljes virtuálisgép-méretezési csoport létrehozása](scripts/powershell-sample-create-complete-scale-set.md?toc=%2fpowershell%2fazure%2ftoc.json) | Létrehoz egy virtuálisgép-méretezési csoportot és a hozzá kapcsolódó erőforrásokat egy konfigurációs fájllal. |
| [Méretezési csoport létrehozása egyéni virtuálisgép-rendszerképből](scripts/powershell-sample-create-scale-set-from-custom-image.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Létrehoz egy az egyéni virtuálisgép-rendszerképet használó virtuálisgép-méretezési csoportot. |
| [Alkalmazások telepítése méretezési csoportokban](scripts/powershell-sample-install-apps.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Az egyéni Azure-szkriptbővítménnyel telepíthet alapszintű webalkalmazásokat a méretezési csoportokban. |
|**Tárhely kezelése**||
| [Lemezek létrehozása és méretezési csoporthoz való csatolása](scripts/powershell-sample-attach-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Egy csatlakoztatott adatlemezekkel rendelkező virtuálisgép-méretezési csoportot hoz létre. |
|**Méretezés és redundancia kezelése**||
| [Gazdagépalapú automatikus méretezés engedélyezése](scripts/powershell-sample-enable-autoscale.md?toc=%2fpowershell%2fazure%2ftoc.json) | Egy virtuálisgép-méretezést hoz létre, amely úgy van konfigurálva, hogy automatikusan a CPU kihasználtságához igazodjon. |
| [Egyzónás méretezési csoport létrehozása](scripts/powershell-sample-single-availability-zone-scale-set.md?toc=%2fpowershell%2fazure%2ftoc.json) | Olyan virtuális-gép méretezést hoz létre, amely egyetlen rendelkezésre állási zónát használ. |
| [Zónaredundáns méretezési csoport létrehozása](scripts/powershell-sample-zone-redundant-scale-set.md?toc=%2fpowershell%2fazure%2ftoc.json) | Egy több rendelkezésre állási zónára kiterjedő virtuálisgép-méretezést hoz létre. |
| | |
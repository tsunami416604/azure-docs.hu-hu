---
title: Azure CLI-példaszkript – Felügyelt lemez pillanatképének másolása (áthelyezése) előfizetésen belül vagy előfizetések között a CLI használatával | Microsoft Docs
description: Azure CLI-példaszkript – Felügyelt lemez pillanatképének másolása (áthelyezése) előfizetésen belül vagy előfizetések között a CLI használatával
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 744990b06b5585763615eb69909c29a27dee5a7f
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2019
ms.locfileid: "57248728"
---
# <a name="copy-snapshot-of-a-managed-disk-to-same-or-different-subscription-with-cli"></a>Felügyelt lemez pillanatképének másolása előfizetésen belül vagy előfizetések között a CLI használatával

Ez a szkript átmásolja egy felügyelt lemez pillanatképét az előfizetésen belül vagy előfizetések között. Ez a szkript a következő esetekben használja:

1. Telepítse át a Premium storage (Premium_LRS) pillanatkép standard szintű tárolóba (Standard_LRS vagy Standard_ZRS) a költségek csökkentése érdekében.
1. Telepítse át egy pillanatképet a helyileg redundáns tárolás (Premium_LRS, Standard_LRS) zónaredundáns tárolás (Standard_ZRS) számára, hogy a nagyobb megbízhatóságot ZRS tárhelyet.
1. Helyezze át a pillanatkép másik előfizetésbe tartozó hosszabb adatmegőrzés megadásához ugyanabban a régióban.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.sh "Copy snapshot")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja egy, a forráspillanatkép azonosítóját használó pillanatkép a célelőfizetésben történő létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az snapshot show](https://docs.microsoft.com/cli/azure/snapshot) | Lekérdezi egy pillanatkép összes tulajdonságát a pillanatkép neve és erőforráscsoport-tulajdonságai alapján. Az „Id” tulajdonság a pillanatkép másik előfizetésbe való másolásakor használatos.  |
| [az snapshot create](https://docs.microsoft.com/cli/azure/snapshot) | Lemásol egy pillanatképet úgy, hogy a szülőpillanatkép azonosítójának és nevének használatával egy másik előfizetésben hoz létre egy pillanatképet.  |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

További virtuális gép és a felügyelt lemezek CLI-példaszkripteket megtalálható a [Azure Windows virtuális gép dokumentációja](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

---
title: Linux rendszerű virtuális gépek rendelkezésre állásának kezelése az Azure-ban
description: Ismerje meg, hogyan használható több virtuális gép a Linux-alkalmazások magas rendelkezésre állásának biztosításához az Azure-ban
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 891c852a-84c0-4940-a61e-ada6e185bf37
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5742ed346c6761dd443d6252e5c9e457fa952b87
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035889"
---
# <a name="manage-the-availability-of-linux-virtual-machines"></a>A Linux rendszerű virtuális gépek rendelkezésre állásának kezelése

Ismerje meg, hogyan állíthat be és kezelhet több virtuális gépet az Azure-beli linuxos alkalmazások magas rendelkezésre állásának biztosítása érdekében. [A Windows rendszerű virtuális gépek rendelkezésre állását is kezelheti](../windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

A rendelkezésre állási csoportnak a Resource Manager-alapú üzemi modellben a parancssori felülettel történő létrehozásával kapcsolatos utasításokért lásd [: Azure availset: a rendelkezésre állási csoportok kezeléséhez használt parancsok](../azure-cli-arm-commands.md#azure-availset-commands-to-manage-your-availability-sets).

[!INCLUDE [virtual-machines-common-manage-availability](../../../includes/virtual-machines-common-manage-availability.md)]

## <a name="next-steps"></a>Következő lépések
Ha többet szeretne megtudni a virtuális gépek terheléselosztásáról, tekintse meg a [virtuális gépek](../virtual-machines-linux-load-balance.md)terheléselosztását ismertető témakört.


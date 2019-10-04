---
title: Linux rendszerű virtuális gépek rendelkezésre állásának kezelése az Azure-ban | Microsoft Docs
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
ms.openlocfilehash: ab269bcd56a5e60fdc8434a58b61163cb8769763
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70082907"
---
# <a name="manage-the-availability-of-linux-virtual-machines"></a>A Linux rendszerű virtuális gépek rendelkezésre állásának kezelése

Ismerje meg, hogyan állíthat be és kezelhet több virtuális gépet az Azure-beli linuxos alkalmazások magas rendelkezésre állásának biztosítása érdekében. [A Windows rendszerű virtuális gépek rendelkezésre állását](../windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)is kezelheti.

A rendelkezésre állási csoportnak a Resource Manager-alapú üzemi modellben a parancssori felülettel történő létrehozásával kapcsolatos utasításokért lásd [: Azure availset: a rendelkezésre állási](../azure-cli-arm-commands.md#azure-availset-commands-to-manage-your-availability-sets)csoportok kezeléséhez használt parancsok.

[!INCLUDE [virtual-machines-common-manage-availability](../../../includes/virtual-machines-common-manage-availability.md)]

## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni a virtuális gépek terheléselosztásáról, tekintse meg a [virtuális gépek](../virtual-machines-linux-load-balance.md)terheléselosztását ismertető témakört.


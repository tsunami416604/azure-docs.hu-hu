---
title: Windows rendszerű virtuális gépek rendelkezésre állásának kezelése az Azure-ban
description: Ismerje meg, hogyan használható több virtuális gép a Windows-alkalmazások magas rendelkezésre állásának biztosításához az Azure-ban
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 02351953-7b6a-4657-b9e1-de2ea8f6aa05
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 11/27/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ae5d60f77319a6590807ae0b18a0c07c116e128b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79267442"
---
# <a name="manage-the-availability-of-windows-virtual-machines-in-azure"></a>A Windows rendszerű virtuális gépek rendelkezésre állásának kezelése az Azure-ban 

Ismerje meg, hogyan állíthat be és kezelhet több virtuális gépet, hogy magas rendelkezésre állást biztosítson a Windows-alkalmazás számára az Azure-ban. [A Linux rendszerű virtuális gépek rendelkezésre állását is kezelheti](../linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-manage-availability](../../../includes/virtual-machines-common-manage-availability.md)]

## <a name="next-steps"></a>Következő lépések
Ha többet szeretne megtudni a virtuális gépek terheléselosztásáról, tekintse meg a [virtuális gépek](tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)terheléselosztását ismertető témakört.

Az N szintű alkalmazások SQL Server IaaS való futtatására szolgáló hivatkozási architektúrák megtekintése

* [Windows N szintű alkalmazás az Azure-ban SQL Server](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/n-tier-sql-server)
* [N szintű alkalmazás futtatása több Azure-régióban a magas rendelkezésre állás érdekében](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/multi-region-sql-server)
